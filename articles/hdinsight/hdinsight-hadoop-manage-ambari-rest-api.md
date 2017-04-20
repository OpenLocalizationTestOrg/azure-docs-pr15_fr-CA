<properties
   pageTitle="Surveiller et gérer des clusters HDInsight à l’aide de l’API REST de Ambari Apache | Microsoft Azure"
   description="Découvrez comment utiliser Ambari pour surveiller et gérer les clusters basés sur Linux de HDInsight. Dans ce document, vous apprendrez comment utiliser l’API REST de Ambari inclus avec les clusters HDInsight."
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

#<a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Gérer les clusters HDInsight à l’aide de l’API REST de Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifie la gestion et la surveillance d’un cluster d’Hadoop en fournissant un facile à utiliser l’interface utilisateur web et les API REST. Ambari est inclus sur les clusters basés sur Linux de HDInsight et est utilisé pour surveiller le cluster et apporter des modifications de configuration. Dans ce document, vous découvrez les notions de base de l’utilisation de l’API REST de Ambari par l’exécution de tâches courantes à l’aide de roulage.

##<a name="prerequisites"></a>Conditions préalables

* [coin](http://curl.haxx.se/): cURL est un utilitaire multiplateforme qui permet de travailler avec les autres API à partir de la ligne de commande. Dans ce document, il est utilisé pour communiquer avec l’API REST de Ambari.
* [jq](https://stedolan.github.io/jq/): jq est un utilitaire de ligne de commande multiplateforme pour travailler avec des documents JSON. Dans ce document, il est utilisé pour analyser les documents JSON retournées par l’API REST de Ambari.
* [Azure CLI](../xplat-cli-install.md): un utilitaire de ligne de commande multiplateforme pour travailler avec des services Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a id="whatis"></a>Nouveautés Ambari

[Apache Ambari](http://ambari.apache.org) simplifie la gestion d’Hadoop en fournissant un interface utilisateur qui peut être utilisé pour configurer, gérer et surveiller les clusters Hadoop du web facile à utiliser. Les développeurs peuvent intégrer ces fonctionnalités dans leurs applications à l’aide de l' [API de reste de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari est fourni par défaut avec les clusters basés sur Linux de HDInsight.

##<a name="rest-api"></a>API REST

L’URI de base pour l’API REST de Ambari de HDInsight est https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, où __CLUSTERNAME__ est le nom de votre cluster.

> [AZURE.IMPORTANT] Alors que le nom du cluster dans la partie nom de domaine complet de l’URI (CLUSTERNAME.azurehdinsight.net) est insensible à la casse, autres occurrences dans l’URI sont sensibles à la casse. Par exemple, si votre cluster est nommé Mon_cluster, les éléments suivants sont des URI valides :
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> L’URI suivant renvoie une erreur parce que la deuxième occurrence du nom n’est pas la casse.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

Connexion à Ambari sur HDInsight nécessite HTTPS. Lors de l’authentification de la connexion, vous devez utiliser le nom du compte administrateur (la valeur par défaut est __admin__) et un mot de passe fourni lors de la création du cluster.

L’exemple suivant utilise le roulage pour effectuer une demande GET par rapport à l’API REST. Remplacez le __mot de passe__ avec le mot de passe administrateur pour le cluster. Remplacez __CLUSTERNAME__ par le nom du cluster :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
La réponse est un document JSON qui contient des informations similaires à ce qui suit :

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Dans la mesure où il s’agit de JSON, il est plus facile d’utiliser un analyseur JSON pour travailler avec les données. Par exemple, l’exemple suivant utilise jq pour afficher uniquement les `health_report` élément.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##<a name="example-get-the-fqdn-of-cluster-nodes"></a>Exemple : Obtenir le nom de domaine complet des nœuds de cluster

Lorsque vous travaillez avec HDInsight, vous devrez connaître le nom de domaine pleinement qualifié (FQDN) d’un nœud de cluster. Vous pouvez facilement récupérer le nom de domaine complet pour les différents nœuds du cluster à l’aide de la commande suivante :

* __Nœuds de tête__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nœuds de travailleur__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nœuds de soigneur__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Notez que chacun de ces exemples suivre le même modèle :

1. Un composant qui nous savons de requête s’exécute sur ces nœuds.
2. Récupérer le `host_name` éléments contenant le nom de domaine complet de ces nœuds.

Le `host_components` élément du document de retour contient plusieurs éléments. À l’aide de `.host_components[]`, et puis en spécifiant un chemin d’accès dans l’élément va effectuer une boucle sur chaque élément et extraire la valeur du chemin d’accès spécifique. Si vous souhaitez uniquement une valeur, comme la première entrée de nom de domaine complet, vous pouvez renvoyer les éléments sous la forme d’une collection et sélectionnez une entrée spécifique :

    jq '[.host_components[].HostRoles.host_name][0]'

Retourne le premier nom de domaine complet à partir de la collection.

##<a name="example-get-the-default-storage-account-and-container"></a>Exemple : Obtenir le compte de stockage par défaut et le conteneur

Lorsque vous créez un cluster de HDInsight, vous devez utiliser un compte de stockage Azure et un conteneur blob en tant que le stockage par défaut pour le cluster. Vous pouvez utiliser Ambari pour récupérer ces informations une fois que le cluster a été créé. Par exemple, si vous souhaitez par programme écrire des données directement dans le conteneur.

Récupère l’URI WASB du stockage clusters par défaut suivantes :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] Cette propriété renvoie la première configuration appliquée au serveur (`service_config_version=1`,) qui contient ces informations. Si vous récupérez une valeur a été modifiée après la création du cluster, vous devez pouvez répertorient les versions de configuration et de récupérer des tout derniers.

Cet exemple renvoie une valeur semblable à l’exemple suivant, le __conteneur__ est le conteneur par défaut où et de __nom de compte__ est le nom du compte de stockage Azure :

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Vous pouvez ensuite utiliser ces informations avec la [CLI d’Azure](../xplat-cli-install.md) à charger ou télécharger des données à partir du conteneur.

1. Obtenir le groupe de ressources pour le compte de stockage. Remplacer le __nom de compte__ avec le nom du compte de stockage récupéré à partir de Ambari :

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Retourne le nom du groupe de ressources pour le compte.
    
    > [AZURE.NOTE] Si rien n’est retourné à partir de cette commande, vous devrez modifier la CLI Azure en mode de Gestionnaire des ressources Azure et réexécutez la commande. Pour passer en mode de gestionnaire de ressources d’Azure, utilisez la commande suivante :
    >
    > `azure config mode arm`
    
2. Récupérez la clé pour le compte de stockage. Remplacez le __nom de groupe__ avec le groupe de ressources à partir de l’étape précédente. Remplacez le __nom de compte__ avec le nom du compte de stockage :

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Cet exemple renvoie la clé primaire pour le compte.
    
3. Pour stocker un fichier dans le conteneur, utilisez la commande de téléchargement :

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Remplacez le __nom de compte__ avec le nom du compte de stockage. Remplacez __ACCOUNTKEY__ par la clé récupérée précédemment. __Chemin d’accès__ est le chemin d’accès au fichier à télécharger, tandis que __BLOBPATH__ est le chemin d’accès dans le conteneur.

    Par exemple, si vous souhaitez que le fichier s’affichent dans le HDInsight à wasbs://example/data/filename.txt, __BLOBPATH__ être `example/data/filename.txt`.

##<a name="example-update-ambari-configuration"></a>Exemple : Configuration d’Ambari de mise à jour

1. Obtenir la configuration actuelle, qui stocke les Ambari en tant que « la configuration souhaitée » :

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    Cet exemple renvoie un document JSON qui contient la configuration actuelle (identifiée par la valeur de _balise_ ) pour les composants installés sur le cluster. L’exemple suivant est un extrait à partir des données retournées à partir d’un type de cluster d’allumage.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    Dans cette liste, vous devez copier le nom du composant (par exemple, __allumage\_thrift\_sparkconf__ et la valeur de la __balise__ .
    
2. Récupérer la configuration pour le composant et la balise à l’aide de la commande suivante. Remplacer __l’allumage-thrift-sparkconf__ et __initiale__ avec le composant et la balise que vous souhaitez récupérer la configuration de.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Curl récupère le document JSON, puis jq est utilisé pour apporter des modifications aux données pour créer un modèle. Le modèle est ensuite utilisé pour ajouter/modifier des valeurs de configuration. En particulier, il effectue les opérations suivantes :
    
    * Crée une valeur unique qui contient la chaîne « version » et la date, qui est stockée dans __newtag__.
    * Crée un document racine pour la nouvelle configuration de votre choix.
    * Obtient le contenu de la `.items[]` du tableau et ajoute sous l’élément __desired_config__ .
    * Supprime le __href__, __version__et éléments de __configuration__ , comme ces éléments ne sont pas nécessaires pour soumettre une nouvelle configuration.
    * Ajoute un nouvel élément de __balise__ et lui affecte la valeur __version ###__. La partie numérique est basée sur la date actuelle. Chaque configuration doit avoir une balise unique.
    
    Enfin, les données sont enregistrées dans le document __newconfig.json__ . La structure du document doit apparaître comme dans l’exemple suivant :
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. Ouvrez les valeurs du document et modifier/ajouter des __newconfig.json__ de l’objet __properties__ . L’exemple suivant modifie la valeur de __« spark.yarn.am.memory »__ de __« 1 g »__ à __« 3 g »__ et et ajoute un nouvel élément pour __« spark.kryoserializer.buffer.max »__ avec la valeur __« 256 m »__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    Enregistrez le fichier une fois que vous avez apporté des modifications.

4. Utilisez la commande suivante pour envoyer la configuration mise à jour de Ambari.

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Cette commande transfère le contenu du fichier __newconfig.json__ à la demande de roulage, qui a été envoyé au cluster en tant que la nouvelle configuration de votre choix. La demande de roulage renvoie un document JSON. L’élément __versionTag__ dans ce document doit correspondre à la version que vous avez soumis, et l’objet de __configurations__ contient les modifications de configuration que vous avez demandée.

###<a name="example-restart-a-service-component"></a>Exemple : Redémarrez un composant de service

À ce stade, si vous regardez l’interface utilisateur du web Ambari, le service d’allumage indique qu’il doit être redémarré pour que la nouvelle configuration prennent effet. Procédez comme suit pour redémarrer le service.

1. Pour activer le mode maintenance pour le service de l’allumage, utilisez ce qui suit :

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Cette commande envoie un document JSON au serveur (contenus dans le `echo` instruction) qui active le mode maintenance.
    Vous pouvez vérifier que le service est maintenant en mode de maintenance à l’aide de la requête suivante :
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    Ceci renvoie une valeur de `"ON"`.

3. Ensuite, utilisez ce qui suit pour désactiver le service :

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Cette commande renvoie une réponse semblable à la suivante.
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    Le `href` valeur renvoyée par cette URL à l’aide de l’adresse IP interne du nœud de cluster. Pour l’utiliser à partir de l’extérieur du cluster, remplacez la partie « 10.0.0.18:8080 » avec le nom de domaine complet du cluster. Par exemple, la commande suivante extrait l’état de la demande.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Si elle retourne une valeur de `"COMPLETED"` ensuite la demande terminée.

4. Une fois la demande précédente terminée, utilisez ce qui suit pour démarrer le service.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Une fois que le service redémarre, il s’agit de nouveaux paramètres de configuration.

5. Enfin, utilisez ce qui suit pour désactiver le mode maintenance.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##<a name="next-steps"></a>Étapes suivantes

Pour une référence complète de l’API REST, consultez [V1 de référence API Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Certaines fonctionnalités de Ambari sont désactivée, il est géré par le service en nuage HDInsight ; par exemple, ajouter ou supprimer des hôtes dans le cluster ou ajout de nouveaux services.
