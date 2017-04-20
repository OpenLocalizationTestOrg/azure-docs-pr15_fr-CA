<properties
   pageTitle="Différences entre les Services en nuage et Service Fabric | Microsoft Azure"
   description="Une vue d’ensemble conceptuelle de la migration d’applications à partir des Services en nuage pour Service de Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>En savoir plus sur les différences entre les Services en nuage et de Fabric du Service avant de migrer les applications.
Le Service Microsoft Azure Fabric est la plate-forme d’applications de cloud de nouvelle génération pour les applications distribuées hautement évolutives et très fiables. Il introduit de nombreuses nouvelles fonctionnalités pour le conditionnement, le déploiement, la mise à niveau et gestion des applications en nuage distribués. 

Il s’agit d’un guide d’introduction à la migration d’applications à partir des Services en nuage pour Service de Fabric. Il se concentre essentiellement sur architecture et conception des différences entre les Services en nuage et de Service de Fabric.
 
## <a name="applications-and-infrastructure"></a>Applications et infrastructure

Une différence fondamentale entre les Services en nuage et de Fabric du Service est la relation entre les ordinateurs virtuels, les charges de travail et les applications. Une charge de travail est défini comme le code que vous écrivez pour effectuer une tâche spécifique ou de fournir un service.
 
 - **Les Services en nuage est sur le déploiement des applications en tant que machines virtuelles.** Le code que vous écrivez est étroitement couplé à une instance de machine virtuelle, un site ou un rôle Worker. Pour déployer une charge de travail dans les Services en nuage consiste à déployer une ou plusieurs instances de machines virtuelles qui s’exécutent de la charge de travail. Il n’y a pas de séparation entre les applications et les ordinateurs virtuels, et par conséquent, il n’y a aucune définition formelle d’une application. Une application peut être considérée sous la forme d’un ensemble d’instances de Web ou rôle de travail au sein d’un déploiement de Services en nuage ou un déploiement complet de Services en nuage. Dans cet exemple, une application est indiquée sous la forme d’un ensemble d’instances de rôle.
 
![Applications de Services de cloud et de la topologie][1]

 - **TISSU de service est sur le déploiement d’applications sur des machines virtuelles existantes ou les ordinateurs exécutant le Service Fabric sous Windows ou Linux.** Les services que vous écrivez sont complètement dissociées de l’infrastructure sous-jacente, qui est abstrait par la plate-forme d’applications Fabric de Service, pour une application peut être déployée sur plusieurs environnements. Une charge de travail dans le Service Fabric est appelée un « service », et un ou plusieurs services sont regroupés dans une application qui s’exécute sur la plate-forme d’applications Fabric de Service formellement défini. Plusieurs applications peuvent être déployées vers un seul cluster Service Fabric.
 
![Topologie et applications Fabric de Service][2]
 
Service lui-même est une couche de plate-forme d’application qui s’exécute sous Windows ou Linux, alors que les Services en nuage est un système pour le déploiement d’Azure gérés de machines virtuelles avec des charges de travail associés.
Le modèle d’application Fabric de Service a un certain nombre d’avantages :

 - Délais de déploiement rapide. Création d’instances de la machine virtuelle peut prendre beaucoup de temps. Service de fabric, les ordinateurs virtuels sont déployés uniquement une fois pour former un cluster qui héberge la plate-forme d’applications Fabric de Service. À ce stade, des packages d’application peuvent être déployés rapidement au cluster.
 - Hébergement de haute densité. Dans les Services en nuage, un rôle de travail ordinateur virtuel héberge une charge de travail. Service de fabric, les applications sont distinctes à partir d’ordinateurs virtuels qui s’exécutent, c'est-à-dire que vous pouvez déployer un grand nombre d’applications d’un petit nombre d’ordinateurs virtuels, ce qui peut réduire les coûts globaux des déploiements de grandes envergure.
 - Le tissu de Service plate-forme peut exécuter n’importe quel endroit qui présente des machines serveur Windows ou Linux, qu’il s’agisse d’Azure ou sur site. La plate-forme fournit une couche d’abstraction sur l’infrastructure sous-jacente afin que votre application peut s’exécuter dans différents environnements. 
 - Gestion des applications distribuées. TISSU de service est une plate-forme que non seulement les hôtes d’applications distribuées, mais aussi vous aide à gérer leur cycle de vie indépendamment de la machine virtuelle hébergement ou machine de cycle de vie.

## <a name="application-architecture"></a>Architecture de l’application

