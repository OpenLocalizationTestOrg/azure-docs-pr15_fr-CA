<properties 
    pageTitle="Utiliser l’émulateur de stockage Azure pour le développement et le test | Microsoft Azure" 
    description="L’émulateur de stockage Azure offre un environnement de développement local gratuit pour le développement et le test sur le stockage Azure. Obtenir des informations sur l’émulateur de stockage, y compris la façon dont les demandes sont authentifiées, comment se connecter à l’émulateur à partir de votre application et comment utiliser l’outil de ligne de commande." 
    services="storage" 
    documentationCenter="" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>
<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="tamram"/>

# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Utiliser l’émulateur de stockage Azure pour le développement et le test

## <a name="overview"></a>Vue d’ensemble

L’émulateur de stockage Microsoft Azure fournit un environnement qui émule les services Azure Blob, file d’attente et Table à des fins de développement local. À l’aide de l’émulateur de stockage, vous pouvez tester votre application avec les services de stockage localement, sans encourir les coûts ou de création d’un abonnement Azure. Lorsque vous êtes satisfait du fonctionne de votre application dans l’émulateur, vous pouvez passer à l’aide d’un compte de stockage Azure dans le nuage.

> [AZURE.NOTE] L’émulateur de stockage est disponible dans le cadre du [Kit de développement logiciel de Microsoft Azure](https://azure.microsoft.com/downloads/). Vous pouvez également installer l’émulateur de stockage à l’aide du [programme d’installation autonome](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409). Pour configurer l’émulateur de stockage, vous devez disposer des privilèges d’administrateur sur l’ordinateur.
> 
> Actuellement, l’émulateur de stockage s’exécute uniquement sous Windows.
>  
> Notez que les données créées dans une version de l’émulateur de stockage ne sont pas garantie être accessible lorsque vous utilisez une version différente. Si vous avez besoin conserver vos données sur le long terme, il est recommandé que vous stockez ces données dans un compte de stockage Azure, plutôt que dans l’émulateur de stockage.

## <a name="how-the-storage-emulator-works"></a>Mode de fonctionnement de l’émulateur de stockage
 
L’émulateur de stockage utilise une instance locale de Microsoft SQL Server et le système de fichiers local pour émuler les services de stockage Azure. Par défaut, l’émulateur de stockage utilise une base de données dans Microsoft SQL Server 2012 Express LocalDB.  Vous pouvez choisir de configurer l’émulateur de stockage pour accéder à une instance locale de SQL Server au lieu de l’instance LocalDB. Pour plus d’informations, reportez-vous à la section [démarrage et initialisation de l’émulateur de stockage](#start-and-initialize-the-storage-emulator) ci-dessous.

Vous pouvez installer SQL Server Management Studio Express pour gérer votre installation LocalDB. L’émulateur de stockage se connecte à SQL Server ou LocalDB à l’aide de l’authentification Windows. 

Il existe quelques différences de fonctionnalités entre l’émulateur de stockage et les services de stockage Azure. Pour plus d’informations sur ces différences, consultez [différences entre l’émulateur de stockage et le stockage Azure](#differences-between-the-storage-emulator-and-azure-storage).

## <a name="authenticating-requests-against-the-storage-emulator"></a>L’authentification des demandes par rapport à l’émulateur de stockage

Tout comme avec le stockage Azure dans le nuage, toutes les requêtes que vous effectuez par rapport à l’émulateur de stockage doivent être authentifié, sauf s’il est une demande anonyme. Vous pouvez authentifier les demandes sur l’émulateur de stockage à l’aide de l’authentification de clé partagée ou avec une signature d’accès partagé (SAS).

### <a name="authentication-with-shared-key-credentials"></a>Authentification avec les informations d’identification de clé partagée

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Pour plus d’informations sur les chaînes de connexion, consultez [Chaînes de connexion de stockage Azure configurer](storage-configure-connection-string.md). 

### <a name="authentication-with-a-shared-access-signature"></a>Authentification avec une signature d’accès partagé 

Certaines bibliothèques de client de stockage Azure, telles que la bibliothèque Xamarin, prennent uniquement en charge l’authentification avec un jeton de signature (SAS) d’un accès partagé. Vous devez créer ce jeton SAS à l’aide d’un outil ou une application prenant en charge l’authentification de clé partagée. Un moyen facile de générer le jeton SAS est via PowerShell d’Azure :

1. Installer PowerShell d’Azure si vous ne l’avez pas déjà. À l’aide de la version la plus récente des applets de commande PowerShell de Azure est recommandé. Pour les instructions d’installation, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md#Install) .

2. Ouvrez Azure PowerShell et exécutez les commandes suivantes. N’oubliez pas de remplacer *ACCOUNT_NAME* et *ACCOUNT_KEY ==* avec vos propres informations d’identification. Remplacez *CONTAINER_NAME* par le nom de votre choix.

        $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
        
        New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
        
        $now = Get-Date 
        
        New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

La signature de l’accès partagé URI qui en résulte pour le nouveau conteneur doit être semblable au suivant :

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

La signature d’accès partagé créée à l’aide de cet exemple n’est valide pour une journée. La signature accorde un accès complet (lecture, écriture, suppression, liste) pour les objets BLOB dans le conteneur.

Pour plus d’informations sur les signatures d’accès partagé, consultez [L’aide partagés accès Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md).


## <a name="start-and-initialize-the-storage-emulator"></a>Démarrer et initialiser l’émulateur de stockage

Pour démarrer l’émulateur de stockage Azure, cliquez sur le bouton Démarrer ou appuyez sur la touche Windows. Commencez à taper **l’Émulateur de stockage Azure**et sélectionnez l’émulateur dans la liste des applications. 

Lorsque l’émulateur est en cours d’exécution, vous verrez une icône dans la zone de notification de la barre des tâches Windows.

Lors du démarrage de l’émulateur de stockage, une fenêtre de ligne de commande s’affiche. Vous pouvez utiliser cette fenêtre de ligne de commande pour démarrer et arrêter l’émulateur de stockage ainsi qu’effacer les données, d’obtenir l’état actuel et initialiser l’émulateur. Pour plus d’informations, consultez [Référence d’outils de stockage émulateur de ligne de commande](#storage-emulator-command-line-tool-reference).

Lors de la fermeture de la fenêtre de ligne de commande, l’émulateur de stockage continue de s’exécuter. Pour afficher à nouveau la ligne de commande, suivez les étapes ci-dessus, comme si le démarrage de l’émulateur de stockage.

La première fois que vous exécutez l’émulateur de stockage, l’environnement de stockage local est initialisée pour vous. Le processus d’initialisation crée une base de données dans LocalDB et réserve les ports HTTP pour chaque service de stockage local. 

L’émulateur de stockage est installé par défaut dans le répertoire d’émulateur C:\Program Files (x86) \Microsoft SDKs\Azure\Storage. 

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initialiser l’émulateur de stockage pour utiliser une autre base de données SQL

Vous pouvez utiliser l’outil de ligne de commande de stockage émulateur pour initialiser l’émulateur de stockage pour pointer vers une instance de base de données SQL autre que l’instance de LocalDB par défaut. Notez que vous devez exécuter l’outil de ligne de commande avec des privilèges d’administrateur pour initialiser la base de données back-end pour l’émulateur de stockage :

1. Cliquez sur le bouton **Démarrer** ou appuyez sur la touche **Windows** . Commencez à taper `Azure Storage Emulator` et sélectionnez-le lorsqu’il apparaît pour afficher l’outil de ligne de commande de stockage émulateur.
2. Dans la fenêtre d’invite de commandes, tapez la commande suivante, où `<SQLServerInstance>` est le nom de l’instance de SQL Server. Pour utiliser LocalDb, spécifiez `(localdb)\v11.0` que l’instance de SQL Server.

        AzureStorageEmulator init /server <SQLServerInstance> 
    
    Vous pouvez également utiliser la commande suivante, qui dirige l’émulateur pour utiliser l’instance de SQL Server par défaut :

        AzureStorageEmulator init /server .\\ 

    Ou bien, vous pouvez utiliser la commande suivante, ce qui réinitialise la base de données à l’instance de LocalDB par défaut :

        AzureStorageEmulator init /forceCreate 

Pour plus d’informations sur ces commandes, consultez la [Référence d’outils de stockage émulateur de ligne de commande](#storage-emulator-command-line-tool-reference).

## <a name="addressing-resources-in-the-storage-emulator"></a>Adressage des ressources dans l’émulateur de stockage

Les points de terminaison de service pour l’émulateur de stockage sont différents de ceux d’un compte de stockage Azure. La différence est due au fait que l’ordinateur local n’exécute pas la résolution de noms de domaine, les points de terminaison d’émulateur de stockage requiert une adresse locale plutôt que d’un nom de domaine.

Lorsque vous adressez une ressource dans un compte de stockage Azure, vous utilisez le schéma suivant, où le nom de compte fait partie du nom d’hôte de l’URI, et la ressource est résolue fait partie du chemin d’accès de l’URI :

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Par exemple, l’URI suivant est une adresse valide pour un objet blob dans un compte de stockage Azure :

    https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

Dans l’émulateur de stockage, car l’ordinateur local n’exécute pas de résolution de noms de domaine, le nom de compte fait partie du chemin d’accès URI plutôt que le nom d’hôte. Vous utilisez le schéma d’une ressource en cours d’exécution dans l’émulateur de stockage :

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Par exemple, l’adresse suivante peut être utilisée pour accéder à un objet blob dans l’émulateur de stockage :

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Les points de terminaison de service pour l’émulateur de stockage sont les suivantes :

    Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
    Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
    Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Le compte secondaire avec RA-GRS d’adressage

À partir de la version 3.1, le compte de l’émulateur de stockage prend en charge la réplication redondante géo-accès en lecture (RA-GRS). Pour les ressources de stockage dans le nuage et l’émulateur local, vous pouvez accéder à l’emplacement secondaire en ajoutant - secondaire au nom de compte. Par exemple, l’adresse suivante peut être utilisée pour accéder à un objet blob à l’aide de la secondaire en lecture seule dans l’émulateur de stockage :

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Pour accéder par programme à l’image secondaire avec l’émulateur de stockage, utilisez la bibliothèque de Client de stockage pour .NET version 3.2 ou ultérieure. Consultez la [Bibliothèque de Client Microsoft Azure stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) pour plus de détails.

## <a name="storage-emulator-command-line-tool-reference"></a>Référence d’outils de ligne de commande d’émulateur de stockage

À partir de la version 3.0, lorsque vous lancez l’émulateur de stockage, vous verrez une fenêtre de ligne de commande contextuelle. Utilisez la fenêtre de ligne de commande pour démarrer et arrêter l’émulateur ainsi que pour l’état de la requête et effectuer d’autres opérations.

> [AZURE.NOTE] Si vous disposez du Microsoft Azure compute émulateur installé, une icône de la barre d’état système s’affiche lorsque vous lancez l’émulateur de stockage. Avec le bouton droit sur l’icône pour afficher un menu, ce qui fournit un moyen graphique de démarrer et d’arrêter l’émulateur de stockage.

### <a name="command-line-syntax"></a>Syntaxe de ligne de commande

    AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### <a name="options"></a>Options

Pour afficher la liste des options, tapez `/help` à l’invite de commande.

| Option | Description                                                    | Commande                                                                                                 | Arguments                                                                                                         |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Début**  | Démarrage de l’émulateur de stockage.                                | `AzureStorageEmulator start [-inprocess]`                                                                    | *-inprocess*: démarrer l’émulateur dans le processus actuel, plutôt que de créer un nouveau processus.                          |
| **Arrêter**   | Arrête l’émulateur de stockage.                                    | `AzureStorageEmulator stop`                                                                                  |                                                                                                                   |
| **État** | Imprime l’état de l’émulateur de stockage.                     | `AzureStorageEmulator status`                                                                                |                                                                                                                   |
| **Effacer**  | Supprime les données de tous les services spécifiés sur la ligne de commande. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *BLOB*: efface blob de données. <br/>*file d’attente*: efface les données de file d’attente. <br/>*tableau*: efface les données de la table. <br/>*tous les*: efface toutes les données de tous les services. |
| **Init**   | Effectue l’initialisation unique pour configurer l’émulateur.       | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-serveur nom_serveur\nom_instance*: Spécifie le serveur qui héberge l’instance SQL. <br/>*sqlinstance - instanceName*: Spécifie le nom de l’instance SQL à utiliser dans l’instance de serveur par défaut. <br/>*-forcecreate*: force la création de la base de données SQL, même si elle existe déjà. <br/>*-inprocess*: exécute l’initialisation du processus en cours au lieu de générer un nouveau processus. Vous devez lancer le processus en cours avec des autorisations élevées pour exécuter l’initialisation.          |
                                                                                                                  
## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Différences entre l’émulateur de stockage et le stockage Azure

L’émulateur de stockage étant un environnement émulé en cours d’exécution dans une instance locale de SQL, il existe quelques différences de fonctionnalités entre l’émulateur et un compte de stockage Azure dans le nuage :

- L’émulateur de stockage prend en charge seulement un seul compte fixe et une clé d’authentification bien connus.

- L’émulateur de stockage n’est pas un service de stockage évolutif et ne prendra pas en charge un grand nombre de clients simultanés.

- Comme indiqué dans [l’adressage des ressources dans l’émulateur de stockage](#addressing-resources-in-the-storage-emulator), les ressources sont traitées différemment dans l’émulateur de stockage par rapport à un compte de stockage Azure. Cette différence est due au fait que résolution de nom de domaine est disponible dans le nuage, mais pas sur l’ordinateur local.

- À partir de la version 3.1, le compte de l’émulateur de stockage prend en charge la réplication redondante géo-accès en lecture (RA-GRS). Dans l’émulateur, tous les comptes ont RA-GRS activée et n’est jamais tout retard entre les réplicas principales et secondaires. Les opérations d’obtenir des statistiques de Service Blob, obtenir les statistiques de file d’attente de Service et obtenir des statistiques de Service Table sont pris en charge sur le compte secondaire et renvoie toujours la valeur de la `LastSyncTime` élément de réponse en tant que l’heure actuelle, en fonction de la base de données SQL sous-jacente.

    Pour accéder par programme à l’image secondaire avec l’émulateur de stockage, utilisez la bibliothèque de Client de stockage pour .NET version 3.2 ou ultérieure. Consultez la [Bibliothèque de Client Microsoft Azure stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) pour plus de détails.

- Le service de fichiers et les points de terminaison service de protocole SMB ne sont actuellement pas pris en charge dans l’émulateur de stockage.

- L’émulateur de stockage renvoie une erreur VersionNotSupportedByEmulator (code d’état HTTP 400 - Requête incorrecte) si vous utilisez une version des services de stockage qui n’est pas encore pris en charge par la version de l’émulateur que vous utilisez.

### <a name="differences-for-blob-storage"></a>Différences pour le stockage des objets Blob 

Les différences suivantes s’appliquent au stockage de Blob dans l’émulateur :

- Le blob de prend en charge uniquement de stockage émulateur tailles jusqu'à 2 Go.

- Une opération de placer un Blob peut réussir contre un blob qui existe dans l’émulateur de stockage et a un bail actif, même si l’ID de location n’a pas été spécifié dans le cadre de la demande. 

- Ajouter un Blob opérations ne sont pas pris en charge par l’émulateur. Tente une opération sur un blob append renvoie une erreur FeatureNotSupportedByEmulator (code d’état HTTP 400 - Requête incorrecte).

### <a name="differences-for-table-storage"></a>Différences pour le stockage de Table 

Les différences suivantes s’appliquent au stockage de Table dans l’émulateur :

- Propriétés de date dans le service de la Table dans l’émulateur de stockage prend en charge uniquement la plage prise en charge par SQL Server 2005 (*c'est-à-dire*, elles doivent être postérieure au 1 er janvier 1753). Toutes les dates antérieures au 1 er janvier 1753 sont modifiées pour cette valeur. La précision des dates est limitée à la précision de SQL Server 2005, ce qui signifie que les dates sont précises à 1/300th de seconde.

- L’émulateur de stockage prend en charge les valeurs de propriété de clé de ligne et de la clé de partition de moins de 512 octets chacune. En outre, la taille totale du nom de compte, le nom de table et l’ensemble des noms de propriété de clé ne peut pas dépasser 900 octets.

- La taille totale d’une ligne d’une table dans l’émulateur de stockage est limitée à moins de 1 Mo.

- Dans l’émulateur de stockage, les propriétés des données de type `Edm.Guid` ou `Edm.Binary` prennent uniquement en charge la `Equal (eq)` et `NotEqual (ne)` opérateurs de comparaison dans la requête filtre les chaînes.

### <a name="differences-for-queue-storage"></a>Différences pour le stockage de la file d’attente

Il n’existe aucune différence spécifique au stockage de file d’attente dans l’émulateur.

## <a name="storage-emulator-release-notes"></a>Notes de mise à jour de stockage émulateur

### <a name="version-45"></a>Version 4.5

- Corrige un bogue qui a provoqué l’initialisation et l’installation de l’émulateur de stockage échoue lorsque la base de données de sauvegarde a été renommé.

### <a name="version-44"></a>Version 4.4

- L’émulateur de stockage prend désormais en charge la version 2015-12-11, les services de stockage sur les points de terminaison de service Blob et Table file d’attente.

- Nettoyage de l’émulateur stockage de données blob est désormais plus efficace lorsque vous travaillez avec un grand nombre d’objets BLOB.

- Corrige un bogue qui a provoqué le conteneur XML ACL à valider dans le service de stockage en quoi il diffère légèrement.

- Corrige un bogue qui entraînait parfois max et min les valeurs DateTime doivent être déclarées dans le fuseau horaire incorrect.

### <a name="version-43"></a>Version 4.3

- L’émulateur de stockage prend désormais en charge la version 2015-08-07, les services de stockage sur les points de terminaison de service Blob et Table file d’attente.

### <a name="version-42"></a>Version 4.2

- L’émulateur de stockage prend désormais en charge la version 2015-04-05 des services de stockage sur les points de terminaison de service Blob et Table file d’attente.

### <a name="version-41"></a>Version 4.1

- L’émulateur de stockage prend désormais en charge la version 2015-02-21, les services de stockage Blob et Table file d’attente de points de terminaison service, à l’exception des nouvelles fonctionnalités ajouter un Blob. 

- L’émulateur de stockage renvoie désormais un message d’erreur significatif si vous utilisez une version des services de stockage qui n’est pas encore pris en charge par cette version de l’émulateur. Nous vous recommandons d’utiliser la dernière version de l’émulateur. Si vous rencontrez une erreur VersionNotSupportedByEmulator (code d’état HTTP 400 - Requête incorrecte), téléchargez la dernière version de l’émulateur de stockage.

- Un bogue dans lequel un concurrence causé table entité données fixes sont incorrects lors des opérations de fusion simultanés.

### <a name="version-40"></a>Version 4.0

- L’émulateur de stockage exécutable a été renommé *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Version 3.2

- L’émulateur de stockage prend désormais en charge la version 14-02-2014, les services de stockage sur les points de terminaison de service Blob et Table file d’attente. Notez que les points de terminaison de service fichier ne sont pas actuellement gérées dans l’émulateur de stockage. Pour plus d’informations sur la version 14-02-2014, consultez [Versioning pour les Services de stockage Azure](https://msdn.microsoft.com/library/azure/dd894041.aspx) .

### <a name="version-31"></a>Version 3.1

- Accès en lecture de stockage redondant geo (RA-GRS) est maintenant pris en charge dans l’émulateur de stockage. L’obtenir des statistiques de Service Blob, obtenir les statistiques de file d’attente Service API obtenir des statistiques du Service de Table sont pris en charge pour le compte secondaire et retournera toujours la valeur de l’élément de réponse LastSyncTime comme l’heure actuelle, en fonction de la base de données SQL sous-jacente. Pour accéder par programme à l’image secondaire avec l’émulateur de stockage, utilisez la bibliothèque de Client de stockage pour .NET version 3.2 ou ultérieure. Pour plus d’informations, consultez la bibliothèque Client de stockage Microsoft Azure pour référence .NET.

### <a name="version-30"></a>Version 3.0

- L’émulateur de stockage Azure n’est plus fourni dans le même package que l’émulateur de calcul.

- L’interface utilisateur graphique d’émulateur stockage est désapprouvé en faveur d’une interface de ligne de commande scriptable. Pour plus d’informations sur l’interface de ligne de commande, consultez la référence d’outils de stockage émulateur de ligne de commande. L’interface graphique sera toujours présent dans la version 3.0, mais elle ne sont accessibles lors de l’installation de l’émulateur de calcul en cliquant sur l’icône de la barre d’état système et en sélectionnant Afficher stockage d’émulateur de l’interface utilisateur.

- Version 2013-08-15, les services de stockage Azure est maintenant entièrement pris en charge. (Déjà cette version était uniquement pris en charge par l’émulateur de stockage version 2.2.1 Aperçu.)
