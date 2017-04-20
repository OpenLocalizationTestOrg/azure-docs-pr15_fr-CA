<properties
    pageTitle="Utilisez les noeuds vides dans HDInsight | Microsoft Azure"
    description="Comment faire pour ajouter un nœud ampty au cluster de HDInsight qui peut être utilisé en tant que client et vos applications HDInsight d’hôte/de test."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="use-empty-edge-nodes-in-hdinsight"></a>Utiliser les noeuds vides dans le HDInsight

Découvrez comment ajouter un nœud vide à un cluster basé sur Linux de HDInsight. Un nœud vide est une machine virtuelle de Linux avec les mêmes outils client installé et configuré comme dans le headnodes, mais sans aucun service hadoop en cours d’exécution. Vous pouvez utiliser le nœud de bord pour accéder au cluster, testez vos applications client et l’hébergement de vos applications clientes. 

Vous pouvez ajouter un nœud vide à un cluster existant d’HDInsight, d’un nouveau cluster lors de la création du cluster. L’ajout d’un nœud vide est effectué à l’aide du modèle de gestionnaire de ressources Azure.  L’exemple suivant montre comment il est effectué à l’aide d’un modèle :

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "[variables('clusterApiVersion')]",
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "[parameters('edgeNodeSize')]"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Comme indiqué dans l’exemple, vous pouvez éventuellement appeler une [action de script](hdinsight-hadoop-customize-cluster-linux.md) pour effectuer une configuration supplémentaire, telle que l’installation [d’Apache teinte](hdinsight-hadoop-hue-linux.md) dans le nœud de bord.

Une fois que vous avez créé un nœud, vous pouvez vous connecter au nœud de bord à l’aide de SSH et exécuter les outils clients pour accéder au cluster Hadoop dans HDInsight.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Ajouter un nœud à un cluster existant

Dans cette section, vous utilisez un modèle du Gestionnaire de ressources pour ajouter un nœud à un cluster existant de la HDInsight.  Le modèle de gestionnaire de ressources, vous pouvez trouver dans [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). Le modèle de gestionnaire de ressources appelle un script d’action de script situé à https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Le script n’effectue pas les actions.  Cela doit démontrer une action script appelant à partir d’un modèle de gestionnaire de ressources.

**Pour ajouter un nœud vide à un cluster existant**

