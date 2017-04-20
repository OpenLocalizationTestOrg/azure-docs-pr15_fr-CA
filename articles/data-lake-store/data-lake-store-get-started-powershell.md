<properties
   pageTitle="Mise en route de la banque de données lac | Azure"
   description="PowerShell d’Azure permet de créer un compte de banque de données LAC et effectuer des opérations de base"
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
   ms.date="10/04/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Mise en route de magasin de LAC de données Azure à l’aide de PowerShell d’Azure

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Découvrez comment utiliser PowerShell d’Azure pour créer un compte Azure données lac banque et effectuer des opérations de base telles que la création de dossiers, charger et téléchargement des fichiers de données, supprimez votre compte, l’etc.. Pour plus d’informations sur la banque de données lac, consultez [Vue d’ensemble de la banque données lac](data-lake-store-overview.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

* **Azure PowerShell 1.0 ou supérieure**. Voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## <a name="authentication"></a>Authentification

Cet article utilise une approche d’authentification plus simple avec les données de magasin de lac où vous êtes invité à entrer vos informations d’identification de compte Azure. Le niveau d’accès au magasin de LAC données compte et système de fichiers est ensuite régi par le niveau d’accès de l’utilisateur connecté. Toutefois, il existe d’autres approches ainsi authentifier avec le magasin de données lac, qui sont **l’authentification de l’utilisateur final** ou **service-service**. Pour plus d’informations sur l’authentification et les instructions, voir [authentifier avec le magasin de données LAC avec Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte de banque de LAC de données Azure

1. À partir de votre bureau, ouvrir une nouvelle fenêtre de Windows PowerShell et entrez l’extrait suivant pour vous connecter à votre compte Azure, définir l’abonnement et enregistrer le fournisseur de magasin de données lac. Lorsque vous êtes invité à ouvrir une session, vérifiez que vous vous connectez sous la forme d’un abonnement admininistrators/du propriétaire de la :

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. Un compte Azure données lac banque est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Créer un groupe de ressources Azure] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Créer un groupe de ressources Azure")

2. Créez un compte de banque de LAC de données Azure. Le nom que vous spécifiez doit uniquement contenir des minuscules et chiffres.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Création d’un compte de banque de LAC de données Azure] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Création d’un compte de banque de LAC de données Azure")

3. Vérifiez que le compte est créé avec succès.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    La sortie de cette doit être **True**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Créer des structures de répertoires dans votre magasin de LAC de données Azure

Vous pouvez créer des répertoires sous votre compte Azure Data Store de LAC pour gérer et stocker des données.

1. Spécifiez un répertoire racine.

        $myrootdir = "/"

2. Créer un nouveau répertoire nommé **mynewdirectory** sous la racine spécifiée.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Vérifiez que le nouveau répertoire est créé avec succès.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Il doit afficher une sortie semblable à la suivante :

    ![Vérifiez le répertoire] (./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Vérifiez le répertoire")


## <a name="upload-data-to-your-azure-data-lake-store"></a>Télécharger les données vers votre Boutique de LAC de données Azure

Vous pouvez télécharger vos données au magasin de LAC des données directement au niveau de la racine ou dans un répertoire que vous avez créée dans le compte. Les extraits de code ci-dessous montrent comment faire pour télécharger des exemples de données dans le répertoire (**mynewdirectory**) que vous avez créé dans la section précédente.

Si vous recherchez des exemples de données à télécharger, vous pouvez obtenir le dossier de **Données d’Ambulance** à partir du [Référentiel Git lac Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Téléchargez le fichier et le stocker dans un répertoire local sur votre ordinateur, comme C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renommer, télécharger et supprimer des données de votre magasin de données lac

Pour renommer un fichier, utilisez la commande suivante :

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Pour télécharger un fichier, utilisez la commande suivante :

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Pour supprimer un fichier, utilisez la commande suivante :

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Lorsque vous y êtes invité, entrez **Y** pour supprimer l’élément. Si vous avez plus d’un fichier à supprimer, vous pouvez fournir tous les chemins d’accès séparés par des virgules.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Supprimer votre compte Azure Data Store de LAC

Utilisez la commande suivante pour supprimer votre compte de banque de données lac.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Lorsque vous y êtes invité, entrez **Y** pour supprimer le compte.


## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans le magasin de données lac](data-lake-store-secure-data.md)
- [Utiliser des données Azure lac Analytique avec le magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilisez HDInsight Azure avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)
