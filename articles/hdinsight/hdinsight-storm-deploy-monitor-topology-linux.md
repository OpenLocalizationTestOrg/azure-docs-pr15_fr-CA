<properties
   pageTitle="Déployer et gérer des topologies de tempête de Apache sur basé sur Linux de HDInsight | Microsoft Azure"
   description="Découvrez comment déployer, de surveiller et de gérer des topologies de tempête d’Apache à l’aide de la tempête du tableau de bord sur HDInsight de basé sur Linux. Hadoop d’utiliser les outils de Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

# <a name="deploy-and-manage-apache-storm-topologies-on-linux-based-hdinsight"></a>Déployer et gérer des topologies de tempête de Apache sur HDInsight de basé sur Linux

Dans ce document, apprendre les principes fondamentaux de la gestion et la surveillance des topologies de tempête en cours d’exécution sur la tempête de basé sur Linux sur les clusters de HDInsight.

> [AZURE.IMPORTANT] Les étapes décrites dans cet article requièrent une tempête basé sur Linux sur cluster de HDInsight. Pour plus d’informations sur le déploiement et la surveillance des topologies de HDInsight de basés sur Windows, reportez-vous à la section [déployer et gérer des topologies de tempête de Apache sur HDInsight de basés sur Windows](hdinsight-storm-deploy-monitor-topology.md)

## <a name="prerequisites"></a>Conditions préalables

* **Basé sur Linux de A la tempête sur HDInsight cluster**: reportez-vous à la section [mise en route de tempête Apache sur HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) les étapes de création d’un cluster

