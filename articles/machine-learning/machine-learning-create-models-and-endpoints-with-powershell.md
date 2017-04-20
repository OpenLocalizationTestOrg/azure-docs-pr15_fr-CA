<properties
pageTitle="Créer plusieurs modèles à partir d’une expérience | Microsoft Azure"
description="Utilisation de PowerShell pour créer plusieurs modèles d’apprentissage automatique et web points de terminaison de service avec le même algorithme mais la formation de différents groupes de données."
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
ms.date="10/03/2016"
ms.author="garye;haining"/>

# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Créer les points de terminaison de service web et modèles d’apprentissage automatique de nombreux à partir d’une expérience à l’aide de PowerShell

Voici un problème d’apprentissage machine courant : vous souhaitez créer plusieurs modèles ayant le même flux de travail de formation et utilisent le même algorithme, mais différente de formation de groupes de données en tant qu’entrée. Cet article explique comment effectuer cette opération à grande échelle dans un Studio de formation de Machine Azure en utilisant simplement une expérience unique.

Par exemple, supposons que vous possédez une entreprise de franchise de location de vélo global. Vous souhaitez créer un modèle de régression afin de prévoir la demande de location basée sur des données historiques. Vous avez 1 000 emplacements de location dans le monde entier et que vous avez collecté un dataset pour chaque emplacement qui inclut des fonctionnalités importantes telles que les date, heure, météo et le trafic qui sont spécifiques à chaque emplacement.

Vous pourriez former votre modèle une fois en utilisant une version fusionnée de tous les jeux de données sur tous les sites. Mais étant donné que chacun de vos emplacements a un environnement unique, une meilleure approche serait pour exercer votre modèle de régression séparément à l’aide du groupe de données pour chaque emplacement. De cette façon, chaque modèle formé pourrait prendre en compte la banque de différentes tailles, volume, géographie, population, environnement de trafic de vélo conviviale, *etc.*.

Qui peut être la meilleure approche, mais que vous ne souhaitez pas créer 1 000 expériences de formation dans l’apprentissage automatique de Azure avec chacun d’eux représentant un emplacement unique. En plus d’être une tâche écrasante, il est également semble assez inefficace dans la mesure où chaque expérience aurait les mêmes composants, à l’exception du groupe de données de formation.

Heureusement, nous pouvons effectuer cette opération à l’aide de la [formation de Machine Azure recyclage API](machine-learning-retrain-models-programmatically.md) et d’automatisation de la tâche avec [Azure Machine Learning PowerShell](machine-learning-powershell-module.md).

> [AZURE.NOTE] Pour accélérer l’exécution notre exemple, nous permet de réduire le nombre d’emplacements de 1 000 à 10. Mais les mêmes principes et procédures s’appliquent aux emplacements 1 000. La seule différence est que si vous souhaitez former de 1 000 groupes de données vous souhaiterez probablement considérer les scripts PowerShell suivants en cours d’exécution en parallèle. Procédure à suivre est au-delà de la portée de cet article, mais vous pouvez trouver des exemples de PowerShell multi-threading sur Internet.  

## <a name="set-up-the-training-experiment"></a>Configurer l’expérience de formation

