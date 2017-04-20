<properties
   pageTitle="Enregistrer les données de magasin de données lac dans le catalogue de données Azure | Microsoft Azure"
   description="Enregistrer les données de magasin de données lac dans le catalogue de données Azure"
   services="data-lake-store,data-catalog" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Enregistrer les données de magasin de données lac dans le catalogue de données Azure

Dans cet article, vous apprendrez comment intégrer le lac Azure Data Store avec le catalogue de données Azure pour transformer vos données détectable au sein d’une organisation en l’intégrant avec les données de catalogue. Pour plus d’informations sur les données de catalogue, consultez le [Catalogue de données Azure](../data-catalog/data-catalog-what-is-data-catalog.md). Pour comprendre les scénarios dans lesquels vous pouvez utiliser le catalogue de données, consultez [les scénarios courants de catalogue de données Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Activer votre abonnement Azure** pour données lac magasin Public Preview. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).

- **Compte de banque de LAC de données azure**. Suivez les instructions de la section [mise en route de magasin de LAC de données Azure via le portail d’Azure](data-lake-store-get-started-portal.md). Pour ce didacticiel, nous créer un compte de banque de données lac appelé **datacatalogstore**. 

    Après avoir créé le compte, télécharger un jeu de données d’exemple pour elle. Pour ce didacticiel, nous charger tous les fichiers .csv sous le dossier **AmbulanceData** dans le [Référentiel Git lac Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Vous pouvez utiliser divers clients, tels que l' [Explorateur de stockage Azure](http://storageexplorer.com/), pour télécharger des données à un conteneur d’objet blob.

- **Catalogue de données azure**. Votre organisation doit avoir un catalogue de données Azure créés pour votre organisation. Un seul catalogue est autorisé pour chaque organisation.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Magasin de LAC de données de Registre en tant que source de données de catalogue

>[AZURE.VIDEO adcwithadl] 

1. Accédez à `https://azure.microsoft.com/services/data-catalog`, puis cliquez sur **mise en route**.

2. Ouvrez une session sur le portail du catalogue de données Azure, puis cliquez sur **publier les données**.

    ![Enregistrer une source de données] (./media/data-lake-store-with-data-catalog/register-data-source.png "Enregistrer une source de données")

3. Sur la page suivante, cliquez sur **Lancer l’Application**. Il télécharge le fichier manifeste d’application sur votre ordinateur. Double-cliquez sur le fichier manifeste pour démarrer l’application.

4. Dans la page Bienvenue, cliquez sur **se connecter**et entrez vos informations d’identification.

    ![Écran d’accueil] (./media/data-lake-store-with-data-catalog/welcome.screen.png "Écran d’accueil")

5. Dans la page Sélectionner une Source de données, sélectionnez le **Lac de données Azure**, puis cliquez sur **suivant**.

    ![Sélectionnez la source de données] (./media/data-lake-store-with-data-catalog/select-source.png "Sélectionnez la source de données")

6. Sur la page suivante, indiquez le nom de compte de banque de données lac que vous souhaitez enregistrer dans le catalogue de données. Laissez les autres options par défaut et puis cliquez sur **se connecter**.

    ![Se connecter à la source de données] (./media/data-lake-store-with-data-catalog/connect-to-source.png "Se connecter à la source de données")

7. La page suivante peut être divisée en segments suivants.

    une barre d’outils. La boîte **Serveur de la hiérarchie** représente la structure de dossier de compte de banque de données lac. **$Root** représente la racine de compte de banque de données LAC et **AmbulanceData** le dossier créé à la racine du compte banque de données lac.

    b. La zone **objets disponibles** répertorie les fichiers et les dossiers sous le dossier **AmbulanceData** .

    c. Les fichiers et les dossiers que vous souhaitez enregistrer dans le catalogue de données Azure répertorie les **objets pour être enregistré** .

    ![Structure de données d’affichage] (./media/data-lake-store-with-data-catalog/view-data-structure.png "Structure de données d’affichage")

8. Pour ce didacticiel, vous devez enregistrer tous les fichiers dans le répertoire. Pour ce faire, cliquez sur (![déplacer des objets de](./media/data-lake-store-with-data-catalog/move-objects.png "déplacer des objets")) pour déplacer tous les fichiers de zone **d’objets à enregistrer** . 

    Dans la mesure où les données seront immatriculées dans un catalogue à l’échelle de l’organisation, il est une approche recommandé pour ajouter des métadonnées que vous pouvez utiliser ultérieurement pour retrouver rapidement les données. Par exemple, vous pouvez ajouter une adresse de messagerie pour le propriétaire des données (par exemple, celui qui transfère les données) ou ajoutez une balise pour identifier les données. La capture d’écran ci-dessous montre une balise que nous ajouter aux données.

    ![Structure de données d’affichage] (./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Structure de données d’affichage")

    Cliquez sur **Enregistrer**.

8. La capture d’écran suivante indique que les données sont correctement enregistrées dans le catalogue de données.

    ![Inscription terminée] (./media/data-lake-store-with-data-catalog/registration-complete.png "Structure de données d’affichage")

9. Cliquez sur le **Portail de l’affichage** pour revenir en arrière sur le portail du catalogue de données et vérifiez que vous pouvez désormais accéder les données enregistrées à partir du portail. Pour rechercher les données, vous pouvez utiliser la balise que vous avez utilisé lors de l’enregistrement de données.

    ![Données de recherche dans le catalogue] (./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Données de recherche dans le catalogue")

10. Vous pouvez désormais effectuer des opérations telles que l’ajout d’annotations et la documentation pour les données. Pour plus d’informations, consultez les liens suivants.
    * [Annoter des sources de données dans le catalogue de données](../data-catalog/data-catalog-how-to-annotate.md)
    * [Sources de données de document dans le catalogue de données](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Voir aussi

* [Annoter des sources de données dans le catalogue de données](../data-catalog/data-catalog-how-to-annotate.md)
* [Sources de données de document dans le catalogue de données](../data-catalog/data-catalog-how-to-documentation.md)
* [Intégrer le magasin de données LAC avec d’autres services Azure](data-lake-store-integrate-with-other-services.md)
