<properties
   pageTitle="Comment faire pour utiliser des sources de données « données volumineuses » | Microsoft Azure"
   description="Article d’apprentissage des modèles pour l’utilisation du catalogue de données Azure avec les sources de données « données volumineuses », y compris le stockage Blob Azure, lac de données Azure et Hadoop HDFS la mise en surbrillance."
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
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Comment faire pour utiliser des sources de données dans le catalogue de données Azure

## <a name="introduction"></a>Introduction
**Catalogue de données de Microsoft Azure** est un service en nuage entièrement géré qui sert d’un système d’enregistrement et système de détection des sources de données d’entreprise. En d’autres termes, **Le catalogue de données Azure** est tout sur aidant les utilisateurs à découvrir, comprendre et utiliser les sources de données et aider les entreprises à tirer davantage de valeur à partir de leurs sources de données existantes, y compris les données volumineuses.

**Catalogue de données Azure** prend en charge l’inscription de Blog le stockage Azure BLOB et des répertoires ainsi que des fichiers de Hadoop HDFS et des répertoires. La nature semi-structurés de ces sources de données offre une grande souplesse, mais cela signifie également que les utilisateurs doivent envisager comment les sources de données sont organisées afin d’obtenir la valeur de leur enregistrement dans le **Catalogue de données Azure**.

## <a name="directories-as-logical-data-sets"></a>Répertoires en tant que jeux de données logiques

Un modèle commun pour organiser les sources de données consiste à traiter des répertoires sous forme de jeux de données logiques. Répertoires de niveau supérieur sont utilisées pour définir un ensemble de données, tandis que les sous-dossiers définissent des partitions, et les fichiers qu’ils contiennent stockent les données lui-même.

Un exemple de ce modèle peut être :

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

Dans cet exemple, vehicle_maintenance_events et location_tracking_events représentent des ensembles de données logiques. Chacun de ces dossiers contient des fichiers de données qui sont organisées par année et mois dans des sous-dossiers. Chacun de ces dossiers peut contenir des centaines ou des milliers de fichiers.

Dans ce modèle, enregistrement des fichiers individuels avec le **Catalogue de données Azure** probablement inutile. Au lieu de cela, enregistrez les répertoires qui représentent les jeux de données qui sera significatifs pour les utilisateurs de travailler avec les données.

## <a name="reference-data-files"></a>Fichiers de données de référence

Un modèle complémentaire est de stocker des ensembles de données de référence en tant que fichiers individuels. Ces ensembles de données peut être considérés comme le côté « small » de données volumineuses et sont souvent similaires aux dimensions d’un modèle de données analytiques. Référencer des fichiers de données contiennent des enregistrements qui sont utilisés pour fournir un contexte pour l’ensemble des fichiers de données stockées dans le magasin de données.

Un exemple de ce modèle peut être :

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Lorsqu’un chercheur analyste ou de données fonctionne avec les données contenues dans les grandes structures de répertoire, les données dans ces fichiers de référence peuvent être utilisées pour fournir des informations plus détaillées pour les entités auxquelles sont uniquement par le nom ou l’ID dans le plus grand ensemble de données.

Dans ce modèle, il sera judicieux d’enregistrer les fichiers de données de référence individuelle avec le **Catalogue de données Azure**. Chaque fichier représente un ensemble de données, et chacun d’eux peut être annoté et découvert individuellement.

## <a name="alternate-patterns"></a>Autres modèles

Les modèles décrits ci-dessus sont deux façons possibles, qu'une banque de données volumineuses peut-être être organisée, mais chaque implémentation sera différente. Quelle que soit la façon dont vos sources de données sont structurées, lors de l’enregistrement des sources de données volumineuses avec le **Catalogue de données Azure**, concentrez-vous sur l’enregistrement des fichiers et des répertoires qui représentent les jeux de données qui seront utiles à d’autres personnes au sein de votre organisation. Enregistrement de tous les fichiers et répertoires risque d’encombrer le catalogue, ce qui rend plus difficile pour les utilisateurs à trouver ce dont ils ont besoin.

## <a name="summary"></a>Résumé
L’inscription de sources de données avec **Azure données catalogue** les rend plus faciles à découvrir et à comprendre. En enregistrant et en annotant les gros fichiers de données et les répertoires qui représentent des ensembles de données logiques, vous pouvez aider les utilisateurs de trouver et d’utiliser les sources de données que dont ils ont besoin.
