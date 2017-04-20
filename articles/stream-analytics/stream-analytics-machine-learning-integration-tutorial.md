<properties
    pageTitle="Analyse de sentiment à l’aide d’Analytique de flux d’Azure et d’Azure Machine Learning | Microsoft Azure"
    description="Comment faire pour utiliser une fonction définie par l’utilisateur et à l’apprentissage de la Machine dans une tâche de flux de données Analytique"
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
    ms.date="10/04/2016" 
    ms.author="jeffstok"
/>

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Analyse de sentiment à l’aide d’Analytique de flux d’Azure et d’Azure Machine Learning #

Cet article est conçu pour vous aider à configurer rapidement une tâche simple Analytique de flux d’Azure, grâce à l’intégration de la formation de Machine Azure. Nous utilise un modèle d’apprentissage automatique sentiment analytique à partir de la galerie d’Intelligence de Cortana pour analyser le flux des données texte et déterminer le score sentiment en temps réel. Les informations contenues dans cet article peuvent vous aider à comprendre des scénarios tels qu’analytique sentiment en temps réel en continu les données Twitter, analyser les enregistrements de client des discussions avec le personnel de support et d’évaluer les commentaires sur des forums, des blogs et des vidéos, en plus des nombreux autres en temps réel, prédictives score scénarios.

Cet article propose un exemple de fichier CSV avec du texte en tant qu’entrée dans le stockage Blob d’Azure, illustré dans l’image suivante. La tâche s’applique le modèle analytique sentiment sous la forme d’une fonction définie par l’utilisateur (UDF) sur les exemples de données de texte à partir du magasin de blob. Le résultat final est placé dans le même magasin de blob dans un autre fichier CSV. 

