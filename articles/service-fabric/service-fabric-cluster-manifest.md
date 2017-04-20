<properties
   pageTitle="Configurer votre cluster autonome | Microsoft Azure"
   description="Cet article décrit comment configurer votre autonome ou un cluster de Service Fabric privé."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="dkshir"/>


# <a name="configuration-settings-for-standalone-windows-cluster"></a>Paramètres de configuration de cluster de Windows autonome

Cet article explique comment configurer un cluster de Service Fabric autonome à l’aide du fichier _**ClusterConfig.JSON**_ . Vous pouvez utiliser ce fichier pour spécifier des informations telles que les nœuds de Service Fabric et leurs adresses IP, les différents types de nœuds sur le cluster, les configurations de sécurité, ainsi que la topologie du réseau en termes de panne/mise à niveau des domaines, pour votre cluster autonome.

Lorsque vous [Téléchargez le package de Service Fabric autonome](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), quelques exemples du fichier ClusterConfig.JSON sont téléchargés sur votre ordinateur de travail. Les échantillons ayant *DevCluster* dans leurs noms vous aide à créer un cluster avec tous les nœuds de trois sur la même machine, comme nœuds logiques. En dehors de ces cas, au moins un noeud doit être marqué en tant que nœud principal. Ce cluster est utile pour un environnement de test ou de développement et n’est pas pris en charge sous la forme d’un cluster de production. Les échantillons présentant des *MultiMachine* dans leurs noms, vous aide à créer un cluster de qualité production, avec chaque noeud sur un ordinateur distinct. Le nombre de nœuds principaux pour ces cluster se fondera sur le [niveau de fiabilité](#reliability).

1. *ClusterConfig.Unsecure.DevCluster.JSON* et *ClusterConfig.Unsecure.MultiMachine.JSON* indiquent comment créer un cluster de test ou de production non sécurisé respectivement. 
    
2. *ClusterConfig.Windows.DevCluster.JSON* et *ClusterConfig.Windows.MultiMachine.JSON* indiquent comment créer le cluster de test ou de production, sécurisée à l’aide de la [sécurité de Windows](service-fabric-windows-cluster-windows-security.md).

3. *ClusterConfig.X509.DevCluster.JSON* et *ClusterConfig.X509.MultiMachine.JSON* indiquent comment créer le cluster de test ou de production, sécurisé à l’aide de [X509 la sécurité basée sur le certificat](service-fabric-windows-cluster-x509-security.md). 


Maintenant, nous allons examiner les différentes sections d’un fichier _**ClusterConfig.JSON**_ ci-dessous.

## <a name="general-cluster-configurations"></a>Configurations de cluster général
Ceci couvre les cluster large des configurations spécifiques, comme indiqué dans l’extrait de JSON ci-dessous.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2015-01-01-alpha",

Vous pouvez attribuer des tout nom convivial à votre cluster Service Fabric en l’assignant à la variable de **nom** . Le **clusterConfigurationVersion** est le numéro de version de votre cluster ; Vous devez l’augmenter chaque fois que vous mettez à niveau votre cluster Service Fabric. Vous devez cependant laisser **apiVersion** la valeur par défaut.


<a id="clusternodes"></a>
## <a name="nodes-on-the-cluster"></a>Nœuds dans le cluster
Vous pouvez configurer les nœuds de votre cluster Service Fabric à l’aide de la section de **nœuds** , comme le montre l’extrait de code suivant.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Un cluster de Service Fabric doit contenir au moins 3 nœuds. Vous pouvez ajouter plus de nœuds à cette section en fonction de votre configuration. Le tableau suivant décrit les paramètres de configuration pour chaque nœud.

|**Configuration de nœud**|**Description**|
|-----------------------|--------------------------|
|Nom_nœud|Vous pouvez donner un nom convivial pour le nœud.|
|adresse IP|Rechercher l’adresse IP du nœud en ouvrant une fenêtre de commande et en tapant `ipconfig`. Notez l’adresse IPV4 et l’assigner à la variable de **l’adresse IP** .|
|nodeTypeRef|Chaque nœud peut être affecté à un type de nœud différents. Les [types de nœuds](#nodetypes) sont définis dans la section ci-dessous.|
|faultDomain|Domaines d’erreur permettent aux administrateurs de cluster définir les nœuds physiques qui peuvent échouer en même temps en raison des dépendances de physiques partagés.|
|upgradeDomain|Mise à niveau domaines décrivent des ensembles de nœuds qui ne sont pas arrêtés pour mises à niveau de Fabric du Service en même temps. Vous pouvez choisir les nœuds à affecter à quels domaines de mise à niveau, car ils ne sont pas limités par les exigences physiques.| 


## <a name="cluster-properties"></a>Propriétés du cluster

La section **Propriétés** de la ClusterConfig.JSON est utilisée pour configurer le cluster comme suit.

<a id="reliability"></a>
### <a name="reliability"></a>Fiabilité 
La section **reliabilityLevel** définit le nombre de copies des services système qui s’exécutent sur les nœuds du cluster principales. Cela permet d’augmenter la fiabilité de ces services et, par conséquent, le cluster. Vous pouvez affecter à cette variable soit *Bronze*, *Silver*, *Gold* ou *Platinum* pour les copies de 3, 5, 7 ou 9 de ces services respectivement. Voir un exemple ci-dessous.

    "reliabilityLevel": "Bronze",
    
Notez que dans la mesure où un nœud principal s’exécute une seule copie des services système, vous devriez un minimum de 3 nœuds principaux de *Bronze*, 5 *Silver*, *Gold* 7 et 9 pour les niveaux de fiabilité *Platinum* .


### <a name="diagnostics"></a>Tests de diagnostic
La section **diagnosticsStore** vous permet de configurer des paramètres pour activer les diagnostics et le dépannage des échecs de cluster ou le nœud, comme illustré dans l’extrait de code suivant. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Les **métadonnées** est une description de vos tests de diagnostic de cluster et peuvent être définie en fonction de votre configuration. Ces variables permettent de collecter ETW les journaux de suivi, vidages sur incident ainsi que les compteurs de performance. Pour plus d’informations sur les journaux de suivi ETW, lire [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) et [Suivi ETW](https://msdn.microsoft.com/library/ms751538.aspx) . Tous les journaux, y compris des [compteurs de performance](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) et de [vidage sur incident](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) peuvent être dirigés vers le dossier **connectionString** sur votre ordinateur. Vous pouvez également utiliser *AzureStorage* pour le stockage des tests de diagnostic. Voir ci-dessous un extrait de l’échantillon.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Sécurité 
La section de **sécurité** est nécessaire pour un cluster de Service Fabric sécurisé autonome. L’extrait de code suivant montre une partie de cette section.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Les **métadonnées** est une description de votre cluster sécurisé et peuvent être définie en fonction de votre configuration. Les **ClusterCredentialType** et les **ServerCredentialType** déterminent le type de sécurité qui implémenteront le cluster et les nœuds. Il peuvent être définies soit *X509* pour une sécurité basée sur les certificats, ou de *Windows* pour une sécurité basée sur Azure Active Directory. Le reste de la section **sécurité** se fondera sur le type de la sécurité. Pour plus d’informations sur la façon de remplir le reste de la section **sécurité** de lecture [basée sur les certificats de sécurité dans un cluster autonome](service-fabric-windows-cluster-x509-security.md) ou la [sécurité de Windows dans un cluster autonome](service-fabric-windows-cluster-windows-security.md) .


<a id="nodetypes"></a>
### <a name="node-types"></a>Types de nœuds
La section **nodeTypes** décrit le type des nœuds de votre cluster. Type d’au moins un nœud doit être spécifié pour un cluster, comme indiqué dans l’extrait de code ci-dessous. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

Le **nom** est le nom convivial pour ce type de nœud particulier. Pour créer un nœud de ce type de nœud, affecter son nom convivial à la variable **nodeTypeRef** pour ce nœud, comme mentionné [ci-dessus](#clusternodes). Pour chaque type de nœud, définir les points de terminaison de connexion qui seront utilisés. Vous pouvez choisir n’importe quel numéro de port pour ces points de terminaison de connexion, dans la mesure où ils ne portent pas sur les autres points de terminaison de ce cluster. Dans un cluster de nœuds multiples, il y aura un ou plusieurs nœuds principaux (c'est-à-dire **isPrimary** la valeur *true*), en fonction de la [**reliabilityLevel**](#reliability). Lisez les [Considérations de planification de capacité Fabric du Service de cluster](service-fabric-cluster-capacity.md) pour les informations sur les valeurs **nodeTypes** et **reliabilityLevel** et de savoir quels sont les principaux et les types de nœud principal. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Les points de terminaison utilisés pour configurer les types de nœuds

- *clientConnectionEndpointPort* est le port utilisé par le client pour se connecter au cluster, lors de l’utilisation de l’API du client. 
- *clusterConnectionEndpointPort* est le port auquel les nœuds communiquent entre eux.
- *leaseDriverEndpointPort* est le port utilisé par le pilote de bail du cluster pour savoir si les nœuds sont toujours actifs. 
- *serviceConnectionEndpointPort* est le port utilisé par les applications et les services déployés sur un nœud, pour communiquer avec le client du Service Fabric sur ce nœud particulier.
- *httpGatewayEndpointPort* est le port utilisé par l’Explorateur de tissu de Service pour se connecter au cluster.
- *ephemeralPorts* sont les [ports dynamiques utilisés par le système d’exploitation](https://support.microsoft.com/kb/929851). Service de Fabric utilise une partie de ces ports d’application et le reste sera disponible pour le système d’exploitation. Il correspondra également cette plage à la plage existante dans le système d’exploitation, pour tous les usages, vous pouvez utiliser les plages indiquées dans les exemples de fichiers JSON. Vous devez vous assurer que la différence entre le début et les ports de fin est au moins de 255. 
- *applicationPorts* sont les ports qui seront utilisés par les applications de Service Fabric. Elles doivent être un sous-ensemble de *ephemeralPorts*, suffisante pour couvrir la demande de point de terminaison de vos applications. Service Fabric les utilisera chaque fois que de nouveaux ports sont requis, ainsient que prendre en charge l’ouverture du pare-feu pour ces ports. 
- *reverseProxyEndpointPort* est un point de terminaison facultatif de proxy inverse. Pour plus d’informations, reportez-vous à la section [Service de Fabric Reverse Proxy](service-fabric-reverseproxy.md) . 


### <a name="other-settings"></a>Autres paramètres
La section **fabricSettings** vous permet de définir les répertoires racine pour les données de Service Fabric et les journaux. Vous pouvez personnaliser uniquement au moment de la création du cluster initial. Voir ci-dessous un extrait de l’exemple de cette section.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Il est recommandé à l’aide d’un lecteur du système d’exploitation non FabricDataRoot et FabricLogRoot car il offre qu'une fiabilité accrue par rapport à système d’exploitation tombe en panne. Notez que si vous personnalisez uniquement la racine de données, puis la racine de journal sera placée un niveau en dessous de la racine de données.


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez un fichier ClusterConfig.JSON complet configuré en fonction de votre configuration de cluster autonome, vous pouvez déployer votre cluster en suivant l’article [créer un cluster d’Azure Fabric de Service sur site ou dans le nuage](service-fabric-cluster-creation-for-windows-server.md) , puis passez à la [visualisation de votre cluster avec Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).


