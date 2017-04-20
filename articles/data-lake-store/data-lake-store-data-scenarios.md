<properties 
   pageTitle="Les scénarios de données impliquant la banque de données lac | Microsoft Azure" 
   description="Comprendre les différents scénarios et outils à l’aide de données peuvent ingérée, traitées, téléchargé et affiché un magasin de données lac" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>À l’aide du magasin de LAC de données Azure pour les besoins de données volumineuses

Il existe quatre phases clés dans le traitement de données volumineux :

* Absorber de grandes quantités de données dans un magasin de données en temps réel ou dans des lots
* Traitement des données
* Téléchargement des données
* Visualisation de données

Dans cet article, nous examinons ces étapes en ce qui concerne le lac Azure Data Store pour comprendre les options et les outils disponibles pour répondre à vos besoins de données volumineuses.

## <a name="ingest-data-into-data-lake-store"></a>Acquisition de données dans le magasin de données lac

Cette section met en évidence les différentes sources de données et les différents modes dans lesquels ces données peuvent être transférées dans un compte de banque de données lac.

![Acquisition des données dans le magasin de données lac] (./media/data-lake-store-data-scenarios/ingest-data.png "Acquisition des données dans le magasin de données lac")

### <a name="ad-hoc-data"></a>Données ad hoc

Ceci représente plus petits ensembles de données qui sont utilisées pour le prototypage, une application de données volumineuses. Il existe différentes façons de réception des données ad hoc selon la source de données.

