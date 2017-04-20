<properties
    pageTitle="Surveiller les clusters Hadoop dans HDInsight à l’aide de l’API Ambari | Microsoft Azure"
    description="Utilisez les Apache Ambari APIs pour la création, la gestion et l’analyse des clusters d’Hadoop. Les API et les outils d’opérateur intuitif masque la complexité de Hadoop."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    editor="cgronlun"
    manager="jhubbard"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Surveiller les clusters Hadoop dans HDInsight à l’aide de l’API Ambari

Découvrez comment surveiller les clusters HDInsight à l’aide de Ambari APIs.

> [AZURE.NOTE] Les informations contenues dans cet article sont principalement pour les clusters basés sur Windows de HDInsight, qui fournit une version en lecture seule de l’API REST de Ambari. Pour les clusters basés sur Linux, consultez [Hadoop de gérer les clusters à l’aide de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="what-is-ambari"></a>Nouveautés Ambari

[Apache Ambari] [ ambari-home] est utilisé pour la mise en service, la gestion et l’analyse des clusters de Apache Hadoop. Il inclut un ensemble intuitif d’outils de l’opérateur et un solide ensemble d’API qui masque la complexité de Hadoop, simplifiant l’opération des clusters. Pour plus d’informations sur les API, reportez-vous à la section [Référence de l’API Ambari][ambari-api-reference]. 

HDInsight prend actuellement en charge uniquement la fonctionnalité de surveillance Ambari. Ambari API 1.0 est pris en charge par les clusters de version 3.0 et 2.1 HDInsight. Cet article traite de l’accès au APIs Ambari sur les clusters de version 3.1 et 2.1 HDInsight. La différence principale entre les deux est que certains composants ont changé avec l’introduction de nouvelles fonctionnalités (par exemple, le serveur de l’historique des travaux). 

**Conditions préalables**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Une station de travail avec PowerShell d’Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- (Facultatif) [coin] [curl]. Pour l’installer, consultez le [coin des versions et des téléchargements][curl-download].

    >[AZURE.NOTE] Quand utiliser la commande cURL dans Windows, utilisez des guillemets doubles plutôt que des guillemets simples pour les valeurs d’option.

- **HDInsight d’Azure un cluster**. Pour obtenir des instructions sur la mise en service de cluster, consultez [mise en route de HDInsight] [ hdinsight-get-started] ou [clusters de disposition HDInsight][hdinsight-provision]. Vous devez les données suivantes pour parcourir le didacticiel :

    Propriété de cluster|Nom de la variable de PowerShell Azure|Valeur|Description
    ---|---|---|---
    Nom du cluster HDInsight|$clusterName||Le nom de votre cluster de HDInsight.
    Nom d’utilisateur du cluster|$clusterUsername||Nom d’utilisateur de cluster spécifié lorsque le cluster a été créé.
    Mot de passe de cluster|$clusterPassword||Passe du cluster.

    >[AZURE.NOTE] Remplir les valeurs de la table. Cela sera utile pour passer par ce didacticiel.

## <a name="jump-start"></a>Début de saut

Il existe plusieurs manières d’utiliser Ambari pour analyser les clusters de HDInsight.

**Utilisation de PowerShell Azure**

L’exemple suivant est un script PowerShell d’Azure pour obtenir les informations de suivi de projet MapReduce *dans un cluster HDInsight 3.1.*  La différence principale est que nous extrayons ces détails par le service de fils (plutôt que MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Voici un script PowerShell d’Azure pour obtenir le MapReduce travail suivi d’informations *dans un cluster HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

Le résultat est :

![Sortie de Jobtracker][img-jobtracker-output]

**Utilisez courbée**

Voici un exemple de l’obtention des informations de cluster à l’aide de la courbure doit :

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

Le résultat est :

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Pour le 10/8/2014 mise à jour**:

Lorsque vous utilisez le point de terminaison Ambari, « https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} », le champ *host_name* renvoie le nom de domaine pleinement qualifié (FQDN) du nœud au lieu du nom d’hôte. Avant la version 10/8/2014, cet exemple retournée simplement «**headnode0**». Après la publication du 10/8/2014, vous obtenez le nom de domaine complet «**headnode0. { .Azurehdinsight de ClusterDNS} .net**«, comme illustré dans l’exemple précédent. Cette modification était nécessaire pour faciliter des scénarios où plusieurs types de cluster (par exemple, HBase et Hadoop) peuvent être déployés dans un réseau virtuel (VNET). Cela se produit, par exemple, lors de l’utilisation de HBase comme une plate-forme de back-end pour Hadoop.

## <a name="ambari-monitoring-apis"></a>API d’analyse de Ambari

Le tableau suivant répertorie certaines le plus courant Ambari contrôle les appels API. Pour plus d’informations sur l’API, voir [Référence de l’API Ambari][ambari-api-reference].

Appel d’API du moniteur|URI|Description
---|---|---
Obtenir des clusters|`/api/v1/clusters`|
Obtenir des informations sur le cluster.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net`|clusters, les services, les hôtes
Obtenir des services|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`|Les services comprennent : très, mapreduce
Obtenir des informations sur les services.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`|
Obtenir les composants de service|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`|TRÈS : namenode, datanode<br/>MapReduce :, jobtracker ; tasktracker
Obtenir des informations sur le composant.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`|ServiceComponentInfo, les composants de l’hôte, les mesures
Obtenir des hôtes|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`|headnode0, workernode0
Obtenir les informations de l’hôte.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>`|
Obtenir les composants de l’hôte|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`|namenode, resourcemanager
Obtenir des informations sur le composant hôte.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`|HostRoles, composant, hôte, mesures
Obtenir les configurations|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations`|Types de configuration : core-site, site très, mapred-site, le site de la ruche
Obtenir des informations de configuration.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>`|Types de configuration : core-site, site très, mapred-site, le site de la ruche


##<a name="next-steps"></a>Étapes suivantes

Maintenant, vous avez appris comment utiliser le contrôle les appels API de Ambari. Pour plus d’informations, voir :

- [Gérer les clusters HDInsight via le portail Azure][hdinsight-admin-portal]
- [Gérer les clusters HDInsight à l’aide de PowerShell d’Azure][hdinsight-admin-powershell]
- [Gérer les clusters HDInsight à l’aide d’interface de ligne de commande][hdinsight-admin-cli]
- [Documentation de HDInsight][hdinsight-documentation]
- [Mise en route de HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
