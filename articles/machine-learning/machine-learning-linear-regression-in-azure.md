<properties 
    pageTitle="À l’aide de la régression linéaire dans l’apprentissage automatique | Microsoft Azure" 
    description="Une comparaison des modèles de régression linéaire dans Excel et dans un Studio de formation de Machine Azure" 
    metaKeywords="" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/09/2016" 
    ms.author="kbaroni;garye" />

# <a name="using-linear-regression-in-azure-machine-learning"></a>À l’aide de la régression linéaire dans l’apprentissage automatique de Azure

> *Kate Baroni* et *Ben Boatman* sont des architectes de solutions d’entreprise de données analyses centre d’Excellence de Microsoft. Dans cet article, ils décrivent leur expérience de la migration d’une suite d’analyse de régression existante à une solution basée sur le cloud à l’aide de la formation de Machine Azure.  
 
&nbsp; 
  
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  
 
## <a name="goal"></a>Objectif

Notre projet a démarré avec deux objectifs à l’esprit :  

1. Analytique prédictive permet d’améliorer la précision des prévisions de recettes de notre organisation mensuel  
2. ML d’Azure permet de confirmer, optimisation, augmenter la rapidité et l’échelle de nos résultats.  

Comme beaucoup d’entreprises, notre organisation passe par un processus de prévisions de bénéfice mensuel. Notre petite équipe des analystes d’entreprise a été chargé à l’aide d’apprentissage automatique pour prendre en charge le processus et améliorer la précision de la prévision.  L’équipe a passé plusieurs mois à collecter des données provenant de plusieurs sources et les attributs de données en cours d’exécution grâce à l’analyse statistique, identifier les attributs de clé aux services de la prévision de vente.  Étapes suivantes a été pour commencer les modèles de régression statistique de prototypage sur les données dans Excel.  Dans quelques semaines, nous avons dû a surpassé du champ actuel et des finances, processus de prévision d’un modèle de régression d’Excel. C’est le résultat de la prédiction planifié.  


Ensuite, nous avons pris l’étape suivante pour transfert sur notre analytique prédictive ML Azure pour savoir comment Azure ML possible d’améliorer les performances prédictive.


## <a name="achieving-predictive-performance-parity"></a>Réalisation de parité performance prédictive

Il était notre priorité pour réaliser la parité entre les modèles de régression ML d’Azure et d’Excel.  Étant donné les mêmes données et la même division, de formation et de test des données que nous voulons obtenir des performances prédictive entre Excel et Azure ML.   Au départ nous a échoué. Le modèle Excel surpassé le modèle Azure ML.   La défaillance était dû à un manque de compréhension du paramètre outil base dans Azure ML. Après une synchronisation avec l’équipe de produit ML d’Azure, nous acquise à une meilleure compréhension de la configuration requise pour nos jeux de données de base et atteint la parité entre les deux modèles.  

### <a name="create-regression-model-in-excel"></a>Créer un modèle de régression dans Excel
Notre régression Excel utilisé le modèle standard de régression linéaire à l’utilitaire d’analyse de Excel. 

Nous avons calculé *signifie absolu % erreur* et que vous utilisé en tant que la mesure de performance pour le modèle.  Il fallait pour parvenir à un modèle de travail à l’aide d’Excel.  Nous avons beaucoup de la formation dans l’expérience d’Azure ML qui au final était utile de comprendre les impératifs.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Créer une expérience comparable dans l’apprentissage automatique de Azure  
Nous avons suivi ces étapes pour créer notre expérience dans Azure ML :  

1.  Télécharger le groupe de données dans un fichier csv à Azure ML (très petit fichier)
2.  Création d’une nouvelle expérience et utilisés de la [Sélection des colonnes dans un groupe de données] [ select-columns] module pour sélectionner les mêmes fonctionnalités de données utilisées dans Excel   
3.  Utiliser les [Données fractionnées] [ split] module (avec le mode *d’Expression Relative* ) pour répartir les données dans les mêmes jeux de train exacte avait été fait dans Excel  
4.  Expérimentées avec la [Droite de régression linéaire] [ linear-regression] module (options par défaut uniquement), documentée et comparer les résultats de notre modèle de régression Excel

### <a name="review-initial-results"></a>Revoir les résultats initiaux
Tout d’abord, le modèle Excel surpassé clairement le modèle Azure ML :  

