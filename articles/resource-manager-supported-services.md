<properties
   pageTitle="Le Gestionnaire de ressources des services pris en charge | Microsoft Azure"
   description="Décrit les fournisseurs de ressources qui prennent en charge le Gestionnaire de ressources, leurs schémas et versions d’API disponibles et les régions qui peuvent héberger des ressources."
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
   ms.date="10/25/2016"
   ms.author="magoedte;tomfitz"/>

# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Fournisseurs de gestionnaire de ressources, des régions, des versions de l’API et des schémas

Azure Resource Manager fournit une nouvelle façon de déployer et de gérer les services qui composent vos applications. Les services de la plupart mais pas tous, prennent en charge le Gestionnaire de ressources, et certains services prennent en charge le Gestionnaire de ressources que partiellement. Cette rubrique fournit une liste de fournisseurs de ressources pris en charge pour le Gestionnaire de ressources Azure.

Lors du déploiement de vos ressources, vous devez également connaître les régions prennent en charge les ressources et les API des versions sont disponibles pour les ressources. La section [les régions pris en charge](#supported-regions) vous indique comment déterminer les zones de travail pour votre abonnement et les ressources. La section [versions des API de prise en charge](#supported-api-versions) vous indique comment déterminer les versions de l’API que vous pouvez utiliser.

Pour voir quels services sont pris en charge dans le portail Azure et d’un portail classique, consultez le [graphique de la disponibilité de portail Azure](https://azure.microsoft.com/features/azure-portal/availability/). Pour voir quels services prend en charge le déplacement de ressources, voir [déplacer des ressources vers le nouveau groupe de ressources ou d’abonnement](resource-group-move-resources.md).

Les tableaux suivants répertorient les prise en charge du déploiement de Microsoft services et la gestion par le Gestionnaire de ressources et qui n’est pas le cas. Le lien dans la colonne **Démarrage rapide modèles** envoie une requête au référentiel de modèles de démarrage rapide d’Azure pour le fournisseur de ressources spécifié. Modèles de démarrage rapide sont ajoutés et mis à jour fréquemment. La présence d’un lien pour un service particulier ne signifie pas nécessairement que la requête renvoie des modèles à partir du référentiel. Il existe également de nombreux fournisseurs de ressources de tiers qui prennent en charge le Gestionnaire de ressources. Vous apprenez à voir tous les fournisseurs de ressources dans la section [fournisseurs de ressources et de types](#resource-providers-and-types) .


## <a name="compute"></a>Calculer

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------------------------ |-------- | ------ | ------ |
| Traitement par lots   | Oui     | [Lot reste](https://msdn.microsoft.com/library/azure/dn820158.aspx) | [2015-12-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json)       |  |
|Conteneur | Oui | [Service de conteneur reste](https://msdn.microsoft.com/library/azure/mt711470.aspx) | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) | [Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Services du cycle de vie de Dynamics | Oui  |      |        |  |
| Jeux d’échelle | Oui | [Échelle définie reste](https://msdn.microsoft.com/library/azure/mt705635.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) | 
| TISSU de service | Oui  | [Reste de tissu de service](https://msdn.microsoft.com/library/azure/dn707692.aspx)  |      | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Ordinateurs virtuels | Oui | [RESTE DE LA MACHINE VIRTUELLE](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [desmachines virtuelles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Virtual Machines (classique) | Limitée | - | - | - |
| Application distante | N°      | -  | - | - |
| Services en nuage (classique) | Limité (voir ci-dessous) | - | - | - |

Virtual Machines (classic) fait référence à des ressources qui ont été déployés via le modèle de déploiement classique, et non via le modèle de déploiement du Gestionnaire de ressources. En général, ces ressources ne prennent pas en charge les opérations de gestionnaire de ressources, mais il existe certaines opérations qui ont été activées. Pour plus d’informations sur ces modèles de déploiement, consultez [déploiement de présentation du Gestionnaire de ressources et de déploiement classique](resource-manager-deployment-model.md). 

Les Services en nuage (classiques) peuvent être utilisés avec d’autres ressources classiques ; Toutefois, les ressources classiques ne tirent pas parti de toutes les fonctionnalités du Gestionnaire de ressources et ne sont pas une bonne solution pour les solutions futures. Au lieu de cela, envisagez la modification de votre infrastructure d’applications pour utiliser les ressources des espaces de noms Microsoft.Compute, Microsoft.Storage et Microsoft.Network.


## <a name="networking"></a>Mise en réseau

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | -------  | -------- | ------ | ------ |
| Passerelle d’application | Oui | [Passerelle d’application reste](https://msdn.microsoft.com/library/azure/mt684939.aspx)   |        | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS     | Oui | [RESTE DE DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)         | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute | Oui  | [ExpressRoute reste](https://msdn.microsoft.com/library/azure/mt586720.aspx)  |       | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| L’équilibreur de charge | Oui  | [Équilibreur de charge reste](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Traffic Manager | Oui | [Traffic Manager reste](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json)  | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Réseaux virtuels | Oui| [Réseau virtuel reste](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Passerelle VPN | Oui | [Passerelle de réseau reste](https://msdn.microsoft.com/library/azure/mt163859.aspx) |  | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[connexions](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |


## <a name="storage"></a>Stockage

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------- | ------ |
| Stockage | Oui  | [Stockage reste](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Compte de stockage](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple | Oui  |         |        |  |

## <a name="databases"></a>Bases de données

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------- | ------ |
| DocumentDB | Oui  | [DocumentDB reste](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json)  | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Redis de Cache | Oui |   | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Base de données SQL | Oui | [Base de données SQL reste](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [2014-04-01-aperçu](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Entrepôt de données SQL | Oui |   |      |


## <a name="web--mobile"></a>Web et Mobile

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Applications d’API | Oui |   | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Applications d’API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Gestion de l’API | Oui  | [Gestion de l’API reste](https://msdn.microsoft.com/library/azure/dn776326.aspx) | [2016-07-07](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json)       | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Modérateur du contenu | Oui |   |   |   |
| Application de la fonction | Oui |   |   | [functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Applications de logique | Oui   | [API du Service de flux de travail reste](https://msdn.microsoft.com/library/azure/mt643787.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Applications mobiles | Oui |  | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json)  | [mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code)   |
| Engagements mobiles | Oui  |  [Engagement mobile reste](https://msdn.microsoft.com/library/azure/mt683754.aspx)        |        | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Recherche | Oui  | [Recherche reste](https://msdn.microsoft.com/library/azure/dn798935.aspx) |  | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Applications Web | Oui |          | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="analytics"></a>Analytique

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | -------  | -------- | ------ | ------ |
| Catalogue de données | Oui  | [Catalogue de données reste](https://msdn.microsoft.com/library/azure/mt267595.aspx)  | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |  |
| Fabrique de données | Oui | [Usine de données reste](https://msdn.microsoft.com/library/azure/dn906738.aspx) |    | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Données lac Analytique | Oui |   |   [2015-10-01-aperçu](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Magasin de données lac | Oui  | [RESTE du magasin de données lac](https://msdn.microsoft.com/library/azure/mt693424.aspx) | [2015-10-01-aperçu](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights | Oui | [HDInsights reste](https://msdn.microsoft.com/library/azure/mt622197.aspx) |        | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Apprentissage automatique | Oui | [Apprentissage du reste de l’ordinateur](https://msdn.microsoft.com/library/azure/mt767538.aspx)        | [2016-05-01-aperçu](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) |
| Analytique de flux | Oui  | [Analytique de vapeur reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)   |        |  |
| Alimentation BI | Oui | [Alimentation BI incorporé reste](https://msdn.microsoft.com/library/azure/mt712303.aspx) | [2016-01-29](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) |  |

## <a name="intelligence"></a>Intelligence

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Services cognitifs | Oui |  | [2016-02-01-aperçu](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) |   |

## <a name="internet-of-things"></a>Internet des objets

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Concentrateur d’événements | Oui  | [Concentrateur d’événements reste](https://msdn.microsoft.com/library/azure/dn790674.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code)  |
| IoTHubs | Oui | [Concentrateur IoT reste](https://msdn.microsoft.com/library/azure/mt589014.aspx) | [2016-02-03](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Concentrateurs de notification | Oui | [Concentrateur de notification reste](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Support et CDN

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| PAIE CANADA-FICHIER | Oui | [RESTE DU CANADA](https://msdn.microsoft.com/library/azure/mt634456.aspx)  | [04-02-2016](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Service de support | Oui | [RESTE de Services multimédia](https://msdn.microsoft.com/library/azure/hh973617.aspx)  | [2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |


## <a name="hybrid-integration"></a>Intégration de hybride

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Services BizTalk | Oui |          | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |  |
| Service de récupération | Oui | [Récupération de site reste](https://msdn.microsoft.com/library/azure/mt750497.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) | [Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Bus des services | Oui | [Bus de service reste](https://msdn.microsoft.com/library/azure/mt639375.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json)       | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Identité et gestion des accès 

Azure Active Directory fonctionne avec le Gestionnaire de ressources pour activer le contrôle d’accès basé sur des rôles pour votre abonnement. Pour en savoir plus sur l’utilisation de Active Directory et contrôle d’accès basé sur les rôles, consultez [Contrôle d’accès basé sur les rôles d’Azure](./active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Services pour les développeurs 

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Aperçu de l’application | Oui  | [RESTE de perspectives](https://msdn.microsoft.com/library/azure/dn931943.aspx)  | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.Insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Maps | Oui  |          |        |  |
| Ateliers de DevTest | Oui |   | [2016-05-15](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code)  |
| Compte de Visual Studio | Oui   |          | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |  |

## <a name="management-and-security"></a>Gestion et sécurité

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Automation | Oui | [Automation reste](https://msdn.microsoft.com/library/azure/mt662285.aspx) | [2015-10-31](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json)       | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Chambre forte de clé | Oui | [Chambre forte de clé reste](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Chambre forte de clé](resource-manager-template-keyvault.md)<br />[Secret de clé coffre-fort](resource-manager-template-keyvault-secret.md)   | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Conseils opérationnels | Oui |          |        |  |
| Planificateur | Oui  | [Planificateur de repos](https://msdn.microsoft.com/library/azure/mt629143.aspx)     | [2016-03-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Sécurité (aperçu) | Oui | [Sécurité reste](https://msdn.microsoft.com/library/azure/mt704034.aspx)  |  | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code)  |

## <a name="resource-manager"></a>Gestionnaire de ressources

| Fonctionnalité | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Autorisation | Oui | [Verrous de gestion](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Contrôle d’accès basé sur les rôles](https://msdn.microsoft.com/library/azure/dn906885.aspx)  | [Verrou de ressource](resource-manager-template-lock.md)<br />[Attributions de rôles](resource-manager-template-role.md)  | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Ressources | Oui | [Ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Liens vers les ressources](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## <a name="resource-providers-and-types"></a>Les fournisseurs de ressources et de types

Lorsque vous déployez des ressources, vous devez fréquemment récupérer des informations sur les fournisseurs de ressources et les types. Vous pouvez récupérer ces informations via l’API REST, Azure PowerShell ou Azure CLI.

Pour travailler avec un fournisseur de ressources, ce fournisseur de ressources doit être enregistré avec votre compte. Par défaut, de nombreux fournisseurs de ressources sont automatiquement enregistrés ; Toutefois, vous devrez enregistrer manuellement des fournisseurs de ressources. Les exemples ci-dessous illustrent comment obtenir l’état de l’inscription d’un fournisseur de ressources et enregistrer le fournisseur de ressources, si nécessaire.

### <a name="portal"></a>Portail

Vous pouvez voir facilement une liste de fournisseurs de ressources pris en charge avec les étapes suivantes :

1. Sélectionnez **Mes autorisations**.

    ![mes autorisations](./media/resource-manager-supported-services/my-permissions.png)

2. Sélectionnez le **statut de fournisseur de ressources**

    ![statut de fournisseur de ressources](./media/resource-manager-supported-services/resource-provider-status.png)

3. Vous voyez la liste complète des fournisseurs de ressources pour votre abonnement.

    ![liste de fournisseurs de ressources](./media/resource-manager-supported-services/list-providers.png)

### <a name="rest-api"></a>API REST

Pour obtenir toutes les ressources disponibles fournisseurs, y compris leurs types, emplacements, versions des API et état de l’enregistrement, utilisent la [liste tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Si vous devez enregistrer un fournisseur de ressources, voir [Enregistrer un abonnement auprès d’un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790548.aspx).

### <a name="powershell"></a>PowerShell

L’exemple suivant montre comment obtenir tous les fournisseurs de ressources disponibles.

    Get-AzureRmResourceProvider -ListAvailable
    

L’exemple suivant montre comment obtenir les types de ressources pour un fournisseur de ressources particulier.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
La sortie est similaire à :

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...
    
Pour enregistrer un fournisseur de ressources, fournir l’espace de noms :

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>CLI Azure

L’exemple suivant montre comment obtenir tous les fournisseurs de ressources disponibles.

    azure provider list
    
La sortie est similaire à :

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Vous pouvez enregistrer les informations relatives à un fournisseur de ressources particulier dans un fichier avec la commande suivante.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Pour enregistrer un fournisseur de ressources, fournir l’espace de noms :

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Régions pris en charge

Lorsque vous déployez des ressources, vous devez généralement spécifier une région pour les ressources. Le Gestionnaire de ressources est pris en charge dans toutes les régions, mais les ressources que vous déployez ne peuvent pas être pris en charge dans toutes les régions. En outre, il peut être sur votre abonnement, les limitations qui vous empêchent d’utiliser certaines régions qui prennent en charge de la ressource. Ces limitations peuvent être liées aux problèmes de votre pays d’origine de la taxe, ou le résultat d’une stratégie est placé par votre administrateur d’abonnement à utiliser uniquement dans certaines régions. 

Pour une liste complète de toutes les régions pris en charge pour tous les services Azure, consultez [Services par région](https://azure.microsoft.com/regions/#services); Toutefois, cette liste peut inclure des zones de votre abonnement ne prenant pas en charge. Vous pouvez déterminer les zones pour un type de ressource particulier qui prend en charge de votre abonnement via le portail, API REST, PowerShell ou Azure CLI.

### <a name="portal"></a>Portail

Vous pouvez voir les régions pris en charge pour un type de ressource dans les étapes suivantes :

1. Sélectionnez **services plus** > **Explorateur de ressources**.

    ![Explorateur de ressources](./media/resource-manager-supported-services/select-resource-explorer.png)

2. Ouvrez le nœud **fournisseurs** .

    ![Sélectionner les fournisseurs](./media/resource-manager-supported-services/select-providers.png)

3. Sélectionnez un fournisseur de ressources et afficher les versions de l’API et les régions pris en charge.

    ![fournisseur d’affichage](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>API REST

Pour découvrir les régions sont disponibles pour un type particulier de votre abonnement, utilisez la [liste de tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

L’exemple suivant montre comment obtenir les régions pris en charge pour les sites web.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
La sortie est similaire à :

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>CLI Azure

L’exemple suivant retourne tous les emplacements pris en charge pour chaque type de ressource.

    azure location list

Vous pouvez également filtrer les résultats de l’emplacement avec un utilitaire JSON comme [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Qui retourne :

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Versions des API prises en charge

Lorsque vous déployez un modèle, vous devez spécifier une version de l’API à utiliser pour la création de chaque ressource. La version de l’API correspond à une version d’opérations API REST publiés par le fournisseur de ressources. Comme un fournisseur de ressources permet de nouvelles fonctionnalités, il publie une nouvelle version de l’API REST. Par conséquent, la version de l’API que vous spécifiez dans votre modèle affecte les propriétés que vous pouvez spécifier dans le modèle. En général, vous souhaitez sélectionner la dernière version de l’API lors de la création de modèles. Pour les modèles existants, vous pouvez décider si vous souhaitez continuer à utiliser une version antérieure de l’API ou mettre à jour votre modèle pour la dernière version de tirer parti des nouvelles fonctionnalités.

### <a name="portal"></a>Portail

Vous déterminez les versions d’API prises en charge de la même façon que vous avez déterminé la prise en charge des régions (illustrées précédemment).

### <a name="rest-api"></a>API REST

Pour découvrir les API des versions sont disponibles pour les types de ressource, utilisez l’opération de la [liste de tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

L’exemple suivant montre comment obtenir les versions API disponibles pour un type particulier.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
La sortie est similaire à :
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>CLI Azure

Vous pouvez enregistrer les informations (y compris les versions API disponibles) pour un fournisseur de ressources dans un fichier avec la commande suivante.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Vous pouvez ouvrir le fichier et recherchez l’élément **apiVersions**

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de modèles du Gestionnaire de ressources, consultez [modèles de création d’un gestionnaire de ressources Azure](resource-group-authoring-templates.md).
- Pour en savoir plus sur le déploiement des ressources, voir [déploiement d’une application avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy.md).
