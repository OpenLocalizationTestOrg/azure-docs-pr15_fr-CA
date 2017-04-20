<properties
   pageTitle="Événements d’acteurs fiables | Microsoft Azure"
   description="Introduction aux événements pour Service de Fabric des acteurs fiable."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/30/2016"
   ms.author="amanbha"/>


# <a name="actor-events"></a>Événements de l’acteur
Événements d’acteur fournissent un moyen d’envoyer des notifications de mieux à partir de l’acteur aux clients. Événements d’acteur sont conçus pour une communication acteur-à-client et ne doivent pas être utilisés pour la communication de l’acteur-à-acteur.

Les extraits de code suivants montrent comment utiliser des événements de l’acteur dans votre application.

Définissez une interface qui décrit les événements publiés par l’acteur. Cette interface doit être dérivée de la `IActorEvents` interface. Les arguments des méthodes doivent être [sérialisable de contrat de données](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Les méthodes doivent retourner void, en tant qu’événement notifications unidirectionnelle et meilleur effort.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Déclarez les événements publiés par l’acteur dans l’interface d’acteur.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

Côté client, implémentez le Gestionnaire d’événements.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

Sur le client, créer un proxy pour l’acteur qui publie l’événement et s’abonner à ses événements.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

En cas de basculement, l’acteur peut basculer d’un nœud ou un autre processus. Le proxy de l’acteur gère les abonnements actifs et automatiquement les abonnements nouveau. Vous pouvez contrôler l’intervalle de nouvel abonnement par le biais de la `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Pour annuler votre abonnement, vous devez utiliser le `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

Sur l’acteur, simplement publier les événements comme ils surviennent. S’il existe des abonnés à l’événement, le runtime acteurs les enverra la notification.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>Étapes suivantes
 - [Réentrance d’acteur](service-fabric-reliable-actors-reentrancy.md)
 - [Diagnostic de l’acteur et l’analyse des performances](service-fabric-reliable-actors-diagnostics.md)
 - [Documentation de référence API d’acteur](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemples de code](https://github.com/Azure/servicefabric-samples)
