
<properties 
    pageTitle="Personnaliser des définitions de Swashbuckle-généré l’API" 
    description="Apprenez à personnaliser des définitions de Swagger des API qui sont générées par Swashbuckle pour une application API dans le Service d’application Azure." 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="bradygaster" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/29/2016" 
    ms.author="rachelap"/>

# <a name="customize-swashbuckle-generated-api-definitions"></a>Personnaliser des définitions de Swashbuckle-généré l’API 

## <a name="overview"></a>Vue d’ensemble

Cet article explique comment personnaliser des Swashbuckle pour gérer des scénarios courants où vous souhaitez modifier le comportement par défaut :

* Swashbuckle génère des identificateurs d’opération en double pour les surcharges des méthodes de contrôleur
* Swashbuckle suppose que la réponse valide uniquement à partir d’une méthode HTTP 200 (OK) 
 
## <a name="customize-operation-identifier-generation"></a>Personnaliser la génération d’identificateur d’opération

Swashbuckle génère des identificateurs d’opération Swagger en concaténant le nom du contrôleur et le nom de la méthode. Ce modèle crée un problème lorsque vous avez plusieurs surcharges de la méthode : Swashbuckle génère l’ID d’opération en double, qui est JSON de Swagger non valide.

Par exemple, le code suivant de contrôleur, Swashbuckle générer des ID d’opération Contact_Get trois.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Vous pouvez résoudre le problème manuellement en donnant les méthodes des noms uniques, telle que la suivante pour cet exemple :

* Télécharger
* GetById
* GetPage

L’alternative est d’étendre Swashbuckle pour qu’il génère automatiquement des ID d’opération unique.

Les étapes suivantes indiquent comment personnaliser Swashbuckle en utilisant le fichier *SwaggerConfig.cs* qui est inclus dans le projet par le modèle de projet Visual Studio aperçu d’applications API.  Vous pouvez également personnaliser Swashbuckle dans un projet Web API que vous configurez pour le déploiement sous la forme d’une application API.

1. Créer un fichier personnalisé `IOperationFilter` mise en œuvre 

    Le `IOperationFilter` interface fournit un point d’extensibilité pour Swashbuckle les utilisateurs qui souhaitent personnaliser différents aspects du processus de métadonnées Swagger. Le code suivant illustre une méthode de modification du comportement de la génération d’id d’opération. Le code ajoute les noms de paramètre pour le nom de code d’opération.  

        using Swashbuckle.Swagger;
        using System.Web.Http.Description;
        
        namespace ContactsList
        {
            public class MultipleOperationsWithSameVerbFilter : IOperationFilter
            {
                public void Apply(
                    Operation operation,
                    SchemaRegistry schemaRegistry,
                    ApiDescription apiDescription)
                {
                    if (operation.parameters != null)
                    {
                        operation.operationId += "By";
                        foreach (var parm in operation.parameters)
                        {
                            operation.operationId += string.Format("{0}",parm.name);
                        }
                    }
                }
            }
        }

