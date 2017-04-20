<properties
    pageTitle="Module PowerShell pour l’apprentissage de l’ordinateur | Microsoft Azure"
    description="Le module PowerShell pour l’apprentissage de Machine Azure est disponible en mode Aperçu public. Utilisation de PowerShell pour créer et gérer des espaces de travail, des expériences, des services web et plus."
    keywords="Faites des essais, régression linéaire, les algorithmes d’apprentissage machine, didacticiel d’apprentissage machine, des techniques de modélisation prédictive, expérience de science de données"
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="garye;haining"/>

# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Module PowerShell pour l’apprentissage de Machine Microsoft Azure

Le module PowerShell pour l’apprentissage de Machine Azure est un outil puissant qui vous permet d’utiliser Windows PowerShell pour gérer les espaces de travail, expériences, des datasets, web serivces et bien plus encore.

Vous pouvez consulter la documentation et le module, ainsi que le code source complet, à [https://aka.ms/amlps](https://aka.ms/amlps). 

## <a name="what-is-the-machine-learning-powershell-module"></a>Quel est le module PowerShell de formation de Machine ?

Le module PowerShell de formation de Machine est un. Basé sur le NET module de la DLL qui vous permet de gérer complètement les espaces de travail Azure Machine Learning, expériences, des groupes de données, services web et points de terminaison de service web à partir de Windows PowerShell. Avec le module, vous pouvez télécharger le code source complet qui inclut une [couche API C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)de correctement séparés. Cela signifie que vous pouvez référencer cette DLL à partir de votre propre projet .NET et gérer Azure Machine formation par l’intermédiaire de code .NET. En outre, la DLL dépend de l’API reste sous-jacentes que vous pouvez exploiter directement à partir de votre client favori.

## <a name="what-can-i-do-with-the-powershell-module"></a>Que puis-je faire avec le module PowerShell ?

Voici quelques-unes des tâches que vous pouvez effectuer avec ce module PowerShell. Consultez la [documentation complète](https://aka.ms/amlps) de celles-ci et de davantage de fonctions.

- Mettre en service un nouvel espace de travail à l’aide d’un certificat de gestion ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Exporter et importer un fichier JSON qui représente un graphique d’expérience ([AmlExperimentGraph-exportation](https://github.com/hning86/azuremlps#export-amlexperimentgraph) et [Importation-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Exécuter une expérience ([Début-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- Créer un service web sur une expérience prédictive ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Créer un nouveau point de terminaison sur un service web publié ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- Appeler un RR et/ou BES service point de terminaison web ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) et [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Voici un exemple rapide de l’utilisation de PowerShell pour exécuter une expérience existante :

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Pour un cas d’usage plus détaillé, consultez cet article sur l’utilisation du module PowerShell pour automatiser une tâche très demandés : [créer des points de terminaison de service à partir d’une expérience à l’aide de PowerShell de nombreux modèles d’apprentissage automatique et web](machine-learning-create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Comment commencer ?

Pour commencer avec PowerShell d’apprentissage Machine, téléchargez le [package de mise à jour](https://github.com/hning86/azuremlps/releases) à partir de GitHub et suivez les [instructions d’installation](https://github.com/hning86/azuremlps/blob/master/README.md). Vous devez débloquer la DLL téléchargé/décompressé et l’importer dans votre environnement PowerShell. La plupart des applets de commande nécessite que vous fournissez l’ID de l’espace de travail, le jeton d’autorisation d’espace de travail et la région Azure figurant dans l’espace de travail. La façon la plus simple pour fournir ces est un fichier config.json par défaut, qui sont couvertes en détail dans les instructions d’installation. Bien sûr, vous pouvez également cloner l’arborescence git et modifier/compiler le code localement à l’aide de Visual Studio.

## <a name="next-steps"></a>Étapes suivantes

Le module PowerShell continuera d’être amélioré et développé au cours de cette période d’aperçu. Gardez un œil sur [Blog d’apprentissage Machine et Cortana](https://blogs.technet.microsoft.com/machinelearning/) pour plus d’informations et de news.
