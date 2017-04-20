<properties
    pageTitle="Fournisseur de Cache de sortie d’ASP.NET du cache"
    description="Découvrez comment mettre en cache la sortie d’une Page ASP.NET à l’aide du Cache Redis d’Azure"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Le fournisseur de Cache de sortie d’ASP.NET pour Azure Redis du Cache

Le fournisseur de Cache de sortie de Redis est un mécanisme de stockage d’out-of-process pour les données de cache de sortie. Ces données sont spécifiquement pour les réponses HTTP complètes (page mise en cache de sortie). Le fournisseur se connecte à la nouvelle sortie cache fournisseur point d’extensibilité qui a été introduit dans ASP.NET 4.

Pour utiliser le fournisseur de Cache de sortie Redis, tout d’abord configurer votre cache, puis configurer votre application ASP.NET à l’aide du package Redis de sortie Cache fournisseur NuGet. Cette rubrique fournit des instructions sur la configuration de votre application pour utiliser le fournisseur de Cache de sortie Redis. Pour plus d’informations sur la création et la configuration d’une instance de Cache Redis d’Azure, voir [Création d’un cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Stocker la sortie de la page ASP.NET dans le cache

Pour configurer une application cliente dans Visual Studio à l’aide du package Redis de sortie Cache fournisseur NuGet, cliquez droit sur le projet dans **l’Explorateur de solutions** et sélectionnez **Manage NuGet Packages**.

![Cache d’Azure Redis gérer les Packages NuGet](./media/cache-aspnet-output-cache-provider/redis-cache-manage-nuget-menu.png)

Tapez **RedisOutputCacheProvider** dans la zone Rechercher le texte, sélectionnez-le à partir des résultats, cliquez sur **installer**.

![Azure Redis fournisseur de Cache de sortie de Cache](./media/cache-aspnet-output-cache-provider/redis-cache-page-output-provider.png)

Le package Redis de sortie Cache fournisseur NuGet a une dépendance sur le package StackExchange.Redis.StrongName. Si le package StackExchange.Redis.StrongName n’est pas présent dans votre projet, qu'il sera installé. Notez qu’en plus du package StackExchange.Redis.StrongName de nom fort est également la version non-fort de StackExchange.Redis. Si votre projet utilise la version de StackExchange.Redis non-nom fort que vous devez le désinstaller, avant ou après l’installation du package Redis de sortie Cache fournisseur NuGet, sinon vous allez obtenir conflits de noms dans votre projet. Pour plus d’informations sur ces packages, reportez-vous à la section [configuration du cache de clients .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Le package NuGet télécharge et ajoute les références d’assembly requises et la section suivante dans votre fichier web.config qui contient la configuration requise pour votre application ASP.NET utiliser le fournisseur de Cache de sortie Redis.

    <caching>
      <outputCachedefault Provider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

La section commentée fournit un exemple des attributs et des paramètres d’exemple pour chaque attribut.

Configurer les attributs avec les valeurs de la blade de cache dans le portail Microsoft Azure et configurer les autres valeurs comme vous le souhaitez. Pour obtenir des instructions sur l’accès aux propriétés de votre cache, reportez-vous à la section [Redis de configurer les paramètres du cache](cache-configure.md#configure-redis-cache-settings).

-   **hôte** – spécifiez le point de terminaison de votre cache.
-   **port** – utilisez votre port non-SSL, ou SSL port, en fonction des paramètres ssl.
-   **accessKey** – utilisation de la clé primaire ou secondaire pour votre cache.
-   **ssl** – la valeur true si vous souhaitez sécuriser les communications client/cache avec ssl ; Renvoie la valeur false. Veillez à spécifier le port correct.
    -   Le port non-SSL est désactivé par défaut pour les nouveaux caches. Spécifiez true pour ce paramètre pour utiliser le port SSL. Pour plus d’informations sur l’activation du port non-SSL, consultez la section [Accès aux Ports](cache-configure.md#access-ports) dans la rubrique [configurer un cache](cache-configure.md) .
-   **databaseId** – spécifié de la base de données à utiliser pour les données de sortie du cache. Si non spécifié, la valeur par défaut 0 est utilisée.
-   **applicationName** – les clés sont stockées dans redis en tant que <AppName>_<SessionId>_Data. Cela permet à plusieurs applications de partager la même clé. Ce paramètre est facultatif, et si vous ne fournissez pas elle une valeur par défaut est utilisée.
-   **connectionTimeoutInMilliseconds** – ce paramètre vous permet de remplacer le paramètre connectTimeout dans le client StackExchange.Redis. Si non spécifié, le paramètre par défaut connectTimeout 5000 est utilisé. Pour plus d’informations, consultez [modèle de configuration de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** : ce paramètre vous permet de substituer le paramètre syncTimeout dans le client StackExchange.Redis. Si non spécifié, la valeur de syncTimeout par défaut de 1000 est utilisée. Pour plus d’informations, consultez [modèle de configuration de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Ajoutez une directive OutputCache dans chaque page pour laquelle vous souhaitez mettre en cache la sortie.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Dans cet exemple les données de la page mise en cache restent dans le cache pendant 60 secondes et une version différente de la page est mise en cache pour chaque combinaison de paramètres. Pour plus d’informations sur la directive OutputCache, voir [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Une fois ces étapes sont effectuées, votre application est configurée pour utiliser le fournisseur de Cache de sortie Redis.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Fournisseur d’état de Session ASP.NET pour le Cache de Redis d’Azure](cache-aspnet-session-state-provider.md).