|   |Excel|Azure ML|
|---|:---:|:---:|
|Performances|   |  |
|<ul style="list-style-type: none;"><li>Ajusté R carré</li></ul>| 0,96 |N/A|
|<ul style="list-style-type: none;"><li>Coefficient de <br />Détermination</li></ul>|N/A|   0.78<br />(faible précision)|
|Erreur absolue moyenne |  $9. 5M|  19.4 $ M|
|Erreur absolue (%) moyenne|   6.03 %|  12,2 %.

Lorsque nous avons exécuté nos processus et les résultats obtenus par les développeurs et les scientifiques des données de l’équipe Azure ML, ils fournie rapidement des conseils utiles.  

* Lorsque vous utilisez la [Régression linéaire] [ linear-regression] module de ML d’Azure, deux méthodes sont fournies :
    *  Descente de Gradient en ligne : Peuvent être plus adaptées pour des problèmes de plus grande envergure
    *  Ordinaire des moindres carrés : Il s’agit de la méthode que considérer la plupart des gens lorsqu’ils entendent régression linéaire. Pour les petits groupes de données, ordinaires moindres carrés peut être un choix plus optimal.
*  Pensez à ajuster le paramètre de poids de régularisation L2 pour améliorer les performances. Elle est définie à 0,001 par défaut et à notre petit jeu de données, nous la définissons à 0,005 pour améliorer les performances.    

### <a name="mystery-solved"></a>Mystère résolu !
Lorsque nous avons appliqué les recommandations, nous avons obtenu les mêmes performances de planification en ML d’Azure comme avec Excel :   

|| Excel|Azure ML (initiale)|Azure ML avec moindres carrés|
|---|:---:|:---:|:---:|
|Valeur étiquetée  |Chiffres réels (numérique)|même|même|
|Étudiant  |Excel -> données analyse -> régression|Régression linéaire.|Régression linéaire|
|Options d’apprentissage|N/A|Valeurs par défaut|moindres carrés ordinaires<br />L2 = 0,005|
|Ensemble de données|26 lignes, 3 fonctions, 1 étiquette.   Tous les numérique.|même|même|
|Fractionnement : Train|Excel formé sur les 18 premiers lignes, testées sur les 8 dernières lignes.|même|même|
|Fractionnement : Test|Formule de régression Excel appliquée aux 8 dernières lignes|même|même|
|**Performances**||||
|Ajusté R carré|0,96|N/A||
|Coefficient de détermination|N/A|0.78|0.952049|
|Erreur absolue moyenne |$9. 5M|19.4 $ M|$9. 5M|
|Erreur absolue (%) moyenne|<span style="background-color: 00FF00;">6.03 %</span>|12,2 %.|<span style="background-color: 00FF00;">6.03 %</span>|

En outre, les coefficients de Excel comparé pour les poids de la fonction dans le modèle formé d’Azure :

||Coefficients d’Excel|Poids de la fonctionnalité Azure|
|---|:---:|:---:|
|Interception/décalage|19470209.88|19328500|
|Fonctionnalité A|0.832653063|0.834156|
|Fonctionnalité B|11071967.08|11007300|
|Fonctionnalité C|25383318.09|25140800|

## <a name="next-steps"></a>Étapes suivantes

Nous voulions consommer le service web de ML d’Azure dans Excel.  Nos analystes d’entreprise s’appuient sur Excel et nous avons besoin d’une méthode à appeler le service web Azure ML avec une ligne de données d’Excel et de sorte qu’elle retourne la valeur prévue pour Excel.   

Nous souhaitions également optimiser notre modèle, avec les options et les algorithmes disponibles dans Azure ML.

### <a name="integration-with-excel"></a>Intégration avec Excel
Notre solution était de mettre notre modèle de régression Azure ML en créant un service web à partir du modèle formé.  Dans quelques minutes, le service web a été créé et nous pouvons l’appeler directement à partir de Excel pour retourner une valeur de chiffre d’affaires prévu.    

La section du *Tableau de bord de Web Services* inclut un classeur Excel à télécharger.  Le classeur est déjà mis en forme avec les informations de schéma et les API de service web intégrées.   Lorsque vous cliquez sur *Télécharger un classeur Excel*, il s’ouvre et vous pouvez l’enregistrer sur votre ordinateur local.    

![][1]
 
