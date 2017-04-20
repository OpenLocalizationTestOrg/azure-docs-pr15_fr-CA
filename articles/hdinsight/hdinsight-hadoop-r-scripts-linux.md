<properties
    pageTitle="Installation R sur basé sur Linux de HDInsight | Microsoft Azure"
    description="Découvrez comment installer et utiliser R pour personnaliser les clusters basés sur Linux de Hadoop."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Installer et utiliser R sur les clusters de HDInsight Hadoop

Vous pouvez installer R sur n’importe quel type de cluster dans Hadoop sur HDInsight à l’aide de personnalisation de cluster **Action de Script** . Ainsi, les scientifiques de données de configuration et aux analystes de R permet de déployer l’infrastructure de programmation puissant MapReduce/fils traiter de grandes quantités de données sur des clusters Hadoop qui sont déployés dans les HDInsight.

> [AZURE.IMPORTANT] Le [niveau de prime](https://azure.microsoft.com/pricing/details/hdinsight/) pour HDInsight inclut un serveur R dans le cadre de votre cluster de HDInsight. Cela permet aux scripts R permet d’exécuter des traitements distribués MapReduce et à allumage commandé. Pour plus d’informations, consultez [mise en route à l’aide du serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md). 


## <a name="what-is-r"></a>Qu’est-R ?

Le <a href="http://www.r-project.org/" target="_blank">Projet de R pour le calcul statistique</a> est un open source de langage et un environnement pour le calcul statistique. R fournit des centaines de génération dans les fonctions statistiques et son propre langage de programmation qui combine des aspects de la programmation fonctionnelle et orientée objet. Il fournit également des fonctionnalités graphiques complètes. R est l’environnement de programmation préféré pour plus professionnel statisticiens et scientifiques dans un grand nombre de champs.

Scripts de R peuvent être exécutés sur des clusters Hadoop dans HDInsight qui ont été personnalisées à l’aide de Script Action lors de la création pour installer l’environnement R. R est compatible avec le stockage des Blob Azure (WASB) afin que les données stockées à cet emplacement peuvent être traitées à l’aide de R sur HDInsight.

## <a name="what-the-script-does"></a>Ce que fait le script

L’action du script d’installation R sur votre cluster HDInsight installe les packages Ubuntu suivants, qui permettent une installation R de base :

* [r-base](http://packages.ubuntu.com/precise/r-base): package de Base GNU R
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): les packages auxiliaire GNU R

Les packages RHadoop suivants sont également installés, qui fournissent l’intégration avec MapReduce et très :

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): permet aux développeurs de R à utiliser Hadoop MapReduce
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): permet aux développeurs de R à utiliser Hadoop HDFS (WASB de HDInsight)

En outre, les packages R suivants sont installés :

