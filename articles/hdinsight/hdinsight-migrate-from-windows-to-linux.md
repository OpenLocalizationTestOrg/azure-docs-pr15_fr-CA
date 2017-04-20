<properties
pageTitle="Migration de HDInsight de basé sur Windows vers basé sur Linux de HDInsight | Azure"
description="Apprenez à migrer à partir d’un cluster basé sur Windows de HDInsight à un cluster basé sur Linux de HDInsight."
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
ms.date="10/28/2016"
ms.author="larryfr"/>

#<a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrer à partir d’un cluster basé sur Windows de HDInsight vers un cluster Linux

Lors de la HDInsight de basé sur Windows offre un moyen facile d’utiliser Hadoop dans le nuage, vous pouvez découvrir que vous avez besoin d’un cluster Linux pour tirer parti des outils et technologies qui sont requis pour votre solution. Beaucoup de choses dans l’écosystème Hadoop est développés sur des systèmes Linux, et certaines n’est peut-être pas disponible pour une utilisation avec HDInsight de basés sur Windows. En outre, de nombreux livres, des vidéos et autres documents de formation supposent que vous utilisez un système Linux lorsque vous travaillez avec Hadoop.

Ce document fournit des détails sur les différences entre les conseils sur la migration des charges de travail existants à un cluster Linux et HDInsight sous Windows et Linux.

