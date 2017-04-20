<properties
    pageTitle="Application des métadonnées de Service API Apps pour la génération de code et de découverte API | Microsoft Azure"
    description="Découvrez comment les applications API dans le Service d’application Azure utiliser Swagger métadonnées pour faciliter la génération de code et de la découverte des API."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps-metadata-for-api-discovery-and-code-generation"></a>Application des métadonnées de Service API Apps pour la génération de code et de la découverte des API 

Prise en charge des métadonnées d’API [2.0 de Swagger](http://swagger.io/) est intégré dans les API de Service App Apps. Vous n’êtes pas obligé d’utiliser Swagger, mais si vous ne l’utilisez pas, vous pouvez tirer parti de fonctionnalités d’applications d’API qui facilitent la découverte et la consommation.   

## <a name="swagger-endpoint"></a>Point de terminaison swagger

Vous pouvez spécifier un point de terminaison qui fournit des métadonnées de JSON de 2.0 Swagger pour une application API dans une propriété de l’application API. Le point de terminaison peut être par rapport à l’URL de base de l’application API ou d’une URL absolue. Les URL absolues peuvent pointer à l’extérieur de l’application API. 

De nombreux clients en aval (par exemple, la génération de code de Visual Studio et flux de « Ajouter l’API » de PowerApps), l’URL doit être accessible publiquement (non protégé par l’utilisateur ou l’authentification du service). Cela signifie que si vous utilisez l’authentification par le Service de l’application et que vous souhaitez exposer la définition de API à partir de votre application lui-même, vous devez utiliser l’option d’authentification qui permet au trafic anonyme accéder à votre API. Pour plus d’informations, reportez-vous à la section [authentification et autorisation pour les applications d’API application de Service](app-service-api-authentication.md).

### <a name="portal-blade"></a>Lame de portail

Dans [Azure portal](https://portal.azure.com/) l’URL du point de terminaison peut être vu et modifié sur la lame de la **Définition de l’API** .

![](./media/app-service-api-metadata/apidefblade.png)

### <a name="azure-resource-manager-property"></a>Propriété de gestionnaire de ressources Azure

Vous pouvez également configurer l’URL de définition d’API pour une application API à l’aide [d’Explorateur de ressources](https://resources.azure.com/) ou des [modèles d’Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md) dans les outils de ligne de commande, tels que [PowerShell d’Azure](../powershell-install-configure.md) et d' [Azure CLI](../xplat-cli-install.md). 

Dans l' **Explorateur de ressources**, accédez à **abonnements > {votre abonnement} > resourceGroups > {votre groupe de ressources} > fournisseurs > Microsoft.Web > sites > {votre site} > Configuration > web**, et vous verrez la `apiDefinition` propriété :

        "apiDefinition": {
          "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
        }

Pour obtenir un exemple d’un modèle de gestionnaire de ressources Azure qui définit les `apiDefinition` propriété, ouvrez le [fichier azuredeploy.json dans l’application d’exemple de liste de tâches](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Recherchez la section du modèle qui ressemble à l’exemple JSON ci-dessus.

### <a name="default-value"></a>Valeur par défaut

Lorsque vous utilisez Visual Studio pour créer une application API, le point de terminaison de définition API est automatiquement définie pour l’URL de base de l’application API plue `/swagger/docs/v1`. Il s’agit de l’URL par défaut utilisée par le package NuGet de [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) pour servir les métadonnées Swagger générée dynamiquement pour un projet ASP.NET Web API. 

## <a name="code-generation"></a>Génération de code

Un des avantages de l’intégration de Swagger dans les applications Azure API est la génération de code automatique. Classes client généré le rendent plus facile d’écrire du code qui appelle une application API.

Vous pouvez générer le code client pour une application API à l’aide de Visual Studio ou à partir de la ligne de commande. Pour plus d’informations sur la génération des classes du client dans Visual Studio pour un projet de l’API de Web ASP.NET, consultez [mise en route avec ASP.NET et les applications d’API](app-service-api-dotnet-get-started.md#codegen). Pour plus d’informations sur la façon de le faire à partir de la ligne de commande pour toutes les langues prises en charge, consultez le fichier readme du référentiel [Azure/autorest](https://github.com/azure/autorest) sur GitHub.com.
 
## <a name="next-steps"></a>Étapes suivantes

Pour un didacticiel qui vous guide dans la création, le déploiement et la consommation d’une application API, reportez-vous à la section [mise en route avec les API des applications de Service d’application Azure](app-service-api-dotnet-get-started.md).

Si vous utilisez Gestion des API Azure avec des applications d’API, vous pouvez utiliser les métadonnées Swagger pour importer votre API dans les API de gestion. Pour plus d’informations, consultez [comment importer la définition d’une API avec Azure API de gestion des opérations](../api-management/api-management-howto-import-api.md). 
