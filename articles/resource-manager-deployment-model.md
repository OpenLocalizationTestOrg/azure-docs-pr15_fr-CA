<properties
   pageTitle="Le Gestionnaire de ressources et classique de déploiement | Microsoft Azure"
   description="Décrit les différences entre le modèle de déploiement du Gestionnaire de ressources et de l’interface classique (ou la gestion des services) modèle de déploiement."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Le Gestionnaire de ressources Azure et déploiement classique : comprendre les modèles de déploiement et de l’état de vos ressources

Dans cette rubrique, vous en savoir plus sur le Gestionnaire de ressources Azure et modèles de déploiement classique, l’état de vos ressources, et pourquoi vos ressources ont été déployés avec une ou l’autre. Le Gestionnaire de ressources et les modèles de déploiement classique représentent les deux façons différentes de déployer et gérer vos solutions Azure. Vous travaillez avec eux via deux ensembles d’API différentes, et les ressources déployées peuvent contenir des différences importantes. Les deux modèles ne sont pas totalement compatibles entre eux. Cette rubrique décrit ces différences.

Pour simplifier le déploiement et la gestion des ressources, Microsoft recommande d’utiliser le Gestionnaire de ressources pour toutes les nouvelles ressources. Dans la mesure du possible, Microsoft recommande que vous redéployez existant par le biais du Gestionnaire de ressources.

