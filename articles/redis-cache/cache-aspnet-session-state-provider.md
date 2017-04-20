<properties
    pageTitle="Fournisseur d’état de Session ASP.NET du cache | Microsoft Azure"
    description="Apprenez à stocker l’état de Session ASP.NET à l’aide du Cache Redis d’Azure"
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
    ms.date="09/01/2016"
    ms.author="sdanie" />

# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Fournisseur d’état de Session ASP.NET pour Azure Redis du Cache

Cache de Redis Azure fournit un fournisseur d’état de session que vous pouvez utiliser pour stocker l’état de session dans une mémoire cache plutôt qu’en mémoire ou dans une base de données SQL Server. Pour utiliser le fournisseur d’état de session mise en cache, tout d’abord configurer votre cache et configurez votre application ASP.NET pour le cache en utilisant le package NuGet Redis d ' état de Session du Cache.

Il n’est souvent pas pratique dans une application réelle cloud pour éviter de stocker une forme d’état pour une session de l’utilisateur, mais certaines des approches un impact sur les performances et l’évolutivité plus que d’autres. Si vous devez stocker l’état, la meilleure solution consiste à minimiser la quantité de l’état et les stocker dans des cookies. Si ce n’est pas possible, la meilleure solution suivante est d’utiliser l’état de session ASP.NET auprès d’un fournisseur de cache en mémoire distribué. La solution la plus défavorable à partir d’un point de vue de l’évolutivité et les performances est d’utiliser une base de données fournisseur d’état de session. Cette rubrique fournit des conseils sur l’utilisation du fournisseur d’état de Session ASP.NET pour le Cache de Redis d’Azure. Pour plus d’informations sur les autres options d’état de session, reportez-vous à la section [options d’état de Session ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Stocker l’état de session ASP.NET dans le cache

Pour configurer une application cliente dans Visual Studio en utilisant le package NuGet Redis d ' état de Session du Cache, cliquez droit sur le projet dans **l’Explorateur de solutions** et sélectionnez **Manage NuGet Packages**.

![Cache d’Azure Redis gérer les Packages NuGet](./media/cache-aspnet-session-state-provider/redis-cache-manage-nuget-menu.png)

Tapez **RedisSessionStateProvider** dans la zone Rechercher le texte, sélectionnez-le à partir des résultats, cliquez sur **installer**.

>[AZURE.IMPORTANT] Si vous utilisez la fonctionnalité de clustering de la couche de prime, vous devez utiliser [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou supérieure, ou une exception est levée. Il s’agit d’une modification avec rupture ; Pour plus d’informations, consultez [v2.0.0 Détails de modification avec rupture](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

![Fournisseur d’état de Session Azure Redis du Cache](./media/cache-aspnet-session-state-provider/redis-cache-session-state-provider.png)

Le package NuGet du fournisseur des état de Session Redis a une dépendance sur le package StackExchange.Redis.StrongName. Si le package StackExchange.Redis.StrongName n’est pas présent dans votre projet, qu'il sera installé. Notez qu’en plus du package StackExchange.Redis.StrongName de nom fort est également la version non-fort de StackExchange.Redis. Si votre projet utilise la version de StackExchange.Redis non-nom fort que vous devez le désinstaller, avant ou après l’installation du package Redis de Session état fournisseur NuGet, sinon vous va obtenir des conflits de noms dans votre projet. Pour plus d’informations sur ces packages, reportez-vous à la section [configuration du cache de clients .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Le package NuGet télécharge et ajoute l’assembly requis fait référence et ajoute que celui-ci ajoute la section suivante dans votre fichier web.config qui contient la configuration requise pour votre application ASP.NET utiliser le fournisseur d’état Redis Cache de Session.

    <sessionState mode="Custom" customProvider="MySessionStateStore">
        <providers>
        <!--
        <add name="MySessionStateStore"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
        />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
    </sessionState>

La section commentée fournit un exemple des attributs et des paramètres d’exemple pour chaque attribut.

Configurer les attributs avec les valeurs de la blade de cache dans le portail de Microsoft Azure et configurer les autres valeurs comme vous le souhaitez. Pour obtenir des instructions sur l’accès aux propriétés de votre cache, reportez-vous à la section [Redis de configurer les paramètres du cache](cache-configure.md#configure-redis-cache-settings).

-   **hôte** – spécifiez le point de terminaison de votre cache.
-   **port** – utilisez votre port non-SSL, ou SSL port, en fonction des paramètres ssl.
-   **accessKey** – utilisation de la clé primaire ou secondaire pour votre cache.
-   **ssl** – la valeur true si vous souhaitez sécuriser les communications client/cache avec ssl ; Renvoie la valeur false. Veillez à spécifier le port correct.
    -   Le port non-SSL est désactivé par défaut pour les nouveaux caches. Spécifiez true pour ce paramètre pour utiliser le port SSL. Pour plus d’informations sur l’activation du port non-SSL, consultez la section [Accès aux Ports](cache-configure.md#access-ports) dans la rubrique [configurer un cache](cache-configure.md) .
-   **throwOnError** – true si vous voulez une exception levée en cas d’échec, ou false si vous souhaitez que l’opération échoue en silence. Vous pouvez vérifier de l’échec en vérifiant la propriété statique Microsoft.Web.Redis.RedisSessionStateProvider.LastException. La valeur par défaut est true.
-   **retryTimeoutInMilliseconds** – Échec d’opérations sont ré-essayées pendant cet intervalle, exprimé en millisecondes. La première nouvelle tentative se produit après 20 millisecondes, et puis les tentatives se produisent chaque seconde jusqu'à l’expiration de l’intervalle retryTimeoutInMilliseconds. Immédiatement après cet intervalle, l’opération est recommencée une dernière fois. Si l’opération échoue, l’exception est levée de nouveau à l’appelant, en fonction du paramètre throwOnError. La valeur par défaut est 0, c'est-à-dire aucune tentative.
-   **databaseId** – Spécifie le cache de la base de données à utiliser pour la sortie des données. Si non spécifié, la valeur par défaut 0 est utilisée.
-   **applicationName** – les clés sont stockées dans redis en tant que `{<Application Name>_<Session ID>}_Data`. Cela permet à plusieurs applications de partager la même clé. Ce paramètre est facultatif, et si vous ne fournissez pas elle une valeur par défaut est utilisée.
-   **connectionTimeoutInMilliseconds** – ce paramètre vous permet de remplacer le paramètre connectTimeout dans le client StackExchange.Redis. Si non spécifié, le paramètre par défaut connectTimeout 5000 est utilisé. Pour plus d’informations, consultez [modèle de configuration de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** : ce paramètre vous permet de substituer le paramètre syncTimeout dans le client StackExchange.Redis. Si non spécifié, la valeur de syncTimeout par défaut de 1000 est utilisée. Pour plus d’informations, consultez [modèle de configuration de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Pour plus d’informations sur ces propriétés, consultez la blog publier une annonce d’origine à [l’Annonce de la sortie ASP.NET Session State Provider pour Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

N’oubliez pas de commenter la section fournisseur des état de session standard InProc dans votre fichier web.config.

    <!-- <sessionState mode="InProc"
         customProvider="DefaultSessionProvider">
         <providers>
            <add name="DefaultSessionProvider"
                  type="System.Web.Providers.DefaultSessionStateProvider,
                        System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                        PublicKeyToken=31bf3856ad364e35"
                  connectionStringName="DefaultConnection" />
          </providers>
    </sessionState> -->

Une fois ces étapes sont effectuées, votre application est configurée pour utiliser le fournisseur d’état Redis Cache de Session. Lorsque vous utilisez l’état de session dans votre application, il est stocké dans une instance de Cache Redis d’Azure.

>[AZURE.NOTE] Notez que les données stockées dans le cache doivent être sérialisables, contrairement aux données qui peuvent être stockées dans la valeur par défaut en mémoire ASP.NET Session State du fournisseur. Lorsque le fournisseur d’état de Session pour Redis est utilisé, assurez-vous que les types de données qui sont stockés dans l’état de session sont sérialisables.

## <a name="aspnet-session-state-options"></a>Options d’état de Session ASP.NET

- Dans le fournisseur d’état de Session de mémoire - ce fournisseur stocke l’état de Session en mémoire. L’avantage de l’utilisation de ce fournisseur est simple et rapide. Cependant vous ne pouvez pas mettre à l’échelle vos applications Web si vous utilisez dans le fournisseur de mémoire dans la mesure où il n’est pas distribué.

- Fournisseur d’état de Session SQL Server - ce fournisseur stocke l’état de Session dans Sql Server. Vous devez utiliser ce fournisseur si vous souhaitez rendre persistant l’état de Session dans un stockage persistant. Vous pouvez mettre à l’échelle votre application Web, mais à l’aide de Sql Server de Session auront un impact sur les performances de votre application Web.

- Distribué dans mémoire Session état fournisseur tel que Redis Cache Session état fournisseur - ce fournisseur vous offre le meilleur des deux mondes. Votre application Web peut avoir un fournisseur d’état de Session simple, rapide et évolutive. Vous avez toutefois à prendre en considération que ce fournisseur stocke l’état de Session dans un Cache et que votre application doit prendre en considération toutes les caractéristiques associées lorsqu’il communique avec un en mémoire Cache distribué telles que les pannes de réseau temporaires. Pour les meilleures pratiques sur l’utilisation du Cache, voir le [Guide de mise en cache](../best-practices-caching.md) à partir de Microsoft Patterns & pratiques de [conception de l’Application Azure Cloud et directives d’implémentation](https://github.com/mspnp/azure-guidance).

Pour plus d’informations sur l’état de session et d’autres pratiques recommandées, reportez-vous à [Web Development Best Practices (création réelle nuage d’applications Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Fournisseur de Cache de sortie ASP.NET pour le Cache de Redis d’Azure](cache-aspnet-output-cache-provider.md).
