<properties
   pageTitle="Mise en route de magasin de LAC de données à l’aide d’interface de ligne de commande multiplateforme | Microsoft Azure"
   description="Ligne de commande multiplateforme Azure permet de créer un compte de banque de données LAC et effectuer des opérations de base"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Mise en route de magasin de LAC de données Azure à l’aide de la ligne de commande Azure

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Découvrez comment utiliser Azure interface de ligne de commande pour créer un compte Azure données lac banque et effectuer des opérations de base telles que la création de dossiers, charger et téléchargement des fichiers de données, supprimez votre compte, l’etc.. Pour plus d’informations sur la banque de données lac, consultez [Vue d’ensemble de la banque données lac](data-lake-store-overview.md).

L’infrastructure du langage commun Azure est implémenté dans Node.js. Il peut être utilisé sur n’importe quelle plate-forme qui prend en charge les Node.js, y compris Windows, Mac et Linux. L’infrastructure du langage commun Azure est open source. Le code source est géré dans GitHub à <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Cet article traite uniquement à l’aide de la CLI Azure avec le magasin de données lac. Pour un guide général sur l’utilisation de CLI d’Azure, voir [comment utiliser la CLI Azure] [azure-command-line-tools].


##<a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI** - voir [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md) pour plus d’informations d’installation et de configuration. Assurez-vous que vous redémarrez votre ordinateur après l’installation de l’interface CLI.

## <a name="authentication"></a>Authentification

Cet article utilise une approche d’authentification plus simple avec magasin lac de données dans laquelle vous vous connectez en tant qu’utilisateur final. Le niveau d’accès au magasin de LAC données compte et système de fichiers est ensuite régi par le niveau d’accès de l’utilisateur connecté. Toutefois, il existe d’autres approches ainsi authentifier avec le magasin de données lac, qui sont **l’authentification de l’utilisateur final** ou **service-service**. Pour plus d’informations sur l’authentification et les instructions, voir [authentifier avec le magasin de données LAC avec Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

##<a name="login-to-your-azure-subscription"></a>Connexion à votre abonnement Azure

1. Suivez les étapes décrites dans la [connexion à un abonnement Azure à partir de l’Interface de ligne de commande Azure (CLI Azure)](../xplat-cli-connect.md) et de se connecter à votre abonnement à l’aide de la `azure login` méthode.

2. Les abonnements qui sont associés à votre compte à l’aide de la liste le `azure account list` commande.

        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false

    À partir de la sortie ci-dessus, **Azure-sub-1** est actuellement activé, et l’autre abonnement est **Azure-sub-2**. 

3. Sélectionnez l’abonnement que vous souhaitez utiliser sous. Si vous souhaitez travailler dans l’abonnement Azure-sub-2, utilisez le `azure account set` commande.

        azure account set Azure-sub-2


## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte de banque de LAC de données Azure

Ouvrez une invite de commande, shell ou une session Terminal Server et exécutez les commandes suivantes.

2. Passer en mode Azure le Gestionnaire de ressources à l’aide de la commande suivante :

        azure config mode arm


5. Créer un nouveau groupe de ressources. Dans la commande suivante, fournir les valeurs de paramètre que vous souhaitez utiliser.

        azure group create <resourceGroup> <location>

    Si le nom de l’emplacement contient des espaces, placez-le entre guillemets. Par exemple « Extrême-Orient US 2 ».

5. Créer le compte de la banque de données lac.

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Créer des dossiers dans votre magasin de LAC de données

Vous pouvez créer les dossiers sous votre compte Azure Data Store de LAC pour gérer et stocker des données. Utilisez la commande suivante pour créer un dossier appelé « MonNouveauDossier » à la racine du lac de magasin de données.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Par exemple :

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Télécharger les données vers votre magasin lac de données

Vous pouvez télécharger vos données au magasin de LAC des données directement au niveau de la racine ou dans un dossier que vous avez créée dans le compte. Les extraits de code ci-dessous montrent comment faire pour télécharger des exemples de données dans le dossier (**MonNouveauDossier**) que vous avez créé dans la section précédente.

Si vous recherchez des exemples de données à télécharger, vous pouvez obtenir le dossier de **Données d’Ambulance** à partir du [Référentiel Git lac Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Téléchargez le fichier et le stocker dans un répertoire local sur votre ordinateur, comme C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Par exemple :

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Liste des fichiers dans un magasin de données lac

Utilisez la commande suivante pour répertorier les fichiers dans un compte de banque de données lac.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Par exemple :

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

La sortie de ce doit être semblable au suivant :

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renommer, télécharger et supprimer des données de votre magasin de données lac

* **Pour renommer un fichier**, utilisez la commande suivante :

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    Par exemple :

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Pour télécharger un fichier**, utilisez la commande suivante. Assurez-vous que le chemin de destination que vous spécifiez déjà existe.

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    Par exemple :

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Pour supprimer un fichier**, utilisez la commande suivante :

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    Par exemple :

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    Lorsque vous y êtes invité, entrez **Y** pour supprimer l’élément.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Permet d’afficher la liste de contrôle d’accès d’un dossier dans le magasin de données lac

Utilisez la commande suivante pour afficher les listes ACL d’un dossier de la banque de données lac. Dans la version actuelle, les ACL peut être définie uniquement à la racine du lac de magasin de données. Par conséquent, le paramètre de chemin d’accès ci-dessous sera toujours la racine (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Par exemple :

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Supprimez votre compte de banque de données lac

Utilisez la commande suivante pour supprimer un compte de banque de données lac.

    azure datalake store account delete <dataLakeStoreAccountName>

Par exemple :

    azure datalake store account delete mynewdatalakestore

Lorsque vous y êtes invité, entrez **Y** pour supprimer le compte.


## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans le magasin de données lac](data-lake-store-secure-data.md)
- [Utiliser des données Azure lac Analytique avec le magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilisez HDInsight Azure avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md
