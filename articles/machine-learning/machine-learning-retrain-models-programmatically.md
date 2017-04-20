<properties
    pageTitle="Former des modèles d’apprentissage automatique par programme | Microsoft Azure"
    description="Découvrez comment former un modèle et le service web pour utiliser le modèle nouvellement formé dans Azure Machine formation de mise à jour par programme."
    services="machine-learning"
    documentationCenter=""
    authors="raymondlaghaeian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="raymondl;garye;v-donglo"/>


# <a name="retrain-machine-learning-models-programmatically"></a>Former des modèles d’apprentissage automatique par programme  

Dans cette procédure pas à pas, vous allez apprendre par programme dispenser une Machine de Azure formation Service Web à l’aide de C# et le service d’exécution de traitement par lots d’apprentissage Machine.

Une fois que vous avez formés du modèle, les procédures suivantes montrent comment mettre à jour le modèle dans votre service web prédictive :

- Si vous avez déployé un service web de classique dans le portail de Services Web de formation Machine, consultez [dispenser un service de web classique](machine-learning-retrain-a-classic-web-service.md). 
- Si vous avez déployé un service web, reportez-vous à [dispenser un service web à l’aide des applets de commande de gestion d’apprentissage Machine](machine-learning-retrain-new-web-service-using-powershell.md).

Pour une vue d’ensemble du processus de reconversion, consultez [former un modèle d’apprentissage Machine](machine-learning-retrain-machine-learning-model.md).

