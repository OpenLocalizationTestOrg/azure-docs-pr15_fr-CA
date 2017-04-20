<properties
    pageTitle="Paie Canada-fichier mise en cache de la stratégie dans l’Extension des Services de support"
    description="Cette rubrique fournit une vue d’ensemble d’un réseau CDN mise en cache de la stratégie dans l’Extension des Services de support."
    services="media-services,cdn"
    documentationCenter=".NET"
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="juliako"/>
 
#<a name="cdn-caching-policy-in-media-services-extension"></a>Paie Canada-fichier mise en cache de la stratégie dans l’Extension des Services de support

Azure Media Services fournit HTTP diffusion adaptative en continu et le téléchargement progressif. Lecture en continu HTTP en fonction est hautement évolutive avec les avantages de la mise en cache de proxy et CDN couches ainsi que la mise en cache du côté client. Diffusion en continu de points de terminaison fournit des possibilités de diffusion générales et de configuration pour les en-têtes HTTP de cache. Diffusion en continu de points de terminaison définit HTTP Cache-Control : max-age et les en-têtes de la date d’expiration. Vous pouvez obtenir plus d’informations pour les en-têtes HTTP de cache à partir de [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

##<a name="default-caching-headers"></a>En-têtes de mise en cache par défaut

Par défaut de diffusion en continu-points de terminaison appliquent des en-têtes du cache de 3 jours à la demande données en continu (fragments de média réel/segments) et manifest(playlist). Pour une diffusion en continu live, des points de terminaison en continu appliquent des en-têtes du cache de 3 jours de données (réelle du média fragments/segments) et 2 secondes en cache en-tête pour les demandes de manifest(playlist). En-têtes du cache de la diffusion en continu à la demande s’appliquent lorsque le programme direct active à la demande (une archive en ligne).

##<a name="azure-cdn-integration"></a>Intégration de Azure CDN

Azure Media Services fournit les [CDN intégré](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) pour la diffusion en continu-points de terminaison. Les en-têtes de contrôle du cache s’applique de la même manière que des points de terminaison en continu CDN activée en continu des points de terminaison. Azure CDN en flux continu de point de terminaison configuré pour définir la durée de vie des objets en interne en mémoire cache, les valeurs de cache et utilise également cette valeur pour définir des en-têtes du cache de la livraison. Lorsque l’aide CDN activé en continu des points de terminaison il n’est pas recommandé pour définir les valeurs de cache de petite taille. Définition de petites valeurs pour diminuer les performances et réduire l’avantage du CDN. Il n’est pas autorisé pour définir des en-têtes de cache inférieure à 600 secondes CDN activé en continu des points de terminaison.

>[AZURE.IMPORTANT] Intégration de Media Services Azure avec Azure CDN est implémentée sur **Azure CDN auprès de Verizon**.  Si vous souhaitez utiliser **Azure CDN à partir d’Akamai** pour Azure Media Services, vous devez [configurer manuellement le point de terminaison](cdn-create-new-endpoint.md).  Pour plus d’informations sur les fonctionnalités d’Azure CDN, consultez la [vue d’ensemble CDN](cdn-overview.md).

##<a name="configuring-cache-headers-with-azure-media-services"></a>Configuration des en-têtes du cache avec Azure Media Services

Vous pouvez utiliser le portail de gestion d’Azure ou Azure Media Services API pour configurer les valeurs d’en-tête de cache.

1. Pour configurer les en-têtes de cache à l’aide de la gestion portail reportez-vous à la section [comment gérer les points de terminaison en continu](../media-services/media-services-portal-manage-streaming-endpoints.md) configuration du point de terminaison pour la diffusion en continu.
2. Azure Media Services API REST, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. Kit de développement .NET, [les propriétés StreamingEndpointCacheControl](http://go.microsoft.com/fwlink/?LinkId=615302)des Services Azure Media.

##<a name="cache-configuration-precedence-order"></a>Ordre de priorité de configuration du cache

1. Valeur de cache Azure Media Services configuré remplace la valeur par défaut.
2. S’il n’existe aucune configuration manuelle, les valeurs par défaut s’applique.
3. Par le cache de 2 secondes par défaut les en-têtes s’applique pour live en continu manifest(playlist) quelle que soit la configuration Azure Media ou stockage Azure et substitution de cette valeur n’est pas disponible.
