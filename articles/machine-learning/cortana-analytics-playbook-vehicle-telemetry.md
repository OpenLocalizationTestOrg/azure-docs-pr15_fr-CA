<properties 
    pageTitle="Scénario de solution de véhicule télémétrie analytique | Microsoft Azure" 
    description="Les capacités d’Intelligence de Cortana permet d’obtenir des analyses prédictives et en temps réel sur la santé du véhicule et à la conduite habitudes." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Scénario de solution de véhicule télémétrie analytique

Cette **menu** contient des liens vers les chapitres de ce manuel. 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Vue d’ensemble
Super ordinateurs ont retirés de l’atelier et sont désormais stationnés dans notre garage ! Ces automobiles pointe contiennent une multitude de capteurs, en leur donnant la possibilité de suivre et de surveiller des millions d’événements par seconde. Nous espérons que d’ici à 2020, la plupart de ces véhicules sera ont été connectée à internet. Imaginez un taraudage dans cette profusion de données pour fournir les meilleures dans l’expérience de conduite, de fiabilité et de sécurité de classe ! Microsoft a fait de ce rêve une réalité avec Intelligence de Cortana.

Intelligence de Cortana de Microsoft est une données volumineuses entièrement gérées et analytique avancé qui vous permet de transformer vos données en action intelligente. Nous souhaitons vous présenter le modèle de Solution du véhicule télémétrie Analytique Cortana Intelligence. Cette solution montre comment concessionnaires de voitures, les constructeurs automobiles et les compagnies d’assurance peuvent utiliser les capacités d’Intelligence de Cortana pour obtenir en temps réel et des analyses prédictives sur la santé du véhicule et à la conduite habituelles. 

La solution est implémentée sous la forme d’un [modèle d’architecture de lambda](https://en.wikipedia.org/wiki/Lambda_architecture) montrant le plein potentiel de la plate-forme d’analyse décisionnelle de Cortana pour en temps réel et de traitement par lots. La solution : 

- fournit un simulateur télématiques pour véhicules
- exploite les concentrateurs d’événements pour traiter des millions d’événements de télémesure de véhicule simulé dans Azure 
- utilise Analytique de flux pour obtenir un aperçu en temps réel sur la santé du véhicule
-  conserve les données dans le stockage à long terme pour l’analytique de lot plus riche. 
- profite de l’apprentissage de l’ordinateur pour détecter une anomalie dans en temps réel et traitement afin d’obtenir des analyses prédictives par lots.
- exploite HDInsight pour transformer des données à grande échelle et Factory de données pour gérer l’orchestration, planification, gestion des ressources et suivi du pipeline de traitement par lots 
- donne à cette solution un tableau de bord riche pour les données en temps réel et des visualisations prédictive analytique à l’aide de BI de puissance

## <a name="architecture"></a>Architecture

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*Figure 1 : l’Architecture de la Solution télémétrie Analytique de véhicule*

Cette solution inclut les suivants **composants Cortana Intelligence** et présente leur intégration de bout en bout


- **Événement concentrateurs** pour traiter des millions d’événements de télémesure de véhicule dans Azure.
- **Analytique de flux** pour obtenir un aperçu en temps réel sur la santé du véhicule et l’enregistre ces données dans le stockage à long terme pour l’analytique de lot plus riche.
- L' **Apprentissage automatique** pour détecter une anomalie en temps réel et de traitement par lots pour obtenir les analyses prédictives.
- **HDInsight** est utilisé pour transformer des données à grande échelle
- **Data Factory** gère d’orchestration, de gestion des ressources de planification et de surveillance du pipeline de traitement par lots.
- **Alimentation BI** donne à cette solution un tableau de bord riche pour les données en temps réel et les visualisations analytique prédictive.

Cette solution accède à deux différentes **sources de données**: 

- **Simuler des signaux de véhicule et des tests de diagnostic**: un simulateur de télématique de véhicule émet des informations de diagnostic et de signaux qui correspondent à l’état du véhicule et le modèle de conduite à un moment donné dans le temps. 
- **Catalogue de véhicule**: un dataset de référence contenant un numéro d’immatriculation au mappage du modèle.
