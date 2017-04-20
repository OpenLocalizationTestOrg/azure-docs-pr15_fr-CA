<properties 
    pageTitle="Application d’apprentissage machine : Service de détection des anomalies | Microsoft Azure" 
    description="Les API de détection des anomalies est un exemple créé avec Microsoft Azure Machine Learning qui détecte des anomalies dans les données de série de temps avec des valeurs numériques qui sont répartis uniformément dans le temps." 
    services="machine-learning" 
    documentationCenter="" 
    authors="alokkirpal" 
    manager="jhubbard"
    editor="cgronlun" /> 

<tags 
    ms.service="machine-learning" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="multiple" 
    ms.date="10/11/2016" 
    ms.author="alokkirpal"/>


# <a name="machine-learning-anomaly-detection-service"></a>Machine de formation du Service de détection des anomalies#

##<a name="overview"></a>Vue d’ensemble

[Les API de détection des anomalies](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) est un exemple généré avec apprentissage automatique Azure qui détecte des anomalies dans les données de série de temps avec des valeurs numériques qui sont répartis uniformément dans le temps. 

Cette API peut détecter les types suivants de modèles anormales dans les données de série de temps :

* **Positive et négative des tendances**: par exemple, lorsque la surveillance de l’utilisation de la mémoire de l’informatique une tendance à la hausse peut-être être utiles comme il peut s’agir d’une fuite de mémoire

* **Modifications dans la plage dynamique de valeurs**: par exemple, lorsque vous surveillez les exceptions levées par un service en nuage, les modifications apportées à la plage dynamique de valeurs peuvent indiquer une instabilité dans l’état de santé du service, et

* **Pics et devient**: par exemple, lorsque vous surveillez le nombre d’échecs d’ouverture de session dans un service ou le nombre d’extractions dans un site de commerce électronique, pics ou creux indique un comportement anormal.

Ces détecteurs d’apprentissage machine suivre ces modifications dans les valeurs de temps et l’état des modifications en cours dans leurs valeurs comme les scores des anomalies. Ils ne nécessitent pas de réglage du seuil ad hoc et leurs scores peuvent être utilisés pour contrôler le taux de faux positifs. La détection des anomalies API est utile dans plusieurs scénarios tels que la surveillance du service de suivi des indicateurs de performance clés dans le temps, analyse de l’utilisation par le biais de mesures telles que le nombre de recherches, nombre de clics, la surveillance des performances par le biais de compteurs de mémoire, processeur, fichier lit, etc. au fil du temps.

L’offre de détection des anomalies est livré avec des outils utiles pour vous aider à démarrer. 

* L' [application web](http://anomalydetection-aml.azurewebsites.net/) vous aide à évaluer et visualiser les résultats de la détection d’anomalie API sur vos données. 

* L' [exemple de code](http://adresultparser.codeplex.com/) montre comment accéder à l’API et analysez les résultats dans C# par programme.

>[AZURE.NOTE]
>Essayez **les solutions informatiques des anomalies perspectives** alimenté par [cette API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection)
>
>Pour obtenir cette solution de bout en bout déployée sur votre abonnement Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **Démarrer ici >**</a>


##<a name="api-definition"></a>Définition de l’API

Le service fournit une API basée sur REST via HTTPS qui peut être consommée de différentes façons, y compris un site ou une application mobile, R, les Python, Excel, etc..  Vous envoyez vos données de série de temps à ce service via un appel de l’API REST, et il s’exécute à une combinaison de trois anomalies types décrits précédemment. Le service s’exécute sur la plate-forme de formation de Machine d’Azure, qui s’adapte aux besoins de votre entreprise en toute transparence et fournit un SLA.

