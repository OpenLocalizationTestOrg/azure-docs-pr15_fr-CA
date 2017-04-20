<properties
   pageTitle="Analyser les données dans le magasin de données lac à l’aide de BI d’alimentation | Microsoft Azure"
   description="Alimentation BI permet d’analyser les données stockées dans le magasin de LAC de données Azure"
   services="data-lake-store" 
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
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analyser les données dans le magasin de données lac à l’aide de BI de puissance

Dans cet article, vous allez apprendre à utiliser le bureau de BI de puissance pour analyser et visualiser des données stockées dans le magasin de LAC de données Azure.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Compte de banque de LAC de données azure**. Suivez les instructions de la section [mise en route de magasin de LAC de données Azure via le portail d’Azure](data-lake-store-get-started-portal.md). Cet article suppose que vous avez déjà créé un compte de banque de données lac, appelé **mybidatalakestore**et télécharger un exemple de fichier de données (**Drivers.txt**) lui. Cet exemple de fichier est disponible au téléchargement à partir du [Référentiel Git lac Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).

- **Bureau de BI d’alimentation**. Vous pouvez le télécharger à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 


## <a name="create-a-report-in-power-bi-desktop"></a>Créer un rapport dans le bureau de BI de puissance

1. Lancement d’alimentation BI bureau sur votre ordinateur.

2. À partir du ruban **accueil** , cliquez sur **Obtenir les données**, puis cliquez sur plus. Dans la boîte de dialogue **Obtenir les données** , cliquez sur **Azure**et cliquez sur **Azure données lac magasin**puis cliquez sur **se connecter**.

    ![Se connecter au magasin de données lac] (./media/data-lake-store-power-bi/get-data-lake-store-account.png "Se connecter au magasin de données lac")

3. Si vous voyez une boîte de dialogue à propos du connecteur en cours dans une phase de développement, opter pour continuer.

4. Dans la boîte de dialogue **Magasin de LAC données Microsoft Azure** , fournir l’URL de votre compte de banque de données lac, puis cliquez sur **OK**.

    ![URL pour le magasin de données lac] (./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL pour le magasin de données lac")

5. Dans la boîte de dialogue suivante, cliquez sur **connexion** pour vous connecter au compte de la banque de données lac. Vous allez être redirigé vers la page de connexion de votre organisation. Suivez les invites pour vous connecter au compte.

    ![Signe dans le magasin de données lac] (./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Signe dans le magasin de données lac")

6. Après que vous être connecté, cliquez sur **se connecter**.

    ![Se connecter au magasin de données lac] (./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Se connecter au magasin de données lac")

7. La boîte de dialogue suivante affiche le fichier que vous avez téléchargé à votre compte de banque de données lac. Vérifiez les informations, puis sur **charger**.

    ![Charger les données de magasin de données lac] (./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Charger les données de magasin de données lac")

8. Une fois que les données a été chargées avec succès en puissance BI, vous verrez les champs suivants dans l’onglet **champs** .

    ![Champs importés] (./media/data-lake-store-power-bi/imported-fields.png "Champs importés")

    Toutefois, pour visualiser et analyser les données, nous préférons les données soient disponibles par les champs suivants

    ![Champs souhaité] (./media/data-lake-store-power-bi/desired-fields.png "Champs souhaité")

    Dans les étapes suivantes, nous mettrons à jour la requête pour convertir les données importées dans le format souhaité.

9. À partir du ruban **accueil** , cliquez sur **Modifier les requêtes**.

    ![Modifier des requêtes] (./media/data-lake-store-power-bi/edit-queries.png "Modifier des requêtes")

10. Dans l’éditeur de requête, sous la colonne de **contenu** , cliquez sur **binaire**.

    ![Modifier des requêtes] (./media/data-lake-store-power-bi/convert-query1.png "Modifier des requêtes")

11. Vous voyez une icône de fichier, ce qui représente le fichier **Drivers.txt** que vous avez téléchargé. Cliquez sur le fichier, puis cliquez sur **CSV**.  

    ![Modifier des requêtes] (./media/data-lake-store-power-bi/convert-query2.png "Modifier des requêtes")

12. Vous devriez voir une sortie comme indiqué ci-dessous. Vos données sont désormais disponibles dans un format que vous pouvez utiliser pour créer des visualisations.

    ![Modifier des requêtes] (./media/data-lake-store-power-bi/convert-query3.png "Modifier des requêtes")

13. À partir du ruban **accueil** , cliquez sur **Fermer et appliquer**, puis cliquez sur **Fermer et appliquer**.

    ![Modifier des requêtes] (./media/data-lake-store-power-bi/load-edited-query.png "Modifier des requêtes")

14. Une fois que la requête est mise à jour, l’onglet **champs** affiche les champs disponibles pour la visualisation.

    ![Champs mis à jour] (./media/data-lake-store-power-bi/updated-query-fields.png "Champs mis à jour")

15. Créons un graphique à secteurs pour représenter les pilotes dans chaque ville d’un pays donné. Pour ce faire, effectuez les sélections suivantes.

    1. À partir de l’onglet de visualisation, cliquez sur le symbole d’un graphique en secteurs.

        ![Graphique en secteurs de créer] (./media/data-lake-store-power-bi/create-pie-chart.png "Graphique en secteurs de créer")

    2. Les colonnes que nous allons utiliser sont la **colonne 4** (nom de la ville) et la **colonne 7** (nom du pays). Faites glisser ces colonnes à partir de l’onglet **champs** à l’onglet **visualisations** comme indiqué ci-dessous.

        ![Créer visualisations] (./media/data-lake-store-power-bi/create-visualizations.png "Créer visualisations")

    3. Le graphique à secteurs doit maintenant ressembler au similaire à celui illustré ci-dessous.

        ![Graphique en secteurs] (./media/data-lake-store-power-bi/pie-chart.png "Créer visualisations")

16. En sélectionnant un pays spécifique dans les filtres au niveau de la page, vous pouvez maintenant voir le nombre de pilotes dans chaque ville du pays sélectionné. Par exemple, sous l’onglet **visualisations** , sous **filtre de niveau de Page**, sélectionnez **Brésil**.

    ![Sélectionnez un pays] (./media/data-lake-store-power-bi/select-country.png "Sélectionnez un pays")

17. Le graphique à secteurs est automatiquement mis à jour pour les pilotes d’affichage dans les villes du Brésil.

    ![Pilotes dans un pays] (./media/data-lake-store-power-bi/driver-per-country.png "Pilotes par pays")

18. Dans le menu **fichier** , cliquez sur **Enregistrer** pour enregistrer la visualisation sous forme de fichier Bureau BI de puissance.

## <a name="publish-report-to-power-bi-service"></a>Publier le rapport au service d’alimentation BI

Une fois que vous avez créé les visualisations dans le bureau de BI d’alimentation, vous pouvez le partager avec d’autres personnes en le publiant sur le service alimentation BI. Pour obtenir des instructions sur la procédure à suivre, voir la [publication d’alimentation BI bureau](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Voir aussi

* [Analyser les données de magasin de données lac à l’aide de données lac Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
