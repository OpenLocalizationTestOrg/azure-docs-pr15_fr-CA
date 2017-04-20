<properties
    pageTitle="Choisir un point de stock ou le niveau de tarification pour la recherche d’Azure | Microsoft Azure"
    description="Recherche Azure peut être mis en service à ces références : libre, de base et Standard, Standard étant disponible dans diverses configurations de ressources et les niveaux de capacité."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/24/2016"
    ms.author="heidist"/>

# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Choisissez un point de stock ou de la couche de tarification pour la recherche d’Azure

Recherche d’Azure, un [service est mis en service](search-create-service-portal.md) à un niveau de tarification spécifique ou d’un point de stock. Les options incluent le **libre**, **base**ou **Standard**, où **Standard** est disponible en plusieurs configurations de capacités. 

L’objectif de cet article est de vous aider à choisir un niveau. Si une capacité de s’avère pour être trop faible, vous devrez mettre en service un nouveau service à la couche supérieure et puis rechargez votre index. Il n’y a aucune mise à niveau du service même à partir d’une référence à un autre. 

> [AZURE.NOTE] Après avoir choisi un niveau et la [fourniture d’un service de recherche](search-create-service-portal.md), vous pouvez augmenter le réplica et partition compte au sein du service. Pour obtenir des instructions, consultez [niveaux d’échelle des ressources pour des charges de travail d’indexation et de requête](search-capacity-planning.md).

## <a name="how-to-approach-a-pricing-tier-decision"></a>L’approche d’une décision de niveau prix

Dans Azure, recherche, la couche détermine la capacité, la disponibilité d’une fonctionnalité pas. En règle générale, les fonctionnalités sont disponibles à tous les niveaux, y compris les fonctions de visualisation. La seule exception n’est aucune prise en charge pour les indexeurs en HD de S3.

> [AZURE.TIP] Nous vous recommandons de vous devez toujours configurer un service **gratuit** (une par abonnement, sans date d’expiration) afin que sa disponibles pour les projets de poids léger. Utilisez le service **gratuit** de test et d’évaluation ; créer un second service facturable à la couche **Standard** ou de **base** pour les plus grandes charges de travail de test ou de production.

Capacité et les coûts de l’exécution du service aller de pair. Informations dans cet article peuvent vous aider à choisir les SKU offre le juste équilibre, mais pour qu’il soit utile les, vous devez au moins les estimations sur les éléments suivants :

- Nombre et la taille de l’index que vous envisagez de créer
- Nombre et taille des documents à télécharger
- Une idée du volume de la requête, en termes de requêtes par seconde (QPS)

Nombre et la taille sont importants, car la limite maximale est atteinte via une limite sur le nombre de documents dans un service ou d’index, ou sur les ressources (stockage ou duplications) utilisés par le service. La limite réelle de votre service est retenue obtient utilisé en premier : ressources ou des objets.

Des estimations dans main, les étapes suivantes doivent simplifier le processus :

- **Étape 1** Passez en revue les descriptions SKU ci-dessous pour en savoir plus sur les options disponibles.
- **Étape 2** Répondez aux questions ci-dessous pour parvenir à une décision préliminaire.
- **Étape 3** Finaliser votre décision en examinant les limites physiques sur le stockage et la tarification.

## <a name="sku-descriptions"></a>Descriptions SKU

Le tableau suivant fournit des descriptions de chaque niveau. 

Couche|Principaux scénarios
----|-----------------
**Gratuit**|Un service partagé, sans frais, utilisé pour les petites charges de travail, enquête ou d’évaluation. Parce qu’il est partagé avec d’autres abonnés, débit de requêtes et l’indexation dépend de qui d’autre est l’utilisation du service. La capacité est petite (50 Mo ou 3 index à l’aide des 10 000 documents chacun).
**Base**|Charges de travail de production de petite taille sur du matériel dédié. Hautement disponible. La capacité est 3 réplicas et 1 partition (2 Go).
**S1**|1 standard prend en charge les combinaisons flexibles des partitions (12) et des duplications (12), utilisées pour les charges de travail de production moyenne sur du matériel dédié. Vous pouvez allouer les partitions et duplications dans les combinaisons prises en charge par un nombre maximal de 36 unités de recherche facturable. À ce niveau, les partitions sont de 25 Go et QPS est environ 15 requêtes par seconde.
**S2**|2 standard s’exécute plus grandes charges de travail à l’aide de la même unité de 36 recherche que S1, mais avec des réplicas et des partitions de tailles plus importantes. À ce niveau, les partitions sont de 100 Go et QPS est environ 60 requêtes par seconde.
**S3**|3 standard s’exécute proportionnellement plus grandes charges de travail sur les systèmes de fin supérieures, dans des configurations de 12 partitions ou réplicas de 12 unités de recherche sous 36. À ce niveau, les partitions sont 200 Go et QPS est 60 plus de requêtes par seconde. 
**S3 HD**|Standard 3 haute densité est conçue pour un grand nombre d’index plus petits. Vous pouvez avoir jusqu'à 3 partitions, à 200 Go. QPS est 60 plus de requêtes par seconde. 

> [AZURE.NOTE] Maximums de réplica et de partition sont facturés à en tant qu’unités de recherche (36 unité maximum par service), qui impose une limite d’effective inférieure à ce qu’implique le maximum à leur valeur faciale. Par exemple, pour utiliser le nombre maximal de 12 duplications, vous pouvez avoir au maximum 3 partitions (12 * 3 = 36 unités). De même, pour utiliser les partitions maximales, réduire réplicas à 3. Voir les [niveaux d’échelle des ressources pour les requêtes et les charges de travail d’indexation dans Azure recherche](search-capacity-planning.md) un graphique de combinaisons autorisées.

