<properties
    pageTitle="Nouveautés recherche d’Azure | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Recherche Azure est un service de recherche entièrement gérés de nuage hébergé. Pour en savoir plus dans cette vue d’ensemble de la fonctionnalité."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="what-is-azure-search"></a>Quelle est la recherche de Azure ?

Recherche Azure est une solution de recherche en tant que service de cloud qui délègue la gestion de serveur et de l’infrastructure de Microsoft, ce qui vous laisse avec un service prêt à l’emploi, que vous pouvez remplir avec vos données et permet d’ajouter la recherche de votre web ou votre application mobile. Recherche Azure vous permet d’ajouter facilement une expérience de recherche robuste pour vos applications à l’aide d’une simple [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) ou [Kit de développement .NET](search-howto-dotnet-sdk.md) sans gestion de l’infrastructure de recherche ou de devenir un expert dans la recherche.

## <a name="give-your-users-a-powerful-search-experience"></a>Donner aux utilisateurs une expérience de recherche puissante

**Des requêtes puissantes** peuvent être formulées à l’aide de la [syntaxe de requête simple](https://msdn.microsoft.com/library/azure/dn798920.aspx), qui offre des opérateurs logiques, opérateurs de recherche d’une expression, les opérateurs de suffixe, les opérateurs de priorité. En outre, la [syntaxe de requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) permettent la recherche floue, recherche de proximité, augmentation de la durée et des expressions régulières. Recherche Azure prend également en charge les analyseurs lexicaux personnalisés pour permettre à votre application pour gérer les requêtes de recherche complexe utilisant la correspondance phonétique et les expressions régulières.

**Prise en charge de la langue** est [inclus pour 56 langues différentes](https://msdn.microsoft.com/library/azure/dn879793.aspx). En utilisant les analyseurs Lucene et analyseurs Microsoft (affinés par années de traitement dans Office et Bing de langage naturel), recherche d’Azure permet d’analyser le texte dans la zone de recherche de votre application pour gérer intelligemment les linguistique spécifique à la langue, y compris des temps de verbe, sexe, noms au pluriel irrégulières (par exemple, « souris » et « souris »), word de la composition, césure de mots (pour les langues sans espaces), etc..

**Suggestions de recherche** peut être activé pour les barres de recherche saisie semi-automatique et les requêtes de type transfert. [Réel des documents dans l’index sont proposées](https://msdn.microsoft.com/library/azure/dn798936.aspx) en tant qu’utilisateurs permet d’entrer une recherche partielle d’entrée.

**Mise en évidence de positionnement** [permet](https://msdn.microsoft.com/library/azure/dn798927.aspx) aux utilisateurs de voir l’extrait de texte dans chaque résultat qui contient les résultats de leurs requêtes. Vous pouvez choisir les champs en surbrillance des extraits de code de retour.

**Navigation à facettes** est facilement ajouté à votre page de résultats de recherche avec la recherche d’Azure. En utilisant [uniquement un seul paramètre de requête](https://msdn.microsoft.com/library/azure/dn798927.aspx), Azure recherche renvoie toutes les informations nécessaires pour construire une expérience de recherche à facettes dans l’interface utilisateur de votre application pour permettre à vos utilisateurs de zoom et de filtrer les résultats de la recherche (par exemple, filtrer catalogue les éléments par gamme de prix ou de la marque).

Prise en charge **géospatiales** vous permet de traiter, filtrer, et afficher les emplacements géographiques. Recherche Azure permet aux utilisateurs d’Explorer des données basée sur la proximité d’un résultat de recherche à un emplacement spécifié ou sur une zone géographique spécifique. Cette vidéo explique comment elle fonctionne : [Channel 9 : recherche d’Azure et géographique des données](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

**Filtres** peuvent être utilisés facilement incorporer une navigation à facettes dans l’interface utilisateur de votre application, améliorer la formulation de la requête, et filtre basé sur spécifié par l’utilisateur ou développeur-critères. Créer des filtres puissants à l’aide de la [syntaxe d’OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>Possibilité de vos développeurs avec un service facile à utiliser

**Haute disponibilité** garantit une expérience de service de recherche extrêmement fiable. Lorsque mise à l’échelle correctement, [recherche d’Azure offre un SLA de 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Ne **entièrement gérés** sous la forme d’une solution de bout en bout, recherche d’Azure nécessite absolument aucune gestion de l’infrastructure. Votre service peut être facilement adapté à vos besoins en mise à l’échelle en deux dimensions pour gérer plus de stockage de document, plus charge de requête ou les deux.

**Intégration de données** à l’aide [d’indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx) permet d’Azure recherche analyse automatiquement la base de données de SQL Azure, Azure DocumentDB ou [stockage des objets Blob Azure](search-howto-indexing-azure-blob-storage.md) pour synchroniser le contenu de votre index de recherche avec votre banque de données principale.

**Craquage de document** est disponible (en aperçu) [de lire et d’indexer des formats de fichiers principaux](search-howto-indexing-azure-blob-storage.md) , y compris Microsoft Office, ainsi que les documents PDF et HTML.

**Analytique de trafic de recherche** sont [facilement recueilli et analysé](search-traffic-analytics.md) pour déverrouiller des idées à partir de ce que les utilisateurs tapez dans la zone de recherche.

**Notation simple** est un avantage clé de recherche d’Azure. [Score de profils](https://msdn.microsoft.com/library/azure/dn798928.aspx) sont utilisés pour permettre aux entreprises de la pertinence du modèle en fonction des valeurs dans les documents eux-mêmes. Par exemple, vous pouvez plus récents produits ou produits figurent dans les résultats de la recherche d’une remise. Vous pouvez également créer des profils de score à l’aide de balises pour un score personnalisé en fonction des préférences de recherche de client que vous avez suivies et enregistrées séparément.

**Tri** est proposée pour plusieurs champs via le schéma d’index et puis activée ou désactivée au moment de la requête avec un paramètre de recherche unique.

**La pagination** et la limitation des résultats de votre recherche est [simple avec le contrôle précises](search-pagination-page-layout.md) qui recherche d’Azure offre par rapport à vos résultats de recherche.  

**Explorateur de recherche** vous permet de pour effectuer des requêtes par rapport à l’ensemble de vos index droit à partir du portail Azure de votre compte afin de pouvoir facilement tester des requêtes et affiner les profils d’évaluation.

## <a name="how-it-works"></a>Mode de fonctionnement

### <a name="1-provision-service"></a>1. service de fourniture de
Vous pouvez lancer un service de recherche d’Azure à l’aide du [Portail Azure](https://portal.azure.com/) ou l' [API de gestion des ressources Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

En fonction de la façon dont vous configurez le service de recherche, vous allez utiliser soit la couche libre de service qui est partagé avec d’autres abonnés Azure recherche ou un [payé couche](https://azure.microsoft.com/pricing/details/search/) qui dédie des ressources pour être utilisés uniquement par votre service. Lors de la mise en service de votre service, vous choisissez également la région du centre de données qui héberge votre service.

Selon le niveau de service que vous choisissez, vous pouvez mettre à l’échelle votre service en deux dimensions : 1) ajouter des réplicas d’augmenter votre capacité à gérer des charges de requêtes lourdes et 2) ajouter des Partitions pour ajouter un stockage pour des documents plus. En gérant le débit de stockage et de requête de document séparément, vous pouvez personnaliser votre service de recherche pour vos besoins spécifiques.

### <a name="2-create-index"></a>2. création d’index
Avant que vous pouvez télécharger votre contenu à votre service de recherche d’Azure, vous devez d’abord définir un index de recherche d’Azure. Un index est semblable à une table de base de données qui conserve vos données et peut accepter des requêtes de recherche. Vous définissez le schéma d’index pour mapper la structure des documents que vous souhaitez rechercher, similaires aux champs dans une base de données.

Le schéma de ces index peut soit créé dans le portail d’Azure, ou par programme [à l’aide du Kit de développement .NET](search-howto-dotnet-sdk.md) ou [l’API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Une fois que l’index est défini, vous pouvez ensuite télécharger vos données au service de recherche d’Azure où, il est indexé par la suite.

### <a name="3-index-data"></a>3. les données d’Index
Une fois que vous avez défini les champs et les attributs de votre index, vous êtes prêt à charger votre contenu dans l’index. Un modèle push et pull vous permet de télécharger les données vers l’index.

Le modèle d’extraction est assuré par les indexeurs peuvent être configurés pour la demande ou des mises à jour planifiées (voir [les opérations d’indexeur (API REST Service Azure recherche)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), ce qui permet à réceptionner facilement les données et les modifications de données à partir d’une DocumentDB d’Azure, base de données de SQL Azure, le stockage Blob Azure SQL Server hébergées dans un ordinateur virtuel d’Azure.

Le modèle push est fourni via le SDK ou des API reste utilisée pour l’envoi des documents mis à jour à un index. Vous pouvez distribuer des données à partir de pratiquement n’importe quel groupe de données au format JSON. Voir [Ajouter, mettre à jour, ou supprimer les Documents](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [comment utiliser le Kit de développement .NET)](search-howto-dotnet-sdk.md) pour obtenir des instructions sur le chargement des données.

### <a name="4-search"></a>4. recherche
Une fois que vous avez rempli votre index de recherche d’Azure, vous pouvez maintenant [effectuer des requêtes de recherche](https://msdn.microsoft.com/library/azure/dn798927.aspx) à votre point de terminaison de service à l’aide de simples requêtes HTTP avec l’API REST ou le Kit de développement .NET.

## <a name="try-it-now-for-free"></a>Essayez dès maintenant (gratuitement !)
Vous pouvez essayer Azure recherche dès aujourd'hui ! Si vous avez déjà un compte Azure, vous pouvez [fournir un service dans la couche libre](search-create-service-portal.md).

Si vous n’avez pas un compte Azure, que vous pouvez essayer une session gratuite, 60 minutes avec n° inscription obligatoire. Le [Service d’application Azure essayez](http://go.microsoft.com/fwlink/p/?LinkId=618214) , sélectionnez « Application Web ». Sélectionnez ensuite le modèle « ASP.NET + Azure recherche » pour commencer.
