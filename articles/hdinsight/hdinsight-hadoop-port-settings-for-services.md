<properties
pageTitle="Ports utilisés par HDInsight | Azure"
description="Une liste des ports utilisés par les services Hadoop sur HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/03/2016"
ms.author="larryfr"/>

# <a name="ports-and-uris-used-by-hdinsight"></a>Les ports et les URI utilisés par HDInsight

Ce document fournit une liste des ports utilisés par les services d’Hadoop en cours d’exécution sur les clusters basés sur Linux de HDInsight. Il fournit également des informations sur les ports utilisés pour se connecter au cluster à l’aide de SSH.

## <a name="public-ports-vs-non-public-ports"></a>Ports publics et non publics ports

Linux HDInsight grappes de serveurs expose uniquement les trois ports publiquement sur internet. 22, 23 et 443. Ceux-ci sont utilisés pour accéder en toute sécurité du cluster à l’aide de SSH et services exposés via le protocole HTTPS sécurisé.

En interne, HDInsight est mis en œuvre par plusieurs ordinateurs virtuels Azure (les nœuds dans le cluster,) en cours d’exécution sur un réseau virtuel d’Azure. À partir de dans le réseau virtuel, vous pouvez accéder aux ports ne pas exposés sur internet. Par exemple, si vous vous connectez à l’un des nœuds du siège à l’aide de SSH, du nœud de tête vous pouvez puis accéder directement aux services s’exécutant sur les nœuds du cluster.

> [AZURE.IMPORTANT] Lorsque vous créez un cluster HDInsight, si vous ne spécifiez pas un réseau virtuel Azure sous la forme d’une option de configuration, un est créé ; Toutefois, vous ne peut pas joindre des autres ordinateurs (par exemple, les autres ordinateurs virtuels de Azure ou votre machine de développement client,) à ce créé automatiquement le réseau virtuel. 