| Source de données        | À l’aide d’acquisition                                                                        |
|--------------------|----------------------------------------------------------------------------------------|
| Ordinateur local     | <ul> <li>[Azure Portal](/data-lake-store-get-started-portal.md)</li> <li>[PowerShell Azure](data-lake-store-get-started-powershell.md)</li> <li>[Azure multiplates-CLI](data-lake-store-get-started-cli.md)</li> <li>[À l’aide des outils de données LAC pour Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Blob de stockage Azure | <ul> <li>[Usine de données Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[Outil de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp en cours d’exécution sur un cluster de HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

 
### <a name="streamed-data"></a>Flux de données

Il s’agit de données qui peuvent être générées par diverses sources telles que des applications, des périphériques, des capteurs, etc.. Ces données peuvent être transférées dans le magasin de données lac par les outils de la variété. Ces outils sont généralement capturer et traiter les données sur une base événement événement dans en temps réel, puis écrivez les événements par lots dans le magasin de données Lake afin qu’ils peuvent être ensuite traités. 

Voici les outils que vous pouvez utiliser :
 
* [Flux azure Analytique] (.. / sortie du lac données stream analytique) - ingérés concentrateurs d’événement des événements peuvent être écrits pour lac de données Azure à l’aide d’une sortie de magasin de LAC de données Azure.
* [Tempête de HDInsight azure](../hdinsight/hdinsight-storm-write-data-lake-store.md) - vous pouvez écrire des données directement au magasin de données lac du cluster tempête.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) – vous pouvez recevoir des événements de l’événement concentrateurs et puis les écrire dans le magasin de données lac l’aide du [SDK de .NET Data lac magasin](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Données relationnelles

Vous pouvez également de source de données à partir de bases de données relationnelles. Sur une période donnée, bases de données relationnelles recueillir d’importants volumes de données qui peuvent donner des idées clées si traité via un pipeline de données volumineuses. Vous pouvez utiliser les outils suivants pour déplacer ces données dans le magasin de données lac.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Usine de données Azure](../data-factory/data-factory-data-movement-activities.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Données de journal de serveur Web (téléchargement à l’aide d’applications personnalisées)

Ce type de groupe de données est indiquée spécifiquement car l’analyse des données de journal de serveur web est un cas d’usage courant pour les applications de données volumineuses et nécessite d’importants volumes de fichiers journaux à télécharger au magasin lac de données. Vous pouvez utiliser les outils suivants pour écrire vos propres scripts ou des applications à télécharger ce type de données.

* [Azure multiplates-CLI](data-lake-store-get-started-cli.md)
* [PowerShell Azure](data-lake-store-get-started-powershell.md)
* [Magasin de LAC de données Azure .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Usine de données Azure](../data-factory/data-factory-data-movement-activities.md)

Pour le transfert de données de journal de serveur web et également pour le téléchargement d’autres types de données (par exemple, les éléments social), il est une bonne approche pour écrire vos propres scripts/applications personnalisées car il vous offre la possibilité d’inclure les données de téléchargement de composant dans le cadre de votre application plus importante de données volumineuses. Dans certains cas, ce code peut prendre la forme d’un script ou un utilitaire de ligne de commande simple. Dans les autres cas, le code peut être utilisé pour intégrer le grand de traitement de données dans une application métier ou une solution.


### <a name="data-associated-with-azure-hdinsight-clusters"></a>Données associées aux clusters d’Azure HDInsight

La plupart des types de cluster HDInsight (Hadoop, HBase, tempête) prend en charge le magasin de données lac comme un référentiel de stockage de données. Les clusters HDInsight accéder aux données à partir d’Azure stockage BLOB (WASB). Pour améliorer les performances, vous pouvez copier les données à partir de WASB dans un compte de banque de données lac associé au cluster. Vous pouvez utiliser les outils suivants pour copier les données.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Service de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Usine de données Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### <a name="data-stored-in-on-premise-or-iaas-hadoop-clusters"></a>Les données stockées dans les versions sur site ou IaaS Hadoop clusters

Grandes quantités de données peuvent être stockées dans des clusters Hadoop existants, localement sur les ordinateurs à l’aide de très. Les clusters Hadoop peuvent être dans un déploiement sur site ou peut-être au sein d’un cluster IaaS sur Azure. Il peut y avoir des conditions requises pour copier ces données au magasin de LAC de données Azure pour une approche One-Off ou de manière périodique. Il existe diverses options que vous pouvez utiliser pour y parvenir. Vous trouverez ci-dessous une liste d’alternatives et les compromis associés.

| Approche  | Détails | Avantages   | Considérations relatives à la  |
|-----------|---------|--------------|-----------------|
| Usine de données Azure (CAD) permet de copier des données directement à partir de clusters d’Hadoop au magasin de LAC de données Azure | [Chargeur automatique de documents prend en charge très comme source de données](../data-factory/data-factory-hdfs-connector.md) | Chargeur automatique de documents prend en charge l’emploi de très et gestion de première classe de bout en bout et de contrôle | Requiert la passerelle de gestion des données d’être déployé sur site ou dans le cluster IaaS |
| Exporter des données à partir d’Hadoop sous forme de fichiers. Ensuite, copiez les fichiers magasin de LAC de données Azure à l’aide du mécanisme approprié.                                   | Vous pouvez copier des fichiers sur le lac Azure Data Store à l’aide de : <ul><li>[PowerShell Azure pour Windows du système d’exploitation](data-lake-store-get-started-powershell.md)</li><li>[Azure multiplates-CLI pour les systèmes d’exploitation non - Windows](data-lake-store-get-started-cli.md)</li><li>Application personnalisée à l’aide de n’importe quel SDK de magasin de données lac</li></ul> | Mise en route rapide. Faire des téléchargements personnalisés                                                   | Processus en plusieurs étapes qui implique plusieurs technologies. Gestion et surveillance seront élargira jusqu'à être un défi au fil du temps, étant donné la nature personnalisée des outils |
| Utilisez Distcp pour copier des données à partir d’Hadoop dans le stockage Azure. Copier les données de stockage Azure magasin lac de données à l’aide du mécanisme approprié. | Vous pouvez copier des données de stockage Azure dans à l’aide du magasin de données lac : <ul><li>[Usine de données Azure](../data-factory/data-factory-data-movement-activities.md)</li><li>[Outil de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp Apache en cours d’exécution sur des clusters de HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul>| Vous pouvez utiliser les outils open source. | Processus en plusieurs étapes qui implique plusieurs technologies |

### <a name="really-large-datasets"></a>Groupes de données très volumineux

Pour télécharger les groupes de données comprises dans plusieurs téraoctets, à l’aide des méthodes décrites ci-dessus peut parfois être lente et coûteuse. Dans ce cas, vous pouvez utiliser les options ci-dessous.

* **À l’aide de ExpressRoute Azure**. ExpressRoute Azure permet de créer des connexions entre l’infrastructure et de centres de données Azure dans vos locaux. Cela fournit une option fiable pour le transfert de grandes quantités de données. Pour plus d’informations, consultez la [documentation d’Azure ExpressRoute](../expressroute/expressroute-introduction.md).


* **Téléchargement de « Hors connexion » de données**. Si l’utilisation de ExpressRoute d’Azure n’est pas possible pour une raison quelconque, vous pouvez utiliser le [service d’importation/exportation d’Azure](../storage/storage-import-export-service.md) à livrer des disques durs avec vos données à un centre de données Azure. Vos données sont tout d’abord téléchargées vers Azure stockage BLOB. Vous pouvez ensuite utiliser le [Factory de données Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) ou [outil de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) pour copier les données de BLOB de stockage Azure au magasin de données lac.

    >[AZURE.NOTE] Lors de l’utilisation du service d’importation/exportation, la taille des fichiers sur les disques que vous livrez à un centre de données Azure ne doit pas être supérieure à 200 Go.


## <a name="process-data-stored-in-data-lake-store"></a>Traiter des données stockées dans le magasin de données lac

Une fois que les données sont disponibles dans le magasin de données lac vous pouvez exécuter l’analyse de ces données à l’aide d’applications de données pris en charge. Actuellement, vous pouvez utiliser HDInsight d’Azure et d’Azure données lac Analytique pour exécuter des tâches d’analyse des données sur les données stockées dans le magasin de données lac. 

![Analyse des données dans le magasin de données lac] (./media/data-lake-store-data-scenarios/analyze-data.png "Analyse des données dans le magasin de données lac")

Vous pouvez consulter les exemples suivants.

* [Créer un cluster de HDInsight avec le magasin de données lac comme espace de stockage](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Utiliser des données Azure lac Analytique avec le magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## <a name="download-data-from-data-lake-store"></a>Télécharger des données à partir du magasin de données lac

Vous pouvez également télécharger ou déplacer les données de magasin de LAC de données Azure pour les scénarios tels que :

* Déplacer les données vers d’autres référentiels d’interface avec vos pipelines de traitement des données existantes. Par exemple, vous pouvez souhaiter déplacer des données à partir du magasin de données lac local SQL Server ou SQL Azure.
* Transfert de données vers votre ordinateur local pour le traitement dans les environnements IDE lors de la création de prototypes d’application.

![Données de sortie à partir du magasin de données lac] (./media/data-lake-store-data-scenarios/egress-data.png "Données de sortie à partir du magasin de données lac")

Dans ce cas, vous pouvez utiliser une des options suivantes :

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Usine de données Azure](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Vous pouvez également utiliser les méthodes suivantes pour écrire votre propre script/application pour télécharger des données à partir de la banque de données lac.

* [Azure multiplates-CLI](data-lake-store-get-started-cli.md)
* [PowerShell Azure](data-lake-store-get-started-powershell.md)
* [Magasin de LAC de données Azure .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualiser les données dans le magasin de données lac

Vous pouvez utiliser une combinaison de services pour créer des représentations visuelles de données stockées dans le magasin de données lac.

![Visualiser les données dans le magasin de données lac] (./media/data-lake-store-data-scenarios/visualize-data.png "Visualiser les données dans le magasin de données lac")

* Vous pouvez démarrer à l’aide de la [Fabrique de données Azure pour déplacer les données de magasin de données LAC pour l’entrepôt de données SQL Azure](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)
* Ensuite, vous pouvez [intégrer BI de puissance avec l’entrepôt de données SQL Azure](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) pour créer une représentation visuelle des données.
