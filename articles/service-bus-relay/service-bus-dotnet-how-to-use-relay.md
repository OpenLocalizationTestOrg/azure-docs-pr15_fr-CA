<properties
    pageTitle="Comment faire pour utiliser le relais de Bus de Service avec .NET | Microsoft Azure"
    description="Apprenez à utiliser le service de relais de Bus des services Azure pour connecter deux applications hébergées dans des emplacements différents."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="sethm"/>


# <a name="how-to-use-the-azure-service-bus-relay-service"></a>Comment faire pour utiliser le service de relais de Bus des services Azure

Cet article décrit comment utiliser le service de relais de Bus de Service. Les exemples sont écrits en C# et utilisent l’API de Windows Communication Foundation (WCF) avec les extensions contenues dans l’assembly de Service Bus. Pour plus d’informations sur le relais de Bus des services, consultez la vue d’ensemble du [Bus de Service de relais de messagerie](service-bus-relay-overview.md) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-the-service-bus-relay"></a>En quoi consiste le relais de Bus de Service ?

Le service de [Bus de Service *relais* ](service-bus-relay-overview.md) vous permet de vous permet de générer des applications hybrides qui s’exécutent dans un centre de données Azure et dans votre propre environnement d’entreprise locaux. Le relais de Bus de Service facilite cela en vous permettant d’exposer des services Windows Communication Foundation (WCF) qui résident au sein d’un réseau d’entreprise vers le nuage public, sans avoir à ouvrir la connexion via un pare-feu ou nécessiter de changements intrusifs dans une infrastructure de réseau d’entreprise en toute sécurité.

