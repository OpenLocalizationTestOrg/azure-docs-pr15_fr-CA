<properties
    pageTitle="Service d’application API Apps - Nouveautés | Microsoft Azure"
    description="Découvrez les nouvelles fonctionnalités pour les applications d’API dans le Service d’application Azure."
    services="app-service\api"
    documentationCenter=".net"
    authors="mohitsriv"
    manager="wpickett"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps---whats-changed"></a>Service d’application API Apps - Nouveautés

L’événement Connect() dans novembre 2015, un certain nombre d’améliorations apportées au Service d’application Azure ont [annoncé](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/). Ces améliorations incluent des modifications sous-jacentes aux applications API pour mieux aligner avec les applications mobiles et Web, réduire le nombre de concept et améliorer le déploiement et les performances d’exécution. Démarrage de nouvelles applications API du 30 novembre 2015, vous créez en utilisant le portail de gestion Azure ou les derniers outils reflète ces modifications. Cet article décrit ces modifications, ainsi que comment redéployer des applications existantes pour tirer parti des fonctionnalités.

## <a name="feature-changes"></a>Modifications de fonctionnalité
Les fonctionnalités clés de l’API des applications – authentification, CORS et les API de métadonnées – ont déplacé directement dans l’application de Service. Avec cette modification, les fonctionnalités sont disponibles sur le Web, Mobile et applications d’API. En fait, les trois partagent le même type de ressource de **Microsoft.Web/sites** dans le Gestionnaire de ressources. La passerelle API applications n’est plus nécessaire ni proposée avec les applications d’API. Cela facilite également pour une gestion d’API Azure dans la mesure où il y aura simplement la passerelle API gestion unique.

![Vue d’ensemble des applications API](./media/app-service-api-whats-changed/api-apps-overview.png)

Il est un principe de conception important avec la mise à jour des applications d’API pour vous permettre de mettre votre API étant, dans la langue de votre choix.  Si votre API est déjà déployée comme une application Web ou d’une application Mobile, vous n’avez pas de redéployer votre application pour tirer parti des nouvelles fonctionnalités. Si vous êtes actuellement sur Aperçu des applications d’API, Guide de migration est détaillée ci-dessous.

