<properties
   pageTitle="Créer un site web frontal de votre application à l’aide de ASP.NET Core | Microsoft Azure"
   description="Exposer votre application Fabric du Service sur le web à l’aide d’un projet d’API de base du Web ASP.NET et de la communication entre services via ServiceProxy."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2016"
   ms.author="seanmck"/>


# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Créer un web service frontal pour votre application à l’aide de ASP.NET Core

Par défaut, les services de Fabric de Service Azure ne fournissent pas une interface publique sur le web. Pour exposer les fonctionnalités de votre application pour les clients HTTP, vous devez créer un projet web pour agir en tant que point d’entrée et de communiquer à partir de là à vos services individuels.

Dans ce didacticiel, nous chercher où nous nous étions arrêtés dans le didacticiel de [Création de votre première application dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) et ajouter un service web devant le service de compteur avec état. Si vous ne le n'avez pas déjà fait, vous devez revenir en arrière et tout d’abord l’étape par l’intermédiaire de ce didacticiel.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Ajouter un service de base d’ASP.NET à votre application

Noyau d’ASP.NET est une infrastructure de développement de web léger, multiplates-formes que vous pouvez utiliser pour créer l’interface utilisateur du web moderne et API de web. Ajoutons un projet ASP.NET Web API à notre application existante.

>[AZURE.NOTE] Pour terminer ce didacticiel, vous devez [installer .NET Core 1.0][dotnetcore-install].

1. Dans l’Explorateur de solutions, cliquez sur **Services** dans le projet d’application et choisissez **Ajouter > nouveau Service de Fabric du Service**.

    ![Ajout d’un nouveau service à une application existante][vs-add-new-service]

2. Sur la page **créer un Service** , cliquez sur **Base de ASP.NET** et lui donner un nom.

    ![Choix du service web ASP.NET dans la boîte de dialogue Nouveau service][vs-new-service-dialog]

3. La page suivante fournit un ensemble de base d’ASP.NET des modèles de projet. Notez que ce sont les mêmes modèles que vous verriez si vous avez créé un projet de base de ASP.NET en dehors d’une application de Service Fabric. Pour ce didacticiel, nous allons choisir **l’API Web**. Toutefois, vous pouvez appliquer les mêmes concepts pour la génération d’une application web complète.

    ![Choix du type de projet ASP.NET][vs-new-aspnet-project-dialog]

    Une fois que votre projet Web API est créé, vous avez deux services dans votre application. Comme vous continuez à générer votre application, vous allez ajouter des services plus exactement de la même manière. Chacune peut être créée et mise à niveau de façon indépendante.