Pour joindre d’autres ordinateurs sur le réseau virtuel, vous devez d’abord créer le réseau virtuel et puis de le spécifier lors de la création de votre cluster de HDInsight. Pour plus d’informations, reportez-vous à la section [HDInsight d’étendre les fonctionnalités à l’aide d’un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Ports publics

Tous les nœuds dans un cluster de HDInsight se trouvent dans un réseau virtuel d’Azure et ne sont pas accessibles directement à partir d’internet. Une passerelle publique fournit un accès à internet sur les ports suivants, qui sont communes à tous les types de cluster HDInsight.

| Service | Port | Protocole | Description |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | Connecte les clients à sshd sur le principal headnode. Voir [utiliser SSH avec HDInsight de basé sur Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd | 22 | SSH | Connecte les clients à sshd sur le nœud de bord (HDInsight Premium uniquement). Reportez-vous à la section [mise en route à l’aide du serveur R HDInsight](hdinsight-hadoop-r-server-get-started.md) |
| sshd | 23 | SSH | Connecte les clients à sshd sur le secondaire headnode. Voir [utiliser SSH avec HDInsight de basé sur Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari | 443 | HTTPS | Interface utilisateur web de Ambari. Reportez-vous à la section [Gérer les HDInsight à l’aide de l’interface utilisateur Web de Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari | 443 | HTTPS | API REST de Ambari. Reportez-vous à la section [Gérer les HDInsight à l’aide de l’API REST de Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat | 443 | HTTPS | API du reste de HCatalog. Voir [utiliser la ruche bouclé](hdinsight-hadoop-use-pig-curl.md), [Utilisez porc bouclé](hdinsight-hadoop-use-pig-curl.md), [Utilisez MapReduce bouclé](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 | 443 | ODBC | Se connecte à la ruche à l’aide de ODBC. Reportez-vous à [Excel de se connecter à HDInsight avec le pilote ODBC de Microsoft](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 | 443 | JDBC | Se connecte à la ruche à l’aide de JDBC. Voir [se connecter à la ruche sur HDInsight en utilisant le pilote JDBC de la ruche](hdinsight-connect-hive-jdbc-driver.md) |

Les éléments suivants sont disponibles pour les types de cluster spécifique :

| Service | Port | Protocole |Type de cluster | Description |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | API REST de HBase. Reportez-vous à la section [mise en route à l’aide de HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Livy | 443 | HTTPS |  Allumage | API de reste d’allumage. Voir [allumage de soumettre des travaux à distance à l’aide de Livy](hdinsight-apache-spark-livy-rest-interface.md) |
| Orage | 443 | HTTPS | Orage | Interface utilisateur web de Storm. Consultez [déployer et gérer des topologies de tempête sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="authentication"></a>Authentification

Tous les services exposés publiquement sur internet doivent être authentifiés :

| Port | Informations d’identification |
| ---- | ----------- |
| 22 ou 23 | Les informations d’identification utilisateur SSH spécifiées lors de la création du cluster |
| 443 | Le nom de connexion (par défaut : administrateur,) et le mot de passe qui ont été définies lors de la création du cluster |

## <a name="non-public-ports"></a>Ports non publics

> [AZURE.NOTE] Certains services sont uniquement disponibles sur les types de cluster spécifique. Par exemple, HBase est uniquement disponible sur les types de cluster HBase.

### <a name="hdfs-ports"></a>Ports très

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- | 
| Interface utilisateur web de NameNode | Nœuds de tête | 30070 | HTTPS | L’interface utilisateur pour afficher l’état en cours Web |
| Service de métadonnées NameNode | nœuds de tête | 8020 | IPC | Métadonnées de système de fichiers 
| DataNode | Tous les nœuds du travailleur | 30075 | HTTPS | Interface utilisateur Web pour afficher le statut, les journaux, etc.. |
| DataNode | Tous les nœuds du travailleur | 30010 | &nbsp; | Transfert de données |
| DataNode | Tous les nœuds du travailleur | 30020 | IPC | Opérations de métadonnées |
| NameNode secondaire | Nœuds de tête | 50090 | HTTP | Point de contrôle pour les métadonnées de la NameNode |

### <a name="yarn-ports"></a>Ports de fils

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| L’interface utilisateur de gestionnaire de ressources web | Nœuds de tête | 8088 | HTTP | Interface utilisateur pour le Gestionnaire de ressources Web |
| L’interface utilisateur de gestionnaire de ressources web | Nœuds de tête | 8090 | HTTPS | Interface utilisateur pour le Gestionnaire de ressources Web |
| Interface d’administration du Gestionnaire de ressources | nœuds de tête | 8141 | IPC | Pour les soumissions de l’application (ruche, serveur de ruche, porc, etc..) |
| Gestionnaire de ressources du planificateur | nœuds de tête | 8030 | HTTP | Interface d’administration |
| Interface de Gestionnaire des ressources d’application | nœuds de tête | 8050 | HTTP |Adresse de l’interface du Gestionnaire d’applications |
| NodeManager | Tous les nœuds du travailleur | 30050 | &nbsp; | L’adresse du Gestionnaire de conteneur |
| Interface utilisateur web de NodeManager | Tous les nœuds du travailleur | 30060 | HTTP | Interface du Gestionnaire de ressources |
| Adresse de la chronologie | Nœuds de tête | 10200 | RPC | Le service RPC du service montage. |
| Interface utilisateur web de montage | Nœuds de tête | 8181 | HTTP | L’interface utilisateur du web service du montage |

### <a name="hive-ports"></a>Ports de la ruche

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | Nœuds de tête | 10001 | Thrift | Service de connexion par programmation à la ruche (Thrift/JDBC) |
| HiveServer | Nœuds de tête | 10000 | Thrift | Service de connexion par programmation à la ruche (Thrift/JDBC) |
| La ruche Metastore | Nœuds de tête | 9083 | Thrift | Service de connexion par programme aux métadonnées de ruche (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Ports WebHCat

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Serveur de WebHCat | Nœuds de tête | 30111 | HTTP | API Web sur HCatalog et d’autres services d’Hadoop |

### <a name="mapreduce-ports"></a>MapReduce ports

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | Nœuds de tête | 19888 | HTTP | Interface utilisateur web de MapReduce JobHistory |
| JobHistory | Nœuds de tête | 10020 | &nbsp; | MapReduce JobHistory server |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | Sorties de mappage intermédiaire de transferts à la demande de réducteurs |

### <a name="oozie"></a>Oozie

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Serveur de Oozie | Nœuds de tête | 11000 | HTTP | URL de service de Oozie |
| Serveur de Oozie | Nœuds de tête | 11001 | HTTP | Port d’administration de Oozie |

### <a name="ambari-metrics"></a>Ambari métrique

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Application (historique) | Nœuds de tête | 6188 | HTTP | L’interface utilisateur du web service du montage |
| Application (historique) | Nœuds de tête | 30200 | RPC | L’interface utilisateur du web service du montage |

### <a name="hbase-ports"></a>Ports HBase

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | Nœuds de tête | 16000 | &nbsp; | &nbsp; |
| Informations sur l’interface utilisateur Web de HMaster | Nœuds de tête | 16010 | HTTP | Le port pour le site web de HBase masque l’interface utilisateur |
| Serveur de la région | Tous les nœuds du travailleur | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | Le port que les clients utilisent pour se connecter à soigneur |

### <a name="kafka-ports"></a>Ports de Kafka

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Service Broker  | Nœuds du travailleur | 9092 | [Protocole Kafka](http://kafka.apache.org/protocol.html) | Utilisé pour la communication client |
| &nbsp; | Nœuds de soigneur | 2181 | &nbsp; | Le port que les clients utilisent pour se connecter à soigneur |
