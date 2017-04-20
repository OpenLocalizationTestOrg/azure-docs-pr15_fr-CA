<properties
    pageTitle="Déplacer les données vers ou depuis le stockage Blob Azure à l’aide de connecteurs SSIS | Microsoft Azure"
    description="Déplacer les données vers ou depuis le stockage Blob Azure à l’aide de connecteurs SSIS."
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Déplacement des données vers ou depuis le stockage Blob Azure à l’aide de connecteurs SSIS

Le [SQL Server Integration Services Feature Pack pour Azure](https://msdn.microsoft.com/library/mt146770.aspx) fournit des composants pour se connecter à Azure, de transfert de données entre les données de processus stockées dans Azure Azure et les sources de données sur site.

Conseils sur les technologies utilisées pour déplacer des données sur et/ou à partir de stockage des objets Blob Azure sont liés ici :

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


Une fois que les clients ont déplacé les données en local dans le nuage, ils peuvent y accéder à partir de n’importe quel service Azure d’exploiter toute la puissance de la suite de technologies d’Azure. Il peut être utilisé, par exemple, dans la formation de Machine Azure ou sur un cluster HDInsight.

Il s’agit généralement d’être la première étape pour les procédures [SQL](machine-learning-data-science-process-sql-walkthrough.md) et [HDInsight](machine-learning-data-science-process-hive-walkthrough.md) .

Pour une présentation des scénarios canoniques SSIS permet d’accomplir les besoins de l’entreprise commune dans les scénarios d’intégration de données hybride, voir le blog de [faire plus avec SQL Server Integration Services Feature Pack pour Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) .

> [AZURE.NOTE] Pour une introduction complète à stockage blob Azure, consultez [Notions fondamentales de Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et [Service d’objet Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les tâches décrites dans cet article, vous devez disposer d’un abonnement Azure et un compte de stockage Azure. Vous devez connaître votre stockage Azure compte nom clé du compte et à charger ou télécharger des données.

- Pour configurer un **abonnement Azure**, consultez [un mois gratuite](https://azure.microsoft.com/pricing/free-trial/).

- Pour obtenir des instructions sur la création d’un **compte de stockage** et d’obtenir des informations de compte et clées, voir [comptes de stockage sur Azure](../storage/storage-create-storage-account.md).


Pour utiliser les **connecteurs SSIS**, vous devez télécharger :

- **SQL Server 2014 ou 2016 Standard (ou une version ultérieure)**: installation inclut SQL Server Integration Services.
- **Microsoft SQL Server 2014 ou 2016 Services Integration Feature Pack pour Azure**: ils peuvent être téléchargés, respectivement, à partir des pages [2014 Integration Services SQL Server](http://www.microsoft.com/download/details.aspx?id=47366) et les [Services d’intégration SQL Server 2016](https://www.microsoft.com/download/details.aspx?id=49492) .

> [AZURE.NOTE] SSIS est installé avec SQL Server, mais n’est pas inclus dans la version Express. Pour plus d’informations sur les applications sont incluses dans les différentes éditions de SQL Server, consultez [Éditions de SQL Server](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)

Pour les supports de formation sur SSIS, consultez [Mains de formation de SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Pour plus d’informations sur l’obtention d’accès et d’exécution à l’aide de SISS pour générer les voir extraction, transformation et chargement (ETL), packages, simple [SSIS didacticiel : création d’un Package ETL Simple](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Télécharger NYC Taxi dataset  
L’exemple décrit utiliser ici un groupe de données accessible au public--le dataset [NYC Taxi voyages](http://www.andresmh.com/nyctaxitrips/) . Le groupe de données est constitué d’environ 173 millions balades de taxi dans NYC dans l’année 2013. Il existe deux types de données : voyage décrit en détail les données de prix et les données. Comme il existe un fichier pour chaque mois, nous avons 24 fichiers dans l’ensemble, chacun d’eux est d’environ 2 Go sans compression.


## <a name="upload-data-to-azure-blob-storage"></a>Télécharger les données vers le stockage blob Azure
Pour déplacer des données à l’aide de SSIS du feature pack à partir de locaux de stockage des objets blob Azure, nous utilisons une instance de la [**Tâche de téléchargement de Blob Azure**](https://msdn.microsoft.com/library/mt146776.aspx), illustré ici :

![configurer-données-science-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


Les paramètres de la tâche sont décrits ici :


Champ|Description|
----------------------|----------------|
**AzureStorageConnection**|Spécifie un gestionnaire de connexions de stockage Azure existant ou crée un nouveau qui fait référence à un compte de stockage Azure qui pointe vers les fichiers blob hébergeant.|
**BlobContainer**|Spécifie le nom du conteneur blob qui contiennent les fichiers téléchargés en tant que BLOB.|
**BlobDirectory**|Spécifie le répertoire de blob dans lequel le fichier téléchargé est stocké comme un objet blob de bloc. Le répertoire de l’objet blob est une structure hiérarchique virtuelle. Si le blob existe déjà, il a remplacé des ia.|
**LocalDirectory**|Spécifie le répertoire local qui contient les fichiers à télécharger.|
**Nom de fichier**|Spécifie un nom de filtre pour sélectionner des fichiers avec le modèle de nom spécifié. Par exemple, MySheet\*.xls\* inclut les fichiers MySheet001.xls et MySheetABC.xlsx|
**TimeRangeFrom/TimeRangeTo**|Spécifie un filtre de plage de temps. Les fichiers modifiés après *TimeRangeFrom* et avant *TimeRangeTo* sont inclus.|


> [AZURE.NOTE] Les informations d’identification de **AzureStorageConnection** doivent être correctes et les **BlobContainer** doit exister avant que le transfert est tenté.

## <a name="download-data-from-azure-blob-storage"></a>Télécharger des données depuis le stockage blob Azure

Pour télécharger des données depuis le stockage blob Azure pour stockage avec SSIS sur site, utilisez une instance de la [Tâche de téléchargement de Blob Azure](https://msdn.microsoft.com/library/mt146779.aspx).

##<a name="more-advanced-ssis-azure-scenarios"></a>Scénarios plus avancés de SSIS-Azure
Nous Notez ici que le pack de déploiement de SSIS permet de flux plus complexes à gérer par les tâches d’empaquetage ensemble. Par exemple, les données blob a alimentation directement dans un cluster de HDInsight, dont la sortie peut être téléchargée sur un blob, puis vers un stockage sur site. SSIS peuvent exécuter des tâches de ruche et de porcins sur un cluster HDInsight à l’aide de connecteurs supplémentaires de SSIS :

- Pour exécuter un script de la ruche sur un cluster HDInsight d’Azure avec SSIS, utilisez la [Tâche de la ruche HDInsight Azure](https://msdn.microsoft.com/library/mt146771.aspx).
- Pour exécuter un script de porc sur un cluster HDInsight d’Azure avec SSIS, utilisez la [Tâche de porc Azure HDInsight](https://msdn.microsoft.com/library/mt146781.aspx).