![Concepts de relais](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Le relais de Bus de Service vous permet d’héberger des services WCF au sein de votre environnement d’entreprise existant. Vous pouvez ensuite déléguer à l’écoute d’entrant sessions et requêtes à ces services WCF du service de Bus des services s’exécutant dans Azure. Cela vous permet d’exposer ces services au code d’application exécuté dans Azure, ou pour les travailleurs mobiles ou les environnements des partenaires de l’extranet. Bus de service vous permet de contrôler en toute sécurité les personnes qui peut accéder à ces services à un niveau de granularité fin. Il offre un moyen puissant et sécurisé pour exposer les fonctionnalités de l’application et les données de vos solutions d’entreprise existantes et de tirer parti du nuage.

Cet article explique comment utiliser le relais de Bus de Service pour créer un service web WCF exposé à l’aide d’une liaison de canal TCP, qui implémente une conversation sécurisée entre les deux parties.

## <a name="create-a-service-namespace"></a>Créer un espace de noms du service

Pour commencer à utiliser le relais de Bus de Service dans Azure, vous devez d’abord créer un espace de noms. Un espace de noms fournit un conteneur de portée pour l’adressage des ressources de Bus de Service au sein de votre application.

Pour créer un espace de noms du service :

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Obtenir le package NuGet de Bus de Service

Le [package NuGet de Bus de Service](https://www.nuget.org/packages/WindowsAzure.ServiceBus) est le moyen le plus simple d’obtenir l’API du Bus de Service et configurer votre application avec toutes les dépendances de Service Bus. Pour installer le package NuGet dans votre projet, effectuez les opérations suivantes :

1.  Dans l’Explorateur de solutions, cliquez sur **références**, puis cliquez sur **Gérer les Packages NuGet**.
2.  Recherchez « Bus de Service » et sélectionnez l’élément de **Bus des services Microsoft Azure** . Cliquez sur **installer** pour terminer l’installation, puis fermez la boîte de dialogue suivante :

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="use-service-bus-to-expose-and-consume-a-soap-web-service-with-tcp"></a>Bus de Service permet d’exposer et de consommer un service web SOAP TCP

Pour exposer un service web de WCF SOAP existant pour l’extérieur, vous devez apporter des modifications pour les liaisons de service et les adresses. Cela peut nécessiter des modifications apportées à votre fichier de configuration, ou il peut nécessiter des modifications du code, selon la façon dont vous avez installé et configuré vos services WCF. Notez que WCF vous permet d’avoir plusieurs points de terminaison de réseau sur le même service, afin que vous pouvez conserver les points de terminaison internes existants lors de l’ajout de points de terminaison de Bus des services pour l’accès externe en même temps.

Dans cette tâche, vous créez un service WCF simple et lui ajouter un écouteur de Service Bus. Cet exercice suppose une connaissance avec Visual Studio et ne parcourt pas à tous les détails de la création d’un projet. Elle se concentre plutôt sur le code.

Avant de commencer cette procédure, effectuez la procédure suivante pour configurer votre environnement :

1.  Dans Visual Studio, créez une application console qui contient deux projets, « Client » et « Service », au sein de la solution.
2.  Ajouter le package Microsoft Azure Service Bus NuGet pour les deux projets. Ce package ajoute toutes les références d’assembly nécessaires à vos projets.

### <a name="how-to-create-the-service"></a>Comment faire pour créer le service

Commencez par créer le service lui-même. N’importe quel service WCF se compose d’au moins trois parties distinctes :

-   Définition d’un contrat qui décrit les messages échangés et les opérations à être appelé.
-   La mise en oeuvre dudit contrat.
-   Hôte héberge le service WCF qui expose plusieurs points de terminaison.

Les exemples de code de cette section traitent chacun de ces composants.

Le contrat définit une seule opération, `AddNumbers`, qui ajoute deux nombres et retourne le résultat. Le `IProblemSolverChannel` interface permet au client de gérer plus facilement la durée de vie du proxy. Création d’une telle interface est vivement conseillée. Il est judicieux de placer cette définition de contrat dans un fichier séparé, afin que vous pouvez référencer ce fichier à partir de projets de votre « Client » et le « Service », mais vous pouvez également copier le code dans les deux projets.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Avec le contrat en place, l’implémentation est simple.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configurer un hôte de service par programme

Avec le contrat et la mise en oeuvre en place, vous pouvez désormais héberger le service. Hébergement se produit à l’intérieur d’un objet [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx) , qui prend en charge de la gestion des instances du service et héberge les points de terminaison qui écoutent les messages. Le code suivant configure le service avec un point de terminaison local normal et un point de terminaison de Bus des services afin d’illustrer l’apparence, côte à côte, de points de terminaison internes et externes. Remplacez *l’espace de noms* de chaîne avec votre nom d’espace de noms et d’un *yourKey* avec la clé d’associations de sécurité que vous avez obtenu à l’étape d’installation précédente.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

Dans l’exemple, vous créez deux points de terminaison qui se trouvent sur la même implémentation de contrat. Un est local et une est projetée via le Bus de Service. Les principales différences entre eux sont les liaisons ; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) pour le local et [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) pour le point de terminaison de Bus de Service et les adresses. Le point de terminaison local a une adresse de réseau local avec un port distinct. Le point de terminaison de Bus de Service possède une adresse de point de terminaison composée de la chaîne `sb`, votre nom d’espace de noms et le chemin d’accès « solveur. » Il en résulte l’URI `sb://[serviceNamespace].servicebus.windows.net/solver`, qui identifie le point de terminaison de service sous la forme d’un point de terminaison TCP de Bus de Service avec un nom DNS externe qualifié. Si vous placez le code à remplacer les espaces réservés dans les `Main` fonction de l’application de **Service** , vous aurez un service fonctionnel. Si vous souhaitez que votre service d’écoute exclusivement sur le Bus des services, supprimez la déclaration de point de terminaison local.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configurer un hôte de service dans le fichier App.config

Vous pouvez également configurer l’hôte en utilisant le fichier App.config. Le code d’hébergement dans ce cas de service apparaît dans l’exemple suivant.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Les définitions de point de terminaison se déplacement dans le fichier App.config. Le package NuGet a déjà ajouté à une plage de définitions dans le fichier App.config, qui sont des extensions de la configuration requise pour le Bus de Service. L’exemple suivant, qui est l’équivalent exact du code précédent, doit apparaître directement sous l’élément **system.serviceModel** . Cet exemple de code suppose que votre espace de noms du projet C# nommé **Service**.
Remplacez les espaces réservés avec votre espace de noms de service Bus de Service et de la clé des associations de sécurité.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Après avoir apporté ces modifications, le service démarre comme auparavant, mais avec des points de terminaison en deux : un utilisateur local et écoute un dans le nuage.

### <a name="create-the-client"></a>Création du client

#### <a name="configure-a-client-programmatically"></a>Configuration d’un client par programme

Pour consommer le service, vous pouvez construire un client WCF à l’aide d’un objet [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) . Bus de service utilise un modèle de sécurité basée sur le jeton implémenté à l’aide des associations de sécurité. La classe [TokenProvider a](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) représente un fournisseur de jeton de sécurité avec les méthodes de fabrique intégrées qui retournent de certains fournisseurs de jetons bien connus. L’exemple suivant utilise la méthode [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) pour gérer l’acquisition du jeton SAS approprié. Le nom et la clé sont ceux obtenus à partir du portail comme décrit dans la section précédente.

Tout d’abord, de référence ou de copier le `IProblemSolver` contrat de code à partir du service dans votre projet client.

Puis, remplacez le code dans la `Main` méthode du client, à nouveau en remplaçant le texte d’espace réservé avec votre espace de noms de Bus de Service et de la clé des associations de sécurité.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Vous pouvez à présent générer le client et le service d’exécution (exécuter d’abord le service) et le client appelle le service et imprime **9**. Vous pouvez exécuter le client et le serveur sur des ordinateurs différents, même sur plusieurs réseaux, et continuera de fonctionner la communication. Le code client peut également s’exécuter dans le nuage ou localement.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configuration d’un client dans le fichier App.config

Le code suivant montre comment configurer le client en utilisant le fichier App.config.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Les définitions de point de terminaison se déplacement dans le fichier App.config. L’exemple suivant, qui est le même que le code répertorié précédemment, doit apparaître directement sous l’élément **system.serviceModel** . Ici, comme avant, vous devez remplacer les espaces réservés avec votre espace de noms de Bus de Service et de la clé des associations de sécurité.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du service de relais de Bus des services, cliquez sur ces liens pour en savoir plus.

- [Bus de service relayé des échanges de vue d’ensemble](service-bus-relay-overview.md)
- [Azure présentation architecturale de Bus des services](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- Téléchargez des exemples de Bus de Service à partir [d’échantillons d’Azure][] , ou consultez la [vue d’ensemble des exemples de Bus de Service][].

  [Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
  [Exemples d’Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [vue d’ensemble des exemples de Bus des services]: ../service-bus-messaging/service-bus-samples.md