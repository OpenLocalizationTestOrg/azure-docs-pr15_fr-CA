<properties
    pageTitle="Types de build et la qualité du modèle : recommandations API | Microsoft Azure"
    description="Recommandations de formation Machine Azure – guide de démarrage rapide"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="luisca"/>

#  <a name="build-types-and-model-quality"></a>Types de build et la qualité des modèles réduits #

<a name="TypeofBuilds"></a>
## <a name="supported-build-types"></a>Types de build pris en charge ##

L’API de recommandations prend actuellement en charge deux types de génération : *recommandation* et *FBT*. Chacun est construit à l’aide de différents algorithmes, et chacun a des fonctions différentes. Ce document décrit chacune de ces versions et les techniques permettant de comparer la qualité des modèles générés.

Si vous n’avez pas déjà fait, nous recommandons de procéder à la [guide de démarrage rapide](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### <a name="recommendation-build-type"></a>Type de build de recommandation ###

Le type de build recommandation utilise une factorisation de matrice pour fournir des recommandations. Il génère des vecteurs de [fonction latente](https://en.wikipedia.org/wiki/Latent_variable) basés sur vos transactions pour décrire chaque élément et utilise ensuite ces vecteurs latentes pour comparer des éléments similaires.

Si le modèle basé sur les achats effectués dans votre magasin de produits électroniques pour former et fournir un téléphone Lumia 650 comme entrée pour le modèle, le modèle renvoie un ensemble d’éléments susceptibles d’être achetés par des personnes qui sont susceptibles d’acheter un téléphone Lumia 650. Les éléments ne peuvent pas être complémentaires. Dans cet exemple, il est possible que le modèle renvoie autres téléphones, car les personnes qui aiment le 650 Lumia aiment autres téléphones.

La génération de recommandation possède deux fonctionnalités qui le rendent attrayant :

Placement de * *la génération de la recommandation prend en charge *l’élément à froid* **

Les éléments qui n’ont pas d’utilisation importante sont appelés des éléments à froid. Par exemple, si vous recevez une livraison d’un téléphone, que vous n’avez jamais vendu avant, le système ne peut pas déduire les recommandations de ce produit sur les transactions en seuls. Cela signifie que le système doit apprendre à partir des informations sur le produit lui-même.

Si vous souhaitez utiliser le placement d’articles à froid, vous devez fournir les informations de fonctions pour chaque article dans le catalogue. Voici ce que les premières lignes de votre catalogue peut avoir l’aspect (format Remarque la clé = valeur pour les fonctionnalités).

>Pro2 6CX-00001, Surface, Surface, Type = matériel, stockage = 128 Go de mémoire = 4G, fabricant = Microsoft

>73H-00013, Wake, Xbox 360, jeux, Type = logiciel, langue = anglais, évaluation = Mature

>Pratiquant-0F05, Minecraft, Xbox 360, jeux,, * Type = logiciel, langue = espagnol, évaluation = jeunesse

Vous devez également définir les paramètres de génération suivants :

| Le paramètre de génération         | Notes
|------------------     |-----------
|*useFeaturesInModel*     | Elle a la valeur **true**.  Indique si les fonctionnalités peuvent être utilisées pour améliorer le modèle de recommandation.
|*allowColdItemPlacement*   | Elle a la valeur **true**. Indique si la recommandation doit également exécuter un push des éléments à froid via la similarité de fonction.
| *modelingFeatureList*   | Liste séparée par des virgules des noms de fonctionnalités pour être utilisé dans la génération de recommandations pour améliorer la recommandation. Par exemple, « langue, stockage » pour l’exemple précédent.

**La génération de la recommandation prend en charge les recommandations de l’utilisateur**

Une génération de recommandation prend en charge les [recommandations de l’utilisateur](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Cela signifie qu’il peut fournir des recommandations personnalisées pour les utilisateurs en fonction de leurs historiques de transaction. Pour des recommandations de l’utilisateur, vous pouvez fournir le nom d’utilisateur ou de l’historique récent des transactions pour cet utilisateur.

Un exemple classique, où vous pouvez appliquer les recommandations de l’utilisateur est à la connexion sur la page d’accueil. Il vous pouvez promouvoir le contenu qui s’applique à l’utilisateur spécifique.

Vous pourriez également appliquer un type de build recommandations lorsque l’utilisateur est sur le point d’extraire. À ce stade, vous avez la liste des éléments que l’utilisateur est sur le point d’achat, et vous pouvez fournir des recommandations basées sur le panier du marché actuel.

#### <a name="recommendations-build-parameters"></a>Paramètres de génération de recommandations

| Nom  |   Description |    Type, <br>  valeurs valides, <br> (valeur par défaut)
|-------|-------------------|------------------
| *NumberOfModelIterations* |   Le nombre d’itérations, que le modèle s’exécute est déterminée par le temps de calcul total et la précision du modèle. Plus le nombre, la plus précise le modèle, mais le temps de calcul prend plus de temps.  |   Entier, <br>  10 à 50 <br>(40)
| *NumberOfModelDimensions* |   Le nombre de dimensions correspond au nombre de fonctionnalités que le modèle va essayer de trouver au sein de vos données. L’augmentation du nombre de dimensions permettra de mieux affiner les résultats en clusters plus petits. Toutefois, trop de dimensions empêche le modèle de recherche des corrélations entre les éléments. |  Entier, <br> 10 à 40 <br>(20) |
| *ItemCutOffLowerBound* |  Définit le nombre minimal de points de l’utilisation que doit être un élément pour que soit considéré comme partie du modèle. |        Entier, <br> 2 ou plus <br> (2) |
| *ItemCutOffUpperBound* |  Définit le nombre maximal de points de l’utilisation que doit être un élément pour que soit considéré comme partie du modèle. |  Entier, <br>2 ou plus<br> (2147483647) |
|*UserCutOffLowerBound* |   Définit le nombre minimal de transactions, que vous devez avoir réalisé un utilisateur pour être considéré comme partie du modèle. | Entier, <br> 2 ou plus <br> (2)
| *UserCutOffUpperBound* |  Définit le nombre maximal de transactions, que vous devez avoir réalisé un utilisateur pour être considéré comme partie du modèle. | Entier, <br>2 ou plus <br> (2147483647)|
| *UseFeaturesInModel* |    Indique si les fonctionnalités peuvent être utilisées pour améliorer le modèle de recommandation. |     Valeur booléenne<br> Par défaut : True
|*ModelingFeatureList* |    Liste séparée par des virgules des noms de fonctionnalités pour être utilisé dans la génération de recommandations pour améliorer la recommandation. Il dépend des fonctionnalités qui sont importantes. |    Chaîne, jusqu'à 512 caractères
| *AllowColdItemPlacement* |    Indique si la recommandation doit également exécuter un push des éléments à froid via la similarité de fonction. | Valeur booléenne <br> Par défaut : False
| *EnableFeatureCorrelation*    | Indique si les fonctionnalités peuvent être utilisées dans le raisonnement. | Valeur booléenne <br> Par défaut : False
| *ReasoningFeatureList* |  Liste de séparées par des virgules des noms de fonctionnalités à utiliser pour le raisonnement de phrases, telles que des explications de recommandation. Il dépend des fonctionnalités qui sont importantes pour les clients. | Chaîne, jusqu'à 512 caractères
| *EnableU2I* | Activer les recommandations personnalisées, également appelées utilisateur article les recommandations (U2I). | Valeur booléenne <br>Par défaut : True
|*EnableModelingInsights* | Définit si l’évaluation en mode hors connexion doit être effectuée pour rassembler des informations de modélisation (autrement dit, les mesures de précision et de la diversité). Si définie sur true, un sous-ensemble de données ne doit pas servir pour la formation, car il doit être réservé pour le test du modèle. Apprenez-en plus sur [les évaluations en mode hors connexion](#OfflineEvaluation). | Valeur booléenne <br> Par défaut : False
| *SplitterStrategy* | Si Activer perspectives de modélisation a la valeur *true*, c’est comment les données doivent être séparées à des fins d’évaluation.  | Chaîne, *RandomSplitter* ou *LastEventSplitter* <br>Par défaut : RandomSplitter


<a name="FBTBuild"></a>
### <a name="fbt-build-type"></a>Type de build FBT ###

La génération de (FBT) ensemble fréquemment achetée effectue une analyse qui compte le nombre d’occurrences de deux ou trois produits différents cofinancement entre eux. Il trie ensuite les jeux basés sur une fonction de similarité (**des occurrences**, **Jaccard**, **soulevez**).

Considérer comme des moyens pour normaliser les occurrences des **Jaccard** et **soulevez** .  Cela signifie que les articles seront retournés uniquement si leur lieu d’achat ainsi que l’élément de départ.

Dans notre exemple de téléphone Lumia 650, téléphone X apparaîtront uniquement si le téléphone X a été acheté dans la même session que le téléphone Lumia 650. Parce que cela soit peu probable, nous pouvons prévoir les éléments complémentaires à la 650 Lumia doit être retourné ; par exemple, une protection d’écran ou un adaptateur d’alimentation pour le 650 Lumia.

Actuellement, deux éléments sont supposées être acheté dans la même session s’ils se produisent dans une transaction avec le même ID d’utilisateur et d’un horodatage.

FBT builds ne gèrent pas les éléments à froid, car par définition, ils s’attendent deux articles à acheter dans la même transaction. Alors que les builds FBT peuvent renvoyer des ensembles d’éléments (triplets), ils ne gèrent pas les recommandations personnalisées car elles acceptent un élément unique de semences comme entrée.


#### <a name="fbt-build-parameters"></a>Paramètres de génération FBT

| Nom  |   Description |       Type,  <br> valeurs valides, <br> (valeur par défaut)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | Le niveau de risque : le modèle est. Nombre d’occurrences des éléments à prendre en considération pour la modélisation. |  Entier, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Délimite le nombre d’éléments dans un ensemble de fréquent.| Nombre entier  <br> 2-3 <br> (2)
| *FbtMinimalScore* | Score minimal ayant un ensemble fréquent doit figurer dans les résultats renvoyés. Le plus élevé possible. | Double <br> 0 et versions ultérieures <br> (0)
| *FbtSimilarityFunction* | Définit la fonction de similarité à utiliser lors de la génération. **Soulevez** favorise serendipity, **une occurrence** favorise la prévisibilité et **Jaccard** est un compromis entre les deux. | Chaîne,  <br>  <i>cooccurrence, ascenseur, jaccard</i><br> Par défaut : <i>jaccard</i>

<a name="SelectBuild"></a>
## <a name="build-evaluation-and-selection"></a>Évaluation de la build et la sélection ##

Ce guide peut vous aider à déterminer si vous devez utiliser un recommandations ou une génération FBT, mais il ne fournit pas une réponse définitive dans les cas où vous pouvez utiliser une d’elles. Également, même si vous savez que vous souhaitez utiliser un type de build FBT, peut-être voulez-vous toujours choisir **Jaccard** ou **soulevez** comme fonction de similarité.

La meilleure façon de choisir entre deux versions différentes consiste à les tester dans le monde réel (évaluation en ligne) et de suivre un taux de conversion pour les différentes générations. Le taux de conversion peut être mesuré en fonction des clics de recommandation, le nombre réel des achats à partir de recommandations affichées, ou même sur les montants d’achat réel lorsque différentes recommandations ont été affichées. Vous pouvez sélectionner la mesure de votre taux de conversion en fonction de votre objectif d’entreprise.

Dans certains cas, vous souhaiterez peut-être évaluer le modèle hors connexion avant de l’insérer dans la production. Lors de l’évaluation en mode hors connexion n’est pas un remplacement pour une évaluation en ligne, il peut constituer une mesure.

<a name="OfflineEvaluation"></a>
## <a name="offline-evaluation"></a>Évaluation en mode hors connexion  ##

L’objectif d’une évaluation en mode hors connexion est de prédire une précision (le nombre d’utilisateurs que vous allez acheter un des éléments recommandés) et la diversité des recommandations (le nombre d’éléments qui sont recommandés).
Dans le cadre de l’évaluation des mesures de précision et de la diversité, le système trouve un échantillon d’utilisateurs et fractionne les transactions pour les utilisateurs en deux groupes : le groupe de données de formation et le groupe de données de test.

> [AZURE.NOTE]Pour utiliser les mesures d’en mode hors connexion, vous devez disposer des horodatages dans vos données d’utilisation.
> Les données de temps sont nécessaires pour fractionner l’utilisation correctement entre le test et la formation de groupes de données.

> Également, évaluation en mode hors connexion ne peut pas produire des résultats pour les fichiers d’utilisation de small. D’évaluation être complet, il faut un minimum de 1 000 points de l’utilisation du groupe de données de test.

<a name="Precision"></a>
### <a name="precision-at-k"></a>Précision-à-k ###
Le tableau suivant représente le résultat de l’évaluation en mode hors connexion de précision-à-k.

| K | 1 | 2 | 3 |   4 |     5
|---|---|---|---|---|---|
|Pourcentage |   13,75 | 18.04   | 21 |  24.31 | 26.61
|Utilisateurs de test |    10 000 |    10 000 |    10 000 |    10 000 |    10 000
|Utilisateurs considérés | 10 000 |    10 000 |    10 000 |    10 000 |    10 000
|Utilisateurs non pris en compte. | 0 | 0 | 0 | 0 | 0

#### <a name="k"></a>K
Dans le tableau précédent, *k* représente le nombre de recommandations pour le client. Le tableau est modifié comme suit : « Si au cours de la période d’essai, qu’une recommandation a été indiquée pour les clients, 13,75 uniquement des utilisateurs serait ont acheté cette recommandation ». Cette instruction est basée sur l’hypothèse que le modèle a été formé avec les données d’achat. Une autre façon de dire cela est la précision à 1 : 13,75.

Vous remarquerez que comme plus d’éléments sont affichés sur le client, la probabilité que le client d’acheter un objet recommandée remonte. Pour l’expérience précédente, la probabilité est presque double à 26.61 % lorsque 5 éléments sont recommandés.

#### <a name="percentage"></a>Pourcentage
Le pourcentage d’utilisateurs qui interagi avec au moins une des recommandations *k* est affiché. Le pourcentage est calculé en divisant le nombre d’utilisateurs qui interagi avec au moins une recommandation par le nombre total d’utilisateurs considérés. Voir utilisateurs considérés pour plus d’informations.

#### <a name="users-in-test"></a>Utilisateurs de test
Les données de cette ligne représentent le nombre total d’utilisateurs dans le groupe de données de test.

#### <a name="users-considered"></a>Utilisateurs considérés
Un utilisateur est considéré comme si le système recommandé au moins *k* éléments basés sur le modèle généré à l’aide de la formation dataset.

#### <a name="users-not-considered"></a>Utilisateurs non pris en compte.
Les données de cette ligne représentent tous les utilisateurs non pris en compte. Les utilisateurs qui n’ont pas reçu d’au moins *k* éléments recommandés.

Utilisateur ne considéré pas = utilisateurs de test – utilisateurs considérés comme

<a name="Diversity"></a>
### <a name="diversity"></a>Diversité ###
Les mesures de diversité mesurent le type des éléments recommandés. Le tableau suivant représente le résultat de l’évaluation de diversité en mode hors connexion.

|Pot de centile |    0 à 90|  90-99| 99-100
|------------------|--------|-------|---------
|Pourcentage        | 34.258 | 55.127| 10.615


Nombre total d’éléments recommandés : 100 000

Éléments uniques recommandés : 954

#### <a name="percentile-buckets"></a>Compartiments de centile
Chaque compartiment centile est représenté par une étendue (valeurs minimales et maximales comprises entre 0 et 100). Les éléments proches de 100 sont les articles les plus populaires et les éléments proche de 0 sont les moins sollicités. Par exemple, si la valeur de pourcentage pour le compartiment de centile 99-100 est 10.6, il signifie que la fonction % 10.6 des recommandations renvoyées les premiers % un articles les plus populaires. La valeur minimale du compartiment centile est incluse, et la valeur maximale est exclusive, à l’exception de 100.
#### <a name="unique-items-recommended"></a>Éléments uniques recommandés
Les éléments uniques recommandés métrique indique le nombre d’éléments distincts qui ont été retournées pour évaluation.
#### <a name="total-items-recommended"></a>Nombre total d’éléments recommandé
Le nombre total d’éléments recommandé metric indique le nombre d’éléments recommandé. Certaines peuvent être des doublons.

<a name="ImplementingEvaluation"></a>
### <a name="offline-evaluation-metrics"></a>Mesures d’évaluation en mode hors connexion ###
Les précision et la diversité des mesures en mode hors connexion peuvent être utiles lorsque vous sélectionnez la build à utiliser. Au moment de la génération, dans le cadre de la recommandation ou de FBT respectifs paramètres de génération :

-   Définissez le paramètre de génération de *enableModelingInsights* à **true**.
-   Si vous le souhaitez, sélectionnez l' *splitterStrategy* (soit *RandomSplitter* ou *LastEventSplitter*).
*RandomSplitter* fractionne les données d’utilisation dans le train et de test définit selon le pourcentage de test donné *randomSplitterParameters* et aléatoires amorcer les valeurs.
*LastEventSplitter* fractionne les données d’utilisation dans le train et tester les jeux basés sur la dernière transaction pour chaque utilisateur.

Cela déclenche une build qui utilise uniquement un sous-ensemble des données pour la formation et le reste des données pour calculer les mesures d’évaluation.  Une fois la génération terminée, pour obtenir le résultat de l’évaluation, vous avez besoin d’appeler l' [API de métriques de build Get](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f), en passant le respectifs *modelId* et *buildId*.

 Voici la sortie JSON pour l’évaluation de l’échantillon.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }
