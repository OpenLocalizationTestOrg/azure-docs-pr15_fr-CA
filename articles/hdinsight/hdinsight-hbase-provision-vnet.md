<properties
    pageTitle="Créer des clusters de HBase dans un RESEAU virtuel | Microsoft Azure"
    description="Mise en route à l’aide de HBase dans HDInsight d’Azure. Apprenez à créer des clusters HDInsight HBase sur un réseau virtuel d’Azure."
    keywords=""
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
   ms.date="10/18/2016"
   ms.author="jgao"/>

# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Créer des clusters d’HBase dans réseau virtuel d’Azure 

Apprenez à créer des clusters d’Azure HDInsight HBase dans un [Réseau virtuel d’Azure][1].

Avec l’intégration de réseau virtuel, HBase les clusters peuvent être déployés au même réseau virtuel que vos applications afin que les applications puissent communiquer directement avec HBase. Les avantages incluent :

- Connectivité directe de l’application web pour les nœuds de cluster, HBase, ce qui permet la communication via la procédure distante de HBase Java appeler les API (RPC).
- Amélioration des performances en n’ayant ne pas votre trafic de passer par plusieurs passerelles et les programmes d’équilibrage de charge.
- La possibilité de traiter des informations sensibles de manière plus sécurisée sans exposer un point de terminaison public.

###<a name="prerequisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Une station de travail avec PowerShell d’Azure**. Reportez-vous à la section [installation et utilisation de PowerShell d’Azure](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). 

## <a name="create-hbase-cluster-into-virtual-network"></a>Créer des clusters HBase dans un réseau virtuel

