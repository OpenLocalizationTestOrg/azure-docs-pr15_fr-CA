<properties
    pageTitle="Application de sur-locaux/cloud hybride (.NET) | Microsoft Azure"
    description="Apprenez à créer une application de sur-locaux/cloud hybride de .NET avec le relais du Bus des services Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="net-on-premisescloud-hybrid-application-using-azure-service-bus-relay"></a>Application de sur-locaux/cloud hybride de .NET à l’aide de relais de Bus de Service Azure

## <a name="introduction"></a>Introduction

Cet article décrit comment générer une application en nuage hybride avec Microsoft Azure et Visual Studio. Ce didacticiel suppose que vous n’avez aucune expérience préalable à l’aide d’Azure. En moins de 30 minutes, vous avez une application qui utilise plusieurs ressources Azure et s’exécutant dans le nuage.

Vous allez apprendre :

-   Comment faire pour créer ou adapter un service web existant pour la consommation par une solution web.
-   Comment faire pour utiliser le service de relais de Bus de Service Azure pour partager des données entre une application Azure et un service web hébergé ailleurs.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-the-service-bus-relay-helps-with-hybrid-solutions"></a>Comment le relais de Bus des services aide avec les solutions hybride

Solutions d’entreprise sont composent généralement d’une combinaison de code personnalisé écrit pour s’attaquer à des besoins nouveaux et uniques et l’utilisation de solutions et de systèmes qui sont déjà en place.

Les architectes de solution commencent à utiliser le nuage pour la gestion plus facile des exigences d’échelle et réduction des coûts opérationnels. Ce faisant, ils trouver qu’actifs existants de service que souhaite exploiter comme blocs de construction pour leurs solutions sont à l’intérieur du pare-feu d’entreprise et de facilement atteint pour l’accès par la solution de cloud. De nombreux services internes ne sont pas intégrés ou hébergés de façon qu’elles peuvent être facilement exposées à la périphérie du réseau d’entreprise.

Le relais de Bus de Service est conçu pour le cas d’utilisation des services web de Windows Communication Foundation (WCF) existants et de rendre les services accessibles en toute sécurité pour les solutions qui résident à l’extérieur du périmètre de l’entreprise sans nécessiter de changements intrusifs dans l’infrastructure réseau de l’entreprise. Ces services de relais de Bus de Service sont toujours hébergés dans leur environnement existant, mais ils délégué écoute entrant sessions et demandes au Bus de Service hébergé sur le nuage. Bus de service protège également ces services à partir de tout accès non autorisé à l’aide de l’authentification de la [Signature de l’accès partagé](../service-bus-messaging/service-bus-sas-overview.md) (SAS).

## <a name="solution-scenario"></a>Scénario de solution

Dans ce didacticiel, vous allez créer un site Web ASP.NET qui vous permet d’afficher une liste de produits sur la page d’inventaire du produit.

![][0]

Ce didacticiel suppose que vous avez des informations sur les produits dans un système sur site existant et utilise le relais de Bus de Service pour accéder à ce système. Cela est simulée par un service web qui s’exécute dans une application console simple et est sauvegardé par un jeu de mémoire dans des produits. Vous ne pourrez pas exécuter cette application console sur votre ordinateur et de déployer le rôle web dans Azure. En procédant ainsi, vous verrez comment le rôle web s’exécutant dans le centre de données Azure appellera en effet à votre ordinateur, même si votre ordinateur se trouve presque certainement au moins un pare-feu et d’une couche de traduction d’adresse réseau.

Voici une capture d’écran de la page de démarrage de l’application web terminée.

![][1]

## <a name="set-up-the-development-environment"></a>Configurer l’environnement de développement

Avant de commencer à développer des applications Azure, obtenez les outils et de votre environnement de développement.

1.  Installer le Kit de développement Azure pour .NET à partir de la page [obtenir des outils et Kit de développement logiciel][] .

2.  Cliquez sur **installer le Kit de développement** de la version de Visual Studio que vous utilisez. Les étapes de ce didacticiel utilisent Visual Studio 2015.

4.  Lorsque vous êtes invité à exécuter ou enregistrer le programme d’installation, cliquez sur **exécuter**.

