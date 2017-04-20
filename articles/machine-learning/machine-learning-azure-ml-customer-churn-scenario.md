<properties
    pageTitle="Analyse à l’aide d’apprentissage automatique d’évolution du client | Microsoft Azure"
    description="Étude de cas de développement d’un modèle intégré pour l’analyse et l’attribution de scores résiliations"
    services="machine-learning"
    documentationCenter=""
    authors="jeannt"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016" 
    ms.author="jeannt"/>

# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Analyse de l’évolution du client à l’aide de formation de Machine Azure

##<a name="overview"></a>Vue d’ensemble
Cette rubrique présente une implémentation de référence d’un projet d’analyse évolution du client qui est créé à l’aide de Studio de formation de Machine Azure. Il traite des modèles génériques associés de façon holistique résoudre le problème des résiliations industrielle. Nous avons également de mesurer la précision des modèles qui sont générés par l’apprentissage automatique et nous évaluons les directions pour un développement ultérieur.  

### <a name="acknowledgements"></a>Accusés de réception

Cette expérience a été développée et testée par Serge Berger, données chercheur chez Microsoft et Roger Barga, anciennement responsable de produit pour l’apprentissage de Machine Microsoft Azure. L’équipe de documentation Azure grandement appréciées reconnaît leur expertise et les remercier de leur confiance pour le partage de ce livre blanc.

>[AZURE.NOTE] Les données utilisées pour cette expérience ne sont pas disponibles publiquement. Pour obtenir un exemple de la création d’un modèle d’apprentissage machine pour l’analyse de l’évolution du, voir : [Telco évolution du modèle](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) dans la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com/)


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="the-problem-of-customer-churn"></a>Le problème des résiliations
Les entreprises dans le marché grand public et dans tous les secteurs de l’entreprise ont à faire face à l’évolution du. Parfois rafraîchissements est excessif et influence les décisions de stratégie. La solution traditionnelle consiste à prévoir la propension haute churners et leurs besoins via un service de concierge, campagnes marketing ou en appliquant des dispenses spéciales. Ces approches peuvent varier du secteur de l’industrie et même à partir d’un cluster de consommateur particulier à un autre au sein d’une industrie (par exemple, télécommunications).

Le facteur commun est que les entreprises doivent réduire ces efforts de rétention client spécial. Par conséquent, une méthodologie naturelle serait de score de chaque client et la probabilité d’évolution du et les N premiers ceux d’adresses. Les meilleurs clients peuvent être les plus rentables ; par exemple, dans des scénarios plus sophistiqués, une fonction de profit est utilisée lors de la sélection des candidats pour une dérogation spéciale. Toutefois, ces considérations sont uniquement une partie de la stratégie globale pour faire face à l’évolution du. Entreprises doivent également tenir à risque de compte (et la tolérance au risque associé), le niveau et le coût de l’intervention et segmentation de client plausible.  

##<a name="industry-outlook-and-approaches"></a>Approches et outlook de l’industrie
Évolution du traitement élaboré est un signe d’une industrie mature. L’exemple classique est le secteur des télécommunications où les abonnés sont connus souvent passer d’un fournisseur à une autre. Cet évolution du volontaire est la principale préoccupation. En outre, les fournisseurs sont accumulées une connaissance significative d' *évolution du pilotes*, qui sont les facteurs que les clients lecteur pour basculer.

Par exemple, choix combiné ou le périphérique est un pilote bien connu d’évolution dans l’entreprise de téléphone mobile. Par conséquent, une stratégie les plus courants est à subventionnent le prix d’un combiné pour un prix complet à ses clients une mise à niveau et les nouveaux abonnés. Historiquement, cette stratégie a conduit aux clients le saut d’un fournisseur à un autre pour obtenir un escompte, qui à son tour, a invité les fournisseurs afin d’affiner leurs stratégies.

Haute volatilité des offres combiné est un facteur qui invalide très rapidement des modèles d’évolution du basés sur les modèles actuels de combiné. En outre, les téléphones mobiles ne sont pas uniquement des dispositifs de télécommunication ; ils sont également des instructions de façon (envisagez l’iPhone), et ces indicateurs sociaux sont en dehors de la portée des télécommunications régulière d’ensembles de données.

