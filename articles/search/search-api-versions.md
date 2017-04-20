<properties
   pageTitle="Versions des API de recherche d’Azure | Microsoft Azure | API de recherche"
   description="Stratégie de version pour Azure recherche reste API et la bibliothèque cliente dans le Kit de développement .NET."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/16/2016"
   ms.author="brjohnst"/>

# <a name="api-versions-in-azure-search"></a>Versions des API dans Azure recherche

Les mises à jour de la fonctionnalité recherche Azure les rouleaux sur une base régulière. Parfois, mais pas toujours, ces mises à jour requièrent de notre part publier une nouvelle version de notre API afin de conserver la compatibilité descendante. Une nouvelle version de publication vous permet de contrôler quand et comment intégrer les mises à jour du service de recherche dans votre code.

En règle générale, nous essayez de publier les nouvelles versions uniquement lorsque cela est nécessaire, car elle peut impliquer des efforts à mettre à niveau votre code pour utiliser une nouvelle version de l’API. Nous seulement publiera une nouvelle version si nous devons modifier certains aspects de l’API d’une façon qui rompt la compatibilité descendante. Cela peut se produire en raison de correctifs à des fonctionnalités existantes, ou en raison des nouvelles fonctionnalités qui changent existant surface d’API.

Nous suivons la même règle pour les mises à jour du Kit de développement logiciel. Le Kit de développement de recherche Azure suit les règles de [versioning sémantique](http://semver.org/) , ce qui signifie que sa version comporte trois parties : principal, secondaire et le numéro (par exemple, 1.1.0). Nous publierons une nouvelle version du Kit de développement qu’en cas de modifications avec les versions précédentes. Les mises à jour de la fonctionnalité sans rupture, nous incrémente la version secondaire, et pour corriger les bogues nous ne fera qu’augmenter le numéro de version.

##<a name="snapshot-of-current-versions"></a>Instantané des versions en cours 

Ci-dessous est un instantané de la version actuelle de toutes les interfaces de programmation à la recherche d’Azure. Vous trouverez les calendriers et autres détails dans les sections suivantes de ce document.

Interfaces|Dernière version principale|État
----------|-------------------------|------
[KIT DE DÉVELOPPEMENT .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1.1|Généralement disponible, publié février 2016
[Aperçu du Kit de développement .NET](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)|2.0-aperçu|Aperçu, publié août 2016
[API de service REST](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|Disponible
[Aperçu de service reste API](search-api-2015-02-28-preview.md)|2015-02-28-aperçu|Aperçu
[Gestion des API REST](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|Disponible

Pour les API reste, y compris la `api-version` sur chaque appel est nécessaire. Cela rend facile de cibler une version spécifique, par exemple un aperçu API. L’exemple suivant illustre comment la `api-version` paramètre est spécifié :

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Bien que chaque demande a un `api-version`, nous vous conseillons d’utiliser la même version pour toutes les demandes de l’API. Cela est particulièrement vrai lorsque de nouvelles versions d’API introduisent des attributs ou des opérations qui ne sont pas reconnues par les versions antérieures. Mélange de versions de l’API peut avoir des conséquences inattendues et doivent être évitées.
> 
L’API REST de Service et l’API REST de gestion sont créée indépendamment les uns des autres. De la similitude de numéros de version est une responsabilité pour les dommages.

Disponible (ou GA) API peuvent être utilisées dans la production et sont soumis à des contrats de niveau de service Azure. Versions d’évaluation ont des fonctionnalités expérimentales qui ne sont pas toujours migrées vers une version de GA. **Nous vous déconseillons fortement à l’aide des API de visualisation dans les applications de production.**

##<a name="sdk-version-roadmap"></a>Plan de version SDK

Chaque version du SDK .NET cible une version particulière de l’API REST de Service. Fonctionnalités de déploiement dans l’API REST tout d’abord des, implémentées dans le Kit de développement.

Le Kit de développement .NET est désormais disponible et le travail est déjà en cours sur la prochaine version. Le tableau suivant recherche dans les futures versions du Kit de développement afin que vous ayez une idée de ce qui est proche.

Version du Kit de développement .NET|Version de l’API REST|Fonctionnalités|ÊTA
----------------|----------------|--------|---
1.1|2015-02-28|Syntaxe de requête Lucene|Février 2016
2.0-aperçu|2015-02-28-aperçu|Les analyseurs personnalisés, les Blob Azure et les indexeurs d’un tableau, des mappages de champs, des ETags|Août 2016
2.x|Nouvelle version de l’API de GA|Identique à 2.0-aperçu|Début du 4e trimestre 2016

##<a name="about-preview-and-generally-available-versions"></a>À propos des versions d’aperçu et en règle générale

Recherche Azure toujours Pre-mises à jour de fonctionnalités expérimentales par le biais de l’API REST tout d’abord, puis par le biais de la version préliminaire du Kit de développement .NET.

Fonctionnalités d’aperçu ne sont pas garanties à migrer vers une version de GA. Considérant que les fonctionnalités dans une version de GA sont considérées comme stable et improbable que cela change, à l’exception des améliorations et des correctifs de compatibilité descendante small, fonctionnalités sont disponibles pour le test et l’expérimentation, dans le but de recueillir des commentaires sur la mise en œuvre et conception de la fonction. 

Toutefois, parce que les fonctionnalités sont susceptibles de changer, nous vous recommandons de ne pas écrire de code de production qui prennent une dépendance sur les versions d’évaluation. Si vous utilisez une ancienne version d’évaluation, nous vous recommandons de migrer vers la version (GA) disponible. 

Pour le Kit de développement .NET : conseils pour la migration de code, consultez [mise à niveau du Kit de développement .NET](search-dotnet-sdk-migration.md).

Disponibilité générale signifie que recherche d’Azure est désormais sous le contrat de niveau de service (SLA). Vous trouverez le contrat SLA à [Azure des contrats de niveau de Service de recherche](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