5.  Dans **Programme d’installation de la plate-forme Web**, cliquez sur **installer** et poursuivre l’installation.

6.  Une fois l’installation terminée, vous disposez de tous les éléments nécessaires pour commencer à développer l’application. Le SDK inclut des outils qui vous permettent de développer facilement des applications Azure dans Visual Studio. Si vous n’avez pas installé de Visual Studio, le Kit de développement installe également la version gratuite Express de Visual Studio.

## <a name="create-a-namespace"></a>Créer un espace de noms

Pour commencer à utiliser les fonctionnalités de Service Bus dans Azure, vous devez d’abord créer un espace de noms du service. Un espace de noms fournit un conteneur de portée pour l’adressage des ressources de Bus de Service au sein de votre application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Création d’un serveur local

Tout d’abord, vous allez créer un système de catalogue de produits (fictifs) sur site. Il est assez simple ; Vous pouvez voir cela comme un système de catalogue produit réel sur site avec une surface de service complet que nous essayons d’intégrer.

Ce projet est une application de console Visual Studio et utilise le [package NuGet de Bus de Service Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) pour inclure les bibliothèques de Bus de Service et les paramètres de configuration.

### <a name="create-the-project"></a>Créer le projet

1.  À l’aide des privilèges d’administrateur, démarrez Visual Studio de Microsoft. Pour démarrer Visual Studio avec des privilèges d’administrateur, cliquez sur l’icône de programme **Visual Studio** , puis cliquez sur **Exécuter en tant qu’administrateur**.

2.  Dans Visual Studio, dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.

3.  À partir de **Modèles installés**, cliquez sur **Application Console**dans **Visual C#**. Dans la zone **nom** , tapez le nom de **ProductsServer**:

    ![][11]

4.  Cliquez sur **OK** pour créer le projet **ProductsServer** .

7.  Si vous avez déjà installé le Gestionnaire de package NuGet pour Visual Studio, passez à l’étape suivante. Dans le cas contraire, visitez [NuGet][] et cliquez sur [Installation de NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Suivez les invites pour installer le Gestionnaire de package NuGet, puis redémarrez Visual Studio.

7.  Dans l’Explorateur de solutions, cliquez sur le projet **ProductsServer** , puis cliquez sur **Gérer les Packages NuGet**.

8.  Cliquez sur l’onglet **Parcourir** , puis recherchez les `Microsoft Azure Service Bus`. Cliquez sur **installer**et accepter les conditions d’utilisation.

    ![][13]

    Notez que les assemblys du client requis sont maintenant référencés.

9.  Ajoutez une nouvelle classe pour le contrat de votre produit. Dans l’Explorateur de solutions, cliquez sur le projet **ProductsServer** et cliquez sur **Ajouter**, puis cliquez sur **classe**.

10. Dans la zone **nom** , tapez le nom **ProductsContract.cs**. Puis cliquez sur **Ajouter**.

11. Dans **ProductsContract.cs**, remplacez la définition de l’espace de noms avec le code suivant, qui définit le contrat pour le service.

    ```
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;
    
        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }
    
        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();
    
        }
    
        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

12. Dans Program.cs, remplacez la définition de l’espace de noms avec le code suivant, qui ajoute le service de profil et de l’hôte pour lui.

    ```
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;
    
        // Implement the IProducts interface.
        class ProductsService : IProducts
        {
    
            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };
    
            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }
    
        }
    
        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();
    
                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();
    
                sh.Close();
            }
        }
    }
    ```

13. Dans l’Explorateur de solutions, double-cliquez sur le fichier **App.config** pour l’ouvrir dans l’éditeur de Visual Studio. Au bas de la ** &lt;système. ServiceModel&gt; ** élément (mais toujours dans &lt;système. ServiceModel&gt;), ajoutez le code XML suivant. Veillez à remplacer *yourServiceNamespace* par le nom de votre espace de noms et *yourKey* avec la clé SAS que vous avez récupérée précédemment à partir du portail :

    ```
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. Toujours dans App.config, dans le ** &lt;appSettings&gt; ** élément, la valeur de chaîne de la connexion avec la chaîne de connexion que vous avez précédemment obtenu à partir du portail de remplacement. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

