<properties
    pageTitle="À l’aide d’importation/exportation pour transférer les données pour le stockage des objets Blob | Microsoft Azure"
    description="Apprenez à créer d’importation et d’exportation des travaux dans le portail Azure classique pour transférer les données pour le stockage blob."
    authors="renashahmsft"
    manager="aungoo"
    editor="tysonn"
    services="storage"
    documentationCenter=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="renash"/>


# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-blob-storage"></a>Le Service d’importation/exportation de Microsoft Azure permet de transférer des données vers le stockage d’objets Blob

## <a name="overview"></a>Vue d’ensemble

Azure Service d’importation/exportation vous permet de transférer en toute sécurité de grandes quantités de données pour le stockage des objets blob Azure par les lecteurs de disque dur à un centre de données Azure d’expédition. Vous pouvez également utiliser ce service pour transférer des données depuis le stockage blob Azure pour lecteurs de disque dur et l’expédier à votre site local. Ce service convient dans les situations où vous souhaitez transférer plusieurs to de données vers ou à partir d’Azure, mais le transfert ou le téléchargement du réseau n’est pas possible en raison d’une bande passante limitée ou les coûts de réseau haute.

Le service nécessite que les disques durs doivent être avantageuse crypté pour la sécurité de vos données. Le service prend en charge les comptes de stockage classique présentes dans toutes les régions de Public Azure. Vous devez expédier les lecteurs de disque dur à l’un des emplacements pris en charge spécifiés plus loin dans cet article.

Dans cet article, vous en apprendrez plus sur le service d’importation/exportation d’Azure et à livrer des lecteurs pour la copie de vos données et de stockage des objets Blob Azure.

> [AZURE.IMPORTANT] Vous pouvez créer et gérer l’importation et exportation de tâches de stockage classique en utilisant le portail classique ou le [service d’importation/exportation des API de reste](http://go.microsoft.com/fwlink/?LinkID=329099). Les comptes de stockage de gestionnaire de ressources ne sont pas pris en charge pour l’instant.

## <a name="when-should-i-use-the-azure-importexport-service"></a>Quand dois-je utiliser le service d’importation/exportation d’Azure ?

Vous pouvez envisager d’utiliser le service d’importation/exportation d’Azure lors du téléchargement de téléchargement de données sur le réseau est trop lent ou mise en route de la bande passante réseau additionnelle est coûteux.

Vous pouvez utiliser ce service dans des scénarios tels que :

- Migration des données dans le nuage : déplacer de grandes quantités de données vers Azure rapidement et à moindre coût.
- Distribution de contenu : envoyer rapidement des données à vos sites de clients.
- Sauvegarde : Effectuer des sauvegardes de vos données locales à stocker dans le stockage blob Azure.
- Récupération des données : récupérer la grande quantité de données stockées dans le stockage blob et l’avez remis à votre emplacement local.

## <a name="pre-requisites"></a>Conditions préalables

Dans cette section, nous avons répertorié les conditions préalables nécessaires à l’utilisation de ce service. Veuillez les vérifier soigneusement avant d’expédier vos lecteurs.

### <a name="storage-account"></a>Compte de stockage

Vous devez avoir un abonnement Azure et un ou plusieurs des comptes de stockage **classique** pour utiliser le service d’importation/exportation. Chaque tâche peut être utilisé pour transférer des données vers ou à partir d’un seul compte de stockage classique. En d’autres termes, un travail d’importation/exportation unique ne peut pas s’étendent sur plusieurs comptes de stockage. Pour plus d’informations sur la création d’un nouveau compte de stockage, reportez-vous à la section [comment créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account).

### <a name="blob-types"></a>Types de BLOB

Vous pouvez utiliser le service d’importation/exportation d’Azure pour copier les données de BLOB de **bloc** ou des objets de la **Page** . À l’inverse, vous ne pouvez exporter des blobs de **bloc** , **Page** BLOB ou **Append** BLOB à partir du stockage Azure à l’aide de ce service.

### <a name="job"></a>Travail

Pour commencer le processus d’importation et l’exportation depuis le stockage Blob, vous créez tout d’abord une tâche. Un travail peut être une tâche d’importation ou d’une tâche d’exportation :

- Créer une tâche d’importation lorsque vous souhaitez transférer des données que vous avez dans votre compte de stockage Azure sur site pour les objets BLOB.
- Créer une tâche d’exportation lorsque vous voulez transférer les données actuellement stockées sous la forme d’objets BLOB dans votre compte de stockage pour les disques durs qui vous sont livrés.

Lorsque vous créez une tâche, vous avertir le service d’importation/exportation que vous livrera un ou plusieurs disques durs à un centre de données Azure.

- Pour une tâche d’importation, vous livrera les disques durs contenant vos données.
- Pour une tâche d’exportation, vous livrera les lecteurs de disque durs vides.
- Vous pouvez livrer jusqu'à 10 disques durs par projet.

Vous pouvez créer une importation ou à l’aide du [portail de classique](https://manage.windowsazure.com/) ou l' [API REST de Azure stockage importation/exportation](http://go.microsoft.com/fwlink/?LinkID=329099)de tâche d’exportation.

### <a name="client-tool"></a>Outil client

La première étape dans la création d’une tâche **d’importation** est pour préparer votre lecteur qui est expédié à importer. Pour préparer votre lecteur, vous devez vous connecter à un serveur local et exécuter l’outil de Client d’importation/exportation Azure sur le serveur local. Cet outil client facilite la copie vos données sur le disque, le chiffrement des données sur le lecteur avec BitLocker et générer les fichiers de feuille de lecteur.

Les fichiers de journal stockent des informations de base sur votre travail et d’un lecteur de numéro de série de lecteur et le nom du compte de stockage. Ce fichier journal n’est pas stocké sur le lecteur. Il est utilisé lors de la création de tâche d’importation. Étape par étape pour plus d’informations sur la création d’emplois sont fournies plus loin dans cet article.

L’outil client n’est pas compatible avec les systèmes d’exploitation Windows 64 bits. Reportez-vous à la section de [système d’exploitation](#operating-system) pour des versions spécifiques du système d’exploitation pris en charge.

Télécharger la version la plus récente de l' [outil d’importation/exportation d’Azure client](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Pour plus d’informations sur l’utilisation de l’outil d’importation/exportation Azure, consultez la [Référence d’outils Azure importation/exportation](http://go.microsoft.com/fwlink/?LinkId=329032).

### <a name="hard-disk-drives"></a>Lecteurs de disque dur

3,5 pouces uniquement des disques durs internes SATA II/III sont pris en charge pour une utilisation avec le service d’importation/exportation. Vous pouvez utiliser des disques durs jusqu'à 10 To.
Pour les tâches d’importation, seul le premier volume de données sur le lecteur est traité. Le volume de données doit être au format NTFS.
Lors de la copie des données sur votre disque dur, vous pouvez attacher directement à l’aide d’un connecteur SATA ou vous pouvez les joindre en externe à l’aide d’un adaptateur SATA II/III USB externe. Nous recommandons d’utiliser un des adaptateurs SATA II/III USB externes suivantes :

- Anker 68UPSATAA - 02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Sharkoon QuickPort XT HC

Si vous avez un convertisseur qui n’est pas répertorié ci-dessus, vous pouvez essayer d’exécuter l’outil d’importation/exportation Azure pour préparer le disque et de voir si elle fonctionne avant d’acheter un convertisseur pris en charge à l’aide de votre convertisseur.

> [AZURE.IMPORTANT] Disques durs externes qui sont fournis avec un adaptateur USB intégré ne sont pas pris en charge par ce service. Également, le disque dans le boîtier d’un disque dur externe ne peut pas être utilisé ; n’envoyez pas de disques durs externes.

### <a name="encryption"></a>Cryptage

Les données sur le lecteur doivent être cryptées à l’aide du chiffrement de lecteur BitLocker. Il protège vos données en transit.

Pour les tâches d’importation, il existe deux façons d’effectuer le chiffrement. La première consiste à utiliser la /Encrypt paramètre lors de l’exécution de l’outil client lors de la préparation du lecteur. La deuxième consiste à activer le chiffrement BitLocker manuellement sur le lecteur et spécifier la clé de cryptage dans la ligne de commande d’outil client lors de la préparation du lecteur.

Pour les tâches d’exportation, une fois que vos données sont copiées vers les lecteurs, le service chiffre le lecteur à l’aide de BitLocker avant de l’expédier vers vous. La clé de cryptage est accordée via le portail classique pour vous.  

### <a name="operating-system"></a>Système d'exploitation

Vous pouvez utiliser un des systèmes d’exploitation 64 bits suivants pour préparer le disque dur à l’aide de l’outil d’importation/exportation Azure avant d’expédier le lecteur pour Azure :

Windows 7 Entreprise, Windows 7 Édition Intégrale, Windows 8 Pro, Windows 8 Entreprise, Windows 8.1 Pro, Windows 8.1, entreprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Tous ces systèmes d’exploitation prennent en charge le chiffrement de lecteur BitLocker.

> [AZURE.IMPORTANT] <sup>1</sup> Si vous utilisez un ordinateur Windows 10 pour préparer votre disque dur, téléchargez la dernière version de l’outil d’importation/exportation Azure.

### <a name="locations"></a>Emplacements

Le service d’importation/exportation d’Azure prend en charge la copie des données vers et à partir de tous les comptes de stockage Public Azure. Vous pouvez expédier des lecteurs de disque dur à l’un des emplacements suivants. Si votre compte de stockage est dans un lieu public Azure n’est pas spécifié ici, un emplacement de livraison de remplacement est fourni lors de la création du travail à l’aide du portail classique ou l’API REST d’importation/exportation.

Prise en charge des lieux d’expédition :

- Les États-Unis

- États-Unis Ouest

- Les États-Unis 2

- États-Unis centre

- États-Unis centre nord

- États-Unis centre sud

- Europe du Nord

- Europe de l’ouest

- Asie de l’est

- Asie du Sud-est

- Est de l’Australie

- Sud-est de l’Australie

- Ouest du Japon

- Nord-est du Japon

- Central de l’Inde


### <a name="shipping"></a>Frais d’expédition

**Lecteurs de livraison au centre de données :**

Lorsque vous créez une tâche d’importation ou d’exportation, vous fournira une adresse d’expédition de l’un des emplacements d’expédier vos lecteurs pris en charge. L’adresse de livraison fournie dépend de l’emplacement de votre compte de stockage, mais il ne peut pas être le même que votre emplacement de compte de stockage.

Vous pouvez utiliser des opérateurs comme FedEx, DHL, l’onduleur ou le Service Postal US pour expédier vos lecteurs à l’adresse de livraison.

**Lecteurs de livraison à partir du centre de données :**

Lorsque vous créez une tâche d’importation ou d’exportation, vous devez fournir une adresse de retour Microsoft à utiliser lors de l’expédition des lecteurs après que votre travail est terminé. Veuillez vous assurer de que vous fournissez une adresse de retour valide pour éviter des retards de traitement.

Vous devez également fournir un transporteur FedEx ou DHL valide pour être utilisé par Microsoft pour les lecteurs de retour de livraison numéro de compte. Un numéro de compte FedEx est requis pour les lecteurs d’expédition retour à partir des emplacements aux États-Unis et en Europe. Un numéro de compte DHL est requis pour les lecteurs d’expédition à partir de sites en Asie et en Australie. Si vous n’en avez pas, vous pouvez créer un [FedEx](http://www.fedex.com/us/oadr/) (pour les États-Unis et en Europe) ou le compte du transporteur [DHL](http://www.dhl.com/) (Asie et Australie). Si vous avez déjà un numéro de compte du transporteur, vérifiez qu’elle est valide.

Dans l’expédition de vos packages, vous devez suivre les termes du contrat à un [Contrat de Service Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [AZURE.IMPORTANT] Veuillez noter que le média physique que vous fournissez doivent traverser des frontières internationales. Vous êtes chargé de veiller à ce que vos données et supports physiques sont importées et/ou exportées conformément à la législation applicable. Avant d’expédier le support physique, consultez vos conseillers pour vérifier que votre support et les données peuvent légalement être expédiées au centre de données identifié. Cela vous permet de vous assurer qu’elles atteignent Microsoft en temps voulu. Par exemple, un package qui se croisent les frontières internationales doit une facture commerciale s’accompagner avec le package (sauf si franchissement des frontières au sein de l’Union européenne). Vous pouvez imprimer une copie remplie de la facture commerciale à partir du site Web du transporteur. Exemple de commercial facture sont [facture commerciale de DHL] (http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) ou la [Facture commerciale de FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Assurez-vous que Microsoft n’a pas été indiqué en tant que l’exportateur.

## <a name="how-does-the-azure-importexport-service-work"></a>Comment fonctionne le service d’importation/exportation d’Azure ?

Vous pouvez transférer des données entre votre site local et le stockage blob Azure à l’aide du service d’importation/exportation d’Azure par la création d’emplois et d’expédition des lecteurs de disque dur à un centre de données Azure. Chaque lecteur de disque dur que vous livrez est associé à une seule tâche. Chaque tâche est associée à un compte de stockage unique. Consultez la [section des composants requis](#pre-requisites) avec soin, pour en savoir plus sur les spécificités de ce types blob de service telles que la prise en charge, les types de disque, emplacements et l’expédition.

Dans cette section, nous décrirons à un niveau élevé, les étapes nécessaires pour importer et exporter des tâches. Plus loin dans la [section Démarrage rapide](#quick-start), nous fournit des instructions pas à pas pour créer une importation et exportation de travail.

### <a name="inside-an-import-job"></a>À l’intérieur d’une tâche d’importation

À un niveau élevé, une tâche d’importation implique les étapes suivantes :

- Déterminer les données à importer et le nombre de lecteurs, que vous aurez besoin.
- Identifier les objets BLOB de destination de vos données dans le stockage Blob.
- L’outil d’importation/exportation Azure permet de copier les données dans un ou plusieurs lecteurs de disque dur et de les crypter avec BitLocker.  
- Créer une tâche d’importation dans votre compte de stockage classique de cible via le portail classique ou l’API REST d’importation/exportation. Si vous utilisez le portail classique, télécharger les fichiers de feuille de lecteur.
- Fournir l’adresse de retour et le numéro de compte de transporteur à utiliser pour les lecteurs de livraison avec vous.
- Expédier les lecteurs de disque dur à l’adresse de livraison fournie lors de la création de la tâche.
- Mise à jour de la livraison de numéro dans les détails de la tâche Importation de suivi et de soumettre l’importation.
- Les disques sont reçus et traités dans le centre de données Azure.
- Les lecteurs sont livrés à l’aide de votre compte de support à l’adresse de retour indiquée dans la tâche d’importation.

    ![Flux de travail de la figure 1:Import](./media/storage-import-export-service/importjob.png)


### <a name="inside-an-export-job"></a>À l’intérieur d’une tâche d’exportation

À un niveau élevé, une tâche d’exportation implique les étapes suivantes :

- Déterminer les données à exporter et le nombre de lecteurs, que vous aurez besoin.
- Identifier le BLOB de la source ou les chemins d’accès du conteneur de vos données dans le stockage Blob.
- Créer une tâche d’exportation de votre compte de stockage source à l’aide du portail classique ou l’API REST d’importation/exportation.
- Dans la tâche d’exportation, spécifiez le BLOB de source ou les chemins d’accès du conteneur de vos données.
- Fournir l’adresse de l’expéditeur et le numéro de compte du transporteur pour à utiliser pour les lecteurs de livraison avec vous.
- Expédier les lecteurs de disque dur à l’adresse de livraison fournie lors de la création de la tâche.
- Mise à jour de la livraison de numéro dans les détails de la tâche exportation de suivi et de soumettre la tâche d’exportation.
- Les lecteurs sont reçus et traités dans le centre de données Azure.
- Les disques sont chiffrés avec BitLocker. les clés sont disponibles via le portail classique.  
- Les lecteurs sont livrés à l’aide de votre compte de support à l’adresse de retour indiquée dans la tâche d’importation.

    ![Flux de travail de la figure 2:Export](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-status"></a>Affichage de l’état de votre travail

Vous pouvez suivre le statut de votre importation ou exportation de tâches à partir du portail classique. Accédez à votre compte de stockage dans le portail classique et cliquez sur l’onglet **Importer/exporter** . Une liste de vos tâches s’affiche sur la page. Vous pouvez filtrer la liste sur le statut de la tâche, nom de la tâche, type de tâche ou numéro de suivi.

Vous verrez un des statuts selon l’emplacement votre lecteur dans le processus de travail suivant.

| Statut de la tâche   | Description                                                                                                                                                                                                                                                                                                                                                                        |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Création     | Votre travail a été créé, mais vous n’avez pas encore fourni vos informations d’expédition.                                                                                                                                                                                                                                                                                                |
| Frais d’expédition     | Votre travail a été créé et que vous avez fourni vos informations d’expédition. **Remarque**: lorsque le lecteur est remis au centre de données Azure, l’état peut afficher toujours « Livraison » pendant un certain temps. Une fois que le service démarre la copie de vos données, l’état passe à « Transfert ». Si vous souhaitez voir un statut plus spécifique de votre lecteur, vous pouvez utiliser l’API REST d’importation/exportation. |
| Transfert | Vos données sont transférées à partir de votre disque dur (pour une tâche d’importation) ou sur votre disque dur (pour une tâche d’exportation).                                                                                                                                                                                                                                                                 |
| Emballage    | Le transfert de vos données est terminé, et votre disque dur est en cours de préparation pour l’expédition vers vous.                                                                                                                                                                                                                                                                             |
| Terminer     | Votre disque dur a été livré avec vous.                                                                                                                                                                                                                                                                                                                                      |

### <a name="time-to-process-job"></a>Temps de traitement

La quantité de temps nécessaire à une opération d’importation/exportation varie en fonction de différents facteurs, tels que le délai d’expédition, des processus de travail type, type et taille des données en cours de copie et la taille des disques fournis. Le Service d’importation/exportation n’a pas d’un contrat SLA. Vous pouvez utiliser l’API REST pour suivre la progression de la tâche de plus près. Il existe un paramètre complète pour cent dans l’opération de la liste des tâches qui donne une indication de la progression de la copie. Contact avec nous si vous avez besoin d’une estimation pour terminer une tâche critique d’importation/exportation de temps.

### <a name="pricing"></a>Tarification

**Frais de manutention de lecteur**

Il existe des frais de gestion de disque pour chaque lecteur traitée dans le cadre de l’importation ou de la tâche d’exportation. Afficher les détails sur le [Prix d’importation/exportation Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Les frais de livraison**

Lorsque vous expédiez des lecteurs sur Azure, vous payez les frais d’expédition au transporteur. Lorsque Microsoft renvoie les lecteurs à vous, le coût d’expédition est facturé sur le compte de support qui vous est fournie au moment de la création d’emplois.

**Coûts de transaction**

Il n’existe aucun coût de transaction lors de l’importation de données dans le stockage blob. Les frais de sortie standard sont applicables lorsque les données sont exportées depuis le stockage blob. Pour plus d’informations sur les coûts de transaction, consultez [de transfert de données tarification.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>Démarrage rapide

Dans cette section, nous fournit des instructions pas à pas pour la création d’une importation et une tâche d’exportation. Vérifiez que vous remplissez toutes les [conditions préalables](#pre-requisites) avant de passer.

## <a name="how-to-create-an-import-job"></a>Comment faire pour créer une tâche d’importation ?

Créer une tâche d’importation pour copier des données vers votre compte de stockage Azure à partir de disques durs par la livraison d’un ou plusieurs disques contenant des données au centre de données spécifié. La tâche d’importation transmet les détails sur les données à copier, cibler le compte de stockage et les informations de livraison pour le service d’importation/exportation d’Azure, lecteurs de disque dur. Création d’une tâche d’importation est un processus en trois étapes. Commencez par préparer vos lecteurs à l’aide de l’outil client Azure importation/exportation. En second lieu, soumettre une tâche d’importation à l’aide du portail classique. Troisièmement, expédier les lecteurs à l’adresse de livraison fournie lors de la création de la tâche et les informations d’expédition dans les détails de la tâche de mise à jour.   

> [AZURE.IMPORTANT] Vous pouvez envoyer uniquement une tâche par compte de stockage. Chaque lecteur de que vous expédiez peut être importé dans un compte de stockage. Par exemple, supposons que vous souhaitez importer des données dans les deux comptes de stockage. Vous devez utiliser des disques durs distincts pour chaque compte de stockage et créer des projets distincts par compte de stockage.

### <a name="prepare-your-drives"></a>Préparer vos lecteurs

La première étape lors de l’importation de données à l’aide du service d’importation/exportation d’Azure consiste à préparer vos lecteurs à l’aide de l’outil client Azure importation/exportation. Suivez les étapes ci-dessous pour préparer vos lecteurs.

1.  Identifier les données à importer. Il peut s’agir de répertoires et fichiers autonomes sur le serveur local ou un partage réseau.  

2.  Déterminer le nombre de lecteurs, que vous aurez besoin en fonction de la taille totale des données. Se procurer le nombre requis de disques durs de 3,5 pouces SATA II/III.

3.  Identifier le compte de stockage cible, conteneur, répertoires virtuels et BLOB.

4.  Déterminer les répertoires ou fichiers autonomes qui seront copiées dans chaque lecteur de disque dur.

5.  L' [Outil d’importation/exportation d’Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) permet de copier les données dans un ou plusieurs disques durs.

    - L’outil d’importation/exportation d’Azure crée des sessions de copie pour copier vos données à partir de la source pour les lecteurs de disque dur. Dans une session de copie unique, l’outil peut copier un seul répertoire et ses sous-répertoires, ou un seul fichier.

    - Vous devrez peut-être copier plusieurs sessions si votre source de données s’étend sur plusieurs répertoires.

    - Chaque lecteur de disque dur que vous préparez nécessitera une session d’au moins une copie.

6.  Vous pouvez spécifier la / crypter le paramètre pour activer le chiffrement Bitlocker sur le lecteur de disque dur. Sinon, vous pouvez également activer le cryptage Bitlocker manuellement sur le disque dur et fournir la clé lors de l’exécution de l’outil.

7.  L’outil d’importation/exportation Azure génère un fichier de feuille de disque pour chaque lecteur tel qu’il est prêt. Le fichier journal de disque est stocké sur votre ordinateur local, et non sur le lecteur proprement dit. Vous allez télécharger le fichier journal lorsque vous créez la tâche d’importation. Un fichier de feuille de disque inclut l’ID du disque et la clé BitLocker, ainsi que d’autres informations sur le lecteur.
**Important**: chaque lecteur de disque dur vous préparez entraîne dans un fichier journal. Lorsque vous créez la tâche d’importation à l’aide du portail classique, vous devez télécharger tous les fichiers de journal des disques qui font partie de cette tâche d’importation. Lecteurs sans feuille de fichiers ne seront pas traités.

8.  Ne modifiez pas les données sur les lecteurs de disque dur ou le fichier journal après la préparation du disque.

Voici les commandes et les exemples de préparation de lecteur de disque dur à l’aide d’outil de client Azure importation/exportation.

Azure importation/exportation client PrepImport commande d’outil pour la première session de copie copier un répertoire :

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Exemple :**

Dans l’exemple ci-dessous, nous copiez tous les fichiers et sous-répertoires à partir de H:\Video sur le disque dur monté sur X:. Les données seront importées dans le compte de stockage de destination spécifié par la clé de compte de stockage et dans le conteneur de stockage appelée vidéo. Si le conteneur de stockage n’est pas présent, il sera créé. Cette commande va également mettre en forme et chiffrer le lecteur du disque dur cible.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Azure importation/exportation client PrepImport commande d’outil pour les sessions ultérieures de copie copier un répertoire :

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Pour les sessions de copie suivants sur le même disque dur, spécifier le même nom de fichier journal et fournissent un nouvel ID de session ; Il n’est pas nécessaire pour fournir le stockage compte clé et le lecteur cible, ni mettre en forme ou chiffrer le lecteur. Dans cet exemple nous copions le dossier H:\Photo et ses sous-répertoires sur le même lecteur cible, dans le conteneur de stockage appelé photo.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

L’outil client Azure importation/exportation commande PrepImport pour la première session de copie copier un fichier :

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

L’outil client Azure importation/exportation commande PrepImport pour les sessions ultérieures de copie copier un fichier :

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**N’oubliez pas**: par défaut, les données seront importées en tant que BLOB de bloc. Vous pouvez utiliser le paramètre /BlobType pour importer des données sous la forme d’un BLOB de la Page. Par exemple, si vous importez des fichiers de disque dur virtuel qui seront montés en tant que disques sur un ordinateur virtuel d’Azure, vous devez les importer en tant que BLOB de la Page. Si vous ne savez pas quel blob de type à utiliser, vous pouvez spécifier /blobType:auto et nous aidera à déterminer le type de droit. Dans ce cas, tous les fichiers de disque dur virtuel et vhdx seront importés en tant que BLOB de la Page et le reste sera importé en tant que BLOB de bloc.

Voir plus de détails sur l’utilisation de l’outil client Azure importer/exporter dans la [Préparation de disques durs pour l’importation](https://msdn.microsoft.com/library/dn529089.aspx).

En outre, reportez-vous à l' [Exemple de flux de travail pour préparer le disque dur pour une tâche d’importation](https://msdn.microsoft.com/library/dn529097.aspx) pour des instructions plus détaillées.  

### <a name="create-the-import-job"></a>Créer la tâche d’importation

1.  Une fois que vous avez préparé votre lecteur, accédez à votre compte de stockage dans le [portail de classique](https://manage.windowsazure.com) et d’afficher le tableau de bord. Sous l' **Aperçu rapide**, cliquez sur **créer une tâche d’importation**. Passez en revue les étapes et activez la case à cocher pour indiquer que vous avez préparé votre lecteur et que vous disposez du fichier de journal de lecteur disponible.

2.  À l’étape 1, fournissent des informations de contact de la personne responsable de cette tâche d’importation et une adresse d’expéditeur valide. Si vous souhaitez enregistrer les données du journal détaillé de la tâche d’importation, activez l’option Enregistrer **le journal en clair dans le conteneur blob 'waimportexport'**.

3.  À l’étape 2, télécharger des fichiers du disque de journal que vous avez obtenu lors de l’étape de préparation du lecteur. Vous devez télécharger un fichier pour chaque disque que vous avez préparés.

    ![Créer tâche d’importation - étape 3](./media/storage-import-export-service/import-job-03.png)

4.  À l’étape 3, entrez un nom descriptif pour la tâche d’importation. Notez que le nom que vous entrez peut contenir uniquement des lettres minuscules, des nombres, des traits d’union et des traits de soulignement, doit commencer par une lettre et ne peut pas contenir d’espaces. Vous allez utiliser le nom que vous choisissez d’effectuer le suivi de vos travaux lorsqu’ils sont en cours et une fois qu’elles sont terminées.

    Ensuite, sélectionnez la région de votre centre de données à partir de la liste. La région de centre de données indique le centre de données et l’adresse à laquelle vous devez expédier votre package. Consultez le Forum aux questions ci-dessous pour plus d’informations.

5.  À l’étape 4, sélectionnez votre opérateur de retour dans la liste et entrez votre numéro de compte du transporteur. Microsoft utilisera ce compte pour expédier les lecteurs vers vous lorsque votre tâche d’importation est terminée.

    Si vous avez votre numéro de suivi, sélectionnez votre opérateur de livraison à partir de la liste et entrez votre numéro de suivi.

    Si vous ne pas disposer d’un numéro de suivi mais, choisissez **je fournirai mes informations de livraison pour ce travail d’importation une fois que j’ai expédié mon package**, puis terminez le processus d’importation.

6. Pour entrer votre numéro de suivi une fois que vous avez envoyé votre package, retour à la page **d’Importation/exportation** pour votre compte de stockage dans le portail classique, sélectionner votre travail dans la liste et les informations **De livraison**. Dans l’Assistant et entrez votre numéro de suivi à l’étape 2.

    Si le numéro de suivi n’est pas mis à jour dans les 2 semaines de la création de la tâche, la tâche expire.

    Si la tâche est dans l’état de création, de livraison ou de transfert, vous pouvez également mettre à jour votre numéro de compte de porteuse à l’étape 2 de l’Assistant. Une fois la tâche dans l’état de l’emballage, vous ne peut pas mettre à jour votre numéro de compte de transporteur pour ce travail.

7. Vous pouvez suivre la progression du travail sur le tableau de bord de portail. Voir la signification de chaque état de la tâche dans la section précédente en [affichant l’état de votre travail](#viewing-your-job-status).

## <a name="how-to-create-an-export-job"></a>Comment faire pour créer une tâche d’exportation ?

Créer une tâche d’exportation pour avertir le service d’importation/exportation que vous allez proposer un ou plusieurs lecteurs vides au centre de données afin que les données peuvent être exportées à partir de votre compte de stockage pour les lecteurs et les lecteurs, puis expédiées.

### <a name="prepare-your-drives"></a>Préparer vos lecteurs

Les vérifications préalables suivantes sont recommandées pour la préparation de vos disques pour une tâche d’exportation :

1. Vérifiez le nombre de disques requis à l’aide de la commande de PreviewExport de l’outil d’importation/exportation d’Azure. Pour plus d’informations, voir [Aperçu de l’utilisation de lecteur pour une tâche d’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx). Il vous permet d’afficher un aperçu de l’utilisation du lecteur pour les objets BLOB que vous avez sélectionné, en fonction de la taille des disques que vous allez utiliser.

2. Vérifiez que vous pouvez en lecture/écriture sur le disque dur qui sera commercialisé pour la tâche d’exportation.

### <a name="create-the-export-job"></a>Créer la tâche d’exportation

1.  Pour créer une tâche d’exportation, accédez à votre compte de stockage dans le [portail de classique](https://manage.windowsazure.com)et d’afficher le tableau de bord. Sous l' **Aperçu rapide**, cliquez sur **créer une tâche d’exportation** et suivez les étapes de l’Assistant.

2.  À l’étape 2, fournir des informations de contact de la personne responsable de cette tâche d’exportation. Si vous souhaitez enregistrer les données du journal détaillé de la tâche d’exportation, activez l’option Enregistrer **le journal en clair dans le conteneur blob 'waimportexport'**.

3.  À l’étape 3, spécifiez les données de blob à exporter à partir de votre compte de stockage pour l’ou les lecteurs vides. Vous pouvez choisir d’exporter toutes les données d’objet blob dans le compte de stockage, ou vous pouvez spécifier laquelle BLOB ou définit des objets BLOB à exporter.

    Pour spécifier un objet blob à exporter, utilisez le sélecteur **Égal à** et spécifiez le chemin d’accès relatif à l’objet blob, en commençant par le nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.

    Pour spécifier tous les BLOB commençant par un préfixe, utilisez le sélecteur **Commence par** et spécifiez le préfixe, commençant par une barre oblique « / ». Le préfixe peut être le préfixe le nom du conteneur, le nom du conteneur complet ou le nom du conteneur complète suivie par le préfixe du nom de l’objet blob.

    Le tableau suivant montre des exemples de chemins d’accès de l’objet blob valide :

    Sélecteur de|Chemin d’accès de l’objet BLOB|Description
    ---|---|---
    Commence par|/|Exporte tous les objets BLOB dans le compte de stockage
    Commence par|/$root /|Exporte tous les objets BLOB dans le conteneur racine
    Commence par|/Book|Exporte tous les objets BLOB dans n’importe quel conteneur qui commence par le préfixe **book**
    Commence par|/Music/|Exporte tous les objets BLOB dans le conteneur **musique**
    Commence par|musique/amour|Exporte tous les objets BLOB dans le conteneur **musique** qui commencent par le préfixe **d’amour**
    Égal à|$root/logo.bmp|Exportations blob **logo.bmp** dans le conteneur racine
    Égal à|videos/Story.mp4|Exportations blob **story.mp4** dans un conteneur, **vidéos**

    Vous devez fournir les chemins d’accès de l’objet blob dans les formats valides pour éviter les erreurs au cours du traitement, comme le montre cette capture d’écran.

    ![Créer tâche d’exportation - étape 3](./media/storage-import-export-service/export-job-03.png)


4.  À l’étape 4, entrez un nom descriptif pour la tâche d’exportation. Le nom que vous entrez peut contenir uniquement des lettres minuscules, des nombres, des traits d’union et des traits de soulignement, doit commencer par une lettre et ne peut pas contenir d’espaces.

    La région de centre de données indique le centre de données à laquelle vous devez expédier votre package. Consultez le Forum aux questions ci-dessous pour plus d’informations.

5.  À l’étape 5, sélectionnez votre opérateur de retour dans la liste et entrez votre numéro de compte du transporteur. Microsoft utilisera ce compte pour envoyer vos lecteurs de vous une fois votre tâche d’exportation s’est terminée.

    Si vous avez votre numéro de suivi, sélectionnez votre opérateur de livraison à partir de la liste et entrez votre numéro de suivi.

    Si vous ne pas disposer d’un numéro de suivi mais, choisissez **je fournirai mes informations de livraison pour ce travail d’exportation une fois que j’ai expédié mon package**, puis terminez le processus d’exportation.

6. Pour entrer votre numéro de suivi une fois que vous avez envoyé votre package, retour à la page **d’Importation/exportation** pour votre compte de stockage dans le portail classique, sélectionner votre travail dans la liste et les informations **De livraison**. Dans l’Assistant et entrez votre numéro de suivi à l’étape 2.

    Si le numéro de suivi n’est pas mis à jour dans les 2 semaines de la création de la tâche, la tâche expire.

    Si la tâche est dans l’état de création, de livraison ou de transfert, vous pouvez également mettre à jour votre numéro de compte de porteuse à l’étape 2 de l’Assistant. Une fois la tâche dans l’état de l’emballage, vous ne peut pas mettre à jour votre numéro de compte de transporteur pour ce travail.

    > [AZURE.NOTE] Si le blob à exporter est en cours d’utilisation au moment de la copie sur disque dur, service d’importation/exportation d’Azure prendre un instantané de l’objet blob et copier la capture instantanée.

7.  Vous pouvez suivre la progression du travail sur le tableau de bord dans le portail classique. Voir la signification de chaque état de la tâche dans la section précédente sur « Afficher votre statut de la tâche ».

8.  Après avoir reçu les lecteurs avec les données exportées, vous pouvez afficher et copier les clés BitLocker générés par le service pour votre lecteur. Accédez à votre compte de stockage dans le portail classique et cliquez sur l’onglet Importer/exporter. Sélectionnez votre tâche d’exportation de la liste, puis cliquez sur le bouton Afficher les clés. Les clés BitLocker s’affichent comme illustré ci-dessous :

    ![Afficher les clés BitLocker pour tâche d’exportation](.\media\storage-import-export-service\export-job-bitlocker-keys.png)

Passez en revue la section Forum aux questions ci-dessous car elle traite de questions les plus courantes rencontrés lors de l’utilisation de ce service.

## <a name="frequently-asked-questions"></a>Forum aux Questions ##


**Combien de temps faut-il pour copier mes données lorsque mes lecteurs atteint le centre de données ?**

Le temps de copier varie en fonction de différents facteurs tels que le type de tâche, type et taille des données copiées, la taille des disques fournis et existants de la charge de travail. Il peut varier de quelques jours à quelques semaines, en fonction de ces facteurs. Par conséquent, il est difficile de fournir une estimation générale. Le service essaie d’optimiser votre travail en copiant plusieurs disques en parallèle lorsque cela est possible. Si vous avez une tâche d’importation/exportation critique de temps, en contact avec nous pour une estimation.

**Quand dois-je utiliser le Service d’importation/exportation Azure ?**
Doit être envisagée à l’aide d’Azure importer exporter si le téléchargement via le réseau prend à peu près comme estimation plus de 7 jours. Vous pouvez calculer combien de temps prendra à l’aide d’une calculatrice en ligne ou en [téléchargement](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/archive/master.zip) celui situé dans notre Azure importation exportation reste exemple d’API de référentiel Azure échantillons au [Calculateur de vitesse de transfert de données](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html). Ce n’est pas un calcul exact, mais uniquement une indication approximative.

**Puis-je utiliser le service d’importation/exportation d’Azure avec un compte de gestionnaire de ressources de stockage ?**

Non, vous ne pouvez pas copier données vers ou à partir d’un compte Gestionnaire de ressources de stockage directement à l’aide du service d’importation/exportation d’Azure. Le service prend uniquement en charge les comptes de stockage classique. Prise en charge de gestionnaire de ressources stockage compte sera bientôt disponible. Comme alternative, vous pouvez importer des données dans un compte de stockage classique et migrer vers votre compte Gestionnaire de ressources de stockage à l’aide de [AzCopy](storage-use-azcopy.md) ou CopyBlob.

**Puis-je copier des fichiers Azure en utilisant le service d’importation/exportation d’Azure ?**

Non, le service d’importation/exportation d’Azure prend uniquement en charge les Blobs de bloc et de Blobs de Page. Tous les autres types de stockage, y compris des fichiers d’Azure, les Tables et les files d’attente ne sont pas pris en charge.

**Le service d’importation/exportation d’Azure n’est disponible que pour les abonnements de fournisseur de services cryptographiques ?**

Non, le service d’importation/exportation d’Azure ne gère pas les abonnements de fournisseur de services cryptographiques. La prise en charge sera ajoutée à l’avenir.

**Puis-je ignorer l’étape de préparation de lecteur d’une importation, ou puis-je préparer un lecteur sans copier ?**

N’importe quel lecteur que vous souhaitez expédier pour l’importation de données doit être préparé à l’aide de l’outil client Azure importation/exportation. Vous devez utiliser l’outil client pour copier les données sur le disque.

**Je dois effectuer une préparation de disques lors de la création d’une tâche d’exportation ?**

Aucune, mais certaines vérifications préalables sont recommandées. Vérifiez le nombre de disques requis à l’aide de la commande de PreviewExport de l’outil d’importation/exportation d’Azure. Pour plus d’informations, voir [Aperçu de l’utilisation de lecteur pour une tâche d’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx). Il vous permet d’afficher un aperçu de l’utilisation du lecteur pour les objets BLOB que vous avez sélectionné, en fonction de la taille des disques que vous allez utiliser. Vérifiez également que vous pouvez lire et écrire sur le disque dur qui sera commercialisé pour la tâche d’exportation.

**Que se passe-t-il si j’envoie par inadvertance un disque dur qui n’est pas conforme aux exigences pris en charge ?**

Le centre de données Azure renverra le lecteur qui n’est pas conforme aux exigences pris en charge pour vous. Si seulement certains lecteurs dans le package requise pour la prise en charge, ces disques seront traités, et les lecteurs qui ne remplissent pas les conditions vous être renvoyés.

**Puis-je annuler mon travail ?**

Vous pouvez annuler un travail lors de la création ou de la livraison de son état.

**Combien de temps puis-je afficher le statut des projets terminés dans portal de classique ?**

Vous pouvez afficher l’état pour les projets terminés pendant 90 jours. Tâches terminées seront supprimées au bout de 90 jours.

**Si vous souhaitez importer ou exporter de plus de 10 unités, que dois-je faire ?**

Une importation ou de la tâche d’exportation peut référencer uniquement 10 disques dans une seule tâche pour le service d’importation/exportation. Si vous voulez envoyer plus de 10 disques, vous pouvez créer plusieurs travaux. Les lecteurs qui sont associés à la même tâche doivent être expédiés ensemble dans le même package.

**Puis-je utiliser un adaptateur USB-SATA qui ne figure pas dans la liste recommandée ?**

Si vous avez un convertisseur qui n’est pas répertorié ci-dessus, vous pouvez essayer d’exécuter l’outil d’importation/exportation Azure pour préparer le disque et de voir si elle fonctionne avant d’acheter un convertisseur pris en charge à l’aide de votre convertisseur.

**Le service met en forme les disques avant de les retourner ?**

N° Tous les lecteurs sont chiffrés avec BitLocker.

**Puis-je acheter des lecteurs pour les tâches d’importation/exportation de Microsoft ?**

N° Vous devez expédier vos propres lecteurs pour les deux importer et exporter des tâches.

**Une fois la tâche d’importation est terminée, ce que mes données ressemblera dans le compte de stockage ? Sera préservé mon arborescence ?**

Lors de la préparation d’un disque dur d’une importation, la destination est spécifiée par le /dstdir : paramètre. Il s’agit du conteneur de destination dans le compte de stockage vers lequel les données du disque dur sont copiées. Partie de ce conteneur de destination, les répertoires virtuels sont créés pour les dossiers du disque dur et BLOB est créées pour les fichiers.

**Si le lecteur comporte des fichiers qui existent déjà dans mon compte de stockage, le service de remplacer mon compte de stockage des objets BLOB existants ?**

Lors de la préparation du lecteur, vous pouvez spécifier si les fichiers de destination doivent être remplacés ou ignorés en utilisant le paramètre appelé /Disposition : < renommer | non-remplacer | remplacer >. Par défaut, le service sera renommer les nouveaux fichiers plutôt que de remplacer les objets BLOB existants.

**L’outil client Azure importation/exportation n’est compatible avec les systèmes d’exploitation 32 bits ?**
N° L’outil client est uniquement compatible avec les systèmes d’exploitation Windows 64 bits. Reportez-vous à la section des systèmes d’exploitation dans les [conditions préalables](#pre-requisites) pour une liste complète des versions de système d’exploitation pris en charge.

**Dois-je inclure tout élément autre que le lecteur de disque dur dans mon package ?**

Veuillez expédier uniquement à vos disques durs. N’incluez pas d’éléments, tels que les câbles d’alimentation ou les câbles USB.

**Je dois expédier mes disques à l’aide de FedEx ou DHL ?**

Vous pouvez expédier des lecteurs au centre de données à l’aide de n’importe quel support connu comme FedEx, DHL, UPS ou US Postal Service. Toutefois, pour les lecteurs à vous à partir du centre de données d’envoi, vous devez fournir un numéro de compte FedEx aux États-Unis et l’Union européenne ou d’un numéro de compte DHL dans les régions Asie et en Australie.

**Y a-t-il des restrictions avec l’expédition à l’international de mon lecteur ?**

Veuillez noter que le média physique que vous fournissez doivent traverser des frontières internationales. Vous êtes chargé de veiller à ce que vos données et supports physiques sont importées et/ou exportées conformément à la législation applicable. Avant d’expédier le support physique, vérifiez vos conseillers pour vérifier que votre support et les données peuvent légalement être expédiées au centre de données identifié. Cela vous permet de vous assurer qu’elles atteignent Microsoft en temps voulu.

**Lorsque vous créez une tâche, l’adresse de livraison est un emplacement différent de l’emplacement de mes comptes de stockage. Que dois-je faire ?**

Certains des emplacements de stockage compte sont associés à des emplacements de livraison de remplacement. Précédemment des lieux d’expédition disponibles peuvent également être temporairement mappées à d’autres emplacements. Toujours vérifier l’adresse de livraison fournie lors de la création du projet avant la livraison de vos lecteurs.

**Pourquoi mon statut de la tâche dans le standard portail dites « expédition » lorsque le site Web du transporteur montre mon package est livré ?**

Le statut dans le portail classique passe de livraison transfert lorsque le lecteur de démarrage du traitement. Si votre lecteur a atteint la facilité, mais n’a pas commencé de traitement, votre statut de la tâche affiche en tant que livraison.

**Lors de l’expédition de mon lecteur, le transporteur demande le nom de contact de centre de données et d’un numéro de téléphone. Que dois-je fournir ?**

Le numéro de téléphone est fourni lors de la création de la tâche. Si vous avez besoin d’un nom de contact, veuillez nous contacter à waimportexport@microsoft.com et nous vous fournira ces informations.

**Puis-je utiliser le service Azure importer/exporter pour copier des boîtes aux lettres des fichiers PST et les données SharePoint vers O365 ?**

Veuillez consulter [les fichiers PST d’importation ou les données SharePoint dans Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Puis-je utiliser le service Azure importer/exporter pour copier mes sauvegardes en mode hors connexion pour le Service de sauvegarde Azure ?**

Reportez-vous au [processus de sauvegarde en mode hors connexion dans Azure sauvegarde](../backup/backup-azure-backup-import-export.md).

## <a name="see-also"></a>Voir aussi :

- [Configuration de l’outil client Azure importation/exportation](https://msdn.microsoft.com/library/dn529112.aspx)

- [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

- [Exemple d’API reste Azure importation exportation](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