### <a name="authentication"></a>Authentification
Les fonctionnalités de l’authentification API Apps, Mobile Services/applications et applications Web clé en main existantes ont été unifiées et sont disponibles dans une lame de l’authentification unique Service d’application Azure dans le portail de gestion. Pour une introduction aux services d’authentification dans le Service d’application, consultez [l’authentification en développant le Service application / autorisation](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

Pour les scénarios de l’API, il existe un certain nombre de nouvelles capacités de pertinentes :

- **Prise en charge de l’utilisation d’Azure Active Directory directement**, sans avoir à échanger le jeton DAS pour un jeton de session le code client : vos clients peuvent simplement inclure les jetons DAS dans l’en-tête d’autorisation, conformément à la spécification de jeton de support. Cela signifie également qu’aucun SDK Service application particulière n’est requise sur le côté client ou serveur. 
- **Service-service ou l’accès « Interne »**: Si vous avez un processus démon ou un autre client nécessitant l’accès aux API sans une interface, vous pouvez demander un jeton à l’aide d’une entité de sécurité du service DAS et passer à l’application de Service pour l’authentification avec votre application.
- **Autorisation de différé**: de nombreuses applications ont des restrictions d’accès différentes pour différentes parties de l’application. Vous pouvez choisir certaines API soit publiquement disponible, alors que d’autres requièrent de se connecter. La fonction d’authentification/autorisation d’origine a été tout ou rien, avec l’ensemble du site nécessitant une connexion. Cette option existe toujours, mais vous pouvez également permettent à votre code d’application rendre des décisions d’accès après que l’application de Service a authentifié l’utilisateur.
 
Pour plus d’informations sur les nouvelles fonctionnalités d’authentification, consultez [authentification et autorisation pour les applications d’API dans le Service d’application Azure](app-service-api-authentication.md). Pour plus d’informations sur la migration des applications API existantes à partir du modèle d’applications API précédent vers le nouveau, reportez-vous à la section [applications API existantes de migration](#migrating-existing-api-apps) plus loin dans cet article.
 
### <a name="cors"></a>CORS
Au lieu d’une valeur délimitée par des virgules de app **MS_CrossDomainOrigins** , il existe désormais une lame dans le portail de gestion Azure de configuration CORS. Sinon, il peut être configuré à l’aide du Gestionnaire de ressources des outils tels que PowerShell d’Azure, CLI ou [Explorateur de ressources](https://resources.azure.com/). Affectez à la propriété **cors** sur le type de ressource de **Microsoft.Web/sites/config** pour votre ** &lt;nom du site&gt;/web** ressource. Par exemple :

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### <a name="api-metadata"></a>Métadonnées de l’API
La lame de définition d’API est désormais disponible sur le Web, Mobile et applications d’API. Dans le portail de gestion, vous pouvez spécifier une url relative ou une url absolue qui pointe vers un point de terminaison de cette représentation hôtes Swagger 2.0 de votre API. Sinon, il peut être configuré à l’aide des outils du Gestionnaire de ressources. Affectez à la propriété **apiDefinition** sur le type de ressource de **Microsoft.Web/sites/config** pour votre ** &lt;nom du site&gt;/web** ressource. Par exemple :

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

À ce stade, le point de terminaison de métadonnées doit être publiquement accessible sans authentification pour de nombreux clients en aval (génération de client, par exemple Visual Studio API REST et le flux de PowerApps « Ajouter API ») pour l’utiliser. Cela signifie que si vous utilisez l’authentification de Service de l’application et à exposer la définition de API à partir de votre application lui-même, vous devez utiliser l’option de différé de l’authentification décrite précédemment afin que l’itinéraire vers vos métadonnées Swagger soit public.

## <a name="management-portal"></a>Portail de gestion
Sélection **Nouveau > Web + Mobile > API App** dans le portail va créer des applications API qui reflètent les nouvelles fonctionnalités décrites dans l’article. **Parcourir > applications d’API** n’affichera que ces nouvelles applications d’API. Une fois que vous accédez à une application d’API, la lame partage la même disposition et fonctionnalités que ceux du Web et d’applications Mobile. Les seules différences sont le contenu des Démarrages rapides et ordre des paramètres.

Applications d’API existantes (ou applications Marketplace API créées à partir d’applications de logique) avec l’aperçu précédente fonctionnalités sera toujours visibles dans le Concepteur d’applications de logique et lors de l’exploration de toutes les ressources dans un groupe de ressources.

## <a name="visual-studio"></a>Visual Studio

La plupart des applications Web des outils fonctionnent avec les nouvelles applications d’API, car ils partagent le même type de ressource **Microsoft.Web/sites** sous-jacent. Le Visual Studio Azure outillage, cependant, doivent être mis à niveau vers la version 2.8.1 ou version ultérieure dans la mesure où il expose un certain nombre de fonctionnalités spécifiques aux API. Télécharger le SDK à partir de la [page de téléchargements Azure](https://azure.microsoft.com/downloads/).

Grâce à la rationalisation des types de services de l’application, publier est également unifiée sous **Publier > Microsoft Azure Application Service**:

![Publier des applications d’API](./media/app-service-api-whats-changed/api-apps-publish.png)

Pour en savoir plus sur le Kit de développement logiciel 2.8.1, lisez l’annonce [le billet de blog](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

Vous pouvez également importer manuellement le profil de publication à partir du portail de gestion pour activer la publication. Toutefois, Cloud Explorer, de génération de code et de sélection d’application API/création nécessitera SDK 2.8.1 ou supérieur.

## <a name="migrating-existing-api-apps"></a>Migration des applications API existantes
Si votre API personnalisé est déployé à la version précédente d’Aperçu des applications d’API, nous avons besoin de migrer vers le nouveau modèle pour les applications d’API par le 31 décembre 2015. Dans la mesure où à la fois le modèle ancien et nouveau est basé sur les API Web hébergé dans une application de Service, la majorité du code existant peut être réutilisée.

### <a name="hosting-and-redeployment"></a>Hébergement et redéploiement
Les étapes de redéploiement sont les mêmes que le déploiement de toute API Web existant au Service de l’application. Étapes suivantes :

1. Créer une application API vide. Pour ce faire dans le portail avec le nouveau > application API, dans Visual Studio à partir de la publication, ou d’outils du Gestionnaire de ressources. Si vous utilisez des modèles ou des outils du Gestionnaire de ressources, vous pouvez définir la valeur de **type** pour les **api** sur le type de ressource **Microsoft.Web/sites** pour que les Démarrages rapides et les paramètres du portail de gestion orienté vers les scénarios de l’API.
2. Se connecter et déployer votre projet dans l’application API vide à l’aide d’un des mécanismes de déploiement pris en charge par le Service de l’application. Lire la [documentation de déploiement de Service d’application Azure](../app-service-web/web-sites-deploy.md) pour en savoir plus. 
  
### <a name="authentication"></a>Authentification
Les services d’authentification de Service d’application prend en charge les mêmes fonctions qui étaient disponibles avec le modèle API applications précédent. Si vous utilisez des jetons de session et exiger des kits de développement logiciel, utilisez les kits de développement client et serveur suivantes :

- Client : [Client Mobile Azure SDK](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Extension de Microsoft Azure Mobile application .NET d’authentification](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) du serveur : 

Si vous utilisez à la place la valeur alpha de Service d’application SDK, ceux-ci sont maintenant désapprouvées :

- Client : [Kit de développement logiciel de Microsoft Azure AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService)
- Serveur : [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

En particulier avec Azure Active Directory, toutefois, aucune application Service spécifique est requis si vous utilisez directement le jeton DAS.

### <a name="internal-access"></a>Accès interne
Le modèle précédent API applications inclus un niveau d’accès interne intégré. Cette nécessaire l’utilisation du Kit de développement pour les demandes de signature. Comme indiqué précédemment, le nouveau modèle d’applications d’API, les entités de service DAS peuvent servir comme une solution de remplacement pour l’authentification du service-service sans nécessiter un application spécifique au Service SDK. Pour en savoir plus en [Service authentification principale pour les applications d’API dans le Service d’application Azure](app-service-api-dotnet-service-principal-auth.md).

### <a name="discovery"></a>Découverte
Le précédent modèle API applications avait une API pour découvrir d’autres applications d’API lors de l’exécution dans le même groupe de ressources derrière la même passerelle. Ceci est particulièrement utile dans les architectures qui implémentent des modèles microservice. Ce n’est pas directement pris en charge, un certain nombre d’options est disponible :

1. Utiliser l’API Gestionnaire de ressources Azure pour la découverte.
2. Placer la gestion des API Azure devant votre API hébergées par le Service de l’application. Gestion des API Azure sert à une façade et peut fournir une url en vis-à-vis externe stable même si les modifications de topologie interne.
3. Créez votre propre application de API de découverte et ont les autres applications d’API à inscrire avec l’application de découverte au démarrage.
4. Au moment du déploiement, remplir les paramètres d’application de toutes les applications d’API (et clients) avec les points de terminaison des autres applications API. Ceci est envisageable dans les déploiements de modèle et dans la mesure où les applications d’API vous offrent désormais le contrôle de l’url.

## <a name="using-api-apps-with-logic-apps"></a>À l’aide des applications d’API avec les applications de logique

Le nouveau modèle d’applications API fonctionne bien avec les [Applications de logique de version de schéma 2015-08-01](../app-service-logic/app-service-logic-schema-2015-08-01.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, lisez les articles de la [section de la documentation sur l’API Apps](https://azure.microsoft.com/documentation/services/app-service/api/). Ils ont été mis à jour pour refléter le nouveau modèle pour les applications d’API. En outre, atteindre sur les forums pour des détails supplémentaires ou des conseils sur la migration :

- [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps)
- [Débordement de pile](http://stackoverflow.com/questions/tagged/azure-api-apps)