1. Créer un cluster de HDInsight si vous n’en avez pas encore.  Consultez [Hadoop didacticiel : mise en route de basé sur Linux de Hadoop dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrez le modèle de gestionnaire de ressources Azure dans le portail Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configurer les propriétés suivantes :

    - NOM_CLUSTER : Entrez le nom d’un cluster d’HDInsight existant.
    - EDGENODESIZE : Sélectionnez une des tailles de la machine virtuelle.
    - EDGENODEPREFIX : La valeur par défaut est **Nouveau**.  À l’aide de la valeur par défaut, le nom de nœud de bord est **nouvelle-edgenode**.  Vous pouvez personnaliser le préfixe à partir du portail. Vous pouvez également personnaliser le nom complet du modèle.


4. Cliquez sur **OK** pour enregistrer les modifications.
5. Dans le **groupe de ressources**, sélectionnez un groupe de ressources.
6. Cliquez sur **termes juridiques de révision**, puis cliquez sur **achat**.
7. Sélectionnez **Ajouter au tableau de bord**, puis cliquez sur **créer**.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Ajouter un nœud lors de la création d’un cluster

Dans cette section, vous utilisez un modèle du Gestionnaire de ressources pour créer un cluster de HDInsight avec un nœud.  Le modèle de gestionnaire de ressources, vous pouvez trouver dans un [conteneur de stockage Azure Blob](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json)de public. Le modèle de gestionnaire de ressources appelle un script d’action de script situé à https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Le script n’effectue pas les actions.  Cela doit démontrer une action script appelant à partir d’un modèle de gestionnaire de ressources.

**Pour ajouter un nœud vide à un cluster existant**

1. Créer un cluster de HDInsight si vous n’en avez pas encore.  Consultez [Hadoop didacticiel : mise en route de basé sur Linux de Hadoop dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrez le modèle de gestionnaire de ressources Azure dans le portail Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configurer les propriétés suivantes :
        
    - NOM_CLUSTER : Entrez un nom pour le nouveau cluster à créer.
    - CLUSTERLOGINUSERNAME : Permet d’entrer le nom d’utilisateur HTTP d’Hadoop.  Le nom par défaut est **admin**.
    - CLUSTERLOGINPASSWORD : Permet d’entrer le mot de passe utilisateur Hadoop HTTP.
    - SSHUSERNAME : Permet d’entrer le nom d’utilisateur SSH. Le nom par défaut est **sshuser**.
    - SSHPASSWORD : Permet d’entrer le mot de passe utilisateur SSH.
    - EMPLACEMENT : Entrez l’emplacement du nom du groupe de ressources du cluster et le compte de stockage par défaut.
    - CLUSTERTYPE : La valeur par défaut est **hadoop**.
    - CLUSTERWORKERNODECOUNT : La valeur par défaut est 2.
    - EDGENODESIZE : Sélectionnez une des tailles de la machine virtuelle.
    - EDGENODEPREFIX : La valeur par défaut est **Nouveau**.  À l’aide de la valeur par défaut, le nom de nœud de bord est **nouvelle-edgenode**.  Vous pouvez personnaliser le préfixe à partir du portail. Vous pouvez également personnaliser le nom complet du modèle.

4. Cliquez sur **OK** pour enregistrer les modifications.
5. Dans le **groupe de ressources**, entrez un nouveau nom de groupe de ressources.
6. Cliquez sur **termes juridiques de révision**, puis cliquez sur **achat**.
7. Sélectionnez **Ajouter au tableau de bord**, puis cliquez sur **créer**. 


## <a name="access-an-edge-node"></a>Accéder à un nœud

Le nœud de bord ssh point de terminaison est <EdgeNodeName>. <ClusterName>-ssh.azurehdinsight.net:22.  Par exemple, nouvelle-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Le nœud apparaît comme une application dans Azure portal.  Le portail vous donne les informations pour accéder au nœud de bord à l’aide de SSH.

**Pour vérifier le point de terminaison de bord nœud SSH**

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com).
2. Ouvrir le cluster HDInsight avec un nœud.
3. À partir de la blade de cluster, cliquez sur **Applications** . Vous doit voir le nœud du bord.  Le nom par défaut est **nouvelle-edgenode**.
4. Cliquez sur le nœud du bord. Vous doit voir le point de terminaison SSH.

**Pour utiliser la ruche sur le nœud de bord**

5. Utiliser le protocole SSH pour se connecter au nœud de bord.  Reportez-vous à la section [utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, Unix ou de OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
6. Après que vous être connecté sur le nœud de bord à l’aide de SSH, utilisez la commande suivante pour ouvrir la console de la ruche :

        hive
7. Exécutez la commande suivante pour afficher les tables de la ruche du cluster :

        show tables;

## <a name="delete-an-edge-node"></a>Supprimer un nœud

Vous pouvez supprimer un nœud à partir du portail Azure.

**Pour accéder à un nœud de bord**

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com).
2. Ouvrir le cluster HDInsight avec un nœud.
3. À partir de la blade de cluster, cliquez sur **Applications** . Vous doit afficher la liste des noeuds.  
4. Cliquez sur le nœud de bord que vous souhaitez supprimer, puis cliquez sur **Supprimer**.
5. Cliquez sur **Oui** pour confirmer.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment ajouter un nœud et accéder au nœud de bord. Pour plus d’informations, consultez les articles suivants :

- [HDInsight d’installer les applications](hdinsight-apps-install-applications.md): Découvrez comment installer une application de HDInsight sur des clusters.
- [Installer des applications HDInsight](hdinsight-apps-install-custom-applications.md): Découvrez comment déployer une application non publiée de HDInsight à HDInsight.
- [HDInsight de publier des applications](hdinsight-apps-publish-applications.md): Découvrez comment publier vos applications personnalisées HDInsight Azure Marketplace.
- [MSDN : installation d’une application de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Apprenez à définir des applications de HDInsight.
- [Clusters basés sur Linux de personnaliser la HDInsight à l’aide de Script Action](hdinsight-hadoop-customize-cluster-linux.md): Apprenez à utiliser l’Action de Script pour installer des applications supplémentaires.
- [Hadoop de basé sur Linux de créer des clusters dans HDInsight à l’aide des modèles du Gestionnaire de ressources](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Apprenez à appeler le Gestionnaire de ressources des modèles pour créer des clusters de HDInsight.

