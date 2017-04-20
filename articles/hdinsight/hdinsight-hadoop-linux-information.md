<properties
   pageTitle="Conseils pour l’utilisation d’Hadoop sur basé sur Linux de HDInsight | Microsoft Azure"
   description="Obtenez des conseils de mise en œuvre pour l’utilisation de clusters de basé sur Linux HDInsight (Hadoop) dans un environnement Linux familier s’exécutant dans le nuage Azure."
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
   ms.date="09/13/2016"
   ms.author="larryfr"/>

# <a name="information-about-using-hdinsight-on-linux"></a>Informations sur l’utilisation d’HDInsight sous Linux

Linux-Azure HDInsight grappes de serveurs fournissent Hadoop sur un environnement Linux familier, s’exécutant dans le nuage Azure. Pour la plupart des choses, il doit fonctionner exactement comme toute autre installation Hadoop-sur-Linux. Ce document s’appelle les différences spécifiques que vous devez connaître.

##<a name="prerequisites"></a>Conditions préalables

La plupart des étapes de ce document utilisent les utilitaires suivants, devant être installé sur votre système.

* [coin](https://curl.haxx.se/) - utilisé pour communiquer avec les services basés sur le web
* [jq](https://stedolan.github.io/jq/) - utilisé pour analyser des documents JSON
* [Azure CLI](../xplat-cli-install.md) - permet de gérer à distance des services Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

## <a name="domain-names"></a>Noms de domaine

Le nom de domaine complet (FQDN) à utiliser lors de la connexion au cluster à partir d’internet est ** &lt;nomcluster >. azurehdinsight.net** ou (SSH) ** &lt;nom_cluster-ssh >. azurehdinsight.net**.

En interne, chaque nœud du cluster possède un nom qui est attribué lors de la configuration du cluster. Pour rechercher les noms de cluster, vous pouvez visiter la page __hôtes__ sur l’interface utilisateur Web de Ambari, ou utiliser les éléments suivants pour renvoyer une liste d’hôtes à partir de l’API REST de Ambari :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Remplacez le __mot de passe__ avec le mot de passe du compte administrateur et __NOM_CLUSTER__ avec le nom de votre cluster. Ceci renverra un document JSON qui contient une liste des hôtes dans le cluster, puis jq de sortir le `host_name` valeur de l’élément pour chaque hôte du cluster.

Si vous devez rechercher le nom du nœud pour un service spécifique, vous pouvez interroger Ambari pour ce composant. Par exemple, pour rechercher les hôtes pour le nœud du nom très, utiliser la syntaxe suivante.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Retourne un document JSON qui décrivent le service, et puis jq de sortir seulement la `host_name` valeur pour les hôtes.

## <a name="remote-access-to-services"></a>Accès à distance aux services

* **Ambari (web)** - https://&lt;nomcluster >. azurehdinsight.net

    S’authentifier à l’aide de l’utilisateur administrateur de cluster et d’un mot de passe, puis connectez-vous à Ambari. Il utilise également l’utilisateur administrateur de cluster et d’un mot de passe.

    L’authentification est en texte brut - toujours utiliser le protocole HTTPS pour vous assurer que la connexion est sécurisée.

    > [AZURE.IMPORTANT] Ambari pour votre cluster est accessible directement via Internet, certaines fonctionnalités s’appuie sur l’accès aux nœuds par le nom de domaine interne utilisé par le cluster. Dans la mesure où il s’agit d’un nom de domaine interne, et non public, vous recevez les erreurs « serveur introuvable » lorsque vous essayez d’accéder à certaines fonctionnalités via Internet.
    >
    > Pour utiliser toutes les fonctionnalités de l’interface utilisateur du web Ambari, utiliser un tunnel SSH pour le trafic web de proxy pour le nœud principal du cluster. Reportez-vous à la section [Utiliser SSH Tunneling pour accéder à l’interface utilisateur web de Ambari, ResourceManager, JobHistory, NameNode, Oozie et autre web, l’interface utilisateur](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (reste)** - https://&lt;nomcluster >.azurehdinsight.net/ambari

    > [AZURE.NOTE] S’authentifier à l’aide de l’utilisateur administrateur de cluster et d’un mot de passe.
    >
    > L’authentification est en texte brut - toujours utiliser le protocole HTTPS pour vous assurer que la connexion est sécurisée.

* **WebHCat (Templeton)** - https://&lt;nomcluster >.azurehdinsight.net/templeton

    > [AZURE.NOTE] S’authentifier à l’aide de l’utilisateur administrateur de cluster et d’un mot de passe.
    >
    > L’authentification est en texte brut - toujours utiliser le protocole HTTPS pour vous assurer que la connexion est sécurisée.

* **SSH** - &lt;nomcluster >-ssh.azurehdinsight.net sur le port 22 ou 23. Le port 22 est utilisé pour se connecter à la headnode principale, tandis que 23 est utilisé pour se connecter sur le site secondaire. Pour plus d’informations sur les nœuds de tête, voir la [disponibilité et la fiabilité des clusters Hadoop dans HDInsight](hdinsight-high-availability-linux.md).

    > [AZURE.NOTE] Vous ne pouvez accéder des nœuds de tête du cluster via SSH à partir d’un ordinateur client. Une fois connecté, vous pouvez ensuite accéder aux nœuds travailleur à l’aide de SSH à partir d’un headnode.

## <a name="file-locations"></a>Emplacements des fichiers

Vous pouvez trouver des fichiers Hadoop sur les nœuds de cluster à `/usr/hdp`. Ce répertoire contient les sous-répertoires suivants :

* __2.2.4.9-1__: ce répertoire est nommé pour la version de la plate-forme de données de Hortonworks utilisé par le HDInsight, le numéro de votre cluster peut être différent de celui indiqué ici.
* __en cours__: ce répertoire contient des liens vers les répertoires sous le répertoire __2.2.4.9-1__ et il existe afin que vous n’êtes pas obligé de taper un numéro de version (ce qui peut modifier,) chaque fois que vous souhaitez accéder à un fichier.

Vous trouverez les exemples de données et les fichiers JAR sur le stockage de système de fichier distribué (très) Hadoop ou Azure Blob à ' / exemple ' ou ' wasbs : / / / exemple ».

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>Meilleures pratiques de stockage, stockage des objets Blob Azure et très

Dans la plupart des distributions d’Hadoop, très bénéficie de stockage local sur les ordinateurs dans le cluster. Bien que cela soit efficace, il peut être coûteux pour une solution basée sur le nuage où vous êtes responsable de toutes les heures ou en minutes pour les ressources de calcul.

HDInsight utilise le stockage des objets Blob Azure banque par défaut, qui offre les avantages suivants :

* Stockage à long terme bon marché

* Accessibilité à partir des services externes tels que des sites Web, des utilitaires de téléchargement de fichiers, plusieurs kits de langue et les navigateurs web

Puisqu’il est le magasin par défaut pour HDInsight, normalement n’avoir rien à faire pour l’utiliser. Par exemple, la commande suivante répertorie les fichiers dans le dossier **/example/data** , qui est stocké sur le stockage des objets Blob Azure :

    hdfs dfs -ls /example/data

Certaines commandes pouvant vous obliger à spécifier que vous utilisez le stockage Blob. Dans ce cas, vous pouvez faire précéder la commande avec **wasb : / /**, ou **wasbs : / /**.

HDInsight vous permet également d’associer plusieurs comptes de stockage Blob avec un cluster. Pour accéder aux données sur un compte de stockage Blob non défini par défaut, vous pouvez utiliser le format **wasbs : / /&lt;container-name>@&lt;-nom du compte >.blob.core.windows.net/**. Par exemple, le texte suivant pour afficher le contenu du répertoire **/example/data** pour le conteneur spécifié et d’un compte de stockage Blob :

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>Le stockage des objets Blob est le cluster à l’aide de ?

Lors de la création du cluster, vous avez sélectionné pour utiliser un conteneur et un compte de stockage Azure existant, soit créer un nouveau. Ensuite, vous avez probablement oublié à son sujet. Vous pouvez trouver le compte de stockage par défaut et le conteneur à l’aide de l’API REST de Ambari.

1. Utilisez la commande suivante pour extraire les informations de configuration très à l’aide de roulage et filtrer à l’aide de [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] Elle retournera la première configuration appliquée au serveur (`service_config_version=1`,) qui contiendra ces informations. Si vous récupérez une valeur a été modifiée après la création du cluster, vous devez pouvez répertorient les versions de configuration et de récupérer des tout derniers.

    Cela renverra une valeur semblable à la suivante, le __conteneur__ est le conteneur par défaut où et de __nom de compte__ est le nom du compte de stockage Azure :

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. Obtenir le groupe de ressources pour le compte de stockage, utilisez la [CLI d’Azure](../xplat-cli-install.md). Dans la commande suivante, remplacer le __nom de compte__ avec le nom du compte de stockage récupéré à partir de Ambari :

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Elle retournera le nom de groupe de ressources pour le compte.
    
    > [AZURE.NOTE] Si rien n’est retourné à partir de cette commande, vous devrez modifier la CLI Azure en mode de Gestionnaire des ressources Azure et réexécutez la commande. Pour passer en mode de gestionnaire de ressources d’Azure, utilisez la commande suivante.
    >
    > `azure config mode arm`
    
2. Récupérez la clé pour le compte de stockage. Remplacez le __nom de groupe__ avec le groupe de ressources à partir de l’étape précédente. Remplacez le __nom de compte__ avec le nom du compte de stockage :

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    Ceci renvoie la clé primaire pour le compte.

Vous pouvez également rechercher les informations de stockage à l’aide du portail Azure :

1. Dans le [Portail Azure](https://portal.azure.com/), sélectionnez votre cluster HDInsight.

2. Dans la section __Essentials__ , sélectionnez __tous les paramètres__.

3. Dans __paramètres__, sélectionnez __Clés de stockage Azure__.

4. À partir de __Clés de stockage Azure__, sélectionnez un des comptes de stockage répertoriés. Il affiche des informations sur le compte de stockage.

5. Sélectionnez l’icône de clé. Les clés pour ce compte de stockage s’affiche.

### <a name="how-do-i-access-blob-storage"></a>Comment pour accéder au stockage de Blob ?

Autre que via la commande Hadoop du cluster, il existe plusieurs façons d’accéder aux objets BLOB :

* [Infrastructure du langage commun pour Mac, Linux et Windows Azure](../xplat-cli-install.md): commandes de l’interface de ligne de commande pour travailler avec Azure. Après l’installation, vous devez utiliser le `azure storage` commande pour de l’aide sur l’utilisation du stockage, ou `azure blob` pour des commandes spécifiques au blob.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): un python de script pour travailler avec des objets BLOB dans le stockage Azure.

* Une variété de kits de développement logiciel :

    * [Java](https://github.com/Azure/azure-sdk-for-java)

    * [Node.js](https://github.com/Azure/azure-sdk-for-node)

    * [PHP](https://github.com/Azure/azure-sdk-for-php)

    * [Python](https://github.com/Azure/azure-sdk-for-python)

    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

    * [.NET](https://github.com/Azure/azure-sdk-for-net)

* [API REST de stockage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>Mise à l’échelle de votre cluster.

La fonctionnalité de mise à l’échelle de cluster vous permet de modifier le nombre de nœuds de données utilisées par un cluster qui exécute dans Azure HDInsight sans devoir supprimer et recréer le cluster.

Vous pouvez effectuer des opérations de mise à l’échelle lors d’autres tâches ou processus sont en cours d’exécution sur un cluster.

Les types de cluster différents sont affectés par la mise à l’échelle comme suit :

* __Hadoop__: lors de la mise à l’échelle le nombre de nœuds dans un cluster, les services du cluster sont redémarrés. Cela peut entraîner des travaux en cours d’exécution ou en attente échoue à la fin de l’opération de mise à l’échelle. Vous pouvez soumettre à nouveau les tâches une fois l’opération terminée.

* __HBase__: les serveurs régionaux sont équilibrées automatiquement après quelques minutes après la fin de l’opération de mise à l’échelle. Pour équilibrer manuellement les serveurs régionaux, procédez comme suit :

    1. Connectez-vous au cluster de HDInsight à l’aide de SSH. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les documents suivants :

        * [Utiliser le protocole SSH avec HDInsight de Linux, Unix et Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Utiliser le protocole SSH avec HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    1. Utilisez la suivante pour démarrer l’interface HBase :

            hbase shell

    2. Une fois que le shell HBase a chargé, utilisez la suivante permettent d’équilibrer manuellement les serveurs régionaux :

            balancer

* __Storm__: vous devez rééquilibrer les topologies de tempête en cours d’exécution après une opération de mise à l’échelle a été effectuée. Ainsi, la topologie de réajuster les paramètres de parallélisme basés sur le nouveau nombre de nœuds dans le cluster. Pour rééquilibrer les topologies en cours d’exécution, utilisez une des options suivantes :

    * __SSH__: connexion au serveur et utilisez la commande suivante pour rééquilibrer une topologie :

            storm rebalance TOPOLOGYNAME

        Vous pouvez également spécifier des paramètres pour remplacer les indications de parallélisme initialement fournies par la topologie. Par exemple, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` va reconfigurer la topologie 5 processus de travail, les exécuteurs de 3 pour le composant bleu-bec et 10 exécuteurs du composant boulon-jaune.

    * __L’interface utilisateur de Storm__: la procédure suivante permet de rééquilibrer une topologie à l’aide de l’interface utilisateur de tempête.

        1. Ouvrez __https://CLUSTERNAME.azurehdinsight.net/stormui__ dans votre navigateur web, où CLUSTERNAME est le nom de votre cluster de tempête. Si vous y êtes invité, entrez le nom de l’administrateur (admin) de cluster HDInsight et le mot de passe spécifié lors de la création du cluster.

        3. Sélectionnez la topologie que vous souhaitez rééquilibrer, puis sélectionnez le bouton __rééquilibrer__ . Entrez le délai avant que l’opération de rééquilibrage.

Pour plus d’informations sur la mise à l’échelle de votre cluster HDInsight, voir :

* [Gérer les clusters de Hadoop dans HDInsight en utilisant le portail Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Gérer les clusters Hadoop dans HDinsight à l’aide de PowerShell d’Azure](hdinsight-administer-use-command-line.md#scaling)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Comment installer teinte (ou un autre composant d’Hadoop) ?

HDInsight est un service géré, ce qui signifie que les nœuds d’un cluster peuvent être détruits et configurés automatiquement Azure si un problème est détecté. Pour cette raison, il est déconseillé d’installer manuellement les éléments directement sur les nœuds du cluster. À la place, utilisez [Les Actions de Script HDInsight](hdinsight-hadoop-customize-cluster.md) lorsque vous devez installer les éléments suivants :

* Un service ou un site web comme l’allumage ou teinte.
* Un composant qui nécessite des modifications de configuration sur plusieurs nœuds dans le cluster. Par exemple, une variable d’environnement obligatoire, création d’un répertoire de journalisation, ou la création d’un fichier de configuration.

Actions de script sont des scripts Bash exécuté au cours de la mise en service de cluster et peuvent être utilisées pour installer et configurer des composants supplémentaires sur le cluster. Exemples de scripts sont fournis pour installer les composants suivants :

* [Teinte](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Mode série sur LAN.r](hdinsight-hadoop-solr-install-linux.md)

Pour plus d’informations sur le développement de vos propres Actions de Script, consultez [développement de Script Action avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

###<a name="jar-files"></a>Fichiers JAR

Certaines technologies d’Hadoop sont fournis dans les fichiers jar autonome contenant des fonctions utilisées dans le cadre d’un travail MapReduce, ou à partir de l’intérieur de porc ou de la ruche. Alors que ceux-ci peuvent être installés à l’aide des Actions de Script, ils souvent ne requiert aucune modification des et peuvent seulement être téléchargés au cluster après la mise en service et utilisés directement. Si vous souhaitez makle que le composant survit à créer de nouvelles images du cluster, vous pouvez stocker le fichier jar dans WASB.

Par exemple, si vous souhaitez utiliser la dernière version de [DataFu](http://datafu.incubator.apache.org/), vous pouvez télécharger un fichier jar contenant le projet et télécharger vers le cluster HDInsight. Suivez la documentation DataFu sur la façon de l’utiliser à partir de porc ou de la ruche.

> [AZURE.IMPORTANT] Certains composants qui sont des fichiers jar autonomes sont fournis avec HDInsight, mais ne sont pas dans le chemin d’accès. Si vous recherchez un composant spécifique, vous pouvez utiliser le suivi pour le rechercher sur votre cluster :
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Cette opération retourne le chemin d’accès d’un fichier jar.

Si le cluster fournit déjà une version d’un composant sous la forme d’un fichier jar d’autonome, mais que vous souhaitez utiliser une autre version, vous pouvez télécharger une nouvelle version du composant sur le cluster et essayez de l’utiliser dans vos projets.

> [AZURE.WARNING] Les composants fournis avec le cluster HDInsight sont entièrement gérés et Support de Microsoft vous permet d’isoler et de résoudre les problèmes liés à ces composants.
>
> Composants assistance commercialement raisonnables pour vous aider à résoudre le problème. Cela peut provoquer de résolution du problème ni à vous demander d’engager les canaux disponibles pour les technologies open source où se trouve une grande expertise pour cette technologie. Par exemple, de nombreux sites de la Communauté qui peuvent être utilisés, comme : [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Également les projets Apache ont des sites de projets sur [http://apache.org](http://apache.org), par exemple : [Hadoop](http://hadoop.apache.org/), [commandé](http://spark.apache.org/).

## <a name="next-steps"></a>Étapes suivantes

* [Migrer à partir de HDInsight de basés sur Windows sur Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Utilisez la ruche avec HDInsight](hdinsight-use-hive.md)
* [Utilisez des porcs avec HDInsight](hdinsight-use-pig.md)
* [Utiliser des travaux de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
