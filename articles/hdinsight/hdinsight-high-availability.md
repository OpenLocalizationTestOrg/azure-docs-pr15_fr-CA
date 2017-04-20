<properties
    pageTitle="Disponibilité d’Hadoop clusters dans HDInsight | Microsoft Azure"
    description="HDInsight déploie les clusters hautement disponibles et fiables avec un nœud de tête supplémentaire."
    services="hdinsight"
    tags="azure-portal"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>


#<a name="availability-and-reliability-of-windows-based-hadoop-clusters-in-hdinsight"></a>Disponibilité et la fiabilité des clusters basés sur Windows de Hadoop dans HDInsight


>[AZURE.NOTE] Les étapes utilisées dans ce document sont spécifiques aux clusters de HDInsight de basées sur Windows. Si vous utilisez un cluster Linux, voir la [disponibilité et la fiabilité des clusters basés sur Linux de Hadoop HDInsight](hdinsight-high-availability-linux.md) pour des informations spécifiques à Linux.

HDInsight permet aux clients de déployer plusieurs types de clusters, pour les charges de travail différentes données analytique. Types de cluster offertes aujourd'hui sont les clusters Hadoop de requête et les charges de travail analyse HBase des clusters pour les charges de travail NoSQL et clusters Storm pour les charges de travail de traitement des événements en temps réel. Dans un type de cluster donné, il y a des rôles différents pour les différents nœuds. Par exemple :



- Le déploiement des clusters de Hadoop pour HDInsight avec deux rôles :
    - Nœud de tête (2 nœuds)
    - Données nœud (au moins 1)

- Le déploiement des clusters de HBase pour HDInsight avec trois rôles :
    - Serveurs de tête (2 nœuds)
    - Serveurs de la région (au moins 1 nœud)
    - Nœuds de maître/soigneur (3 nœuds)

- Le déploiement des clusters de Storm pour HDInsight avec trois rôles :
    - Nœuds de Nimbus (2 nœuds)
    - Serveurs de superviseur (au moins 1 nœud)
    - Nœuds de soigneur (3 nœuds)

Les implémentations standard de clusters d’Hadoop ont généralement un seul nœud de tête. HDInsight supprime ce point unique de défaillance grâce à l’ajout d’un nœud de tête secondaire de /head nœud serveur/Nimbus pour augmenter la disponibilité et la fiabilité du service de gestion des charges de travail. Ces nœuds de serveurs/Nimbus tête/tête de nœuds sont conçus pour gérer la défaillance de des nœuds de travailleur sans problème, mais les risques d’arrêt des services maîtres en cours d’exécution sur le nœud de tête provoque le cluster cessent de fonctionner.


Nœuds de [soigneur](http://zookeeper.apache.org/ ) (ZKs) ont été ajoutés et sont utilisés pour l’élection du chef de nœuds de tête et pour vous assurer que le travailleur nœuds et passerelles (GWs) de savoir quand basculer sur le nœud secondaire de tête (Head Node1) lorsque le nœud de tête (Head Node0) actif devienne inactif.

![Diagramme des nœuds dans l’implémentation de HDInsight Hadoop têtes extrêmement fiables.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## <a name="check-active-head-node-service-status"></a>Vérifier l’état de service d’un nœud de tête active
Pour déterminer quel nœud principal est actif et vérifier l’état des services en cours d’exécution sur ce nœud de tête, vous devez vous connecter au cluster Hadoop en utilisant le protocole RDP (Remote Desktop). Pour les instructions RDP, consultez [Hadoop de gérer des clusters dans HDInsight en utilisant le portail Azure](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp). Une fois que vous êtes connecté au cluster, double-cliquez sur l’icône **Hadoop Service disponible** sur le bureau pour obtenir l’état sur le nœud de tête le Namenode, Jobtracker, Templeton, Oozieservice, Metastore et Hiveserver2 de services sont en cours d’exécution, ou pour les services HDI 3.0, Namenode, Gestionnaire de ressources, l’historique de serveur, Templeton, Oozieservice, Metastore et Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

Dans la capture d’écran, le nœud principal actif est *headnode0*.

## <a name="access-log-files-on-the-secondary-head-node"></a>Fichiers journaux d’accès sur le nœud principal secondaire

Pour accéder aux tâches journaux sur le noeud secondaire de tête dans le cas où il est devenu le nœud principal actif, le JobTracker UI de navigation fonctionne toujours comme il le fait pour le nœud actif principal. Pour accéder à JobTracker, vous devez vous connecter au cluster Hadoop à l’aide de RDP comme décrit dans la section précédente. Une fois que vous avez distants dans le cluster, double-cliquez sur l’icône **d’État du nœud du nom Hadoop** située sur le bureau, puis sur les **journaux de NameNode** pour obtenir le répertoire de journaux sur le noeud secondaire de tête.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## <a name="configure-head-node-size"></a>Configuration de la taille du nœud de tête
Les nœuds de tête sont affectés en tant que machines virtuelles de grande taille (VMs) par défaut. Cette taille est adaptée à la gestion de la plupart des travaux d’Hadoop exécutés sur le cluster. Mais il existe des scénarios qui peuvent nécessiter des machines virtuelles de grande échelle pour les nœuds de tête. Par exemple, lorsque le cluster doit gérer un grand nombre de petites tâches Oozie.

Machines virtuelles de grande échelle peuvent être configurés à l’aide des applets de commande PowerShell d’Azure, soit le SDK HDInsight.

La création et la mise en service d’un cluster à l’aide de PowerShell d’Azure est documenté dans [HDInsight d’administrer à l’aide de PowerShell](hdinsight-administer-use-powershell.md). La configuration d’un nœud de tête de grande échelle nécessite l’ajout de la `-HeadNodeVMSize ExtraLarge` paramètre pour le `New-AzureRmHDInsightcluster` applet de commande utilisé dans ce code.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
                -ResourceGroupName $resourceGroupName `
                -ClusterName $clusterName ` 
                -Location $location `
                -HeadNodeVMSize ExtraLarge `
                -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $storageAccountKey `
                -DefaultStorageContainerName $containerName  `
                -ClusterSizeInNodes $clusterNodes

Le Kit de développement logiciel, l’histoire est similaire. La création et la mise en service d’un cluster à l’aide du Kit de développement logiciel est documentée dans le [Kit de développement .NET HDInsight à l’aide de](hdinsight-provision-clusters.md#sdk). La configuration d’un nœud de tête de grande échelle nécessite l’ajout de la `HeadNodeSize = NodeVMSize.ExtraLarge` paramètre pour le `ClusterCreateParameters()` méthode utilisée dans ce code.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
        Name = clustername,
        Location = location,
        HeadNodeSize = NodeVMSize.ExtraLarge,
        DefaultStorageAccountName = storageaccountname,
        DefaultStorageAccountKey = storageaccountkey,
        DefaultStorageContainer = containername,
        UserName = username,
        Password = password,
        ClusterSizeInNodes = clustersize
    };


## <a name="next-steps"></a>Étapes suivantes

- [Apache soigneur](http://zookeeper.apache.org/ )
- [Se connecter à des clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp)
- [À l’aide du Kit de développement .NET HDInsight](hdinsight-provision-clusters.md#sdk)
