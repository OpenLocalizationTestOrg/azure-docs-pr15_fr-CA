<properties
   pageTitle="Les scénarios courants de catalogue de données Azure | Microsoft Azure"
   description="Vue d’ensemble des scénarios communs d’Azure catalogue de données, y compris l’inscription et la découverte des sources de données de valeur élevée, permettant l’analyse décisionnelle libre-service et de l’acquisition des connaissances tribales existant sur les sources de données et les processus."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/03/2016"
   ms.author="maroche"/>


# <a name="azure-data-catalog-common-scenarios"></a>Scénarios courants de catalogue de données Azure

Cet article présente des scénarios courants où la catalogue de données Azure peut aider les organisations à tirer davantage de valeur à partir de leurs sources de données existantes.

## <a name="scenario-1---registration-of-central-data-sources"></a>Scénario #1 - enregistrement des sources de données centrales

Les organisations ont souvent un certain nombre de sources de données de valeur élevée. Ces sources de données sont ligne des systèmes OLTP, entrepôts de données et de veille stratégique / bases de données analytique. Souvent le nombre de systèmes et le chevauchement entre les systèmes, à mesure que les besoins de l’evolve business, et de l’entreprise elle-même son évolution par le biais de fusions et acquisitions.

Il est souvent difficile pour les utilisateurs de savoir où rechercher des données dans ces sources de données. Questions comme celles-ci sont trop répandues :

- Des systèmes RH trois utilisés dans la société, qui dois-je utiliser pour créer ce type de rapport ?
- Où puis-je pour obtenir les chiffres des ventes certifiés pour l’exercice comptable que vous venez de mettre fin ?
- Qui dois-je demander ou quelle est la procédure que pour obtenir l’accès à l’entrepôt de données dois-je utiliser ?
- Je ne sais pas si ces numéros sont à droite – qui puis-je poser pour un aperçu sur comment ces données sont censé être utilisé avant de partager ce tableau de bord avec mon équipe ?

Dans ce scénario, le catalogue de données Azure peut aider. Sources de données central, haute valeur, gérée par le service qui sont utilisés dans l’entreprise sont souvent le point de départ logique pour remplir le catalogue. Bien que tous les utilisateurs peuvent s’inscrire à une source de données, comprenant le catalogue kick-started avec les sources de données qui sont susceptibles de fournir de valeur pour le plus grand nombre d’utilisateurs aidera à encourager l’adoption et l’utilisation du système. Pour les clients de mise en route du catalogue de données Azure, identification et l’enregistrement des sources de données de clé utilisés par de nombreuses équipes différentes des consommateurs de données peuvent être la première étape de la réussite.

Ce scénario présente également une opportunité pour annoter les sources de données de valeur élevée pour les rendre plus faciles à comprendre et à y accéder. Un aspect clé de cet effort est d’inclure des informations sur la façon dont les utilisateurs peuvent demander l’accès à la source de données. Catalogue de données Azure permet aux utilisateurs de fournir l’adresse e-mail de l’utilisateur ou l’équipe responsable du contrôle des accès aux sources de données, des liens vers les outils existants ou de documentation ou de texte libre qui décrit le processus de demande d’accès. Grâce à ces informations est incluses dans le catalogue, les utilisateurs qui découvrir les sources de données enregistrées mais qui n’ont pas encore des autorisations d’accès aux données peuvent demander facilement accès à l’aide des processus définis et contrôlés par les propriétaires de la source de données.

## <a name="scenario-2---self-service-business-intelligence"></a>Scénario #2 : analyse décisionnelle libre-service

Bien que les solutions d’analyse décisionnelle d’entreprise traditionnels continuent à être un élément important des paysages de données beaucoup d’organisations, la modification rythme de l’entreprise a fait analyse Décisionnelle en libre-service de plus en plus important. Analyse Décisionnelle en libre-service permet de travailleurs de l’information et aux analystes de créer leurs propres rapports, des classeurs et des tableaux de bord sans compter sur une équipe informatique centrale – ou être limités par le calendrier et la disponibilité de cette équipe informatique.

