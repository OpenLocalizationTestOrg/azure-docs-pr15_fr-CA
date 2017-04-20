<properties
 pageTitle="Comment gérer l’expiration de contenu Azure Web Apps/Cloud Services, ASP.NET et IIS dans Azure CDN | Microsoft Azure"
 description="Décrit comment gérer l’expiration de contenu d’un service cloud dans Azure CDN"
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# <a name="how-to-manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Comment gérer l’expiration de contenu de Services d’applications/Cloud Azure Web, ASP.NET ou IIS dans Azure CDN

> [AZURE.SELECTOR]
- [Services d’applications/Cloud Azure Web, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Service d’objet blob stockage Azure](cdn-manage-expiration-of-blob-content.md)

Les fichiers à partir de n’importe quel serveur de web accessibles au public d’origine peuvent être mis en cache dans Azure CDN jusqu'à expiration de sa durée de vie (TTL).  La durée de vie est déterminée par l' [en-tête *Cache-Control* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) dans la réponse HTTP à partir du serveur d’origine.  Cet article décrit comment définir les `Cache-Control` les en-têtes pour Azure Web Apps, Azure Cloud, ASP.NET applications, sites et Services Internet Information Services, qui sont configurés de la même façon.

>[AZURE.TIP] Vous pouvez choisir de ne définir aucune durée de vie sur un fichier.  Dans ce cas, Azure CDN applique automatiquement une durée de vie de sept jours par défaut.
>
>Pour plus d’informations sur le fonctionnement d’Azure CDN pour accélérer les accès aux fichiers et aux autres ressources, consultez la [Vue d’ensemble de Azure CDN](./cdn-overview.md).

## <a name="setting-cache-control-headers-in-configuration"></a>Définition des en-têtes de contrôle du Cache dans la configuration

Pour le contenu statique, tel que les images et les feuilles de style, vous pouvez contrôler la fréquence de mise à jour en modifiant les fichiers **applicationHost.config** ou **web.config** de votre application web.  L’élément **system.webServer\staticContent\clientCache** dans le fichier de configuration définit les `Cache-Control` en-tête pour votre contenu. Pour **web.config**, les paramètres de configuration affectera tous les éléments dans le dossier et tous ses sous-dossiers, sauf si la substitution au niveau du sous-dossier.  Par exemple, vous pouvez définir une valeur par défaut time-to-live à la racine pour que tout le contenu statique mis en cache pendant 3 jours, mais ont un sous-dossier qui possède un contenu plus variable avec un paramètre de mémoire cache de 6 heures.  Pour **applicationHost.config**, toutes les applications sur le site seront affectées, mais peuvent être substituées dans les fichiers **web.config** dans les applications.

Le XML indique et exemple de paramètre **clientCache** pour spécifier un âge maximal de 3 jours suivants :  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Spécification de **UseMaxAge** ajoute un `Cache-Control: max-age=<nnn>` en-tête de la réponse en fonction de la valeur spécifiée dans l’attribut **CacheControlMaxAge** . Le format de l’instance timespan est pour l’attribut **cacheControlMaxAge** est <days>. <hours>:<min>:<sec>. Pour plus d’informations sur le nœud **clientCache** , reportez-vous à la section [Cache Client <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Définir les en-têtes de contrôle du Cache dans le Code

Pour les applications ASP.NET, vous pouvez définir le comportement de cache par programme en définissant la propriété de la **propriété HttpResponse.Cache** CDN. Pour plus d’informations sur la propriété de la **propriété HttpResponse.Cache** , voir [Propriété HttpResponse.Cache propriété](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) et [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Si vous souhaitez cache le contenu de l’application dans ASP.NET par programme, de s’assurer que le contenu est marqué comme pouvant être mises en définissant HttpCacheability *public*. En outre, assurez-vous que l’option un validateur de cache est défini. Le validateur de cache peut être une dernière modification timestamp définir en appelant SetLastModified, ou une valeur etag défini en appelant SetETag. Si vous le souhaitez, vous pouvez également spécifier un délai d’expiration de cache en appelant SetExpires, ou vous pouvez compter sur l’heuristique de cache par défaut décrit plus haut dans ce document.  

Par exemple, pour du contenu du cache pendant une heure, ajoutez les éléments suivants :  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Étapes suivantes

- [Lire plus d’informations sur l’élément **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [Consultez la documentation de la propriété **propriété HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
- [Lire la documentation pour la **Classe HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  
