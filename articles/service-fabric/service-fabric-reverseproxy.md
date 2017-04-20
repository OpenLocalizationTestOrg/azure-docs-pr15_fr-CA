<properties
   pageTitle="Service Proxy inverse de Fabric | Microsoft Azure"
   description="Utiliser le proxy inverse du Service Fabric pour la communication à microservices à partir de l’intérieur et l’extérieur du cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/04/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-reverse-proxy"></a>Proxy inverse de tissu de service

Le proxy inverse de Service Fabric est un tissu intégré avec le service de proxy inverse qui permet l’adressage microservices dans le service de cluster de fabric qui exposent des points de terminaison HTTP.

## <a name="microservices-communication-model"></a>Modèle de communication Microservices

Microservices dans un tissu de service s’exécutent sur un sous-ensemble de l’ordinateur virtuel dans le cluster en général et peut déplacer à partir d’un ordinateur virtuel à un autre pour diverses raisons. Pour les points de terminaison de microservices peuvent modifier de façon dynamique. Le modèle standard pour communiquer avec le microservice est la boucle de résolution ci-dessous,

1. Résoudre l’emplacement du service initialement par le biais du Service d’attribution de noms.
2. La connexion au service.
3. Déterminer la cause des échecs de connexion et de résoudre l’emplacement du service lorsque cela est nécessaire.