L’architecture d’une application de Services en nuage inclut généralement de nombreuses dépendances service externe, tels que Service Bus, Table d’Azure et stockage Blob, SQL, Redis, pour gérer l’état et les données d’une application et de la communication entre les rôles Web et Worker dans un déploiement de Services en nuage. Un exemple d’une application de Services en nuage complète peut ressembler à ceci :  

![Architecture de Services cloud][9]

Applications Fabric de service peuvent également choisir d’utiliser les mêmes services externes dans une application complète. Le chemin de migration la plus simple à partir des Services en nuage pour tissu de Service à l’aide de cet exemple, l’architecture des Services de nuage, est de remplacer uniquement le déploiement des Services Cloud avec une application de Service Fabric, conserver l’architecture globale de la même. Les rôles Web et Worker peuvent être portés aux services de Fabric de Service sans état avec des modifications de code minimal.

![Architecture de service Fabric après la migration simple][10]

À ce stade, le système doit continuer à utiliser le même qu’avant. En tirant profit des fonctionnalités de dynamique du Service Fabric, banques d’état externe peut être admise comme avec état services le cas échéant. Cela est plus complexe que la simple migration de rôles Web et Worker aux services de Fabric de Service sans état, car il nécessite l’écriture de services personnalisés qui fournissent une fonctionnalité équivalente à votre application comme des services externes avant. Les avantages de cette opération sont les suivantes : 

 - Suppression des dépendances externes 
 - Unifier le déploiement, la gestion et la mise à niveau des modèles. 
 
Une architecture qui en résulte d’exemple d’internalizing de ces services pourrait ressembler à ceci :

![Architecture de service Fabric après migration complète][11]

## <a name="communication-and-workflow"></a>Communication et le workflow

La plupart des applications de Service Cloud constitué de plus d’un niveau. De même, une application de Service Fabric se compose de plus d’un service (généralement de nombreux services). Deux modèles de communication courants sont une communication directe et via un stockage durable externe.

### <a name="direct-communication"></a>Communication directe

Avec une communication directe, niveaux peut communiquer directement par l’intermédiaire de point de terminaison exposé par chaque couche. Dans les environnements sans état, tels que les Services en nuage, cela signifie sélectionner une instance d’un rôle VM, soit au hasard ou la répétition alternée pour équilibrer la charge et se connecter directement à son point de terminaison.

![Communication directe de Services de cloud][5]

 Communication directe est un modèle commun de communication dans le Service Fabric. La principale différence entre un Service de Fabric et des Services en nuage est que vous vous connectez à un ordinateur virtuel, les Services Cloud en considérant que le Service de fabric, vous connectez à un service. Cette distinction est importante pour plusieurs raisons :

 - Services de Fabric de Service ne sont pas liés aux ordinateurs virtuels qui hébergent les ; Services peut se déplacer dans le cluster et en fait, doivent se déplacer pour diverses raisons : ressource équilibrage, basculement, mises à niveau des applications et l’infrastructure et les contraintes de placement ou de charge. Cela signifie le qu'adresse d’une instance service peut changer à tout moment. 
 - Un ordinateur virtuel dans un Service de Fabric peut héberger plusieurs services, chacun avec des points de terminaison uniques.

TISSU de service fournit un mécanisme de découverte de service, appelé le Service d’attribution de noms, ce qui peut être utilisé pour résoudre les adresses de point de terminaison des services. 

![Communication directe de tissu de service][6]

### <a name="queues"></a>Files d’attente

Un mécanisme de communication commun entre les différents niveaux dans des environnements sans état, tels que les Services en nuage est d’utiliser une file d’attente du périphérique de stockage externe pour durablement enregistrer les tâches de travail à partir d’un niveau à un autre. Un scénario courant est une couche web qui envoie les travaux à une file d’attente d’Azure ou le Bus de Service où les instances de rôle de travail peuvent supprimer file d’attente et traiter les tâches.

![Communication de file d’attente de Services de cloud][7]

Le même modèle de communication peut servir de Service Fabric. Cela peut être utile lors de la migration d’une application de Services en nuage existante à un Service de Fabric. 

![Communication directe de tissu de service][8]
 
## <a name="next-steps"></a>Étapes suivantes

Le chemin de migration la plus simple à partir des Services en nuage pour tissu de Service est de remplacer uniquement le déploiement des Services Cloud avec une application de Service Fabric, conserver l’architecture globale de votre application à peu près le même. L’article suivant fournit un guide pour vous aider à convertir un site ou un rôle de travail à un service de Fabric de Service sans état.

 - [Migration simple : convertir un site ou un rôle de travail à un service de Fabric de Service sans état](./service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
