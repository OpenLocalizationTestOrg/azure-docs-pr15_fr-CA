<properties
    pageTitle="Mettre à l’échelle votre travail Analytique de flux de données avec fonctions de formation de Machine Azure | Microsoft Azure"
    description="Découvrez comment faire évoluer correctement les tâches de flux Analytique (partitionnement, quantité SU, etc.) lors de l’utilisation de fonctions d’apprentissage automatique de Azure."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Mettre à l’échelle votre travail Analytique de flux avec des fonctions d’apprentissage automatique de Azure

Il est souvent très facile de définir une tâche de flux de données Analytique et exécuter des exemples de données par son intermédiaire. Que dois-je faire lorsque nous avons besoin exécuter la même tâche avec le volume de données plus élevé ? Elle nécessite de comprendre comment configurer la tâche de flux de données Analytique afin qu’il mettra à l’échelle. Dans ce document, nous nous concentrerons sur les aspects particuliers de l’évolution des travaux de flux Analytique avec fonctions d’apprentissage automatique. Pour plus d’informations sur la façon de mettre à l’échelle des tâches de flux de données Analytique en général, voir l’article [les tâches de mise à l’échelle](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Ce qui est une fonction d’apprentissage automatique de Azure dans flux Analytique ?

Une fonction d’apprentissage automatique dans le flux de données Analytique peut servir comme un appel de fonction normale dans le langage de requête Analytique de flux de données. Toutefois, derrière la scène, les appels de fonction sont en fait des demandes Azure Machine formation Web Service. Machine Learning web services prend en charge le « traitement par lot » plusieurs lignes, appelée mini lot, dans l’appel même API de service web, afin d’améliorer le débit global. Veuillez consulter les articles suivants pour plus de détails ; [Les fonctions de formation de Machine Azure dans le flux de données Analytique](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) et [Azure Machine Learning Web Services](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurer une tâche de flux de données Analytique avec fonctions d’apprentissage automatique

Lorsque vous configurez une fonction d’apprentissage automatique pour la tâche de flux de données Analytique, il y a deux paramètres à prendre en compte, la taille de lot de l’apprentissage automatique des appels de fonction et les unités de transmission en continu (SUs) mis en service pour la tâche de flux de données Analytique. Pour déterminer les valeurs appropriées pour ces, tout d’abord une décision doit être prise entre latence et le débit, c'est-à-dire que la latence de la tâche de flux de données Analytique et le débit de chaque SU. SUs peuvent toujours être ajoutés à un projet pour augmenter le débit d’une requête de flux Analytique bien partitionnée, bien que SUs supplémentaires augmente le coût de l’exécution de la tâche.

Par conséquent, il est important de déterminer la *tolérance* de la latence dans l’exécution d’une tâche de flux de données Analytique. Latence d’exécuter des demandes de service de formation de Machine Azure naturellement augmente avec la taille de lot, qui sera composé de la latence de la tâche de flux de données Analytique. D’autre part, l’augmentation de la taille de lot permet de la tâche de flux Analytique traiter les *plus d’événements avec le *même nombre * des demandes de service web apprentissage automatique. L’augmentation de la latence de service web apprentissage automatique est souvent inférieur linéaire à l’augmentation de la taille de lot pourquoi il est important de tenir compte de la taille de lot la plus économique pour un service web de formation de Machine dans une situation donnée. La taille de lot par défaut pour les demandes de service web est de 1000 et peut être modifiée en utilisant l' [API REST d’Analytique flux](https://msdn.microsoft.com/library/mt653706.aspx "Flux Analytique reste API") ou le [client de PowerShell pour Analytique de flux](stream-analytics-monitor-and-manage-jobs-use-powershell.md "client PowerShell pour l’Analytique des flux de données").

Une fois qu’une taille de lot a été déterminée, la quantité d’unités de transmission en continu (SUs) peut être déterminée en fonction du nombre d’événements que la fonction doit traiter par seconde. Pour plus d’informations sur les unités de transmission en continu, consultez l’article [échelle Analytique de flux de travaux](stream-analytics-scale-jobs.md#configuring-streaming-units).

En général, il y a 20 connexions simultanées au service web apprentissage automatique pour chaque 6 SUs, à ceci près que les travaux SU 1 et 3 tâches SU obtenez 20 connexions simultanées également.  Par exemple, si le taux d’entrée des données est de 200 000 événements par seconde et de la taille du lot est laissée à la valeur par défaut de 1000 la latence de service web qui en résulte avec le traitement par lots de 1000 événements mini est de 200 ms. Cela signifie que chaque connexion peut envoyer des demandes de 5 pour le service web de formation de l’ordinateur en une seconde. Avec 20 connexions, la tâche de flux de données Analytique peut traiter les 20 000 événements de 200 ms et par conséquent de 100 000 en une seconde. Pour traiter les 200 000 événements par seconde, la tâche de flux de données Analytique doit donc 40 connexions simultanées, qui sort de 12 SUs. Le schéma ci-dessous illustre les demandes à partir de la tâche de flux de données Analytique pour le point de terminaison du service web apprentissage automatique – chaque 6 SUs a 20 connexions simultanées au service web d’apprentissage automatique à max.

![Analytique de flux d’échelle avec un exemple de travail de Machine Learning fonctions 2] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Analytique de flux d’échelle avec un exemple de travail de Machine Learning fonctions 2")

En général, ***B*** pour la taille de lot, ***L*** pour le web service latence en fonction de la taille de lot B en millisecondes, le débit d’un travail de flux Analytique avec ***N*** SUs est :

![Mettre à l’échelle Analytique de flux avec apprentissage formule de fonctions de l’ordinateur] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Mettre à l’échelle Analytique de flux avec apprentissage formule de fonctions de l’ordinateur")

Une considération supplémentaire peut être le « maximum d’appels concurrents » côté service web apprentissage automatique, il est recommandé de définir la valeur maximale (200 actuellement).

Pour plus d’informations sur ce sujet de paramètre, consultez l' [article de mise à l’échelle pour les Services Web de formation Machine](../machine-learning/machine-learning-scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exemple : analyse de Sentiment

L’exemple suivant inclut un projet de flux Analytique avec l’analyse sentiment fonction d’apprentissage automatique, comme décrit dans le [didacticiel d’intégration de flux Analytique Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

La requête est une requête simple entièrement partitionnée, suivie par la fonction **sentiment** , comme indiqué ci-dessous :

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Envisagez le scénario suivant ; avec un débit de 10 000 tweet par seconde, une tâche de flux de données Analytique doit être créée pour effectuer une analyse de sentiment de la tweet (événements). 1 SU, cette tâche de flux de données Analytique serait en mesure de gérer le trafic ? Le travail à l’aide de la taille de lot par défaut de 1000 doit être en mesure de faire face à l’entrée. Plus la fonction d’apprentissage automatique ajoutée doit générer n’excède pas une seconde de latence, ce qui est le grand temps de latence par défaut de l’analyse de sentiment service web de formation de Machine (avec une taille de lot par défaut de 1000). Latence de bout à bout ou **Général** du travail Analytique de flux de données serait en général quelques secondes. Tenir une analyse plus détaillée ce traitement Analytique de flux de données, *en particulier* l’apprentissage des appels de fonction de l’ordinateur. La taille de lot en tant que 1000, un débit de 10 000 événements prendra environ 10 demandes au service web. Même avec 1 SU, il y a suffisamment de connexions simultanées pour s’adapter à ce trafic d’entrée.

Mais que se passe-t-il si le taux de l’événement d’entrée augmente de 100 x et la tâche de flux de données Analytique doit maintenant traiter de 1 000 000 tweet par seconde ? Il existe deux options :

1.  Augmenter la taille du lot, ou
2.  Partition du flux d’entrée pour traiter les événements en parallèle

La première option, augmente la **latence** du travail.

Avec la seconde option, plus SUs doit être mis en service et par conséquent générer plus apprentissage automatique web service de demandes simultanées. Cela signifie que le **coût** du projet augmente.


Supposons que la latence de l’analyse du service web de formation de Machine sentiment est de 200 ms pour les lots d’événements 1000 ou au-dessous, 250ms pour les lots de 5 000 événements, de 300 MS pour les lots d’événements-10 000 ou 500 ms pour les lots de 25 000 événements.

1. À l’aide de la première option, (**pas** mise en service de SUs plus), la taille de lot peut être augmentée à **25 000**. Cela permet à son tour le la tâche à traiter de 1 000 000 événements avec 20 connexions simultanées au service web apprentissage automatique (avec une latence de 500 ms par appel). Par conséquent, la latence du travail Analytique de flux de données en raison de demandes fonction sentiment contre les demandes de service web apprentissage automatique passerait de **200 ms** à **500 ms**. Toutefois, notez que le traitement par lots de la taille **ne peut pas** être considérablement accrue que les services web de formation de Machine requiert la taille de la charge utile d’une demande de 4 Mo ou plus petit délai de demande de service web après 100 secondes de l’opération.
2. À l’aide de la seconde option, la taille du lot est laissée à 1 000, avec une latence de 200 ms web service, chaque 20 connexions simultanées au service web qui serait en mesure de traiter les 1000 événements *20* 5 = 100 000 par seconde. Pour traiter les 1 000 000 événements par seconde, la tâche aurait donc 60 SUs. Par rapport à la première option, tâche de flux de données Analytique rendrait plus requêtes de lots de service web, générant tour à une augmentation des coûts.

Voici une table pour le débit de la tâche de flux de données Analytique pour SUs différents et des tailles de lot (en nombre d’événements par seconde).

| taille de lot (latence ML)  | 500 (200 MS) | 1 000 (200 ms) | 5 000 (250ms) | 10 000 (de 300 MS) | 25 000 (500 MS) |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| **1 SU** | 2 500 | 5 000 | 20 000 | 30 000 | 50 000 |
| **3 SUs** | 2 500 | 5 000 | 20 000 | 30 000 | 50 000 |
| **6 SUs** | 2 500 | 5 000 | 20 000 | 30 000 | 50 000 |
| **12 SUs** | 5 000 | 10 000 | 40 000 | 60 000 | 100 000 |
| **18 SUs** | 7 500 | 15 000 | 60 000 | 90 000 | 150 000 |
| **24 SUs** | 10 000 | 20 000 | 80 000 | 120 000 | 200 000 |
| **…** | … | … | … | … | … |
| **60 SUs** | 25 000 | 50 000 | 200 000 | 300 000 | 500 000 |

À présent, vous disposez déjà d’une bonne compréhension du fonctionnement des fonctions d’apprentissage automatique dans le flux de données Analytique. Vous probablement également comprenez que des données de tâches de flux Analytique « collecte » à partir de sources de données et chaque « collecte » renvoie un lot d’événements pour le travail d’Analytique de flux de données à traiter. Comment cet impact de modèle pull l’apprentissage Machine web des demandes de service ?

En règle générale, la taille de lot que nous avons définies pour des fonctions d’apprentissage automatique ne sera exactement divisible par le nombre d’événements retourné par chaque tâche de flux Analytique « collecte ». Dans ce cas que le service web d’apprentissage automatique sera appelé avec les lots « partielles ». Cela n'entraîne pas de surcharge de latence projet supplémentaires dans les événements de fusion d’extraction pour extraction.

## <a name="new-function-related-monitoring-metrics"></a>Nouvelles mesures de surveillance liées à la fonction

Dans la zone d’écran d’une tâche de flux de données Analytique, trois mesures liés à la fonction supplémentaires ont été ajoutés. Ils sont des demandes de fonctions, les événements de la fonction et les demandes de fonction a échoué, comme indiqué dans l’illustration ci-dessous.

![Mettre à l’échelle Analytique de flux avec apprentissage des métriques de fonctions de l’ordinateur] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Mettre à l’échelle Analytique de flux avec apprentissage des métriques de fonctions de l’ordinateur")

Les sont définis comme suit :

**Demandes de fonctions**: le nombre de demandes de fonction.

**Événements de la fonction**: le nombre d’événements contenus dans la demande de la fonction.

**Demandes de fonction a échoué**: le nombre de demandes de fonction qui a échoué.

## <a name="key-takeaways"></a>Points clés  

Pour résumer les principaux points, afin de mettre à l’échelle un travail Analytique de flux avec des fonctions d’apprentissage automatique, les éléments suivants doivent être considérés :

1.  Le taux de l’événement d’entrée
2.  La latence tolérée pour la tâche de flux de données Analytique en cours d’exécution (et donc la taille de lot des demandes de service web apprentissage automatique)
3.  Le SUs Analytique flux mis en service et le nombre de demandes de service web apprentissage automatique (supplémentaires liés à la fonction de coûts)

Par exemple, une requête Analytique de flux entièrement partitionnée a été utilisée. Si une requête plus complexe est nécessaire le [forum d’Analytique de flux Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) est une ressource précieuse pour obtenir une aide supplémentaire de l’équipe d’Analytique de flux de données.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les flux de données Analytique, voir :

- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
