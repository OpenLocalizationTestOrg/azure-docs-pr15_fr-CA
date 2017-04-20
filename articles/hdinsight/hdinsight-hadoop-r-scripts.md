<properties
    pageTitle="Utilisation de R dans HDInsight pour personnaliser les clusters | Microsoft Azure"
    description="Pour savoir comment installer R à l’aide de Script Action et utilisez R sur les clusters de HDInsight."
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
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Installer et utiliser R sur les clusters de HDInsight Hadoop

Découvrez comment personnaliser Windows basée HDInsight cluster avec R à l’aide des actions de Script, et comment utiliser R HDInsight clusters. Le [niveau de prime](https://azure.microsoft.com/pricing/details/hdinsight/) pour HDInsight inclut un serveur R dans le cadre de votre cluster de HDInsight. Cela permet aux scripts R permet d’exécuter des traitements distribués MapReduce et à allumage commandé. Pour plus d’informations, consultez [mise en route à l’aide du serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md). Pour plus d’informations sur l’utilisation de R avec un cluster Linux, voir [installer et utiliser R sur les clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md).
 
Vous pouvez installer R sur n’importe quel type de cluster (Hadoop, tempête, HBase, allumage) sur Azure HDInsight à l’aide des *Actions de Script*. Un exemple de script d’installation R sur un cluster HDInsight est disponible à partir d’un blob en lecture seule de stockage Azure à [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1). 

**Articles connexes**

- [Installer et utiliser R sur les clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Hadoop de créer des clusters dans HDInsight](hdinsight-provision-clusters.md): informations générales sur la création de clusters de HDInsight
- [Personnaliser le cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: informations générales sur la personnalisation de clusters HDInsight à l’aide des actions de Script
- [Développer des scripts d’Action de Script pour HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>Qu’est-R ?

Le <a href="http://www.r-project.org/" target="_blank">Projet de R pour le calcul statistique</a> est un open source de langage et un environnement pour le calcul statistique. R fournit des centaines de génération dans les fonctions statistiques et son propre langage de programmation qui combine des aspects de la programmation fonctionnelle et orientée objet. Il fournit également des fonctionnalités graphiques complètes. R est l’environnement de programmation préféré pour plus professionnel statisticiens et scientifiques dans un grand nombre de champs.

R est compatible avec le stockage des Blob Azure (WASB) afin que les données stockées à cet emplacement peuvent être traitées à l’aide de R sur HDInsight.  

## <a name="install-r"></a>Installation de R

Un [exemple de script](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) pour installer R sur un cluster HDInsight est disponible à partir d’un blob en lecture seule dans le stockage Azure. Cette section fournit des instructions sur l’utilisation de l’exemple de script lors de la création du cluster en utilisant le portail Azure.

> [AZURE.NOTE] L’exemple de script a été introduite avec la version de cluster HDInsight 3.1. Pour plus d’informations sur les versions de cluster HDInsight, consultez [versions de cluster HDInsight](hdinsight-component-versioning.md).

1. Lorsque vous créez un cluster de HDInsight à partir du portail, cliquez sur **Configuration facultative**, puis cliquez sur **Actions de Script**.
2. Dans la page **Actions de Script** , entrez les valeurs suivantes :

    ![Action de Script utilisé pour personnaliser un cluster] (./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Action de Script utilisé pour personnaliser un cluster")

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Spécifiez un nom pour l’action de script, par exemple, <b>Installer de R</b>.</td></tr>
        <tr><td>URI de script</td>
            <td>Spécifiez l’URI du script qui est appelée pour personnaliser le cluster, par exemple, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Type de nœud</td>
            <td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous pouvez choisir que <b>Tous les nœuds</b>, <b>uniquement les nœuds de tête</b>ou de <b>nœuds de traitement</b> .
        <tr><td>Paramètres</td>
            <td>Spécifiez les paramètres, si nécessaire par le script. Toutefois, le script d’installation R ne nécessite pas de paramètres, afin que vous laissez cette zone vide.</td></tr>
    </table>

    Vous pouvez ajouter plus d’une action de script pour installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la case à cocher pour démarrer crating du cluster.

Vous pouvez également utiliser le script d’installation R sur HDInsight à l’aide de PowerShell d’Azure ou le Kit de développement .NET HDInsight. Instructions pour ces procédures sont fournies plus loin dans cet article.

## <a name="run-r-scripts"></a>Exécuter des scripts de R
Cette section décrit comment exécuter un script R sur le cluster Hadoop avec HDInsight.

1. **Établir une connexion Bureau à distance pour le cluster**: à partir du portail, activer le Bureau à distance pour le cluster que vous avez créé avec R installé et connectez-vous au cluster. Pour obtenir des instructions, voir [se connecter à des clusters HDInsight utilisant le protocole RDP](hdinsight-administer-use-management-portal.md#rdp).

2. **Ouvrez la console R**: installation de R la place un lien vers la console R sur le bureau du nœud principal. Cliquez dessus pour ouvrir la console R.

3. **Exécutez le script R**: script de la R peut ainsi être exécuté directement à partir de la console de R, en les collant, en sélectionnant et en appuyant sur ENTRÉE. Voici un exemple simple de script qui génère les numéros de 1 à 100 et puis les multiplie par 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

Les deux premières lignes appellent les bibliothèques RHadoop qui sont installés avec R. La dernière ligne imprime les résultats dans la console. La sortie doit ressembler à ceci :

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Installation de R à l’aide de la Aure PowerShell

Voir [clusters HDInsight de personnaliser à l’aide des actions de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  L’exemple montre comment installer allumage à l’aide de PowerShell d’Azure. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Installation de R à l’aide du Kit de développement .NET

Voir [clusters HDInsight de personnaliser à l’aide des actions de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). L’exemple montre comment installer allumage à l’aide du Kit de développement .NET. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).


## <a name="see-also"></a>Voir aussi

- [Installer et utiliser R sur les clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Hadoop de créer des clusters dans HDInsight](hdinsight-provision-clusters.md): informations générales sur la création de clusters de HDInsight
- [Personnaliser le cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: informations générales sur la personnalisation de clusters HDInsight à l’aide des actions de Script
- [Développer des scripts d’Action de Script pour HDInsight](hdinsight-hadoop-script-actions.md)
- [Installer et utiliser l’allumage sur des clusters de HDInsight][hdinsight-install-spark]: exemple de Script Action sur l’installation d’allumage
- [Installation de Giraph sur des clusters de HDInsight](hdinsight-hadoop-giraph-install.md): exemple de Script Action sur l’installation de Giraph
- [Mode série sur LAN.r installer sur des clusters de HDInsight](hdinsight-hadoop-solr-install-linux.md): exemple de Script Action sur l’installation du mode série sur LAN.r.

[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md
