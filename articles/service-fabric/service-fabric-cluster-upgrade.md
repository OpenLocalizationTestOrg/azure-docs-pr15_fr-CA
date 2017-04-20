<properties
   pageTitle="Mettre à niveau un cluster Service de Azure Fabric | Microsoft Azure"
   description="Mettre à niveau le code de Service Fabric et/ou la configuration qui exécute un cluster Service Fabric, y compris la définition du mode de mise à jour de cluster, mise à niveau des certificats, ajout de ports d’application, effectuant des correctifs du système d’exploitation, et ainsi de suite. Ce que vous attendez lorsque les mises à niveau sont exécutées ?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-an-azure-service-fabric-cluster"></a>Mise à niveau d’un cluster d’Azure Fabric de Service

> [AZURE.SELECTOR]
- [Cluster Azure](service-fabric-cluster-upgrade.md)
- [Cluster autonome](service-fabric-cluster-upgrade-windows-server.md)

Pour les systèmes modernes, conception de la mise à jour est la clé pour atteindre un succès à long terme de votre produit. Un cluster d’Azure Fabric de Service est une ressource que vous possédez, mais partiellement géré par Microsoft. Cet article explique ce qu’est géré automatiquement, et ce que vous pouvez configurer vous-même.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Contrôle de la version de fabric qui s’exécute sur votre Cluster.

Vous pouvez définir votre cluster pour recevoir des mises à niveau de fabric automatique, lorsque Microsoft publie une nouvelle version ou cliquez sur pour sélectionner une version de fabric pris en charge que vous souhaitez que votre cluster sur.

Pour cela, la définition de la configuration de cluster de « upgradeMode » sur le portail ou à l’aide du Gestionnaire de ressources au moment de la création ou version ultérieure sur un cluster live 