###<a name="headers"></a>En-têtes
Veillez à inclure les en-têtes appropriés dans votre demande, qui doit être comme suit :

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Vous trouverez votre clé de compte à partir de votre compte, sur le [Marché de données Azure](https://datamarket.azure.com/account/keys). 

###<a name="score-api"></a>API de score

L’API de Score est utilisée pour exécuter la détection des anomalies sur série de données de temps non saisonnière. L’API s’exécute un certain nombre de DETECTEURS d’anomalies sur les données et retourne leurs scores des anomalies. La figure ci-dessous montre un exemple des anomalies capables de détecter l’API de Score. Cette série de temps a 2 modifications de niveau distinctes et 3 pics. Les points rouges indiquent l’heure à laquelle la modification du niveau est détectée, alors que les points noirs affichent les pointes détectés.

![API de score][1]
    
**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Corps de requête d’exemple**

Dans la demande ci-dessous, nous envoyer une série temporelle (illustrée tronquée) avec des points de données à partir de 1/3/2016 à 3/10/2016 et les paramètres de détecteurs de pic à l’API pour détecter si un de ces points de données est anormal. 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

La réponse à ce sera : 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

###<a name="scorewithseasonality-api"></a>API de ScoreWithSeasonality

L’API ScoreWithSeasonality est utilisé pour l’exécution de détection des anomalies sur les séries temporelles qui ont saisonnier. Cette API est utile pour détecter des écarts dans les saisonniers.  

La figure suivante illustre un exemple des anomalies constatées dans une série chronologique saisonnières. La série chronologique a un pic (le 1er point noir), deux creux (le 2e point noir et l’autre à la fin) et un changement de niveau (point rouge). Notez que les deux dip au milieu de la série temporelle et la modification du niveau sont uniquement perceptibles après que saisonniers composants sont supprimés de la série.

![Caractère saisonnier API][2]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Corps de requête d’exemple**

Dans la demande ci-dessous, nous envoyer une série temporelle (illustrée tronquée) avec des points de données à partir de 1/3/2016 à 3/10/2016 et les paramètres à l’API pour détecter si un de ces points de données est anormal. 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

La réponse à ce sera : 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
            "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
            "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
            ]
        }"
    }

###<a name="detectors"></a>Détecteurs

La détection des anomalies API prend en charge les détecteurs dans 3 grandes catégories. Vous trouverez plus d’informations sur les paramètres d’entrée spécifiques et les sorties de chaque détecteur dans le tableau suivant.

|Catégorie de détecteur|DÉTECTEUR|Description|Paramètres d’entrée|Sorties
|---|---|---|---|---|
|Détecteurs de pic|Détecteur de TSpike|Détecter les pics et les creux basé sur l’extrême, les valeurs est de quartiles premier et troisième|*tspikedetector.sensitivity :* prend entier dans la plage 1-10, défaut : 3 ; Des valeurs plus élevées interceptera plusieurs valeurs extrêmes, ce qui rend moins sensibles|TSpike : les valeurs binaires – 1 en cas de détection d’un pic d’activité/dip, « 0 » dans le cas contraire|
||Détecteur de ZSpike|Détecter les pics et les creux en fonction de la distance séparant les points de données sont à leur moyenne arithmétique|*zspikedetector.sensitivity :* prendre le nombre entier dans la plage par défaut de 1 à 10, : 3 ; Des valeurs plus élevées interceptera plus de valeurs extrêmes, ce qui la rend moins sensibles|ZSpike : les valeurs binaires – 1 en cas de détection d’un pic d’activité/dip, « 0 » dans le cas contraire|
|Détecteur de tendance lente|Détecteur de tendance lente|Détecter une tendance positive lente en fonction de la sensibilité du jeu|*trenddetector.sensitivity :* seuil de score de détecteur (par défaut : 3,25, 3,25 – 5 est une gamme raisonnable pour sélectionner cette option ; Plus le moins sensibles)|TScore : flottante nombre représentant le score d’anomalies sur les tendances|
|Modification du niveau détecteurs|DETECTEUR de changement de niveau unidirectionnel|Détecter le niveau vers le haut modifier en fonction de la sensibilité du jeu|*upleveldetector.sensitivity :* seuil de score de détecteur (par défaut : 3,25, 3,25 – 5 est une gamme raisonnable pour sélectionner cette option ; Plus le moins sensibles)|PScore : flottante nombre représentant de score d’anomalies sur vers le haut niveau de modification|
||DETECTEUR de changement de niveau bidirectionnelle|Détecter la modification du niveau à la fois vers le haut et vers le bas en fonction de la sensibilité du jeu|*bileveldetector.sensitivity :* seuil de score de détecteur (par défaut : 3,25, 3,25 – 5 est une gamme raisonnable pour sélectionner cette option ; Plus le moins sensibles)|RPScore : flottante numéro anomalies représentant score sur vers le haut et vers le bas niveau de modifier

###<a name="parameters"></a>Paramètres

Des informations plus détaillées sur ces paramètres d’entrée sont répertoriées dans le tableau ci-dessous :

