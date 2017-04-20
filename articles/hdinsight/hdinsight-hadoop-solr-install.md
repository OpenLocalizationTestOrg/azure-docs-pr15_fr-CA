<properties
    pageTitle="Action de Script permet d’installer le mode série sur LAN.r sur Hadoop cluster | Microsoft Azure"
    description="Apprenez à personnaliser HDInsight cluster avec le mode série sur LAN.r à l’aide des actions de Script."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Installer et utiliser le mode série sur LAN.r sur des clusters de HDInsight Hadoop

Découvrez comment personnaliser Windows en fonction HDInsight cluster avec le mode série sur LAN.r à l’aide des actions de Script et comment utiliser le mode série sur LAN.r pour rechercher des données. Pour plus d’informations sur l’utilisation du mode série sur LAN.r avec un cluster basé sur Linux, voir [installer et utiliser le mode série sur LAN.r sur des clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).
 
Vous pouvez installer le mode série sur LAN.r sur n’importe quel type de cluster (Hadoop, tempête, HBase, commandé) sur Azure HDInsight à l’aide des *Actions de Script*. Un exemple de script pour installer le mode série sur LAN.r sur un cluster HDInsight est disponible à partir d’un blob en lecture seule de stockage Azure à [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). 

L’exemple de script ne fonctionne qu’avec la version de cluster HDInsight 3.1. Pour plus d’informations sur les versions de cluster HDInsight, consultez [versions de cluster HDInsight](hdinsight-component-versioning.md).

L’exemple de script utilisé dans cette rubrique crée un cluster mode série sur LAN.r basé sur Windows avec une configuration spécifique. Si vous souhaitez configurer le cluster du mode série sur LAN.r avec différentes collections, milieu des fragments, schémas, duplications, etc., vous devez modifier le script et les fichiers binaires du mode série sur LAN.r en conséquence.

**Articles connexes**

