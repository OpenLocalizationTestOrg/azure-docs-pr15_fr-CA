<properties
    pageTitle="Créer des clusters tempête, HBase ou Hadoop sur Linux dans HDInsight à l’aide de la CLI d’Azure multiplates-formes | Microsoft Azure"
    description="Apprenez à créer des clusters basés sur Linux de HDInsight à l’aide de la CLI d’Azure multiplates-formes, des modèles de Azure le Gestionnaire de ressources et l’API REST Azure. Vous pouvez spécifier le type de cluster (Hadoop, HBase ou tempête), ou utiliser des scripts pour installer des composants personnalisés..."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>Créer des grappes de serveurs Linux dans HDInsight à l’aide de la CLI d’Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

L’infrastructure du langage commun Azure est un utilitaire de ligne de commande multiplateforme qui permet de gérer des Services Azure. Il peut être utilisé, ainsi que des modèles de gestion des ressources d’Azure, pour créer un cluster de HDInsight, ainsi que les comptes de stockage associés et d’autres services.

Les modèles de gestion des ressources Azure sont des documents JSON qui décrivent un __groupe de ressources__ et toutes les ressources qu’il contient (par exemple, HDInsight.) Cette approche basée sur un modèle vous permet de définir toutes les ressources dont vous avez besoin pour HDInsight dans un modèle. Il vous permet également de gérer les modifications apportées au groupe dans son ensemble par le biais de __déploiements__, qui applique des modifications à l’ensemble du groupe.

Les étapes décrites dans ce document décrivent le processus de création d’un nouveau cluster de HDInsight à l’aide de la CLI d’Azure et d’un modèle.

> [AZURE.IMPORTANT] Les étapes de ce document utilisent le nombre par défaut de nœuds de traitement (4) pour un cluster de HDInsight. Si vous prévoyez plus de 32 nœuds de travail (lors de la création d’un cluster ou en redimensionnant le cluster), vous devez sélectionner une taille de nœud de tête avec au moins 8 cœurs et 14 Go de ram.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, reportez-vous à la section [HDInsight de tarification](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __CLI azure__. Les étapes décrites dans ce document ont été dernier testées avec Azure CLI version 0.10.1.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 


### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="log-in-to-your-azure-subscription"></a>Connectez-vous à votre abonnement Azure

Suivez les étapes décrites dans la [connexion à un abonnement Azure à partir de l’Interface de ligne de commande Azure (CLI Azure)](../xplat-cli-connect.md) et connectez-vous à votre abonnement à l’aide de la méthode de __connexion__ .

##<a name="create-a-cluster"></a>Créer un cluster

Les étapes suivantes doivent être effectuées à partir d’une invite de commande, shell ou une session Terminal Server après l’installation et la configuration de l’infrastructure du langage commun Azure.

1. Pour s’authentifier sur votre abonnement Azure, utilisez la commande suivante :

        azure login

    Vous êtes invité à fournir votre nom et le mot de passe. Si vous avez plusieurs abonnements d’Azure, utilisez `azure account set <subscriptionname>` pour définir l’abonnement utilisent des commandes CLI d’Azure.

3. Passer en mode Azure le Gestionnaire de ressources à l’aide de la commande suivante :

        azure config mode arm

4. Créer un groupe de ressources. Ce groupe de ressources contient le cluster HDInsight et compte de stockage associé.

        azure group create groupname location
        
    * Remplacez __groupname__ par un nom unique pour le groupe. 
    * Remplacez __l’emplacement__ avec la zone géographique que vous voulez créer le groupe dans. 
    
        Pour obtenir la liste des emplacements valides, utilisez le `azure location list` de commande et ensuite utiliser un des emplacements à partir de la colonne __nom__ .

5. Créer un compte de stockage. Ce compte de stockage servira le stockage par défaut pour l’HDInsight du cluster.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Remplacer le __nom de groupe__ avec le nom du groupe créé à l’étape précédente.
     * Remplacez __l’emplacement__ avec le même emplacement que celui utilisé à l’étape précédente. 
     * Remplacez __storagename__ par un nom unique pour le compte de stockage.
     
     > [AZURE.NOTE] Pour plus d’informations sur les paramètres utilisés dans cette commande, utilisez `azure storage account create -h` pour afficher l’aide de cette commande.

5. Récupérer la clé utilisée pour accéder au compte de stockage.

        azure storage account keys list -g groupname storagename
        
    * Remplacez __groupname__ par le nom de groupe de ressources.
    * Remplacez __storagename__ par le nom du compte de stockage.
    
    Dans les données retournées, enregistrez la valeur de __clé__ pour __key1__.

6. Créer un cluster de HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Remplacez __groupname__ par le nom de groupe de ressources.

    * Remplacez __Hadoop__ avec le type de cluster que vous souhaitez créer. Par exemple, `Hadoop`, `HBase`, `Storm` ou `Spark`.

        > [AZURE.IMPORTANT] HDInsight clusters sont dotés de types qui correspondent à la charge de travail ou de la technologie de cluster est réglé pour. Il n’existe aucune méthode prise en charge pour créer un cluster qui combine plusieurs types, tels que tempête et HBase sur un cluster. 

    * Remplacez __l’emplacement__ avec le même emplacement que celui utilisé dans les étapes précédentes.

    * Remplacez __storagename__ par le nom de compte de stockage.

    * Remplacez la __clé de stockage__ avec la clé obtenue à l’étape précédente. 

    * Pour le `--defaultStorageContainer` paramètre, utilisez le même nom que vous utilisez pour le cluster.

    * Remplacer __admin__ et __httppassword__ avec le nom et le mot de passe à utiliser lors de l’accès au cluster via HTTPS.

    * Remplacer __sshuser__ et __sshuserpassword__ avec le nom d’utilisateur et le mot de passe à utiliser lors de l’accès au cluster à l’aide de SSH

    Elle peut prendre plusieurs minutes pour que le processus de création de cluster à la fin. Généralement environ 15.

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un cluster de HDInsight à l’aide de la CLI d’Azure, utilisez ce qui suit pour apprendre à travailler avec votre cluster :

###<a name="hadoop-clusters"></a>Hadoop clusters

* [Utilisez la ruche avec HDInsight](hdinsight-use-hive.md)
* [Utilisez des porcs avec HDInsight](hdinsight-use-pig.md)
* [Utilisez MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>Clusters HBase

* [Mise en route de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clusters de tempête

* [Développer des topologies de Java pour tempête sur HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilisation des composants de Python de tempête sur HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déployer et surveiller des topologies avec tempête sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
