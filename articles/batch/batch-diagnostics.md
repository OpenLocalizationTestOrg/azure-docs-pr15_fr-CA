<properties
   pageTitle="L’enregistrement des diagnostics par lots Azure | Microsoft Azure"
   description="Enregistrer et analyser les événements du journal de diagnostic pour les ressources de compte Azure lot comme les pools et les tâches."
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="10/12/2016"
   ms.author="marsma"/>

# <a name="azure-batch-diagnostic-logging"></a>Enregistrement des diagnostics par lots Azure

À l’instar de nombreux services Azure, le service de traitement par lots émet des événements du journal pour certaines ressources pendant la durée de vie de la ressource. Vous pouvez activer les journaux de diagnostic Azure lot pour enregistrer des événements pour les ressources, telles que les pools et les tâches et ensuite utiliser les journaux pour évaluation de diagnostic et de suivi. Créent des événements tels que le pool, pool delete, début de la tâche, tâche terminée et d’autres sont inclus dans les journaux de diagnostic de lot.

>[AZURE.NOTE] Cet article décrit l’enregistrement des événements pour les ressources de compte lot eux-mêmes, pas des tâches et des données de sortie de la tâche. Pour plus d’informations sur le stockage des données de sortie des travaux et des tâches, consultez la [sortie de projet et la tâche de rendre persistant le lot Azure](batch-task-output.md).

## <a name="prerequisites"></a>Conditions préalables

* [Compte de lot Azure](batch-account-create-portal.md)

* [Compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account)

  Pour rendre persistantes les journaux de diagnostic de traitement par lots, vous devez créer un compte de stockage Azure où Azure stocke les journaux. Vous spécifiez ce compte de stockage lorsque vous [activez l’enregistrement des diagnostics](#enable-diagnostic-logging) pour votre compte de traitement par lots. Le compte de stockage que vous spécifiez lorsque vous activez la collection des journaux n’est pas identique à un compte de stockage visé à l’articles [des packages d’application](batch-application-packages.md) et de la [persistance de sortie de tâche](batch-task-output.md) .

  >[AZURE.WARNING] Vous êtes **facturés** pour les données stockées dans votre compte de stockage Azure. Cela inclut les journaux de diagnostic décrits dans cet article. Gardez à l’esprit lors de la conception de votre [stratégie de rétention du journal](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).

## <a name="enable-diagnostic-logging"></a>Activer l’enregistrement des Diagnostics

L’enregistrement des diagnostics n’est pas activé par défaut pour votre compte de traitement par lots. Vous devez activer explicitement l’enregistrement des diagnostics pour chaque compte de lot que vous souhaitez surveiller :

[Comment faire pour activer la collecte des journaux de Diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)

Nous vous conseillons de lire l’article complet de la [vue d’ensemble des journaux de Diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) pour comprendre non seulement comment faire pour activer la journalisation, mais les catégories de journal pris en charge par les divers services Azure. Par exemple, lot d’Azure prend actuellement en charge une seule catégorie de journaux : **Journaux de Service**.

## <a name="service-logs"></a>Journaux de service

Journaux de Service Azure lots contiennent des événements émis par le service de traitement par lots d’Azure pendant la durée de vie d’une ressource de traitement par lots comme un pool ou d’une tâche. Chaque événement émis par lot est enregistré dans le compte de stockage spécifié au format JSON. Par exemple, ceci est le corps d’un exemple de **pool de créer un événement**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Chaque corps de l’événement réside dans un fichier .json dans le compte de stockage Azure spécifié. Si vous souhaitez accéder directement aux journaux, vous pouvez souhaiter consulter le [schéma des journaux de diagnostics dans le compte de stockage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Événements du journal de service

Actuellement, le service de traitement par lots émet les événements du journal de Service suivants. Cette liste n’est peut-être pas exhaustive, dans la mesure où les événements supplémentaires ont été ajoutés dans la mesure où cet article a été mis à jour.

| **Événements du journal de service** |
| ------------------ |
| [Création de pool][pool_create] |
| [Début de suppression de pool][pool_delete_start] |
| [Suppression du pool complète][pool_delete_complete] |
| [Début de redimensionnement de pool][pool_resize_start] |
| [Redimensionnement du pool complète][pool_resize_complete] |
| [Début de la tâche][task_start] |
| [Tâche terminée][task_complete] |
| [Échec de la tâche][task_fail] |

## <a name="next-steps"></a>Étapes suivantes

Outre le stockage des événements du journal de diagnostic dans un compte de stockage Azure, vous pouvez également transmettre les événements du journal de Service de traitement par lots à un [Concentrateur d’événements Azure](../event-hubs/event-hubs-what-is-event-hubs.md)et les envoyer vers [Azure journal Analytique](../log-analytics/log-analytics-overview.md).

* [Flux de données des journaux de Diagnostic Azure à des concentrateurs d’événement](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)

  Flux d’événements de diagnostic de lot au service de pénétration des données hautement évolutive, concentrateurs de l’événement. Concentrateurs d’événement peuvent traiter des millions d’événements par seconde, que vous pouvez ensuite transformer et stocker à l’aide de n’importe quel fournisseur d’analytique en temps réel.

* [Analyser les journaux de diagnostic Azure à l’aide du journal Analytique](../log-analytics/log-analytics-azure-storage-json.md)

  Envoyer les journaux de diagnostics à Analytique journal où vous pouvez les analyser dans le portail de la Suite de gestion des opérations (OMS), ou les exporter pour analyse dans BI d’alimentation ou d’Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