- [Installer et utiliser le mode série sur LAN.r sur des clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Hadoop de créer des clusters dans HDInsight](hdinsight-provision-clusters.md): informations générales sur la création de clusters de HDInsight.
- [Personnaliser le cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: informations générales sur la personnalisation de clusters HDInsight à l’aide des actions de Script.
- [Action de Script développer des scripts pour HDInsight](hdinsight-hadoop-script-actions.md).


## <a name="what-is-solr"></a>Quel est le mode série sur LAN.r ?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Le mode série sur LAN.r Apache</a> est une plate-forme de recherche d’entreprise qui permet de puissante recherche de texte intégral sur des données. Tandis que Hadoop permet le stockage et la gestion d’importants volumes de données, le mode série sur LAN.r Apache fournit les fonctionnalités de recherche pour récupérer rapidement les données. 

## <a name="install-solr-using-portal"></a>Installer le mode série sur LAN.r à l’aide du portail

1. Démarrer la création d’un cluster à l’aide de l’option **Créer un personnalisé** , comme décrit sur [Hadoop de créer des clusters dans HDInsight](hdinsight-provision-clusters.md#portal).
2. Dans la page **Actions de Script** de l’Assistant, cliquez sur **Ajouter une action de script** pour fournir plus d’informations sur l’action du script, comme indiqué ci-dessous :

    ![Action de Script utilisé pour personnaliser un cluster] (./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Action de Script utilisé pour personnaliser un cluster")

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Spécifiez un nom pour l’action du script. Par exemple, <b>Installer mode série sur LAN.r</b>.</td></tr>
        <tr><td>URI de script</td>
            <td>Spécifiez l’identificateur URI (Uniform Resource) pour le script qui est appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Type de nœud</td>
            <td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous pouvez choisir <b>tous les noeuds</b>, les <b>noeuds de tête uniquement</b>ou <b>uniquement les nœuds de traitement</b>.
        <tr><td>Paramètres</td>
            <td>Spécifiez les paramètres, si nécessaire par le script. Le script pour installer le mode série sur LAN.r ne nécessite pas de paramètres, afin que vous laissez cette zone vide.</td></tr>
    </table>

    Vous pouvez ajouter plus d’une action de script pour installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour commencer à créer le cluster.


## <a name="use-solr"></a>Utiliser le mode série sur LAN.r

Vous devez démarrer avec une indexation de mode série sur LAN.r avec certains fichiers de données. Vous pouvez ensuite utiliser le mode série sur LAN.r pour exécuter des requêtes de recherche sur les données indexées. Effectuez les étapes suivantes pour utiliser le mode série sur LAN.r dans un cluster de HDInsight :

1. **Utilisation protocole RDP (Remote Desktop) à distance dans le cluster HDInsight avec mode série sur LAN.r installé**. À partir du portail Azure, activez le Bureau à distance pour le cluster que vous avez créé avec le mode série sur LAN.r installé et à distance puis, dans le cluster. Pour obtenir des instructions, voir [se connecter à des clusters HDInsight utilisant le protocole RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. **Mode série sur LAN.r un Index en téléchargeant les fichiers de données**. Lorsque vous indexez le mode série sur LAN.r, vous y placez les documents que vous devrez peut-être effectuer la recherche. Pour indexer le mode série sur LAN.r, utiliser le protocole RDP à distance dans le cluster, naviguer vers le bureau, ouvrez la ligne de commande Hadoop et accédez à **C:\apps\dist\solr-4.7.2\example\exampledocs**. Exécutez la commande suivante :

        java -jar post.jar solr.xml monitor.xml

    Vous verrez la sortie suivante sur la console :

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    L’utilitaire post.jar indexe le mode série sur LAN.r avec deux exemples de documents, **solr.xml** et **monitor.xml**. L’utilitaire post.jar et les exemples de documents sont disponibles avec l’installation du mode série sur LAN.r.

3. **Utilisez le tableau de bord du mode série sur LAN.r pour effectuer une recherche dans les documents indexés**. Dans la session RDP au cluster HDInsight, ouvrez Internet Explorer et lancer le tableau de bord au mode série sur LAN.r **http://headnodehost:8983/mode série sur LAN.r / #/**. Dans le volet gauche, dans le menu déroulant **Sélecteur de base** , sélectionnez **collection1**et dans ce cas, cliquez sur **la requête**. Par exemple, pour sélectionner et retourner tous les documents en mode série sur LAN.r, fournir les valeurs suivantes :

    * Dans la zone de texte **q** , entrez ** \*:**\*. Ceci renvoie tous les documents sont indexés en mode série sur LAN.r. Si vous souhaitez rechercher une chaîne spécifique dans les documents, vous pouvez entrer cette chaîne ici.
    
    * Dans la zone de texte **wt** , sélectionnez le format de sortie. Valeur par défaut est **json**. Cliquez sur **exécuter la requête**.

    ![Action de Script utilisé pour personnaliser un cluster] (./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Exécuter une requête sur un tableau de bord de mode série sur LAN.r")
    
    La sortie renvoie les deux documents que nous avons utilisé pour l’indexation du mode série sur LAN.r. La sortie ressemble à ceci :

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }


4. **Recommandée : sauvegardez les données indexées à partir du mode série sur LAN.r au stockage Azure Blob associé au cluster HDInsight**. Comme une bonne pratique, vous devez sauvegarder les données indexées mode série sur LAN.r des nœuds de cluster sur le stockage des objets Blob Azure. Effectuez les étapes suivantes pour effectuer cette opération :

    1. À partir de la session RDP, ouvrez Internet Explorer et pointez sur l’URL suivante :

            http://localhost:8983/solr/replication?command=backup

        Vous devez voir une réponse comme suit :

            <?xml version="1.0" encoding="UTF-8"?>
            <response>
              <lst name="responseHeader">
                <int name="status">0</int>
                <int name="QTime">9</int>
              </lst>
              <str name="status">OK</str>
            </response>

    2. Dans la session à distance, accédez à {SOLR_HOME}\{Collection} \data. Pour le cluster créé par l’exemple de script, ce doit être **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. À cet emplacement, vous devez voir un dossier de capture instantanée créé avec un nom semblable à * *snapshot.* horodatage***.

    3. Compressez le dossier de capture instantanée et de le télécharger pour le stockage des objets Blob Azure. À partir de la ligne de commande Hadoop, accédez à l’emplacement du dossier de capture instantanée à l’aide de la commande suivante :

              hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

        Cette commande copie la capture instantanée à /example/data/sous le conteneur dans le compte de stockage associé au cluster par défaut.

## <a name="install-solr-using-aure-powershell"></a>Installer le mode série sur LAN.r utilisant la Aure PowerShell

Voir [clusters HDInsight de personnaliser à l’aide des actions de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  L’exemple montre comment installer allumage à l’aide de PowerShell d’Azure. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Installer le mode série sur LAN.r à l’aide du Kit de développement .NET

Voir [clusters HDInsight de personnaliser à l’aide des actions de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). L’exemple montre comment installer allumage à l’aide du Kit de développement .NET. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).



## <a name="see-also"></a>Voir aussi

- [Installer et utiliser le mode série sur LAN.r sur des clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Hadoop de créer des clusters dans HDInsight](hdinsight-provision-clusters.md): informations générales sur la création de clusters de HDInsight.
- [Personnaliser le cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: informations générales sur la personnalisation de clusters HDInsight à l’aide des actions de Script.
- [Action de Script développer des scripts pour HDInsight](hdinsight-hadoop-script-actions.md).
- [Installer et utiliser l’allumage sur des clusters de HDInsight][hdinsight-install-spark]: exemple de Script Action sur l’installation d’allumage.
- [Installation de R sur les clusters de HDInsight][hdinsight-install-r]: exemple de Script Action sur l’installation de R.
- [Installation de Giraph sur des clusters de HDInsight](hdinsight-hadoop-giraph-install.md): exemple de Script Action sur l’installation de Giraph.


[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
