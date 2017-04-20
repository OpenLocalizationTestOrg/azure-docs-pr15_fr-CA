<properties
    pageTitle="Création d’un modèle avec l’UI Recommnendations | Microsoft Azure"
    description="Azure recommandations d’apprentissage automatique - création d’un modèle avec l’interface utilisateur de recommandations"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="luisca"/>

# <a name="building-a-model-with-the-recommendations-ui"></a>Création d’un modèle avec l’interface utilisateur de recommandations

Ce document est un guide pas à pas. Notre objectif est de vous guider à travers les étapes nécessaires pour former un modèle à l’aide de l' [Interface utilisateur de recommandations](https://recommendations-portal.azurewebsites.net/).
Passer par l’exercice vous permet de comprendre le processus de création d’un modèle avant de l’effectuer par programmation. Il vous familiarise également avec l’interface utilisateur, qui est très pratique lorsque vous commencez à utiliser le service.

Cet exercice prend environ 30 minutes.

<a name="Step1"></a>
## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Étape 1 - signe dans l’interface utilisateur des recommandations ##

1. Si vous ne le n'avez pas déjà fait, vous devez [d’inscription](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) d’un nouvel abonnement [d’API de recommandations](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) . Vous pouvez vous inscrire pour le service sur **Azure** au [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) et à la connexion à votre compte Azure. Des instructions détaillées sur la procédure d’inscription sont fournies dans la *tâche 1* du [Guide de démarrage](cognitive-services-recommendations-quick-start.md) 