|Paramètres d’entrée|Description|Paramètre par défaut|Type de|Plage valide|Plage suggérée|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|Intervalle en secondes pour l’agrégation d’agrégation d’entrée série temporelle|0 (aucune agrégation n’est effectuée)|nombre entier|0 : ignorer agrégation, > 0 dans le cas contraire|5 minutes à 1 jour, dépendant de la série chronologique
|preprocess.aggregationFunc|Fonction utilisée pour agréger des données dans le AggregationInterval spécifié|on entend par|énumérée|moyenne, somme, longueur|N/A|
|preprocess.replaceMissing|Valeurs utilisées pour imputer les données manquantes|lkv (dernière connue de valeur)|énumérée|zéro, lkv, moyenne|N/A|
|detectors.historyWindow|Historique (en nombre de points de données) utilisé pour le calcul du score des anomalies|500|nombre entier|10-2000|Dépendant de la série chronologique|
|upleveldetector.Sensitivity|La sensibilité d’un niveau vers le haut modifier détecteur. |3,25|Double|Aucun|3,25-5(Lesser values mean more sensitive)|
|bileveldetector.Sensitivity|Sensibilité pour détecteur de modification du niveau de bidirectionnel. |3,25|Double|Aucun|3,25-5(Lesser values mean more sensitive)|
|trenddetector.Sensitivity|Sensibilité d’un détecteur de tendance positive. |3,25|Double|Aucun|3,25-5(Lesser values mean more sensitive)|
|tspikedetector.Sensitivity |Sensibilité d’un détecteur de TSpike|3|nombre entier|1-10|3-5(Lesser values mean more sensitive)|
|zspikedetector.Sensitivity |Sensibilité d’un détecteur de ZSpike|3|nombre entier|1-10 |3-5(Lesser values mean more sensitive)|
|seasonality.Enable|Si analyse du caractère saisonnier doit être effectuée|valeur True|valeur booléenne|True, false|Dépendant de la série chronologique|
|seasonality.numSeasonality |Nombre maximal de cycles périodiques à détecter|1|nombre entier|1, 2|1-2|
|seasonality.Transform |Si saisonniers (et) des composants de tendance doivent être supprimés avant d’appliquer la détection des anomalies|deseason|énumérée|Aucun, deseason, deseasontrend|N/A|
|postprocess.tailRows |Nombre de points de données plus récentes soit conservé dans les résultats|0|nombre entier|0 (conserver tous les points de données), ou de spécifier le nombre de points à conserver dans les résultats|N/A|

###<a name="output"></a>Sortie
L’API s’exécute tous les détecteurs sur vos données de série heure et retourne les scores des anomalies et des indicateurs binaires Pique-notes pour chaque point dans le temps. Le tableau ci-dessous répertorie les sorties de l’API. 

|Sorties|Description|
|---|---|
|Heure|Les horodatages à partir des données brutes ou des données (et/ou) d’agrégées imputées si agrégation (et/ou) manquant imputation des données est appliquée.|
|OriginalData|Si les valeurs à partir des données brutes ou des données (et/ou) d’agrégées imputées agrégation (et/ou) manquant imputation des données est appliquée.|
|ProcessedData|Une des opérations suivantes : <ul><li>Saisonnières des séries temporelles si le caractère saisonnier importante a été détectée et deseason option est sélectionnée ;</li><li>selon la saison ajusté et redressé série temporelle si le caractère saisonnier importante a été détectée et deseasontrend option est sélectionnée</li><li>Sinon, c’est le même que OriginalData</li>|
|TSpike|Indicateur binaire pour indiquer si un pic est détecté par le détecteur de TSpike|
|ZSpike|Indicateur binaire pour indiquer si un pic est détecté par le détecteur de ZSpike|
|Pscore|Nombre flottant représentant le score des anomalies sur vers le haut niveau modifier|
|Palert|1/0 indiquant un niveau vers le haut modifier des anomalies en fonction de la sensibilité d’entrée|
|RPScore|Une anomalie représentant nombre flottante score sur la modification du niveau de bidirectionnel|
|RPAlert|1/0 indiquant le niveau de bidirectionnelle est modifier des anomalies en fonction de la sensibilité d’entrée|
|TScore|Une anomalie représentant nombre flottante score sur la tendance positive|
|TAlert|1/0 indiquant il est une anomalie de tendance positive en fonction de la sensibilité d’entrée|


Cette sortie peut être analysée à l’aide d’un [analyseur simple](https://adresultparser.codeplex.com/) - elle a des exemples de code qui montre comment se connecter à l’API et d’analyser la sortie. Les anomalies constatées peuvent être affichées dans un tableau de bord ou transmis à des experts humaines aux actions correctives ou intégrés dans les systèmes de réservation.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 
