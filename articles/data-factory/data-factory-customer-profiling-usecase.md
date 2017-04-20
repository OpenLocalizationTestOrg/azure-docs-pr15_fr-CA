<properties 
    pageTitle="Utiliser la casse - création de profils utilisateur" 
    description="Découvrez comment Azure Data Factory est utilisé pour créer un workflow piloté par les données (pipeline) pour définir le profil des clients de jeu." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="shlo"/>

# <a name="use-case---customer-profiling"></a>Utiliser la casse - création de profils utilisateur

Azure Data Factory est un des nombreux services utilisés pour implémenter l’Intelligence de Cortana Suite d’accélérateurs de solutions.  Pour plus d’informations sur l’analyse décisionnelle de Cortana, visitez le site [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). Dans ce document, nous décrire un cas d’utilisation simple pour les aider à comprendre comment Azure Data Factory permet de résoudre les problèmes courants analytique.

Il vous suffit d’accéder à et essayer ce cas d’usage simples est un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/).  Vous pouvez déployer un exemple qui implémente ce cas d’usage en suivant les étapes décrites dans l’article [d’échantillons](data-factory-samples.md) .

## <a name="scenario"></a>Scénario

Contoso est une société de jeux qui crée des jeux pour plusieurs plates-formes : ordinateurs personnels (PC), appareils de poche et consoles de jeu. Pour exécuter ces jeux, joueurs, gros volume de données de journal est produit qui assure le suivi des modèles d’utilisation, style de jeu, les préférences de l’utilisateur.  Lorsqu’il est combiné avec des données démographiques, régionales et les données sur les produits, Contoso peut effectuer analytique pour le guider sur la façon d’améliorer l’expérience des joueurs et cible les mises à niveau et de la partie des achats. 

Objectif de Contoso est d’identifier à distance-donneur d’ordre/des opportunités de vente en fonction de l’historique de jeu de ses lecteurs et ajouter des fonctionnalités intéressantes à dynamiser sa croissance et offrir une meilleure expérience à ses clients. Pour ce cas d’usage, nous utilisons une société de jeux par exemple d’une entreprise. La société souhaite optimiser ses jeux basés sur le comportement des joueurs. Ces principes s’appliquent à toute entreprise qui souhaite exercer ses clients autour de ses biens et de services et d’améliorer l’expérience de leurs clients.

## <a name="challenges"></a>Défis


## <a name="solution-overview"></a>Présentation de la solution

Ce cas d’usage simples permet par exemple de comment vous pouvez utiliser Azure Data Factory à réceptionner, préparer, transformer, analyser et publier des données.

![Workflow de bout en bout](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Cette Figure décrit comment les pipelines de données apparaissent dans le portail Azure après que qu’ils ont été déployés.

1.  La **PartitionGameLogsPipeline** lit les événements de jeu bruts depuis le stockage blob et crée des partitions en fonction de l’année, mois et jour.
2.  **EnrichGameLogsPipeline** joint des événements de jeu partitionnés avec les données de référence de code géographique et enrichir les données en mappant les adresses IP pour les géo-emplacements correspondants.
3.  Le pipeline **AnalyzeMarketingCampaignPipeline** utilise les données enrichies et il traite les données de publicité pour créer la sortie finale qui contient l’efficacité de la campagne marketing.

Dans cet exemple, Data Factory est utilisé pour organiser des activités qui copie les données d’entrée, de transformer et de traiter les données et les données finales à une base de données de SQL Azure de sortie.  Vous pouvez également visualiser le réseau des pipelines de données, les gérer et contrôler leur état à partir de l’interface utilisateur.

## <a name="benefits"></a>Avantages

En optimisant leurs analytique de profil utilisateur et alignez-le sur les objectifs de l’entreprise, société de jeu est en mesure de rapidement collecter des modèles d’utilisation et analyser l’efficacité de ses campagnes marketing.




