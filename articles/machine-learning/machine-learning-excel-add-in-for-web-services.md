<properties
    pageTitle="Macro complémentaire Excel pour les services Web d’apprentissage Machine | Microsoft Azure"
    description="Comment utiliser les services Web de formation de Machine Azure directement dans Excel sans écrire de code."
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/05/2016"
    ms.author="tedway;garye" />

# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Le complément Excel pour les services Web de formation de Machine Azure

Excel vous permet de facilement appeler des services Web directement sans avoir à écrire de code.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Étapes à suivre pour utiliser un service Web existant dans le classeur

1. Ouvrez l' [exemple de fichier Excel](http://aka.ms/amlexcel-sample-2)qui contient le complément Excel et les données relatives aux passagers sur le TITANE.
2. Choisissez le service Web en cliquant dessus-« Titanic Predictor de survie (exemple Excel Add-in) [résultat] » dans cet exemple.

    ![Sélectionnez le service Web][01]

3. Vous accédez alors à la section **Predict** .  Ce classeur contient déjà des exemples de données, mais pour un classeur vide, vous pouvez sélectionner une cellule dans Excel et cliquez sur **utiliser les données exemple**.
4. Sélectionnez les données avec les en-têtes, puis cliquez sur l’icône de la plage d’entrée de données.  Assurez-vous que la case « mes données ont des en-têtes » est activée.
5. Sous **sortie**, entrez le numéro de la cellule où vous souhaitez que la sortie est, par exemple « H1 » ici.
6. Cliquez sur **prévoir**.

    ![Prédire la section][02]

## <a name="steps-to-add-a-new-web-service"></a>Étapes pour ajouter un nouveau site Web service

Déployer un service Web ou utiliser un service Web existant. Pour plus d’informations sur le déploiement d’un service Web, consultez [procédure étape 5 : déployer le service Web de formation Azure Machine](machine-learning-walkthrough-5-publish-web-service.md).

Obtention de la clé de l’API pour votre service Web. Où cela dépend si vous avez publié un service Web de formation Machine classique d’un service Web de formation nouvelle Machine.

**Utiliser un service Web classique** 

1. Dans un Studio de formation de Machine, cliquez sur la section **WEB SERVICES** dans le volet gauche et puis sélectionnez le service Web.

    ![Sélection de Studio un service Web][04]

2. Copiez la clé d’API pour le service Web.

    ![Clé de l’API de Studio][05]

3. Sous l’onglet **tableau de bord** pour le service Web, cliquez sur le lien de **Demande/réponse** .
4. Recherchez la section de **l’URI de la demande** .  Copier et enregistrer l’URL.

>[AZURE.NOTE] Il est désormais possible de se connecter au portail [Azure Machine Learning Web Services](https://services.azureml.net) pour obtenir la clé API d’un service Web de formation classique Machine.

**Utiliser un service Web de nouvelles**

1. Dans le portail [Azure Machine Learning, Services Web](https://services.azureml.net) , **Services Web**, puis sur votre service Web. 
2. Cliquez sur **utiliser**.
3. Recherchez la section **info de la base de la consommation** . Copier et enregistrer la **Clé primaire** et l’URL de **Demande-réponse** .


## <a name="steps-to-add-a-new-web-service"></a>Étapes pour ajouter un nouveau site Web service

1. Déployer un service Web ou utiliser un service Web existant. Pour plus d’informations sur le déploiement d’un service Web, consultez [procédure étape 5 : déployer le service Web de formation Azure Machine](machine-learning-walkthrough-5-publish-web-service.md).
2. Cliquez sur **utiliser**.
3. Recherchez la section **info de la base de la consommation** . Copier et enregistrer la **Clé primaire** et l’URL de **Demande-réponse** .
2. Dans Excel, consultez la section **Services Web** (si vous êtes dans la section **Predict** , cliquez sur la flèche vers la gauche pour accéder à la liste des services Web).

    ![Aller à la sélection de service Web][03]
    
3. Cliquez sur **Ajouter un Service Web**.
4. Collez l’URL dans la zone de texte de macro complémentaire Excel **URL**.
5. Coller la clé API/principal dans la zone de texte **clé API**.
6. Cliquez sur **Ajouter**.

    ![Clé API et les URL d’un service Web classique.][06]

10. Pour utiliser le service Web, suivez les instructions ci-dessus, « Étapes pour utiliser un Service Web d’existant ».

## <a name="sharing-your-workbook"></a>Partage de votre classeur.

Si vous enregistrez le classeur, puis la clé API/principal pour les services Web que vous avez ajouté est également enregistrée. Cela signifie que vous devez uniquement partager le classeur avec des personnes de que confiance.

Poser des questions dans la section de commentaire suivante ou sur notre [forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png
