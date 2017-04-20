<properties
   pageTitle="Collecter les journaux à l’aide de Linux Azure Diagnostics | Microsoft Azure"
   description="Cet article décrit comment configurer des Diagnostics d’Azure pour collecter des journaux à partir d’un cluster de Service Fabric Linux qui exécute dans Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Collecter les journaux à l’aide des Diagnostics d’Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Lorsque vous exécutez un cluster Service de Azure Fabric, il est judicieux de recueillir les journaux à partir de tous les nœuds dans un emplacement central. Avec les journaux dans un emplacement central facilite analyser et résoudre les problèmes, qu’ils soient dans le cluster lui-même, votre application ou vos services. Une pour transférer et collecter les journaux consiste à utiliser l’extension de Diagnostics d’Azure, qui transfère les journaux vers le stockage Azure. Vous pouvez lire les événements du stockage et les placer dans un produit tel que [Recherche élastique](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou d’une autre solution d’analyse de journal.

## <a name="log-sources-that-you-might-want-to-collect"></a>Sources du journal que vous souhaitez collecter
- **Les journaux de service de Fabric**: émis à partir de la plate-forme via [LTTng](http://lttng.org) et téléchargées vers votre compte de stockage. Les journaux peuvent être les événements opérationnels ou événements du runtime qui émet de la plate-forme. Ces journaux sont stockés dans l’emplacement du manifeste de cluster spécifie. (Pour obtenir les détails de compte de stockage, recherchez la balise **AzureTableWinFabETWQueryable** et recherchez **StoreConnectionString**.)
- **Événements d’application**: émis à partir du code de votre service. Vous pouvez utiliser n’importe quelle solution de journalisation qui écrit des fichiers de journal de type texte--par exemple, LTTng. Pour plus d’informations, consultez la documentation de LTTng de traçage de votre application.  


## <a name="deploy-the-diagnostics-extension"></a>Déploiement de l’extension de Diagnostics
La première étape de collecte des journaux est de déployer l’extension de Diagnostics sur chacun des ordinateurs virtuels dans le cluster Service Fabric. L’extension de diagnostic collecte des journaux sur chaque machine virtuelle et les transfère vers le compte de stockage que vous spécifiez. Les étapes varient selon que vous utilisez le portail Azure ou Azure le Gestionnaire de ressources.

Pour déployer l’extension de Diagnostics pour les ordinateurs virtuels du cluster dans le cadre de la création du cluster, la valeur **Diagnostics** **On**. Après avoir créé le cluster, vous ne pouvez pas modifier ce paramètre via le portail.

Ensuite, configurez Linux Azure Diagnostics (SCÉNARISTE) pour collecter les fichiers et les placer dans votre compte de stockage. Ce processus est expliqué en tant que scénario 3 (« vos propres fichiers journaux télécharger ») dans l’article [à l’aide de SCÉNARISTE pour surveiller et diagnostiquer les ordinateurs virtuels de Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md). Cette procédure obtient accès aux traces. Vous pouvez télécharger les traces à un visualiseur de votre choix.

Vous pouvez également déployer l’extension de Diagnostics à l’aide du Gestionnaire de ressources Azure. Le processus est identique pour Windows et Linux et est documenté pour les clusters Windows dans [la collecte des journaux de Diagnostics d’Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Vous pouvez également utiliser la Suite de gestion des opérations, comme décrit dans les [Opérations de gestion Suite journal Analytique avec Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Après avoir terminé cette configuration, l’agent SCÉNARISTE surveille les fichiers journaux spécifiés. Chaque fois qu’une nouvelle ligne est ajoutée au fichier, il crée une entrée de journal système qui est envoyée au stockage que vous avez spécifié.


## <a name="next-steps"></a>Étapes suivantes
Pour comprendre plus en détail les événements que vous devez examiner lors de la résolution des problèmes, consultez [documentation de LTTng](http://lttng.org/docs) et [à l’aide de SCÉNARISTE](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md).
