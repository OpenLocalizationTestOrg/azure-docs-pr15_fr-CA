<properties
   pageTitle="Les diagnostics de Services fiables avec état | Microsoft Azure"
   description="Fonctionnalités de diagnostic pour les Services fiables avec état"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/17/2016"
   ms.author="alanwar"/>

# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Fonctionnalités de diagnostic pour les Services fiables avec état
La classe StatefulServiceBase de Services fiables avec état émet des événements de [source d’événement](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) qui peuvent être utilisés pour déboguer le service, fournir de comprendre comment fonctionne le runtime et dépannage.

## <a name="eventsource-events"></a>Événements de la source d’événement
Le nom de la source d’événement pour la classe StatefulServiceBase de Services fiables avec état est « Microsoft-ServiceFabric-Services ». Événements provenant de cette source d’événement apparaissent dans la fenêtre de [Diagnostics événements](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) lorsque le service est en cours [de débogage dans Visual Studio](service-fabric-debugging-your-application.md).

Exemples d’outils et de technologies qui permettent de collecte et/ou d’affichage des événements de la source d’événement sont [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Tests de diagnostic de Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)et la [Bibliothèque TraceEvent de Microsoft](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Événements

|Nom de l’événement|ID d’événement|Niveau|Description de l’événement|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|D’information|Émis lors du démarrage de la tâche de service RunAsync|
|StatefulRunAsyncCancellation|2|D’information|Émis lors de l’annulation de tâche du service de RunAsync|
|StatefulRunAsyncCompletion|3|D’information|Émis après la tâche de service RunAsync|
|StatefulRunAsyncSlowCancellation|4|Avertissement|Émis lors de la tâche RunAsync de service prend trop de temps pour effectuer l’annulation|
|StatefulRunAsyncFailure|5|Erreur|Émis lorsque le service RunAsync tâche lève une exception|

## <a name="interpret-events"></a>Interprétation des événements

Événements StatefulRunAsyncInvocation, StatefulRunAsyncCompletion et StatefulRunAsyncCancellation sont utiles pour le rédacteur du service pour comprendre le cycle de vie d’un service, ainsi que le minutage lorsqu’un service est démarré, annulé ou terminé. Cela peut être utile lorsque le débogage des problèmes de service ou pour comprendre le cycle de vie du service.

Rédacteurs du service doivent payer attentif aux événements StatefulRunAsyncSlowCancellation et StatefulRunAsyncFailure car ils indiquent des problèmes avec le service.

StatefulRunAsyncFailure est émis chaque fois que la tâche du service de RunAsync() lève une exception. En général, une exception indique une erreur ou un bogue dans le service. En outre, l’exception entraîne l’échec du service afin qu’il est déplacé vers un autre nœud. Cela peut être une opération coûteuse et peut retarder les demandes entrantes pendant que le service est déplacé. Rédacteurs du service doivent déterminer la cause de l’exception et réduire dans la mesure du possible, il.

StatefulRunAsyncSlowCancellation est émise chaque fois qu’une demande d’annulation de la tâche RunAsync prend plue de quatre secondes. Lorsqu’un service prend trop de temps pour effectuer l’annulation, il a un impact sur la capacité du service de redémarrer rapidement sur un autre nœud. Cela peut avoir un impact sur la disponibilité générale du service.