>[AZURE.NOTE] Assurez-vous de conserver votre cluster exécutant une version prise en charge de fabric toujours. Au fur et annonce de la sortie d’une nouvelle version de tissu de service, la version précédente est marquée pour la fin de la prise en charge après un délai minimal de soixante jours à partir de cette date. les les nouvelles versions sont annoncés [sur le blog de l’équipe service fabric](https://blogs.msdn.microsoft.com/azureservicefabric/ ). La nouvelle version est disponible, puis de choisir. 

14 jours avant l’expiration de la version de que votre cluster est en cours d’exécution, un événement d’état est généré qui place votre cluster dans un état d’avertissement. Le cluster reste dans un état d’avertissement jusqu'à ce que vous mettez à niveau vers une version prise en charge de fabric.


### <a name="setting-the-upgrade-mode-via-portal"></a>Définition du mode de mise à niveau via le portail 

Vous pouvez définir le cluster sur automatique ou manuel lors de la création du cluster.

![Create_Manualmode][Create_Manualmode]

Vous pouvez affecter le cluster automatique ou manuelle sur un cluster live, à l’aide de l’expérience de gestion. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Mise à niveau vers une nouvelle version sur un cluster qui est défini en mode manuel via le portail.
 
Pour mettre à niveau vers une nouvelle version, il vous suffit de faire est sélectionner la version disponible dans la liste déroulante et enregistrer. La mise à niveau de Fabric obtient automatiquement lancée. Les stratégies de bon fonctionnement du cluster (une combinaison de l’état du nœud et de la santé toutes les applications en cours d’exécution dans le cluster) sont respectées lors de la mise à niveau.

Si les stratégies de bon fonctionnement du cluster ne sont pas remplies, la mise à niveau est annulée. Défilement vers le bas de ce document pour en savoir plus sur la manière de définir ces stratégies d’état personnalisé. 

Une fois que vous avez résolu les problèmes qui ont abouti à la restauration, vous devez lancer de nouveau, la mise à niveau en suivant la même procédure qu’avant.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Définition du mode de mise à niveau via un modèle de gestionnaire de ressources 

Ajouter la configuration de « upgradeMode » à la définition de la ressource Microsoft.ServiceFabric/clusters et définir la « clusterCodeVersion » à l’une des versions prises en charge de fabric comme indiqué ci-dessous et puis déployer le modèle. Les valeurs valides pour « upgradeMode » sont « Manuel » ou « Automatique »
 
![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Mise à niveau vers une nouvelle version sur un cluster qui est défini en mode manuel via un modèle de gestionnaire de ressources.
 
Lorsque le cluster est en mode manuel, mise à niveau vers une nouvelle version, modifier la « clusterCodeVersion » à une version prise en charge et le déployer. Le déploiement du modèle, le plaisir de la mise à niveau de Fabric obtient lancée automatiquement. Les stratégies de bon fonctionnement du cluster (une combinaison de l’état du nœud et de la santé toutes les applications en cours d’exécution dans le cluster) sont respectées lors de la mise à niveau.

Si les stratégies de bon fonctionnement du cluster ne sont pas remplies, la mise à niveau est annulée. Défilement vers le bas de ce document pour en savoir plus sur la manière de définir ces stratégies d’état personnalisé. 

Une fois que vous avez résolu les problèmes qui ont abouti à la restauration, vous devez lancer de nouveau, la mise à niveau en suivant la même procédure qu’avant.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Obtenir la liste de toutes les version disponible pour tous les environnements pour un abonnement donné

Exécutez la commande suivante, et vous devez obtenir une sortie semblable à celui-ci.

« supportExpiryUtc » indique votre lorsqu’une version donnée arrive à expiration ou a expiré. La version la plus récente n’a pas une date valide, il a la valeur « 9999-12-31T23:59:59.9999999 », qui signifie simplement que la date d’expiration n’est pas encore définie.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/clusterVersions?api-version= 2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Comportement de mise à niveau de fabric lorsque le Mode de mise à niveau de cluster est automatique

Microsoft met à jour le code de structure et de la configuration qui s’exécute dans un cluster Azure. Nous effectuons automatiques surveillés les mises à niveau du logiciel sur une en fonction des besoins. Ces mises à niveau peuvent être de code, la configuration ou les deux. Pour vous assurer que votre application ne souffre aucun impact ou un impact minimal dû à ces mises à niveau, nous effectuons les mises à niveau dans les phases suivantes :

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Phase 1 : Une mise à niveau est effectuée à l’aide de toutes les stratégies d’intégrité de cluster

Pendant cette phase, les mises à niveau poursuivre un domaine de mise à niveau à la fois, et les applications en cours d’exécution dans le cluster continuent de fonctionner sans interruption de service. Les stratégies de bon fonctionnement du cluster (une combinaison de l’état du nœud et de la santé toutes les applications en cours d’exécution dans le cluster) sont respectées lors de la mise à niveau.

Si les stratégies de bon fonctionnement du cluster ne sont pas remplies, la mise à niveau est annulée. Puis, un e-mail est envoyé au propriétaire de l’abonnement. L’e-mail contient les informations suivantes :

- Notification que nous avons dû annuler une mise à niveau de cluster.
- Suggéré des actions correctives, le cas échéant.
- Le nombre de jours (n) jusqu'à ce que nous allons exécuter la Phase 2.

Nous essayons d’exécuter la mise à niveau même quelques fois de plus dans le cas où les mises à niveau a échoué pour des raisons d’infrastructure. Après les n jours à partir de la date de qu'envoi de l’e-mail, nous passer à la Phase 2.

Si les stratégies de bon fonctionnement du cluster sont remplies, la mise à niveau est considérée comme réussie et marquées comme achevée. Cela peut se produire au cours de la mise à niveau initiale ou des mise à niveau rediffusions dans cette phase. Il n’y a aucun e-mail de confirmation d’une exécution réussie. C’est pour éviter d’envoyer vous trop de courriels : réception d’un e-mail devez être considérée comme une exception normale. Nous pensons que la plupart des mises à niveau de cluster se dérouler sans impact sur la disponibilité de votre application.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Phase 2 : Une mise à niveau est effectuée à l’aide de stratégies d’intégrité par défaut uniquement

Les stratégies d’intégrité dans cette phase sont définies de manière à ce que le nombre d’applications qui étaient en bonne santé au début de la mise à niveau reste la même pendant toute la durée du processus de mise à niveau. Comme dans la Phase 1, les mises à niveau de la Phase 2 passez un domaine de mise à niveau à la fois, et les applications en cours d’exécution dans le cluster continuent de fonctionner sans interruption de service. Les stratégies de bon fonctionnement du cluster (une combinaison de l’état du nœud et de la santé toutes les applications en cours d’exécution dans le cluster) sont respectées pendant toute la durée de la mise à niveau.

Si les stratégies de bon fonctionnement du cluster en vigueur ne sont pas remplies, la mise à niveau est annulée. Puis, un e-mail est envoyé au propriétaire de l’abonnement. L’e-mail contient les informations suivantes :

- Notification que nous avons dû annuler une mise à niveau de cluster.
- Suggéré des actions correctives, le cas échéant.
- Le nombre de jours (n) jusqu'à ce que nous allons exécuter la Phase 3.

Nous essayons d’exécuter la mise à niveau même quelques fois de plus dans le cas où les mises à niveau a échoué pour des raisons d’infrastructure. Un e-mail de rappel est envoyé à deux jours avant les n jours. Après les n jours à partir de la date de qu'envoi de l’e-mail, nous passer à la Phase 3. Les e-mails que nous vous enverrons dans la Phase 2 doivent être prises très au sérieux et actions correctives doivent être prises.

Si les stratégies de bon fonctionnement du cluster sont remplies, la mise à niveau est considérée comme réussie et marquées comme achevée. Cela peut se produire au cours de la mise à niveau initiale ou des mise à niveau rediffusions dans cette phase. Il n’y a aucun e-mail de confirmation d’une exécution réussie.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Phase 3 : Une mise à niveau est effectuée à l’aide de stratégies d’intégrité agressif

Ces stratégies d’intégrité lors de cette phase sont adressent à la fin de la mise à niveau plutôt que de la santé des applications. Mises à niveau de cluster très peu finissent dans cette phase. Si votre cluster obtient pour cette phase, il existe de bonnes chances que votre application devient défectueuse ou perte de disponibilité.

Comme pour les deux autres phases, mises à niveau de la Phase 3 passez un domaine de mise à niveau à la fois.

Si les stratégies de bon fonctionnement du cluster ne sont pas remplies, la mise à niveau est annulée. Nous essayons d’exécuter la mise à niveau même quelques fois de plus dans le cas où les mises à niveau a échoué pour des raisons d’infrastructure. Après cela, le cluster est épinglé, afin qu’il ne recevront plus de mises à niveau et/ou de prise en charge.

Un e-mail contenant cette information est envoyé au propriétaire de l’abonnement, ainsi que les actions correctives. Nous n’attendent pas les clusters pour obtenir un état dans lequel la Phase 3 a échoué.

Si les stratégies de bon fonctionnement du cluster sont remplies, la mise à niveau est considérée comme réussie et marquées comme achevée. Cela peut se produire au cours de la mise à niveau initiale ou des mise à niveau rediffusions dans cette phase. Il n’y a aucun e-mail de confirmation d’une exécution réussie.

## <a name="cluster-configurations-that-you-control"></a>Configurations de cluster que vous contrôlez

En plus de la possibilité de définir le cluster en mode de mise à niveau, voici les configurations que vous pouvez modifier sur un cluster live.

### <a name="certificates"></a>Certificats

Vous pouvez ajouter ou supprimer facilement des certificats pour le cluster et le client via le portail. Reportez-vous à [ce document pour obtenir des instructions détaillées](service-fabric-cluster-security-update-certs-azure.md)

![Capture d’écran qui affiche les empreintes de certificats dans le portail Azure.][CertificateUpgrade]


### <a name="application-ports"></a>Ports d’application

Vous pouvez modifier des ports d’application en modifiant les propriétés de ressource d’équilibrage de la charge qui sont associées avec le type de nœud. Vous pouvez utiliser le portail, ou vous pouvez utiliser le Gestionnaire de ressources PowerShell directement.

Pour ouvrir un nouveau port sur tous les ordinateurs virtuels dans un type de nœud, effectuez les opérations suivantes :

1. Ajouter une nouvelle sonde à l’équilibreur de charge approprié.

    Si vous avez déployé votre cluster à l’aide du portail, les équilibreurs de charge sont nommés « Livres-nom de la ressource groupe-NodeTypename », un pour chaque type de nœud. Étant donné que les noms d’équilibrage de la charge sont uniques seulement au sein d’un groupe de ressources, il est préférable du faire si vous les recherchez sous un groupe de ressources spécifique.

    ![Capture d’écran montre l’ajout d’une sonde à un équilibreur de charge dans le portail.][AddingProbes]

2. Ajouter une nouvelle règle à l’équilibreur de charge.

    Ajouter une nouvelle règle à la même équilibreur de charge à l’aide de la sonde que vous avez créé à l’étape précédente.

    ![Ajout d’une nouvelle règle à un équilibreur de charge dans le portail.][AddingLBRules]


### <a name="placement-properties"></a>Propriétés de positionnement

Pour chacun des types de nœuds, vous pouvez ajouter des propriétés de positionnement personnalisés que vous souhaitez utiliser dans vos applications. NodeType est une propriété par défaut que vous pouvez utiliser sans l’ajouter explicitement.

>[AZURE.NOTE] Pour plus d’informations sur l’utilisation de contraintes de placement et comment définir les propriétés du nœud, reportez-vous à la section « Contraintes de Placement et propriétés nœud » dans le Document du Gestionnaire de ressources du Cluster Service Fabric sur la [Description de votre Cluster](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="capacity-metrics"></a>Mesures de capacité

Pour chacun des types de nœuds, vous pouvez ajouter des mesures de capacité personnalisé que vous souhaitez utiliser dans vos applications à charge de l’état. Pour plus d’informations sur l’utilisation des mesures de capacité à rapport charger, consultez les Documents de Service Gestionnaire de ressources du Cluster Fabric sur la [Description de votre Cluster](service-fabric-cluster-resource-manager-cluster-description.md) et [métriques et charge](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Paramètres de mise à niveau de fabric - stratégies de santé

Vous pouvez spécifier des stratégies de santé personnalisée pour la mise à niveau de fabric. Si vous avez configuré votre cluster aux mises à niveau de fabric automatique, puis ces stratégies appliqués à la Phase 1 de la mise à niveau automatique de fabric.
Si vous avez configuré votre cluster pour tissu manuelle de mises à niveau, puis ces stratégies appliqués chaque fois que vous sélectionnez une nouvelle version, le système pour lancer la mise à niveau de fabric dans votre cluster. Si vous ne substituez pas les stratégies, les valeurs par défaut sont utilisés.

Vous pouvez spécifier des stratégies personnalisées ou passer en revue les paramètres en cours sous la lame de « mise à niveau de fabric », en sélectionnant les paramètres de mise à niveau avancées. Examinez l’image suivante Comment. 

![Gestion des stratégies de santé personnalisés][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Personnaliser les paramètres de tissu pour votre cluster

Reportez-vous aux [paramètres du fabric cluster service de fabric](service-fabric-cluster-fabric-settings.md) sur quoi et comment vous pouvez les personnaliser.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Correctifs du système d’exploitation sur les ordinateurs virtuels qui composent le cluster

Cette fonctionnalité est prévue pour l’avenir, comme une fonction automatisée. Mais vous êtes actuellement responsable pour corriger vos ordinateurs virtuels. Vous devez effectuer cette machine un virtuelle à la fois, afin que vous ne prenez pas plus d’un à la fois.

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Mises à niveau du système d’exploitation sur les ordinateurs virtuels qui composent le cluster

Si vous devez mettre à niveau l’image du système d’exploitation sur les ordinateurs virtuels du cluster, vous devez le faire un ordinateur virtuel à la fois. Vous êtes responsable de cette mise à niveau : il n’existe actuellement aucune automation pour cette.

## <a name="next-steps"></a>Étapes suivantes
- Apprenez à personnaliser certains [paramètres de fabric cluster service fabric](service-fabric-cluster-fabric-settings.md)
- Découvrez comment faire [évoluer votre cluster en entrée et en sortie](service-fabric-cluster-scale-up-down.md)
- En savoir plus sur les [mises à niveau de l’application](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG