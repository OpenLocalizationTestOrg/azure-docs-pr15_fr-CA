<properties
    pageTitle="Copier ou déplacer des données vers un stockage avec AzCopy | Microsoft Azure"
    description="Utilisez l’utilitaire AzCopy pour déplacer ou copier des données vers ou à partir de blob, table et fichier de contenu. Copier des données vers le stockage Azure à partir de fichiers locaux ou copier des données dans ou entre des comptes de stockage. Facilement migrer vos données vers le stockage Azure."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="micurd"/>

# <a name="transfer-data-with-the-azcopy-command-line-utility"></a>Transférer des données avec l’utilitaire de ligne de commande AzCopy

## <a name="overview"></a>Vue d’ensemble

AzCopy est un utilitaire de ligne de commande Windows conçu pour copier les données vers et depuis le stockage Blob Azure de Microsoft, fichier et Table à l’aide de commandes simples avec des performances optimales. Vous pouvez copier des données d’un objet à un autre dans votre compte de stockage, ou entre les comptes de stockage.

> [AZURE.NOTE] Ce guide suppose que vous êtes déjà familiarisé avec [Le stockage Azure](https://azure.microsoft.com/services/storage/). Si ce n’est pas le cas, la lecture de la documentation [d’Introduction au stockage Azure](storage-introduction.md) peut vous être utile. Plus important encore, vous devrez [créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) afin de démarrer à l’aide de AzCopy.

## <a name="download-and-install-azcopy"></a>Téléchargez et installez AzCopy

### <a name="windows"></a>Windows

Télécharger la [version la plus récente de AzCopy](http://aka.ms/downloadazcopy).

### <a name="maclinux"></a>Mac/Linux

AzCopy n’est pas disponible pour les systèmes d’exploitation Mac/Linux. Toutefois, CLI d’Azure constitue une solution pour copier les données vers et depuis le stockage Azure. Lire [à l’aide de la CLI Azure avec stockage Azure](storage-azure-cli.md) pour en savoir plus.

## <a name="writing-your-first-azcopy-command"></a>Écriture de votre première commande de AzCopy

La syntaxe de base pour les commandes de AzCopy est :

    AzCopy /Source:<source> /Dest:<destination> [Options]

Ouvrez une fenêtre de commande et accédez au répertoire d’installation sur votre ordinateur - AzCopy où les `AzCopy.exe` exécutable se trouve. Si vous le souhaitez, vous pouvez ajouter l’emplacement d’installation de AzCopy à votre chemin d’accès système. Par défaut, AzCopy est installé dans `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` ou `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

Les exemples suivants illustrent différents scénarios pour copier des données vers et à partir de Microsoft Azure BLOB, les fichiers et les Tables. Reportez-vous à la section [Paramètres de AzCopy](#azcopy-parameters) pour une explication détaillée des paramètres utilisés dans chaque échantillon.

## <a name="blob-download"></a>BLOB : télécharger

### <a name="download-single-blob"></a>Télécharger le blob unique

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

Notez que si le dossier `C:\myfolder` n’existe pas, crée et télécharger AzCopy `abc.txt ` dans le nouveau dossier.

### <a name="download-single-blob-from-secondary-region"></a>Télécharger les blob unique à partir de la zone secondaire

    AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Notez que vous devez avoir accès en lecture de stockage redondant geo activé.

### <a name="download-all-blobs"></a>Télécharger tous les BLOB

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Supposons que les objets BLOB suivantes se trouvent dans le conteneur spécifié :  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Après l’opération de téléchargement, le répertoire `C:\myfolder` inclut les fichiers suivants :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Si vous ne spécifiez pas l’option `/S`, aucun BLOB n’est téléchargés.

### <a name="download-blobs-with-specified-prefix"></a>Télécharger des objets BLOB avec le préfixe spécifié

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Supposons que les objets BLOB suivantes se trouvent dans le conteneur spécifié. Tous les BLOB commencent par le préfixe `a` seront téléchargés :

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Après l’opération de téléchargement, le dossier `C:\myfolder` inclut les fichiers suivants :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Le préfixe s’applique pour le répertoire virtuel, ce qui constitue la première partie du nom de l’objet blob. Dans l’exemple ci-dessus, le répertoire virtuel ne correspond pas au préfixe spécifié, afin qu’il n’est pas téléchargé. En outre, si l’option `\S` n’est pas spécifié, AzCopy ne télécharge pas les objets BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Définir l’heure de dernière modification des fichiers exportés pour être égal au BLOB de la source

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

Vous pouvez également exclure des objets BLOB de l’opération de téléchargement en fonction de leur heure de dernière modification. Par exemple, si vous souhaitez exclure les objets BLOB dont la dernière date de modification est identique ou ultérieure à celle du fichier de destination, ajoutez le `/XN` option :

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

Ou si vous souhaitez exclure les objets BLOB dont la dernière date de modification est la même ou plus anciens que le fichier de destination, ajoutez le `/XO` option :

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## <a name="blob-upload"></a>BLOB : télécharger

### <a name="upload-single-file"></a>Télécharger fichier unique

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

Si le conteneur de destination spécifié n’existe pas, AzCopy crée et télécharger le fichier dedans.

### <a name="upload-single-file-to-virtual-directory"></a>Télécharger fichier unique pour le répertoire virtuel

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Si le répertoire virtuel spécifié n’existe pas, AzCopy pour télécharger le fichier pour inclure le répertoire virtuel en son nom (*par exemple*, `vd/abc.txt` dans l’exemple ci-dessus).

### <a name="upload-all-files"></a>Télécharger tous les fichiers

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

L’option `/S` télécharge le contenu du répertoire spécifié de manière récursive le stockage Blob, c'est-à-dire que tous les sous-dossiers et les fichiers seront téléchargés ainsi. Par exemple, supposons que les fichiers suivants se trouvent dans le dossier `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Après l’opération de téléchargement, le conteneur comprend les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Si vous ne spécifiez pas l’option `/S`, AzCopy pas télécharger de manière récursive. Après l’opération de téléchargement, le conteneur comprend les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-files-matching-specified-pattern"></a>Télécharger les fichiers correspondant au modèle spécifié

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Supposons que les fichiers suivants se trouvent dans le dossier `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Après l’opération de téléchargement, le conteneur comprend les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Si vous ne spécifiez pas l’option `/S`, AzCopy pour télécharger uniquement les objets BLOB qui ne se trouve dans un répertoire virtuel :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Spécifiez le type de contenu MIME d’un objet blob de destination

Par défaut, AzCopy définit le type de contenu d’un objet blob de destination à `application/octet-stream`. Depuis la version 3.1.0, vous pouvez spécifier explicitement le type de contenu via l’option `/SetContentType:[content-type]`. Cette syntaxe définit le type de contenu pour tous les BLOB dans une opération de téléchargement.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Si vous spécifiez `/SetContentType` sans valeur, puis AzCopy définit chaque objet blob ou type de contenu du fichier en fonction de son extension de fichier.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## <a name="blob-copy"></a>BLOB : copie

### <a name="copy-single-blob-within-storage-account"></a>Copier un blob unique dans un compte de stockage

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

Lorsque vous copiez un objet blob dans un compte de stockage, une opération de [copie du côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est effectuée.

### <a name="copy-single-blob-across-storage-accounts"></a>Blob unique de copie entre les comptes de stockage

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Lorsque vous copiez un objet blob sur des comptes de stockage, une opération de [copie du côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est effectuée.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copier les blob unique à partir de la zone secondaire à une zone primaire

    AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Notez que vous devez avoir accès en lecture de stockage redondant geo activé.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copier les blob unique et ses instantanés entre les comptes de stockage

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Après l’opération de copie, le conteneur cible comprend le blob et ses instantanés. En supposant que le blob dans l’exemple ci-dessus a deux instantanés, le conteneur inclura les blob et les snapshots suivants :

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Copier synchrone des objets BLOB entre les comptes de stockage

AzCopy par défaut copie les données entre deux points de terminaison de stockage de façon asynchrone. Par conséquent, l’opération de copie s’exécute l’arrière-plan à l’aide de la capacité de la bande passante qui ne possède aucun SLA en termes de façon rapide un blob est copié et AzCopy vérifie périodiquement l’état de la copie jusqu'à ce que la copie est terminée ou a échoué.

Le `/SyncCopy` option permet de s’assurer que l’opération de copie obtiendra une vitesse constante. AzCopy effectue la copie synchrone en téléchargeant les blobs pour copier à partir de la source spécifiée dans la mémoire locale et puis en les téléchargeant vers la destination de stockage Blob.

    AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy`peut générer des coûts de sortie supplémentaires par rapport à la copie asynchrone, l’approche recommandée consiste à utiliser cette option dans un ordinateur virtuel Azure qui se trouve dans la même région que votre compte de stockage source afin d’éviter des coûts de sortie.

## <a name="file-download"></a>Fichier : téléchargement

### <a name="download-single-file"></a>Téléchargez le fichier unique

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Si la source spécifiée est un partage de fichier Azure, puis vous devez spécifier le nom de fichier exact (*par exemple,* `abc.txt`) pour télécharger un fichier unique ou pour spécifier l’option `/S` pour télécharger tous les fichiers dans le partage de manière récursive. Tentative de spécifier à la fois un modèle de fichier et l’option `/S` ensemble provoquera une erreur.

### <a name="download-all-files"></a>Télécharger tous les fichiers

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Notez que les dossiers vides ne seront pas téléchargés.

## <a name="file-upload"></a>Fichier : téléchargement

### <a name="upload-single-file"></a>Télécharger fichier unique

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### <a name="upload-all-files"></a>Télécharger tous les fichiers

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Notez que les dossiers vides ne sont pas téléchargées.

### <a name="upload-files-matching-specified-pattern"></a>Télécharger les fichiers correspondant au modèle spécifié

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <a name="file-copy"></a>Fichier : copie

### <a name="copy-across-file-shares"></a>Copier dans des partages de fichiers

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### <a name="copy-from-file-share-to-blob"></a>Copier à partir du partage de fichiers pour les blob

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Notez que la copie asynchrone à partir du fichier de stockage de Blob de Page n’est pas pris en charge.

### <a name="copy-from-blob-to-file-share"></a>Copier à partir de l’objet blob de partage de fichiers

    AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### <a name="synchronously-copy-files"></a>Copier les fichiers de façon synchrone

Vous pouvez spécifier le `/SyncCopy` option pour copier les données du stockage de fichier pour le stockage de fichiers, du stockage de fichier pour le stockage des objets Blob et depuis le stockage Blob pour le stockage de fichiers synchrone, AzCopy effectue cette opération en téléchargeant la source de données vers la mémoire locale et de le télécharger à nouveau vers la destination.

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Lors de la copie du fichier de stockage pour le stockage des objets Blob, le type de blob par défaut est le blob de bloc, utilisateur peut spécifier l’option `/BlobType:page` pour modifier le type de blob de destination.

Notez que `/SyncCopy` peut générer des coûts comparant à une copie asynchrone de sortie supplémentaire, l’approche recommandée consiste à utiliser cette option dans la machine virtuelle de Azure qui se trouve dans la même région que votre compte de stockage source afin d’éviter des coûts de sortie.

## <a name="table-export"></a>Tableau : exportation

### <a name="export-table"></a>Table d’exportation

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy écrit un fichier manifeste dans le dossier de destination spécifié. Le fichier manifest est utilisé dans le processus d’importation pour localiser les fichiers de données nécessaires et d’effectuer une validation de données. Le fichier manifest utilise la convention d’affectation de noms suivante par défaut :

    <account name>_<table name>_<timestamp>.manifest

Utilisateur peut également spécifier l’option `/Manifest:<manifest file name>` pour définir le nom du fichier manifeste.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### <a name="split-export-into-multiple-files"></a>Exportation de fractionnement en plusieurs fichiers

    AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy utilise un *indice de volume* dans les noms de fichiers de données de fractionnement pour distinguer plusieurs fichiers. L’indice de volume se compose de deux parties, un *index de clé plage de partition* et *Fractionner le fichier d’index*. Les deux index est de base zéro.

L’index de clé plage de partition est égal à 0 si l’utilisateur ne spécifie pas l’option `/PKRS`.

Par exemple, supposons que AzCopy génère deux fichiers de données une fois que l’utilisateur spécifie l’option de `/SplitSize`. Les noms de fichiers de données qui en résulte peuvent être :

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Notez que le minimum possible de valeur pour l’option `/SplitSize` est 32 Mo. Si la destination spécifiée est le stockage Blob, AzCopy sera fractionner le fichier de données une fois que sa taille atteigne la limite de taille de blob (200 Go), indépendamment de si l’option `/SplitSize` a été spécifié par l’utilisateur.

### <a name="export-table-to-json-or-csv-data-file-format"></a>Table d’exportation au format de fichier des données JSON ou CSV

AzCopy par défaut exporte des tables dans les fichiers de données JSON. Vous pouvez spécifier l’option `/PayloadFormat:JSON|CSV` pour exporter les tables en tant que JSON ou CSV.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Lorsque vous spécifiez le format de charge utile CSV, AzCopy génère également un fichier de schéma avec l’extension de fichier `.schema.csv` pour chaque fichier de données.

### <a name="export-table-entities-concurrently"></a>Exporter simultanément les entités de table

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy démarre des opérations simultanées pour exporter les entités lorsque l’utilisateur spécifie l’option `/PKRS`. Chaque opération exporte une plage clé de partition.

Notez que le nombre d’opérations simultanées est également contrôlé par l’option `/NC`. AzCopy utilise le nombre de processeurs comme valeur par défaut de `/NC` lors de la copie des entités de la table, même si `/NC` n’a été spécifié. Lorsque l’utilisateur spécifie option `/PKRS`, AzCopy utilise la plus petite des deux valeurs - plages de clés de partition et implicitement ou explicitement spécifiée des opérations simultanées - pour déterminer le nombre d’opérations simultanées pour démarrer. Pour plus d’informations, tapez `AzCopy /?:NC` à la ligne de commande.

### <a name="export-table-to-blob"></a>Table d’exportation de blob

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy génère un fichier de données JSON dans le conteneur d’objet blob avec suivant la convention d’affectation de noms :

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Le fichier de données JSON généré suit le format de charge utile des métadonnées minimale. Pour plus d’informations sur ce format de charge utile, voir [Format de charge utile pour les opérations de Service de Table](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Notez que lorsque vous exportez des tables pour les objets BLOB, AzCopy télécharger les entités de Table pour les fichiers de données temporaires locaux et ensuite télécharger ces entités au blob. Ces fichiers de données temporaires sont placés dans le dossier du fichier journal avec le chemin d’accès par défaut «<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>», vous pouvez spécifier option/Z: [dossier-fichier-journal] pour modifier la feuille emplacement de dossier du fichier et par conséquent de modifier l’emplacement des fichiers de données temporaires. Taille des fichiers les données temporaires est déterminée par la taille des entités de votre table et de la taille spécifiée avec l’option /SplitSize, bien que le fichier de données temporaires dans disque local sera supprimé instantanément une fois qu’il a été chargé dans l’objet blob, assurez-vous que vous disposez de suffisamment d’espace disque local pour stocker ces fichiers de données temporaires avant d’être supprimés.

## <a name="table-import"></a>Tableau : importation

### <a name="import-table"></a>Table d’importation

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

L’option `/EntityOperation` indique comment insérer des entités dans la table. Les valeurs possibles sont :

- `InsertOrSkip`: Ignore une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.
- `InsertOrMerge`: Fusionne une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.
- `InsertOrReplace`: Remplace une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.

Notez que vous ne pouvez pas spécifier option `/PKRS` dans le scénario de l’importation. À la différence du scénario d’exportation, dans laquelle vous devez spécifier option `/PKRS` pour démarrer des opérations simultanées, AzCopy par défaut démarre des opérations simultanées lorsque vous importez une table. Le nombre par défaut d’opérations simultanées démarré est égal au nombre de processeurs ; Toutefois, vous pouvez spécifier un nombre différent de simultanées avec l’option `/NC`. Pour plus d’informations, tapez `AzCopy /?:NC` à la ligne de commande.

Notez que AzCopy prend uniquement en charge l’importation de JSON, pas CSV. AzCopy ne pas prendre en charge les importations de table à partir de JSON de créés par l’utilisateur et fichiers manifeste. Ces deux fichiers doivent provenir d’une exportation de la table AzCopy. Pour éviter les erreurs, veuillez ne modifier le JSON exporté ou du fichier manifeste.

### <a name="import-entities-to-table-using-blobs"></a>Entités d’importation à la table à l’aide d’objets BLOB

Supposons un conteneur Blob contient les éléments suivants : fichier JSON d’un représentant une Table Azure et son fichier de manifeste associé.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Vous pouvez exécuter la commande suivante pour importer les entités dans une table en utilisant le fichier de manifeste de ce conteneur blob :

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## <a name="other-azcopy-features"></a>Autres fonctionnalités de AzCopy

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copier uniquement les données qui n’existent pas dans la destination

La `/XO` et `/XN` paramètres permettent d’exclure les ressources source plus ancien ou plus récent soit copié, respectivement. Si vous voulez uniquement copier les ressources de code source qui n’existent pas dans la destination, vous pouvez spécifier les deux paramètres dans la commande AzCopy :

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Remarque : Ceci est impossible lorsque la source ou la destination est une table.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Utiliser un fichier réponse pour spécifier les paramètres de ligne de commande

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

Vous pouvez inclure des paramètres de ligne de commande AzCopy dans un fichier réponse. AzCopy traite les paramètres dans le fichier comme s’ils avaient été spécifiés sur la ligne de commande, effectuer une substitution directe avec le contenu du fichier.

Supposons un fichier réponse nommé `copyoperation.txt`, qui contient les lignes suivantes. Chaque paramètre de AzCopy peut être spécifié sur une seule ligne

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

ou sur des lignes distinctes :

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy échouera si vous fractionnez le paramètre répartie sur deux lignes, comme indiqué ici pour le `/sourcekey` paramètre :

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Utilisez plusieurs fichiers de réponse pour spécifier les paramètres de ligne de commande

Supposons un fichier réponse nommé `source.txt` qui spécifie un conteneur source :

    /Source:http://myaccount.blob.core.windows.net/mycontainer

Et un fichier de réponses nommé `dest.txt` qui spécifie un dossier de destination dans le système de fichiers :

    /Dest:C:\myfolder

Et un fichier de réponses nommé `options.txt` qui spécifie des options pour AzCopy :

    /S /Y

Pour appeler AzCopy avec ces fichiers de réponse, qui se trouvent dans un répertoire `C:\responsefiles`, utilisez cette commande :

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy traite cette commande, comme elle le ferait si vous avez inclus tous les paramètres individuels de la ligne de commande :

    AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### <a name="specify-a-shared-access-signature-sas"></a>Spécifier une signature d’accès partagé (SAS)

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

Vous pouvez également spécifier un SAS sur le conteneur URI :

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### <a name="journal-file-folder"></a>Dossier du fichier journal

Chaque fois que vous lancez une commande de AzCopy, il vérifie si un fichier journal existe dans le dossier par défaut, ou s’il existe dans un dossier que vous avez spécifié à l’aide de cette option. Si le fichier journal n’existe pas à cet emplacement, AzCopy traite l’opération en tant que nouvelles et génère un nouveau fichier journal.

Si le fichier journal n’existe pas, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande dans le fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l’opération non terminée. Si elles ne correspondent pas, vous devez soit remplacer le fichier journal pour démarrer une nouvelle opération, ou pour annuler l’opération en cours.

Si vous souhaitez utiliser l’emplacement par défaut pour le fichier journal :

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Si vous omettez l’option `/Z`, ou spécifiez l’option `/Z` sans le chemin d’accès du dossier, comme indiqué ci-dessus, AzCopy crée le fichier journal dans l’emplacement par défaut, qui est `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Si le fichier journal existe déjà, AzCopy reprend l’opération basée sur le fichier journal.

Si vous souhaitez spécifier un emplacement pour le fichier journal :

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

Cet exemple crée le fichier journal s’il n’existe pas déjà. S’il n’existe pas, AzCopy reprend l’opération basée sur le fichier journal.

Si vous souhaitez reprendre une opération de AzCopy :

    AzCopy /Z:C:\journalfolder\

Cet exemple reprend la dernière opération, ce qui peut ne pas terminer.

### <a name="generate-a-log-file"></a>Générer un fichier journal

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Si vous spécifiez l’option `/V` sans fournir un chemin d’accès de fichier pour le journal en clair, puis AzCopy crée le fichier journal dans l’emplacement par défaut, qui est `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Dans le cas contraire, vous pouvez créer un fichier journal dans un emplacement personnalisé :

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Notez que si vous spécifiez un chemin d’accès relatif suivant l’option `/V`, tel que `/V:test/azcopy1.log`, puis le journal en clair est créé dans le répertoire de travail en cours dans un sous-dossier nommé `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Spécifier le nombre d’opérations simultanées pour démarrer

Option `/NC` Spécifie le nombre d’opérations de copie simultanées. Par défaut, AzCopy démarre un certain nombre d’opérations simultanées pour augmenter le débit de transfert de données. Pour les opérations de la Table, le nombre d’opérations simultanées est égal au nombre de processeurs que vous avez. Pour les opérations Blob et le fichier, le nombre d’opérations simultanées est égale à 8 fois le nombre de processeurs que vous avez. Si vous exécutez AzCopy via un réseau à faible bande passante, vous pouvez spécifier un nombre inférieur pour /NC afin d’éviter l’échec provoqué par la concurrence de la ressource.

### <a name="run-azcopy-against-azure-storage-emulator"></a>Exécutez AzCopy sur l’émulateur de stockage Azure

Vous pouvez exécuter AzCopy par rapport à l' [Émulateur de stockage Azure](storage-use-emulator.md) pour les objets BLOB :

    AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

Tables et :

    AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## <a name="azcopy-parameters"></a>Paramètres de AzCopy

Paramètres de AzCopy sont décrites ci-dessous. Vous pouvez également taper une des commandes suivantes à partir de la ligne de commande pour l’aide à l’aide de AzCopy :

- Pour plus d’informations de ligne de commande pour AzCopy :`AzCopy /?`
- Pour obtenir une aide détaillée sur n’importe quel paramètre de AzCopy :`AzCopy /?:SourceKey`
- Pour obtenir des exemples de ligne de commande :`AzCopy /?:Samples`

### <a name="sourcesource"></a>/ Source : « source »

Spécifie les données source à partir duquel copier. La source peut être un répertoire de système de fichiers, un conteneur blob, un blob répertoire virtuel, un partage de fichiers de stockage, un répertoire de fichiers de stockage ou une table Azure.

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="destdestination"></a>/ Dest : « destination »

Spécifie la destination de copie. La destination peut être un répertoire de système de fichiers, un conteneur blob, un répertoire virtuel de blob, un partage de fichiers de stockage, un répertoire de fichiers de stockage ou une table Azure.

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="patternfile-pattern"></a>/ Modèle : « modèle de fichier »

Spécifie un fichier qui indique les fichiers à copier. Le comportement du paramètre /Pattern est déterminé par l’emplacement de la source de données et la présence de l’option de mode récursif. Mode récursif est spécifié via l’option /S.

Si la source spécifiée est un répertoire dans le système de fichiers, puis les caractères génériques standard sont en vigueur et du modèle de fichier fourni est mis en correspondance avec les fichiers dans le répertoire. Si l’option que /s est spécifiée, puis AzCopy correspond également à la chaîne spécifiée par rapport à tous les fichiers dans des sous-dossiers sous le répertoire.

Si la source spécifiée est un conteneur blob ou un répertoire virtuel, les caractères génériques ne sont pas appliquées. Si l’option que /s est spécifiée, AzCopy puis interprète le modèle du fichier spécifié comme un préfixe d’objet blob. Si l’option que /s n’est pas spécifié, puis AzCopy correspond au modèle de fichier par rapport aux noms de blob exacte.

Si la source spécifiée est un partage de fichier Azure, puis vous devez spécifier le nom de fichier exact, (par exemple, abc.txt) pour copier un seul fichier, ou spécifiez l’option /S pour copier tous les fichiers dans le partage de manière récursive. Tentative de spécifier à la fois un modèle de fichier et l’option /S ensemble entraîne une erreur.

AzCopy utilise la mise en correspondance qui respecte la casse lorsque la /Source est un conteneur blob ou le répertoire virtuel de blob et utilise la correspondance de la casse dans tous les autres cas.

Le modèle de fichier par défaut utilisé lorsqu’aucun modèle de fichier ne spécifié est *.* pour un emplacement de système de fichiers ou un préfixe vide pour un emplacement de stockage Azure. Spécification de plusieurs modèles de fichier n’est pas pris en charge.

**Applicable à :** BLOB, fichiers

### <a name="destkeystorage-key"></a>/ DestKey : « clé de stockage »

Spécifie la clé de compte de stockage de la ressource de destination.

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="destsassas-token"></a>/ DestSAS : « jeton de sas »

Spécifie une Signature de l’accès partagé (SAS) avec les autorisations de lecture et d’écriture pour la destination (le cas échéant). Entourer les associations de sécurité avec des guillemets doubles, comme il peut contenir des caractères de ligne de commande.

Si la ressource de destination est un conteneur blob, partage de fichiers ou une table, vous pouvez spécifier cette option suivie du jeton SAS, ou vous pouvez spécifier les associations de sécurité en tant que partie du conteneur de blob destination, partage de fichiers ou URI la table, sans cette option.

Si la source et la destination sont les deux objets BLOB, le blob de destination doit résider dans le même compte de stockage que le blob de la source.

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="sourcekeystorage-key"></a>/ SourceKey : « clé de stockage »

Spécifie la clé de compte de stockage de la ressource de code source.

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="sourcesassas-token"></a>/ SourceSAS : « jeton de sas »

Spécifie une Signature accès partagé avec les autorisations de lecture et de la liste de la source (le cas échéant). Entourer les associations de sécurité avec des guillemets doubles, comme il peut contenir des caractères de ligne de commande.

Si la ressource de la source est un conteneur de blob, et ni une clé ni un SAS est fournie, le conteneur de l’objet blob est lus par l’accès anonyme.

Si la source est un partage de fichier ou une table, une clé ou un SAS doit être fourni.

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="s"></a>/S

Spécifie le mode récursif pour les opérations de copie. Dans le mode récursif, AzCopy copie tous les objets BLOB ou les fichiers qui correspondent au modèle de fichier spécifié, y compris ceux des sous-dossiers.

**Applicable à :** BLOB, fichiers

### <a name="blobtypeblock--page--append"></a>/ BlobType : « bloquer » | « page » | « append »

Spécifie si le blob de destination est un blob de bloc, un blob de la page ou un blob append. Cette option est applicable uniquement lorsque vous téléchargez un objet blob. Dans le cas contraire, une erreur est générée. Si cette option n’est pas spécifiée, par défaut, et si la destination est un blob AzCopy crée un objet blob de bloc.

**Applicable à :** Objets BLOB

### <a name="checkmd5"></a>/ CheckMD5

Calcule un hachage MD5 pour les données téléchargées et vérifie que le hachage MD5 est stocké dans l’objet blob ou propriété de Content-MD5 du fichier correspond à la valeur de hachage calculée. La vérification MD5 est désactivée par défaut, vous devez spécifier cette option pour effectuer la vérification MD5 lors du téléchargement des données.

Notez que le stockage Azure ne garantit pas que le hachage MD5 stocké pour le blob ou le fichier est à jour. Il est responsable du client pour mettre à jour le hachage MD5, chaque fois que le blob ou le fichier est modifié.

AzCopy affecte toujours la propriété Content-MD5 d’un fichier ou un blob Azure après son téléchargement en au service.  

**Applicable à :** BLOB, fichiers

### <a name="snapshot"></a>/ Snapshot

Indique s’il faut transférer les captures instantanées. Cette option n’est valide que lorsque la source est un objet blob.

Les instantanés de blob transférées sont renommés dans ce format : .extension de blob-nom (heure de la capture instantanée)

Par défaut, les instantanés ne sont pas copiés.

**Applicable à :** Objets BLOB

### <a name="vverbose-log-file"></a>/ V: [verbose-fichier journal]

Affiche les messages d’état détaillés dans un fichier journal.

Par défaut, le fichier journal détaillé est nommé AzCopyVerbose.log dans `%LocalAppData%\Microsoft\Azure\AzCopy`. Si vous spécifiez un emplacement de fichier existant pour cette option, le journal détaillé sera ajouté à ce fichier.  

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="zjournal-file-folder"></a>/ Z: [dossier-fichier-journal]

Spécifie un dossier du fichier journal pour une reprise.

AzCopy prend toujours en charge la reprise si une opération a été interrompue.

Si cette option n’est pas spécifiée ou qu’il est spécifié sans un chemin d’accès du dossier, puis AzCopy créera le fichier journal dans l’emplacement par défaut, qui est % LocalAppData%\Microsoft\Azure\AzCopy.

Chaque fois que vous lancez une commande de AzCopy, il vérifie si un fichier journal existe dans le dossier par défaut, ou s’il existe dans un dossier que vous avez spécifié à l’aide de cette option. Si le fichier journal n’existe pas à cet emplacement, AzCopy traite l’opération en tant que nouvelles et génère un nouveau fichier journal.

Si le fichier journal n’existe pas, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande dans le fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l’opération non terminée. Si elles ne correspondent pas, vous devez soit remplacer le fichier journal pour démarrer une nouvelle opération, ou pour annuler l’opération en cours.

Le fichier journal est supprimé en cas de réussite de l’opération.

Notez qu’une reprise à partir d’un fichier journal créé par une version précédente de AzCopy n’est pas pris en charge.

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="parameter-file"></a>/@:"parameter-file"

Spécifie un fichier qui contient des paramètres. AzCopy traite les paramètres dans le fichier, comme s’ils avaient été spécifiés sur la ligne de commande.

Dans un fichier de réponse, vous pouvez spécifier plusieurs paramètres sur une seule ligne, ou spécifier chaque paramètre sur sa propre ligne. Notez qu’un paramètre individuel ne peut couvrir plusieurs lignes.

Les fichiers de réponse peuvent inclure des lignes de commentaires commencent par le symbole #.

Vous pouvez spécifier plusieurs fichiers réponse. Toutefois, notez que AzCopy ne prend pas en charge les fichiers réponse imbriqués.

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="y"></a>/Y

Supprime toutes les invites de confirmation de AzCopy.

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="l"></a>/L

Spécifie une opération de vente uniquement. aucune donnée n’est copiée.

AzCopy interprète l’à l’aide de cette option comme une simulation pour l’exécution de la ligne de commande sans cette option /L et compter le nombre d’objets est copié, vous pouvez spécifier option /V en même temps pour vérifier les objets seront copiés dans le journal en clair.

Le comportement de cette option est également déterminé par l’emplacement de la source de données et de la présence de la mode option /S et le fichier modèle option récursive /Pattern.

AzCopy requiert une autorisation de liste et de la lecture de cet emplacement source lors de l’utilisation de cette option.

**Applicable à :** BLOB, fichiers

### <a name="mt"></a>/MT

Définit l’heure de dernière modification du fichier téléchargé à être identique à celui du blob de la source ou du fichier.

**Applicable à :** BLOB, fichiers

### <a name="xn"></a>/XN

Exclut une ressource plus récente de la source. La ressource ne sera pas copiée si l’heure de dernière modification de la source est identique ou ultérieure à celle de destination.

**Applicable à :** BLOB, fichiers

### <a name="xo"></a>/XO

Exclut une ancienne ressource de source. La ressource ne sera pas copiée si l’heure de dernière modification de la source est identique ou antérieure à la destination.

**Applicable à :** BLOB, fichiers

### <a name="a"></a>/A

Télécharge uniquement les fichiers qui ont l’attribut Archive.

**Applicable à :** BLOB, fichiers

### <a name="iarashcnetoi"></a>/ IA : [RASHCNETOI]

Télécharge uniquement les fichiers qui ont un des attributs spécifiés est défini.

Les attributs disponibles sont les suivantes :

- R = les fichiers en lecture seule
- A = fichiers prêts pour l’archivage
- S = les fichiers système
- H = les fichiers cachés
- C = les fichiers compressés
- N = les fichiers normaux
- E = crypté les fichiers
- T = les fichiers temporaires
- O = les fichiers hors connexion
- I = les fichiers Non indexés

**Applicable à :** BLOB, fichiers

### <a name="xarashcnetoi"></a>/ XA : [RASHCNETOI]

Exclut les fichiers qui ont un des attributs spécifiés est défini.

Les attributs disponibles sont les suivantes :

- R = les fichiers en lecture seule
- A = fichiers prêts pour l’archivage
- S = les fichiers système
- H = les fichiers cachés
- C = les fichiers compressés
- N = les fichiers normaux
- E = crypté les fichiers
- T = les fichiers temporaires
- O = les fichiers hors connexion
- I = les fichiers Non indexés

**Applicable à :** BLOB, fichiers

### <a name="delimiterdelimiter"></a>/ Délimiteur : « séparateur »

Indique le caractère séparateur utilisé pour délimiter les répertoires virtuels dans un nom d’objet blob.

Par défaut, utilise des AzCopy / comme caractère délimiteur. Toutefois, AzCopy prend en charge à l’aide de n’importe quel caractère commun (tel que @, # ou %) comme délimiteur. Si vous devez inclure un de ces caractères spéciaux sur la ligne de commande, placez le nom de fichier entre guillemets doubles.

Cette option n’est applicable pour le téléchargement des objets BLOB.

**Applicable à :** Objets BLOB

### <a name="ncnumber-of-concurrent-operations"></a>/ CN : « numéro-de--opérations simultanées »

Spécifie le nombre d’opérations simultanées.

AzCopy par défaut démarre un certain nombre d’opérations simultanées pour augmenter le débit de transfert de données. Notez que le grand nombre d’opérations simultanées dans un environnement à faible bande passante peut submerger la connexion réseau et empêcher les opérations de se terminer complètement. Limitation des opérations simultanées en fonction de la bande passante du réseau disponible.

La limite supérieure pour des opérations simultanées est 512.

**Applicable à :** Tables d’objets BLOB, fichiers,

### <a name="sourcetypeblob--table"></a>/ SourceType : « Blob » | « Table »

Spécifie que la `source` ressource est disponible dans l’environnement de développement local, en cours d’exécution dans l’émulateur de stockage blob.

**Applicable à :** BLOB, Tables

### <a name="desttypeblob--table"></a>/ TypeDest : « Blob » | « Table »

Spécifie que la `destination` ressource est disponible dans l’environnement de développement local, en cours d’exécution dans l’émulateur de stockage blob.

**Applicable à :** BLOB, Tables

### <a name="pkrskey1key2key3"></a>/ PKRS : « key1 #key2 argument key3 #... »

Fractionne la plage clé de partition pour permettre l’exportation des données de table en parallèle, ce qui augmente la vitesse de l’opération d’exportation.

Si cette option n’est pas spécifiée, AzCopy utilise un seul thread pour exporter les entités de table. Par exemple, si l’utilisateur spécifie /PKRS : « CA-#bb », puis AzCopy démarre trois opérations simultanées.

Chaque opération exporte une de trois plages de clé de partition, comme illustré ci-dessous :

  [première clé de partition, aa)

  [aa, bb)

  [dernière clé de partition, bb]

**Applicable à :** Tables

### <a name="splitsizefile-size"></a>/ SplitSize : « taille de fichier »

Spécifie le fichier exporté fractionner la taille en Mo, la valeur minimale autorisée est de 32.

Si cette option n’est pas spécifiée, AzCopy exportera les données de la table à fichier unique.

Si les données du tableau sont exportées dans un blob, et la taille du fichier exporté atteint la limite de 200 Go pour la taille du blob, puis AzCopy fractionne le fichier exporté, même si cette option n’est pas spécifiée.

**Applicable à :** Tables

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation : « InsertOrSkip » | « InsertOrMerge » | « InsertOrReplace »

Spécifie le comportement d’importation de données table.

- InsertOrSkip - ignore une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.

- InsertOrMerge - fusionne une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.

- InsertOrReplace - remplace une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.

**Applicable à :** Tables

### <a name="manifestmanifest-file"></a>/ Manifeste : « fichier de manifeste »

Spécifie le fichier manifeste pour la table d’exportation et l’opération d’importation.

Cette option est facultative lors de l’opération d’exportation, AzCopy génère un fichier manifeste portant un nom prédéfini si cette option n’est pas spécifiée.

Cette option est requise lors de l’opération d’importation pour localiser les fichiers de données.

**Applicable à :** Tables

### <a name="synccopy"></a>/ SyncCopy

Indique s’il faut copier de manière synchrone BLOB ou des fichiers entre deux points de terminaison de stockage Azure.

AzCopy par défaut utilise la copie asynchrone du côté serveur. Spécifiez cette option pour effectuer une copie synchrone, qui télécharge le BLOB ou fichiers vers la mémoire locale et les transmet ensuite au stockage Azure.

Vous pouvez utiliser cette option pour copier les fichiers dans le stockage Blob, le stockage de fichiers ou de stockage Blob pour le stockage de fichiers ou vice versa.

**Applicable à :** BLOB, fichiers

### <a name="setcontenttypecontent-type"></a>/ SetContentType : « content-type »

Spécifie le type de contenu MIME pour les fichiers ou les objets BLOB de destination.

AzCopy définit le type de contenu pour un fichier ou un objet blob à application/octet-stream par défaut. Vous pouvez définir le type de contenu pour tous les objets BLOB ou des fichiers en spécifiant explicitement une valeur pour cette option.

Si vous spécifiez cette option sans valeur, AzCopy définir chaque blob ou type de contenu du fichier en fonction de son extension de fichier.

**Applicable à :** BLOB, fichiers

### <a name="payloadformatjson--csv"></a>/ PayloadFormat : « JSON » | « CSV »

Spécifie le format du fichier de données exporté table.

Si cette option n’est pas spécifiée, par défaut, AzCopy exporte fichier table de données au format JSON.

**Applicable à :** Tables

## <a name="known-issues-and-best-practices"></a>Problèmes connus et les meilleures pratiques

### <a name="limit-concurrent-writes-while-copying-data"></a>Limite des écritures simultanées lors de la copie des données

Lorsque vous copiez des objets BLOB ou avec AzCopy, gardez à l’esprit qu’une autre application peut modifier les données pendant que vous effectuez la copie. Si possible, assurez-vous que les données que vous copiez ne sont pas modifiées au cours de l’opération de copie. Par exemple, lors de la copie d’un disque dur virtuel associé à une machine virtuelle Azure, assurez-vous qu’aucune autre application n’écrivez actuellement sur le disque dur virtuel. Un bon moyen de procéder est en louant la ressource à copier. Alternativement, vous pouvez d’abord créer une capture instantanée d’un disque dur virtuel et puis copier la capture instantanée.

Si vous ne pouvez pas empêcher les autres applications à partir de l’écriture pour les objets BLOB ou fichiers pendant qu’ils sont en cours de copie, puis esprit qu’au moment où que la tâche se termine, les ressources copiées ne peuvent ne plus avoir une parité complète avec les ressources de la source.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Exécuter une seule instance de AzCopy sur un ordinateur.
AzCopy est conçu pour optimiser l’utilisation de votre ressource de l’ordinateur pour accélérer le transfert des données, nous vous recommandons de vous exécutez qu’une seule instance de AzCopy sur un ordinateur et que vous spécifiez l’option `/NC` si vous avez besoin d’opérations simultanées plus. Pour plus d’informations, tapez `AzCopy /?:NC` à la ligne de commande.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Activer les algorithmes MD5 compatibles FIPS pour AzCopy lorsque vous « utiliser FIPS algorithmes compatibles pour le cryptage, le hachage et la signature ».
AzCopy par défaut utilise l’implémentation .NET MD5 pour calculer le hachage MD5 lors de la copie des objets, mais il existe certaines exigences de sécurité qui ont besoin de AzCopy pour activer le paramètre de MD5 compatible FIPS.

Vous pouvez créer un fichier app.config `AzCopy.exe.config` avec la propriété `AzureStorageUseV1MD5` et le placer ailleurs avec AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

La valeur par défaut, AzCopy utilise pour la propriété « AzureStorageUseV1MD5 » • True - implémentation MD5 de .NET.
• Faux – AzCopy utilise les algorithmes MD5 compatibles FIPS.

Notez que les algorithmes compatibles FIPS est désactivé par défaut sur votre ordinateur Windows, vous pouvez tapez secpol.msc dans la fenêtre exécution et vérifier ce commutateur au paramètre de sécurité -> Stratégies locales -> sécurité Options -> cryptographie système : utilisez conforme des algorithmes FIPS pour le cryptage, le hachage et la signature.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le stockage Azure et AzCopy, consultez les ressources suivantes.

### <a name="azure-storage-documentation"></a>Documentation du stockage Azure :

- [Introduction au stockage Azure](storage-introduction.md)
- [Comment faire pour utiliser le stockage Blob à partir de .NET](storage-dotnet-how-to-use-blobs.md)
- [Comment faire pour utiliser le stockage de fichiers à partir de .NET](storage-dotnet-how-to-use-files.md)
- [Comment faire pour utiliser le stockage de Table à partir de .NET](storage-dotnet-how-to-use-tables.md)
- [Comment faire pour créer, gérer ou supprimer un compte de stockage](storage-create-storage-account.md)

### <a name="azure-storage-blog-posts"></a>Billets de blog de stockage Azure :
- [Présentation d’aperçu de bibliothèque de mouvement de données de stockage Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy : Présentation de copie synchrone et le type de contenu personnalisé](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy : Annonce de la disponibilité générale de AzCopy 3.0 et version d’évaluation de AzCopy 4.0 avec prise en charge de Table et le fichier](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy : Optimisé pour les scénarios de la copie à grande échelle](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy : la prise en charge pour le stockage redondant géo-accès en lecture](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy : Transfert de données avec le mode redémarré et jeton SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy : À l’aide d’inter-compte copie Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy : Téléchargement/chargement des fichiers pour les BLOB Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
