<properties
   pageTitle="Mise en route avec les acteurs fiable de Service Fabric | Microsoft Azure"
   description="Ce didacticiel vous guide à travers les étapes de la création, le débogage et déploiement d’un service basé sur acteur simple à l’aide des acteurs de Service Fabric fiable."
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
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Mise en route avec les acteurs fiable

> [AZURE.SELECTOR]
- [C# sous Windows](service-fabric-reliable-actors-get-started.md)
- [Java sous Linux](service-fabric-reliable-actors-get-started-java.md)

Cet article explique les notions de base des acteurs fiable du tissu Azure Service et vous guide à travers la création, le débogage et déploiement d’une application fiable acteur simple dans Visual Studio.

## <a name="installation-and-setup"></a>Installation et configuration
Avant de commencer, assurez-vous que vous disposez de l’environnement de développement de tissu de Service configuré sur votre ordinateur.
Si vous avez besoin pour le configurer, consultez les instructions détaillées sur la [façon de configurer l’environnement de développement](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Concepts de base
Pour vous familiariser avec les acteurs fiable, vous devez uniquement comprendre quelques concepts de base :

 * **Service de l’acteur**. Acteurs fiables sont empaquetées dans des Services fiables qui peuvent être déployés dans l’infrastructure de Service Fabric. Instances d’acteur sont activés dans une instance de service nommé.
 
 * **Inscription de l’acteur**. Comme avec des Services fiables, un service fiable acteur doit être enregistrée avec le runtime de Service Fabric. En outre, le type d’acteur doit être enregistrée avec le runtime de l’acteur.
 
 * **Interface d’acteur**. L’interface d’acteur est utilisée pour définir une interface fortement typée publique d’un acteur. Dans la terminologie de modèle acteur fiable, l’interface d’acteur définit les types de messages que l’acteur peut comprendre et processus. L’interface d’acteur est utilisé par d’autres acteurs et les applications clientes à « envoyer » (de façon asynchrone) les messages à l’acteur. Acteurs fiables peuvent implémenter plusieurs interfaces.
 
 * **Classe de ActorProxy**. La classe ActorProxy est utilisée par les applications clientes pour appeler les méthodes exposées par le biais de l’interface d’acteur. La classe ActorProxy fournit deux fonctionnalités importantes :
    * La résolution de nom : il est en mesure de localiser l’acteur dans le cluster (rechercher le nœud du cluster où elle est hébergée).
    * Gestion de défaillance : elle peut réessayer les appels de méthode et de résoudre l’emplacement de l’acteur après, par exemple, une panne qui requiert l’acteur être déplacé vers un autre nœud du cluster.

Les règles suivantes qui se rapportent aux interfaces d’acteur sont important de mentionner :

- Les méthodes d’interface acteur ne peut pas être surchargés.
- Interface d’acteur ne doivent pas avoir des méthodes, ref ou des paramètres facultatifs.
- Interfaces génériques ne sont pas pris en charge.

## <a name="create-a-new-project-in-visual-studio"></a>Créez un nouveau projet dans Visual Studio
Une fois que vous avez installé les outils de Service Fabric pour Visual Studio, vous pouvez créer de nouveaux types de projet. Les nouveaux types de projet sont dans la catégorie de **nuage** de la boîte de dialogue **Nouveau projet** .


![Outils de service Fabric pour Visual Studio - nouveau projet][1]

Dans la boîte de dialogue suivante, vous pouvez choisir le type de projet que vous souhaitez créer.

![Modèles de projet de service Fabric][5]

Pour le projet HelloWorld, nous allons utiliser le service d’acteurs fiable de tissu de Service.

Après avoir créé la solution, vous devez voir la structure suivante :

![Structure de projet de service Fabric][2]

## <a name="reliable-actors-basic-building-blocks"></a>Blocs de construction de base fiables acteurs

Une solution fiable acteurs typique se compose de trois projets :

* **Le projet d’application (MyActorApplication)**. C’est le projet qui regroupe tous les services conjointement pour le déploiement. Il contient les scripts *ApplicationManifest.xml* et PowerShell pour gérer l’application.

* **Le projet d’interface (MyActor.Interfaces)**. C’est le projet qui contient la définition d’interface pour l’acteur. Dans le projet MyActor.Interfaces, vous pouvez définir les interfaces qui seront utilisés par les acteurs de la solution. Vos interfaces acteur peuvent être définis dans n’importe quel projet avec n’importe quel nom, mais l’interface définit le contrat d’acteur qui est partagé par la mise en oeuvre de l’acteur et les clients de l’appel de l’acteur, donc il généralement judicieux de définir dans un assembly qui est distincte de la mise en oeuvre de l’acteur et peut être partagé par plusieurs autres projets.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **Le projet de service acteur (MyActor)**. Il s’agit du projet utilisé pour définir le service Fabric de Service qui va héberger l’acteur. Il contient l’implémentation de l’acteur. Une implémentation de l’acteur est une classe qui dérive du type de base `Actor` et implémente les interfaces qui sont définis dans le projet MyActor.Interfaces. Une classe d’acteur doit également implémenter un constructeur qui accepte un `ActorService` instance et une `ActorId` et les transmet à la base de `Actor` classe. Ainsi, pour l’injection de dépendance de constructeur de dépendances de plateforme.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

Le service de l’acteur doit être enregistré avec un type de service du runtime de Service Fabric. Afin que le Service de l’acteur exécuter les instances de votre acteur, votre type d’acteur doit également être enregistré avec le Service de l’acteur. Le `ActorRuntime` méthode d’inscription effectue ce travail pour les acteurs.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Si vous démarrez à partir d’un nouveau projet dans Visual Studio, et vous n'avez qu’une seule définition d’acteur, l’enregistrement est inclus par défaut dans le code que Visual Studio génère. Si vous définissez d’autres acteurs dans le service, vous devez ajouter l’enregistrement de l’acteur à l’aide de :

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [AZURE.TIP] Le runtime Service Fabric acteurs émet des [événements et des compteurs de performance liés aux méthodes d’acteur](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Elles sont utiles dans le diagnostic et la surveillance des performances.


## <a name="debugging"></a>Débogage

Les outils de Service Fabric pour Visual Studio prend en charge le débogage sur votre ordinateur local. Vous pouvez démarrer une session de débogage en appuyant sur la touche F5. Visual Studio génère (si nécessaire) les packages. Il déploie l’application sur le cluster local de Service Fabric et attache le débogueur.

Pendant le processus de déploiement, vous pouvez voir la progression dans la fenêtre **sortie** .

![Fenêtre de sortie de débogage Fabric du service][3]


## <a name="next-steps"></a>Étapes suivantes
 - [Comment acteurs fiables utiliser le Service Fabric](service-fabric-reliable-actors-platform.md)
 - [Gestion d’état acteur](service-fabric-reliable-actors-state-management.md)
 - [Acteur du cycle de vie et le garbage collection](service-fabric-reliable-actors-lifecycle.md)
 - [Documentation de référence API d’acteur](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemples de code](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
