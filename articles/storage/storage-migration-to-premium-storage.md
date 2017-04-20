<properties
    pageTitle="Migration vers Azure Premium stockage | Microsoft Azure"
    description="Migrer vos ordinateurs virtuels existants vers le stockage Azure Premium. Stockage de prime offre la prise en charge de disque hautes performances et à faible latence pour les charges d’e/S intensives sur Azure Virtual Machines en cours d’exécution."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="yuemlu"/>


# <a name="migrating-to-azure-premium-storage"></a>Migration vers un stockage Azure Premium

## <a name="overview"></a>Vue d’ensemble

Stockage de prime Azure offre une prise en charge de disque hautes performances et à faible latence pour les machines virtuelles exécutant des charges d’e/S intensives. En migrant des disques de mémoire virtuelle de votre application vers le stockage Azure Premium, vous pouvez tirer parti de la vitesse et les performances de ces disques.

L’objectif de ce guide est d’aider les nouveaux utilisateurs du stockage de prime Azure mieux à préparer à effectuer une transition en douceur à partir de leur système actuel pour le stockage de la prime. Le guide traite de trois composants clés dans ce processus de : 

  - [Planification de la Migration vers un stockage de prime](#plan-the-migration-to-premium-storage)
  - [Préparer et copier les disques durs virtuels (VHD) sur le stockage de la prime](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
  - [Créer Azure Virtual Machine à l’aide du stockage de prime](#create-azure-virtual-machine-using-premium-storage)

Vous pouvez migrer des machines virtuelles à partir d’autres plateformes vers le stockage Azure Premium ou migrer existant Azure VM à partir du stockage Standard pour le stockage de la prime. Ce guide couvre les étapes à suivre pour les deux scénarios. Suivez la procédure indiquée dans la section appropriée en fonction de votre scénario.

>[AZURE.NOTE] Vous trouverez une présentation des fonctionnalités et le prix du stockage de prime dans le stockage de prime : [Stockage hautes performances pour les charges de travail de Machine virtuelle Azure](storage-premium-storage.md). Nous vous recommandons de migrer n’importe quel disque de l’ordinateur virtuel nécessitant des e/s élevé pour le stockage Azure Premium pour des performances optimales pour votre application. Si votre disque ne nécessite pas d’e/s élevées, vous pouvez limiter les coûts par la mise à jour dans le stockage Standard, qui stocke les données de disque de machine virtuelle sur des disques durs (HDD) au lieu de SSDs.

À la fin du processus de migration dans son intégralité peut requérir une action supplémentaire avant et après les étapes décrites dans ce guide. Les exemples incluent la configuration des réseaux virtuels ou des points de terminaison ou d’apporter des modifications de code dans l’application elle-même, qui peut nécessiter un temps d’inactivité dans votre application. Ces actions sont propres à chaque application, et elles doivent être exécutées en même temps que les étapes décrites dans ce guide pour la transition de complet au stockage de prime aussi transparente que possible.


## <a name="plan-the-migration-to-premium-storage"></a>Planification de la migration vers un stockage de prime

Cette section permet de s’assurer que vous êtes prêt à suivre les étapes de la migration de cet article et vous aide à prendre la meilleure décision sur les types de machine virtuelle et le disque.

### <a name="prerequisites"></a>Conditions préalables
- Vous aurez besoin d’un abonnement Azure. Si vous n’en avez pas, vous pouvez créer un abonnement d’un mois [gratuite](https://azure.microsoft.com/pricing/free-trial/) ou visitez [Azure de tarification](https://azure.microsoft.com/pricing/) pour plus d’options.
- Pour exécuter les applets de commande PowerShell, vous devez le module PowerShell de Microsoft Azure. Pour les instructions d’installation et le point d’installation, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .
- Lorsque vous envisagez d’utiliser les ordinateurs virtuels d’Azure exécuté sur un stockage de prime, vous devez utiliser les ordinateurs virtuels capables de stockage de la prime. Vous pouvez utiliser des disques Standard et Premium stockage avec les machines virtuelles capables de stockage de la prime. Les disques de stockage de prime sera disponibles avec plusieurs types de machine virtuelle à l’avenir. Pour plus d’informations sur tous les types de disque Azure VM et les tailles disponibles, reportez-vous à la section [tailles pour les machines virtuelles](../virtual-machines/virtual-machines-windows-sizes.md) et les [tailles des Services en nuage](../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Considérations relatives à la

Un Azure VM prend en charge l’attachement plusieurs disques de stockage de prime afin que vos applications peuvent avoir jusqu'à 64 To de stockage par machine virtuelle. Avec le stockage de la prime, vos applications peuvent atteindre 80 000 e/s (entrées/sorties par seconde) par machine virtuelle et de 2000 Mo débit par seconde disque par machine virtuelle avec des latences extrêmement faibles pour les opérations de lecture. Vous disposez des options de plusieurs ordinateurs virtuels et des disques. Cette section est de vous aider à trouver une option qui correspond le mieux à votre charge de travail.

#### <a name="vm-sizes"></a>Tailles de machine virtuelle
Les spécifications de taille d’Azure VM sont répertoriées dans les [tailles pour les machines virtuelles](../virtual-machines/virtual-machines-windows-sizes.md). Passez en revue les caractéristiques de performances des machines virtuelles qui fonctionnent avec stockage de prime et de choisir la taille de mémoire virtuelle plus appropriée qui correspond le mieux à votre charge de travail. Assurez-vous qu’il existe suffisamment de bande passante disponible sur votre machine virtuelle pour le trafic de disque du lecteur.


#### <a name="disk-sizes"></a>Tailles de disque
Il existe trois types de disques qui peuvent être utilisés avec votre machine virtuelle, et chacun a des Ops ES/s et un débit limites. Tenir compte de ces limites lors de la sélection le type de disque de votre machine virtuelle en fonction des besoins de votre application en termes de capacité, de performances, d’évolutivité, et les charges maximum.

|Type de disque de stockage de prime|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Taille du disque|128 GO|512 GO|1 024 GO (1 TO)|
|Ops ES/s par disque|500|2300|5000|
|Débit par disque|100 Mo par seconde|150 Mo par seconde|200 Mo par seconde|

En fonction de votre charge de travail, déterminer si les disques de données supplémentaires sont nécessaires pour votre machine virtuelle. Vous pouvez attacher plusieurs disques de données permanentes de votre machine virtuelle. Si nécessaire, vous pouvez répartir sur les disques pour augmenter la capacité et les performances du volume. (Voir l’entrelacement [ici](storage-premium-storage-performance.md#disk-striping).) Si vous assemblez des disques de données de stockage de la prime à l’aide des [Espaces de stockage][4], vous devez la configurer avec une colonne pour chaque disque qui est utilisé. Dans le cas contraire, les performances globales du volume agrégé par bandes peuvent être inférieure que prévu en raison d’une répartition inégale du trafic entre les disques. Pour les machines virtuelles de Linux, vous pouvez utiliser l’utilitaire *mdadm* pour obtenir les mêmes. Voir l’article [Configurer le RAID logiciel sous Linux](../virtual-machines/virtual-machines-linux-configure-raid.md) pour plus de détails.

#### <a name="storage-account-scalability-targets"></a>Cibles de stockage compte l’évolutivité
Les comptes de stockage Premium ont les objectifs d’évolutivité suivants en plus des [cibles de performances et de l’évolutivité du stockage Azure](storage-scalability-targets.md). Si les exigences de votre application dépassent les cibles de l’évolutivité d’un compte de stockage unique, générer votre application pour utiliser plusieurs comptes de stockage et de partitionner vos données sur ces comptes de stockage.

|Total compte la capacité|Bande passante totale pour un compte de stockage redondants localement|
|:--|:---|
|Capacité disque : 35 To<br />Capacité de capture instantanée : 10 To|Jusqu'à 50 gigabits par seconde pour les entrant / sortant|

Pour plus d’informations sur les spécifications de stockage de la prime, Découvrez [l’évolutivité et des cibles de performances lors de l’utilisation du stockage de la prime](storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

#### <a name="disk-caching-policy"></a>Stratégie de cache de disque
Par défaut, la mise en cache de stratégie est *En lecture seule* pour toutes les données de prime disques et *En lecture-écriture* pour le disque du système d’exploitation Premium attaché à la machine virtuelle. Ce paramètre de configuration est recommandé pour obtenir des performances optimales pour votre application IOs. Pour les disques de données lourds-écriture ou en écriture seule (par exemple, les fichiers journaux de SQL Server), désactivez la mise en cache du disque afin que vous pouvez obtenir de meilleures performances. Les paramètres du cache pour les disques de données existants peuvent être mis à jour à l’aide [d’Azure Portal](https://portal.azure.com) ou le paramètre *- HostCaching* de l’applet de commande *Set-AzureDataDisk* .

#### <a name="location"></a>Emplacement
Sélectionnez l’emplacement où le stockage Azure Premium est disponible. Pour obtenir des informations sur les emplacements disponibles, reportez-vous à la section [Services Azure par région](https://azure.microsoft.com/regions/#services) . Ordinateurs virtuels situés dans la même région que le compte de stockage que magasins de disques pour la machine virtuelle donneront de meilleures performances que si elles sont dans des zones distinctes.

#### <a name="other-azure-vm-configuration-settings"></a>Autres paramètres de configuration Azure VM
Lorsque vous créez un ordinateur virtuel d’Azure, vous devrez configurer certains paramètres de la machine virtuelle. N’oubliez pas que certains paramètres sont fixés à la durée de vie de la machine virtuelle, alors que vous pouvez modifier ou ajouter d’autres ultérieurement. Passez en revue les paramètres de configuration Azure VM et assurez-vous qu’ils sont correctement configurés pour répondre aux besoins de votre charge de travail.

### <a name="optimization"></a>Optimisation

[Le stockage azure Premium : conception pour des performances élevées](storage-premium-storage-performance.md) fournit des indications pour la création d’applications hautes performances à l’aide de prime le stockage Azure. Vous pouvez suivre les instructions combinées avec les procédures recommandées applicables aux technologies utilisées par votre application.


## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Préparer et copier les disques durs virtuels (VHD) sur le stockage de la prime

La section suivante fournit des instructions pour la préparation des disques durs virtuels à partir de votre ordinateur virtuel et copie des disques durs virtuels pour le stockage Azure.

- [Scénario 1 : « je suis migration machines virtuelles Azure existantes vers un stockage Azure Premium ».](#scenario1)
- [Scénario 2 : « je suis migration des ordinateurs virtuels depuis d’autres plates-formes de stockage Azure de prime. »](#scenario2)

### <a name="prerequisites"></a>Conditions préalables

Pour préparer les disques durs virtuels pour la migration, vous devez :

- Un abonnement Azure, un compte de stockage et un conteneur dans ce compte de stockage à laquelle vous pouvez copier votre disque dur virtuel. Notez que le compte de stockage de destination peut être un compte Standard ou le stockage de la prime en fonction de vos besoins.
- Un outil permettant de généraliser le disque dur virtuel si vous envisagez de créer plusieurs instances de la machine virtuelle à partir de celui-ci. Par exemple, sysprep pour Windows ou sysprep de pointeurs pour Ubuntu.
- Un outil pour télécharger le fichier de disque dur virtuel pour le compte de stockage. Consultez le [transfert de données avec l’utilitaire de ligne de commande de AzCopy](storage-use-azcopy.md) , ou utilisez un [Explorateur de stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Ce guide décrit la copie de votre disque dur virtuel à l’aide de l’outil AzCopy.

> [AZURE.NOTE] Si vous choisissez l’option de copie synchrone avec AzCopy, pour des performances optimales, copiez votre disque dur virtuel en exécutant un de ces outils à partir d’un ordinateur virtuel Azure qui se trouve dans la même région que le compte de stockage de destination. Si vous copiez un disque dur virtuel à partir d’un ordinateur virtuel d’Azure dans une région différente, vos performances peuvent être lentes.
>
> Pour copier une grande quantité de données sur une bande passante limitée, envisagez [l’utilisation du service Azure importation/exportation pour transférer les données pour le stockage des objets Blob](storage-import-export-service.md); Vous pouvez ainsi transférer vos données par des lecteurs de disque dur livraison à un centre de données Azure. Vous pouvez utiliser le service Azure importer/exporter pour copier des données vers un compte de stockage standard uniquement. Une fois les données de votre compte de stockage standard, vous pouvez utiliser [l’API de copie Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou AzCopy pour transférer les données vers votre compte de stockage premium.
>
> Notez que Microsoft Azure prend uniquement en charge les fichiers de disque dur virtuel de taille fixe. Fichiers VHDX ou disques durs virtuels dynamiques ne sont pas pris en charge. Si vous avez un disque dur virtuel dynamique, vous pouvez convertir à l’aide de l’applet de commande [Convert-disque dur virtuel](http://technet.microsoft.com/library/hh848454.aspx) de taille fixe.

### <a name="scenario1"></a>Scénario 1 : « je suis migration machines virtuelles Azure existantes vers un stockage Azure Premium ».

Si vous faites migrer des machines virtuelles existantes de Azure, arrêter la machine virtuelle, préparer des disques durs virtuels par le type de disque dur virtuel que vous souhaitez et copiez-le sur le disque dur virtuel avec AzCopy ou PowerShell.

La machine virtuelle doit être complètement vers le bas pour faire migrer un état propre. Il y aura un temps d’arrêt jusqu'à ce que la migration se termine.

#### <a name="step-1-prepare-vhds-for-migration"></a>Étape 1. Préparation des disques durs virtuels pour la migration

Si vous migrez des machines virtuelles existantes de Azure pour stockage de prime, votre disque dur virtuel peut être :

- Une image de système d’exploitation généralisée
- Un disque unique du système d’exploitation
- Un disque de données

Ci-dessous, nous vous guidons à travers ces 3 scénarios pour la préparation de votre disque dur virtuel.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Un disque dur virtuel généralisée de système d’exploitation permet de créer plusieurs instances de machine virtuelle

Si vous téléchargez un disque dur virtuel qui sera utilisé pour créer plusieurs instances d’Azure VM génériques, vous devez tout d’abord généraliser VHD à l’aide d’un utilitaire sysprep. Cela s’applique à un disque dur virtuel qui est local ou dans le nuage. Sysprep supprime les informations spécifiques à l’ordinateur à partir du disque dur virtuel.

>[AZURE.IMPORTANT] Prendre un instantané ou sauvegarder votre machine virtuelle avant la généralisation il. L’exécution de sysprep arrêtera et désallouer l’instance de la machine virtuelle. Suivez les étapes ci-après pour sysprep, un disque dur virtuel de Windows du système d’exploitation. Notez que l’exécution de la commande Sysprep nécessite que vous arrêtez l’ordinateur virtuel. Pour plus d’informations sur Sysprep, consultez [Vue d’ensemble de Sysprep](http://technet.microsoft.com/library/hh825209.aspx) ou [Techniques de référence de Sysprep](http://technet.microsoft.com/library/cc766049.aspx).

1. En tant qu’administrateur, ouvrez une fenêtre d’invite de commande.
2. Entrez la commande suivante pour ouvrir Sysprep :

        %windir%\system32\sysprep\sysprep.exe

3. Dans l’outil de préparation système, sélectionnez entrer le système de Out-of-Box expérience (OOBE), activez la case à cocher Generalize, sélectionnez **Arrêter le système**et puis cliquez sur **OK**, comme illustré dans l’image ci-dessous. Sysprep généralise le système d’exploitation et arrêter le système.

    ![][1]

Pour une VM Ubuntu, utiliser les pointeurs-sysprep pour atteindre la même. Consultez les [pointeurs-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) pour plus de détails. Voir aussi certaines open source [software de provisionnement des serveurs Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) pour d’autres systèmes d’exploitation Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Utilisez un disque dur virtuel unique de système d’exploitation pour créer une instance unique de la machine virtuelle

Si vous avez une application en cours d’exécution sur l’ordinateur virtuel qui requiert les données des ordinateurs spécifiques, ne généralisez pas le disque dur virtuel. Un disque dur virtuel non généralisée peut être utilisé pour créer une instance d’Azure VM unique. Par exemple, si vous avez un contrôleur de domaine sur votre disque dur virtuel, l’exécution de sysprep rend inefficaces comme contrôleur de domaine. Passez en revue les applications en cours d’exécution sur votre ordinateur virtuel et de l’impact de l’exécution de sysprep sur eux avant la généralisation du disque dur virtuel.

##### <a name="register-data-disk-vhd"></a>Enregistrer les données de disque dur

Si vous avez des disques de données dans Azure à migrer, il se peut que vous devez vous assurer que les ordinateurs virtuels qui utilisent ces disques de données sont en cours d’arrêt.

Suivez les étapes décrites ci-après pour copier un disque dur virtuel pour le stockage Azure Premium et l’enregistrer sous la forme d’un disque de données mis en service.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Étape 2. Créez la destination de votre disque dur virtuel

Créer un compte de stockage pour la gestion de vos disques durs virtuels. Tenez compte des points suivants lorsque vous planifiez l’emplacement de stockage de vos disques durs virtuels :

- Primes de stockage cible.
- L’emplacement du compte doit être identique au VMs Azure capable de stockage de prime, vous allez créer dans la phase finale. Vous pouvez copier vers un nouveau compte de stockage, ou envisagez d’utiliser le même compte de stockage selon vos besoins.
- Copier et enregistrer la clé de compte de stockage du compte de stockage de destination pour l’étape suivante.

Pour les disques de données, vous pouvez choisir de conserver les disques de certaines données dans un compte de stockage standard (par exemple, les disques qui disposent de stockage REFROIDISSEUR), mais nous vous recommandons vivement déplacer toutes les données de la charge de travail d’utiliser le stockage de la prime.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Étape 3. Copie de disque dur virtuel avec AzCopy ou PowerShell

Vous devez trouver votre clé conteneur de compte chemin d’accès et le stockage pour traiter une ou l’autre de ces deux options. Vous trouverez la clé de compte de stockage et le chemin d’accès au conteneur dans **Azure Portal** > **stockage**. L’URL de conteneur sera comme « https://myaccount.blob.core.windows.net/mycontainer/ ».

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Option 1 : Copie d’un disque dur virtuel avec AzCopy (copie asynchrone)

À l’aide de AzCopy, vous pouvez facilement le télécharger le disque dur virtuel sur Internet. Selon la taille des VHD, cela peut prendre de temps. N’oubliez pas de vérifier les limites d’ENTREE/sortie de compte de stockage lors de l’utilisation de cette option. Pour plus d’informations, consultez [cibles de performances et de l’évolutivité du stockage Azure](storage-scalability-targets.md) .

1. Téléchargez et installez AzCopy à partir d’ici : [dernière version de AzCopy](http://aka.ms/downloadazcopy)
2. Ouvrez Azure PowerShell et accédez au dossier où est installé le AzCopy.
3. Utilisez la commande suivante pour copier le fichier de disque dur virtuel à partir de « Source » à « Destination ».

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Exemple :

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd

    Voici une description des paramètres utilisés dans la commande AzCopy :

 - * */Source : * &lt;source de&gt;:*** emplacement de dossier ou de l’URL de conteneur de stockage qui contient le disque dur virtuel.
 - * */SourceKey : * &lt;clé de compte source&gt;:*** clé de compte de stockage du compte de stockage source.
 - * */Dest : * &lt;destination&gt;:*** URL de conteneur de stockage à copier du disque dur virtuel à.
 - * */DestKey : * &lt;clé-dest-compte&gt;:*** clé de compte de stockage du compte de stockage de destination.
 - * */Motif : * &lt;nom de fichier&gt;:*** spécifier le nom du fichier de disque dur virtuel pour copier.

Pour plus d’informations sur l’utilisation d’outil de AzCopy, reportez-vous à [transférer des données avec l’utilitaire de ligne de commande de AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Option 2 : Copier un disque dur virtuel avec PowerShell (copie synchronisée)

Vous pouvez également copier le fichier de disque dur virtuel à l’aide de l’applet de commande PowerShell début-AzureStorageBlobCopy. Utilisez la commande suivante sur Azure PowerShell à copier du disque dur virtuel. Remplacez les valeurs de <> avec les valeurs correspondantes à partir de votre compte de stockage source et de destination. Pour utiliser cette commande, vous devez disposer d’un conteneur appelé des disques durs virtuels dans votre compte de stockage de destination. Si le conteneur n’existe pas, créez-le avant d’exécuter la commande.

    $sourceBlobUri = <source-vhd-uri>

    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext

Exemple :

    C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

    C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

    C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

    C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### <a name="scenario2"></a>Scénario 2 : « je suis migration des ordinateurs virtuels depuis d’autres plates-formes de stockage Azure de prime. »

Si vous migrez VHD de stockage non Azure Cloud sur Azure, vous devez d’abord exporter le disque dur virtuel à un répertoire local. Que le chemin source complet du répertoire local où le disque dur virtuel est stocké sous la main et ensuite à l’aide de AzCopy du pour télécharger vers le stockage Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Étape 1. Exporter un disque dur virtuel à un répertoire local

##### <a name="copy-a-vhd-from-aws"></a>Copier d’un disque dur virtuel à partir de l’AWS

1. Si vous utilisez l’AWS, exporter l’instance EC2 vers un disque dur virtuel dans un compartiment d’Amazon S3. Suivez les étapes décrites dans la documentation d’Amazon pour exporter les Instances Amazon EC2 installer l’outil de l’interface de ligne de commande (CLI) d’Amazon EC2 et exécutez la commande create-instance-exportation-tâche pour exporter l’instance EC2 dans un fichier de disque dur virtuel. Veillez à utiliser un **disque dur virtuel** pour le disque #95 ; IMAGE & #, 95 ; Variable FORMAT lors de l’exécution de la commande **Créer instance-exportation-tâche** . Le fichier de disque dur virtuel exporté est enregistré dans le compartiment Amazon S3 que vous désignez au cours de ce processus.

        aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
        --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX

2. Téléchargez le fichier de disque dur virtuel à partir de la plage de S3. Sélectionnez le fichier de disque dur virtuel, puis les **Actions** > **Télécharger**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copier d’un disque dur virtuel à partir d’autre nuage non-Azure

Si vous migrez VHD de stockage non Azure Cloud sur Azure, vous devez d’abord exporter le disque dur virtuel à un répertoire local. Copier le chemin source complet du répertoire local dans lequel est stocké le disque dur virtuel.

##### <a name="copy-a-vhd-from-on-premise"></a>Copier d’un disque dur virtuel de sur site

Si vous migrez un disque dur virtuel à partir d’un environnement sur site, vous devez le chemin source complet dans lequel est stocké le disque dur virtuel. Le chemin d’accès source peut être un partage de fichier ou emplacement de serveur.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Étape 2. Créez la destination de votre disque dur virtuel

Créer un compte de stockage pour la gestion de vos disques durs virtuels. Tenez compte des points suivants lorsque vous planifiez l’emplacement de stockage de vos disques durs virtuels :

- Le compte de stockage cible peut être standard ou haut de gamme de stockage en fonction de vos besoins d’application.
- La zone compte de stockage doit être identique au VMs Azure capable de stockage de prime, vous allez créer dans la phase finale. Vous pouvez copier vers un nouveau compte de stockage, ou envisagez d’utiliser le même compte de stockage selon vos besoins.
- Copier et enregistrer la clé de compte de stockage du compte de stockage de destination pour l’étape suivante.

Nous recommandons vivement vous déplacer toutes les données de la charge de travail d’utiliser le stockage de la prime.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Étape 3. Télécharger le disque dur virtuel au stockage Azure

Maintenant que vous avez votre disque dur virtuel dans le répertoire local, vous pouvez utiliser AzCopy ou AzurePowerShell pour télécharger le fichier .vhd de stockage Azure. Les deux options sont fournies ici :

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Option 1 : À l’aide d’Azure PowerShell Add-AzureVhd pour télécharger le fichier .vhd

    Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>

Un exemple de <Uri> peut être **_« https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd »_**. Un exemple de <FileInfo> peut être **_« C:\path\to\upload.vhd »_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Option 2 : À l’aide de AzCopy pour télécharger le fichier .vhd

À l’aide de AzCopy, vous pouvez facilement le télécharger le disque dur virtuel sur Internet. Selon la taille des VHD, cela peut prendre de temps. N’oubliez pas de vérifier les limites d’ENTREE/sortie de compte de stockage lors de l’utilisation de cette option. Pour plus d’informations, consultez [cibles de performances et de l’évolutivité du stockage Azure](storage-scalability-targets.md) .

1. Téléchargez et installez AzCopy à partir d’ici : [dernière version de AzCopy](http://aka.ms/downloadazcopy)
2. Ouvrez Azure PowerShell et accédez au dossier où est installé le AzCopy.
3. Utilisez la commande suivante pour copier le fichier de disque dur virtuel à partir de « Source » à « Destination ».

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Exemple :

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd

    Voici une description des paramètres utilisés dans la commande AzCopy :

 - * */Source : * &lt;source de&gt;:*** emplacement de dossier ou de l’URL de conteneur de stockage qui contient le disque dur virtuel.
 - * */SourceKey : * &lt;clé de compte source&gt;:*** clé de compte de stockage du compte de stockage source.
 - * */Dest : * &lt;destination&gt;:*** URL de conteneur de stockage à copier du disque dur virtuel à.
 - * */DestKey : * &lt;clé-dest-compte&gt;:*** clé de compte de stockage du compte de stockage de destination.
 - **/BlobType : page :** Spécifie que la destination est un blob de la page.
 - * */Motif : * &lt;nom de fichier&gt;:*** spécifier le nom du fichier de disque dur virtuel pour copier.

Pour plus d’informations sur l’utilisation d’outil de AzCopy, reportez-vous à [transférer des données avec l’utilitaire de ligne de commande de AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Autres options pour le transfert d’un disque dur virtuel

Vous pouvez également télécharger un disque dur virtuel à votre compte de stockage à l’aide d’un des moyens suivants :

- [Copie de stockage Azure Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Objets BLOB Azure Storage Explorer téléchargement](https://azurestorageexplorer.codeplex.com/)
- [Référence de l’API de stockage importation/exportation Service reste](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] Nous recommandons l’utilisation de Service d’importation/exportation si l’estimation du temps de téléchargement est supérieure à 7 jours. Vous pouvez utiliser [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) pour estimer le temps à partir de l’unité de taille et de transfert de données.
>
> Importation/exportation vous permet de copier dans un compte de stockage standard. Vous devrez copier à partir du stockage standard pour le compte de stockage premium à l’aide d’un outil tel que AzCopy.


## <a name="create-azure-virtual-machine-using-premium-storage"></a>Créer des ordinateurs virtuels d’Azure à l’aide du stockage de prime

Une fois le disque dur virtuel est téléchargé ou copié dans le compte de votre choix de stockage, suivez les instructions de cette section pour enregistrer le disque dur virtuel sous la forme d’une image de système d’exploitation, ou le disque du système d’exploitation en fonction de votre scénario et puis créer une instance de la machine virtuelle à partir de celui-ci. Le disque dur virtuel de disque de données peut être associé à la machine virtuelle une fois qu’il est créé. À la fin de cette section, vous trouverez un exemple de script de migration. Ce script simple ne correspond pas à tous les scénarios. Vous devez mettre à jour le script pour faire correspondre à votre scénario spécifique. Pour voir si ce script s’applique à votre scénario, consultez ci-dessous [Un exemple de Script de Migration](#a-sample-migration-script).

### <a name="checklist"></a>Liste de vérification

1.  Attendez que tous les disques dur copie est terminée.
2.  Vérifiez que le stockage de la prime n’est disponible dans la région que vous migrez vers.
3.  Décidez de la nouvelle série de machine virtuelle que vous utilisez. Il doit être un système de stockage Premium capable, et doit être en fonction de la disponibilité dans la région de la taille et en fonction de vos besoins.
4.  Décidez de la taille de mémoire virtuelle exacte que vous allez utiliser. Taille de mémoire virtuelle doit être assez grand pour prendre en charge des disques de données que vous avez. Par exemple Si vous avez 4 disques de données, l’ordinateur virtuel doit avoir des cœurs de 2 ou plus. Tenez également compte de mémoire la puissance de traitement et les besoins en bande passante du réseau.
5.  Créer un compte de stockage de la prime dans la région cible. Il s’agit du compte à qu'utiliser pour la nouvelle machine virtuelle.
6.  Avoir les détails de la machine virtuelle en cours pratiques, y compris la liste des disques et des objets BLOB de disque dur virtuel correspondant.

Préparer votre application pour les interruptions de service. Pour effectuer un nettoyage de la migration, vous devez arrêter le traitement dans le système actuel. Puis vous pouvez l’obtenir à un état cohérent qui vous pouvez migrer vers la nouvelle plate-forme. Temps d’arrêt dépend de la quantité de données sur les disques à migrer.

>[AZURE.NOTE] Si vous créez un gestionnaire de ressources de Azure VM à partir d’un disque VHD spécialisés, veuillez vous reporter à [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) de déploiement VM Gestionnaire de ressources à l’aide du disque existant.

### <a name="register-your-vhd"></a>Enregistrer votre disque dur virtuel

Pour créer un ordinateur virtuel à partir du disque dur virtuel de système d’exploitation ou d’attacher un disque de données pour un nouvel ordinateur virtuel, vous devez d’abord les enregistrer. Suivez les étapes ci-dessous en fonction du scénario de votre disque dur virtuel.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Généralisé de disque dur virtuel de système d’exploitation pour créer plusieurs instances d’Azure VM

Une fois l’image du système d’exploitation généralisée VHD est téléchargé vers le compte de stockage, enregistrer en tant qu’une **Image de machine virtuelle Azure** afin que vous pouvez créer une ou plusieurs instances de la machine virtuelle à partir de celui-ci. Utiliser les applets de commande PowerShell suivante pour enregistrer votre disque dur virtuel sous la forme d’une image de système d’exploitation de la machine virtuelle Azure. Indiquez l’URL complète de conteneur où le disque dur virtuel a été copié à.

    Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Copier et enregistrer le nom de cette nouvelle Image de machine virtuelle Azure. Dans l’exemple ci-dessus, il est *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disque dur virtuel unique de système d’exploitation pour créer une seule instance d’Azure VM

Une fois le disque dur virtuel unique de système d’exploitation est chargé pour le compte de stockage, l’enregistrer sous la forme d’un **Disque du système d’exploitation Azure** afin que vous pouvez créer une instance de la machine virtuelle à partir de celui-ci. Utilisez ces applets de commande PowerShell pour inscrire votre disque dur virtuel comme un disque du système d’exploitation Azure. Indiquez l’URL complète de conteneur où le disque dur virtuel a été copié à.

    Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Copier et enregistrer le nom de ce nouveau disque du système d’exploitation Azure. Dans l’exemple ci-dessus, il est *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Données disque VHD à joindre à la nouvelle instance d’Azure VM

Une fois le disque dur virtuel de disque de données est téléchargé pour le compte de stockage, l’enregistrer sous la forme d’un disque de données Azure afin qu’il peut être joint à votre nouvelle gamme DS, DSv2 série ou une instance d’Azure VM de la série GS.

Utilisez ces applets de commande PowerShell pour inscrire votre disque dur virtuel comme un disque de données Azure. Indiquez l’URL complète de conteneur où le disque dur virtuel a été copié à.

    Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Copier et enregistrer le nom de ce nouveau disque de données Azure. Dans l’exemple ci-dessus, il est *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Créer un ordinateur virtuel capable de stockage de la prime

Une fois l’image du système d’exploitation ou de disque du système d’exploitation sont enregistrés, créer une nouvelle série DS, la série DSv2 ou la VM de la série GS. Vous utiliserez l’image du système d’exploitation ou le nom du disque système d’exploitation que vous avez enregistré. Sélectionnez le type de la machine virtuelle à partir de la couche de stockage de la prime. Dans l’exemple ci-dessous, nous utilisons la taille de la mémoire virtuelle *Standard_DS2* .

>[AZURE.NOTE] Mettre à jour la taille du disque afin de vous assurer qu’il correspond à la capacité et les exigences de performances et les tailles de disque Azure disponible.

Suivez les cmdlets PowerShell étape par étape ci-dessous pour créer la nouvelle machine virtuelle. Tout d’abord, définissez les paramètres communs :

    $serviceName = "yourVM"
    $location = "location-name" (e.g., West US)
    $vmSize ="Standard_DS2"
    $adminUser = "youradmin"
    $adminPassword = "yourpassword"
    $vmName ="yourVM"
    $vmSize = "Standard_DS2"

Tout d’abord, créez un service en nuage dans lequel vous hébergez vos nouveaux ordinateurs virtuels.

    New-AzureService -ServiceName $serviceName -Location $location

Ensuite, en fonction de votre scénario, créez l’instance d’Azure VM à partir de l’Image du système d’exploitation ou le disque du système d’exploitation que vous avez enregistré.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Généralisé de disque dur virtuel de système d’exploitation pour créer plusieurs instances d’Azure VM

Créer un ou plusieurs nouveaux Azure VM de la série DS instances à l’aide de l' **Image de l’OS Azure** que vous avez enregistré. Spécifier le nom de cette Image du système d’exploitation dans la configuration de la machine virtuelle lors de la création de nouvelle machine virtuelle comme indiqué ci-dessous.

    $OSImage = Get-AzureVMImage –ImageName "OSImageName"

    $vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

    Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

    New-AzureVM -ServiceName $serviceName -VM $vm

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disque dur virtuel unique de système d’exploitation pour créer une seule instance d’Azure VM

Créer une nouvelle instance de Azure VM de série DS utilise le **Disque du système d’exploitation Azure** que vous avez enregistré. Spécifier ce nom de disque du système d’exploitation dans la configuration de la machine virtuelle lors de la création de la nouvelle machine virtuelle comme indiqué ci-dessous.

    $OSDisk = Get-AzureDisk –DiskName "OSDisk"

    $vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

    New-AzureVM -ServiceName $serviceName –VM $vm

Spécifier d’autres informations Azure VM, un service en nuage, région, compte de stockage, ensemble de disponibilité, des stratégie de mise en cache. Notez que l’instance de la machine virtuelle doit être cohabiter avec le système d’exploitation associé ou des disques de données, le compte de service, la région et le stockage cloud sélectionnés doit être dans le même emplacement que les disques durs virtuels sous-jacente de ces disques.

### <a name="attach-data-disk"></a>Attacher le disque de données

Enfin, si vous avez enregistré des données disque VHD, les attacher à la nouvelle prime stockage capable Azure machine virtuelle.

Utilisez après l’applet de commande PowerShell pour attacher le disque de données vers la nouvelle machine virtuelle et de spécifier la stratégie de mise en cache. Dans l’exemple ci-dessous, la stratégie de mise en cache a la valeur *ReadOnly*.

    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

    Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

    Update-AzureVM  -VM $vm

>[AZURE.NOTE] Il peut y avoir des étapes supplémentaires pour prendre en charge de votre application ne doivent ne pas être couvertes par ce guide.

### <a name="checking-and-plan-backup"></a>Plan de sauvegarde et de vérification

Une fois la nouvelle machine virtuelle en cours d’exécution, y accéder en utilisant le même id de connexion et mot de passe est que l’ordinateur virtuel d’origine et vérifier que tout fonctionne comme prévu. Tous les paramètres, y compris les volumes agrégés par bandes, est présents dans la nouvelle machine virtuelle.

La dernière étape consiste à planifier la sauvegarde et la planification de la maintenance de la nouvelle machine virtuelle en fonction des besoins de l’application.

### <a name="a-sample-migration-script"></a>Un exemple de script de migration

Si vous avez plusieurs ordinateurs virtuels à migrer, automatisation via des scripts PowerShell s’avérera utile. Voici un exemple de script qui automatise la migration d’un ordinateur virtuel. Notez que sous le script n’est qu’un exemple et il existe quelques hypothèses concernant les disques VM en cours. Vous devez mettre à jour le script pour faire correspondre à votre scénario spécifique.

Les hypothèses sont les suivantes :

- Vous créez classique Azure VM.
- Vos disques de source du système d’exploitation et les disques de données source sont dans le même compte de stockage et même conteneur. Si votre système d’exploitation disques et données ne sont pas au même endroit, vous pouvez utiliser AzCopy ou Azure PowerShell pour copier les disques durs virtuels sur des conteneurs et des comptes de stockage. Reportez-vous à l’étape précédente : [Copie des VHD avec AzCopy ou PowerShell](#copy-vhd-with-azcopy-or-powershell). Modifier ce script pour répondre à votre scénario est un autre choix, mais nous recommandons l’utilisation de AzCopy ou PowerShell dans la mesure où il est plus facile et plus rapide.

Le script d’automation est fourni ci-dessous. Remplacez le texte par vos informations et mettre à jour le script pour faire correspondre à votre scénario spécifique.

>[AZURE.NOTE] À l’aide du script existant ne conserve pas la configuration du réseau de votre source de machine virtuelle. Vous devrez re-config les paramètres réseau de vos ordinateurs virtuels migrés.

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

#### <a name="optimization"></a>Optimisation

Votre configuration actuelle de la machine virtuelle peut être personnalisée spécifiquement pour fonctionner avec des disques Standard. Par exemple, pour augmenter les performances à l’aide de nombreux disques dans un volume agrégé par bandes. Par exemple, au lieu de 4 disques séparément sur le stockage de prime, vous pourrez peut-être optimiser le coût grâce à un seul disque. Optimisations telles que ce doivent être gérées sur une base cas par cas et nécessitent des étapes personnalisées après la migration. En outre, notez que ce processus ne fonctionne pas bien pour les bases de données et les applications qui dépendent de la disposition de disque définie dans le programme d’installation.

##### <a name="preparation"></a>Préparation

1.  Effectuer la Migration Simple comme décrit dans la section précédente. Optimisations seront effectuées sur la nouvelle machine virtuelle après la migration.
2.  Permet de définir les nouvelles tailles de disque nécessaires à la configuration optimisée.
3.  Déterminer le mappage de disques/volumes en cours pour les nouvelles spécifications du disque.

##### <a name="execution-steps"></a>Étapes de l’exécution

1.  Créer les nouveaux disques à la bonne taille sur l’ordinateur de stockage de prime virtuel.
2.  Connexion à la machine virtuelle et copie les données du volume en cours vers le nouveau disque qui mappe à ce volume. Cette opération pour tous les volumes en cours doivent mapper vers un nouveau disque.
3.  Ensuite, modifiez les paramètres de l’application pour basculer vers les nouveaux disques et détacher des anciens volumes.

Pour le réglage de l’application pour obtenir de meilleures performances de disque, reportez-vous à [Optimisation des performances de l’Application](storage-premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrations d’application

Bases de données et d’autres applications complexes peuvent nécessiter des étapes spéciales, tel que défini par le fournisseur de l’application pour la migration. Reportez-vous à la documentation de l’application respective. Par exemple généralement les bases de données peuvent être migrés par le biais de sauvegarde et de restauration.

## <a name="next-steps"></a>Étapes suivantes

Consultez les ressources suivantes pour des scénarios spécifiques pour la migration de machines virtuelles :

- [Migrer des Machines virtuelles Azure entre des comptes de stockage](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Créer et télécharger un disque dur virtuel du serveur de Windows Azure.](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md)
- [Création et chargement d’un disque dur virtuel qui contient le système d’exploitation Linux](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md)
- [Migration des Machines virtuelles à partir d’Amazon AWS à Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

En outre, consultez les ressources suivantes pour en savoir plus sur le stockage Azure et les ordinateurs virtuels Azure :

- [Stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [: Premium hautes performances de stockage pour les charges de travail des machines virtuelles Azure](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
