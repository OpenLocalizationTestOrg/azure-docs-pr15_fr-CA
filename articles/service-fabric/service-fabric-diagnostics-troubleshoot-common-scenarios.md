<properties
   pageTitle="Résolution des problèmes avec le suivi d’événements | Microsoft Azure"
   description="Les problèmes les plus courants rencontrés lors du déploiement des services sur le Service Microsoft Azure Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/31/2016"
   ms.author="mattrow"/>


# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Résoudre les problèmes courants lorsque vous déployez des services sur Azure Fabric de Service

Lorsque vous exécutez des services sur votre ordinateur de développement, il est facile à utiliser [d’Outils de débogage de Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Pour les clusters à distance, les [rapports d’intégrité](service-fabric-view-entities-aggregated-health.md) sont toujours un bon point de départ. Le plus simple pour accéder à ces rapports sont par le biais de PowerShell ou [SFX](service-fabric-visualizing-your-cluster.md). Cet article suppose que vous déboguez un cluster à distance et avez une connaissance élémentaire de la façon d’utiliser une ou l’autre de ces outils.

##<a name="application-crash"></a>Blocage de l’application
La « Partition est inférieure à cibles réplica ou instance de compteur « rapport est une bonne indication que l’arrêt de votre service. Pour savoir où votre service se bloque prend un peu plus d’enquête. Lorsque votre service est en cours d’exécution à grande échelle, votre meilleur ami sera un ensemble de traces de bien pensé.  Nous suggérons que vous essayez [d’Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md) pour collecter ces traces et à l’aide d’une solution de [Recherche élastique](service-fabric-diagnostic-how-to-use-elasticsearch.md) pour l’affichage et de recherche de traces.

![SFX Partition santé](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###<a name="during-service-or-actor-initialization"></a>Lors de l’initialisation de service ou un acteur
Toutes les exceptions avant d’initialiser le type de service provoque l’arrêt du processus. Pour ces types de défaillances, le journal des événements applications affiche l’erreur à partir de votre service.
Il s’agit d’exceptions les plus courantes à afficher avant l’initialisation du service.

***System.IO.FileNotFoundException***

Cette erreur est souvent due à assembly dépendances manquantes. Vérifiez la propriété CopyLocal dans Visual Studio ou dans le global assembly cache pour le nœud.

***System.Runtime.InteropServices.COMException***
 *à System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory (IntPtr, IFabricStatefulServiceFactory)*
 
 Cela indique que le nom de type de service enregistré ne correspond pas le manifeste du service.

[Diagnostics d’Azure](service-fabric-diagnostics-how-to-setup-wad.md) peut être configuré pour télécharger automatiquement le journal des événements pour tous les nœuds de votre application.

###<a name="runasync-or-onactivateasync"></a>RunAsync() ou OnActivateAsync()
Si l’incident se produit pendant l’initialisation ou l’exécution de votre type de service inscrit ou un acteur, l’exception est interceptée par Azure Fabric de Service. Vous pouvez les afficher à partir des fournisseurs EventSource détaillées dans la section « Étapes suivantes ».

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les diagnostics existants fournis par le Service de Fabric :

* [Diagnostics d’acteurs fiable](service-fabric-reliable-actors-diagnostics.md)
* [Diagnostics de Services fiables](service-fabric-reliable-services-diagnostics.md)