Dans les scénarios d’analyse Décisionnelle libre-service, il est courant pour les utilisateurs de combiner des données provenant de plusieurs sources, dont un grand nombre ne peuvent pas avoir été utilisées auparavant pour BI et l’analyse. Bien que certaines de ces sources de données peuvent être déjà connu, il est souvent un processus pour découvrir ce qui doit intervenir pour localiser et évaluer les sources potentielles de données pour une tâche donnée.

En règle générale, ce processus de découverte est un manuel : les analystes utiliseront leurs connexions de réseau homologue pour identifier d’autres personnes qui travaillent avec les données recherchées. Après avoir trouvé une source de données qu’il peut être utilisé, mais le processus se répète à nouveau pour chaque ultérieures libre-service BI effort, avec plusieurs utilisateurs effectuant le processus manuel redondant même de découverte.

Avec le catalogue de données Azure, les utilisateurs peuvent rompre ce cycle de travail redondant. Une fois qu’une source de données a été découvert par le biais des moyens traditionnels, un analyste peut enregistrer la source de données pour le rendre plus facilement identifiable à l’avenir. Bien que l’utilisateur peut ajouter plus de valeur en annotant les actifs de données enregistrées, cela n’a pas besoin d’avoir lieu en même temps que l’inscription. Les utilisateurs peuvent contribuer au fil du temps, comme leur permis de planifications, progressivement Ajout de valeur aux sources de données enregistrées dans le catalogue.

Cette croissance organique du contenu du catalogue est un complément naturel de l’enregistrement initial des sources de données centrales. Préalable remplir le catalogue avec des données de nombreux utilisateurs ont besoin, un facteur de motivation pour une utilisation initiale et la découverte peut être. Permettant aux utilisateurs d’enregistrer et d’annoter des sources supplémentaires, un moyen de conserver et que leurs homologues, qui peut être.

Il est également important de noter que bien que ce scénario se concentre spécifiquement sur l’analyse Décisionnelle en libre-service, les motifs et les défis même s’appliquent à grande échelle projets BI d’entreprise. Tout effort qui implique un processus manuel de découverte de source de données est un effort qui peut ajouter de valeur pour l’entreprise à l’aide du catalogue de données Azure.

## <a name="scenario-3---capturing-tribal-knowledge"></a>Scénario #3 : capture des connaissances tribales

Comment savoir quelles données vous avez besoin pour votre travail et où trouver ces données ?

Si vous avez été dans votre travail pendant un certain temps, vous connaissez probablement. Vous avez passé par le biais de processus d’apprentissage progressive et dans le temps avez permis de découvrir les sources de données qui sont essentielles à votre journée de travail.

Lorsqu’un nouvel employé rejoint votre équipe, comment sait-il que les données qu’il doit faire son travail et où le trouver ?

Il est fort probable qu’il vous demande.

Ce transfert continu de connaissances tribales fait partie du processus de découverte de source de données dans les entreprises de toutes tailles. Plus hauts et expérimentés membres de l’équipe ont émergé des connaissances au fil des années, et de nouveaux membres de l’équipe ont appris à leur poser lorsqu’ils ont des questions. Les informations critiques plus souvent existent uniquement dans les têtes de quelques personnes, et lorsque des personnes sont en vacances ou laisser l’équipe, l’organisation sont affectées.

Parfois ces experts données rendra l’effort de documenter leurs connaissances, il partage par e-mail ou dans des documents Word sur un site d’équipe SharePoint. Bien que ceci peut être utile, qu'elle introduit un nouveau problème de découverte – comment personnes savoir quelle documentation existe et où le trouver...

Catalogue de données Azure fournit un emplacement pour le partage des connaissances tribales et pour le rendre facilement détectable. Les experts de données peuvent annoter directement les données de l’entreprise et peuvent également inclure des liens vers la documentation existante. Non seulement cette capture la base de connaissances, mais il met également à la base de connaissances dans la même expérience que celui qui est utilisée pour la découverte de source de données. Lorsqu’une personne utilise le catalogue pour découvrir une source de données non seulement s’il trouve la source lui-même, il y trouverez également des connaissances de l’expert qui existait précédemment que dans l’esprit de l’expert lui-même.