Le résultat net de modélisation est que vous ne peut pas concevoir une stratégie de sonore simplement en éliminant les raisons connues d’évolution du. En fait, une stratégie de modélisation en continu, y compris les modèles classiques de quantifier les variables par catégorie (par exemple, les arbres de décision), est **obligatoire**.

À l’aide de jeux de données volumineux sur leurs clients, organisations effectuent analytique des données (en particulier, la détection d’évolution du basé sur des données volumineuses) sous la forme d’une approche efficace à ce problème. Vous pouvez en savoir plus sur l’approche de données volumineuses pour le problème de l’évolution dans les recommandations de la section de l’ETL.  

##<a name="methodology-to-model-customer-churn"></a>Méthodologie pour les résiliations de modèle
Un processus de résolution de problèmes courant pour résoudre les résiliations est représenté dans les Figures 1-3 :  

1.  Un modèle de risque vous permet de prendre en compte l’impact des actions de probabilité et risque.
2.  Un modèle d’intervention vous permet d’envisager comment le niveau d’intervention peut affecter la probabilité de rafraîchissements et le montant du client valeur de durée de vie (CLV).
3.  Cette analyse se prête à une analyse qualitative qui est affectée à une campagne marketing proactive qui cible les segments de clientèle pour fournir l’offre optimale.  

![][1]

Cette approche de recherche vers l’avant est le meilleur moyen de traiter l’évolution du, mais il est fourni avec la complexité : il faut développer un archétype multimodèle et le traçage des dépendances entre les modèles. L’interaction entre les modèles peut être encapsulée comme illustré dans le diagramme suivant :  

![][2]

*Figure 4 : Unifiée archétype multimodèle*  

Interaction entre les modèles est clé si nous voulons fournir une approche holistique de la rétention client. Chaque modèle est nécessairement diminue dans le temps ; Par conséquent, l’architecture est une boucle implicite (semblable à l’archétype du définie par la norme de mining de données CRISP-DM, [***3***]).  

Le cycle d’ensemble de la décision de risques-marketing segmentation/décomposition est toujours une structure généralisée, qui est applicable à de nombreux problèmes de l’entreprise. Analyse de l’évolution du est simplement un représentant fort de ce groupe de problèmes car il présente toutes les caractéristiques d’un problème d’entreprise complexe qui ne permet pas une solution simplifiée de prévision. Les aspects sociaux de l’approche de l’évolution du moderne ne sont pas particulièrement mis en surbrillance dans l’approche, mais les aspects sociaux sont encapsulés dans l’archétype de modélisation, comme elles le seraient dans n’importe quel modèle.  

Un complément intéressant ici est analytique des données volumineuses. Télécommunications et les entreprises de vente au détail d’aujourd'hui collectent des données exhaustives sur leurs clients, et nous pouvons facilement prévoir que le besoin de connectivité multimodèle deviendra une tendance commune, étant donnée les tendances émergentes tels que l’Internet des objets et omniprésente, qui permettent à l’entreprise d’utiliser des solutions intelligentes à plusieurs niveaux.  

 
##<a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Mise en œuvre de l’archétype de modélisation dans le Studio de formation de Machine
Étant donné le problème vient d’être décrit, quelle est la meilleure façon de mettre en œuvre une modélisation intégrée et le score approche ? Dans cette section, nous va vous montrer comment nous avons accompli cela à l’aide de Studio de formation de Machine Azure.  

L’approche multimodèle est indispensable lors de la conception d’un archétype global pour l’évolution du. Même la score (prévision) une partie de l’approche doit être multimodèle.  

Le diagramme suivant illustre le prototype que nous créé, qui utilise des algorithmes de score quatre dans un Studio de formation de Machine pour prédire l’évolution du. La raison à l’aide d’une approche multimodèle n’est pas uniquement pour créer un classifieur ensemble pour augmenter la précision, mais également pour se protéger contre le surapprentissage et pour améliorer la sélection des caractéristiques.  

