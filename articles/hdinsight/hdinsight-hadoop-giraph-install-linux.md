<properties
    pageTitle="Installer et utiliser les Giraph sur basé sur Linux HDInsight (Hadoop) | Microsoft Azure"
    description="Découvrez comment installer Giraph sur les clusters basés sur Linux de HDInsight à l’aide des Actions de Script. Actions de script vous autorisent à personnaliser le cluster lors de la création, par modification de la configuration de cluster ou l’installation d’utilitaires et services."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="larryfr"/>

# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installer Giraph sur les clusters de HDInsight Hadoop et utiliser Giraph pour traiter les graphiques à grande échelle

Vous pouvez installer Giraph sur n’importe quel type de cluster dans Hadoop sur Azure HDInsight en utilisant **l’Action de Script** pour personnaliser un cluster.

Dans cette rubrique, vous apprendrez comment installer des Giraph à l’aide des actions de Script. Une fois que vous avez installé Giraph, vous apprendrez également comment utiliser Giraph pour les applications plus classiques, qui consiste à traiter les graphiques à grande échelle.

> [AZURE.NOTE] Les informations contenues dans cet article sont spécifiques aux clusters de HDInsight de fonctionnant sous Linux. Pour plus d’informations sur l’utilisation des clusters basés sur Windows, reportez-vous à la section [Installation de Giraph sur les clusters d’Hadoop de HDInsight (Windows)](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>Quel est Giraph ?

[Apache Giraph](http://giraph.apache.org/) , vous pouvez exécuter le traitement à l’aide d’Hadoop de graphique et peut être utilisé avec HDInsight d’Azure. Graphiques de modéliser les relations entre les objets, tels que les connexions entre routeurs sur un réseau de grande taille tels que l’Internet ou des relations entre les personnes sur les réseaux sociaux (parfois appelés un graphique social). Traitement de graphique permet de raisonner sur les relations entre les objets dans un graphique, tel que :

- Identification des amis potentiels en fonction de vos relations actuelles.
- Qui identifie l’itinéraire le plus court entre les deux ordinateurs dans un réseau.
- Calculer le rang de page des pages Web.

> [AZURE.WARNING] Les composants fournis avec le cluster HDInsight sont entièrement gérés et Support de Microsoft vous permet d’isoler et de résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés, tels que Giraph, assistance commercialement raisonnables pour vous aider à résoudre le problème. Cela peut provoquer de résolution du problème ni à vous demander d’engager les canaux disponibles pour les technologies open source où se trouve une grande expertise pour cette technologie. Par exemple, de nombreux sites de la Communauté qui peuvent être utilisés, comme : [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Également les projets Apache ont des sites de projets sur [http://apache.org](http://apache.org), par exemple : [Hadoop](http://hadoop.apache.org/).

##<a name="what-the-script-does"></a>Ce que fait le script

Ce script effectue les actions suivantes :

* Installe Giraph à`/usr/hdp/current/giraph`
* Copie le `giraph-examples.jar` fichier pour le stockage par défaut (WASB) pour votre cluster :`/example/jars/giraph-examples.jar`

## <a name="install"></a>Installer des Giraph à l’aide des Actions de Script

Un exemple de script pour installer le Giraph sur un cluster HDInsight est disponible à l’emplacement suivant.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Cette section fournit des instructions sur l’utilisation de l’exemple de script lors de la création du cluster à l’aide du portail Azure. 

> [AZURE.NOTE] PowerShell Azure, la CLI d’Azure, le Kit de développement .NET HDInsight ou modèles d’Azure le Gestionnaire de ressources peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer des actions de script pour les clusters en cours d’exécution. Pour plus d’informations, voir [clusters de personnaliser la HDInsight avec les Actions de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrer la création d’un cluster à l’aide de la procédure dans [les clusters basés sur Linux de créer de HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md), mais n’effectuez pas de création.

2. Sélectionnez les **Actions de Script**sur la lame de **Configuration facultatives** et fournir les informations ci-dessous :

    * __Nom__: entrez un nom convivial pour l’action du script.
    * __URI du SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    * __Tête__: Activez cette case à cocher
    * __Travailleur__: laissez désactivé
    * __SOIGNEUR__: laissez désactivé
    * __Paramètres__: laissez ce champ vide

3. Au bas des **Actions de Script**, utilisez **le bouton** enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** en bas de la lame de **Configuration facultative** pour enregistrer les informations de configuration facultatives.

4. Continuer la création du cluster, comme décrit dans [les clusters basés sur Linux de créer un HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Utilisation de Giraph dans HDInsight

Une fois que le cluster a terminé la création, procédez comme suit pour exécuter l’exemple SimpleShortestPathsComputation inclus dans Giraph. Il implémente la mise en oeuvre de <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> la base permettant de trouver le chemin le plus court entre les objets dans un graphique.

1. Connectez-vous au cluster de HDInsight à l’aide de SSH :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les rubriques suivantes :

    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Utilisez ce qui suit pour créer un nouveau fichier nommé __tiny_graph.txt__:

        nano tiny_graph.txt

    Comme le contenu de ce fichier, utilisez ce qui suit :

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Ces données décrivent une relation entre des objets dans un graphique dirigé, en utilisant le format [source\_id, la source\_valeur, [[dest\_id], [bord\_valeur],...]]. Chaque ligne représente une relation entre un **source\_id** objet et un ou plusieurs **et\_id** objets. Le **bord\_valeur** (ou poids) peut être considérée comme la puissance et la distance de la connexion entre **source_id** et **et\_id**.

    Dessiné, et à l’aide de la valeur (ou un poids) que la distance entre les objets, les données ci-dessus peuvent ressembler à ceci :

    ![tiny_graph.txt, représentés par des cercles avec lignes de distance variable entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. Pour enregistrer le fichier, utilisez __Ctrl + X__, puis __Y__et enfin sur __entrée__ pour accepter le nom de fichier.

3. Pour stocker les données dans le stockage principal pour votre cluster HDInsight, utilisez les éléments suivants :

        hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt

4. Exécutez l’exemple SimpleShortestPathsComputation à l’aide de la commande suivante.

         yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

    Les paramètres utilisés avec cette commande sont décrits dans le tableau suivant.

  	| Paramètre | Ce qu’il fait |
  	| --------- | ------------ |
  	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | Le fichier jar contenant les exemples. |
  	| `org.apache.giraph.GiraphRunner` | La classe utilisée pour démarrer les exemples. |
  	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | L’exemple sera exécuté. Dans ce cas, il va calculer le chemin le plus court entre l’ID 1 et tous les autres codes dans le graphique. |
  	| `-ca mapred.job.tracker=headnodehost:9010` | Le headnode pour le cluster. |
  	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | Le format d’entrée à utiliser pour les données d’entrée. |
  	| `-vip /example/data/tiny_graph.txt` | Le fichier de données d’entrée. |
  	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | Le format de sortie. Dans ce cas, le code et la valeur en tant que texte brut. |
  	| `-op /example/output/shortestpaths` | L’emplacement de sortie. |
  	| `-w 2` | Le nombre de collaborateurs à utiliser. Dans ce cas, 2. |

    Pour plus d’informations sur celles-ci et d’autres paramètres utilisés avec des exemples de Giraph, consultez le [démarrage rapide de Giraph](http://giraph.apache.org/quick_start.html).

5. Une fois la tâche terminée, les résultats seront stockés dans le __wasbs : / / / exemple/out/shotestpaths__ répertoire. Les fichiers créés commence par __partie-m -__ et se termine par un nombre indiquant le premier, deuxième, fichier de etc.. Afficher le résultat, utilisez ce qui suit :

        hdfs dfs -text /example/output/shortestpaths/*

    La sortie doit ressembler à ce qui suit :

        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    La SimpleShortestPathComputation exemple est codé en dur pour commencer avec objet ID 1 et trouver le chemin le plus court à d’autres objets. Afin que la sortie doit être lu en tant que `destination_id distance`, où la distance est la valeur (ou poids) des bords a transité entre objet ID 1 et de l’ID cible.

    Cette visualisation, vous pouvez vérifier les résultats en déplacements les chemins les plus courts entre ID 1 et tous les autres objets. Notez que le chemin d’accès le plus court entre l’ID 1 et 4 de l’ID est 5. Il s’agit de la distance totale entre <span style="color:orange">ID 1 et 3</span>, puis <span style="color:red">ID 3 et 4</span>.

    ![Dessin d’objets sous forme de cercles avec les chemins les plus courts entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## <a name="next-steps"></a>Étapes suivantes

- [L’installation et l’utilisation des clusters de teinte sur HDInsight](hdinsight-hadoop-hue-linux.md). La nuance est une interface utilisateur qui permet de facilement créer, exécuter et enregistrer des travaux de porc et de la ruche, ainsi que de la parcourir le stockage par défaut pour votre HDInsight de cluster web.

- [Installation de R sur les clusters de HDInsight](hdinsight-hadoop-r-scripts-linux.md): obtenir des Instructions sur l’utilisation de cluster personnalisation pour installer et utiliser les clusters HDInsight Hadoop R. R est un langage open-source et un environnement pour le calcul statistique. Il fournit des centaines de fonctions statistiques intégrées et son propre langage de programmation qui combine des aspects de la programmation fonctionnelle et orientée objet. Il fournit également des fonctionnalités graphiques complètes.

- [Mode série sur LAN.r installer sur des clusters de HDInsight](hdinsight-hadoop-solr-install-linux.md). Personnalisation de cluster permet d’installer le mode série sur LAN.r sur HDInsight Hadoop clusters. Mode série sur LAN.r vous permet d’effectuer des opérations de recherche puissants sur les données stockées.
