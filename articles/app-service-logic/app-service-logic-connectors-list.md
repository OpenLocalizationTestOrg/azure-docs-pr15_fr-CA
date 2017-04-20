<properties
    pageTitle="Liste des applications d’API et des connecteurs disponibles | Service d’application Microsoft Azure"
    description="En savoir plus sur les connecteurs et les applications d’API dans le Service d’application Azure"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>


# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Liste des liens et des applications de l’API à utiliser dans vos applications de logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2014-12-01-aperçu. Pour la version logique Apps disponibilité générale, voir la [Nouvelle liste de liens](../connectors/apis-list.md).

Obtenir des informations sur tous les connecteurs disponibles et applications API créée par Microsoft à utiliser dans vos applications de logique.

Pour la tarification des informations et une liste de ce qui est inclus avec chaque niveau de Service, consultez [Tarification d’Azure Application Service](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Pour commencer avec la logique d’applications avant l’ouverture d’un compte Azure, accédez à [Essayer une application de logique](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer immédiatement une application de logique de démarrage de courte durée dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="core-connectors"></a>Connecteurs principaux
Le tableau suivant répertorie tous les connecteurs disponibles et les applications d’API créée par Microsoft qui sont disponibles en tant que connecteurs de base :

Nom | Description
--- | ---
[Bing Translator](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Utilisez Bing pour traduire du texte dans une autre langue.
[HTTP](app-service-logic-connector-http.md) | L’écouteur HTTP ouvre un point de terminaison qui agit comme un serveur HTTP et écoute les demandes HTTP ou HTTPS. L’action HTTP ne nécessite pas une application API et est pris en charge en mode natif dans les applications de la logique.
[Marge](app-service-logic-connector-slack.md) | Connectez-vous à la marge et publier des messages dans les canaux de marge.


## <a name="enterprise-integration-connectors"></a>Connecteurs d’intégration entreprise
Le tableau suivant répertorie tous les connecteurs disponibles et applications API créée par Microsoft disponible en tant que connecteurs d’intégration d’entreprise :

Nom  | Description
------------- | -------------
[Règles de BizTalk](app-service-logic-use-biztalk-rules.md) | Utiliser les règles de BizTalk pour définir et contrôler la logique métier d’une organisation. Les stratégies d’entreprise peuvent être mises à jour sans recompiler ou sans redéployer les applications associées.
[Extracteur de XPath BizTalk](app-service-logic-xpath-extract.md) | Recherche et extrait les données à partir du contenu XML basé sur un XPath que vous choisissez.
[Connecteur de DB2](app-service-logic-connector-db2.md) | Se connecte à un IBM DB2 de la base de données locale et sur une machine virtuelle Azure exécute un système d’exploitation de Windows. Peut mapper les opérations Web API et OData aux commandes du langage SQL de Informix. <br/><br/>Pas de déclencheurs. Les actions incluent la table select, insert, update, delete et instruction personnalisée<br/><br/>Ce connecteur inclut également le Client Microsoft pour DRDA pour se connecter à un serveur Informix, via un réseau TCP/IP.
[Fichier](app-service-logic-connector-file.md) | À l’aide de ce connecteur, vous pouvez vous connecter au système de fichiers local ou réseau et tâches complète de fichier différents, y compris le chargement, la suppression, les fichiers de la liste et plus.
[Informix](app-service-logic-connector-informix.md) | Se connecte à une base de données IBM Informix, sur site et sur une machine virtuelle Azure exécutant un système d’exploitation de Windows. Peut mapper les opérations Web API et OData aux commandes du langage SQL de Informix.<br/><br/>Pas de déclencheurs. Les actions incluent la table select, insert, update, delete et instruction personnalisée.<br/><br/>Lors de l’utilisation sur site, VPN ou Azure ExpressRoute utilisables. Ce connecteur inclut également un Client Microsoft pour DRDA pour se connecter à un serveur Informix, via un réseau TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Se connecte à locale SQL Server ou une base de données SQL Azure. Vous pouvez créer, mettre à jour, obtenir et supprimer les entrées d’une table de base de données SQL.
MQ | Se connecte à IBM WebSphere MQ Server version 8, sur site et sur une machine virtuelle Azure exécutant un système d’exploitation de Windows. Lors de l’utilisation sur site, VPN ou Azure ExpressRoute utilisables. Le connecteur inclut également le Client Microsoft pour MQ.<br/><br/>Pas de déclencheurs. Aucune action.<br/><br/>**Remarque** Actuellement ne peut pas être utilisé avec des applications de logique.

## <a name="connectors-as-triggers"></a>Connecteurs en tant que déclencheurs
Plusieurs connecteurs fournissent des déclencheurs pour les applications de la logique. Ces déclencheurs sont de deux types :

1. Déclencheurs de sondage : Ces déclencheurs interrogent votre service à la fréquence spécifiée pour vérifier les nouvelles données. Lorsque de nouvelles données sont disponibles, une nouvelle instance de votre application logique s’exécute avec les données en entrée. Pour éviter que les mêmes données d’être utilisé à plusieurs reprises, le déclencheur peut nettoyage des données qui a été lues et transmises à l’application de la logique. Fichier, SQL et le stockage Azure sont des exemples de ces connecteurs.
2. Déclencheur d’émission : Ces déclencheurs écoutent pour les données sur un point de terminaison ou un événement se produise. Ensuite, déclenche une nouvelle instance d’une application de logique. Écouteur HTTP et Twitter sont des exemples de ces connecteurs.

## <a name="connectors-as-actions"></a>Connecteurs en tant qu’Actions
Connecteurs peuvent également servir en tant qu’actions dans votre application logique. Les actions sont utiles pour rechercher des données dans l’application logique qui peut ensuite utilisé lors de l’exécution. Par exemple, vous devrez peut-être rechercher des données à partir d’une base de données SQL pour plus d’informations sur un client lors du traitement d’une commande. Ou bien, vous devrez peut-être écrire, de mettre à jour ou de supprimer des données dans une destination. Pour cela, à l’aide des actions fournies par les connecteurs. Actions correspondent à des opérations dans les applications d’API (tel que défini par leurs métadonnées Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Créer vos propres connecteurs et les applications d’API
[Connecteurs et les applications de référence sur l’API](http://aka.ms/appservicesconnectorreference)  
[Déclencheurs d’application API du Service application Azure](../app-service-api/app-service-api-dotnet-triggers.md)  
[Référence d’application logique](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Plus d’informations sur les connecteurs et les applications d’API
[Quels sont les connecteurs et les applications d’API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)  
[À l’aide du Gestionnaire de connexion hybride dans le Service d’application Azure](app-service-logic-hybrid-connection-manager.md)  
[Gérer et surveiller les connecteurs et les applications d’API intégrées](app-service-logic-monitor-your-connectors.md)
