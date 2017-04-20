<properties 
    pageTitle="Charger des données dans des environnements de stockage pour l’analytique | Microsoft Azure" 
    description="Déplacement des données vers et depuis le stockage Blob Azure" 
    services="machine-learning,storage" 
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
    ms.date="09/19/2016" 
    ms.author="bradsev" />

# <a name="load-data-into-storage-environments-for-analytics"></a>Charger des données dans des environnements de stockage pour l’analytique

Le processus de Science de données équipe requiert que données ingérées ou chargées dans une variété d’environnements de stockage différents pour être traités ou analysé de la manière la plus appropriée à chaque étape du processus. Destinations des données utilisées pour le traitement incluent le stockage Blob Azure, bases de données SQL Azure, SQL Server sur Azure VM, HDInsight (Hadoop) et formation de Machine Azure. 

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Ce **menu** liens vers des rubriques qui décrivent comment faire pour ajouter des données dans ces environnements cible où les données sont stockées et traitées.

Techniques et les besoins de l’entreprise, ainsi que l’emplacement initial, mettre en forme et la taille de vos données détermine l’environnement cible dans lequel les données doivent être ingérée pour atteindre les objectifs de votre analyse. Il n’est pas rare pour un scénario d’exiger des données doit être déplacé entre plusieurs environnements pour atteindre les différentes tâches nécessaires à la construction d’un modèle de prévision. Cette séquence de tâches peut inclure, par exemple, exploration de données, traitement préalable, le nettoyage, d’échantillonnage et formation de modèle.