![][3]

*La figure 5 : Prototype d’un évolution du approche de modélisation*  

Les sections suivantes fournissent plus d’informations sur le score du modèle que nous avons implémentées à l’aide de Studio de formation de Machine de prototype.  

###<a name="data-selection-and-preparation"></a>Préparation et la sélection de données
Les données utilisées pour construire les modèles et les clients de score obtenu à partir d’une solution CRM verticale avec les données masquées afin de protéger la confidentialité des clients. Les données contient des informations sur les abonnements 8 000 aux États-Unis, et combine trois sources : mise en service de données (métadonnées d’abonnement), les données d’activité (utilisation du système) et données de prise en charge de client. Les données n’incluent pas de toute entreprise des informations sur les clients. par exemple, il n’inclut pas les notes de crédit ou de métadonnées fidélité.  

Pour plus de simplicité, ETL et le processus de nettoyage des données sont hors de portée parce que nous partons du principe que la préparation de données a déjà été faites ailleurs.   

Sélection des fonctionnalités pour la modélisation est basée sur signification préliminaire de notation du jeu de variables explicatives, inclus dans le processus qui utilise le module de forêt aléatoire. Pour la mise en oeuvre dans le Studio d’apprentissage Machine, nous avons calculé la moyenne, médiane et plages de fonctionnalités représentatives. Par exemple, nous avons ajouté des agrégats pour les données qualitatives, telles que les valeurs minimum et maximum pour l’activité de l’utilisateur.    

Nous avons capturé également les informations temporelles pour les six mois la plus récentes. Nous avons analysé les données pendant un an, et nous avons établi que, même s’il existait des tendances statistiquement significatives, l’effet sur l’évolution du est fortement compromise après six mois.  

Le point le plus important est que l’ensemble du processus, y compris les processus ETL, sélection des fonctionnalités et de modélisation a été implémenté dans le Studio d’apprentissage Machine, à l’aide de sources de données dans Microsoft Azure.   

Les diagrammes suivants illustrent les données qui a été utilisées.  

![][4]

*Figure 6 : Les extrait de la source de données (masqué)*  

![][5]


*Figure 7 : Les caractéristiques extraites de la source de données*
 