1. Une fois que vous avez obtenu une **clé** pour votre abonnement d’API de recommandations, accédez à [L’interface utilisateur de recommandations](https://recommendations-portal.azurewebsites.net/). 

1. Connectez-vous au portail en entrant votre clé dans le champ **Clé de compte** , puis cliquez sur le bouton de **connexion** .

    ![Recommandations de l’interface utilisateur : Signe-boîte de dialogue.][reco_signin]


<a name="Step2"></a>
## <a name="step-2---lets-gather-some-training-data"></a>Étape 2 - nous allons recueillir certaines données de formation ##

Avant de pouvoir créer une build, le moteur a besoin de deux informations : un fichier de catalogue et d’un ensemble de fichiers d’utilisation. 

Le fichier catalogue contient des informations sur les articles que vous offrez à vos clients. Un fichier d’utilisation contient des informations sur la façon dont ces éléments sont utilisés, ou les transactions à partir de votre entreprise.

Généralement, vous souhaitez interroger votre base de données de banque d’informations pour ces éléments d’information. Aujourd'hui, nous avons fourni des exemples de données pour vous afin que vous pouvez apprendre à utiliser l’API de recommandations.

Vous pouvez télécharger les données à partir de [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Copier et décompresser le fichier **Books.Zip** dans un dossier sur votre ordinateur local. Par exemple, **c:\data**.

Vous trouverez des informations détaillées sur le schéma des fichiers de catalogue et l’utilisation dans l’article [collecte de données pour exercer votre modèle](cognitive-services-recommendations-collecting-data.md) .
 
Pour cet exercice, nous allons travailler avec un fichier de petite taille afin que vous n’ayez pas très d’attente trop longue pour la formation. Si vous souhaitez un fichier plus réaliste, nous avons également **MsStoreData.zip** qui contient des exemples de transactions à partir du Microsoft Store dans [même emplacement](http://aka.ms/RecoSampleData).

<a name="Step3"></a>
## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Étape 3 - créer un projet et télécharger des données de catalogue et d’utilisation ##

Lors de la connexion à l' [Interface utilisateur de recommandations](https://recommendations-portal.azurewebsites.net/), vous consultez la Page projets. Si vous avez créé tous les projets, vous devez les voir ici.
Un projet (également connu sous le nom *d’un modèle* dans la référence de l’API) est un conteneur pour vos données de catalogue et d’utilisation. Vous pouvez créer plusieurs *versions* dans le projet. Nous vous guidera à travers le processus dans les étapes suivantes.

1. Pour créer un nouveau projet, tapez le nom de la zone de texte (quelque chose comme « MyFirstModel » pourrait fonctionner), puis cliquez sur **Ajouter un projet**.
 
    ![Recommandations de l’interface utilisateur : La Page projets.][reco_projects]

1. Une fois que le projet est créé, cliquez sur le bouton **Rechercher le fichier** dans la section **Ajouter un fichier de catalogue** . Télécharger le catalogue que vous avez obtenu à l’étape 2. Si vous l’avez enregistré dans *c:\data*, vous avez besoin accéder à ce dossier.

    ![Recommandations de l’interface utilisateur : Ajout de données à un projet.][reco_firstmodel]

1. Une fois que le catalogue est téléchargé, cliquez sur le bouton **Rechercher le fichier** dans la section **Ajouter des fichiers de l’utilisation** . Ajoutez le fichier usage_large.txt.

> **Que se passe-t-il si j’ai un fichier volumineux, des données d’utilisation ?**
>
> Seuls les fichiers utilisation inférieures à 200 Mo peuvent être téléchargés. Ceci dit, le système peut contenir jusqu'à équivalent de 2 Go de données de transaction, afin que vous pouvez télécharger plusieurs fichiers si nécessaire.
> Vous n’êtes peut-être pas toutes ces données pour générer un modèle utile, il n’est pas simplement la taille des données qui est important, mais la qualité des données. Il est courant de voir les données d’utilisation où la plupart des transactions est seulement sur quelques éléments les plus appréciés, et il existe peu de signal « » pour les autres éléments.

<a name="Step4"></a>
## <a name="step-4---lets-do-some-training"></a>Étape 4 - nous allons effectuer une formation ! ##

Maintenant que vous avez chargé à la fois le catalogue et les données d’utilisation, nous sommes prêts à former le moteur afin qu’il peut apprendre des modèles à partir de nos données.

1.  Cliquez sur le bouton **Générer de nouveau** .

1.  Sélectionnez **les recommandations** comme le type de build. Notez que nous prenons en charge une priorité de Build et un FBT (souvent acheté ensemble) générer ainsi des types.

    ![Recommandations de l’interface utilisateur : Créer la boîte de dialogue.][reco_build_dialog.png]


    Une génération FBT vous permet d’identifier des modèles pour les produits qui sont généralement achetés/utilisés dans la même transaction.
    Une génération de classement est utilisée pour identifier des fonctionnalités intéressantes. 
    Nous n’entrerons pas très profond dans FBT ou classement génère dans cet atelier, mais si vous souhaitez vous devez extraire la [page de documentation de qualité de modèle et les types de Build](cognitive-services-recommendations-buildtypes.md).

1. Cliquez sur le bouton **Générer** . Le processus de génération dure environ cinq minutes si vous utilisez les données de livres. Il prend plus de temps sur des ensembles de données plus grandes.

<a name="Step5"></a>
## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Étape 5 - voyons ce que l’ordinateur a appris ! ##

Une fois la build terminée, vous remarquerez une nouvelle build dans la liste de builds. Cette build peut être interrogée pour obtenir des recommandations article et de l’utilisateur.

1. Une fois la build terminée, cliquez sur **note**. Cela vous permet de jouer avec le modèle et voir quels éléments sont recommandés.

    ![Recommandations de l’interface utilisateur : Bouton de Score][reco_score_button]

1. Sélectionnez un élément pour afficher les éléments qui sont retournés sous forme de recommandations pour cet élément. Notez que s’il n’y a pas assez de mouvements de prédire une recommandation pour un élément particulier, le système renverra pas les recommandations de cet article.  Si pour une raison quelconque, vous avez un élément qui ne renvoie aucune recommandation, essayez le score des autres éléments.

<a name="Step6"></a>
## <a name="step-6---next-steps"></a>Étape 6 - étapes suivantes ##
Félicitations ! vous avez formé un modèle et a ensuite des recommandations à partir de ce modèle.  L’interface utilisateur de recommandations est un outil très utile qui vous permet de voir l’état de vos projets et les builds. 

Maintenant que vous disposez d’un modèle, vous souhaiterez peut-être apprendre à effectuer toutes les étapes ci-dessus par programme. Pour savoir à appeler l’API par programme, nous vous invitons à vérifier la [Référence des API de recommandations](http://go.microsoft.com/fwlink/?LinkId=759348) et de télécharger l' [Exemple d’Application recommandations](http://go.microsoft.com/fwlink/?LinkID=759344).


[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png
