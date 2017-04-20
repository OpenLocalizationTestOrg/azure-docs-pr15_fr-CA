<properties 
   pageTitle="Mise en route de la banque de données lac | Azure" 
   description="Le portail permet de créer un compte de banque de données LAC et effectuer des opérations de base du magasin de données lac" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Mise en route de magasin de LAC de données Azure via le portail Azure

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Apprenez à utiliser le portail Azure pour créer un compte Azure données lac banque et effectuer des opérations de base telles que la création de dossiers, charger et téléchargement des fichiers de données, supprimez votre compte, l’etc.. Pour plus d’informations sur la banque de données lac, consultez [Vue d’ensemble du magasin de LAC des données Azure](data-lake-store-overview.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

## <a name="do-you-learn-fast-with-videos"></a>Vous découvrez rapidement avec les vidéos ?

Regardez les vidéos suivantes pour démarrer avec le magasin de données lac.

* [Créer un compte de banque de données lac](https://mix.office.com/watch/1k1cycy4l4gen)
* [Gérer les données de magasin de données lac à l’aide de l’Explorateur de données](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte de banque de LAC de données Azure

1. Ouvrez une session sur le nouveau [Portail Azure](https://portal.azure.com).

2. Cliquez sur **Nouveau**et cliquez sur **données + de stockage**, puis cliquez sur **Magasin de LAC de données Azure**. Lire les informations de la lame de **Magasin de LAC de données Azure** , puis cliquez sur **créer** dans le coin inférieur gauche de la lame.

3. De la lame du **Nouveau magasin de LAC données** , fournir les valeurs comme indiqué dans la capture d’écran ci-dessous :

    ![Créer un nouveau compte de banque de LAC de données Azure] (./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Créer un nouveau compte du lac de données Azure")

    - **Abonnement**. Sélectionnez l’abonnement sous lequel vous souhaitez créer un nouveau compte de banque de données lac.
    - **Groupe de ressources**. Sélectionnez un groupe de ressources existant, ou cliquez sur **créer un groupe de ressources** pour en créer un. Un groupe de ressources est un conteneur qui conserve les ressources associées à une application. Pour plus d’informations, consultez [Les groupes de ressources dans Azure](azure-resource-manager/resource-group-overview.md#resource-groups).
    - **Emplacement**: sélectionnez l’emplacement où vous souhaitez créer le compte de la banque de données lac.

4. Sélectionnez le **code Pin à Startboard** si vous souhaitez que le compte de la banque de données lac soient accessibles à partir de la Startboard.

5. Cliquez sur **créer**. Si vous choisissez d’épingler le compte à la startboard, vous retournez à la startboard, et vous pouvez voir la progression de votre compte de banque de données lac mise en service. Une fois le compte de la banque de données lac est mis en service, la lame de compte s’affiche.

6. Développer l' **éducation** vers le bas pour voir les informations sur votre compte de banque de données lac telles que la ressource de groupe il soient une partie, de l’emplacement, etc.. Cliquez sur l’icône de **Démarrage rapide** pour afficher des liens vers d’autres ressources relatives au magasin de données lac.

    ![Compte de votre Boutique de LAC de données Azure] (./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Lac de données Azure votre compte")

## <a name="createfolder"></a>Créer des dossiers dans Azure données lac compte

Vous pouvez créer les dossiers sous votre compte de banque de données LAC pour gérer et stocker des données.

1. Ouvrez le magasin de données lac compte que vous venez de créer. Dans le volet gauche, cliquez sur **Parcourir**, cliquez sur la **Banque de données lac**et puis cliquez sur le nom du compte sous lequel vous souhaitez créer des dossiers à partir de la blade de magasin de données lac. Si vous épinglé le compte pour le startboard, cliquez sur mosaïque de ce compte.

2. Dans la blade de compte de banque de données lac, cliquez sur **Explorateur de données**.

    ![Créer des dossiers dans la banque de données lac compte] (./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Créer des dossiers dans la banque de données lac compte")

3. Dans la blade de compte de banque de données lac, cliquez sur **Nouveau dossier**, entrez un nom pour le nouveau dossier et puis cliquez sur **OK**.
    
    ![Créer des dossiers dans la banque de données lac compte] (./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Créer des dossiers dans la banque de données lac compte")
    
    Le nouveau dossier apparaît dans la blade **d’Explorateur de données** . Vous pouvez créer des dossiers imbriqués jusqu'à n’importe quel niveau.

    ![Création des dossiers de données lac compte] (./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Création des dossiers de données lac compte")


## <a name="uploaddata"></a>Télécharger les données vers le compte de la banque de LAC de données Azure

Vous pouvez télécharger vos données à un compte de banque de LAC de données Azure directement au niveau de la racine ou dans un dossier que vous avez créée dans le compte. Dans la capture d’écran ci-dessous, suivez les étapes pour télécharger un fichier vers un sous-dossier de la lame **d’Explorateur de données** . Dans cette capture d’écran, le fichier est téléchargé dans un sous-dossier indiqué dans les arborescences de navigation (marqués en rouge).

Si vous recherchez des exemples de données à télécharger, vous pouvez obtenir le dossier de **Données d’Ambulance** à partir du [Référentiel Git lac Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Téléchargement des données] (./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Téléchargement des données")


## <a name="properties"></a>Propriétés et actions disponibles dans les données stockées

Cliquez sur le fichier nouvellement ajouté pour ouvrir la lame de **Propriétés** . Les propriétés associées avec le fichier et les actions que vous pouvez effectuer sur le fichier sont disponibles dans cette blade. Vous pouvez également copier le chemin d’accès complet au fichier dans votre compte de banque de LAC de données Azure, mis en surbrillance dans la zone rouge dans la capture d’écran ci-dessous.

![Propriétés sur les données] (./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriétés sur les données")

* Cliquez sur **Aperçu** pour afficher un aperçu du fichier directement à partir du navigateur. Vous pouvez spécifier le format de l’aperçu ainsi. Cliquez sur **Aperçu**et cliquez sur **Format** de la lame **d’Aperçu du fichier** dans la **Format du fichier de prévisualisation** lame spécifier les options telles que le nombre de lignes à afficher, à l’encodage à utiliser, le séparateur à utiliser, etc..

  ![Format du fichier de prévisualisation] (./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Format du fichier de prévisualisation")

* Cliquez sur **Télécharger** pour télécharger le fichier sur votre ordinateur.

* Cliquez sur **Renommer le fichier** pour renommer le fichier.

* Cliquez sur **Supprimer les fichiers** pour supprimer le fichier.


## <a name="secure-your-data"></a>Sécurisation de vos données

Vous pouvez sécuriser les données stockées dans votre compte de banque de LAC de données Azure Azure Active Directory et de contrôle d’accès (ACL). Pour obtenir des instructions sur la façon de procéder, consultez [sécurisation des données dans le magasin de LAC de données Azure](data-lake-store-secure-data.md).


## <a name="delete-azure-data-lake-store-account"></a>Compte de supprimer le magasin lac données Azure

Pour supprimer un compte Azure Data Store de LAC, à partir de la blade de magasin de données lac, cliquez sur **Supprimer**. Pour confirmer l’action, vous serez invité à entrer le nom du compte que vous souhaitez supprimer. Entrez le nom du compte, puis cliquez sur **Supprimer**.

![Supprimer données lac compte] (./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Supprimer données lac compte")


## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans le magasin de données lac](data-lake-store-secure-data.md)
- [Utiliser des données Azure lac Analytique avec le magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilisez HDInsight Azure avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Journaux de diagnostic d’accès pour le magasin de données lac](data-lake-store-diagnostic-logs.md)
