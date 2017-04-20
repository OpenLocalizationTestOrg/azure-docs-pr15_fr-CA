<properties
   pageTitle="Mise en route des Services fiables | Microsoft Azure"
   description="Introduction à la création d’une application Microsoft Azure Fabric de Service avec les services sans et avec état."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="vturecek"/>

# <a name="get-started-with-reliable-services"></a>Mise en route des Services fiables

> [AZURE.SELECTOR]
- [C# sous Windows](service-fabric-reliable-services-quick-start.md)
- [Java sous Linux](service-fabric-reliable-services-quick-start-java.md)

Cet article explique les notions de base du Service Azure Fabric des Services fiables et vous guide à travers la création et le déploiement d’une application de Service fiable simple écrite en Java.

## <a name="installation-and-setup"></a>Installation et configuration
Avant de commencer, assurez-vous que vous disposez de l’environnement de développement de tissu de Service configuré sur votre ordinateur.
Si vous avez besoin de l’ajouter, passez à la [mise en route sur Mac](service-fabric-get-started-mac.md) ou [mise en route sous Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Concepts de base
Pour la mise en route des Services fiables, vous devez uniquement comprendre quelques concepts de base :

 - **Type de service**: il s’agit de votre implémentation de service. Il est défini par la classe que vous écrivez qui étend `StatelessService` et tout autre code ou les dépendances utilisées avec un nom et un numéro de version.

 - **Instance du service nommé**: pour exécuter votre service, vous créez des instances nommées de votre type de service, beaucoup comme vous créez des instances d’objet d’un type de classe. Instances de service sont en fait des instanciations d’objet de votre classe de service que vous écrivez. 

 - **Hôte de service**: les instances de service nommé que vous créez doivent s’exécuter au sein d’un hôte. L’hôte de service est simplement un processus dans lequel les instances de votre service peuvent s’exécuter.

 - **L’inscription du service**: inscription rassemble tous les éléments. Le type de service doit être enregistré avec le runtime Service Fabric dans un hôte de service afin de permettre le Fabric du Service créer des instances de celui-ci à exécuter.  

## <a name="create-a-stateless-service"></a>Créer un service sans état

Commencez par créer une nouvelle application de Service Fabric. Le Service Fabric pour Linux comprend un Yeoman générateur pour fournir la génération de modèles automatique pour une application de Service Fabric avec un service sans état. Commencez par exécuter le Yeoman suivant commande :

```bash
$ yo azuresfjava
```

Suivez les instructions pour créer un **Service sans état fiable**. Pour ce didacticiel, nom de l’application « HelloWorldApplication » et le « HelloWorld » du service. Le résultat comprendra les répertoires pour le `HelloWorldApplication` et `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Implémenter le service

Ouvrez **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Cette classe définit le type de service et peut exécuter n’importe quel code. L’API du service fournit deux points d’entrée pour votre code :

 - Une méthode de point d’entrée ouvertes, appelée `runAsync()`, où vous pouvez commencer à exécuter les charges de travail, y compris les charges de travail de calcul de la durée d’exécution longue.

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

 - Un point d’entrée de communication dans lequel vous pouvez brancher votre pile de communication de choix. Voici où vous pouvez commencer à recevoir des demandes des utilisateurs et des autres services.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Dans ce didacticiel, nous allons nous concentrer sur le `runAsync()` méthode point d’entrée. Voici où vous pouvez immédiatement commencer à exécuter votre code.

### <a name="runasync"></a>RunAsync

La plate-forme appelle cette méthode lorsqu’une instance d’un service est placé et prêts à être exécutés. Le cycle d’ouverture/fermeture d’une instance de service peut se produire plusieurs fois sur la durée de vie du service dans son ensemble. Cela peut se produire pour diverses raisons, notamment :

- Le système déplace vos instances de service pour l’équilibrage des ressources.
- Des erreurs surviennent dans votre code.
- L’application ou le système est mis à niveau.
- Le matériel sous-jacent connaît une panne.

Cette orchestration est gérée par le Service Fabric conserver votre service hautement disponible et correctement équilibrée.

#### <a name="cancellation"></a>Annulation

Il est vital que votre code dans `runAsync()` pouvez arrêter l’exécution lors de la notification par le Service de Fabric. Le `CompletableFuture` retourné par `runAsync()` est annulée lorsque le Service Fabric requiert votre service pour arrêter l’exécution. L’exemple suivant montre comment gérer un événement d’annulation : 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);
        
        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });
 
        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });
 
        return completableFuture;
   }
``` 

### <a name="service-registration"></a>Inscription du service

Les types de service doivent être enregistrés avec le runtime Service Fabric. Le type de service est défini dans le `ServiceManifest.xml` et votre classe de service implémente `StatelessService`. L’inscription du service est effectuée dans le point d’entrée principal du processus. Dans cet exemple, est le processus principal point d’entrée `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Exécution de l’application

La Yeoman la structure comprend un script gradle pour générer l’application et bash déployer des scripts et des Nations unies-déployer l’application. Pour exécuter l’application, tout d’abord générer l’application avec gradle :

```bash
$ gradle
```

Cela produira un package d’application Fabric du Service qui peut être déployé à l’aide du Service de tissu Azure CLI. Le script install.sh contient les commandes CLI d’Azure nécessaires pour déployer le package d’application. Exécutez simplement le script install.sh à déployer :

```bask
$ ./install.sh
```