* **Connaissance de SSH et SCP**: pour plus d’informations sur l’utilisation de SSH et SCP avec HDInsight, consultez la rubrique suivante :

    * **Les clients Linux, Unix ou OS X**: reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Les clients Windows**: reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* **Client d’un SCP**: Ceci est fourni avec tous les systèmes Linux, Unix et OS X. Pour les clients Windows, nous vous recommandons de PSCP, qui est disponible à partir de la [page de téléchargement de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="start-a-storm-topology"></a>Démarrer une topologie de tempête

### <a name="using-ssh-and-the-storm-command"></a>À l’aide de SSH et la commande de tempête

1. Utiliser le protocole SSH pour se connecter au cluster HDInsight. **Nom d’utilisateur** de remplacer le nom de votre connexion SSH. Remplacez **CLUSTERNAME** par votre nom de cluster HDInsight :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Pour plus d’informations sur l’utilisation du protocole SSH pour se connecter à votre cluster HDInsight, consultez les documents suivants :
    
    * **Les clients Linux, Unix ou OS X**: reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Les clients Windows**: reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Utilisez la commande suivante pour démarrer un exemple de topologie :

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    L’exemple de topologie WordCount démarre sur le cluster. Il sera générer des phrases et pour compter les occurrences de chaque mot dans les phrases de manière aléatoire.

    > [AZURE.NOTE] Lorsque vous soumettez la topologie pour le cluster, vous devez d’abord copier le fichier jar contenant du cluster avant d’utiliser la `storm` commande. Ceci peut être accompli à l’aide de la `scp` commande à partir du client où se trouve le fichier. Par exemple,`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > L’exemple WordCount et autres exemples de starter tempête, sont déjà incluses dans votre cluster à `/usr/hdp/current/storm-client/contrib/storm-starter/`.

### <a name="programmatically"></a>Par programme

Vous pouvez par programmation déployer une topologie à tempête sur HDInsight en communiquant avec le service Nimbus hébergé dans votre cluster. [https://github.com/Azure-Samples/hdinsight-Java-Deploy-Storm-Topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fournit un exemple d’application Java illustrant comment déployer et démarrer une topologie via le service Nimbus.

## <a name="monitor-and-manage-using-the-storm-command"></a>Surveiller et gérer à l’aide de la commande de tempête

Le `storm` utilitaire vous permet de travailler avec les topologies en cours d’exécution à partir de la ligne de commande. Voici une liste des commandes couramment utilisées. Utilisez `storm -h` pour une liste complète des commandes.

### <a name="list-topologies"></a>Topologies de liste

Utilisez la commande suivante pour répertorier toutes les topologies l’exécution :

    storm list
    
Ceci renvoie des informations semblables à ce qui suit :

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Désactivez, puis réactivez

La désactivation d’une topologie, il suspend jusqu'à ce qu’il soit mis à mort ou réactivé. Utilisez la suivante pour désactiver et réactiver :

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Supprimer une topologie en cours d’exécution

Topologies de tempête, une fois commencées, continuera jusqu'à l’arrêt en cours d’exécution. Pour arrêter une topologie, utilisez la commande suivante :

    storm stop TOPOLOGYNAME

### <a name="rebalance"></a>Rééquilibrage

Rééquilibrage d’une topologie permet de réviser le parallélisme de la topologie du système. Par exemple, si vous avez redimensionné le cluster pour ajouter d’autres notes, rééquilibrage permet à une topologie en cours d’exécution utiliser les nouveaux nœuds.

> [AZURE.WARNING] Rééquilibrage de tout d’abord une topologie désactive la topologie, répartit uniformément des travailleurs au sein du cluster, puis enfin retourne la topologie à l’état que précédant le rééquilibrage s’est produite. Ainsi, si la topologie est active, il devient actif à nouveau. Si elle a été désactivée, elle restera désactivée.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-using-the-storm-ui"></a>Surveiller et gérer à l’aide de l’interface utilisateur de tempête

L’interface utilisateur de tempête fournit une interface web pour l’utilisation de topologies en cours d’exécution et est inclus sur votre cluster de HDInsight. Pour afficher l’interface utilisateur de tempête, utilisez un navigateur web pour ouvrir __https://CLUSTERNAME.azurehdinsight.net/stormui__, où __CLUSTERNAME__ est le nom de votre cluster.

> [AZURE.NOTE] Si vous êtes invité à fournir un nom d’utilisateur et le mot de passe, entrez l’administrateur de cluster (admin) et le mot de passe que vous avez utilisé quand créer le cluster.


### <a name="main-page"></a>Page principale

La page principale de l’interface utilisateur de tempête fournit les informations suivantes :

* **Cluster résumé**: informations de base sur le cluster de tempête.

* **Résumé de la topologie**: liste des topologies en cours d’exécution. Utilisez les liens de cette section pour afficher plus d’informations sur les topologies spécifiques.

* **Résumé du superviseur**: informations sur le maître d’oeuvre tempête.

* **Configuration de Nimbus**: configuration Nimbus pour le cluster.

### <a name="topology-summary"></a>Résumé de la topologie

Sélection d’un lien dans la section **topologie résumé** affiche les informations suivantes sur la topologie :

* **Résumé de la topologie**: informations de base sur la topologie.

* **Actions de topologie**: des actions de gestion que vous pouvez effectuer pour la topologie.

  * **Activer**: traitement de curriculum vitae de topologie est désactivée.

  * **Désactiver**: interrompt une topologie en cours d’exécution.

  * **Rééquilibrer**: ajuste le parallélisme de la topologie. Vous devez rééquilibrer les topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Ainsi, la topologie à ajuster le parallélisme pour compenser l’augmenté ou diminué le nombre de nœuds dans le cluster.

    Pour plus d’informations, voir <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">comprendre le parallélisme de topologie de tempête</a>.

  * **Kill**: met fin à une topologie de tempête après le délai spécifié.

* **Statistiques de la topologie**: statistiques sur la topologie. Utilisez les liens dans la colonne de la **fenêtre** pour définir le délai pour les entrées restantes sur la page.

* **Becs verseurs amovibles**: les becs verseurs utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur becs verseurs spécifiques.

* **Boulons**: les boulons utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur les boulons spécifiques.

* **Configuration de la topologie**: la configuration de la topologie sélectionnée.

### <a name="spout-and-bolt-summary"></a>Un bec et un résumé de boulon

Sélection d’un BEC VERSEUR à partir de la section **des becs verseurs amovibles** ou **boulons** affiche les informations suivantes sur l’élément sélectionné :

* **Composant Sommaire**: informations de base sur le bec ou le boulon.

* **Statistiques de bec/du boulon**: statistiques sur le bec ou le boulon. Utilisez les liens dans la colonne de la **fenêtre** pour définir le délai pour les entrées restantes sur la page.

* **Statistiques de l’entrée** (boulon uniquement) : informations sur les flux d’entrée utilisée par le boulon.

* **Statistiques de sortie**: informations sur les flux de données émis par cet BEC VERSEUR ou de boulon.

* **Les exécuteurs**: informations sur les instances du boulon ou du bec. Sélectionnez le **Port** d’entrée d’un exécuteur spécifique afficher un journal d’informations de diagnostic produites pour cette instance.

* **Erreur**: les informations d’erreur pour cette goulotte ou de boulon.

## <a name="rest-api"></a>API REST

L’interface utilisateur de tempête est construit sur l’API REST, pour vous pouvez d’effectuer similaires de gestion et de contrôle des fonctionnalités à l’aide de l’API REST. Vous pouvez utiliser l’API REST pour créer des outils personnalisés pour la gestion et la surveillance des topologies de tempête.

Pour plus d’informations, voir [l’API REST de l’interface utilisateur tempête](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Les informations suivantes sont spécifiques à l’utilisation de l’API REST avec tempête Apache sur HDInsight.

> [AZURE.IMPORTANT] L’API REST de tempête n’est pas accessible au public via internet et doivent être accessibles à l’aide d’un tunnel SSH pour le nœud de tête du cluster HDInsight. Pour plus d’informations sur la création et à l’aide d’un tunnel SSH, consultez [Utiliser SSH Tunneling pour accéder à l’interface utilisateur web de Ambari, ResourceManager, JobHistory, NameNode, Oozie et autre web, l’interface utilisateur](hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>L’URI de base

L’URI de base pour l’API REST sur les clusters basés sur Linux de HDInsight est disponible sur le nœud de tête à **https://HEADNODEFQDN:8744/api/v1/**; Toutefois, le nom de domaine du nœud principal est généré lors de la création du cluster et n’est pas statique.

Vous trouverez le nom de domaine pleinement qualifié (FQDN) pour le nœud principal de cluster de plusieurs manières :

* __Session à partir d’un SSH__: utilisez la commande `headnode -f` à partir d’une session SSH pour le cluster.

* __À partir du Web de Ambari__: sélectionnez les __Services__ à partir du haut de la page, puis sélectionnez __tempête__. À partir de l’onglet __Résumé__ , sélectionnez le __Serveur de l’interface utilisateur de tempête__. Le nom de domaine complet de l’interface utilisateur de tempête et d’une API REST est en cours d’exécution sur le nœud sera en haut de la page.

* __À partir de Ambari reste API__: utilisez la commande `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` pour extraire des informations à propos de l’interface utilisateur de tempête et d’une API REST sont exécutant sur le nœud. Remplacez le __mot de passe__ avec le mot de passe administrateur pour le cluster. Remplacez __CLUSTERNAME__ par le nom du cluster. Dans la réponse, l’entrée « host_name » contient le nom de domaine complet du nœud.

### <a name="authentication"></a>Authentification

Les demandes pour l’API REST doivent utiliser **l’authentification de base**, afin que vous utilisez le nom d’administrateur de cluster de HDInsight et le mot de passe.

> [AZURE.NOTE] Étant donné que l’authentification de base est envoyée à l’aide de texte en clair, vous devez **toujours** utiliser HTTPS pour sécuriser les communications avec le cluster.

### <a name="return-values"></a>Valeurs de retour

Les informations retournées par l’API REST peuvent uniquement être utilisables à partir de dans le cluster ou les ordinateurs virtuels sur le même réseau que le cluster virtuel au Azure. Par exemple, le nom de domaine complet (FQDN) retourné pour les serveurs soigneur ne sera pas accessibles à partir d’Internet.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment déployer et surveiller des topologies en utilisant le tableau de bord de tempête, découvrez comment [les topologies basée sur Java de développer à l’aide de Maven](hdinsight-storm-develop-java-topology.md).

Pour obtenir une liste de plusieurs exemples de topologies, consultez [exemples de topologies pour tempête sur HDInsight](hdinsight-storm-example-topology.md).
