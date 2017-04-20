<properties
   pageTitle="Résoudre les problèmes des Services en nuage à l’aide des informations d’Application | Microsoft Azure"
   description="Découvrez comment résoudre les problèmes de service cloud à l’aide des perspectives d’Application pour traiter des données de diagnostic d’Azure."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# <a name="troubleshoot-cloud-services-using-application-insights"></a>Résoudre les problèmes des Services en nuage à l’aide des informations d’Application

Avec l’extension de diagnostics [Azure SDK 2.8](https://azure.microsoft.com/downloads/) et Azure 1.5, vous pouvez maintenant envoyer vos données de tests de diagnostic Azure pour votre Service Cloud directement sur les perspectives de l’Application. Ouvre une session ETW les différents types de journaux collecté par les Diagnostics d’Azure, y compris les journaux d’application, les journaux d’événements windows, et les compteurs de performance peuvent maintenant être envoyés aux analyses de l’Application et affichées dans le portail d’idées d’Application interface utilisateur. Lorsqu’il est utilisé avec le SDK de perspectives d’Application, vous pouvez désormais obtenir appréhender métriques et journaux provenant de votre application, ainsi que les données de niveau système et de l’infrastructure en provenance de Diagnostics d’Azure.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurer les Diagnostics Azure pour envoyer des données aux analyses de l’Application

Suivez ces étapes pour configurer votre projet de service cloud pour envoyer des données de Diagnostics d’Azure aux analyses de l’Application.

1) Dans l’Explorateur de solutions Visual Studio avec le bouton droit sur un rôle et sélectionnez **Propriétés** pour ouvrir le Concepteur de rôles

![Propriétés de rôle de l’Explorateur de solutions][1]

2) Dans le Concepteur de rôles sous la section diagnostics, activez la case à cocher pour **Envoyer des données de diagnostic pour les perspectives de l’Application**

![Concepteur de rôles envoyer des données de diagnostic aux analyses de l’application][2]

3) Dans la boîte de dialogue qui s’affiche, sélectionnez la ressource de perspectives d’Application que vous souhaitez envoyer les données de diagnostics de Windows Azure à. La boîte de dialogue vous permet de sélectionner une ressource d’idées d’Application existante de votre abonnement ou de spécifier manuellement une clé de l’instrumentation d’une ressource d’idées d’Application. Si vous n’avez pas une ressource de perspectives d’Application existante vous pouvez créer sur en cliquant sur le lien **créer une nouvelle ressource** qui ouvre une fenêtre de navigateur pour le portail classique Azure où vous pouvez créer une ressource de perspectives d’Application. Pour plus d’informations sur la création d’une ressource d’informations d’Application, voir [Création d’une nouvelle ressource d’idées d’Application](../application-insights/app-insights-create-new-resource.md)

![Sélectionnez la ressource de perspectives d’application][3]

4) Une fois que vous avez ajouté la ressource de l’Application aperçu, la clé de l’instrumentation de cette ressource est stockée sous la forme d’un paramètre de configuration de service avec le nom **APPINSIGHTS_INSTRUMENTATIONKEY**. Vous pouvez modifier ce paramètre de configuration pour chaque configuration de service ou d’un environnement en sélectionnant une autre configuration dans la liste déroulante configuration de Service vers le bas et en spécifiant une nouvelle clé d’instrumentation pour cette configuration.

![Sélectionnez la configuration de service][4]

Le paramètre de configuration **APPINSIGHTS_INSTRUMENTATIONKEY** est utilisé par Visual Studio pour configurer l’extension de diagnostics avec les informations de ressource de perspectives d’Application appropriées lors de la publication. Le paramètre de configuration est un moyen pratique de définir des clés différentes d’instrumentation pour les configurations de services différentes. Visual Studio va traduire ce paramètre et l’insérer dans la configuration d’extension de diagnostics public lors de la publication. Pour simplifier le processus de configuration de l’extension de diagnostics avec PowerShell, le package également la sortie à partir de Visual Studio contient le fichier XML de configuration public avec l’instrumentation d’idées d’Application appropriée clé inclus. Les fichiers de configuration public sont créés dans le dossier Extensions et suivent le modèle PaaSDiagnostics. <RoleName>. PubConfig.xml. Les déploiements de PowerShell basé peuvent utiliser ce modèle pour mapper chaque configuration à un rôle.

5) Ce qui permet d' **Envoyer des données de diagnostic pour les perspectives de l’Application** va automatiquement configurer les diagnostics de Windows Azure pour envoyer tous les compteurs de performance et les journaux de niveau d’erreur qui sont collectées par l’agent de diagnostics de Windows Azure pour les perspectives de l’Application. Si vous souhaitez configurer les données envoyées aux analyses de l’Application, vous devez modifier manuellement le fichier *diagnostics.wadcfgx* pour chaque rôle. Reportez-vous à la section [Configurer les Diagnostics Azure pour envoyer des données aux analyses de l’Application](../azure-diagnostics-configure-applicationinsights.md) pour plus d’informations sur la mise à jour manuelle de la configuration.

Une fois que le Service en nuage est configuré pour envoyer des données de diagnostics de Windows Azure pour les perspectives d’application que vous pouvez la déployer vers Azure comme vous le feriez normalement assurant l’extension de diagnostics de Windows Azure est activé. Voir la [publication d’un Service Cloud à l’aide de Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Affichage des données de diagnostics de Windows Azure dans les perspectives de l’Application
La télémétrie diagnostic Azure s’affichera dans la ressource de perspectives de l’Application configurée pour votre service cloud.

Voici comment les différents diagnostics de Windows Azure enregistrer les mappage de types aux concepts d’idées d’Application :  

-  Les compteurs de performance sont affichés sous forme de mesures personnalisées dans les perspectives de l’Application
-  Journaux d’événements Windows sont affichés sous forme de Traces et des événements personnalisés dans les perspectives de l’Application
-  Journaux d’application, les journaux ETW et des journaux de l’Infrastructure de diagnostic sont affichés sous forme de Traces dans les perspectives de l’Application.

Pour afficher des données de diagnostics de Windows Azure dans les perspectives de l’Application :

- [Explorateur de mesures](../application-insights/app-insights-metrics-explorer.md) permet de visualiser les compteurs de performance personnalisés ou un nombre de différents types d’événements du journal des événements windows.

![Mesures personnalisées dans l’Explorateur de mesures][5]

- Utilisez [recherche](../application-insights/app-insights-diagnostic-search.md) pour rechercher dans les différents journaux de trace envoyées par les tests de diagnostic Azure. Pour exemple, si vous avez une exception non gérée dans un rôle qui a causé le rôle à se bloquer et de recycler des informations s’inscrive dans le canal de *l’Application* du *journal des événements Windows*. Vous pouvez utiliser la fonctionnalité de recherche de rechercher l’erreur du journal des événements Windows et d’obtenir la trace de la pile de l’exception, ce qui vous permet de déterminer la cause du problème.

![Traces de recherche][6]

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter le SDK de perspectives d’Application à votre service cloud](../application-insights/app-insights-cloudservices.md) pour envoyer des données sur les demandes, les exceptions, les dépendances et tout télémétrie personnalisé à partir de votre application. Les tests de diagnostic Azure associé de données que vous pouvez obtenir une vue complète de votre système et d’application tous dans la même ressource d’Application Insight.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