Si vous souhaitez démarrer avec votre service web de nouveau gestionnaire de ressources Azure base existante, reportez-vous à [dispenser un service web prédictive existant](machine-learning-retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Créer une expérience de formation
 
Pour cet exemple, vous allez utiliser « exemple 5 : évaluation de Train, Test, pour le classement binaire : Dataset pour adultes » à partir des échantillons d’apprentissage de Microsoft Azure automatique. 
    
Pour créer l’expérience :

1.  Connexion à Microsoft Azure Studio de formation de l’ordinateur. 
2.  Dans le coin inférieur droit du tableau de bord, cliquez sur **Nouveau**.
3.  Dans le Microsoft Samples, sélectionnez 5 de l’exemple.
4.  Pour renommer l’expérimentation, dans la partie supérieure de la zone de l’expérimentation, sélectionnez le nom de l’expérience « exemple 5 : évaluation de Train, Test, pour le classement binaire : Dataset pour adultes ».
5.  Type de modèle recensement.
6.  En bas de l’expérimentation la zone de dessin, cliquez sur **exécuter**.
7.  Cliquez sur **configurer le service web** et sélectionnez le **service web de Retraining**. 

    ![Expérience initiale.][2]

Diagramme 2 : Initiaux de l’expérimentation.

## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Créer une expérience prédictive et le publier sous la forme d’un service web  

Ensuite, vous créez une expérience Predicative.

1.  En bas de la zone d’essai, cliquez sur **La valeur d’un Service Web** et sélectionnez **Prédictive Web Service**. Cela enregistre le modèle comme un modèle formé et ajoute des modules d’entrée et de sortie du service web. 
2.  Cliquez sur **exécuter**. 
3.  Après que l’exécution de l’essai est terminée, cliquez sur **Service Web de déploiement [standard]** ou de **Déploiement du Service Web [nouveau]**.

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Déployer l’expérience de formation sous la forme d’un service web de formation

Pour former le modèle formé, vous devez déployer l’expérience de formation que vous avez créé sous la forme d’un service web de Retraining. Ce service web nécessite un module de *Sortie du Service Web* connecté à la * [Modèle de Train] [ train-model] * module, pour être en mesure de produire de nouveaux modèles bien formés.

1. Pour revenir à l’expérience de formation, cliquez sur l’icône d’expériences dans le volet gauche, puis cliquez sur l’expérience nommé modèle de recensement.  
2. Dans la zone de recherche d’éléments d’expérience de recherche, tapez service web. 
3. Faites glisser un module *D’entrée du Service Web* sur le canevas de l’expérimentation et vous connecter de sa sortie dans le module de *Nettoyage des données manquantes* .  Ceci garantit que vos données de reconversion sont traitées de la même manière que vos données d’origine de la formation.
4. Faites glisser deux modules de *service de web sortie* sur le canevas de l’expérimentation. Connectez la sortie du module *Modèle du Train* à une et à la sortie du module *Évaluer le modèle* à l’autre. La sortie du service web pour le **Modèle de Train** nous donne le nouveau modèle formé. La sortie associée à **Évaluer le modèle** renvoie la sortie de ce module, qui est le résultat de performances.
5. Cliquez sur **exécuter**. 

Ensuite, vous devez déployer l’expérience de formation sous la forme d’un service web qui produit un modèle formé et des résultats d’évaluation de modèle. Pour ce faire, votre jeu suivant d’actions dépendent si vous travaillez avec un service web de classique ou un nouveau service web.  
  
**Service web classique**

En bas de la zone d’essai, cliquez sur **La valeur d’un Service Web** et sélectionnez le **Service Web de déploiement [standard]**. Le **tableau de bord** de Service Web s’affiche avec la clé d’API et de la page d’aide de l’API pour l’exécution par lots. Uniquement la méthode de l’exécution par lots peut être utilisée pour la création de modèles bien formés.

**Nouveau service web**

En bas de la zone d’essai, cliquez sur **La valeur d’un Service Web** et sélectionnez **Web Service de déploiement [nouveau]**. Le portail de formation de Machine Azure Service Web, Services Web s’ouvre à la page service de déploiement web. Tapez un nom pour votre service web et choisir un plan de paiement, puis cliquez sur **déployer**. Seule la méthode d’exécution du traitement par lots peut servir pour la création de modèles formés

Dans les deux cas, après que l’essai est terminée, le flux de travail qui en résulte doit se présenter comme suit :

![Flux de travail qui en résulte après exécution.][4]

Figure 3 : Résultant après l’exécution du flux de travail.

## <a name="retrain-the-model-with-new-data-using-bes"></a>Former le modèle avec de nouvelles données à l’aide d’environnements d’initialisation

Pour cet exemple, vous utilisez C# pour créer l’application de reconversion. Vous pouvez également utiliser l’exemple de code Python ou R pour accomplir cette tâche.

Pour appeler les API de recyclage :

1. Créez une Application de Console C# dans Visual Studio (Nouveau -> projet -> Windows Desktop -> Application Console).
2.  Ouvrez une session sur le portail du Service Web de formation Machine.
3.  Si vous travaillez avec un service de web standard, cliquez sur **Les Services Web classique**.
    1.  Cliquez sur le service web que vous utilisez.
    2.  Cliquez sur le point de terminaison par défaut.
    3.  Cliquez sur **utiliser**.
    4.  Au bas de la page de **consommer** , dans la section de **l’Exemple de Code** , cliquez sur **traitement par lots**.
    5.  Passez à l’étape 5 de cette procédure.
4.  Si vous travaillez avec un service web, cliquez sur **Les Services Web**.
    1.  Cliquez sur le service web que vous utilisez.
    2.  Cliquez sur **utiliser**.
    3.  Au bas de la page de consommer, dans la section de **l’Exemple de Code** , cliquez sur **traitement par lots**.
5.  Copier le code C# pour l’exécution du traitement par lots et collez-le dans le fichier Program.cs, s’assurer que l’espace de noms demeure intacte.

Ajoutez le package Nuget Microsoft.AspNet.WebApi.Client comme indiqué dans les commentaires. Pour ajouter la référence à Microsoft.WindowsAzure.Storage.dll, vous devrez tout d’abord installer la bibliothèque client Microsoft Azure services de stockage. Pour plus d’informations, consultez [Services de stockage Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Mise à jour de la déclaration d’apikey

Localisez la déclaration **apikey** .

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Dans la section **informations de base de la consommation** de la page de **consommer** , recherchez la clé primaire et le copier à la déclaration de **apikey** .

### <a name="update-the-azure-storage-information"></a>Mettre à jour les informations de stockage Azure

L’exemple de code BES télécharge un fichier à partir d’un lecteur local (par exemple « C:\temp\CensusIpnput.csv ») pour le stockage Azure, traite et écrit les résultats sur le stockage Azure.  

Pour accomplir cette tâche, vous devez récupérer les informations de nom et conteneur de clé de compte de stockage pour votre compte de stockage depuis le portail Azure classique et les valeurs correspondantes de la mise à jour dans le code. 

1. Connectez-vous au portail Azure classique.
1. Dans la colonne de navigation de gauche, cliquez sur **stockage**.
1. Dans la liste des comptes de stockage, sélectionnez un pour stocker le modèle retrained.
1. En bas de la page, cliquez sur **Gérer les touches d’accès rapide**.
1. Copier et enregistrer la **Clé d’accès primaire** et fermer la boîte de dialogue. 
1. En haut de la page, cliquez sur **les conteneurs**.
1. Sélectionnez un conteneur existant ou créez-en un nouveau et enregistrer le nom.

Recherchez les déclarations *StorageAccountName*, *StorageAccountKey*et *StorageContainerName* et mettre à jour les valeurs que vous avez enregistré à partir du portail Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
Vous devez également vérifier que le fichier d’entrée est disponible à l’emplacement que vous spécifiez dans le code. 

### <a name="specify-the-output-location"></a>Spécifier l’emplacement de sortie

Lorsque vous spécifiez l’emplacement de sortie dans la charge utile de demande, l’extension du fichier spécifié dans *RelativeLocation* doit être spécifiée en tant qu’ilearner. 

Voir l’exemple suivant :

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] Les noms de vos emplacements de sortie peuvent être différentes de celles dans cette procédure pas à pas dans l’ordre dans lequel vous avez ajouté les modules de sortie du service web. Dans la mesure où vous avez configuré cette expérience de formation avec deux sorties, les résultats incluent des informations d’emplacement de stockage pour les deux.  

![Recyclage de sortie][6]

Figure 4 : Recyclage de sortie.

## <a name="evaluate-the-retraining-results"></a>Évaluer les résultats de la reconversion
 
Lorsque vous exécutez l’application, la sortie inclut le jeton d’URL et les associations de sécurité nécessaire pour accéder aux résultats de l’évaluation.

Vous pouvez voir les résultats de performances du modèle retrained en combinant les *BaseLocation*, *RelativeLocation*et *SasBlobToken* à partir des résultats de sortie pour *output2* (comme illustré dans l’image de sortie de réadaptation précédente) et collez l’URL complète dans la barre d’adresse du navigateur.  

Examinez les résultats pour déterminer si le modèle qui vient d’être formé fonctionne assez bien pour remplacer l’existant.

Copier le *BaseLocation*, *RelativeLocation*et *SasBlobToken* dans les résultats, vous les utiliserez au cours du processus de reconversion.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez déployé le service web de prévision en cliquant sur le **Service Web de déploiement [standard]**, reportez-vous à [dispenser un service de web classique](machine-learning-retrain-a-classic-web-service.md).

Si vous avez déployé le service web de prévision en cliquant sur le **Déploiement du Service Web [nouveau]**, reportez-vous à [dispenser un service web à l’aide des applets de commande de gestion d’apprentissage Machine](machine-learning-retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/