<properties
    pageTitle="Résolution des problèmes d’échec d’allocation de Service Cloud | Microsoft Azure"
    description="Échec d’allocation de dépannage lorsque vous déployez des Services en nuage dans Azure"
    services="azure-service-management, cloud-services"
    documentationCenter=""
    authors="simonxjx"
    manager="felixwu"
    editor=""
    tags="top-support-issue"/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-six"/>



# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Échec d’allocation de dépannage lorsque vous déployez des Services en nuage dans Azure

## <a name="summary"></a>Résumé
Lorsque vous déployez des instances à un Service en nuage ou ajoutez le nouveau site web ou des instances de rôle de travail, Microsoft Azure alloue les ressources de calcul. Vous pouvez parfois recevoir des erreurs lors de l’exécution de ces opérations avant même que vous atteignez les limites de l’abonnement Azure. Cet article explique les causes des échecs d’allocation de la commune et suggère des mesures correctives possibles. Les informations peuvent également être utiles lorsque vous planifiez le déploiement de vos services.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Arrière-plan – fonctionne de l’allocation
Les serveurs de centres de données Azure sont partitionnés en clusters. Une nouvelle demande d’allocation de service cloud est tentée dans plusieurs clusters. Lorsque la première instance est déployée à un service cloud (en transit ou production), service de cloud obtient épinglée à un cluster. Les déploiements supplémentaires pour le service en nuage se produit dans le même cluster. Dans cet article, nous allons faire référence à ce que « épinglé dans un cluster ». La figure 1 ci-dessous illustre le cas d’une allocation normale qui est tentée dans plusieurs clusters ; Diagramme 2 illustre le cas d’une répartition qui a placé en attente de Cluster 2 car il s’agit d’où est hébergé le CS_1 de Service Cloud existant.

![Diagramme de répartition](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Pourquoi l’échec d’allocation se produit
Lorsqu’une demande d’allocation est épinglée à un cluster, il existe une probabilité de supérieure de ne parvient pas à trouver de ressources libres, dans la mesure où la liste des ressources disponibles est limitée à un cluster. En outre, si votre demande d’allocation est épinglé à un cluster, mais le type de ressource que vous avez demandée n’est pas pris en charge par ce cluster, votre demande échouera même si le cluster a une ressource libre. La figure 3 ci-dessous illustre le cas où une allocation en attente échoue car le cluster seul candidat n’a pas de libérer des ressources. Figure 4 illustre le cas où une allocation en attente échoue parce que le cluster seul candidat ne gère pas la taille de mémoire virtuelle demandée, même si le cluster a de libérer des ressources.

![Échec d’Allocation en attente](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Échec d’allocation de résolution des problèmes des services en nuage
### <a name="error-message"></a>Message d’erreur
Vous pouvez voir le message d’erreur suivant :

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problèmes courants
Voici les scénarios courants de répartition qui provoquent une demande d’allocation être épinglés à un seul cluster.

- Déploiement pour module de transfert - si un service en nuage a un déploiement dans les deux emplacements, puis le service en nuage tout est mis en attente pour un cluster spécifique.  Cela signifie que si un déploiement déjà existe dans l’emplacement de production, puis un nouveau déploiement intermédiaire ne peut être affecté dans le même cluster que l’emplacement de production. Si le cluster est proche de capacité, la demande peut échouer.

- Mise à l’échelle : ajout de nouvelles instances à un service cloud existant doit allouer dans le même cluster.  Petite mise à l’échelle de demandes peut généralement être alloué, mais pas toujours. Si le cluster est proche de capacité, la demande peut échouer.

- Groupe d’affinité - un déploiement à un service cloud vide peut être affecté par le fabric dans un cluster de cette région, à moins que le service en nuage est épinglé à un groupe d’affinité. Déploiements sur le même groupe d’affinité va être tentées sur le même cluster. Si le cluster est proche de capacité, la demande peut échouer.

- Affinité du groupe vNet - anciens réseaux virtuels étaient liées à l’affinité des groupes plutôt que des régions, et les services en nuage dans ces réseaux virtuels devrait être épinglés à l’affinité du groupe cluster. Déploiements à ce type de réseau virtuel va être tentées sur le cluster épinglé. Si le cluster est proche de capacité, la demande peut échouer.

## <a name="solutions"></a>Solutions

1. Redéploiement d’un service en nuage - cette solution est susceptible d’être plus de succès car elle permet à la plate-forme de tous les clusters dans cette région.

    - Déployer la charge de travail vers un nouveau service de cloud  

    - Mettre à jour l’enregistrement CNAME ou un enregistrement de point de trafic au nouveau service cloud

    - Une fois que l’ancien site va zéro le trafic, vous pouvez supprimer l’ancien service nuage. Cette solution doit subir aucun temps d’arrêt.

2. Supprimer à la fois la production et emplacements de la zone de transit - que cette solution permettra de conserver votre nom DNS existant, mais entraînera l’arrêt de votre application.

    - Supprimer la production et la mise en attente de créneaux horaires d’un service cloud existant afin que le service en nuage est vide, puis

    - Créer un nouveau déploiement dans le service cloud existant. Ce nouveau tentera d’allocation sur tous les clusters dans la région. Assurez-vous que le service en nuage n’est pas lié à un groupe d’affinité.

3. IP réservée - cette solution permettra de conserver votre IP existants d’adresses, mais provoquera des interruptions de service à votre application.  

    - Créer une adresse IP réservée pour votre déploiement existant à l’aide de Powershell

    ```
    New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
    ```

    - Suivez #2 dessus, en veillant à spécifier l’adresse IP réservée de nouveau dans CSCFG du service.

4. Supprimer le groupe d’affinité pour les nouveaux déploiements - affinités sont plus recommandé. Suivez les étapes ci-dessus pour déployer un nouveau service en nuage à # 1. Assurez-vous que service nuage n’est pas dans un groupe d’affinité.

5. Convertir un réseau virtuel régionaux - voir [comment migrer à partir de groupes d’affinité pour un réseau virtuel (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
