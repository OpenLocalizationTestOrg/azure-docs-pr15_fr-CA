<properties
    pageTitle="Azure moniteur autoscaling commun métriques. | Microsoft Azure"
    description="Découvrez les mesures utilisés couramment autoscaling vos Services en nuage, les Machines virtuelles et les applications Web."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/02/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-autoscaling-common-metrics"></a>Mesures courantes Azure moniteur autoscaling

Azure autoscaling de moniteur vous permet de mettre à l’échelle le nombre d’instances en cours d’exécution, vers le haut ou vers le bas, en fonction des données de télémétrie (métrique). Ce document décrit les mesures communes que vous souhaitez utiliser. Dans le portail Azure pour les Services en nuage et les batteries de serveurs, vous pouvez choisir la mesure de la ressource à mettre à l’échelle. Toutefois, vous pouvez également choisir toute mesure à partir d’une autre ressource à l’échelle par.

Voici les détails sur comment rechercher et répertorier les mesures que vous souhaitez mettre à l’échelle. Les éléments suivants s’applique pour la mise à l’échelle et de jeux d’échelle de Machine virtuelle.

## <a name="compute-metrics"></a>Calculer la métrique
Par défaut, Azure VM v2 est fourni avec l’extension de diagnostics configurée et qu’ils ont les métriques suivantes est activées.

