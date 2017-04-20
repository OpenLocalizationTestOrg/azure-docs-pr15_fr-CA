<properties
   pageTitle="Créer et télécharger une image de FreeBSD VM | Microsoft Azure"
   description="Apprenez à créer et télécharger un disque dur virtuel (VHD) qui contient le système d’exploitation FreeBSD pour créer un ordinateur virtuel Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="kyliel"/>

# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Créer et télécharger un VHD FreeBSD vers Azure

Cet article vous montre comment créer et télécharger un disque dur virtuel (VHD) qui contient le système d’exploitation FreeBSD. Après que l’avoir téléchargée, vous pouvez l’utiliser comme votre propre image pour créer une machine virtuelle (VM) dans Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous disposez des éléments suivants :

- **Azure un abonnement**--si vous n’avez pas un compte, vous pouvez en créer un en quelques minutes. Si vous avez un abonnement MSDN, consultez [crédit mensuel Azure pour les abonnés de Visual Studio.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Dans le cas contraire, découvrez comment [créer un compte d’essai gratuit](https://azure.microsoft.com/pricing/free-trial/).  

- **Outils d’azure PowerShell**--PowerShell d’Azure le module doit être installé et configuré pour utiliser votre abonnement Azure. Pour télécharger le module, reportez-vous à la section [téléchargements d’Azure](https://azure.microsoft.com/downloads/). Un didacticiel qui explique comment installer et configurer le module est disponible ici. Utilisez l’applet de commande [Azure téléchargements](https://azure.microsoft.com/downloads/) pour télécharger le disque dur virtuel.

- **Système d’exploitation FreeBSD dans un fichier .vhd**de--une prise en charge système d’exploitation FreeBSD doit être installé sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd. Par exemple, vous pouvez utiliser une solution de virtualisation telles que Hyper-V pour créer le fichier .vhd et installer le système d’exploitation. Pour obtenir des instructions sur la façon d’installer et d’utiliser Hyper-V, consultez [installer Hyper-V et créer un ordinateur virtuel](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Le nouveau format VHDX n’est pas pris en charge dans Azure. Vous pouvez convertir le disque au format de disque dur virtuel en utilisant le Gestionnaire Hyper-V ou l' applet de commande [convert-disque dur virtuel](https://technet.microsoft.com/library/hh848454.aspx). En outre, il est d’un [didacticiel sur MSDN relatives à l’utilisation de FreeBSD avec Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Cette tâche inclut les cinq étapes suivantes.

## <a name="step-1-prepare-the-image-for-upload"></a>Étape 1 : Préparation de l’image pour le téléchargement

Sur l’ordinateur virtuel où vous avez installé le système d’exploitation FreeBSD, procédez comme suit :

1. Activez le serveur DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart

2. Activez le protocole SSH.

    SSH est activé par défaut après l’installation à partir du disque. S’il n’est pas activé pour une raison quelconque, ou si vous utilisez FreeBSD VHD directement, tapez ce qui suit :

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart

3. Permet de paramétrer une console série.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. Installez sudo.

    Le compte de la racine est désactivé dans Azure. Cela signifie que vous devez utiliser sudo provenant d’un utilisateur sans privilège d’exécuter des commandes avec des privilèges élevés.

        # pkg install sudo
;
5. Conditions requises pour l’Agent Azure.

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git

6. Installez l’Agent Azure.

    La dernière version de l’Agent d’Azure peut toujours trouver sur [github](https://github.com/Azure/WALinuxAgent/releases). La version 2.0.10 + officiellement prise en charge de FreeBSD 10 et 10.1 et la version 2.1.4 prend officiellement en charge FreeBSD 10.2 et les versions ultérieures.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0, nous utiliserons 2.0.16 par exemple :

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1, nous utiliserons 2.1.4 par exemple :

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

    >[AZURE.IMPORTANT] Après l’installation de l’Agent d’Azure, il est conseillé de vérifier qu’il s’exécute :

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log

7. Deprovision le système.

    Deprovision le système pour nettoyer et adapté à la mise en service de nouveau. La commande suivante supprime également le dernier compte de l’utilisateur mis en service et les données associées :

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Vous pouvez maintenant arrêter votre machine virtuelle.

## <a name="step-2-create-a-storage-account-in-azure"></a>Étape 2 : Créer un compte de stockage dans Azure ##

Vous avez besoin d’un compte de stockage dans Azure pour télécharger un fichier .vhd afin qu’il puisse être utilisé pour créer un ordinateur virtuel. Vous pouvez utiliser le portail classique Azure pour créer un compte de stockage.

1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com).

2. Dans la barre de commandes, sélectionnez **Nouveau**.

3. Sélectionnez **les Services de données** > **stockage** > **Création rapide**.

    ![Création rapide d’un compte de stockage](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)

4. Renseignez les champs comme suit :

    - Dans le champ **URL** , tapez un nom de sous-domaine à utiliser dans l’URL de compte de stockage. L’entrée peut contenir des nombres et les lettres minuscules 24-3. Ce nom devient le nom d’hôte dans l’URL qui est utilisée pour adresser les Blob Azure stockage, stockage de la file d’attente d’Azure ou des ressources de stockage Azure Table pour l’abonnement.

    - Dans le menu déroulant **Groupe d’affinité des emplacements** , choisissez l' **emplacement ou affinité de groupe** pour le compte de stockage. Un groupe d’affinité vous permet de placer vos services en nuage et stockage dans le même centre de données.

    - Dans le champ de **réplication** , vous pouvez décider s’il faut utiliser la réplication **Redondante Geo** pour le compte de stockage. Geo-réplication est activée par défaut. Cette option réplique les données vers un emplacement secondaire, sans aucun frais pour vous, afin que votre stockage bascule vers cet emplacement si une panne majeure se produit à l’emplacement principal. Le site secondaire est automatiquement attribué et ne peut pas être modifié. Si vous avez besoin de davantage de contrôle sur l’emplacement de votre stockage en nuage en raison d’obligations juridiques ou la stratégie de l’organisation, vous pouvez désactiver géo-réplication. Toutefois, sachez que si vous activez ultérieurement géo-réplication, vous devrez payer des frais de cession des données uniques pour répliquer vos données à l’emplacement secondaire. Services de stockage sans géo-réplication sont proposés à un prix. Plus de détails sur la gestion des géo-réplication des comptes de stockage se trouve ici : [créer, gérer, ou de supprimer un compte de stockage](../storage-create-storage-account/#replication-options).

    ![Entrez les détails de compte de stockage](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)


5. Sélectionnez **créer le compte de stockage**. Le compte apparaît maintenant sous **stockage**.

    ![Compte de stockage a été correctement créé](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)

6. Ensuite, créez un conteneur pour vos fichiers .vhd téléchargé. Sélectionnez le nom de compte de stockage et sélectionnez **les conteneurs**.

    ![Détails de compte de stockage](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)

7. Sélectionnez **créer un conteneur**.

    ![Détails de compte de stockage](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)

8. Dans le champ **nom** , tapez un nom pour votre conteneur. Puis, dans le menu déroulant **accès** , sélectionnez le type de stratégie d’accès.

    ![Nom du conteneur](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] Par défaut, le conteneur est privé et n’être accessible que par le propriétaire du compte. Pour autoriser les publics d’accès en lecture pour les objets BLOB dans le conteneur, mais pas pour les propriétés du conteneur et les métadonnées, utilisez l’option **Public Blob** . Pour permettre un accès en lecture publique complète pour le conteneur et les objets BLOB, utilisez l’option de **Conteneur Public** .

## <a name="step-3-prepare-the-connection-to-azure"></a>Étape 3 : Préparer la connexion à Azure

Avant que vous pouvez télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Azure. Pour ce faire, vous pouvez utiliser la méthode Azure Active Directory (AD Azure) ou le certificat.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>La méthode AD Azure permet de télécharger un fichier .vhd

1. Ouvrez la console PowerShell d’Azure.

2. Tapez la commande suivante :  
    `Add-AzureAccount`

    Cette commande ouvre une fenêtre de connexion où vous pouvez vous connecter à votre compte de travail ou l’école.

    ![Fenêtre PowerShell](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure authentifie et enregistre les informations d’identification. Puis elle ferme la fenêtre.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>La méthode de certificat permet de télécharger un fichier .vhd

1. Ouvrez la console PowerShell d’Azure.

2. Type :  `Get-AzurePublishSettingsFile`.

3. Une fenêtre de navigateur s’ouvre et vous invite à télécharger un fichier .publishsettings. Ce fichier contient des informations et un certificat pour votre abonnement Azure.

    ![Page de téléchargement de navigateur](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Enregistrez le fichier .publishsettings.

4. Type :  `Import-AzurePublishSettingsFile <PathToFile>`, où `<PathToFile>` est le chemin d’accès complet au fichier .publishsettings.

   Pour plus d’informations, consultez [mise en route avec les applets de commande Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Pour plus d’informations sur l’installation et la configuration de PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

## <a name="step-4-upload-the-vhd-file"></a>Étape 4 : Télécharger le fichier .vhd

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n’importe où dans le stockage Blob. Voici quelques termes que vous utiliserez lorsque vous téléchargez le fichier :
-  **BlobStorageURL** est l’URL pour le compte de stockage que vous avez créé à l’étape 2.
-  **YourImagesFolder** est le conteneur dans le stockage Blob où vous souhaitez stocker vos images.
- **VHDName** est l’étiquette qui apparaît dans le portail Azure classique pour identifier le disque dur virtuel.
- **PathToVHDFile** est le chemin d’accès complet et le nom du fichier .vhd.


Dans la fenêtre PowerShell d’Azure que vous avez utilisé à l’étape précédente, tapez :

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Étape 5 : Créer un ordinateur virtuel avec le fichier téléchargé .vhd
Après avoir téléchargé le fichier .vhd, vous pouvez l’ajouter en tant qu’image à la liste des images personnalisées qui sont associés à votre abonnement et de créer une machine virtuelle avec cette image personnalisée.

1. Dans la fenêtre PowerShell d’Azure que vous avez utilisé à l’étape précédente, tapez :

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    > [AZURE.NOTE]Utilisez Linux comme type de système d’exploitation. La version actuelle de PowerShell d’Azure accepte uniquement « Linux » ou « Windows » en tant que paramètre.

2. Après avoir terminé les étapes précédentes, la nouvelle image apparaît lorsque vous cliquez sur l’onglet **Images** sur Azure portal classique.  

    ![Choisir une image](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Créer un ordinateur virtuel à partir de la galerie. Cette nouvelle image est maintenant disponible sous **Mes Images**.
4. Sélectionnez la nouvelle image. Ensuite, suivez les invites pour configurer un nom d’hôte, un mot de passe, un code SSH, etc..

    ![Image personnalisée](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Après avoir terminé la mise en service, vous verrez votre VM FreeBSD exécute dans Azure.

    ![Image de FreeBSD dans azure](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)
