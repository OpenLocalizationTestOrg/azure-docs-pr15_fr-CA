<properties
    pageTitle="Utilisez teinte avec Hadoop sur les clusters Linux de HDInsight | Microsoft Azure"
    description="Découvrez comment installer et utiliser la teinte avec des clusters de Hadoop sur HDInsight Linux."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="nitinme"/>

# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installer et utiliser la teinte sur des clusters de HDInsight Hadoop

Découvrez comment installer teinte dans les clusters Linux de HDInsight et d’utiliser le tunneling pour acheminer les demandes à la teinte.

## <a name="what-is-hue"></a>Quelle est la teinte ?

La nuance est un ensemble d’applications Web permettant d’interagir avec un cluster Hadoop. Teinte vous permet de parcourir le stockage associé à un cluster d’Hadoop (dans le cas des clusters de HDInsight, WASB), exécuter des travaux de la ruche et les scripts de porc, etc.. Les composants suivants sont disponibles avec les installations sur un cluster HDInsight Hadoop teinte.

* Éditeur de ruche de cire
* Porcs
* Gestionnaire de Metastore
* Oozie
* FileBrowser (qui communique avec conteneur WASB par défaut)
* Navigateur de travail

> [AZURE.WARNING] Les composants fournis avec le cluster HDInsight sont entièrement gérés et Support de Microsoft vous permet d’isoler et de résoudre les problèmes liés à ces composants.
>
> Composants assistance commercialement raisonnables pour vous aider à résoudre le problème. Cela peut provoquer de résolution du problème ni à vous demander d’engager les canaux disponibles pour les technologies open source où se trouve une grande expertise pour cette technologie. Par exemple, de nombreux sites de la Communauté qui peuvent être utilisés, comme : [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Également les projets Apache ont des sites de projets sur [http://apache.org](http://apache.org), par exemple : [Hadoop](http://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Installation de teinte à l’aide des Actions de Script

L’action de script suivant peut servir à installer des teinte sur un cluster basé sur Linux de HDInsight.
https://hdiconfigactions.BLOB.Core.Windows.NET/linuxhueconfigactionv02/Install-hue-uber-v02.sh
    
Cette section fournit des instructions sur la façon d’utiliser le script de mise à disponibilité au cluster en utilisant le portail Azure. 

> [AZURE.NOTE] PowerShell Azure, la CLI d’Azure, le Kit de développement .NET HDInsight ou modèles d’Azure le Gestionnaire de ressources peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer des actions de script pour les clusters en cours d’exécution. Pour plus d’informations, voir [clusters de personnaliser la HDInsight avec les Actions de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrer la mise en service d’un cluster à l’aide de la procédure dans les [clusters de disposition HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mais n’effectuez pas de mise en service.

    > [AZURE.NOTE] Pour installer la teinte sur des clusters de HDInsight, la taille recommandée de headnode est au moins A4 (8 cœurs, 14 Go de mémoire).

2. Sur la lame de **Configuration facultatives** , sélectionnez les **Actions de Script**et fournir les informations comme indiqué ci-dessous :

    ![Fournir le paramètre action de teinte] (./media/hdinsight-hadoop-hue-linux/hue_script_action.png "Fournir le paramètre action de teinte")

    * __Nom__: entrez un nom convivial pour l’action du script.
    * __URI du SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
    * __Tête__: Activez cette case à cocher
    * __Travailleur__: laissez ce champ vide.
    * __SOIGNEUR__: laissez ce champ vide.
    * __Paramètres__: laissez ce champ vide.

3. Au bas des **Actions de Script**, utilisez **le bouton** enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** en bas de la lame de **Configuration facultative** pour enregistrer les informations de configuration facultatives.

4. Continuer la mise en service du cluster, comme décrit dans [HDInsight de fourniture des clusters sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="use-hue-with-hdinsight-clusters"></a>Teinte d’utilisation avec des clusters de HDInsight

Tunneling SSH est le seul moyen d’accéder la teinte sur le cluster une fois qu’il est en cours d’exécution. Tunneling via le protocole SSH n’autorise le trafic à accéder directement à la headnode du cluster exécutant teinte. Une fois que le cluster a terminé la mise en service, procédez comme suit pour utiliser la teinte sur un cluster HDInsight Linux.

1. Utilisez les informations dans [Utiliser SSH Tunneling pour accéder à l’interface utilisateur web Ambari, ResourceManager, JobHistory, NameNode, Oozie et autre web, l’interface utilisateur](hdinsight-linux-ambari-ssh-tunnel.md) pour créer un tunnel SSH à partir de votre système client au cluster HDInsight, puis configurez votre navigateur Web pour utiliser le tunnel SSH en tant que proxy.

2. Une fois que vous avez créé un tunnel SSH et configuré votre navigateur pour passer le trafic de proxy, vous devez rechercher le nom d’hôte du nœud principal primaire. Pour cela, en vous connectant au cluster à l’aide de SSH sur le port 22. Par exemple, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` où __nom_utilisateur__ est votre nom d’utilisateur SSH et __CLUSTERNAME__ est le nom de votre cluster.

    Pour plus d’informations sur l’utilisation du protocole SSH, consultez les documents suivants :

    * [Utiliser le protocole SSH avec basé sur Linux de HDInsight à partir d’un client Mac OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utiliser le protocole SSH avec basé sur Linux de HDInsight à partir d’un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Une fois connecté, utilisez la commande suivante pour obtenir le nom de domaine complet de la headnode principale :

        hostname -f

    Ceci renverra un nom semblable au suivant :

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
    Il s’agit du nom d’hôte de l’headnode primaire dans lequel se trouve le site Web de teinte.

2. Utilisez le navigateur pour ouvrir le portail de teinte à http://HOSTNAME:8888. Remplacez le nom d’hôte avec le nom que vous avez obtenu à l’étape précédente.

    > [AZURE.NOTE] Lorsque vous vous connectez pour la première fois, vous devrez créer un compte pour vous connecter au portail de teinte. Les informations d’identification que vous spécifiez ici seront limitées au portail et ne sont pas liées à l’admin ou les informations d’identification de l’utilisateur SSH vous avez spécifié lors de la fourniture du cluster.

    ![Connexion au portail de teinte] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Spécifiez les informations d’identification pour le portail de teinte")

### <a name="run-a-hive-query"></a>Exécuter une requête de la ruche

1. À partir du portail de la teinte, cliquez sur **Éditeurs de requête**, puis cliquez sur **la ruche** pour ouvrir l’éditeur de la ruche.

    ![Utilisez la ruche] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Utilisez la ruche")

2. Sous l’onglet **aide** , sous la **base de données**, vous devriez voir **hivesampletable**. Il s’agit d’un exemple de table qui est fourni avec tous les clusters Hadoop sur HDInsight. Permet d’entrer un exemple de requête dans le volet droit et voir le résultat dans l’onglet **résultats** dans le volet inférieur, comme illustré dans la capture d’écran.

    ![Requête d’exécution de la ruche] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Requête d’exécution de la ruche")

    Vous pouvez également utiliser l’onglet **graphique** pour afficher une représentation visuelle du résultat.

### <a name="browse-the-cluster-storage"></a>Parcourir le stockage de cluster

1. À partir du portail de la teinte, cliquez sur **Explorateur** dans le coin supérieur droit de la barre de menus.

2. Par défaut, l’Explorateur s’ouvre dans le répertoire **/user/myuser** . Cliquez sur la barre oblique avant le répertoire de l’utilisateur dans le chemin d’accès pour accéder à la racine du conteneur de stockage Azure associé au cluster.

    ![Explorateur de fichiers d’utilisation] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Explorateur de fichiers d’utilisation")

3. Avec le bouton droit sur un fichier ou un dossier pour afficher les opérations disponibles. Utilisez le bouton **Télécharger** dans le coin droit de télécharger des fichiers dans le répertoire en cours. Utilisez le bouton **Nouveau** pour créer de nouveaux fichiers ou des répertoires.

> [AZURE.NOTE] L’Explorateur teinte peut uniquement afficher le contenu du conteneur par défaut associé au cluster HDInsight. Les comptes/conteneurs de stockage supplémentaire peut avoir associé à un cluster ne sera pas accessibles à l’aide de l’Explorateur de fichiers. Toutefois, les autres conteneurs associés au cluster sera toujours accessibles pour les travaux de la ruche. Par exemple, si vous entrez la commande `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` dans l’éditeur de la ruche, vous pouvez voir le contenu ainsi que des conteneurs supplémentaires. Dans cette commande, **newcontainer** n’est pas le conteneur par défaut associé à un cluster.

## <a name="important-considerations"></a>Considérations importantes

1. Le script utilisé pour installer la teinte installe uniquement sur le headnode principal du cluster.

2. Lors de l’installation, plusieurs services d’Hadoop (très, fils, RM2, Oozie) sont redémarrés de mise à jour de la configuration. Le script après l’installation de teinte, il peut prendre un certain temps pour d’autres services d’Hadoop Démarrer. Cela peut affecter les performances de teinte initialement. Une fois que tous les services démarrent, teinte sera entièrement fonctionnel.

3.  Teinte ne comprend pas les travaux de Tez, qui est la valeur par défaut en cours pour la ruche. Si vous souhaitez utiliser MapReduce comme le moteur d’exécution de ruche, mettre à jour le script pour utiliser la commande suivante dans votre script :

        set hive.execution.engine=mr;

4.  Avec les clusters Linux, vous pouvez avoir un scénario où vos services sont en cours d’exécution sur le headnode principal alors que le Gestionnaire de ressources peut s’exécuter sur le serveur secondaire. Un tel scénario peut générer des erreurs (illustrées ci-dessous) lors de l’utilisation de teinte pour plus d’informations sur les tâches en cours d’exécution sur le cluster. Toutefois, vous pouvez afficher les détails du travail lorsque la tâche est terminée.

    ![Erreur de portail de teinte] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Erreur de portail de teinte")

    Cela est dû à un problème connu. Pour résoudre ce problème, modifiez Ambari afin que le Gestionnaire de ressources active s’exécute également sur le principal headnode.

5.  Teinte comprend WebHDFS tandis que HDInsight clusters utilisent le stockage Azure en utilisant `wasbs://`. Par conséquent, le script personnalisé est utilisé avec une action de script installe WebWasb, qui est un service compatible avec WebHDFS pour communiquer avec WASB. Ainsi, même si le portail de teinte indique très endroits (par exemple, lorsque vous déplacez votre souris sur le **Navigateur de fichiers**), il doit être interprété en tant que WASB.


## <a name="next-steps"></a>Étapes suivantes

- [Installation de Giraph sur des clusters de HDInsight](hdinsight-hadoop-giraph-install-linux.md). Personnalisation de cluster permet d’installer Giraph sur les clusters de HDInsight Hadoop. Giraph vous permet d’effectuer le traitement graphique à l’aide d’Hadoop, et il peut être utilisé avec HDInsight d’Azure.

- [Mode série sur LAN.r installer sur des clusters de HDInsight](hdinsight-hadoop-solr-install-linux.md). Personnalisation de cluster permet d’installer le mode série sur LAN.r sur HDInsight Hadoop clusters. Mode série sur LAN.r vous permet d’effectuer des opérations de recherche puissants sur les données stockées.

- [Installation de R sur les clusters de HDInsight](hdinsight-hadoop-r-scripts-linux.md). Personnalisation de cluster permet d’installer des R sur des clusters de HDInsight Hadoop. R est un langage open-source et un environnement pour le calcul statistique. Il fournit des centaines de fonctions statistiques intégrées et son propre langage de programmation qui combine des aspects de la programmation fonctionnelle et orientée objet. Il fournit également des fonctionnalités graphiques complètes.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