14. Appuyez sur **Ctrl + Maj + B** ou dans le menu **Générer** , cliquez sur **Générer la Solution** pour générer l’application et vérifiez l’exactitude de votre travail jusqu'à présent.

## <a name="create-an-aspnet-application"></a>Création d’une application ASP.NET

Dans cette section, vous allez générer une application ASP.NET simple qui affiche les données récupérées à partir de votre service technique.

### <a name="create-the-project"></a>Créer le projet

1.  Assurez-vous que Visual Studio s’exécute avec des privilèges d’administrateur.

2.  Dans Visual Studio, dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.

3.  À partir de **Modèles installés**, sous **Visual C#**, cliquez sur / / / **ASP.NET Web Application**. Nommez le projet **ProductsPortal**. Puis cliquez sur **OK**.

    ![][15]

4.  Dans la liste **Sélectionnez un modèle** , cliquez sur **MVC**. 

6.  La case à cocher pour **l’hôte dans le nuage**.

    ![][16]

5. Cliquez sur le bouton **Modifier l’authentification** . Dans la boîte de dialogue **Modifier l’authentification** , cliquez sur **Aucune authentification**, puis cliquez sur **OK**. Pour ce didacticiel, vous déployez une application qui n’a pas besoin de connexion de l’utilisateur.

    ![][18]

6.  Dans la section **Microsoft Azure** de la boîte de dialogue **Nouveau projet ASP.NET** , assurez-vous que **l’hôte dans le nuage** est sélectionnée et que le **Service d’application** est sélectionnée dans la liste déroulante.

    ![][19]

7. Cliquez sur **OK**. 