| Package de R | Ce qu’il fournit. |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | Un faible niveau de R à interface de Java. |
| [RCPP](https://cran.r-project.org/web/packages/Rcpp/index.html) | Intégration de R et de C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | R de sérialiser/désérialiser les objets JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Fonctions pour les opérations au niveau du bit sur les vecteurs de nombre entier. |
| [Digest](https://cran.r-project.org/web/packages/digest/index.html) | Créer des résumés de hachage cryptographique d’objets de R. |
| [fonctionnel](https://cran.r-project.org/web/packages/functional/index.html) | Curry, de composition et d’autres fonctions d’ordre supérieur |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Restructurer et agréger des données de manière flexible. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Wrappers simples et cohérentes pour les opérations de chaînes courantes. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Outils de fractionnement, l’application et combiner des données. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Outils pour le déplacement des statistiques de la fenêtre, GIF, Base64, ROC AUC, etc.. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Rapprocher la correspondance de chaînes et de Distance des fonctions String. |

## <a name="install-r-using-script-actions"></a>Installation de R à l’aide des Actions de Script

L’action de script suivant est utilisée pour installer R sur un cluster HDInsight. https://hdiconfigactions.BLOB.Core.Windows.NET/linuxrconfigactionv01/r-installer-v01.sh
    
Cette section fournit des instructions sur la façon d’utiliser le script lors de la création d’un nouveau cluster en utilisant le portail Azure. 

> [AZURE.NOTE] PowerShell Azure, la CLI d’Azure, le Kit de développement .NET HDInsight ou modèles d’Azure le Gestionnaire de ressources peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer des actions de script pour les clusters en cours d’exécution. Pour plus d’informations, voir [clusters de personnaliser la HDInsight avec les Actions de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrer la mise en service d’un cluster à l’aide de la procédure dans [les clusters basés sur Linux de fourniture de HDInsight](hdinsight-hadoop-provision-linux-clusters.md#portal), mais n’effectuez pas de mise en service.

2. Sélectionnez les **Actions de Script**sur la lame de **Configuration facultatives** et fournir les informations ci-dessous :

    * __Nom__: entrez un nom convivial pour l’action du script.
    * __URI du SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    * __Tête__: Activez cette case à cocher
    * __Travailleur__: Activez cette case à cocher
    * __SOIGNEUR__: Activez cette option pour installer sur le nœud soigneur.
    * __Paramètres__: laissez ce champ vide

3. Au bas des **Actions de Script**, utilisez **le bouton** enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** en bas de la lame de **Configuration facultative** pour enregistrer les informations de configuration facultatives.

4. Continuer la mise en service du cluster, comme décrit dans [les clusters basés sur Linux de fourniture de HDInsight](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="run-r-scripts"></a>Exécuter des scripts de R

Une fois que le cluster a terminé la mise en service, procédez comme suit pour utiliser R pour effectuer une opération MapReduce sur le cluster.

1. Connectez-vous au cluster de HDInsight à l’aide de SSH :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les rubriques suivantes :

    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. À partir de la `username@hn0-CLUSTERNAME:~$` invite, entrez la commande suivante pour démarrer une session interactive de R :

        R

3. Entrer dans le programme de R suivant. Cela génère les numéros de 1 à 100 et puis les multiplie par 2.

        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

    La première ligne appelle la rmr2 de bibliothèque RHadoop, qui est utilisé pour les opérations de MapReduce.

    La deuxième ligne génère des valeurs de 1 à 100, puis les stocke sur le système de fichier Hadoop à l’aide `to.dfs`.

    La troisième ligne crée un processus MapReduce à l’aide de la fonctionnalité fournie par rmr2 et commence le traitement. Vous devriez voir plusieurs lignes à défiler que le traitement commence.

4. Ensuite, utilisez ce qui suit pour afficher le chemin d’accès temporaire qui a été stockée la sortie MapReduce à :

        print(calc())

    Ce doit être semblable à `/tmp/file5f615d870ad2`. Pour afficher le résultat réel, procédez comme suit :

        print(from.dfs(calc))

    La sortie doit ressembler à ceci :

        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. Pour quitter R, entrez les informations suivantes :

        q()


## <a name="next-steps"></a>Étapes suivantes

- [L’installation et l’utilisation des clusters de teinte sur HDInsight](hdinsight-hadoop-hue-linux.md). La nuance est une interface utilisateur qui permet de facilement créer, exécuter et enregistrer des travaux de porc et de la ruche, ainsi que de la parcourir le stockage par défaut pour votre HDInsight de cluster web.

- [Installation de Giraph sur des clusters de HDInsight](hdinsight-hadoop-giraph-install.md). Personnalisation de cluster permet d’installer Giraph sur les clusters de HDInsight Hadoop. Giraph vous permet d’effectuer le traitement graphique à l’aide d’Hadoop, et il peut être utilisé avec HDInsight d’Azure.

- [Mode série sur LAN.r installer sur des clusters de HDInsight](hdinsight-hadoop-solr-install.md). Personnalisation de cluster permet d’installer le mode série sur LAN.r sur HDInsight Hadoop clusters. Mode série sur LAN.r vous permet d’effectuer des opérations de recherche puissants sur les données stockées.

- [Installer la teinte sur des clusters de HDInsight](hdinsight-hadoop-hue-linux.md). Personnalisation de cluster permet d’installer la teinte sur des clusters de HDInsight Hadoop. La nuance est un ensemble d’applications Web permettant d’interagir avec un cluster Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