Avec le classeur ouvert, copiez les paramètres prédéfinis dans la section paramètre bleue comme indiqué ci-dessous.  Une fois les paramètres entrés, Excel appelle le service web AzureML et un score de la manière prévue étiquettes seront affiche dans la section valeurs prédites verte.  Le classeur va continuer à créer des prévisions pour les paramètres basés sur votre modèle formé pour tous les articles de ligne entré sous l’onglet Paramètres.   Pour plus d’informations sur l’utilisation de cette fonctionnalité, reportez-vous à la section [utilisation d’un Service Web de Azure Machine Learning à partir d’Excel](machine-learning-consuming-from-excel.md). 

![][2]
 
### <a name="optimization-and-further-experiments"></a>Optimisation et autres expériences
Maintenant que nous avons eu une base avec notre modèle Excel, nous avons déplacé en avance afin d’optimiser notre modèle de régression linéaire Azure ML.  Nous avons utilisé le module de [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] d’améliorer notre sélection de données initiales éléments et nous aidé à atteindre une amélioration des performances de 4,6 % signifie que l’erreur absolue.   Pour les projets à venir, nous allons utiliser cette fonction qui a pu enregistrer nous semaines lors de l’itération via des attributs pour rechercher le jeu de fonctionnalités à utiliser pour la modélisation de données.  

Ensuite, nous prévoyons d’inclure des algorithmes supplémentaires comme [bayésien] [ bayesian-linear-regression] ou [Amplifier les arbres de décision] [ boosted-decision-tree-regression] dans notre expérience, pour comparer les performances.    

Si vous souhaitez expérimenter de régression, un dataset bon à essayer est le dataset d’exemple de régression de l’efficacité énergétique, qui a beaucoup d’attributs numériques. Le dataset est fourni dans le cadre des jeux de données exemple dans un Studio de ML.  Vous pouvez utiliser une variété de modules d’apprentissage pour prévoir la charge de chauffage ou de refroidissement de charge.  Le tableau ci-dessous est qu'une comparaison des performances de régression différente apprend contre la prédiction de dataset de l’efficacité énergétique pour la charge de refroidissement de variable cible : 

|Modèle|Erreur absolue moyenne|Erreur en moyenne de racine carré|Erreur absolue relative|Relative au carré erreur|Coefficient de détermination
|---|---|---|---|---|---
|Arbre de décision amplifié|0.930113|1.4239|0.106647|0.021662|0.978338
|Régression linéaire (profondeur de dégradé)|2.035693|2.98006|0.233414|0.094881|0.905119
|Régression de réseau neuronal|1.548195|2.114617|0.177517|0.047774|0.952226
|Régression linéaire (ordinaire moindres carrés)|1.428273|1.984461|0.163767|0.042074|0.957926  

## <a name="key-takeaways"></a>Points clés 

Nous avons beaucoup appris par de régression d’Excel en cours d’exécution et les expériences d’apprentissage automatique de Azure en parallèle. Création du modèle de référence dans Excel et en les comparant aux modèles à l’aide d’Azure ML [Régression linéaire] [ linear-regression] ayant permis de nous apprendre ML d’Azure, et nous avons découvert des opportunités d’amélioration des performances de sélection et le modèle de données.         

Nous avons trouvé également qu’il est conseillé d’utiliser la [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] pour accélérer les projets futurs de prévision.  En appliquant la sélection des fonctionnalités à vos données, vous pouvez créer un modèle amélioré dans Azure ML avec meilleures performances globales. 

La capacité de transférer prédictive analytique prévisions d’Azure ML à Excel systémiques permet une augmentation significative de la capacité de fournir correctement les résultats à un public large business.     


## <a name="resources"></a>Ressources
Certaines ressources sont répertoriées pour vous aider à qu'utiliser avec la régression :  

* Régression dans Excel.  Si vous n’avez jamais de régression dans Excel, ce didacticiel facilite : [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Régression par rapport à la prévision  Tyler Chessman a écrit un article de blog expliquant comment de la prévision dans Excel, qui contient la description du bon débutant de régression linéaire de séries temporelles. [http://sqlmag.com/SQL-Server-Analysis-Services/Understanding-Time-Series-Forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
*   Ordinaire des carrés régression linéaire : Défauts, les problèmes et les pièges.  Pour une introduction et une discussion de régression : [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ )

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