Si vous êtes gestionnaire de ressources, vous souhaiterez tout d’abord passer en revue la terminologie définie dans la [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Historique des modèles de déploiement

Azure fourni à l’origine uniquement le modèle de déploiement classique. Dans ce modèle, chaque ressource existait indépendamment ; Il n’a aucun moyen de regrouper les ressources connexes. Au lieu de cela, vous deviez manuellement les ressources constitués de votre solution ou votre application de suivre et n’oubliez pas de les gérer dans une approche coordonnée. Pour déployer une solution, vous deviez créer chaque ressource individuellement via le portail classique ou créer un script qui a déployé toutes les ressources dans l’ordre correct. Pour supprimer une solution, que vous deviez supprimer chaque ressource individuellement. Vous ne pouvez pas facilement appliquer et mettre à jour les stratégies de contrôle d’accès aux ressources liées. Enfin, vous ne pouvez pas appliquer des balises à des ressources pour les étiqueter avec des conditions qui vous aident à surveiller vos ressources et gérer la facturation.

En 2014, Azure, a introduit le Gestionnaire de ressources, qui ajoute le concept d’un groupe de ressources. Un groupe de ressources est un conteneur pour les ressources qui partagent un cycle de vie commune. Le modèle de déploiement du Gestionnaire de ressources offre plusieurs avantages :

- Vous pouvez déployer, gérer et surveiller tous les services de votre solution en tant que groupe, plutôt que de gérer ces services individuellement.
- Vous pouvez déployer votre solution tout au long de leur cycle de vie et à plusieurs reprises font confiance à que vos ressources sont déployés dans un état cohérent.
- Vous pouvez appliquer le contrôle d’accès à toutes les ressources dans votre groupe de ressources, et que ces stratégies sont appliquées automatiquement lorsque de nouvelles ressources sont ajoutés au groupe de ressources.
- Vous pouvez appliquer des balises aux ressources pour organiser logiquement de toutes les ressources de votre abonnement.
- Vous pouvez utiliser la Notation JSON (JavaScript Object) pour définir l’infrastructure de votre solution. Le fichier JSON est connu en tant que gestionnaire de ressources du modèle.
- Vous pouvez définir les dépendances entre les ressources afin de les déployer dans le bon ordre.

Lorsque le Gestionnaire de ressources a été ajouté, toutes les ressources ont été ajoutés avec effet rétroactif aux groupes de ressources par défaut. Si vous créez une ressource via un déploiement classique maintenant, la ressource est automatiquement créée au sein d’un groupe de ressources par défaut pour ce service, même si vous n’avez pas spécifié ce groupe de ressources au moment du déploiement. Toutefois, simplement existant au sein d’un groupe de ressources ne signifie pas que la ressource a été convertie au modèle de gestionnaire de ressources. Nous allons étudier la manière dont chaque service gère les deux modèles de déploiement dans la section suivante. 

## <a name="understand-support-for-the-models"></a>Comprendre la prise en charge pour les modèles 

Lorsque vous choisissez le modèle de déploiement à utiliser pour vos ressources, trois scénarios sont à connaître :

1. Le service prend en charge le Gestionnaire de ressources et fournit uniquement un seul type.
2. Le service prend en charge le Gestionnaire de ressources, mais fournit des deux types suivants : un pour le Gestionnaire de ressources et un pour classique. Ce scénario s’applique uniquement aux ordinateurs virtuels, des comptes de stockage et des réseaux virtuels.
3. Le service ne gère pas le Gestionnaire de ressources.

Pour découvrir si un service prend en charge le Gestionnaire de ressources, consultez [le Gestionnaire de ressources pris en charge les fournisseurs](resource-manager-supported-services.md).

Si le service que vous souhaitez utiliser ne gère pas le Gestionnaire de ressources, vous devez continuer en utilisant le déploiement classique.

Si le service prend en charge le Gestionnaire de ressources et **n’est pas** une machine virtuelle, compte de stockage ou de réseau virtuel, vous pouvez utiliser le Gestionnaire de ressources sans difficulté.

Pour les machines virtuelles, comptes de stockage et des réseaux virtuels, si la ressource a été créée par le biais de déploiement classique, vous devez continuer à fonctionner sur ce à travers des opérations classiques. Si l’ordinateur virtuel, le compte de stockage ou le réseau virtuel a été créé via le Gestionnaire de ressources du déploiement, vous devez continuer à l’aide des opérations de gestionnaire de ressources. Cette distinction peut être déroutante lors de votre abonnement contient une combinaison de ressources créés par le Gestionnaire de ressources et de déploiement classique. Cette combinaison de ressources peut créer des résultats inattendus, car les ressources ne prennent pas en charge les mêmes opérations.

Dans certains cas, une commande du Gestionnaire de ressources peut récupérer des informations sur la ressource créée par le biais de déploiement classique, ou peut effectuer une tâche administrative, telle que le déplacement d’une ressource classique à un autre groupe de ressources. Toutefois, ces cas ne doivent pas donner l’impression que le type prend en charge les opérations du Gestionnaire de ressources. Par exemple, supposons que vous avez un groupe de ressources qui contient un ordinateur virtuel qui a été créé avec un déploiement classique. Si vous exécutez la commande du Gestionnaire de ressources PowerShell suivante :

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Il renvoie la machine virtuelle :
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

Toutefois, Gestionnaire de ressources applet de commande **Get-AzureRmVM** ne renvoie que les ordinateurs virtuels déployés par le biais du Gestionnaire de ressources. La commande suivante ne renvoie pas la machine virtuelle créée par le biais de déploiement classique.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Uniquement les ressources créées par le biais de balises de prise en charge du Gestionnaire de ressources. Impossible d’appliquer des balises pour les ressources classiques.

## <a name="resource-manager-characteristics"></a>Caractéristiques du Gestionnaire de ressources

Pour vous aider à comprendre les deux modèles, passons en revue les caractéristiques des types de gestionnaire de ressources :

- Créé via le [portail Azure](https://portal.azure.com/).

     ![Azure portal](./media/resource-manager-deployment-model/portal.png)

     Pour le calcul, stockage et ressources réseau, vous avez la possibilité d’utiliser le Gestionnaire de ressources ou de classique de déploiement. Sélectionnez **Le Gestionnaire de ressources**.

     ![Gestionnaire de ressources du déploiement](./media/resource-manager-deployment-model/select-resource-manager.png)

- Créés avec la version du Gestionnaire de ressources des applets de commande PowerShell d’Azure. Ces commandes ont le format *Verbe-AzureRmNoun*.

        New-AzureRmResourceGroupDeployment

- Créé par le biais de l' [REST API de gestionnaire de ressources Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) pour les opérations de reste.

- Créé par le biais de commandes CLI d’Azure s’exécutés en mode **arm** .

        azure config mode arm
        azure group deployment create 

- Le type de ressource n’inclut pas **(classique)** dans le nom. L’image suivante montre le type en tant que **compte de stockage**.

    ![application Web](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Caractéristiques de déploiement classique

Vous pouvez également connaître le modèle de déploiement classique comme modèle de gestion des services.

Les ressources créées dans le modèle de déploiement classique partagent les caractéristiques suivantes :

- Créé via le [portail classique](https://manage.windowsazure.com)

     ![Portail classique](./media/resource-manager-deployment-model/classic-portal.png)

     Ou bien, le portail Azure et que vous spécifiez un déploiement **classique** (pour le calcul, stockage et réseau).

     ![Déploiement classique](./media/resource-manager-deployment-model/select-classic.png)

- Créé par le biais de la version de gestion des services des applets de commande PowerShell d’Azure. Ces noms de commandes possèdent le format *Verbe-AzureNoun*.

        New-AzureVM 

- Créé par le biais de l' [API REST de gestion de Service](https://msdn.microsoft.com/library/azure/ee460799.aspx) pour les opérations de reste.
- Créé par le biais de commandes CLI d’Azure s’exécutés en mode **d’asm** .

        azure config mode asm
        azure vm create 

- Le type de ressource inclut **(classique)** dans le nom. L’image suivante montre le type en tant que **compte de stockage (classique)**.

    ![type classique](./media/resource-manager-deployment-model/classic-type.png)

Vous pouvez utiliser le portail Azure pour gérer les ressources qui ont été créées via le déploiement classique.

## <a name="changes-for-compute-network-and-storage"></a>Modifications de calcul, de réseau et de stockage

Le diagramme suivant affiche les ressources informatiques, de réseau et de stockage déployés par l’intermédiaire du Gestionnaire de ressources.

![Architecture du Gestionnaire de ressources](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Notez les relations suivantes entre les ressources :

- Toutes les ressources existent au sein d’un groupe de ressources.
- L’ordinateur virtuel dépend d’un compte de stockage spécifique défini dans le fournisseur de ressources de stockage pour stocker ses disques dans le stockage blob (obligatoire).
- L’ordinateur virtuel est relié à une carte de réseau spécifique défini dans le fournisseur de ressources réseau (obligatoire) et une disponibilité définies dans le fournisseur de ressources de calcul (facultatif).
- La carte réseau fait référence à adresse IP attribuée la machine virtuelle (obligatoire), le sous-réseau du réseau virtuel pour la machine virtuelle (obligatoire) et à un groupe de sécurité réseau (facultatif).
- Le sous-réseau au sein d’un réseau virtuel est relié à un groupe de sécurité réseau (facultatif).
- Le back-end pool d’adresses IP qui inclut la carte réseau d’un ordinateur virtuel (facultatif) et fait référence à une charge équilibrage publique ou privée IP adresse (facultative) fait référence à l’instance d’équilibrage de la charge.

Voici les composants et leurs relations pour un déploiement classique :

![architecture classique](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

La solution classique pour l’hébergement d’une machine virtuelle comprend :

- Un service en nuage requis qui agit comme un conteneur pour héberger virtual machines (calculer). Machines virtuelles sont automatiquement fournis avec une carte d’interface réseau (NIC) et une adresse IP attribuée par Azure. En outre, le service cloud contient une instance d’équilibreur de charge externe, une adresse IP publique et points de terminaison par défaut pour permettre à distance Bureau et distant PowerShell du trafic pour les machines virtuelles basées sur Windows et SSH (Secure Shell) pour les machines virtuelles basées sur Linux.
- Un compte de stockage requis qui stocke les disques durs virtuels d’une machine virtuelle, y compris le système d’exploitation, des disques de données temporaires et d’autres (stockage).
- Un réseau virtuel facultatif qui agit comme un conteneur supplémentaire, dans lequel vous pouvez créer une structure de sous-réseaux et désigner le sous-réseau sur lequel se trouve l’ordinateur virtuel (réseau).

Le tableau suivant décrit les modifications de l’interagissent entre les fournisseurs de ressources informatiques, de réseau et de stockage :

 Élément | Classique | Gestionnaire de ressources
 ---|---|---
| Service de cloud pour les Machines virtuelles |  Service cloud a été récipient destiné à contenir les ordinateurs virtuels requis de disponibilité à partir de la plate-forme et l’équilibrage de la charge. | Service cloud n’est plus un objet requis pour la création d’une Machine virtuelle en utilisant le nouveau modèle. |
| Réseaux virtuels | Un réseau virtuel est facultatif pour la machine virtuelle. Si inclus, le réseau virtuel ne peut pas être déployé avec le Gestionnaire de ressources. | Ordinateur virtuel requiert un réseau virtuel qui a été déployé avec le Gestionnaire de ressources. |
| Comptes de stockage | L’ordinateur virtuel requiert un compte de stockage contenant les disques durs virtuels avec le système d’exploitation, les disques de données temporaires et d’autres. | L’ordinateur virtuel requiert un compte de stockage pour stocker ses disques dans le stockage blob. |
| Jeux de disponibilité | Disponibilité de la plate-forme a été indiquée en configurant le même « AvailabilitySetName » sur les Machines virtuelles. Le nombre maximal de domaines d’erreur est 2. | Ensemble de disponibilité est une ressource exposée par le fournisseur de Microsoft.Compute. Machines virtuelles qui nécessitent une disponibilité élevée doit être inclus dans l’ensemble de la disponibilité. Le nombre maximal de domaines d’erreur est maintenant 3. |
| Groupes d’affinité | Groupes d’affinité étaient nécessaires pour créer des réseaux virtuels. Toutefois, avec l’introduction de réseaux virtuels régionaux, qui n’a pas requis plus. |Pour simplifier, le concept de groupes d’affinité n’existe pas dans les API exposées par le biais du Gestionnaire de ressources Azure. |
| Équilibrage de la charge    | Création d’un Service Cloud fournit un équilibreur de charge implicite pour les ordinateurs virtuels déployés. | L’équilibreur de charge est une ressource exposée par le fournisseur de Microsoft.Network. L’équilibreur de charge doit faire référence à l’interface réseau principale des ordinateurs virtuels qui doit être équilibré. Équilibreurs de charge peuvent être internes ou externes. Le back-end pool d’adresses IP qui inclut la carte réseau d’un ordinateur virtuel (facultatif) et fait référence à une charge équilibrage publique ou privée IP adresse (facultative) fait référence à une instance d’équilibrage de la charge. [En savoir plus.](../articles/resource-groups-networking.md) |
|Adresse IP virtuelle | Les Services en nuage d’obtenir une adresse IP virtuelle par défaut (adresse IP virtuelle) lorsqu’un ordinateur virtuel est ajouté à un service en nuage. L’adresse IP virtuelle est l’adresse associée à l’équilibreur de charge implicite.    | Adresse IP publique est une ressource exposée par le fournisseur de Microsoft.Network. Adresse IP publique peuvent être statiques (réservé) ou dynamique. Dynamique IPs publics peuvent être affectés à un équilibreur de charge. Adresses IP publique peut être sécurisé à l’aide de groupes de sécurité. |
|Adresse IP réservée|   Vous pouvez réserver une adresse IP dans Azure et l’associer à un Service en nuage pour s’assurer que l’adresse IP est fixe.   | Adresse IP publique peuvent être créé en mode « Statique », et il offre la même fonctionnalité en tant que « Adresse IP réservée ». Statique IPs publics peuvent uniquement être affectés à un équilibreur de charge dès maintenant. |
|Adresse IP publique (PIP) par machine virtuelle | Adresses IP publiques peut également être associés à une machine virtuelle directement. | Adresse IP publique est une ressource exposée par le fournisseur de Microsoft.Network. Adresse IP publique peuvent être statiques (réservé) ou dynamique. Toutefois, uniquement les IPs publics dynamiques peuvent avoir à une Interface réseau pour obtenir une adresse IP publique par machine virtuelle dès maintenant. |
|Points de terminaison| Les points de terminaison d’entrée nécessaire pour être configuré sur un ordinateur virtuel à être ouvrez connectivité pour certains ports. Un des modes de courants de connexion à des ordinateurs virtuels faits en définissant des points de terminaison d’entrée. | Règles de trafic entrant NAT peut être configurés sur les équilibreurs de charge pour atteindre la même capacité d’activation des points de terminaison sur des ports spécifiques pour la connexion aux ordinateurs virtuels. |
|Nom DNS| Un service en nuage obtenez un nom de DNS globalement unique implicite. Par exemple : `mycoffeeshop.cloudapp.net`. | Les noms DNS sont des paramètres facultatifs qui peuvent être spécifiés sur une ressource d’adresse IP publique. Le nom de domaine complet est dans le format suivant : `<domainlabel>.<region>.cloudapp.azure.com`. |
|Interfaces réseau | Primaire et secondaire Interface de réseau et de ses propriétés ont été définies en tant que la configuration réseau d’un ordinateur virtuel. | Interface réseau est une ressource exposée par le fournisseur de Microsoft.Network. Le cycle de vie de l’Interface réseau n’est pas lié à une Machine virtuelle. Il fait référence à adresse IP attribuée la machine virtuelle (obligatoire), le sous-réseau du réseau virtuel pour la machine virtuelle (obligatoire) et à un groupe de sécurité réseau (facultatif). |

Pour en savoir plus sur la connexion des réseaux virtuels à partir de modèles de déploiement différentes, voir [connexion des réseaux virtuels à partir de modèles de déploiement différentes dans le portail](./vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migrer à partir de standard pour le Gestionnaire de ressources

Si vous êtes prêt à migrer vos ressources de déploiement classique pour le déploiement du Gestionnaire de ressources, voir :

1. [Technique approfondie sur la migration de plate-forme prise en charge de classique pour le Gestionnaire de ressources Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Migration de la plate-forme prise en charge des ressources du IaaS de classique pour le Gestionnaire de ressources Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Migration des ressources IaaS de classique pour le Gestionnaire de ressources Azure à l’aide de PowerShell d’Azure](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Migrer les ressources IaaS de classique pour le Gestionnaire de ressources Azure en utilisant l’infrastructure du langage commun Azure](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Forum aux Questions

**Est-il possible de créer un ordinateur virtuel à l’aide d’Azure le Gestionnaire de ressources pour le déploiement dans un réseau virtuel ou d’un compte de stockage créés à l’aide de déploiement classique ?**

Ce n’est pas pris en charge. Vous ne pouvez pas utiliser Azure le Gestionnaire de ressources pour déployer un ordinateur virtuel dans un réseau virtuel qui a été créé à l’aide de déploiement classique.

**Est-il possible de créer un ordinateur virtuel à l’aide du Gestionnaire de ressources à partir d’une image de l’utilisateur qui a été créée à l’aide de l’API de gestion de Service Azure Azure ?**

Ce n’est pas pris en charge. Toutefois, vous pouvez copier les fichiers de disque dur virtuel à partir d’un compte de stockage qui a été créé à l’aide de l’API de gestion de Service et les ajouter à un nouveau compte créé par le biais du Gestionnaire de ressources Azure.

**Quel est l’impact sur le quota pour mon abonnement ?**

Les quotas pour les ordinateurs virtuels, réseaux virtuels et les comptes de stockage créés par le Gestionnaire de ressources Azure sont séparés des autres contingents. Chaque abonnement Obtient des quotas pour créer les ressources en utilisant les nouvelles API. Vous pouvez en savoir plus sur les quotas supplémentaires [ici](../articles/azure-subscription-service-limits.md).

**Puis-je continuer à utiliser mes scripts automatisés pour la mise en service des machines virtuelles, les réseaux virtuels et des comptes de stockage via les API du Gestionnaire de ressources ?**

Tous les scripts que vous avez créés et automation continuent de fonctionner pour les ordinateurs virtuels existants, réseaux virtuels créés sous le mode de gestion des services Azure. Toutefois, les scripts doivent être mis à jour pour utiliser le nouveau schéma pour créer les mêmes ressources en mode Gestionnaire de ressources.

**Où puis-je trouver des exemples de modèles du Gestionnaire de ressources Azure ?**

Vous trouverez un ensemble complet de modèles de starter sur les [Modèles de QuickStart Azure le Gestionnaire de ressources](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Étapes suivantes

- Pour guider la création d’un modèle qui définit une machine virtuelle, le compte de stockage et le réseau virtuel, consultez [procédure pas à pas de gestionnaire de ressources du modèle](resource-manager-template-walkthrough.md).
- Pour voir les commandes pour le déploiement d’un modèle, consultez [déploiement d’une application avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy.md).