> Notez que ces données sont privées et par conséquent le modèle et les données ne peuvent pas être partagées.
> Toutefois, pour un modèle similaire à l’aide de données accessibles au public, consultez cet exemple essayer dans la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com/): [Évolution du client de télécommunications](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Pour plus d’informations sur la façon d’implémenter un modèle d’analyse évolution à l’aide de Cortana Intelligence Suite, nous vous recommandons également de [cette vidéo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) par un responsable de programme Senior ouvrables Hyong Tok. 
> 

###<a name="algorithms-used-in-the-prototype"></a>Algorithmes utilisés dans le prototype

Les algorithmes d’apprentissage machine quatre suivants nous permet de créer le prototype (aucune personnalisation) :  

1.  Régression logistique (longue distance)
2.  Arborescence de décision amplifiée (BT)
3.  Moyenne perceptron (AP)
4.  Machine à vecteurs supports (SVM)  


Le diagramme suivant illustre une partie de l’aire de conception d’expérimentation, qui indique l’ordre dans lequel les modèles ont été créés :  

![][6]  


*Figure 8 : Création de modèles dans un Studio de formation de Machine*  

###<a name="scoring-methods"></a>Méthodes de notation
Nous avons un score les quatre modèles à l’aide d’un groupe de données appelé formation.  

Nous a également présenté le dataset score pour un modèle comparable généré à l’aide de l’édition desktop de SAS entreprise mineur de 12. Nous avons mesuré la précision du modèle SAS et tous les quatre modèles de Studio de formation de Machine.  

##<a name="results"></a>Résultats
Dans cette section, nous vous présentons nos conclusions sur la précision des modèles, basé sur le score du dataset.  

###<a name="accuracy-and-precision-of-scoring"></a>Précision de l’attribution de scores
En règle générale, la mise en oeuvre dans l’apprentissage automatique de Azure est derrière SAS dans une précision d’environ 10-15 % (zone sous la courbe ou AUC).  

Toutefois, la mesure la plus importante dans l’évolution du est le taux de mauvaise classification : autrement dit, des churners N premiers comme prévu par le classifieur, qui les a réellement fait **pas** évolution du et encore reçu un traitement spécial ? Le diagramme suivant compare ce taux de mauvaise classification à tous les modèles :  

![][7]


*Figure 9 : Zone de prototype Passau sous la courbe*

###<a name="using-auc-to-compare-results"></a>Pour comparer les résultats à l’aide de AUC
Zone sous courbe (AUC) est une mesure qui représente une mesure globale de *séparation* entre les distributions de scores pour les populations positives et négatives. Il est similaire à la courbe caractéristique d’opérateur destinataire (ROC) traditionnels, mais une différence importante est que la métrique AUC ne requiert pas de choisir une valeur de seuil. Au lieu de cela, il récapitule les résultats sur **tous les** choix possibles. En revanche, le graphique ROC traditionnel affiche le taux de positif sur l’axe vertical et le taux de faux positifs sur l’axe horizontal et le seuil de classification varie.   

AUC est généralement utilisé en tant que mesure utile pour des algorithmes différents (ou différents systèmes) car elle permet des modèles à comparer par leurs valeurs AUC. Il s’agit d’une approche populaires dans les secteurs météorologique et des biosciences. Par conséquent, AUC représente un outil populaire pour l’évaluation des performances de classifieur.  

###<a name="comparing-misclassification-rates"></a>Comparaison des taux de mauvaise classification
Nous avons comparé les taux de mauvaise classification sur le groupe de données en question en utilisant les données CRM d’environ 8 000 abonnements.  

-   Le taux de mauvaise classification SAS était de 10 à 15 %.
-   Le taux de mauvaise classification Studio d’apprentissage Machine était de 15 à 20 % pour les churners de haut 200-300.  

Dans le secteur des télécommunications, il est important résoudre uniquement les clients qui ont le risque le plus élevé d’évolution du en leur proposant un service de concierge ou autre traitement spécial. À cet égard, la mise en oeuvre du Studio d’apprentissage Machine permet d’obtenir des résultats comparables à celles du modèle SAS.  

Pour les mêmes raisons, la précision est plus importante que la précision car nous intéresse principalement correctement classification churners potentiels.  

Le diagramme suivant de Wikipedia décrit la relation dans un graphisme vivant, facile à comprendre :  

![][8]

*Figure 10 : Les compromis entre l’exactitude et la précision*

###<a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Exactitude et la précision des résultats pour le modèle d’arborescence de décision amplifié  

Le graphique suivant affiche les résultats bruts d’attribution de scores à l’aide du prototype d’apprentissage automatique pour le modèle d’arborescence de décision amplifiée, qui se trouve être le plus précis possible parmi les quatre modèles :  

![][9]

*Figure 11 : Décision amplifiée arborescence modèle caractéristiques*

##<a name="performance-comparison"></a>Comparaison des performances
Nous avons comparé la vitesse à laquelle les données a été évaluées à l’aide de modèles Studio de formation de Machine et un modèle comparable, créé à l’aide de l’édition desktop de SAS entreprise mineur 12.1.  

Le tableau suivant récapitule les performances des algorithmes :  

*Le tableau 1. Performances générales (précision) des algorithmes*

| LR|BT|POINT D’ACCÈS|SVM|
|---|---|---|---|
|Modèle moyenne|Le meilleur modèle|Performants|Modèle moyenne|

Les modèles hébergés dans un Studio de formation Machine surpassé le SAS par 15 à 25 % de la vitesse d’exécution, mais précision a été en grande partie à égalité.  

##<a name="discussion-and-recommendations"></a>Discussion et recommandations
Dans le secteur des télécommunications, plusieurs méthodes sont apparues pour analyser l’évolution du, y compris :  

-   Dériver les mesures pour quatre catégories fondamentales :
    -   **Entité (par exemple, un abonnement)**. Fournir des informations de base sur l’abonnement et/ou le client qui fait l’objet d’une évolution du.
    -   **Activité**. Obtenir toutes les informations d’utilisation possible qui sont liées à l’entité, par exemple, le nombre de connexions.
    -   **Assistance à la clientèle**. Collecter des informations à partir des journaux de prise en charge de client pour indiquer si l’abonnement avait des problèmes ou des interactions avec le support technique.
    -   **Données d’entreprise et sur la concurrence**. Obtenir toutes les informations possibles sur le client (par exemple, peut être indisponible ou difficiles à suivre).
-   Utilisez l’importance pour la sélection des fonctionnalités de lecteur. Cela implique que le modèle d’arborescence de décision amplifié est toujours une approche de promesse de livraison.  

L’utilisation de ces quatre catégories crée l’illusion une approche simple *déterministe* , basée sur les index formés sur les facteurs raisonnables par catégorie, devrait suffire pour identifier les clients exposés au risque d’évolution du. Malheureusement, bien que cette notion semble plausible, il s’agit d’une compréhension false. La raison est qu’évolution du est un effet temporel et les facteurs pour l’évolution du sont généralement dans les états transitoires. Entraîne un client à prendre en compte la laissant aujourd'hui peut être différent demain, et il sera certainement différent six mois à partir de maintenant. Par conséquent, un *PROBABILISTE* modèle est une nécessité.  

Cette observation importante est souvent négligée dans les entreprises, qui préfère généralement une approche orientée sur l’intelligence d’entreprise analytique, principalement parce qu’il est une vente et admet automation simple.  

Cependant, la promesse d’analytique de libre service à l’aide de Studio de formation de Machine est que les quatre catégories d’informations, classés par division ou département, deviennent une source précieuse pour l’apprentissage machine sur l’évolution du.  

Une autre fonctionnalité intéressante entrant dans la formation de Machine Azure est la possibilité d’ajouter un module personnalisé dans le référentiel de modules prédéfinis qui sont déjà disponibles. Cette capacité permet pour l’essentiel, pour sélectionner les bibliothèques et créer des modèles pour des marchés verticaux. Il s’agit d’un critère important de différenciation d’apprentissage d’ordinateur Azure sur le marché.  

Nous espérons continuer cette rubrique à l’avenir, tout particulièrement lié aux analytique des données volumineuses.
  
##<a name="conclusion"></a>Conclusion
Ce document décrit une approche sensible à l’offensive commune d’évolution du client à l’aide d’une structure générique. Nous considéré comme un prototype pour modèles de score et implémentée à l’aide de formation de Machine Azure. Enfin, nous avons évalué l’exactitude et les performances de la solution de prototype en ce qui concerne les algorithmes comparables dans SAS.  

**Pour plus d’informations :**  

Ce document vous a aidé ? Faites-nous part de vos commentaires. Dites-nous sur une échelle de 1 (mauvais) à 5 (excellent), comment évaluez-vous ce document et pourquoi vous lui ayez donné cette note ? Par exemple :  

-   Sont vous évaluant élevé dû à des exemples, les écrans excellente, désactivez écriture ou une autre raison ?
-   Sont vous évaluant faible en raison d’une mauvaise exemples, captures d’écran floue ou écriture claire ?  

Vos commentaires nous aideront à améliorer la qualité des livres blancs que nous libérer.   

[Envoyer des commentaires](mailto:sqlfback@microsoft.com).
 
##<a name="references"></a>Références
Analytique prédictive de [1] : au-delà de la gestion des informations, ouest McKnight, des prévisions, juillet/août 2011, p. 18-20.  

L’article de Wikipedia [2] : [précision](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0 : Guide d’exploration de données étape par étape](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [données Marketing : engager vos clients plus efficacement et de valeur du lecteur](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco évolution du modèle](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) dans la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com/) 
 
##<a name="appendix"></a>Appendice

![][10]

*Figure 12 : Les instantanés d’une présentation sur l’évolution du prototype*


[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