8. Vous devez maintenant configurer les ressources Azure pour une nouvelle application web. Suivez toutes les étapes de la section [ressources Azure de configurer une nouvelle application web](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Ensuite revenir à ce didacticiel et passez à l’étape suivante.

5.  Dans l’Explorateur de solutions, cliquez sur **modèles** et cliquez sur **Ajouter**, puis cliquez sur **une classe**. Dans la zone **nom** , tapez le nom **Product.cs**. Puis cliquez sur **Ajouter**.

    ![][17]

### <a name="modify-the-web-application"></a>Modifier l’application web

1.  Dans le fichier Product.cs dans Visual Studio, remplacez la définition de l’espace de noms existant par le code suivant.

    ```
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
        public class Product
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public string Quantity { get; set; }
        }
    }
    ```

2.  Dans l’Explorateur de solutions, développez le dossier **Controllers** , puis double-cliquez sur le fichier **HomeController.cs** pour l’ouvrir dans Visual Studio.

3. Dans **HomeController.cs**, remplacez la définition de l’espace de noms existant par le code suivant.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;
    
        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

3.  Dans l’Explorateur de solutions, développez le dossier Views\Shared, puis double-cliquez sur **_Layout.cshtml** pour l’ouvrir dans l’éditeur de Visual Studio.

5.  Remplacez toutes les occurrences de **Mon Application ASP.NET** **produits de LITWARE**.

6. Supprimer les liens ** **accueil**et **Contact** **. Dans l’exemple suivant, supprimez le code en surbrillance.

    ![][41]

7.  Dans l’Explorateur de solutions, développez le dossier Views\Home, puis double-cliquez sur **Index.cshtml** pour l’ouvrir dans l’éditeur de Visual Studio.
    Remplacez tout le contenu du fichier par le code suivant.

    ```
    @model IEnumerable<ProductsWeb.Models.Product>
    
    @{
            ViewBag.Title = "Index";
    }
    
    <h2>Prod Inventory</h2>
    
    <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                  <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>
    
    @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>
    }
    
    </table>
    ```

9.  Pour vérifier l’exactitude de votre travail jusqu’ici, vous pouvez appuyer sur **Ctrl + Maj + B** pour générer le projet.


### <a name="run-the-app-locally"></a>Exécutez l’application localement

Exécutez l’application pour vérifier qu’il fonctionne.

1.  Assurez-vous que **ProductsPortal** est le projet actif. Cliquez sur le nom du projet dans l’Explorateur de solutions et sélectionnez **Définir comme projet de démarrage**.
2.  Dans Visual Studio, appuyez sur F5.
3.  Votre application doit apparaître, en cours d’exécution dans un navigateur.

    ![][21]

## <a name="put-the-pieces-together"></a>Reconstituer le puzzle

L’étape suivante consiste à raccorder le serveur local de produits à l’application ASP.NET.

1.  S’il n’est pas déjà ouvert, dans Visual Studio d’ouvrir à nouveau le projet **ProductsPortal** que vous avez créé dans la section [Création d’une application ASP.NET](#create-an-aspnet-application) .

2.  Similaire à l’étape de la section « Création d’un serveur locale », ajoutez le package NuGet aux références du projet. Dans l’Explorateur de solutions, cliquez sur le projet **ProductsPortal** , puis cliquez sur **Gérer les Packages NuGet**.

3.  Recherchez « Bus de Service » et sélectionnez l’élément de **Bus des services Microsoft Azure** . Terminer l’installation, puis fermez cette boîte de dialogue.

4.  Dans l’Explorateur de solutions, cliquez sur le projet **ProductsPortal** , puis cliquez sur **Ajouter**, puis **Un élément existant**.

5.  Accédez au fichier **ProductsContract.cs** à partir du projet de console **ProductsServer** . Cliquez sur ProductsContract.cs. Cliquez sur la flèche en regard de **Ajouter**, puis cliquez sur **Ajouter en tant que lien**.

    ![][24]

6.  Maintenant, ouvrez le fichier **HomeController.cs** dans l’éditeur Visual Studio et remplacez la définition de l’espace de noms par le code suivant. Veillez à remplacer *yourServiceNamespace* par le nom de votre espace de noms de service et *yourKey* avec votre clé d’associations de sécurité. Cela permettra au client d’appeler le service sur site, en retournant le résultat de l’appel.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Linq;
        using System.ServiceModel;
        using System.Web.Mvc;
        using Microsoft.ServiceBus;
        using Models;
        using ProductsServer;
    
        public class HomeController : Controller
        {
            // Declare the channel factory.
            static ChannelFactory<IProductsChannel> channelFactory;
    
            static HomeController()
            {
                // Create shared access signature token credentials for authentication.
                channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                    "sb://yourServiceNamespace.servicebus.windows.net/products");
                channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                    TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey") });
            }
    
            public ActionResult Index()
            {
                using (IProductsChannel channel = channelFactory.CreateChannel())
                {
                    // Return a view of the products inventory.
                    return this.View(from prod in channel.GetProducts()
                                     select
                                         new Product { Id = prod.Id, Name = prod.Name,
                                             Quantity = prod.Quantity });
                }
            }
        }
    }
    ```

7.  Dans l’Explorateur de solutions, cliquez droit sur la solution **ProductsPortal** (Vérifiez avec le bouton droit de la solution, et non sur le projet). Cliquez sur **Ajouter**, puis cliquez sur **Projet existant**.

8.  Naviguez jusqu’au projet **ProductsServer** , puis double-cliquez sur le fichier solution **ProductsServer.csproj** pour l’ajouter.

9.  **ProductsServer** doit exécuter pour afficher les données sur **ProductsPortal**. Dans l’Explorateur de solutions, avec le bouton droit de la solution **ProductsPortal** , puis cliquez sur **Propriétés**. La boîte de dialogue **Pages de propriétés** s’affiche.

10. Sur le côté gauche, cliquez sur le **Projet de démarrage**. Sur le côté droit, cliquez sur **plusieurs projets de démarrage**. S’assurer que **ProductsServer** et **ProductsPortal** sont, dans cet ordre, en cliquant sur **Démarrer** définie comme action pour les deux.

      ![][25]

11. Toujours dans la boîte de dialogue **Propriétés** , cliquez sur **Dépendances du projet** sur le côté gauche.

12. Dans la liste **projets** , cliquez sur **ProductsServer**. Assurez-vous que **ProductsPortal** n’est **pas** sélectionnée.

14. Dans la liste **projets** , cliquez sur **ProductsPortal**. Assurez-vous que **ProductsServer** est sélectionné. 

    ![][26]

15. Cliquez sur **OK** dans la boîte de dialogue **Pages de propriétés** .

## <a name="run-the-project-locally"></a>Exécuter le projet localement

Pour tester l’application localement, dans Visual Studio, appuyez sur **F5**. Le serveur local (**ProductsServer**) doit démarrer en premier, puis l’application **ProductsPortal** doit commencer dans une fenêtre de navigateur. Cette fois, vous verrez que l’inventaire des produits répertorie les données récupérées à partir du système local du service produit.

![][10]

Appuyez sur la touche **Actualiser** sur la page **ProductsPortal** . Chaque fois que vous actualisez la page, vous verrez l’application de serveur affiche un message lors de la `GetProducts()` de **ProductsServer** est appelée.

Fermez les applications avant de passer à l’étape suivante.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Déployer le projet de ProductsPortal dans une application web Azure

L’étape suivante consiste à convertir la partie frontale de **ProductsPortal** pour une application web Azure. Déployez d’abord le projet **ProductsPortal** , en suivant les étapes décrites dans la section [déployer le projet web à l’application web Azure](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app). Une fois le déploiement terminé, revenir à ce didacticiel et passez à l’étape suivante.

> [AZURE.NOTE] Vous pouvez voir un message d’erreur dans la fenêtre du navigateur lorsque le projet web **ProductsPortal** est automatiquement lancé après le déploiement. Cela est prévu et se produit parce que l’application **ProductsServer** ne s’exécute pas encore.

Copier l’URL de l’application web déployée, vous devez l’URL à l’étape suivante. Vous pouvez également obtenir cette URL à partir de la fenêtre de l’activité de Service d’application Azure dans Visual Studio :

![][9] 

### <a name="set-productsportal-as-web-app"></a>ProductsPortal de jeu en tant qu’application web

Avant d’exécuter l’application dans le nuage, vous devez vous assurer que **ProductsPortal** est démarré depuis Visual Studio sous la forme d’une application web.

1. Dans Visual Studio, cliquez sur le projet **ProjectsPortal** et puis cliquez sur **Propriétés**.

3. Dans la colonne de gauche, cliquez sur **Web**.

5. Dans la section **Action de démarrage** , cliquez sur le bouton **Démarrer l’URL** et dans la zone de texte Entrez l’URL de votre application web précédemment déployées ; par exemple, `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]

