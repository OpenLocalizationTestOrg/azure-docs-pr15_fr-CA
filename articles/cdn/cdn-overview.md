<properties
    pageTitle="Vue d’ensemble d’Azure CDN | Microsoft Azure"
    description="Découvrez ce qu’est le réseau de livraison de contenu (CDN) Azure et comment l’utiliser pour diffuser du contenu de la bande passante élevée en mettant en cache BLOB et le contenu statique."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/30/2016"
    ms.author="casoper"/>

# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Vue d’ensemble du réseau Azure de diffusion de contenu (CDN)

> [AZURE.NOTE] Ce document explique ce qu’est le réseau de livraison de contenu (CDN) d’Azure, comment il fonctionne et que les fonctionnalités de chaque produit Azure CDN.  Si vous souhaitez ignorer ces informations et d’accéder directement à un didacticiel sur la création d’un point de terminaison CDN, reportez-vous [à l’aide d’Azure CDN](cdn-create-new-endpoint.md).  Si vous souhaitez afficher la liste des emplacements de noeud en cours CDN, consultez [Emplacements de POP Azure CDN](cdn-pop-locations.md).

Le réseau de livraison de contenu (CDN) Azure met en cache le contenu web statique à des emplacements stratégiques pour fournir un débit maximal de diffuser du contenu aux utilisateurs.  Le CDN offre aux développeurs une solution globale pour fournir un contenu de large bande passante en mettant en cache le contenu au niveau des nœuds physiques dans le monde entier. 

Avantages de l’utilisation du CDN aux ressources du site web du cache :

- Meilleures performances et utilisateur expérience pour les utilisateurs finaux, en particulier lors de l’utilisation d’applications où plusieurs allers-retours sont requis pour charger le contenu.
- Grande mise à l’échelle afin de mieux gérer une charge élevée instantanée, comme au début d’un événement de lancement de produit.
- En distribuant les requêtes de l’utilisateur et servant le contenu à partir des serveurs de périphérie, moins de trafic est envoyé à l’origine.


## <a name="how-it-works"></a>Mode de fonctionnement

![Vue d’ensemble CDN](./media/cdn-overview/cdn-overview.png)

1. Un utilisateur (Alice) demande un fichier (également appelé un actif) à l’aide d’une URL avec un nom de domaine spécial, tel que `<endpointname>.azureedge.net`.  DNS achemine la demande vers la meilleure exécution d’emplacement de Point de présence (POP).  Cela est généralement le POP géographiquement plus proche de l’utilisateur.

2. Si les serveurs de périphérie dans la fenêtre contextuelle n’ont pas le fichier dans leur cache, le serveur de transport edge demande le fichier à partir de l’origine.  L’origine peut être une application Web de Azure, Azure Cloud Service, compte de stockage Azure ou n’importe quel serveur web accessibles au public.

3. L’origine retourne le fichier sur le serveur edge, y compris les en-têtes HTTP facultatifs décrivant Time-to-Live du fichier (TTL).

4. Le serveur de transport edge met en cache le fichier et renvoie le fichier au demandeur d’origine (Alice).  Le fichier reste en mémoire cache sur le serveur de transport edge avant l’expiration de la durée de vie.  Si vous ne spécifiez pas une durée de vie de l’origine, la durée de vie par défaut est de sept jours.

5. Des utilisateurs peuvent demander le même fichier à l’aide de cette même URL et peuvent également être dirigés vers ce même POP.

6. Si la durée de vie pour le fichier n’a pas expiré, le serveur de transport edge renvoie le fichier à partir du cache.  Il en résulte une expérience utilisateur plus rapide et plus réactives.


## <a name="azure-cdn-features"></a>Fonctionnalités d’Azure CDN

Il existe trois produits Azure CDN : **Azure CDN Standard à partir d’Akamai** **Azure CDN Standard de Verizon**et **Prime à Azure CDN auprès de Verizon**.  Le tableau suivant répertorie les fonctionnalités disponibles avec chaque produit.

|       | Akamai standard | Verizon standard | Prime Verizon |
|-------|-----------------|------------------|-----------------|
| Une intégration facile avec Azure des services tels que le [stockage](cdn-create-a-storage-account-with-cdn.md), [Les Services en nuage](cdn-cloud-service-with-cdn.md), [Applications Web](../app-service-web/cdn-websites-with-cdn.md)et [Des Services de support](../media-services/media-services-portal-manage-streaming-endpoints.md) | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;**|
| Gestion via [l’API REST](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](./cdn-app-dev-net.md), [Node.js](./cdn-app-dev-node.md)ou [PowerShell](./cdn-manage-powershell.md). | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| Prise en charge d’HTTPS | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| Équilibrage de la charge | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| Protection de [DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| Double pile IPv4/IPv6 | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| [Prise en charge du nom de domaine personnalisé](cdn-map-content-to-custom-domain.md) | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| [La mise en cache de chaîne de requête](cdn-query-string.md) | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| [Geo-filtrage](cdn-restrict-access-by-country.md) |  | **& #x 2713 ;** | **& #x 2713 ;** |
| [Purge rapide](cdn-purge-endpoint.md) | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| [Préchargement des actifs](cdn-preload-endpoint.md) |  | **& #x 2713 ;** | **& #x 2713 ;** |
| [Analytique de base](cdn-analyze-usage-patterns.md) |  | **& #x 2713 ;** | **& #x 2713 ;** |
| [Prise en charge HTTP/2](https://msdn.microsoft.com/library/mt762901.aspx) | **& #x 2713 ;**  |  |  |
| [Rapports avancés de HTTP](cdn-advanced-http-reports.md) | | | **& #x 2713 ;** |
| [Statistiques en temps réel](cdn-real-time-stats.md) | | | **& #x 2713 ;** |
| [Alertes en temps réel](cdn-real-time-alerts.md) | | | **& #x 2713 ;** |
| [Moteur de livraison de contenu personnalisable, basée sur des règles](cdn-rules-engine.md) | | | **& #x 2713 ;** |
| Paramètres de cache/en-tête (à l’aide du [moteur de règles](cdn-rules-engine.md))  | | | **& #x 2713 ;** |
| Redirection/réécriture d’URL (à l’aide du [moteur de règles](cdn-rules-engine.md)) | | | **& #x 2713 ;** |
| Règles de périphérique mobile (à l’aide du [moteur de règles](cdn-rules-engine.md))  | | | **& #x 2713 ;** |

>[AZURE.TIP] Existe-t-il une fonctionnalité que vous souhaitez voir dans Azure CDN ?  [Faites-nous part de vos commentaires](https://feedback.azure.com/forums/169397-cdn)! 

## <a name="next-steps"></a>Étapes suivantes

Pour vous familiariser avec le Canada, reportez-vous [à l’aide d’Azure CDN](./cdn-create-new-endpoint.md).

Si vous êtes un client CDN existant, vous pouvez désormais gérer vos points de terminaison CDN via le [portail Microsoft Azure](https://portal.azure.com) ou avec [PowerShell](cdn-manage-powershell.md).

Pour découvrir le CDN en action, consultez la [vidéo de votre session de génération 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Apprenez à automatiser Azure CDN avec [.NET](./cdn-app-dev-net.md) ou [Node.js](./cdn-app-dev-node.md).

Informations de tarification, consultez [Tarification du CDN](https://azure.microsoft.com/pricing/details/cdn/).