Nous allons utiliser un exemple [essayer de formation](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) que nous avons déjà créé dans la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com). Ouvrez cette expérience dans votre espace de travail de [Studio de formation de Machine Azure](https://studio.azureml.net) .

>[AZURE.NOTE] Pour suivre cet exemple, vous souhaiterez peut-être utiliser un espace de travail standard plutôt que dans un espace de travail disponible. Nous allons créer un point de terminaison pour chaque client - pour un total de 10 points de terminaison - et qui nécessitent un espace de travail standard depuis un espace de travail disponible est limitée à 3 points de terminaison. Si vous disposez d’un espace de travail libre, il suffit de modifier les scripts pour autoriser uniquement 3 emplacements ci-dessous.

L’expérience utilise un module **d’Importation de données** pour importer le groupe de données de formation *customer001.csv* à partir d’un compte de stockage Azure. Supposons que nous avons collecté de formation de groupes de données à partir de tous les emplacements de location de vélo et stockés dans le même emplacement de stockage des blob avec des noms de fichier comprise entre *rentalloc001.csv* et *rentalloc10.csv*.

![image](./media/machine-learning-create-models-and-endpoints-with-powershell/reader-module.png)

Notez qu’un module de **Sortie du Service Web** a été ajouté dans le module du **Modèle de Train** .
Lorsque cette expérience est déployée comme un service web, le point de terminaison associé à que sortie renverra le modèle formé dans le format d’un fichier .ilearner.

Notez également que nous avons définies un paramètre du service web pour l’URL utilisée par le module **d’Importation de données** . Ceci nous permet d’utiliser le paramètre pour spécifier des groupes de données de formation individuelle pour former le modèle pour chaque emplacement.
Il existe autres façons que nous pourrions avoir fait cela, par exemple à l’aide d’une requête SQL avec un paramètre du service web pour obtenir des données à partir d’une base de données SQL Azure, ou simplement à l’aide d’un module **D’entrée du Service Web** pour passer dans un groupe de données au service web.

![image](./media/machine-learning-create-models-and-endpoints-with-powershell/web-service-output.png)

Maintenant, exécutons cette expérience de formation à l’aide de la valeur de par défaut *rental001.csv* comme le groupe de données de formation. Si vous affichez la sortie du module **Evaluate** (cliquez sur la sortie et sélectionnez **visualiser**), vous pouvez voir nous obtenons une bonne performance de *AUC* = 0,91. À ce stade, nous sommes prêts à déployer un service web en dehors de cette expérience de formation.

## <a name="deploy-the-training-and-scoring-web-services"></a>Déployer la formation et l’évaluation des services web

Pour déployer le service web de formation, cliquez sur le bouton **Définir les Service Web** au-dessous de la toile de l’expérience et sélectionnez **Déployer le Service Web**. Appeler ce service web « « vélo location Training ».

Nous devons maintenant déployer le service web de score.
Pour ce faire, nous pouvons cliquez sur **La valeur d’un Service Web** au-dessous de la toile et sélectionnez **Prédictive Web Service**. Cela crée une expérience d’évaluation.
Nous devons faire quelques réglages mineurs pour qu’il fonctionne comme un service web, comme la suppression de la colonne étiquette « cnt » à partir des données d’entrée et limitant la sortie à l’id d’instance et les valeurs prédites valeur.

Pour vous faire gagner ce travail, vous pouvez simplement ouvrir l' [expérience prédictive](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) dans la galerie qui est déjà été préparée.

Pour déployer le service web, exécutez l’expérience prédictive, puis sur le bouton de **Déployer le Service Web** au-dessous de la toile. Nommez le service web score « Vélo location score » ».

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Créer des points de terminaison de service web identiques 10 avec PowerShell

Ce service web est fourni avec un point de terminaison par défaut. Mais nous n'intéresse pas comme le point de terminaison par défaut dans la mesure où il ne peut pas être mis à jour. Ce que nous devons faire est de créer les points de terminaison de supplémentaires 10, une pour chaque emplacement. Pour cela, nous allons avec PowerShell.

Tout d’abord, nous avons configuré notre environnement PowerShell :

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Ensuite, exécutez la commande PowerShell suivante :

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Maintenant, nous avons créé 10 points de terminaison et ils contiennent tous le même modèle formé formé sur *customer001.csv*. Vous pouvez les afficher dans le portail de gestion Azure.

![image](./media/machine-learning-create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Les points de terminaison pour utiliser des datasets de formation distinct à l’aide de PowerShell de mise à jour

L’étape suivante consiste à mettre à jour les points de terminaison avec des modèles de façon unique formés sur les données individuelles de chaque client. Mais tout d’abord, nous devons produire ces modèles à partir du service web de **Formation de location de vélo** . Revenons au service web **Formation de location de vélo** . Nous devons appeler son point de terminaison BES 10 fois avec 10 groupes de formation différentes données afin de produire différents modèles de 10. Nous allons utiliser l’applet de commande PowerShell **InovkeAmlWebServiceBESEndpoint** pour cela.

Vous devez également fournir des informations d’identification de votre compte de stockage blob dans `$configContent`, à savoir les champs `AccountName`, `AccountKey` et `RelativeLocation`. Le `AccountName` peut être une de vos comptes, comme dans le **Portail de gestion Azure classique** (onglet*stockage* ). Une fois que vous cliquez sur un compte de stockage, sa `AccountKey` pouvez trouver en appuyant sur le bouton **Gérer les touches d’accès rapide** en bas et de copie de la *Clé d’accès primaire*. Le `RelativeLocation` est le chemin d’accès par rapport à l’espace de stockage où un nouveau modèle sera stocké. Par exemple, le chemin d’accès `hai/retrain/bike_rental/` dans le script ci-dessous pointe vers un conteneur nommé `hai`, et `/retrain/bike_rental/` sont des sous-dossiers. Actuellement, vous ne pouvez pas créer de sous-dossiers via le portail d’interface utilisateur, mais il y a [Plusieurs explorateurs de stockage Azure](../storage/storage-explorers.md) qui vous permettent de le faire. Il est recommandé de créer un nouveau conteneur dans votre espace de stockage pour stocker les nouveaux modèles de formation (fichiers .ilearner) comme suit : à partir de votre page de stockage, cliquez sur le bouton **Ajouter** au bas et nommez-le `retrain`. En résumé, les modifications nécessaires pour le script ci-dessous se rapportent à `AccountName`, `AccountKey` et `RelativeLocation` ( :`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

>[AZURE.NOTE] Le point de terminaison BES est le seul mode de prise en charge pour cette opération. RR ne peut pas être utilisées pour produire des modèles bien formés.

Comme vous pouvez le constater ci-dessus, au lieu de construire 10 BES travail configuration json des fichiers différents, nous dynamiquement créer à la place de la chaîne de configuration et aliments pour animaux au paramètre *jobConfigString* de l’applet de commande **InvokeAmlWebServceBESEndpoint** , dans la mesure où il n’est vraiment aucun nécessaire de conserver une copie sur le disque.

Si tout se passe bien, après un certain temps, vous devez voir les fichiers .ilearner 10, à partir de *model001.ilearner* à *model010.ilearner*, dans votre compte de stockage Azure. Nous sommes maintenant prêts à mettre à jour nos points de terminaison score web service 10 avec ces modèles à l’aide de l’applet de commande PowerShell **Correctif-AmlWebServiceEndpoint** . N’oubliez pas à nouveau que nous pouvons uniquement corriger les points de terminaison par défaut que nous créés par programme.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Il doit s’exécuter assez rapidement. Une fois l’exécution terminée, nous allons avez créé 10 prédictive service points de terminaison web, contenant chacune un modèle formé formé de manière unique sur le groupe de données spécifique à un emplacement de la location, à partir d’une expérience de formation unique. Pour ce faire, vous pouvez essayer de l’appel de ces points de terminaison à l’aide de l’applet de commande **InvokeAmlWebServiceRRSEndpoint** , leur donnant les mêmes données d’entrée, et vous devriez voir les résultats des prévisions différentes dans la mesure où les modèles sont formés avec formation de différents jeux.

## <a name="full-powershell-script"></a>Script complet de PowerShell

Voici la liste de l’intégralité du code source :

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
