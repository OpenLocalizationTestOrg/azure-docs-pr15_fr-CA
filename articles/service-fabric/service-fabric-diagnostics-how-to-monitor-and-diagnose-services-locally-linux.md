<properties
   pageTitle="Localement de surveiller et de diagnostiquer les services écrits avec Azure Fabric de Service | Microsoft Azure"
   description="Découvrez comment surveiller et diagnostiquer vos services écrits à l’aide de Microsoft Azure Fabric de Service sur un ordinateur de développement local."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Surveiller et diagnostiquer des services dans une configuration de développement local machine


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

Surveillance, détection et diagnostic et résolution des problèmes permettent de services pour continuer avec une perturbation minimale de l’expérience utilisateur. Surveillance et diagnostic est critiques dans un environnement réel de production déployée. L’adoption d’un modèle semblable au cours du développement de services garantit que le pipeline de diagnostic fonctionne lorsque vous passez à un environnement de production. TISSU de service facilite pour les développeurs de service mettre en œuvre des tests de diagnostic qui peut fonctionner en toute transparence entre les configurations de développement local de machine unique et les configurations de cluster de production réel.


## <a name="debugging-service-fabric-java-applications"></a>Débogage d’applications Java de tissu de Service

Pour les applications Java, [plusieurs infrastructures de journalisation](http://en.wikipedia.org/wiki/Java_logging_framework) sont disponibles. Dans la mesure où `java.util.logging` est l’option par défaut avec le JRE, il est également utilisé pour les [exemples de code dans github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  La section suivante explique comment configurer le `java.util.logging` framework. 
 
À l’aide de java.util.logging vous pouvez rediriger vos journaux d’application dans la mémoire, les flux de sortie, les fichiers de console ou sockets. Pour chacune de ces options, il existe des gestionnaires par défaut déjà fournis dans le framework. Vous pouvez créer un `app.properties` fichier pour configurer le Gestionnaire de fichier de votre application rediriger tous les journaux dans un fichier local. 

L’extrait de code suivant contient un exemple de configuration : 

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

Le dossier vers lequel pointe le `app.properties` fichier doit exister. Après le `app.properties` fichier est créé, vous devez également modifier votre script de point d’entrée, `entrypoint.sh` dans les `<applicationfolder>/<servicePkg>/Code/` dossier pour définir la propriété `java.util.logging.config.file` à `app.propertes` fichier. L’entrée doit ressembler à l’extrait de code suivant :

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
Cette configuration se traduit par des journaux collectés de manière rotative à `/tmp/servicefabric/logs/`. Le **%u** et **%g** permettent la création de plusieurs fichiers, avec les noms de fichiers mysfapp0.log, mysfapp1.log et ainsi de suite. Si aucun gestionnaire n’est pas configuré explicitement, le Gestionnaire de console est enregistré par défaut. Un peut afficher les journaux dans syslog sous /var/log/syslog.
 
Pour plus d’informations, consultez les [exemples de code dans github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  



## <a name="next-steps"></a>Étapes suivantes
Le même code de traçage ajouté à votre application fonctionne également avec les tests de diagnostic de votre application sur un cluster Azure. Consultez ces articles qui traitent les différentes options pour les outils et décrivent comment vous pouvez les définir.
* [Comment collecter les journaux de diagnostic d’Azure](service-fabric-diagnostics-how-to-setup-lad.md)
