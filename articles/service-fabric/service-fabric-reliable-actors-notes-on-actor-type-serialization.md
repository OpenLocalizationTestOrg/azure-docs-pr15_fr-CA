<properties
   pageTitle="Sérialisation de type de notes d’acteurs fiables sur acteur | Microsoft Azure"
   description="Décrit les exigences de base pour la définition des classes sérialisables qui peuvent être utilisés pour définir les États d’acteurs fiable de tissu de Service et des interfaces"
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
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Sérialisation de type de notes sur les acteurs de Service Fabric fiable


Les arguments de toutes les méthodes, les types de résultats de tâches retournées par chaque méthode dans une interface d’acteur et des objets stockés dans le Gestionnaire d’état d’un acteur doivent être de [Contrat de données sérialisable](https://msdn.microsoft.com/library/ms731923.aspx)... Cela s’applique également aux arguments des méthodes définies dans les [interfaces d’événement acteur](service-fabric-reliable-actors-events.md#actor-events). (Les méthodes d’interface acteur événement toujours retournent void.)

## <a name="custom-data-types"></a>Types de données personnalisés

Dans cet exemple, l’interface d’acteur suivant définit une méthode qui retourne un type de données personnalisé appelé `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

L’interface est impelemented par un acteur, qui utilise le Gestionnaire d’état pour stocker un `VoicemailBox` objet :

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

Dans cet exemple, le `VoicemailBox` l’objet est sérialisé lorsque :
 - L’objet est transmise entre une instance de l’acteur et un appelant.
 - L’objet est enregistré dans le Gestionnaire d’état où il est conservé sur le disque et répliquée vers d’autres nœuds.
 
L’infrastructure de fiabilité acteur utilise DataContract sérialisation. Par conséquent, les objets de données personnalisés et leurs membres doivent être annotés avec les attributs **DataContract** et **DataMember** , respectivement

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## <a name="next-steps"></a>Étapes suivantes
 - [Acteur du cycle de vie et le garbage collection](service-fabric-reliable-actors-lifecycle.md)
 - [Rappels et les temporisations de l’acteur](service-fabric-reliable-actors-timers-reminders.md)
 - [Événements de l’acteur](service-fabric-reliable-actors-events.md)
 - [Réentrance d’acteur](service-fabric-reliable-actors-reentrancy.md)
 - [Polymorphisme d’acteur et les modèles de conception orientée objet](service-fabric-reliable-actors-polymorphism.md)
 - [Diagnostic de l’acteur et l’analyse des performances](service-fabric-reliable-actors-diagnostics.md)