- [Mesures d’invité Windows VM v2](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Mesures de l’invité Linux VM v2](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

Vous pouvez utiliser la `Get MetricDefinitions` PoSH/API/CLI permet d’afficher les mesures disponibles pour votre ressource VMSS. 

Si vous utilisez des jeux d’échelle de machine virtuelle et vous ne voyez pas une mesure particulière est répertoriée, il est probablement *désactivée* dans votre extension de tests de diagnostic.

Si une mesure particulière ne sont pas échantillonnés ou transféré à la fréquence que vous le souhaitez, vous pouvez mettre à jour la configuration de diagnostics.

Si deux cas ci-dessus est true, puis examinez [PowerShell utilisation pour activer les tests de diagnostic sur un ordinateur virtuel exécutant Windows Azure](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) sur PowerShell pour configurer et mettre à jour votre extension Azure VM Diagnostics pour activer la mesure. Cet article inclut également un exemple de fichier de configuration de diagnostics.

### <a name="compute-metrics-for-windows-vm-v2-as-a-guest-os"></a>Calculer la métrique pour Windows VM v2 comme un système d’exploitation invité

Lorsque vous créez une nouvelle machine virtuelle (v2) dans Azure, tests de diagnostic est activé à l’aide de l’extension des tests de diagnostic.

Vous pouvez générer une liste des mesures à l’aide de la commande suivante dans PowerShell.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Vous pouvez créer une alerte pour les métriques suivantes.

|Nom de mesure|   Unité|
|---|---|
|\Processor(_Total)\% temps processeur    |Pour cent|
|\Processor(_Total)\% temps privilégié   |Pour cent|
|\Processor(_Total)\% temps utilisateur |Pour cent|
|Fréquence de \Processor d’informations (_Total) \Processor |Nombre|
|\System\Processes| Nombre|
|Nombre de \Thread \Processus (_Total)| Nombre|
|Nombre de \Handle \Processus (_Total)  |Nombre|
|\Memory\% octets dédiés utilisés   |Pour cent|
|\Memory\Available octets|   Octets|
|\Memory\Committed octets    |Octets|
|Limite de \Memory\Commit|  Octets|
|Octets de réserve \Memory\Pool|  Octets|
|\Memory\Pool octets de réserve non paginées|   Octets|
|\PhysicalDisk(_Total)\% disque fois| Pour cent|
|\PhysicalDisk(_Total)\% temps de lecture de disque|    Pour cent|
|\PhysicalDisk(_Total)\% temps d’écriture de disque|   Pour cent|
|\PhysicalDisk (_Total) \Disk/s   |CountPerSecond|
|\PhysicalDisk (_Total) \Disk lectures/s   |CountPerSecond|
|\PhysicalDisk (_Total) \Disk écritures/s  |CountPerSecond|
|\PhysicalDisk (_Total) \Disk octets/s   |BytesPerSecond|
|\Disk \PhysicalDisk (_Total) octets lus/s| BytesPerSecond|
|\PhysicalDisk (_Total) \Disk écriture octets/s |BytesPerSecond|
|\Avg \PhysicalDisk (_Total). Longueur de file d’attente du disque|  Nombre|
|\Avg \PhysicalDisk (_Total). Longueur de file d’attente lecture disque| Nombre|
|\Avg \PhysicalDisk (_Total). Longueur de file d’attente écriture disque |Nombre|
|\LogicalDisk(_Total)\% Libérez de l’espace| Pour cent|
|\LogicalDisk (_Total) \Free Mo|   Nombre|



### <a name="compute-metrics-for-linux-vm-v2-as-a-guest-os"></a>Calculer la métrique pour Linux VM v2 comme un système d’exploitation invité

Lorsque vous créez une nouvelle machine virtuelle (v2) dans Azure, tests de diagnostic est activé par défaut en utilisant l’extension des tests de diagnostic.

Vous pouvez générer une liste des mesures à l’aide de la commande suivante dans PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Vous pouvez créer une alerte pour les métriques suivantes.

|Nom de mesure                            |Unité|
|---|---|
|\Memory\AvailableMemory                |Octets|
|\Memory\PercentAvailableMemory         |Pour cent|
|\Memory\UsedMemory                     |Octets|
|\Memory\PercentUsedMemory              |Pour cent|
|\Memory\PercentUsedByCache             |Pour cent|
|\Memory\PagesPerSec                    |CountPerSecond|
|\Memory\PagesReadPerSec                |CountPerSecond|
|\Memory\PagesWrittenPerSec             |CountPerSecond|
|\Memory\AvailableSwap                  |Octets|
|\Memory\PercentAvailableSwap           |Pour cent|
|\Memory\UsedSwap                       |Octets|
|\Memory\PercentUsedSwap                |Pour cent|
|\Processor\PercentIdleTime             |Pour cent|
|\Processor\PercentUserTime             |Pour cent|
|\Processor\PercentNiceTime             |Pour cent|
|\Processor\PercentPrivilegedTime       |Pour cent|
|\Processor\PercentInterruptTime        |Pour cent|
|\Processor\PercentDPCTime              |Pour cent|
|\Processor\PercentProcessorTime        |Pour cent|
|\Processor\PercentIOWaitTime           |Pour cent|
|\PhysicalDisk\BytesPerSecond           |BytesPerSecond|
|\PhysicalDisk\ReadBytesPerSecond       |BytesPerSecond|
|\PhysicalDisk\WriteBytesPerSecond      |BytesPerSecond|
|\PhysicalDisk\TransfersPerSecond       |CountPerSecond|
|\PhysicalDisk\ReadsPerSecond           |CountPerSecond|
|\PhysicalDisk\WritesPerSecond          |CountPerSecond|
|\PhysicalDisk\AverageReadTime          |Secondes|
|\PhysicalDisk\AverageWriteTime         |Secondes|
|\PhysicalDisk\AverageTransferTime      |Secondes|
|\PhysicalDisk\AverageDiskQueueLength   |Nombre|
|\NetworkInterface\BytesTransmitted     |Octets|
|\NetworkInterface\BytesReceived        |Octets|
|\NetworkInterface\PacketsTransmitted   |Nombre|
|\NetworkInterface\PacketsReceived      |Nombre|
|\NetworkInterface\BytesTotal           |Octets|
|\NetworkInterface\TotalRxErrors        |Nombre|
|\NetworkInterface\TotalTxErrors        |Nombre|
|\NetworkInterface\TotalCollisions      |Nombre|




## <a name="commonly-used-web-server-farm-metrics"></a>Mesures de Web (batterie de serveurs) couramment utilisés

Vous pouvez également effectuer échelle automatique en fonction de mesures du serveur web courantes telles que la longueur de la file d’attente Http. Son nom métrique est **HttpQueueLength**.  La section suivante répertorie les mesures de la batterie de serveurs (applications Web) de serveur disponible.

### <a name="web-apps-metrics"></a>Mesures d’applications Web

Vous pouvez générer une liste des mesures de Web Apps à l’aide de la commande suivante dans PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Vous pouvez émettre une alerte ou mettre à l’échelle par ces mesures.

|Nom de mesure        |Unité|
|---                |---|
|CpuPercentage      |Pour cent|
|MemoryPercentage   |Pour cent|
|DiskQueueLength    |Nombre|
|HttpQueueLength    |Nombre|
|BytesReceived      |Octets|
|Octets envoyés          |Octets|


## <a name="commonly-used-storage-metrics"></a>Mesures de stockage couramment utilisées
Vous pouvez mettre à l’échelle par longueur de file d’attente de stockage, qui est le nombre de messages dans la file d’attente de stockage. Longueur de file d’attente de stockage est une mesure particulière et le seuil appliqué sera le nombre de messages par instance. Cela signifie que si les deux instances et si le seuil est défini sur 100, il mettra à l’échelle lorsque le nombre total de messages dans la file d’attente sont 200. Par exemple, 100 messages par instance.

Vous pouvez configurer dans le portail d’Azure dans la lame de **paramètres** . Pour les jeux d’échelle de machine virtuelle, vous pouvez mettre à jour le paramètre échelle automatique dans le modèle ARM pour utiliser *metricName* comme *ApproximateMessageCount* et passez l’ID de la file d’attente de stockage en tant que *metricResourceUri*.

Par exemple, avec un compte de stockage classique du metricTrigger du paramètre échelle automatique comprend :

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

Pour un compte de stockage de (non classique), le metricTrigger comprennent :

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Storage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## <a name="commonly-used-service-bus-metrics"></a>Mesures de Bus des services couramment utilisés

Vous pouvez mettre à l’échelle par longueur de file d’attente de Bus de Service, qui est le nombre de messages dans la file d’attente de Bus de Service. Longueur de file d’attente de Bus de service est une mesure particulière et que le seuil spécifié appliqué sera le nombre de messages par instance. Cela signifie que si les deux instances et si le seuil est défini sur 100, il mettra à l’échelle lorsque le nombre total de messages dans la file d’attente sont 200. Par exemple, 100 messages par instance.

Pour les jeux d’échelle de machine virtuelle, vous pouvez mettre à jour le paramètre échelle automatique dans le modèle ARM pour utiliser *metricName* comme *ApproximateMessageCount* et passez l’ID de la file d’attente de stockage en tant que *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Bus des services, le concept de groupe de ressource n’existe pas, mais le Gestionnaire de ressources Azure crée un groupe de ressources par défaut par région. Le groupe de ressources est généralement au format 'Par défaut - ServiceBus-[Région]'. Par exemple, 'Par défaut-ServiceBus-EastUS', 'Par défaut-ServiceBus-WestUS', 'Par défaut-ServiceBus-AustraliaEast' etc..
