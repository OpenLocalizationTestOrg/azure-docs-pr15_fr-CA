<properties
    pageTitle="Installer et utiliser le Giraph sur des clusters Hadoop dans HDInsight | Microsoft Azure"
    description="Découvrez comment personnaliser le cluster HDInsight avec Giraph et comment utiliser Giraph."
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

# <a name="install-and-use-giraph-in-hdinsight"></a>Installer et utiliser Giraph dans HDInsight


Apprenez comment personnaliser Windows en fonction HDInsight cluster avec Giraph à l’aide de Script Action et l’utilisation de Giraph pour traiter des graphiques à grande échelle. Pour plus d’informations sur l’utilisation de Giraph avec un cluster Linux, voir [Installation de Giraph sur les clusters de HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).
 
Vous pouvez installer Giraph sur n’importe quel type de cluster (Hadoop, tempête, HBase, commandé) sur Azure HDInsight en utilisant *l’Action de Script*. Un exemple de script pour installer le Giraph sur un cluster HDInsight est disponible à partir d’un blob en lecture seule de stockage Azure à [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). L’exemple de script ne fonctionne qu’avec la version de cluster HDInsight 3.1. Pour plus d’informations sur les versions de cluster HDInsight, consultez [versions de cluster HDInsight](hdinsight-component-versioning.md).

**Articles connexes**

