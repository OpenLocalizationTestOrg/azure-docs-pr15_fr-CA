<properties
    pageTitle="L’utilisation du Cache de Redis Azure avec Node.js | Microsoft Azure"
    description="Mise en route avec Azure Redis de Cache à l’aide de Node.js et node_redis."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>L’utilisation du Cache de Redis Azure avec Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Cache de Redis Azure vous permet d’accéder à un cache de Redis dédié, sécurisé, géré par Microsoft. Le cache est accessible à partir de n’importe quelle application de Microsoft Azure.

Cette rubrique indique comment se familiariser avec Azure Redis mémoire Cache à l’aide de Node.js. Pour un autre exemple d’utilisation du Cache de Redis Azure avec Node.js, consultez [créer une Application de conversation Node.js avec Socket.IO sur un site Web d’Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## <a name="prerequisites"></a>Conditions préalables

Installation de [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Ce didacticiel utilise [node_redis](https://github.com/mranney/node_redis). Pour obtenir des exemples d’utilisation des autres clients Node.js, reportez-vous à la documentation individuelle pour les clients Node.js répertoriés au [Node.js Redis de clients](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Créer un cache de Redis sur Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Récupérer les clés d’accès et le nom d’hôte

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Se connecter au cache en toute sécurité à l’aide de SSL

Les dernières versions de [node_redis](https://github.com/mranney/node_redis) en charge pour la connexion au Cache de Redis Azure à l’aide de SSL. L’exemple suivant montre comment se connecter à un Cache de Redis Azure en utilisant le point de terminaison SSL de 6380. Remplacer `<name>` avec le nom de votre cache et `<key>` avec soit votre clé primaire ou secondaire en tant que décrit dans la section précédente de [récupérer les clés d’accès et le nom d’hôte](#retrieve-the-host-name-and-access-keys) .

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Ajouter un élément au cache et récupérer

L’exemple suivant vous montre comment se connecter à une instance de Cache Redis d’Azure et de stocker et de récupérer un élément à partir du cache. Pour plus d’exemples d’utilisation de Redis avec le client [node_redis](https://github.com/mranney/node_redis) , consultez [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
    
    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });
    
    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Sortie :

    OK
    value


## <a name="next-steps"></a>Étapes suivantes

- [Activer les diagnostics de la mémoire cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin de pouvoir le [Moniteur de](cache-how-to-monitor.md) l’état de santé de votre cache.
- Lire le officiel [Redis documentation](http://redis.io/documentation).



