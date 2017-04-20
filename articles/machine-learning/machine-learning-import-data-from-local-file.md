<properties
    pageTitle="Importer des données dans un Studio de formation de Machine à partir d’un fichier local | Microsoft Azure"
    description="Comment importer vos données de formation Azure Machine Learning Studio à partir d’un fichier local."
    keywords="importer des données, format de données, types de données, les sources de données, les données de formation"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye;bradsev" />


# <a name="import-your-training-data-into-azure-machine-learning-studio-from-a-local-file"></a>Importer vos données de formation dans Azure Machine Learning Studio à partir d’un fichier local

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Pour utiliser vos propres données dans Studio d’apprentissage Machine, vous pouvez télécharger un fichier de données à l’avance à partir de votre disque dur pour créer un module de dataset dans votre espace de travail. 


## <a name="import-data-from-a-local-file"></a>Importer des données à partir d’un fichier local

Vous pouvez importer des données à partir d’un disque dur local en procédant comme suit :

1. Cliquez sur **+ Nouveau** en bas de la fenêtre de la Machine Learning Studio.
2. Sélectionnez le **groupe de données** et **les fichiers locaux de**.
3. Dans la boîte de dialogue **télécharger un nouveau groupe de données** , recherchez le fichier que vous souhaitez télécharger
4. Entrez un nom, identifier le type de données et éventuellement entrer une description. Une description est recommandée : il vous permet d’enregistrer toutes les caractéristiques sur les données que vous devez garder à l’esprit lorsque vous utilisez les données à l’avenir.
5. La case à cocher **il s’agit de la nouvelle version d’un dataset existant** vous permet de mettre à jour un dataset existant avec les nouvelles données. Cliquez sur cette case à cocher, puis entrez le nom d’un groupe de données existant.

Pendant le téléchargement, vous verrez un message que votre fichier est en cours de téléchargement. Télécharger durée dépend de la taille de vos données et de la vitesse de votre connexion au service.
Si vous connaissez que le fichier prendra un certain temps, vous faire autre chose dans Studio d’apprentissage Machine pendant que vous attendez. Toutefois, la fermeture du navigateur ne provoque pas le téléchargement des données échoue.

Une fois vos données sont téléchargées, il est stocké dans un module de groupe de données et est disponible pour toute expérience dans votre espace de travail.
Lorsque vous modifiez une expérience, vous trouverez les jeux de données que vous avez créé dans la liste de **Mes groupes de données** dans la liste **Des groupes de données enregistrées** dans la palette du module. Vous pouvez glisser- déposer sur le canevas de l’expérience, le groupe de données lorsque vous souhaitez utiliser le jeu de données pour l’analytique supplémentaire et l’apprentissage de l’ordinateur.