Ce processus implique généralement l’habillage des bibliothèques de communication côté client dans une boucle de nouvelle tentative qui implémente les stratégies du service de résolution puis réessayez.
Pour plus d’informations sur ce sujet, consultez [communication avec les services](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-via-sf-reverse-proxy"></a>Communication via un proxy inverse de SF
TISSU de service proxy inverse s’exécute sur tous les nœuds du cluster. Il exécute le processus de résolution de service pour un client et transmet ensuite la demande du client. Par conséquent, clients s’exécutant sur le cluster suffit d’utiliser les bibliothèques de communication HTTP côté client pour communiquer avec le service cible via le serveur proxy inverse SF exécuté localement sur le même nœud.

![Communication interne][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>Atteindre Microservices à partir de l’extérieur du cluster
Le modèle de communication externe par défaut pour microservices est **opt-in** où chaque service par défaut ne sont pas accessibles directement à partir de clients externes. L' [Équilibreur de charge Azure](../load-balancer/load-balancer-overview.md) est une limite de réseau entre microservices et des clients externes, qui effectue la traduction d’adresses réseau et transfère les requêtes externes à interne **IP : port** points de terminaison. Pour rendre le point de terminaison d’un microservice directement accessible aux clients externes, l’équilibreur de charge Azure doit d’abord être configuré pour transférer le trafic pour chaque port utilisé par le service du cluster. En outre, la plupart des microservices (esp. avec état microservices) ne live sur tous les nœuds du cluster et ils peuvent se déplacer entre les nœuds de basculement, afin que, dans ce cas, l’équilibreur de charge Azure ne peut pas déterminer efficacement les nœud cible des réplicas se trouvent pour transférer le trafic.

### <a name="reaching-microservices-via-the-sf-reverse-proxy-from-outside-the-cluster"></a>Atteindre Microservices via le proxy inverse SF à partir de l’extérieur du cluster

Au lieu de configurer des ports individuels du service de l’équilibreur de charge azure, simplement le port de proxy inverse de df peut être configuré dans l’équilibreur de charge Azure. Cela permet aux clients de l’extérieur du cluster joindre les services à l’intérieur du cluster via le proxy inverse sans une configuration supplémentaire.

![Communication externe][0]

>[AZURE.WARNING] Configurer les ports du serveur proxy inverse de l’équilibreur de charge, permet de tous les services micro dans le cluster qui expose un point de terminaison http, pour être addressible à partir de l’extérieur du cluster.


## <a name="uri-format-for-addressing-services-via-the-reverse-proxy"></a>Format d’URI pour l’adressage des services via le serveur proxy inverse

Le serveur proxy inversé utilise un format URI spécifique pour identifier la requête entrante doit être transférée vers la partition dans laquelle service :

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http :** Le proxy inverse peut être configuré pour accepter le trafic HTTP ou HTTPS. Dans le cas du trafic HTTPS, arrêt de SSL se produit au niveau du proxy inverse. Les demandes qui sont transmises par le proxy inverse pour les services dans le cluster sont sur http.
 - **Nom de domaine complet cluster | adresse IP interne :** Pour les clients externes, le proxy inverse peut être configuré afin qu’il soit accessible par le biais du domaine de cluster (par exemple, mycluster.eastus.cloudapp.azure.com). Par défaut que le proxy inverse s’exécute sur chaque nœud, donc pour le trafic interne il peut être atteint sur localhost ou sur n’importe quel nœud interne d’IP (par ex. 10.0.0.1).
 - **Port :** Le port a été spécifié pour le serveur proxy inverse. Par exemple : 19008.
 - **ServiceInstanceName :** C’est le nom complet de service déployé d’instance du service que vous essayez d’atteindre le « fabric : / » régime. Par exemple, pour atteindre le service *fabric : / myapp/myservice/*, vous utiliseriez *myapp/myservice*.
 - **Chemin d’accès de suffixe :** C’est le chemin URL du service que vous souhaitez vous connecter. Par exemple, *myapi/valeurs/ajouter/3*
 - **PartitionKey :** Pour un service partitionné, il s’agit de la clé calculée de partition de la partition que vous souhaitez atteindre. Notez que c’est *pas* la GUID de l’ID de partition. Ce paramètre n’est pas obligatoire pour les services en utilisant le schéma de partition de singleton.
 - **PartitionKind :** Le schéma de partition de service. Il peut s’agir de 'Int64Range' ou 'Named'. Ce paramètre n’est pas obligatoire pour les services en utilisant le schéma de partition de singleton.
 - **Timeout :**  Ce paramètre spécifie le délai d’attente pour la demande http créée par le proxy inverse pour le service au nom de la demande du client. La valeur par défaut est de 60 secondes. Il s’agit d’un paramètre facultatif.

### <a name="example-usage"></a>Exemple d’utilisation

Par exemple, prenons le service **fabric : / MyApp/MyService** qui ouvre un port d’écoute HTTP sur l’URL suivante :

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Avec les ressources suivantes :

 - `/index.html`
 - `/api/users/<userId>`

Si le service utilise le schéma de partitionnement singleton, les paramètres de chaîne de requête *PartitionKey* et *PartitionKind* ne sont pas nécessaires, et le service est accessible via la passerelle en tant que :

 - En externe :`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - En interne :`http://localhost:19008/MyApp/MyService`

Si le service utilise le schéma de partitionnement Int64 uniforme, les paramètres de chaîne de requête de *PartitionKey* et de *PartitionKind* doivent être utilisés pour accéder à une partition du service :

 - En externe :`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - En interne :`http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Pour atteindre les ressources exposées par le service, placez simplement le chemin d’accès de la ressource après le nom du service dans l’URL :

 - En externe :`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - En interne :`http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

La passerelle puis transmet ces demandes vers les URL du service :

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Un traitement spécial pour le partage de port de services

La passerelle d’Application tente de résoudre une adresse de service et de réessayer la demande lorsqu’un service ne peut pas être atteint. Il s’agit d’un des principaux avantages de la passerelle, comme le code de client n’a pas besoin d’implémenter sa propre résolution de service et de résoudre la boucle.

Lorsqu’un service ne peut pas être atteint qu’il signifie généralement que l’instance de service ou le réplica a déplacé vers un autre nœud dans le cadre de son cycle de vie normal. Dans ce cas, la passerelle peut recevoir une erreur de connexion réseau indiquant qu'un point de terminaison n’est plus ouverte sur l’adresse résolue à l’origine.

Toutefois, les réplicas ou les instances de service peuvent partager un processus hôte et peuvent également partager l’un port lorsqu’il est hébergé par un serveur web http.sys, y compris :

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [Noyau d’ASP.NET WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Interconnexions](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Dans ce cas, il est probable que le serveur web est disponible dans le processus hôte et répondre aux requêtes, mais l’instance de service résolu ou le réplica n’est plus disponible sur l’hôte. Dans ce cas, la passerelle recevra une réponse HTTP 404 à partir du serveur web. Par conséquent, un HTTP 404 a deux significations distinctes :

 1. L’adresse de service est correcte, mais la ressource demandée par l’utilisateur n’existe pas.
 2. L’adresse du service est incorrecte, et que la ressource demandée par l’utilisateur ne peut exister réellement sur un nœud différent.

Dans le premier cas, il s’agit d’une erreur 404 HTTP normal, qui est considérée comme une erreur de l’utilisateur. Toutefois, dans le deuxième cas, l’utilisateur a demandé une ressource qui existe, mais que la passerelle n’a pas pu localiser parce que le service lui-même a été déplacé, auquel cas la passerelle doit résoudre l’adresse et réessayez.

La passerelle doit par conséquent un moyen de faire la distinction entre ces deux cas. Pour rendre cette distinction, une indication du serveur est requise.

 - Par défaut, la passerelle d’Application suppose les cas #2 et essaie de résoudre et de ré-émettre la demande.
 - Pour indiquer les cas #1 à la passerelle d’Application, le service doit retourner l’en-tête de réponse HTTP suivante :

`X-ServiceFabric : ResourceNotFound`

Cet en-tête de réponse HTTP indique une situation HTTP 404 normale dans lequel la ressource demandée n’existe pas, et la passerelle ne tentera de résoudre l’adresse de service.

## <a name="setup-and-configuration"></a>Le programme d’installation et de configuration
Le proxy inverse de tissu de service peut être activé pour le cluster via le [modèle de gestionnaire de ressources Azure](./service-fabric-cluster-creation-via-arm.md).

Une fois que le modèle de cluster que vous souhaitez déployer (dans les exemples de modèles ou en créant un modèle de gestionnaire de ressources personnalisés) le proxy inverse peut être activée dans le modèle par les étapes suivantes.

1. Définir un port pour le serveur proxy inversé dans la [section Paramètres](../resource-group-authoring-templates.md) du modèle.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Spécifier le port pour chacun des objets nodetype dans la [section type de ressource](../resource-group-authoring-templates.md) de **Cluster**

    Pour apiVersion avant ' 2016-09-01', le port est identifié par le paramètre de nom de ***httpApplicationGatewayEndpointPort***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

    Pour apiVersion sur ou après ' 2016-09-01', le port est identifié par le paramètre de nom de ***reverseProxyEndpointPort***

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

3. Pour résoudre le proxy inverse à partir de l’extérieur du cluster azure, configurer les **règles d’équilibrage de la charge azure** pour le port spécifié à l’étape 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Pour configurer des certificats SSL sur le port pour le serveur proxy inverse, ajouter le certificat à la propriété httpApplicationGatewayCertificate dans la [section type de ressource](../resource-group-authoring-templates.md) de **Cluster**

    Pour apiVersion avant ' 2016-09-01', le certificat est identifié par le paramètre de nom de ***httpApplicationGatewayCertificate***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```
    Pour apiVersion sur ou après ' 2016-09-01', le certificat est identifié par le paramètre de nom de ***reverseProxyCertificate***
    
    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## <a name="next-steps"></a>Étapes suivantes
 - Consultez un exemple de communication HTTP entre les services dans un [exemple de projet sur GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Appels de procédure distante avec l’accès distant des Services fiables](service-fabric-reliable-services-communication-remoting.md)

 - [Web API qui utilise OWIN dans des Services fiables](service-fabric-reliable-services-communication-webapi.md)

 - [Communication WCF à l’aide des Services fiables](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
