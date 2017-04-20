<properties
    pageTitle="Application de fils d’Hadoop Access se connecte par programme | Microsoft Azure"
    description="Application Access se connecte par programme un cluster Hadoop dans HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Application de fils Access ouvre une session sur HDInsight de basées sur Windows

Cette rubrique explique comment accéder aux journaux des applications de fils (encore une autre ressource négociateur) terminés sur un cluster Hadoop dans Azure HDInsight

> [AZURE.NOTE] Les informations contenues dans ce document s’applique uniquement aux clusters de HDInsight de basées sur Windows. Pour des informations sur l’accès à des fils ouvre une session sur les clusters basés sur Linux de HDInsight, reportez-vous à la section [application de fils d’accès de session basé sur Linux d’Hadoop sur HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

### <a name="prerequisites"></a>Conditions préalables

- Un cluster d’HDInsight de basées sur Windows.  Voir [clusters fonctionnant sous Windows de créer de Hadoop dans HDInsight](hdinsight-provision-clusters.md).


## <a name="yarn-timeline-server"></a>Serveur de montage de fils

Les <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Fils de barre de Planning Server</a> fournit des informations génériques sur les applications terminées ainsi que les informations spécifiques à l’infrastructure application par le biais de deux interfaces différentes. En particulier :

* Stockage et récupération des informations d’application générique sur des clusters de HDInsight a été activé avec la version 3.1.1.374 ou ultérieure.
* Le composant d’informations spécifiques à l’infrastructure application de la barre de Planning Server n’est pas actuellement disponible sur les clusters de HDInsight.


Les types suivants de données incluent des informations génériques sur les applications :

* L’ID d’application, un identificateur unique d’une application
* L’utilisateur qui a démarré l’application.
* Informations sur les tentatives effectuées pour exécuter l’application
* Les conteneurs utilisés par toute tentative par une application donnée

Sur vos clusters HDInsight, ces informations seront stockées par le Gestionnaire de ressources Azure à un magasin de l’historique dans le conteneur par défaut de votre compte de stockage Azure par défaut. Ces données génériques sur les applications terminées peuvent être récupérées via une API REST :

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Les journaux et les applications des fils

FILS prend en charge plusieurs modèles de programmation (MapReduce un d’eux étant) en découplant la gestion des ressources de planification/analyse d’application. Cela s’effectue via un global *ResourceManager* (RM), noeud de travail *NodeManagers* (NMs) et par application *ApplicationMasters* (AMs). L’AM par application négocie des ressources (processeur, mémoire, disque, réseau) pour l’exécution de votre application avec le Gestionnaire de ressources. Le Gestionnaire de ressources travaille avec NMs accorder ces ressources, qui sont accordées en tant que *conteneurs*. L’AM est responsable du suivi de la progression des conteneurs qui lui est assignée par le Gestionnaire de ressources. Une application peut nécessiter de nombreux conteneurs en fonction de la nature de l’application.

En outre, chaque application peut consister en plusieurs *tentatives d’application* afin de terminer en présence de pannes ou à la perte de communication entre un AM et un gestionnaire de ressources. Par conséquent, les conteneurs sont accordés à une tentative spécifique d’une application. Dans un sens, un conteneur fournit le contexte pour l’unité de base du travail effectué par une application de fils, et tout le travail est effectué dans le contexte d’un conteneur est effectué sur le nœud de travail unique sur lequel le conteneur a été alloué. Consultez [Les Concepts de fils] [ YARN-concepts] pour référence ultérieure.

Journaux de l’application (et les journaux de conteneur associées) sont essentiels dans le débogage des applications Hadoop problématiques. FILS fournit un cadre agréable de collecte, d’agrégation et de stocker les journaux d’application avec l' [Agrégation de journaux] [ log-aggregation] fonctionnalité. La fonction d’agrégation de journaux permet l’accès aux journaux d’application plus déterministe, car il regroupe des journaux sur tous les conteneurs sur un nœud du travailleur et les stocke sous la forme d’un fichier journal agrégées par noeud de travail sur le système de fichiers par défaut, une fois une application terminée. Votre application peut utiliser des centaines ou des milliers de conteneurs, mais tous les conteneurs qui s’exécutés sur un nœud de travail unique dans les journaux seront toujours agrégées dans un seul fichier, ce qui résulte dans un fichier journal par nœud de travail utilisé par votre application. Agrégation de journal est activée par défaut sur les clusters de HDInsight (version 3.0 et ultérieure), et agrégées journaux se trouvent dans le conteneur par défaut de votre cluster à l’emplacement suivant :

    wasbs:///app-logs/<user>/logs/<applicationId>

Emplacement, *l’utilisateur* est le nom de l’utilisateur qui a démarré l’application et *applicationId* est l’identificateur unique d’une application, tel qu’assigné par le RM de fils.

Les journaux agrégées ne sont pas lisibles directement, comme elles sont écrites dans un [TFile]de[T-file], [au format binaire] [ binary-format] indexé par conteneur. FILS fournit des outils CLI pour vider ces journaux au format texte brut pour les applications ou les conteneurs d’intérêt. Vous pouvez afficher ces journaux texte brut en exécutant un du fil suivant des commandes directement sur les nœuds de cluster (après vous être connecté à celui-ci via RDP) :

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>Interface utilisateur de ResourceManager fils

L’interface utilisateur de fils ResourceManager s’exécute sur le headnode de cluster et sont accessibles via le tableau de bord de portail Azure : 

1. Connectez-vous au [portail Azure](https://portal.azure.com/). 
2. Dans le menu de gauche, cliquez sur **Parcourir**, cliquez sur **HDInsight Clusters**, un cluster Windows que vous souhaitez accéder aux journaux d’application de fils.
3. Dans le menu supérieur, cliquez sur **tableau de bord**. Vous verrez une page ouverte dans une nouvelle fenêtre de navigateur onglet appelé **Console de requête HDInsight**.
4. À partir de la **Console de requête HDInsight**, cliquez sur **L’interface utilisateur de fils**.




[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