## <a name="review-limits-per-tier"></a>Révision des limites par niveau

Le tableau suivant est un sous-ensemble des limites des [Limites de Service de recherche d’Azure](search-limits-quotas-capacity.md). Il répertorie les facteurs susceptibles d’avoir un impact sur une décision de SKU. Vous pouvez faire référence à ce graphique lors de l’examen des questions ci-dessous.

Ressources|Gratuit|Base|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Contrat de niveau de service (SLA)|Aucun <sup>1</sup> |Oui |Oui  |Oui |Oui  |Oui 
Limites d’index|3|5|50|200|200|1000 <sup>2</sup>
Limites du document|10 000 au total|1 million par service|15 millions de partition |60 millions par partition|120 millions par partition |1 million par index
Partitions maximales|N/A |1 |12  |12 |12|3 <sup>2</sup>
Taille de la partition|50 Mo au total|2 Go par service|25 Go par partition |100 Go par partition (avec un maximum de 1,2 To par service)|200 Go par partition (avec un maximum de 2,4 To par service)|200 Go (jusqu'à un maximum de 600 Go par service)
Réplicas maximum|N/A |3 |12 |12 |12|12
Requêtes par seconde|N/A|~ 3 par le réplica|~ 15 par réplica|~ 60 par réplica|> 60 par réplica|> 60 par réplica

<sup>1</sup> libre et les points de stock aperçu ne proviennent pas avec les SLA. SLA est appliquées une fois qu’un point de stock est généralement disponible.

<sup>2</sup> S3 et S3 HD sont soutenues par les infrastructures identiques haute capacité, mais chacun d’eux atteint sa limite maximale de différentes manières. S3 cible un petit nombre d’index de très grande taille. Par conséquent, sa limite maximale est lié aux ressources (2,4 To pour chaque service). S3 HD cible un grand nombre d’index de très petite taille. À 1 000 index, S3 HD atteint ses limites sous forme de contraintes d’index. Si vous êtes un client de S3 HD qui nécessite plus de 1 000 index, contactez le Support Microsoft pour plus d’informations sur la façon de procéder.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Éliminer les points de stock qui ne répondent pas aux exigences 

Les questions suivantes peuvent vous aider à parvenir à la bonne décision SKU pour votre charge de travail.

1. Avez-vous des exigences du **Contrat de niveau de Service (SLA)** ? Limiter la décision de référence à la norme de base ou sans aperçu.
2. **Index combien** avez-vous besoin ? L’une des plus grandes variables factorisation dans une décision de référence est le numéro d’index pris en charge par chaque point de stock. Prise en charge de l’index est sensiblement différents niveaux dans les niveaux de tarification inférieurs. Exigences sur le nombre d’index peuvent être un déterminant principal d’une décision de SKU.
3. **Le nombre de documents** est chargé dans chaque index ? Le nombre et la taille des documents déterminera la taille éventuelle de l’index. En supposant que vous pouvez estimer la taille prévue de l’index, vous pouvez comparer ce nombre avec la taille de partition par référence, le total par le nombre de partitions nécessaires pour stocker un index de cette taille. 
4. **Quelle est la charge de requête attendu**? Une fois les besoins de stockage sont comprises, envisagez de charges de travail de requête. S2 et les deux SKU de S3 offrent équivalente débit, mais les exigences SLA va éliminer les références SKU de l’aperçu. 
5. Si vous envisagez d’utiliser la couche S2 ou S3, déterminez si vous avez besoin [d’indexeurs](search-indexer-overview.md). Les indexeurs ne sont pas encore disponibles pour le niveau S3 HD. Autre approche consiste à utiliser un modèle d’émission pour les mises à jour de l’index, où vous écrivez le code d’application pour distribuer un jeu de données pour un index.

La plupart des clients peuvent règle à un point de stock spécifique ou en fonction de leurs réponses aux questions ci-dessus. Si vous ne sont toujours pas que le SKU pour aller avec, vous pouvez publier des questions sur les forums MSDN ou StackOverflow ou contactez Azure Support pour obtenir des instructions.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>La décision de validation : la référence SKU offre un espace suffisant et QPS ?

La dernière étape, réversible [par service et par index de sections dans les limites de Service](search-limits-quotas-capacity.md) et la [page de tarification](https://azure.microsoft.com/pricing/details/search/) pour revérifier vos estimations par rapport aux limites de service et d’abonnement. 

Si les exigences de prix ou du stockage sont en dehors des limites, vous pouvez souhaiter refactoriser les charges de travail entre plusieurs services plus petits (par exemple). Sur un niveau plus granulaire, vous pourriez recréer les index pour être plus petit, ou utiliser des filtres pour rendre plus efficaces les requêtes.

> [AZURE.NOTE] Exigences de stockage peuvent être trop gonflés si les documents contiennent des données superflues. Dans l’idéal, les documents contiennent uniquement des métadonnées ou des données de recherche. Données binaires sont non consultable et doivent être stockées séparément (peut-être dans un stockage de table ou blob Azure) avec un champ de l’index pour contenir une référence d’URL à des données externes. La taille maximale d’un document individuel est 16 Mo (ou moins se vous sont plusieurs documents dans une seule demande de chargement en bloc). Pour plus d’informations, reportez-vous à la section [limites de Service de recherche d’Azure](search-limits-quotas-capacity.md) .

## <a name="next-step"></a>Étape suivante

Une fois que vous savez qui référence est adaptée, poursuivez ces étapes :

- [Créer un service de recherche dans le portail](search-create-service-portal.md)
- [Modifier la répartition des partitions et duplications à l’échelle de votre service](search-capacity-planning.md)