- [Installer Giraph sur les clusters de HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Hadoop de créer des clusters dans HDInsight](hdinsight-provision-clusters.md): informations générales sur la création de clusters de HDInsight.
- [Personnaliser le cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: informations générales sur la personnalisation de clusters HDInsight à l’aide des actions de Script.
- [Action de Script développer des scripts pour HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Quel est Giraph ?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> , vous pouvez exécuter le traitement à l’aide d’Hadoop de graphique et peut être utilisé avec HDInsight d’Azure. Graphiques de modéliser les relations entre les objets, tels que les connexions entre routeurs sur un réseau de grande taille tels que l’Internet ou des relations entre les personnes sur les réseaux sociaux (parfois appelés un graphique social). Traitement de graphique permet de raisonner sur les relations entre les objets dans un graphique, tel que :

- Identification des amis potentiels en fonction de vos relations actuelles.
- Qui identifie l’itinéraire le plus court entre les deux ordinateurs dans un réseau.
- Calculer le rang de page des pages Web.


## <a name="install-giraph-using-portal"></a>Installer à l’aide du portail de Giraph

1. Démarrer la création d’un cluster à l’aide de l’option **Créer un personnalisé** , comme décrit sur [Hadoop de créer des clusters dans HDInsight](hdinsight-provision-clusters.md#portal).
2. Dans la page **Actions de Script** de l’Assistant, cliquez sur **Ajouter une action de script** pour fournir plus d’informations sur l’action du script, comme indiqué ci-dessous :

    ![Action de Script utilisé pour personnaliser un cluster] (./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Action de Script utilisé pour personnaliser un cluster")

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Spécifiez un nom pour l’action du script. Par exemple, <b>Installer le Giraph</b>.</td></tr>
        <tr><td>URI de script</td>
            <td>Spécifiez l’identificateur URI (Uniform Resource) pour le script qui est appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Type de nœud</td>
            <td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous pouvez choisir <b>tous les noeuds</b>, les <b>noeuds de tête uniquement</b>ou <b>uniquement les nœuds de traitement</b>.
        <tr><td>Paramètres</td>
            <td>Spécifiez les paramètres, si nécessaire par le script. Le script d’installation Giraph ne nécessite pas de paramètres, afin que vous laissez cette zone vide.</td></tr>
    </table>

    Vous pouvez ajouter plus d’une action de script pour installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour commencer à créer le cluster.

## <a name="use-giraph"></a>Utiliser Giraph

L’exemple SimpleShortestPathsComputation nous permet d’illustrer l’implémentation de <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> de base permettant de trouver le chemin le plus court entre les objets dans un graphique. Utilisez les étapes suivantes pour télécharger les exemples de données et le fichier jar d’exemple, d’exécuter une tâche à l’aide de l’exemple SimpleShortestPathsComputation et afficher les résultats.

1. Télécharger un exemple de fichier de données pour le stockage des objets Blob Azure. Sur votre station de travail, créez un nouveau fichier nommé **tiny_graph.txt**. Il doit contenir les lignes suivantes :

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Télécharger le fichier tiny_graph.txt sur le stockage principal pour votre cluster HDInsight. Pour obtenir des instructions sur la façon de télécharger des données, voir [télécharger les données pour les travaux d’Hadoop dans HDInsight](hdinsight-upload-data.md).

    Ces données décrivent une relation entre des objets dans un graphique dirigé, en utilisant le format [source\_id, la source\_valeur, [[dest\_id], [bord\_valeur],...]]. Chaque ligne représente une relation entre un **source\_id** objet et un ou plusieurs **et\_id** objets. Le **bord\_valeur** (ou poids) peut être considérée comme la puissance et la distance de la connexion entre **source_id** et **et\_id**.

    Dessiné, et à l’aide de la valeur (ou un poids) que la distance entre les objets, les données ci-dessus peuvent ressembler à ceci :

    ![tiny_graph.txt, représentés par des cercles avec lignes de distance variable entre](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. Exécutez l’exemple SimpleShortestPathsComputation. Utiliser les applets de commande PowerShell de Azure suivant pour exécuter l’exemple en utilisant le fichier tiny_graph.txt comme entrée. 

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

        $clusterName = "clustername"
        # Giraph examples jar
        $jarFile = "wasbs:///example/jars/giraph-examples.jar"
        # Arguments for this job
        $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                        "-ca", "mapred.job.tracker=headnodehost:9010",
                        "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                        "-vip", "wasbs:///example/data/tiny_graph.txt",
                        "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                        "-op",  "wasbs:///example/output/shortestpaths",
                        "-w", "2"
        # Create the definition
        $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
          -JarFile $jarFile
          -ClassName "org.apache.giraph.GiraphRunner"
          -Arguments $jobArguments

        # Run the job, write output to the Azure PowerShell window
        $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $job
        Write-Host "STDERR"
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

    Dans l’exemple ci-dessus, remplacez **Nom_Cluster** avec le nom de votre cluster HDInsight avec Giraph installé.

5. Permet d’afficher les résultats. Une fois la tâche terminée, les résultats seront stockés dans deux fichiers de sortie dans le __wasbs : / / / exemple/out/shotestpaths__ dossier. Les fichiers sont appelés __partie-m-00001__ et __partie-m-00002__. Procédez comme suit pour télécharger et afficher les résultats :

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name
        $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the Storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

    Cela va créer la structure de répertoire __exemple/sortie/shortestpaths__ dans le répertoire en cours sur votre station de travail et télécharger les fichiers de sortie de deux à cet emplacement.

    Pour afficher le contenu des fichiers, utilisez l’applet de commande __Cat__ :

        Cat example/output/shortestpaths/part*

    La sortie doit ressembler à ce qui suit :


        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    La SimpleShortestPathComputation exemple est codé en dur pour commencer avec objet ID 1 et trouver le chemin le plus court à d’autres objets. Afin que la sortie doit être lu en tant que `destination_id distance`, où la distance est la valeur (ou poids) des bords a transité entre objet ID 1 et de l’ID cible.

    Cette visualisation, vous pouvez vérifier les résultats en déplacements les chemins les plus courts entre ID 1 et tous les autres objets. Notez que le chemin d’accès le plus court entre l’ID 1 et 4 de l’ID est 5. Il s’agit de la distance totale entre <span style="color:orange">ID 1 et 3</span>, puis <span style="color:red">ID 3 et 4</span>.

    ![Dessin d’objets sous forme de cercles avec les chemins les plus courts entre](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Installer des Giraph à l’aide de la Aure PowerShell

Voir [clusters HDInsight de personnaliser à l’aide des actions de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  L’exemple montre comment installer allumage à l’aide de PowerShell d’Azure. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Installer des Giraph à l’aide du Kit de développement .NET

Voir [clusters HDInsight de personnaliser à l’aide des actions de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). L’exemple montre comment installer allumage à l’aide du Kit de développement .NET. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).


## <a name="see-also"></a>Voir aussi

- [Installer Giraph sur les clusters de HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Hadoop de créer des clusters dans HDInsight](hdinsight-provision-clusters.md): informations générales sur la création de clusters de HDInsight.
- [Personnaliser le cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: informations générales sur la personnalisation de clusters HDInsight à l’aide des actions de Script.
- [Action de Script développer des scripts pour HDInsight](hdinsight-hadoop-script-actions.md).
- [Installer et utiliser l’allumage sur des clusters de HDInsight][hdinsight-install-spark]: exemple de Script Action sur l’installation d’allumage.
- [Installation de R sur les clusters de HDInsight][hdinsight-install-r]: exemple de Script Action sur l’installation de R.
- [Mode série sur LAN.r installer sur des clusters de HDInsight](hdinsight-hadoop-solr-install.md): exemple de Script Action sur l’installation du mode série sur LAN.r.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
