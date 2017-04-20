<properties 
    pageTitle="Concepts clés de la gestion de l’API" 
    description="Obtenir des informations sur les API, les produits, les rôles, les groupes et les autres concepts clés de gestion de l’API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

#<a name="what-is-api-management"></a>Quelle est la gestion de l’API ?

API de gestion aide les entreprises à publier des API externes, des partenaires et des développeurs internes pour déverrouiller le potentiel de leurs données et leurs services. Les entreprises partout sont cherchant à étendre leurs opérations sous la forme d’une plate-forme numérique, création de nouveaux canaux, recherche de nouveaux clients et plus profond engagement avec existants de conduite. API de gestion fournit les principales compétences pour garantir la réussite de ce programme API via l’engagement de développeur, perspectives métier, analytique, sécurité et protection.

Regardez la vidéo suivante pour une vue d’ensemble de la gestion des API Azure et apprenez à utiliser les API de gestion pour ajouter de nombreuses fonctionnalités à votre API, y compris le contrôle d’accès, limitation de vitesse, la surveillance, l’enregistrement des événements et réponse mise en cache, avec un minimum de travail de votre part.

> [AZURE.VIDEO azure-api-management-overview]

Pour utiliser la gestion de l’API, les administrateurs créent des API. Chaque API se compose d’une ou plusieurs opérations, et chaque API peut être ajoutée à un ou plusieurs produits. Pour utiliser une API, les développeurs s’abonner à un produit qui contient cette API, et ils peuvent alors appeler le fonctionnement de l’API, sous réserve des stratégies d’utilisation qui peuvent être en vigueur.

Cette rubrique fournit une vue d’ensemble des concepts de gestion de l’API.