6. Dans le menu **fichier** dans Visual Studio, cliquez sur **Enregistrer tout**.

7. Dans le menu Générer dans Visual Studio, cliquez sur **Régénérer la Solution**.

## <a name="run-the-application"></a>Exécution de l’application

2.  Appuyez sur F5 pour générer et exécuter l’application. Le serveur local (l’application de console **ProductsServer** ) doit démarrer en premier, puis l’application **ProductsPortal** doit commencer dans une fenêtre de navigateur, comme illustré dans la capture d’écran suivante. Notez à nouveau que l’inventaire des produits répertorie les données extraites du service de produit système local et affiche ces données dans l’application web. Vérifiez l’URL pour vous assurer que **ProductsPortal** est en cours d’exécution dans le nuage, comme une application web Azure. 

    ![][1]

    > [AZURE.IMPORTANT] L’application de console **ProductsServer** doit être en cours d’exécution et capable de servir les données à l’application **ProductsPortal** . Si le navigateur affiche une erreur, attendez quelques secondes supplémentaires pour **ProductsServer** charger et afficher le message suivant. Appuyez ensuite sur **Actualiser** dans le navigateur.

    ![][37]

3. Dans le navigateur, appuyez sur la touche **Actualiser** sur la page **ProductsPortal** . Chaque fois que vous actualisez la page, vous verrez l’application de serveur affiche un message lors de la `GetProducts()` de **ProductsServer** est appelée.

    ![][38]

## <a name="next-steps"></a>Étapes suivantes  

Pour en savoir plus sur le Bus de Service, consultez les ressources suivantes :  

* [Bus des services Azure][sbwacom]  
* [L’utilisation de files d’attente de Bus de Service][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obtenir des outils et Kit de développement logiciel]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