![Analytique de flux Machine d’apprentissage](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

L’image suivante illustre cette configuration. Dans un scénario plus réaliste, vous pouvez remplacer le stockage Blob avec la diffusion en continu de données Twitter à partir d’une entrée de concentrateurs d’événement Azure. En outre, vous pourriez créer une visualisation en temps réel de [Microsoft BI de puissance](https://powerbi.microsoft.com/) du sentiment d’agrégation.    

![Analytique de flux Machine d’apprentissage](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Conditions préalables

Les composants requis pour exécuter les tâches présentées dans cet article sont les suivantes :

-   Un abonnement Azure.
-   Un fichier CSV avec des données qu’il contient. Vous pouvez télécharger le fichier indiqué dans la Figure 1 à partir de [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv), ou vous pouvez créer votre propre fichier. Pour cet article, nous supposons que vous utilisez celui qui est fourni gratuitement sur GitHub.

À un niveau élevé, pour effectuer les tâches démontrés dans cet article, vous devez procédez comme suit :

1.  Télécharger le fichier d’entrée CSV pour le stockage des objets Blob Azure.
2.  Ajout d’un modèle d’analytique de sentiment dans la galerie d’Intelligence Cortana à votre espace de travail de formation de Machine d’Azure.
3.  Déployer ce modèle sous la forme d’un service web dans l’espace de travail d’apprentissage automatique.
4.  Créer une tâche de flux Analytique qui appelle ce service web en tant que fonction, pour déterminer le sentiment de l’entrée de texte.
5.  Démarrer la tâche de flux de données Analytique et observez le résultat.

## <a name="upload-the-csv-input-file-to-blob-storage"></a>Télécharger le fichier d’entrée CSV pour le stockage des objets Blob

Pour cette étape, vous pouvez utiliser n’importe quel fichier CSV, telles que celle déjà spécifiée comme disponibles pour le téléchargement sur GitHub. Vous pouvez utiliser [Explorateur de stockage Azure](http://storageexplorer.com/) ou Visual Studio pour télécharger le fichier, ou vous pouvez utiliser du code personnalisé. Nous utilisons des exemples basés sur Visual Studio.

1.  Dans Visual Studio, cliquez sur **Azure** > **stockage** > **Joignez un stockage externe**. Entrez un **nom de compte** et d’une **clé de compte**.  

    ![Analytique de flux Machine d’apprentissage, Explorateur de serveurs](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)  

2.  Pour augmenter le stockage que vous avez associé à l’étape 1 et cliquez sur **Créer un conteneur Blob**, puis saisissez un nom logique. Après avoir créé le conteneur, ouvrez-le pour afficher son contenu. (Il sera vide à ce stade).  

    ![Formation de Machine Analytique de flux, de créer des blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)  

3.  Pour télécharger le fichier CSV, cliquez sur **Télécharger un Blob**, puis cliquez sur **le fichier à partir du disque local**.  

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Ajoutez le modèle analytique de sentiment dans la galerie d’Intelligence Cortana

1.  Télécharger le [modèle de prévision sentiment analytique](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) à partir de la galerie d’Intelligence Cortana.  
2.  Dans un Studio de formation de Machine, cliquez sur **Ouvrir dans le Studio**.  

    ![Flux de données Analytique, Machine d’apprentissage, ouvrir Studio de formation de Machine](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3.  Ouvrez une session accéder à l’espace de travail. Sélectionnez l’emplacement qui convient le mieux à votre propre emplacement.
4.  Cliquez sur **exécuter** en bas de la page.  
5.  Une fois que le processus s’exécute avec succès, cliquez sur **Déployer le Service Web**.
6.  Le modèle analytique sentiment est prêt à utiliser. Pour valider, cliquez sur le bouton de **Test** et fournir l’entrée de texte, tel que, « Je t’aime Microsoft ». Le test doit retourner un résultat semblable au suivant :

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Apprentissage automatique de flux Analytique, les données d’analyse](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

Dans la colonne des **applications** , cliquez sur le lien pour **Excel 2010 ou classeur antérieur** obtenir votre clé d’API et l’URL que vous aurez besoin ultérieurement pour configurer la tâche de flux de données Analytique. (Cette étape est requise uniquement pour utiliser un modèle d’apprentissage de l’ordinateur à partir d’un autre espace de travail compte Azure. Cet article suppose que c’est le cas, à l’adresse de ce scénario.)  

Notez l’URL et accès clé du service web à partir du fichier Excel téléchargé, comme indiqué ci-dessous :  

![Apprentissage automatique de flux Analytique, coup de œil rapide](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Créer une tâche de flux Analytique qui utilise le modèle d’apprentissage automatique

1.  Accédez au [portail Azure](https://manage.windowsazure.com).  
2.  Cliquez sur **Nouveau** > **les Services de données** > **Analytique de flux** > **Création rapide**. Entrez un nom pour votre projet dans le **Nom de la tâche**, entrez la région appropriée pour le travail dans la **région**et puis sélectionnez le compte dans le **Compte de stockage de surveillance régionale**.    
3.  Après la création du travail, dans l’onglet **entrées** , cliquez sur **Ajouter une entrée**.  

    ![Formation de Machine Analytique de flux, ajouter l’entrée d’apprentissage automatique](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)  

4.  Sur la première page de l’Assistant **Ajouter une entrée** , cliquez sur le **flux de données**, puis cliquez sur **suivant**. Sur la page suivante, cliquez sur **Stockage Blob** comme entrée, puis cliquez sur **suivant**.  
5.  Dans la page **Paramètres du stockage Blob** de l’Assistant, fournissez le nom de conteneur de compte de stockage blob défini précédemment lorsque vous avez chargé les données. Cliquez sur **suivant**. Pour le **Format de sérialisation d’événement**, cliquez sur **CSV**. Acceptez les valeurs par défaut pour le reste de la page des **paramètres de sérialisation** . Cliquez sur **OK**.  
6.  Sous l’onglet **sortie** , cliquez sur **Ajouter une sortie**.  

    ![Formation de Machine Analytique de flux, ajouter une sortie](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)  

7.  Cliquez sur le **Stockage des objets Blob**et entrez les mêmes paramètres, à l’exception du conteneur. La valeur de **l’entrée** a été configurée pour lire à partir du conteneur nommé « test » dans lequel le fichier **CSV** a été téléchargé. Pour la **sortie**, entrez « RésultatTest ». Les noms de conteneur doivent être différents. Vérifier l’existence de ce conteneur.     
8.  Cliquez sur **suivant** pour configurer les **paramètres de sérialisation**de la sortie. Comme avec l' **entrée**, cliquez sur **CSV**, puis cliquez sur le bouton **OK** .
9.  Sous l’onglet **fonctions** , cliquez sur **Ajouter une fonction d’apprentissage Machine**.  

    ![Formation de Machine Analytique de flux, ajoutez la fonction d’apprentissage automatique](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)  

10. Dans la page **Paramètres du Service Machine formation Web** , recherchez l’espace de travail apprentissage automatique, le service web et le point de terminaison par défaut. Pour cet article, appliquez les paramètres manuellement pour se familiariser avec la configuration d’un service web pour un espace de travail, tant que vous connaissez l’URL et que vous possédez la clé API. Entrez le point de terminaison **URL** et la **clé d’API**. Cliquez sur **OK**.    

    ![Service web de flux Analytique Machine apprentissage, apprentissage automatique](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)    

11. Sous l’onglet **requête** , modifiez la requête comme suit :    

 ```
    WITH subquery AS (  
        SELECT text, sentiment(text) as result from input  
    )  
 
    Select text, result.[Scored Labels]  
    Into output  
    From subquery  
 ```    
12. Cliquez sur **Enregistrer** pour enregistrer la requête.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Démarrer la tâche de flux de données Analytique et observez la sortie

1.  Cliquez sur **Démarrer** au bas de la page de la tâche.
2.  Dans la **Boîte de dialogue requête démarrer**, cliquez sur **Heure personnalisé**et sélectionnez une heure avant lorsque vous téléchargez le CSV pour le stockage des objets Blob. Cliquez sur **OK**.  

    ![Formation de Machine de flux Analytique, heure personnalisée](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)  

3.  Atteindre le stockage Blob à l’aide de l’outil qui que vous permet de télécharger le fichier CSV, par exemple, Visual Studio.
4.  Quelques minutes après le démarrage de la tâche, le conteneur de sortie est créé et un fichier CSV est téléchargé vers elle.  
5.  Ouvrez le fichier dans l’éditeur par défaut CSV. Quelque chose de similaire au suivant doit s’afficher :  

    ![Afficher les flux Analytique Machine Learning, CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Conclusion

Cet article montre comment créer une tâche de flux Analytique qui lit le flux des données de texte et applique l’analytique de sentiment aux données en temps réel. Vous pouvez accomplir tout cela sans avoir à vous soucier de la complexité de la création d’un modèle analytique de sentiment. Il s’agit d’un des avantages de la Suite d’Intelligence Cortana.

Vous pouvez également afficher les mesures de formation de Machine Azure liés à la fonction. Pour ce faire, cliquez sur l’onglet **Moniteur** . Trois mesures liés à la fonction sont affichés.  

- **Fonction de demandes** indique le nombre de demandes envoyées à un service web de formation de l’ordinateur.  
- **Événements de fonction** indique le nombre d’événements dans la demande. Par défaut, chaque demande à un service web de formation de Machine contient jusqu'à 1 000 événements.  
- **Demandes de fonction ayant échoué** indique le nombre de demandes qui ont échoué pour le service web d’apprentissage automatique.  

    ![Apprentissage automatique de flux Analytique, affichage de moniteur d’apprentissage automatique](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)  
