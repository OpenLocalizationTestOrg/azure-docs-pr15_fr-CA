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
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Mise en route avec les acteurs fiable

> [AZURE.SELECTOR]
- [C# sous Windows](service-fabric-reliable-actors-get-started.md)
- [Java sous Linux](service-fabric-reliable-actors-get-started-java.md)

Cet article explique les notions de base des acteurs fiable du tissu Azure Service et vous guide à travers la création et le déploiement d’une application fiable acteur simple dans Java.

## <a name="installation-and-setup"></a>Installation et configuration
Avant de commencer, assurez-vous que vous disposez de l’environnement de développement de tissu de Service configuré sur votre ordinateur.
Si vous avez besoin de l’ajouter, passez à la [mise en route sur Mac](service-fabric-get-started-mac.md) ou [mise en route sous Linux](service-fabric-get-started-linux.md).

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

## <a name="create-an-actor-service"></a>Créer un service de l’acteur
Commencez par créer une nouvelle application de Service Fabric. Le Service Fabric pour Linux comprend un Yeoman générateur pour fournir la génération de modèles automatique pour une application de Service Fabric avec un service sans état. Commencez par exécuter le Yeoman suivant commande :

```bash
$ yo azuresfjava
```

Suivez les instructions pour créer un **Service fiable d’acteur**. Pour ce didacticiel, nom de l’application « HelloWorldActorApplication » et l’acteur « HelloWorldActor ». La génération de modèles automatique suivante sera créée :

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Blocs de construction de base fiables acteurs

Les concepts de base décrits précédemment traduisent dans les blocs de construction de base d’un service fiable acteur.

### <a name="actor-interface"></a>Interface d’acteur

Il contient la définition d’interface pour l’acteur. Cette interface définit le contrat d’acteur qui est partagé par les clients afin de généralement judicieux de définir dans un lieu distinct de la mise en oeuvre de l’acteur et peut être partagé par plusieurs autres services ou applications clientes, l’appel de l’acteur et de la mise en oeuvre de l’acteur.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Service de l’acteur 
Il contient votre mise en oeuvre de l’acteur et le code d’inscription acteur. La classe d’acteur implémente l’interface d’acteur. Voici où votre acteur effectue son travail.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Inscription de l’acteur

Le service de l’acteur doit être enregistré avec un type de service du runtime de Service Fabric. Afin que le Service de l’acteur exécuter les instances de votre acteur, votre type d’acteur doit également être enregistré avec le Service de l’acteur. Le `ActorRuntime` méthode d’inscription effectue ce travail pour les acteurs.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {
    
    public static void main(String[] args) throws Exception {
        
        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);
            
        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Client de test

Il s’agit d’une application cliente de test simple que vous pouvez exécuter séparément à partir de l’application de Service Fabric pour tester votre service d’acteur. Il s’agit d’un exemple où la ActorProxy peut être utilisé pour activer et communiquer avec les instances de l’acteur. Il ne pas obtenir déployé avec votre service.

### <a name="the-application"></a>L’application 

Enfin, l’application compresse le service acteur et autres services, que vous pouvez ajouter à l’avenir ensemble pour le déploiement. Il contient les détenteurs de *ApplicationManifest.xml* et de la place pour le package de service acteur.

## <a name="run-the-application"></a>Exécution de l’application

La Yeoman la structure comprend un script gradle pour générer l’application et bash déployer des scripts et des Nations unies-déployer l’application. Pour exécuter l’application, tout d’abord générer l’application avec gradle :

```bash
$ gradle
```

Cela produira un package d’application Fabric du Service qui peut être déployé à l’aide du Service de tissu Azure CLI. Le script install.sh contient les commandes CLI d’Azure nécessaires pour déployer le package d’application. Exécutez simplement le script install.sh à déployer :

```bask
$ ./install.sh
```
