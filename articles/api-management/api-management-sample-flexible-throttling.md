<properties
    pageTitle="La limitation avec Azure API de gestion de demande avancée"
    description="Découvrez comment créer et appliquer le quota souple et les stratégies de gestion des API Azure de limitation du débit."
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="advanced-request-throttling-with-azure-api-management"></a>La limitation avec Azure API de gestion de demande avancée

La possibilité de limiter les demandes entrantes est un rôle clé de la gestion des API Azure. Soit, en contrôlant le taux de demandes ou des demandes totale/données de transfert, gestion de l’API permet à API de protéger leurs API contre les abus et obtenir la valeur de différents niveaux de produit API.

## <a name="product-based-throttling"></a>La limitation des produits en fonction
À ce jour, la limitation de la vitesse des fonctionnalités ont été uniquement d’une portée limitée à un abonnement de produit (essentiellement une clé) spécifique, défini par l’API portail de gestion publisher. Cela est utile pour le fournisseur de l’API appliquer des limites sur les développeurs qui ont souscrit à utiliser leur API, toutefois, il ne permet pas, par exemple, dans la limitation les utilisateurs finals de l’API. Il est possible que pour un seul utilisateur de l’application du développeur pour consommer tout contingent et puis empêcher les autres clients du développeur d’être en mesure d’utiliser l’application. Également, plusieurs utilisateurs peuvent générer un volume élevé de demandes peuvent limiter l’accès à des utilisateurs occasionnels.

## <a name="custom-key-based-throttling"></a>Clé personnalisée en fonction de la limitation
Les nouvelles stratégies de [taux limite par clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) et [par clé de quota](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) offrent une solution beaucoup plus souple pour le contrôle du trafic. Ces nouvelles stratégies permettent de définir des expressions pour identifier les clés qui seront utilisés pour effectuer le suivi de l’utilisation du trafic. Le fonctionnement est illustré plus simples avec un exemple. 

## <a name="ip-address-throttling"></a>Limitation de l’adresse IP
Les stratégies suivantes restreignent une adresse IP unique à seulement 10 appels toutes les minutes, avec un total de 1 000 000 appels et 10 000 kilo-octets de la bande passante par mois. 

    <rate-limit-by-key  calls="10"
              renewal-period="60"
              counter-key="@(context.Request.IpAddress)" />

    <quota-by-key calls="1000000"
              bandwidth="10000"
              renewal-period="2629800"
              counter-key="@(context.Request.IpAddress)" />

Si tous les clients sur Internet utilisaient une adresse IP unique, il peut s’agir d’un moyen efficace de limiter l’utilisation par l’utilisateur. Toutefois, il est probable que plusieurs utilisateurs seront partager une seule adresse IP publique en raison de leur accès à Internet via un périphérique NAT. Malgré cela, pour les API qui autorisent l’accès non authentifié le `IpAddress` peut être la meilleure option.

## <a name="user-identity-throttling"></a>Limitation des identités d’utilisateur
Si un utilisateur est authentifié, puis une limitation de la clé peut être générée en fonction des informations qui identifient de façon unique un utilisateur.

    <rate-limit-by-key calls="10"
        renewal-period="60"
        counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />

Dans cet exemple, nous allons extraire l’en-tête Authorization, convertir en `JWT` objet et l’objet du jeton permet d’identifier l’utilisateur et de l’utiliser comme la clé de la limitation du débit. Si l’identité de l’utilisateur est stockée dans le `JWT` comme une des autres créances puis que la valeur peut être utilisée à sa place.

## <a name="combined-policies"></a>Stratégies combinées
Bien que les nouvelles stratégies de limitation offrent plus de contrôle que les stratégies de limitation de, il est toujours combinant les deux fonctions. Limitation de la clé d’inscription de produit ([taux d’appel limite par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) et [définir le quota d’utilisation par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) est un excellent moyen pour activer la monétisation d’une API en charge en fonction des niveaux d’utilisation. Le contrôle préci à grains fins de pouvoir limiter par utilisateur est complémentaire et empêche la dégradation de l’expérience d’un autre comportement d’un utilisateur. 

## <a name="client-driven-throttling"></a>Client contrôlée par la limitation
La limitation de la clé est définie à l’aide d’une [expression de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx), puis il est l’API fournisseur qui consiste à choisir comment la limitation de la portée est limitée. Toutefois, un développeur peut décider de contrôler comment ils limite de taux leurs propres clients. Il peut être activé par le fournisseur de l’API en introduisant un en-tête personnalisé pour permettre à l’application du développeur client à communiquer la clé à l’API.

    <rate-limit-by-key calls="100"
              renewal-period="60"
              counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>

Cela permet à l’application du développeur client à choisir comment ils souhaitent créer la clé de la limitation du débit. Avec un peu d’ingéniosité un développeur client peut créer ses propres niveaux de taux en allouant des jeux de clés pour les utilisateurs et la rotation de l’utilisation de la clé.

## <a name="summary"></a>Résumé
Gestion des API Azure assure des taux et des devis de limitation pour protéger et valoriser votre service API. Les nouvelles stratégies de limitation avec les règles de portée personnalisés permettent de précis à grains fins contrôle sur ces politiques permettent à vos clients de créer des applications mieux encore. Les exemples de cet article illustrent l’utilisation de ces nouvelles stratégies par clés avec les adresses IP des clients, identité de l’utilisateur et les valeurs de client généré de limitation du débit de fabrication. Toutefois, il existe de nombreuses autres parties du message qui peut être utilisé comme agent utilisateur, les fragments de chemin d’accès d’URL, la taille des messages.

## <a name="next-steps"></a>Étapes suivantes
Veuillez faites-nous part de vos commentaires dans le thread Disqus pour cette rubrique. Il serait idéal être informé des autres valeurs clés potentielles qui ont été un choix logique dans vos scénarios.

## <a name="watch-a-video-overview-of-these-policies"></a>Regarder une vidéo de présentation de ces stratégies.
Pour plus d’informations sur les stratégies du [taux limite par clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) et [par clé de quota](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) abordés dans cet article, veuillez consulter la vidéo suivante.

> [AZURE.VIDEO advanced-request-throttling-with-azure-api-management]