Dans cette section, vous allez créer un cluster basé sur Linux de HBase avec le compte de stockage Azure dépendant dans un réseau virtuel Azure à l’aide d’un [modèle de gestionnaire de ressources Azure](../resource-group-template-deploy.md). Pour d’autres méthodes de création de cluster et la compréhension les paramètres, reportez-vous à la section [HDInsight de créer des clusters](hdinsight-hadoop-provision-linux-clusters.md). Pour plus d’informations sur l’utilisation d’un modèle pour créer des clusters d’Hadoop dans HDInsight, reportez-vous à la section [Hadoop de créer des clusters dans HDInsight à l’aide des modèles du Gestionnaire de ressources Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

> [AZURE.NOTE] Certaines propriétés ont été codés en dur dans le modèle. Par exemple :
>
> * __Emplacement__: américains Extrême-Orient 2
> * Version de __Cluster : 3.4
> * __Nombre de nœuds de cluster travailleur__: 4
> * __Compte de stockage par défaut__: &lt;nom de Cluster > stocker
> * __Nom du réseau virtuel__: &lt;nom de Cluster >-vnet
> * __Espace d’adressage virtuel réseau__: 10.0.0.0/16
> * __Nom du sous-réseau__: par défaut
> * __Plage d’adresses de sous-réseau__: 10.0.0.0/24
>
> &lt;Nom de cluster > est remplacé par le nom de cluster que vous fournissez lors de l’utilisation du modèle.

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure. Le modèle se trouve dans un conteneur blob publique. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir de la blade de **déploiement de personnalisé** , entrez ce qui suit :

    - **Abonnement**: sélectionnez un abonnement Azure utilisé pour créer le cluster de HDInsight, le compte de stockage dépendant et le réseau virtuel Azure.
    - **Groupe de ressources**: sélectionnez **Créer nouveau**et spécifiez un nouveau nom de groupe de ressources.
    - **Emplacement**: sélectionnez un emplacement pour le groupe de ressources.
    - **Nom_Cluster**: entrez un nom pour le cluster Hadoop que vous allez créer.
    - **Mot de passe et le nom de connexion de cluster**: le nom d’ouverture de session par défaut est **admin**.
    - **Mot de passe et nom d’utilisateur SSH**: le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez le renommer. 
    - **J’accepte les termes et les conditions susmentionnées**: (Sélectionner)
    

3. Cliquez sur **achat**. Il faut environ 20 minutes environ pour créer un cluster. Une fois que le cluster est créé, vous pouvez cliquer sur la lame de cluster dans le portail pour l’ouvrir.

Après avoir terminé le didacticiel, vous pouvez souhaiter supprimer le cluster. Avec HDInsight, vos données sont stockées dans le stockage Azure, donc vous pouvez supprimer en toute sécurité un cluster lorsqu’il n’est pas en cours d’utilisation. Vous êtes également chargé pour un cluster de HDInsight, même lorsqu’il n’est pas en cours d’utilisation. Dans la mesure où les frais pour le cluster sont bien plus que les frais de stockage, il est économique judicieux de supprimer les clusters lorsqu’ils ne sont pas en cours d’utilisation. Pour les instructions de la suppression d’un cluster, consultez [Hadoop de gérer des clusters dans HDInsight en utilisant le portail Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Pour commencer à utiliser votre nouveau cluster HBase, vous pouvez utiliser les procédures de [mise en route de HBase avec Hadoop dans HDInsight](hdinsight-hbase-tutorial-get-started.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Connectez-vous au cluster de HBase à l’aide des API de RPC de HBase Java

1.  Création d’une infrastructure en tant que service (IaaS) virtual machine dans le même réseau virtuel Azure et le même sous-réseau. Pour obtenir des instructions sur la création d’un ordinateur virtuel IaaS, voir [créer un ordinateur virtuel exécutant Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Lorsqu’ils suivent les étapes décrites dans ce document, vous devez utiliser les éléments suivants pour la configuration du réseau :

    - __Réseaux virtuels__: &lt;nom de Cluster >-vnet
    - __Sous-réseau__: par défaut

    > [AZURE.IMPORTANT] Remplacer &lt;nom de Cluster > avec le nom que vous avez utilisé lors de la création du cluster HDInsight des étapes précédentes.

    À l’aide de ces valeurs configurera l’ordinateur virtuel pour utiliser le même réseau virtuel et sous-réseau que le cluster HDInsight. Cela leur permet de communiquer directement entre eux.

2.  Lorsque vous utilisez une application Java pour se connecter à distance à HBase, vous devez utiliser le nom de domaine pleinement qualifié (FQDN). Pour cela, vous devez obtenir le suffixe DNS spécifique à la connexion du cluster HBase. Pour ce faire, vous pouvez utiliser l’une des méthodes suivantes :

    * Utilisez un navigateur Web pour effectuer un appel de Ambari :
    
        Accédez à https://&lt;nom_cluster >.azurehdinsight.net/api/v1/clusters/&lt;nomcluster > / hosts?minimal_response = true. En fait, un fichier JSON avec les suffixes DNS.

    * Utilisez le site Web de Ambari :

        1. Accédez à https://&lt;nomcluster >. azurehdinsight.net.
        2. Dans le menu supérieur, cliquez sur **Hosts** .

    * Courbure doit utiliser pour effectuer des appels du reste :

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        Dans les Notation JSON (JavaScript Object) données retournées, trouver l’entrée de « host_name ». Il contient le nom de domaine complet pour les nœuds dans le cluster. Par exemple :

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        La partie de début de nom de domaine avec le nom du cluster est le suffixe DNS. Par exemple, mycluster.b1.cloudapp.net.

    * Utilisation de PowerShell Azure
    
        Le script PowerShell d’Azure suivant permet d’enregistrer la fonction **Get-ClusterDetail** , qui peut être utilisée pour renvoyer le suffixe DNS :

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS
                 Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
                .Description
                 This command shows the following 4 properties of an HDInsight cluster:
                 1. ZookeeperQuorum (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (supports only HBase type cluster)
                    Shows a list of host FQDNs that run the HBase REST server.
                 4. FQDNSuffix (supports all cluster types)
                    Shows the FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of HBase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs that run the HBase REST server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows the FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        Après avoir exécuté le script PowerShell d’Azure, utilisez la commande suivante pour renvoyer le suffixe DNS à l’aide de la fonction **Get-ClusterDetail** . Spécifier le nom de votre nom de cluster HDInsight HBase, administrateur et mot de passe administrateur lors de l’utilisation de cette commande.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        Ceci renvoie le suffixe DNS. Par exemple, **yourclustername.b4.internal.cloudapp.net**.

    * Utiliser le protocole RDP
    
        Vous pouvez également utiliser le Bureau à distance pour se connecter au cluster HBase (vous serez connecté au nœud principal), exécutez **ipconfig** à partir d’une invite de commande pour obtenir le suffixe DNS. Pour obtenir des instructions sur l’activation du protocole RDP (Remote Desktop) et la connexion au cluster en utilisant le protocole RDP, consultez [Hadoop de gérer des clusters dans HDInsight à l’aide du portail Azure][hdinsight-admin-portal].
        
        ![hdinsight.hbase.DNS.surffix][img-dns-surffix]


<!--
3.  Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Pour vérifier que la machine virtuelle peut communiquer avec le cluster HBase, utilisez la commande `ping headnode0.<dns suffix>` de la machine virtuelle. Par exemple, ping headnode0.mycluster.b1.cloudapp.net.

Pour utiliser ces informations dans une application Java, vous pouvez suivre les étapes de l' [Utilisation de Maven pour générer des applications Java qui utilisent HBase avec HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) pour créer une application. Pour que l’application de se connecter à un serveur distant de HBase, modifiez le fichier de **hbase-site.xml** dans cet exemple pour utiliser le nom de domaine complet pour soigneur. Par exemple :

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [AZURE.NOTE] Pour plus d’informations sur la résolution de noms dans Azure réseaux virtuels, y compris l’utilisation de votre serveur DNS, reportez-vous à [La résolution de nom (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment créer un cluster de HBase. Pour plus d’informations, voir :

- [Mise en route de HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configurer la réplication de l’HBase de HDInsight](hdinsight-hbase-geo-replication.md)
- [Créer des clusters d’Hadoop dans HDInsight](hdinsight-provision-clusters.md)
- [Mise en route de HBase avec Hadoop dans HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Analyser le sentiment Twitter avec HBase dans HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Vue d’ensemble du réseau virtuel][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Informations de disposition pour le nouveau cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Action de Script permet de personnaliser un cluster HBase"

[azure-preview-portal]: https://portal.azure.com
