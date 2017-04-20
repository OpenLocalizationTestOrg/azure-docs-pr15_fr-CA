<properties
    pageTitle="Excel de se connecter à Hadoop avec alimentation requête | Microsoft Azure"
    description="Apprenez à tirer parti des composants business intelligence et d’utiliser des requêtes de puissance pour Excel pour accéder aux données stockées dans Hadoop sur HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>


#<a name="connect-excel-to-hadoop-by-using-power-query"></a>Utiliser Excel Hadoop à l’aide de requêtes de puissance

Une fonctionnalité clé de la solution de données volumineuses de Microsoft est l’intégration des composants de Microsoft business intelligence (BI) avec les clusters Hadoop dans Azure HDInsight. Un exemple principal de cette intégration est la capacité à Excel de se connecter sur le compte de stockage Azure qui contient les données associées à votre cluster Hadoop pour le complément Excel à l’aide de la puissance de Microsoft Query. Cet article vous explique comment configurer et utiliser la requête d’alimentation pour interroger les données associées à un cluster de Hadoop géré avec HDInsight.

> [AZURE.NOTE] Si les étapes décrites dans cet article peuvent être utilisés avec un cluster d’un HDInsight de fonctionnant sous Windows ou Linux, Windows est requise pour la station de travail cliente.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **HDInsight d’un cluster**. Pour configurer une, consultez [mise en route avec Azure HDInsight][hdinsight-get-started].
- **Une station de travail** qui exécute Windows 7, Windows Server 2008 R2 ou un système d’exploitation ultérieur.
- **Office 2013 Professionnel Plus, Office 365 ProPlus, Excel 2013 autonome ou Office 2010 Professionnel Plus**.


## <a name="install-power-query"></a>Installer Query de puissance

Requête d’alimentation peut servir à importer des données provenant de diverses sources dans Microsoft Excel, où il peut alimenter les Outils BI comme PowerPivot et Power View. En particulier, les requêtes d’alimentation peuvent importer des données qui a été sortie ou qui a été générée par un travail Hadoop s’exécutant sur un cluster HDInsight.

Télécharger la puissance de Microsoft Query pour Excel à partir du [Centre de téléchargement Microsoft] [ powerquery-download] et l’installer.

## <a name="import-hdinsight-data-into-excel"></a>Importer des données dans Excel HDInsight

Le complément de requête de la puissance d’Excel facilite l’importation de données à partir de votre cluster HDInsight dans Excel, où Outils BI tels que PowerPivot et cartographie de puissance peuvent servir à examiner, analyser et présentent les données.

**Pour importer des données à partir d’un cluster HDInsight**

1. Ouvrez Excel.

2. Créer un nouveau classeur vide.

3. Cliquez sur le menu de la **Requête de l’alimentation** , cliquez sur **à partir d’Azure**, puis cliquez sur **à partir de Microsoft Azure HDInsight**.

    ![HDI. PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

    **Remarque :** Si vous ne voyez pas le menu **Alimentation requête** , accédez au **fichier** > **Options de** > **Des compléments**et sélectionnez **compléments COM** à partir de la zone de liste déroulante **Gérer** au bas de la page. Cliquez sur le bouton **Aller...** et vérifiez que la zone de la requête d’alimentation pour le complément Excel a été vérifiée.

    **Remarque :** Requête d’alimentation vous permet également de vous permet d’importer des données à partir de très en cliquant sur **à partir d’autres Sources**.

3. **Nom du compte**, entrez le nom du compte de stockage Azure Blob associé à votre cluster et puis cliquez sur **OK**. Cela peut être le [compte de stockage par défaut](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) ou un compte de stockage.  Le format est *https://<StorageAccountName>.blob.core.windows.net/*.

4. **Clé de compte**, entrez la clé pour le compte de stockage Blob, puis cliquez sur **Enregistrer**. (Vous devez faire cela uniquement la première fois que vous accédez à cette banque d’informations).

5. Dans le volet de **navigation** sur la gauche de l’éditeur de requête, double-cliquez sur le nom de conteneur de stockage Blob. Par défaut, le nom du conteneur est le même nom que le nom du cluster.

6. Recherchez **HiveSampleData.txt** dans la colonne **nom** (le chemin d’accès du dossier est **... / la ruche/entrepôt/hivesampletable/**), puis cliquez sur **binaire** sur la gauche de HiveSampleData.txt. HiveSampleData.txt est fourni avec tout le cluster. Si vous le souhaitez, vous pouvez utiliser votre propre fichier.

    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Si vous le souhaitez, vous pouvez renommer les noms de colonne. Lorsque vous êtes prêt, cliquez sur **Fermer et la charger**.  Les chargement de données dans votre classeur :

    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment utiliser la requête d’alimentation pour extraire des données de HDInsight dans Excel. De même, vous pouvez récupérer des données de HDInsight dans la base de données de SQL Azure. Il est également possible de charger des données dans HDInsight. Pour plus d’informations, consultez les articles suivants :

* [Excel de se connecter à HDInsight avec le pilote ODBC de Microsoft ruche][hdinsight-ODBC]
* [Téléchargement des données vers HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
