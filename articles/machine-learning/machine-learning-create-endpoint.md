<properties
    pageTitle="Création de points de terminaison de service Web dans l’apprentissage automatique | Microsoft Azure"
    description="Création de points de terminaison de service Web dans l’apprentissage automatique de Azure"
    services="machine-learning"
    documentationCenter=""
    authors="hiteshmadan"
    manager="padou"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="10/04/2016"
    ms.author="himad"/>


# <a name="creating-endpoints"></a>Création de points de terminaison

>[AZURE.NOTE] Cette rubrique décrit les techniques applicables à un service Web classique.

Lorsque vous créez des Web services que vous vendez transférer à vos clients, vous devez fournir des modèles bien formés à chaque client qui sont toujours liés à l’expérience à partir de laquelle le service Web a été créé. En outre, les mises à jour à l’essai doivent être appliqués de manière sélective à un point de terminaison sans remplacer les personnalisations.

Pour ce faire, Azure Machine Learning vous permet de créer plusieurs points de terminaison pour un service Web déployé. Chaque point de terminaison du service Web est indépendamment adressé, accéléré et géré. Chaque point de terminaison est une URL unique et la clé d’autorisation que vous pouvez distribuer à vos clients.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Ajout de points de terminaison pour un service Web

Il existe trois façons d’ajouter un point de terminaison pour un service Web.

* Par programme
* Via le portail Azure Machine Learning Web Services
* Bien que le portail classique Azure

Une fois le point de terminaison est créé, vous pouvez consommer via des API synchrones, lot d’API et feuilles de calcul excel. Outre l’ajout de points de terminaison par le biais de cette interface utilisateur, vous pouvez également utiliser les API de gestion de point de terminaison pour ajouter par programme des points de terminaison.

 >[AZURE.NOTE] Si vous avez ajouté des points de terminaison supplémentaires au service Web, vous ne pouvez pas supprimer le point de terminaison par défaut.

## <a name="adding-an-endpoint-programmatically"></a>Ajout par programme d’un point de terminaison

Vous pouvez ajouter un point de terminaison pour votre service Web par programmation à l’aide de l’exemple de code [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Ajout d’un point de terminaison via le portail Azure Machine Learning Web Services

1. Dans un Studio de formation de Machine, sur la colonne de navigation de gauche, cliquez sur les Services Web.
2. En bas du tableau de bord de service Web, cliquez sur **gestion de points de terminaison**. Le portail Azure Machine Learning Web Services s’ouvre à la page points de terminaison pour le service Web.
3. Cliquez sur **Nouveau**.
4. Tapez un nom et une description pour le nouveau point de terminaison. Les noms de point de terminaison doivent être 24 caractères ou moins, longueur et doivent être composés de lettres minuscules ou des nombres. Sélectionnez le niveau d’enregistrement et d’exemples de données soit activés ou non. Pour plus d’informations sur la journalisation, consultez [activation de la journalisation pour les services Web de formation de Machine](machine-learning-web-services-logging.md).

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>Ajout d’un point de terminaison à l’aide du portail classique Azure


1. Connectez-vous à l' [Azure portal classique](http://manage.windowsazure.com), cliquez sur **Apprentissage automatique** dans la colonne de gauche. Cliquez sur l’espace de travail qui contient le service Web qui vous intéressez.

    ![Accédez à l’espace de travail](./media/machine-learning-create-endpoint/figure-1.png)

2. Cliquez sur **les Services Web**.

    ![Accédez aux services Web](./media/machine-learning-create-endpoint/figure-2.png)

3. Cliquez sur le service Web que qui vous intéressent afficher la liste des points de terminaison disponibles.

    ![Naviguez jusqu’au point de terminaison](./media/machine-learning-create-endpoint/figure-3.png)

4. En bas de la page, cliquez sur **Ajouter un point de terminaison**. Tapez un nom et une description, vous assurer qu’aucun autre point de terminaison portant le même nom dans ce service Web. Laissez le niveau d’accélération avec sa valeur par défaut, sauf si vous avez des exigences particulières. Pour plus d’informations sur la régulation, reportez-vous à la section [Mise à l’échelle d’API de points de terminaison](machine-learning-scaling-webservice.md).

    ![Créer le point de terminaison](./media/machine-learning-create-endpoint/figure-4.png)

## <a name="next-steps"></a>Étapes suivantes

[Comment consommer un service Web de formation de Machine Azure publié](machine-learning-consume-web-services.md).