>[AZURE.NOTE] Pour plus d’informations, consultez la [Gestion des API en nuage : maîtriser la puissance de l’API](http://j.mp/ms-apim-whitepaper) livre blanc du document PDF. Ce livre blanc d’introduction sur la gestion des API par CITO Research couvre : 
>
> - Défis et exigences d’API communes
>     - Découplage d’API et la présentation des façades
>     - Route des développeurs et à exécuter rapidement
>     - Sécurisation de l’accès
>     - Analytique et métriques
> - Contrôle et un aperçu avec une plate-forme de gestion de l’API
> - À l’aide de solutions de cloud vs sur site
> - Gestion de l’API Azure

## <a name="apis"> </a>API et des opérations

Les API sont le fondement d’une instance de service de gestion de l’API. Chaque API représente un ensemble d’opérations disponibles pour les développeurs. Chaque API contienne une référence au service principal qui implémente l’API et sa table des opérations pour les opérations effectuées par le service principal. Opérations de gestion de l’API sont hautement configurables, de contrôler les URL mappage, requête et les paramètres de chemin d’accès, demande et contenu de la réponse et la mise en cache de réponse opération. Limite de débit, des quotas et des stratégies de restriction IP peuvent également être implémentées au niveau de l’API ou opération individuelle.

Pour plus d’informations, consultez [comment créer des API][] et [Ajouter des opérations à une API][].


## <a name="products"></a> Produits

Les produits sont l’API est surfacés aux développeurs. Produits de gestion de l’API ont un ou plusieurs des API et sont configurés avec un titre, description et les conditions d’utilisation. Produits peuvent être **ouverte** ou **protégé**. Produits protégés doivent être souscrite avant de pouvoir être utilisés, lors de l’ouverture produits peuvent être utilisés sans abonnement. Lorsqu’un produit est prêt à être utilisé par les développeurs, il peut être publié. Une fois qu’il est publié, il peut être affichée (et pour les produits protégés abonné à) par les développeurs. Approbation de l’abonnement est configurée au niveau du produit et peut exiger l’approbation administrateur, soit être acceptés automatiquement.

Les groupes sont utilisés pour gérer la visibilité des produits pour les développeurs. Produits accordent la visibilité aux groupes, et les développeurs peuvent afficher et s’abonner aux produits qui sont visibles pour les groupes dont ils font partie. 

Pour plus d’informations, consultez [comment créer et publier un produit][] et la vidéo suivante.

> [AZURE.VIDEO using-products]

## <a name="groups"></a> Groupes

Les groupes sont utilisés pour gérer la visibilité des produits pour les développeurs. Gestion de l’API a les groupes suivants du système immuable.

-   **Les administrateurs** , les administrateurs d’abonnement Azure sont membres de ce groupe. Les administrateurs gèrent les instances du service de gestion de l’API, la création de l’API, les opérations et les produits qui sont utilisés par les développeurs.
-   **Les développeurs** - portail développeur authentifié les utilisateurs appartiennent à ce groupe. Les développeurs sont les clients qui génèrent des applications à l’aide de votre API. Les développeurs sont autorisés à accéder au portail développeur et créer des applications qui appellent les opérations d’une API.
-   **Invités** - utilisateurs du portail développeur non authentifiés, tels que les clients potentiels en visitant le portail de développement d’une instance de gestion de l’API appartiennent à ce groupe. Ils peuvent être accordées à certain accès en lecture seule, telles que la possibilité d’afficher des API, mais pas de les appeler.

En plus de ces groupes, les administrateurs peuvent créer des groupes personnalisés ou [exploiter des groupes externes dans les locataires Azure Active Directory associés](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Personnalisé et des groupes externes peuvent être utilisés avec les groupes système en donnant aux développeurs visibilité et l’accès aux produits d’API. Par exemple, vous pourriez créer un groupe personnalisé pour les développeurs de l’affilié à une organisation partenaire et leur permettre l’accès aux API à partir d’un produit contenant uniquement les API correspondantes. Un utilisateur peut être membre de plusieurs groupes.

Pour plus d’informations, consultez [comment créer et utiliser des groupes][].

## <a name="developers"></a> Les développeurs

Les développeurs représentent les comptes d’utilisateur dans une instance de service de gestion de l’API. Les développeurs peuvent être créés ou invités à rejoindre par les administrateurs, ou ils peuvent inscrire à partir du [portail de développement][]. Chaque développeur est membre d’un ou plusieurs groupes et peut s’abonner aux produits qui accordent la visibilité de ces groupes.

Lorsque les développeurs s’abonner à un produit qu’ils bénéficient de la clé primaire et secondaire du produit. Cette clé est utilisée lors d’appels dans les API de produits.

Pour plus d’informations, consultez [comment créer ou inviter les développeurs][] et [associer les groupes avec les développeurs][].

## <a name="policies"></a> Stratégies

Les stratégies sont une fonctionnalité puissante de gestion d’API qui permettent de l’éditeur modifier le comportement de l’API de configuration. Les stratégies sont un ensemble d’instructions qui sont exécutées de façon séquentielle dans la demande ou la réponse d’une API. Les instructions les plus courants incluent la conversion du format à partir de XML vers JSON et appel de limitation du débit pour limiter la durée des appels entrants à partir d’un développeur, et de nombreuses autres stratégies sont disponibles.

Les expressions de stratégie peuvent être utilisées en tant que valeurs d’attribut ou de valeurs de texte dans une des stratégies de gestion de l’API, sauf indication contraire de la stratégie. Certaines stratégies comme les stratégies de [flux de contrôle](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) et de [définir de variable](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) sont basées sur des expressions de stratégie. Pour plus d’informations, consultez [Stratégies avancées](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), des [expressions de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx)et regardez la vidéo suivante.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Pour obtenir une liste complète des stratégies de gestion de l’API, consultez [référence de la stratégie][]. Pour plus d’informations sur l’utilisation et la configuration des stratégies, reportez-vous à la section [des stratégies de gestion de l’API][]. Pour un didacticiel sur la création d’un produit dont le taux limite et de quota, consultez [comment créer et configurer les paramètres avancés de produit][]. Pour une démonstration, consultez la vidéo suivante.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"></a> Portail des développeurs

Le portail de développement est où les développeurs peuvent obtenir des informations sur vos opérations API, d’affichage et d’appel et s’abonner à des produits. Clients potentiels peuvent visiter le portail des développeurs, afficher des API et des opérations et de l’inscription. L’URL de votre portail développeur se trouve sur le tableau de bord dans le portail classique d’Azure pour votre instance de service de gestion de l’API.

Vous pouvez personnaliser l’apparence de votre portail développeur par l’ajout de contenu personnalisé, personnalisation des styles et l’ajout de votre marque.

## <a name="api-management-and-the-api-economy"></a>API de gestion et l’économie de l’API

Pour en savoir plus sur la gestion de l’API, regardez la présentation suivante de la conférence Microsoft Ignite 2015.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Portail des développeurs]: #developer-portal

[Comment créer des API]: api-management-howto-create-apis.md
[Comment faire pour ajouter des opérations à une API]: api-management-howto-add-operations.md
[Comment faire pour créer et publier un produit]: api-management-howto-add-products.md
[Comment créer et utiliser des groupes]: api-management-howto-create-groups.md
[Comment faire pour associer les groupes aux développeurs]: api-management-howto-create-groups.md#associate-group-developer
[Comment créer et configurer les paramètres avancés de produit]: api-management-howto-product-with-rules.md
[Comment créer ou inviter les développeurs]: api-management-howto-create-or-invite-developers.md
[Référence de la stratégie]: api-management-policy-reference.md
[Stratégies de gestion de l’API]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 