>[AZURE.TIP] Pour plus d’informations sur la création de services de base d’ASP.NET, consultez la [Documentation de base ASP.NET](https://docs.asp.net).

## <a name="run-the-application"></a>Exécution de l’application

Pour obtenir une idée de ce que nous avons terminé, nous allons déployer la nouvelle application et observez le comportement par défaut du modèle API Web de base ASP.NET fournit.

1. Appuyez sur F5 dans Visual Studio pour déboguer l’application.

2. Lorsque le déploiement est terminé, Visual Studio lance le navigateur à la racine du service ASP.NET Web API--quelque chose comme http://localhost:33003. Le numéro de port attribué de manière aléatoire et peut-être être différent sur votre ordinateur. Le modèle de l’API de base du Web ASP.NET ne fournit pas par défaut pour la racine, afin que vous obtiendrez une erreur dans le navigateur.

3. Ajouter `/api/values` à l’emplacement dans le navigateur. Cette action ouvre le `Get` méthode sur le ValuesController dans le modèle de l’API Web. Elle renvoie la réponse par défaut qui est fournie par le modèle : un tableau JSON qui contient deux chaînes :

    ![Valeurs par défaut retournés à partir du modèle API Web de base ASP.NET][browser-aspnet-template-values]

    À la fin de ce didacticiel, nous seront ont remplacé ces valeurs par défaut avec la dernière valeur de compteur de notre service dynamique.


## <a name="connect-the-services"></a>Les services de connexion

TISSU de service fournit une flexibilité complète dans la façon dont vous communiquez avec des services fiables. Au sein d’une seule application, vous pouvez avoir des services accessibles via TCP, d’autres services sont accessibles via une API REST de HTTP et encore d’autres services qui sont accessibles via des sockets de web. Pour d’informations sur les options disponibles et les compromis, consultez [communication avec les services](service-fabric-connect-and-communicate-with-services.md). Dans ce didacticiel, nous vous suivez l’une des approches plus simples et la `ServiceProxy` / `ServiceRemotingListener` les classes qui sont fournies dans le Kit de développement logiciel.

Dans le `ServiceProxy` approche (modélisé sur les appels de procédure distante ou RPC), vous définissez une interface en tant que contrat pour le service public. Ensuite, vous utilisez cette interface pour générer une classe proxy permettant d’interagir avec le service.


### <a name="create-the-interface"></a>Créer l’interface

Nous allons commencer par la création de l’interface en tant que le contrat entre le service d’état et de ses clients, y compris le projet de base de ASP.NET.

1. Dans l’Explorateur de solutions, avec le bouton droit de votre solution et choisissez **Ajouter** > **Nouveau projet**.

2. Choisissez l’entrée **Visual C#** dans le volet de navigation de gauche, puis sélectionnez le modèle de **Bibliothèque de classes** . Assurez-vous que la version du.NET Framework est définie au **point 4.5.2**.

    ![Création d’un projet d’interface de votre service avec état][vs-add-class-library-project]

3. Pour une interface utilisable par `ServiceProxy`, elle doit dériver de l’interface de l’Ile. Cette interface est incluse dans un des packages NuGet Fabric de Service. Pour ajouter le package, cliquez sur votre projet de bibliothèque de classes et sélectionnez **Manage NuGet Packages**.

4. Recherchez le package de la **Microsoft.ServiceFabric.Services** et l’installer.

    ![Ajout du package NuGet de Services][vs-services-nuget-package]

5. Dans la bibliothèque de classes, créer une interface avec une méthode unique, `GetCountAsync`, et d’étendre l’interface à partir de l’Ile.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### <a name="implement-the-interface-in-your-stateful-service"></a>Implémenter l’interface dans votre service avec état

Maintenant que nous avons défini l’interface, nous devons mettre en œuvre dans le service d’état.

1. Dans votre service avec état, ajoutez une référence au projet bibliothèque de classes qui contient l’interface.

    ![Ajout d’une référence au projet de bibliothèque de classes dans le service d’état][vs-add-class-library-reference]

2. Recherchez la classe qui hérite de `StatefulService`, tel que `MyStatefulService`, et l’étendre pour implémenter le `ICounter` interface.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. À présent implémenter la méthode unique qui est définie dans le `ICounter` interface, `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Exposer le service d’état à l’aide d’un programme d’écoute distant service

Avec le `ICounter` interface implémentée, la dernière étape de l’activation du service d’état peut être appelé à partir d’autres services est d’ouvrir un canal de communication. Pour les services avec état, Fabric de Service fournit une méthode overridable est appelée `CreateServiceReplicaListeners`. Avec cette méthode, vous pouvez spécifier un ou plusieurs écouteurs de communication, en fonction du type de communication que vous souhaitez activer à votre service.

>[AZURE.NOTE] La méthode équivalente pour l’ouverture d’un canal de communication vers les services sans état est appelée `CreateServiceInstanceListeners`.

Dans ce cas, nous remplaçons existant `CreateServiceReplicaListeners` méthode et fournir une instance de `ServiceRemotingListener`, qui crée un point de terminaison RPC qui peut être appelé à partir de clients par l’intermédiaire de `ServiceProxy`.  

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>La classe ServiceProxy permet d’interagir avec le service

Notre service avec état est prêt à recevoir le trafic provenant d’autres services. Ainsi, tout ce qui reste consiste à ajouter le code permettant de communiquer avec lui à partir du service web ASP.NET.

1. Dans votre projet ASP.NET, ajoutez une référence à la bibliothèque de classes qui contient le `ICounter` interface.

2. Dans le menu **Générer** , ouvrez le **Gestionnaire de Configuration**. Vous devriez voir quelque chose comme ceci :

    ![Bibliothèque de classes de configuration manager affichant comme AnyCPU][vs-configuration-manager]

    Notez que le projet de bibliothèque de classes, **MyStatefulService.Interface**, est configuré pour créer n’importe quel processeur. Pour fonctionner correctement avec le Service de Fabric, il doit être ciblé de manière explicite à le x64. Cliquez sur la liste déroulante et choisissez **Nouveau**, puis créer un x64 configuration de plate-forme.

    ![Création de la nouvelle plate-forme de la bibliothèque de classes][vs-create-platform]

3. Ajoutez le package Microsoft.ServiceFabric.Services au projet ASP.NET, comme précédemment pour le projet de bibliothèque de classes. Ceci fournira la `ServiceProxy` classe.

4. Dans le dossier **contrôleurs** , ouvrez le `ValuesController` classe. Notez que la `Get` méthode retourne actuellement simplement un tableau de chaîne codée en dur de « valeur1 » et « valeur2 », qui correspond à ce que nous l’avons vu précédemment dans le navigateur. Remplacez cette mise en œuvre par le code suivant :

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    La première ligne de code est la clé. Pour créer le proxy ICounter au service avec état, vous devez fournir deux éléments d’information : un ID de partition et le nom du service.

    Vous pouvez utiliser le partitionnement à l’échelle des services avec état en fractionnant la leur état dans des compartiments différents, selon une clé que vous définissez, par exemple un code client ou code postal. Dans notre application triviale, le service dynamique n'a qu’une seule partition, afin que la clé n’a aucune importance. N’importe quelle touche que vous fournissez entraîne la même partition. Pour en savoir plus sur le partitionnement des services, voir [comment la partition de Service Fabric des Services fiables](service-fabric-concepts-partitioning.md).

    Le nom du service est un URI de l’étoffe de formulaire : /&lt;nom de l’application&gt;/&lt;nom_service&gt;.

    Avec ces deux informations, Service Fabric peut identifier de manière unique l’ordinateur sur lequel les demandes doivent être envoyées à. Le `ServiceProxy` classe gère également de façon transparente le cas où l’ordinateur qui héberge la partition de service dynamique échoue et une autre machine doit être promu au rang de prendre sa place. Cette abstraction permet d’écrire le code client à traiter avec d’autres services beaucoup plus simples.

    Une fois le proxy, il suffit d’appeler le `GetCountAsync` méthode et retourner son résultat.

5. Appuyez sur F5 pour exécuter l’application modifiée. Comme avant, Visual Studio lance automatiquement le navigateur vers la racine du projet web. Ajouter le chemin d’accès « api/values », et vous devriez voir la valeur actuelle du compteur retournée.

    ![La valeur du compteur mémoire d’état s’affichée dans le navigateur][browser-aspnet-counter-value]

    Actualisez le navigateur régulièrement afin de vérifier la valeur du compteur de mise à jour.


>[AZURE.WARNING] Serveur web principal de ASP.NET fourni dans le modèle, appelé Kestrel, est [actuellement pas pris en charge pour gérer le trafic internet directement](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel). Pour les scénarios de production, envisagez d’héberger vos points de terminaison ASP.NET principal derrière des [API de gestion] [ api-management-landing-page] ou une autre passerelle avec accès via internet. Notez que le Fabric du Service n’est pas pris en charge pour le déploiement d’IIS.


## <a name="what-about-actors"></a>Qu’en est-il des acteurs ?

Ce didacticiel se concentre sur l’ajout d’un site web frontal communiqué avec un service dynamique. Toutefois, vous pouvez suivre un modèle très similaire pour communiquer avec les acteurs. En fait, il est un peu plus simple.

Lorsque vous créez un projet d’acteur, Visual Studio génère automatiquement un projet d’interface pour vous. Vous pouvez utiliser cette interface pour générer un proxy de l’acteur dans le projet web pour communiquer avec l’acteur. Le canal de communication est fourni automatiquement. Vous n’avez pas besoin de faire tout ce qui est équivalent à l’établissement d’une `ServiceRemotingListener` comme vous l’avez fait pour le service avec état dans ce didacticiel.

## <a name="how-web-services-work-on-your-local-cluster"></a>Fonctionnement des services web sur votre cluster local

En général, vous pouvez déployer exactement la même application de Service Fabric à un cluster comportant plusieurs ordinateur que vous avez déployés sur votre cluster local et très sûr qu’il fonctionnera comme prévu. C’est parce que votre cluster local est simplement une configuration de cinq nœuds qui est réduite à une seule machine.

Lorsqu’il s’agit des services web, toutefois, il existe une nuance de clé. Lorsque votre cluster se trouve derrière un équilibreur de charge, comme il le fait dans Azure, vous devez vous assurer que vos services web sont déployés sur chaque ordinateur dans la mesure où l’équilibrage de charge sera simplement alternée trafic entre les ordinateurs. Vous pouvez le faire en définissant le `InstanceCount` pour le service à la valeur spéciale «-1 ».

En revanche, lorsque vous exécutez un service web localement, vous devez vous assurer qu’une seule instance du service est en cours d’exécution. Dans le cas contraire, vous exécuterez de conflits à partir de plusieurs processus qui sont à l’écoute sur le même chemin d’accès et le même port. Par conséquent, le nombre d’instances de service web doit indiquer « 1 » pour les déploiements locaux.

Pour savoir comment configurer des valeurs différentes pour un environnement différent, consultez [Gestion des paramètres de l’application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créer un cluster dans Azure pour déployer votre application dans le nuage](service-fabric-cluster-creation-via-portal.md)
- [En savoir plus sur la communication avec les services](service-fabric-connect-and-communicate-with-services.md)
- [Pour en savoir plus sur le partitionnement avec état services](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/en-us/services/api-management/
