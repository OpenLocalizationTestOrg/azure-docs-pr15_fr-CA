<properties 
   pageTitle="Configurer la réplication entre les deux centres de données HBase | Microsoft Azure" 
   description="Apprenez à configurer la réplication de HBase sur deux centres de données, ainsi que sur les cas d’usage pour la réplication de cluster." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/25/2016"
   ms.author="jgao"/>

# <a name="configure-hbase-geo-replication-in-hdinsight"></a>Configurer la HBase géo-réplication de HDInsight

> [AZURE.SELECTOR]
- [Configurez la connectivité VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurer le service DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurer la réplication de HBase](hdinsight-hbase-geo-replication.md) 
 
Apprenez à configurer la réplication HBase sur deux centres de données. Des cas d’usage pour la réplication de cluster incluent :

- Sauvegarde et reprise après sinistre
- Agrégation de données
- Distribution des données géographique
- Réception de données en ligne combiné avec analytique des données hors connexion

Réplication de cluster utilise une méthodologie de push de la source. Un cluster de HBase peut être une source et une destination, ou peut répondre à ces deux rôles à la fois. La réplication est asynchrone, et l’objectif de la réplication est cohérence éventuelle. Lorsque la source reçoit une modification à une famille de colonne avec la réplication activée, cette modification est propagée à tous les clusters de destination. Lorsque les données sont répliquées à partir d’un cluster à un autre, le cluster source et tous les clusters qui ont déjà utilisé les données sont suivies pour éviter les boucles de réplication. Pour plus d’informations, dans ce didacticiel, vous allez configurer une réplication source-destination.  Pour les autres topologies de cluster, consultez le [Guide de référence de HBase Apache](http://hbase.apache.org/book.html#_cluster_replication).

Il s’agit de la troisième partie de la série :

- [Configurer une connexion VPN entre deux réseaux virtuels][hdinsight-hbase-replication-vnet]
- [Configurer le service DNS pour les réseaux virtuels][hdinsight-hbase-replication-dns]
- Configurer la réplication de zone géographique HBase (ce didacticiel)

Le diagramme suivant illustre les deux réseaux virtuels et la connectivité de réseau que vous avez créé dans [configurer une connectivité VPN entre les deux réseaux virtuels] [ hdinsight-hbase-geo-replication-vnet] et [Configurer le serveur DNS pour les réseaux virtuels][hdinsight-hbase-replication-dns]: 

![Diagramme de réseau virtuel de réplication HDInsight HBase][img-vnet-diagram]

## <a id="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Une station de travail avec PowerShell d’Azure**.

    Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu’administrateur et la valeur de la stratégie d’exécution *RemoteSigned*. Consultez l’aide de l’applet de commande Set-ExecutionPolicy.
    
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Réseau virtuel de deux Azure avec connectivité VPN et DNS configurés**.  Pour plus d’informations, voir [configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-replication-vnet]et [Configurer le serveur DNS entre deux réseaux virtuels Azure][hdinsight-hbase-replication-dns].


    Avant d’exécuter des scripts PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l’aide de l’applet de commande suivante :

        Add-AzureAccount

    Si vous avez plusieurs abonnements d’Azure, utilisez l’applet de commande suivante pour définir l’abonnement actuel :

        Select-AzureSubscription <AzureSubscriptionName>



## <a name="provision-hbase-clusters-in-hdinsight"></a>Configurer des clusters HBase dans HDInsight

Dans [configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-replication-vnet], vous avez créé un réseau virtuel dans une Europe, centre de données et un réseau virtuel dans un centre de données américain. Les deux réseaux virtuels sont connectés via le VPN. Dans cette session, vous met en service un cluster HBase dans chacun des réseaux virtuels. Plus loin dans ce didacticiel, vous effectuerez un des clusters pour répliquer l’autre cluster HBase HBase.

Le portail classique Azure ne prend pas en charge la mise en service de clusters HDInsight avec les options de configuration personnalisée. Par exemple, définissez *hbase.replication* à *true*. Si vous définissez la valeur dans le fichier de configuration après qu’un cluster est mis en service, vous perdrez le paramètre une fois que le cluster est en cours de reconfiguration. Pour plus d’informations, voir [clusters de fourniture Hadoop dans HDInsight][hdinsight-provision]. Une des options de mise en service cluster HDInsight avec des options personnalisées est à l’aide de PowerShell d’Azure.


**Pour configurer un Cluster d’HBase de Contoso-VNet-UE** 

1. À partir de votre station de travail, ouvrir Windows PowerShell ISE.
2. Définissez les variables au début du script, puis exécutez le script.

        # create hbase cluster with replication enabled
        
        $azureSubscriptionName = "[AzureSubscriptionName]"
        
        $hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
        $hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
        $hadoopUserLogin = "[HadoopUserName]"
        $hadoopUserpw = "[HadoopUserPassword]"
        
        $vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
        $subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
        
        $storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
        $storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
        $blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
        
        #connect to your Azure subscription
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName
        
        # Create a storage account used by the HBase cluster
        $location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
        
        # Create a blob container used by the HBase cluster
        $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
        
        # Create provision configuration object
        $hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
            
        $hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
        
        # retrieve vnet id based on vnetname
        $vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
        
        $config = New-AzureHDInsightClusterConfig `
                        -ClusterSizeInNodes $hbaseClusterSize `
                        -ClusterType HBase `
                        -VirtualNetworkId $vNetID `
                        -SubnetName $subNetName `
                    | Set-AzureHDInsightDefaultStorage `
                        -StorageAccountName $storageAccountName `
                        -StorageAccountKey $storageAccountKey `
                        -StorageContainerName $blobContainerName `
                    | Add-AzureHDInsightConfigValues `
                        -HBase $hbaseConfigValues
        
        # provision HDInsight cluster
        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
        
        $config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Pour configurer un Cluster de HBase VNet-Contoso-États-Unis** 

- Utiliser le même script avec les valeurs suivantes :

        $hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
        $vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
        $storageAccountName = 'ContosoStoreUS'  

    Car vous êtes déjà connecté à votre compte Azure, vous n’avez pas besoin de plus d’exécuter le comlets suivant :

        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName




## <a name="configure-dns-conditional-forwarder"></a>Configuration du redirecteur conditionnel du DNS

Dans la [Configuration de DNS pour les réseaux virtuels][hdinsight-hbase-replication-dns], vous avez configuré des serveurs DNS des deux réseaux. Les clusters HBase ont des suffixes de domaine différents. Vous devez donc configurer les redirecteurs DNS supplémentaires.

Pour configurer un redirecteur conditionnel, vous devez connaître les suffixes de domaine des deux clusters HBase. 

**Pour rechercher les suffixes de domaine des deux clusters HBase**

1. RDP dans **Contoso-HBase-Union européenne**.  Pour obtenir des instructions, reportez-vous à la section [Hadoop de gérer des clusters dans HDInsight en utilisant le portail classique Azure][hdinsight-manage-portal]. Il s’agit en fait d’headnode0 du cluster.
2. Ouvrez une console Windows PowerShell ou invite de commande.
3. Exécutez **ipconfig**et notez le **suffixe DNS spécifique à la connexion**.
4. Veuillez ne pas fermer la session RDP.  Vous en aurez besoin ultérieurement pour tester la résolution de nom de domaine.
5. Répétez les mêmes étapes, à savoir le **suffixe DNS spécifique à la connexion** de **Contoso-HBase-US**.


**Pour configurer des redirecteurs DNS**
 
1.  RDP dans **Contoso-DNS-Union européenne**. 
2.  Cliquez sur la touche Windows sur le coin inférieur gauche.
2.  Cliquez sur **Outils d’administration**.
3.  Cliquez sur **DNS**.
4.  Dans le volet gauche, développez **DSN**, **Contoso-DNS-UE**.
5.  **Redirecteurs conditionnels**d’avec le bouton droit, puis cliquez sur **Nouveau redirecteur conditionnel**. 
5.  Entrez les informations suivantes :
    - **Domaine DNS**: entrez le suffixe DNS de Contoso-HBase-US. Par exemple : Contoso-HBase-US.f5.internal.cloudapp.net.
    - **Les adresses IP des serveurs maîtres**: entrez 10.2.0.4, qui est l’adresse IP du Contoso-DNS-États-Unis. Vérifiez que la période d’enquête. Votre serveur DNS peut avoir une adresse IP différente.
6.  Appuyez sur **entrée**, puis cliquez sur **OK**.  Maintenant, vous serez en mesure de résoudre l’adresse IP du Contoso-DNS-US Contoso-DNS-UE.
7.  Répétez les étapes pour ajouter un redirecteur conditionnel de DNS pour le service DNS sur l’ordinateur virtuel de Contoso-États-Unis-DNS avec les valeurs suivantes :
    - **Domaine DNS**: entrez le suffixe DNS de l’UE de HBase de Contoso. 
    - **Les adresses IP des serveurs maîtres**: entrez 10.2.0.4, qui est l’adresse IP de Contoso-DNS-l’Union européenne.

**Pour tester la résolution de nom de domaine**

1. Basculer vers la fenêtre de Contoso-HBase-UE RDP.
2. Ouvrez une invite de commande.
3. Exécutez la commande ping :

        ping headnode0.[DNS suffix of Contoso-HBase-US]

    Le protocole de l’ICM est activé sur les nœuds de travailleur des clusters HBase

4. Ne fermez pas la session RDP. Vous aurez toujours besoin plus loin dans le didacticiel.
5. Répétez la même procédure pour faire un ping le headnode0 de l’UE HBase Contoso de Contoso-HBase-US.

>[AZURE.IMPORTANT] DNS doit travailler avant de passer aux étapes suivantes.

## <a name="enable-replication-between-hbase-tables"></a>Activer la réplication entre les tables de HBase

Maintenant, vous pouvez créer un exemple de table de HBase, activer la réplication et puis la tester avec des données. L’exemple de table que vous utilisez a deux familles de colonne : personnel et Office. 

Dans ce didacticiel, vous effectuerez l’Europe HBase du cluster le cluster source et que le cluster de l’US HBase que le cluster de destination.

Créer des tables de HBase avec les mêmes noms et les familles de colonne sur la source et la destination des clusters, pour que le cluster de destination sache où stocker les données qu’il reçoit. Pour plus d’informations sur l’utilisation de l’interpréteur de commandes HBase, reportez-vous à la section [mise en route de HBase Apache dans HDInsight][hdinsight-hbase-get-started].

**Pour créer une table HBase sur Contoso-HBase-UE**

1. Basculez vers la fenêtre RDP **Contoso-HBase-UE** .
2. À partir du bureau, cliquez sur **ligne de commande Hadoop**.
2. Changer le dossier vers le répertoire de base de HBase :

        cd %HBASE_HOME%\bin
3. Ouvrez le shell HBase :

        hbase shell
4. Créer une table HBase :

        create 'Contacts', 'Personal', 'Office'
5. Ne fermez pas la session RDP ni la fenêtre de ligne de commande Hadoop. Vous aurez toujours besoin plus loin dans le didacticiel.
    
**Pour créer une table HBase sur HBase-Contoso-États-Unis**

- Répétez les mêmes étapes pour créer la même table de Contoso-HBase-US.


**Ajouter Contoso-HBase-US comme un homologue de réplication**

1. Basculer vers la fenêtre RDP de **Contso-HBase_EU** .
2. À partir de la fenêtre du shell HBase, ajoutez le cluster de destination (Contoso-HBase-US) comme un homologue, par exemple :

        add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

    Dans l’exemple, le suffixe de domaine est *contoso-hbase-us.d4.internal.cloudapp.net*. Vous devez mettre à jour pour correspondre à votre suffixe de domaine du cluster nous HBase. Assurez-vous qu’il n’existe pas d’espaces entre les noms d’hôtes.

**Pour configurer chaque famille de colonne doivent être répliquées sur le cluster source**

1. À partir de la fenêtre du shell HBase de la session RDP de **Contso-HBase-UE** , configurer la famille de chaque colonne à répliquer :

        disable 'Contacts'
        alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
        alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
        enable 'Contacts'

**En bloc des données de téléchargement dans la table HBase**

Un exemple de fichier de données a été chargé dans un conteneur d’Azure Blob publique avec l’URL suivante :

        wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

Le contenu du fichier :

        8396    Calvin Raji 230-555-0191    5415 San Gabriel Dr.
        16600   Karen Wu    646-555-0113    9265 La Paz
        4324    Karl Xie    508-555-0163    4912 La Vuelta
        16891   Jonathan Jackson    674-555-0110    40 Ellis St.
        3273    Miguel Miller   397-555-0155    6696 Anchor Drive
        3588    Osarumwense Agbonile    592-555-0152    1873 Lion Circle
        10272   Julia Lee   870-555-0110    3148 Rose Street
        4868    Jose Hayes  599-555-0171    793 Crawford Street
        4761    Caleb Alexander 670-555-0141    4775 Kentucky Dr.
        16443   Terry Chander   998-555-0171    771 Northridge Drive

Vous pouvez télécharger le même fichier de données dans votre cluster HBase et importer les données à partir de là.

1. Basculez vers la fenêtre RDP **Contoso-HBase-UE** .
2. À partir du bureau, cliquez sur **ligne de commande Hadoop**.
3. Changer le dossier vers le répertoire de base de HBase :

        cd %HBASE_HOME%\bin

4. charger les données :

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## <a name="verify-that-data-replication-is-taking-place"></a>Vérifiez que la réplication de données est en cours

Vous pouvez vérifier que la réplication a lieu en analysant les tables à partir de deux clusters avec les commandes de shell HBase suivantes :

        Scan 'Contacts'


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à configurer une réplication de HBase sur deux centres de données. Pour en savoir plus sur les HDInsight et HBase, voir :

- [Page de service de HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Documentation de HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Mise en route de HBase Apache dans HDInsight][hdinsight-hbase-get-started]
- [Vue d’ensemble de l’HDInsight HBase][hdinsight-hbase-overview]
- [Configurer des clusters HBase sur un réseau virtuel d’Azure][hdinsight-hbase-provision-vnet]
- [Analyse en temps réel sentiment Twitter avec HBase][hdinsight-hbase-twitter-sentiment]
- [Analyse des données de capteur avec tempête et HBase dans les HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