2. Dans le fichier de *App_Start\SwaggerConfig.cs* , appelez le `OperationFilter` Swashbuckle à utiliser la nouvelle méthode `IOperationFilter` mise en œuvre.

        c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

    ![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

    Le fichier *SwaggerConfig.cs* qui est déplacé le package Swashbuckle NuGet contenait de nombreux exemples commentées de points d’extensibilité. Les commentaires supplémentaires ne sont pas indiqués ici. 

    Après avoir apporté cette modification, votre `IOperationFilter` mise en œuvre est utilisé et provoque l’ID d’opération unique doit être généré.
 
    ![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>
    
## <a name="allow-response-codes-other-than-200"></a>Permettre que les codes de réponse autre que 200

Par défaut, Swashbuckle suppose qu’une réponse HTTP 200 (OK) est la réponse légitime *uniquement* à partir d’une méthode de l’API Web. Dans certains cas, vous souhaiterez peut-être retourner des codes de réponse sans provoquer le client déclenche une exception.  Par exemple, le code API Web suivant illustre un scénario dans lequel vous pouvez le client à accepter un 200 ou une erreur 404 comme des réponses valides.

    [ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

Dans ce scénario, le Swagger Swashbuckle génère par défaut ne spécifie qu’un seul légitime code d’état HTTP, HTTP 200.

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Étant donné que Visual Studio utilise la définition de l’API de Swagger pour générer le code pour le client, il crée un code client qui déclenche une exception pour toute réponse autre qu’un HTTP 200. Le code ci-dessous est à partir d’un client C# généré pour cet exemple de méthode Web API.

    if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

Swashbuckle fournit deux méthodes de personnalisation de la liste des codes de réponse HTTP attendus qu’il génère, à l’aide de commentaires XML ou de le `SwaggerResponse` attribut. L’attribut est plus facile, mais il est uniquement disponible dans Swashbuckle 5.1.5 ou ultérieure. Les applications d’API aperçu nouveau projet de Visual Studio 2013 comprend Swashbuckle version 5.0.0, donc si vous utilisé le modèle et que vous ne souhaitez pas mettre à jour Swashbuckle, votre seule option consiste à utiliser des commentaires XML. 

### <a name="customize-expected-response-codes-using-xml-comments"></a>Personnaliser les codes de réponse attendue à l’aide de commentaires XML

Utilisez cette méthode pour spécifier les codes de réponse si votre version de Swashbuckle est antérieure à 5.1.5.

1. Tout d’abord, ajoutez des commentaires de documentation XML sur les méthodes que vous souhaitez spécifier les codes de réponse HTTP pour. Prélèvement de l’échantillon API Web action ci-dessus et lui appliquer la documentation XML aurait pour résultat dans le code, comme dans l’exemple suivant. 

        /// <summary>
        /// Returns the specified contact.
        /// </summary>
        /// <param name="id">The ID of the contact.</param>
        /// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
        /// <response code="200">OK</response>
        /// <response code="404">Not Found</response>
        [ResponseType(typeof(Contact))]
        public HttpResponseMessage Get(int id)
        {
            var contacts = GetContacts();
        
            var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
        
            if (requestedContact == null)
            {
                return Request.CreateResponse(HttpStatusCode.NotFound);
            }
            else
            {
                return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
            }
        }

1. Ajouter des instructions dans le fichier *SwaggerConfig.cs* pour diriger Swashbuckle pour utiliser du code XML des fichiers de documentation.

    * Ouvrez *SwaggerConfig.cs* et créer une méthode sur la classe *SwaggerConfig* pour spécifier le chemin d’accès au fichier de documentation XML. 

            private static string GetXmlCommentsPath()
            {
                return string.Format(@"{0}\XmlComments.xml", 
                    System.AppDomain.CurrentDomain.BaseDirectory);
            }

    * Faites défiler la liste vers le bas dans le fichier *SwaggerConfig.cs* jusqu'à ce que vous voyiez la ligne commenté du code ressemblant à la capture d’écran ci-dessous. 

        ![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
    
    * Ne commentez pas la ligne pour permettre les traitement pendant la génération de Swagger les commentaires XML. 
    
        ![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
    
1. Pour générer le fichier de documentation XML, allez dans les propriétés du projet et activer le fichier de documentation XML, comme illustré dans la capture d’écran ci-dessous. 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png) 

Une fois ces étapes effectuées, le JSON Swagger généré par Swashbuckle reflète les codes de réponse HTTP que vous avez spécifié dans les commentaires XML. La capture d’écran ci-dessous illustre cette nouvelle charge JSON. 

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

Lorsque vous utilisez Visual Studio pour générer le code client pour votre API REST, le code C# accepte les codes d’état à la fois HTTP OK et introuvable sans lever d’exception, ce qui permet de prendre des décisions sur la manière de gérer le retour d’un enregistrement de Contact null votre code utilisateur. 

        if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
        {
            HttpOperationException<object> ex = new HttpOperationException<object>();
            ex.Request = httpRequest;
            ex.Response = httpResponse;
            ex.Body = null;
            if (shouldTrace)
            {
                ServiceClientTracing.Error(invocationId, ex);
            }
                throw ex;
        }

Vous trouverez le code pour cette démonstration dans [ce référentiel de GitHub](https://github.com/Azure-Samples/app-service-api-dotnet-swashbuckle-swaggerresponse). Avec l’API Web avec du code avec les commentaires de documentation XML de projet est un projet d’Application Console qui contient un client généré pour cette API. 

### <a name="customize-expected-response-codes-using-the-swaggerresponse-attribute"></a>Personnaliser les codes de réponse attendue à l’aide de l’attribut SwaggerResponse

L’attribut [SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) est disponible dans Swashbuckle 5.1.5 et versions ultérieures. Dans le cas où vous disposez d’une version antérieure dans votre projet, cette section commence par expliquer comment mettre à jour le package Swashbuckle NuGet afin que vous puissiez utiliser cet attribut.

1. Dans l' **Explorateur de solutions**, avec le bouton droit de votre projet Web API et cliquez sur **Gérer les Packages NuGet**. 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)

1. Cliquez sur le bouton *mise à jour* du package NuGet *Swashbuckle* . 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)

1. Ajoutez les attributs de *SwaggerResponse* pour les méthodes d’action API Web pour lequel vous souhaitez spécifier les codes de réponse HTTP valides. 

        [SwaggerResponse(HttpStatusCode.OK)]
        [SwaggerResponse(HttpStatusCode.NotFound)]
        [ResponseType(typeof(Contact))]
        public HttpResponseMessage Get(int id)
        {
            var contacts = GetContacts();

            var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
            if (requestedContact == null)
            {
                return Request.CreateResponse(HttpStatusCode.NotFound);
            }
            else
            {
                return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
            }
        }

2. Ajouter un `using` instruction pour l’espace de noms de l’attribut :

        using Swashbuckle.Swagger.Annotations;
        
1. Accédez à l’URL */swagger/docs/v1* de votre projet et les différents codes de réponse HTTP sera visibles dans le JSON Swagger. 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

Vous trouverez le code pour cette démonstration dans [ce référentiel de GitHub](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes). Avec l’API Web décorée avec l’attribut *SwaggerResponse* de projet est un projet d’Application Console qui contient un client généré pour cette API. 

## <a name="next-steps"></a>Étapes suivantes

Cet article a montré comment personnaliser la manière dont Swashbuckle génère l’ID d’opération et les codes de réponse valide. Pour plus d’informations, consultez [Swashbuckle sur GitHub](https://github.com/domaindrivendev/Swashbuckle).
 