> [AZURE.NOTE] HDInsight clusters utilisent Ubuntu à long terme (LTS) que le système d’exploitation pour les nœuds dans le cluster. Pour plus d’informations sur la version d’Ubuntu disponible avec HDInsight, ainsi que d’autres informations de contrôle de version de composant, consultez [versions de composants HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Tâches de migration

Le flux de travail général pour la migration est la suivante.

![Diagramme de flux de travail de migration](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1.  Lisez chaque section de ce document pour comprendre les modifications qui peuvent être requises lors de la migration de votre flux de travail existant, les travaux, etc. à un cluster basé sur Linux.

2.  Créer un cluster Linux comme un environnement d’assurance qualité/test. Pour plus d’informations sur la création d’un cluster basé sur Linux, consultez [basé sur Linux de créer des clusters dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3.  Copier les travaux existants, les sources de données et des récepteurs dans le nouvel environnement. Consultez les données de copie à la section d’environnement de test pour plus de détails.

4.  Effectuez les tests de validation pour vous assurer que vos travaux fonctionnent comme prévu sur le nouveau cluster.

Une fois que vous avez vérifié que tout fonctionne comme prévu, planifier une interruption pour la migration. Pendant ce temps d’indisponibilité, effectuez les actions suivantes.

1.  Sauvegarde toutes les données stockées en local sur les nœuds de cluster en régime transitoire. Par exemple, si vous avez des données stockées directement sur un nœud de tête.

2.  Supprimer le cluster basés sur Windows.

3.  Créer un cluster Linux à l’aide du même magasin de données par défaut utilisé par le cluster basés sur Windows. Ainsi, le nouveau cluster continuer à travailler sur vos données de production existante.

4.  Importer des données en régime transitoire que vous avez sauvegardé.

5.  Démarrer les travaux/continuer en utilisant le nouveau cluster.

### <a name="copy-data-to-the-test-environment"></a>Copier des données dans l’environnement de test

Il existe de nombreuses méthodes pour copier les données et les tâches, toutefois les deux décrits dans cette section sont directement les méthodes les plus simples à déplace les fichiers vers un cluster de test.

#### <a name="hdfs-dfs-copy"></a>Copie d’HDFS DFS

Vous pouvez utiliser la commande Hadoop HDFS pour copier directement des données à partir du stockage pour votre cluster de production existant, dans le stockage pour un nouveau cluster de test à l’aide de la procédure suivante.

1. Trouvez les informations stockage par défaut et le compte de conteneur pour votre cluster existant. Pour cela, à l’aide du script PowerShell d’Azure suivant.

        $clusterName="Your existing HDInsight cluster name"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
        write-host "Default container: $clusterInfo.DefaultStorageContainer"

2. Suivez les étapes dans les clusters basés sur Linux de créer dans le document HDInsight pour créer un nouvel environnement de test. Arrêter avant la création du cluster et sélectionnez à la place de **Configuration facultatives**.

3. À partir de la blade de Configuration facultative, permet de sélectionner **Les comptes de stockage lié**.

4. Sélectionnez **Ajouter une clé de stockage**et lorsque vous y êtes invité, sélectionnez le compte de stockage qui a été renvoyé par le script PowerShell à l’étape 1. Cliquez sur **Sélectionner** sur chaque lame pour les fermer. Enfin, créez le cluster.

5. Une fois que le cluster a été créé, vous connecter à l’aide de **SSH.** Si vous n’êtes pas familiarisé avec l’utilisation du protocole SSH avec HDInsight, consultez les articles suivants.

    * [Utiliser le protocole SSH avec basé sur Linux de HDInsight à partir de clients Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Utiliser le protocole SSH avec basé sur Linux de HDInsight à partir de clients Mac, Unix et Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

6. À partir de la session SSH, utilisez la commande suivante pour copier des fichiers à partir du compte de stockage pour le nouveau compte de stockage par défaut. Remplacez le conteneur et le compte du conteneur et les informations de compte retourné par le script PowerShell à l’étape 1. Remplacez le chemin d’accès aux données par le chemin d’accès à un fichier de données.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    [AZURE.NOTE] Si la structure du répertoire qui contient les données n’existe pas dans l’environnement de test, vous pouvez créer à l’aide de la commande suivante.

        hdfs dfs -mkdir -p /new/path/to/create

    Le `-p` commutateur permet la création de tous les répertoires dans le chemin d’accès.

#### <a name="direct-copy-between-azure-storage-blobs"></a>Copie directe entre le stockage Azure BLOB

Vous pouvez également utiliser le `Start-AzureStorageBlobCopy` applet de commande PowerShell de Azure pour copier des objets BLOB entre des comptes de stockage en dehors de HDInsight. Pour plus d’informations, consultez Comment gérer la section Azure BLOB à l’aide de PowerShell Azure avec stockage Azure.

##<a name="client-side-technologies"></a>Technologies côté client

En règle générale, les technologies côté client telles que [les applets de commande PowerShell d’Azure](../powershell-install-configure.md), [Azure CLI](../xplat-cli-install.md) ou le [Kit de développement .NET pour Hadoop](https://hadoopsdk.codeplex.com/) continuera à fonctionnent de la même façon avec les clusters basés sur Linux, comme ils s’appuient sur des API reste qui sont les mêmes dans les deux types de cluster du système d’exploitation.

##<a name="server-side-technologies"></a>Technologies côté serveur

Le tableau suivant fournit des conseils sur la migration des composants côté serveur qui sont spécifiques de Windows.

| Si vous utilisez cette technologie... | Effectuez cette opération... |
| ----- | ----- |
| **PowerShell** (les scripts côté serveur, y compris les Actions de Script utilisé lors de la création du cluster) | Réécrivez les scripts Bash. Pour les Actions de Script, consultez [basé sur Linux de personnaliser la HDInsight avec les Actions de Script](hdinsight-hadoop-customize-cluster-linux.md) et [développement d’action de Script pour HDInsight de fonctionnant sous Linux](hdinsight-hadoop-script-actions-linux.md). |
| **CLI Azure** (scripts côté serveur) | Tandis que la CLI Azure est disponible sous Linux, il n’est pas déjà installé sur les nœuds du cluster HDInsight head. Si vous avez besoin pour écrire des scripts côté serveur, reportez-vous à la section [installer CLI Azure](../xplat-cli-install.md) pour plus d’informations sur l’installation sur les plates-formes Linux. |
| **Composants .NET** | .NET n’est pas entièrement pris en charge sur les clusters basés sur Linux de HDInsight. Tempête de basé sur Linux sur HDInsight clusters créé après le 28/10/2017 prise en charge des topologies de tempête C# à l’aide de l’infrastructure SCP.NET. Prise en charge supplémentaire pour .NET est ajouté dans les futures mises à jour. |
| **Les composants Win32 ou une autre technologie Windows uniquement** | Conseils varient selon le composant ou la technologie ; vous serez peut-être en mesure de trouver une version compatible avec Linux, ou vous devrez peut-être rechercher une solution alternative ou réécrire ce composant. |

##<a name="cluster-creation"></a>Création d’un cluster

Cette section fournit des informations sur les différences de création du cluster.

### <a name="ssh-user"></a>SSH utilisateur

Linux HDInsight grappes de serveurs utilisent le protocole **SSH (Secure Shell)** pour fournir un accès distant aux nœuds du cluster. Contrairement aux clusters basés sur le Bureau à distance pour Windows, la plupart des clients SSH ne fournissent pas une expérience utilisateur graphique, mais fournit à la place une ligne de commande qui vous permet d’exécuter des commandes sur le cluster. Certains clients (tels que [MobaXterm](http://mobaxterm.mobatek.net/),) fournit un navigateur de système de fichiers graphiques en plus d’une télécommande de ligne de commande.

Lors de la création du cluster, vous devez fournir un utilisateur SSH et un **mot de passe** ou **un certificat de clé publique** pour l’authentification.

Nous recommandons d’utiliser le certificat de clé publique, telle qu’elle est plus sûre que l’utilisation d’un mot de passe. L’authentification par certificat fonctionne en générant une paire de clés publique/privée signée, puis fournir la clé publique lors de la création du cluster. Lors de la connexion au serveur à l’aide de SSH, la clé privée sur le client fournit l’authentification pour la connexion.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir :

- [Utiliser le protocole SSH avec HDInsight à partir de clients Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- [Utiliser le protocole SSH avec HDInsight à partir de clients Linux, Unix et OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="cluster-customization"></a>Personnalisation du cluster

**Actions de script** utilisées avec les clusters basés sur Linux doit être écrit dans les scripts Bash. Alors que les Actions de Script peuvent être utilisées lors de la création du cluster, pour les clusters basés sur Linux ils peuvent également être utilisé pour effectuer la personnalisation après qu’un cluster est disponible et en cours d’exécution. Pour plus d’informations, consultez [basé sur Linux de personnaliser la HDInsight avec les Actions de Script](hdinsight-hadoop-customize-cluster-linux.md) et [développement d’action de Script pour HDInsight de fonctionnant sous Linux](hdinsight-hadoop-script-actions-linux.md).

Une autre fonctionnalité de personnalisation est **d’amorçage**. Pour les clusters Windows, cela vous permet de spécifier l’emplacement des bibliothèques supplémentaires à utiliser avec la ruche. Après la création du cluster, ces bibliothèques sont automatiquement disponibles pour une utilisation avec des requêtes de ruche sans devoir utiliser de `ADD JAR`.

Données d’amorçage pour les clusters basés sur Linux ne fournissent pas cette fonctionnalité. Utilisez plutôt l’action de script documentée dans les [bibliothèques d’ajouter la ruche lors de la création du cluster](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Réseaux virtuels

HDInsight de base de Windows clusters fonctionnent uniquement avec les réseaux virtuels classique, tandis que les clusters sous Linux de HDInsight nécessitent des réseaux virtuels de gestionnaire de ressources. Si vous avez des ressources dans le cluster Linux-HDInsight doit se connecter à un réseau virtuel classique, voir [connexion d’un réseau virtuel classique à un gestionnaire de ressources des réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Pour plus d’informations sur la configuration requise pour l’utilisation de réseaux virtuels d’Azure avec HDInsight, reportez-vous à la section [HDInsight d’étendre les fonctionnalités à l’aide d’un réseau virtuel](hdinsight-extend-hadoop-virtual-network.md).

##<a name="management-and-monitoring"></a>Gestion et surveillance

La plupart des interfaces utilisateur que vous avez peut-être utilisé avec HDInsight basés sur Windows, telles que l’historique du travail ou de l’interface utilisateur de fils, web sont disponibles par l’intermédiaire de Ambari. En outre, la vue de la ruche Ambari fournit un moyen d’exécuter des requêtes de ruche à l’aide de votre navigateur web. L’interface utilisateur Web de Ambari est disponible sur les grappes de serveurs Linux à https://CLUSTERNAME.azurehdinsight.net.

Pour plus d’informations sur l’utilisation de Ambari, consultez les documents suivants :

- [Ambari Web](hdinsight-hadoop-manage-ambari.md)

- [API REST de Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alertes de Ambari

Ambari a un système d’alerte qui peut indiquer des problèmes potentiels avec le cluster. Les alertes s’affichent en tant qu’entrées de rouges ou jaunes dans l’interface utilisateur Web de Ambari, mais vous pouvez également récupérer par le biais de l’API REST.

> [AZURE.IMPORTANT] Ambari alertes indiquent qu’il *peut* y avoir un problème, pas qu’il *est* un problème. Par exemple, vous pouvez recevoir une alerte que HiveServer2 ne sont pas accessibles, même si vous pouvez y accéder normalement.
>
> De nombreuses alertes sont implémentées comme basé sur l’intervalle des requêtes par rapport à un service et attendent une réponse dans un délai spécifique. Afin que l’alerte ne signifie pas nécessairement que le service est arrêté, simplement qu’il n’a pas renvoyé les résultats dans le délai prévu.

En général, vous devez évaluer si une alerte se pose pendant une longue période, ou qu’il reflète les problèmes des utilisateurs qui ont été précédemment signalées avec le cluster avant d’entreprendre une action sur celui-ci.

##<a name="file-system-locations"></a>Emplacements des fichiers système

Le système de fichiers de cluster Linux se présente différemment les clusters basés sur Windows de HDInsight. Utilisez le tableau suivant pour les fichiers de recherche couramment utilisé.

| Je dois trouver... | Il se trouve en cours... |
| ----- | ----- |
| Configuration de | `/etc`. Par exemple,`/etc/hadoop/conf/core-site.xml` |
| Fichiers journaux | `/var/logs` |
| Plate-forme de données de Hortonworks (HDP) | `/usr/hdp`. Il existe deux répertoires situés ici, qui est la version actuelle de HDP (par exemple, `2.2.9.1-1`,) et `current`. Le `current` répertoire contient des liens symboliques vers les fichiers et répertoires situés dans le répertoire de numéro de version et est fournie comme un moyen pratique d’accéder aux fichiers HDP depuis la version numéro va changer dans la version HDP est mis à jour. |
| Hadoop-streaming.jar | `/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

En règle générale, si vous connaissez le nom du fichier, vous pouvez utiliser la commande suivante à partir d’une session SSH pour rechercher le chemin d’accès du fichier :

    find / -name FILENAME 2>/dev/null

Vous pouvez également utiliser des caractères génériques avec le nom de fichier. Par exemple, `find / -name *streaming*.jar 2>/dev/null` renvoie le chemin d’accès aux fichiers qui contiennent le mot « en continu » comme faisant partie du nom du fichier jar.

##<a name="hive-pig-and-mapreduce"></a>Ruche, porc et MapReduce

Porcs et MapReduce des charges de travail sont très similaires sur les clusters basés sur Linux, la principale différence est si vous utilisez le Bureau à distance pour vous connecter à un cluster Windows, et exécuter des travaux, vous allez utiliser SSH avec clusters basés sur Linux.

- [Utilisez des porcs avec SSH](hdinsight-hadoop-use-pig-ssh.md)

- [Utilisez MapReduce avec SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>La ruche

Le tableau suivant fournit des conseils sur la migration de vos charges de travail de ruche.

| Sur base Windows, utiliser... | Sur Linux... |
| ----- | ----- |
| **Éditeur de la ruche** | [Affichage de ruche dans Ambari](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;`Pour activer Tez | Tez est le moteur d’exécution par défaut pour les clusters basés sur Linux, afin que l’instruction set n’est plus nécessaire. |
| Fichiers CMD ou des scripts sur le serveur appelé dans le cadre d’un travail de la ruche | utiliser les scripts Bash |
| `hive`commande à partir du Bureau à distance | Utilisez [Beeline](hdinsight-hadoop-use-hive-beeline.md) ou [la ruche à partir d’une session SSH](hdinsight-hadoop-use-hive-ssh.md) |

##<a name="storm"></a>Orage

| Sur base Windows, utiliser... | Sur Linux... |
| ----- | ----- |
| Tableau de bord de tempête | Le tableau de bord de tempête n’est pas disponible. Consultez les [topologies de déployer et de gérer une tempête sur basé sur Linux de HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md) pour savoir comment soumettre des topologies |
| L’interface utilisateur de tempête | L’interface utilisateur de Storm est disponible à l’adresse https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio pour créer, déployer et gérer des topologies C# ou hybride | Visual Studio peut être utilisé pour créer, déployer et gérer C# (SCP.NET) ou les topologies hybrides sur basé sur Linux une tempête sur des clusters HDInsight créés après le 28/10/2017. |

##<a name="hbase"></a>HBase

Sur les clusters basés sur Linux, est le parent de znode de HBase `/hbase-unsecure`. Vous devez le définir dans la configuration d’un client Java les applications qui utilisent les API de Java native HBase.

Pour un exemple de client qui définit cette valeur, consultez [créer une application basée sur Java de HBase](hdinsight-hbase-build-java-maven.md) .

##<a name="spark"></a>Allumage

Érosion clusters étaient disponibles sur les clusters de Windows lors de l’aperçu ; Toutefois, pour libération, allumage n’est disponible avec les clusters basés sur Linux. Il n’y a aucun chemin d’accès de migration d’un cluster d’aperçu basés sur Windows une étincelle à un cluster basé sur Linux une étincelle de version.

##<a name="known-issues"></a>Problèmes connus

### <a name="azure-data-factory-custom-net-activities"></a>Activités .NET personnalisées en usine de données Azure

Les activités .NET personnalisées en usine de données Azure ne sont actuellement pas pris en charge sur les clusters basés sur Linux de HDInsight. Au lieu de cela, vous devez utiliser une des méthodes suivantes pour mettre en œuvre les activités personnalisées dans le cadre de votre pipeline de chargeur automatique de documents.

-   Exécuter les activités de .NET sur le pool de traitement par lots d’Azure. Reportez-vous à la section service de lot Azure lié [d’utiliser les activités personnalisées dans un pipeline Azure Data Factory](../data-factory/data-factory-use-custom-activities.md#AzureBatch)

-   Mettre en œuvre l’activité comme une activité MapReduce. Pour plus d’informations, reportez-vous à la section [Appeler des programmes MapReduce Data Factory](../data-factory/data-factory-map-reduce.md) .

### <a name="line-endings"></a>Fins de ligne

En général, les fins de ligne sur les systèmes Windows utilisent CRLF, alors que les systèmes Linux utilisent LF. Si vous produisent, ou que vous attendez, les données à des fins de ligne CRLF, vous devrez peut-être modifier les producteurs ou les consommateurs pour travailler avec la fin de la ligne LF.

Par exemple, à l’aide de PowerShell d’Azure à la requête HDInsight sur un cluster Windows renverra les données par CRLF. La même requête avec un cluster Linux retournera LF. Dans de nombreux cas, cela n’a aucune importance pour le consommateur de données, mais il doit être traité avant de migrer vers un cluster Linux.

Si vous avez des scripts qui seront exécutées directement sur les nœuds de cluster de Linux (par exemple, un script Python utilisé avec ruche ou un travail MapReduce), vous devez toujours utiliser le saut de ligne en tant que la fin de la ligne. Si vous utilisez CRLF, des erreurs peuvent s’afficher lors de l’exécution des scripts sur un cluster Linux.

Si vous savez que les scripts ne contiennent pas de chaînes de caractères de retour chariot incorporés, vous pouvez en bloc modifier les fins de ligne à l’aide d’une des méthodes suivantes :

-   **Si vous avez des scripts que vous prévoyez de téléchargement vers le cluster**, utilisez les instructions PowerShell suivantes pour remplacer les fins de ligne CRLF par LF avant de télécharger le script au cluster.

        $original_file ='c:\path\to\script.py'
        $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
        [IO.File]::WriteAllText($original_file, $text)

-   **Si vous avez des scripts qui se trouvent déjà dans l’espace de stockage utilisé par le cluster**, vous pouvez utiliser la commande suivante à partir d’une session SSH pour le cluster basé sur Linux pour modifier le script.

        hdfs dfs -get wasbs:///path/to/script.py oldscript.py
        tr -d '\r' < oldscript.py > script.py
        hdfs dfs -put -f script.py wasbs:///path/to/script.py

##<a name="next-steps"></a>Étapes suivantes

-   [Apprenez à créer des clusters basés sur Linux de HDInsight](hdinsight-hadoop-provision-linux-clusters.md)

-   [Se connecter à un cluster Linux à l’aide de SSH à partir d’un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

-   [Se connecter à un cluster Linux à l’aide de SSH à partir d’un client Mac, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

-   [Gérer un cluster Linux à l’aide de Ambari](hdinsight-hadoop-manage-ambari.md)
