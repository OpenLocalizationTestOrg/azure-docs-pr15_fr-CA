<properties
    pageTitle="Comment faire pour créer une image de modèle personnalisé pour Azure RemoteApp | Microsoft Azure"
    description="Apprenez à créer une image de modèle personnalisé pour Azure RemoteApp. Vous pouvez utiliser ce modèle avec un hybride ou un nuage de collection."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-custom-template-image-for-azure-remoteapp"></a>Comment faire pour créer une image de modèle personnalisé pour Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

RemoteApp Azure utilise une image du modèle Windows Server 2012 R2 pour héberger tous les programmes que vous souhaitez partager avec les utilisateurs. Pour créer une image de modèle RemoteApp personnalisée, vous pouvez démarrer avec une image existante ou créer une nouvelle. 


> [AZURE.TIP] Saviez-vous que vous pouvez créer une image à partir d’un ordinateur virtuel d’Azure ? Une histoire vraie et il permet de réduire la durée pendant laquelle il prend pour importer l’image. Découvrez les étapes [ici](remoteapp-image-on-azurevm.md).

La configuration requise pour l’image qui peut être téléchargé pour une utilisation avec Azure RemoteApp est les suivantes :


- La taille de l’image doit être un multiple de MBs. Si vous essayez de télécharger une image qui n’est pas un multiple exact, le téléchargement échoue.
- La taille de l’image doit être de 127 Go ou plus petite.
- Il doit être sur un fichier de disque dur virtuel (fichiers VHDX [disques durs virtuels Hyper-V] ne sont pas actuellement pris en charge).
- Le disque dur virtuel ne doit pas être une machine virtuelle de génération 2.
- Le disque dur virtuel peut être de taille fixe ou dynamique. Un disque dur virtuel de taille dynamique est recommandé parce qu’il prend moins de temps à télécharger vers Azure qu’un fichier de disque dur virtuel de taille fixe.
- Le disque doit être initialisé à l’aide de l’enregistrement de démarrage principal (MBR) style de partitionnement. Le style de partition GUID partition table (GPT) n’est pas pris en charge.
- Le disque dur virtuel doit contenir une seule installation de Windows Server 2012 R2. Il peut contenir plusieurs volumes, mais un seul qui contient une installation de Windows.
- Le rôle de l’hôte de Session à distance Bureau (RDSH) et de la fonctionnalité Expérience Bureau doivent être installés.
- Le rôle de Broker pour les connexions Bureau à distance doit *pas* être installé.
- Le système EFS (ENCRYPTING File System) doit être désactivé.
- L’image doit être préparée avec Sysprep en utilisant les paramètres **/oobe /generalize /shutdown** (ne pas utiliser le paramètre **/mode:vm** ).
- Téléchargement de votre disque dur virtuel à partir d’une chaîne d’instantané n’est pas pris en charge.


**Avant de commencer**

Vous devez effectuer les tâches suivantes avant de créer le service :

- [Inscrivez-vous](https://azure.microsoft.com/services/remoteapp/) pour RemoteApp.
- Créer un compte d’utilisateur dans Active Directory à utiliser comme compte de service de RemoteApp. Restreindre les autorisations de ce compte afin qu’il seulement peut joindre des ordinateurs au domaine. Pour plus d’informations, consultez [Configurer Azure Active Directory de RemoteApp](remoteapp-ad.md) .
- Rassembler des informations sur votre réseau local : adresse IP d’informations et de détails de périphérique VPN.
- Installez le module [PowerShell de Azure](../powershell-install-configure.md) .
- Rassembler des informations sur les utilisateurs que vous souhaitez accorder l’accès à. Ce peut être soit les informations de compte Microsoft ou les informations de compte de travail Active Directory pour les utilisateurs.



## <a name="create-a-template-image"></a>Créer une image du modèle ##

Voici les principales étapes à suivre pour créer une nouvelle image du modèle à partir de zéro :

1.  Recherchez une DVD de mise à jour de Windows Server 2012 R2 ou une image ISO.
2.  Créez un fichier de disque dur virtuel.
4.  Installez Windows Server 2012 R2.
5.  Installez le rôle de l’hôte de Session à distance Bureau (RDSH) et de la fonctionnalité Expérience Bureau.
6.  Installer des fonctionnalités supplémentaires requises par vos applications.
7.  Installer et configurer vos applications. Pour faciliter le partage d’applications, ajouter toutes vos applications ou les programmes que vous souhaitez partager dans le menu **Démarrer** de l’image, en particulier dans les **%systemdrive%\ProgramData\Microsoft\Windows\Start Démarrer\Programmes.
8.  Effectuer toutes les configurations Windows supplémentaires requises par vos applications.
9.  Désactiver le système de fichiers EFS (Encrypting File System).
10. **Requise :** Accédez à Windows Update et installer les mises à jour importantes.
9.  L’image SYSPREP.

Les étapes détaillées de création d’une image sont les suivants :

1.  Recherchez une DVD de mise à jour de Windows Server 2012 R2 ou une image ISO.
2.  Créez un fichier de disque dur virtuel à l’aide du composant Gestion des disques.
    1.  Lancement de gestion des disques (diskmgmt.msc).
    2.  Créer un disque dur virtuel de taille dynamique de 40 Go ou plus. (D’estimer la quantité d’espace nécessaire pour Windows, vos applications et des personnalisations. Windows serveur avec le rôle RDSH et de la fonctionnalité Expérience Bureau installée nécessite environ 10 Go d’espace).
        1.  Cliquez sur **Action > créer le disque dur virtuel**.
        2.  Spécifier l’emplacement, la taille et le format de disque dur virtuel. Sélectionnez **l’extension dynamique**, puis cliquez sur **OK**.

            Il s’exécute pendant quelques secondes. Lors de la création du disque dur virtuel est terminée, vous devriez voir un nouveau disque sans aucune lettre de lecteur et en état de « Not initialized » dans la console Gestion des disques.

        - Cliquez sur le disque (et pas dans l’espace non alloué), puis cliquez sur **Initialiser le disque**. Sélectionnez le style de partition **MBR** (Master Boot Record), puis cliquez sur **OK**.
        - Créer un nouveau volume : cliquez sur l’espace non alloué, puis cliquez sur **Nouveau Volume Simple**. Vous pouvez accepter les valeurs par défaut dans l’Assistant, mais veillez à qu'assigner une lettre de lecteur afin d’éviter les problèmes potentiels lorsque vous téléchargez l’image du modèle.
        - Cliquez sur le disque, puis cliquez sur **Détacher un disque dur virtuel**.





1. Installer Windows Server 2012 R2 :
    1. Créer un ordinateur virtuel. Utilisez l’Assistant Nouvel ordinateur virtuel Hyper-V Client ou Gestionnaire Hyper-v.
        1. Dans la page spécifier une génération, sélectionnez **génération 1**.
        2. Sur la page de connecter un disque dur virtuel, sélectionnez **utiliser un disque dur virtuel existant**et naviguez vers le disque dur virtuel que vous avez créé à l’étape précédente.
        2. Dans la page Options d’Installation, sélectionnez **installer un système d’exploitation à partir d’un CD/ce dernier de démarrage**et sélectionnez l’emplacement de votre support d’installation de Windows Server 2012 R2.
        3. Choisissez les autres options de l’Assistant d’installation de Windows et vos applications. Fin de l’Assistant.
    2.  Une fois l’Assistant terminé, modifier les paramètres de l’ordinateur virtuel et apportez les modifications nécessaires à l’installation de Windows et vos programmes, telles que le nombre de processeurs virtuels et puis cliquez sur **OK**.
    4.  Se connecter à l’ordinateur virtuel et installez Windows Server 2012 R2.
1. Installer le rôle de l’hôte de Session à distance Bureau (RDSH) et de la fonctionnalité Expérience Bureau :
    1. Lancez le Gestionnaire de serveur.
    2. Cliquez sur **Ajouter des rôles et fonctionnalités** sur l’écran de bienvenue ou du menu **Gérer** .
    3. Sur la page avant de commencer, cliquez sur **suivant** .
    4. Sélectionnez **installation basée sur les rôles ou basé sur la fonctionnalité**, puis cliquez sur **suivant**.
    5. Sélectionnez l’ordinateur local dans la liste, puis cliquez sur **suivant**.
    6. Sélectionnez les **Services Bureau à distance**, puis cliquez sur **suivant**.
    7. Développez des **Interfaces utilisateur et l’Infrastructure** et sélectionnez la **Fonctionnalité expérience utilisateur**.
    8. Cliquez sur **Ajouter des fonctionnalités**, puis cliquez sur **suivant**.
    9. Sur la page des Services Bureau à distance, cliquez sur **suivant**.
    10. Cliquez sur **hôte de Session Bureau à distance**.
    11. Cliquez sur **Ajouter des fonctionnalités**, puis cliquez sur **suivant**.
    12. Dans la page Options d’installation de confirmer, sélectionnez **redémarrer le serveur de destination automatiquement si nécessaire**et puis cliquez sur **Oui** dans l’avertissement de redémarrage.
    13. Cliquez sur **installer**. L’ordinateur va redémarrer.
1.  Installer des fonctionnalités supplémentaires requises par vos applications, comme le 3.5 de.NET Framework. Pour installer les fonctionnalités, exécutez l’ajout de rôles et fonctionnalités Assistant.
7.  Installer et configurer les programmes et les applications que vous souhaitez publier par le biais de RemoteApp.

>[AZURE.IMPORTANT]
>
>Installez le rôle RDSH avant d’installer les applications afin de garantir que les problèmes de compatibilité des applications sont découverts avant que l’image est téléchargée de RemoteApp.
>
>Assurez-vous qu’un raccourci vers votre application (fichier**.lnk** ) apparaît dans le menu **Démarrer** pour tous les utilisateurs (%systemdrive%\ProgramData\Microsoft\Windows\Start Démarrer\Programmes). Assurez-vous également que l’icône que s’affiche dans le menu **Démarrer** est ce que vous souhaitez que les utilisateurs voient. Si ce n’est pas le cas, modifiez-la. (Vous *n’avez* à ajouter au début de l’application menu, mais il rend beaucoup plus facile de publier l’application de RemoteApp. Dans le cas contraire, vous devez fournir le chemin d’installation de l’application lorsque vous publiez l’application.)


8.  Effectuer toutes les configurations Windows supplémentaires requises par vos applications.
9.  Désactiver le système de fichiers EFS (Encrypting File System). Exécutez la commande suivante dans une fenêtre de commande avec élévation de privilèges :

        Fsutil behavior set disableencryption 1

    Sinon, vous pouvez définir ou ajouter la valeur DWORD suivante dans le Registre :

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.  Si vous générez votre image à l’intérieur d’une machine virtuelle Azure, renommez la ** \%windir%\Panther\Unattend.xml** de fichiers, comme le script de téléchargement utilisé ultérieurement à partir de travail sera bloqué. Modifier le nom de ce fichier à Unattend.old afin que vous aurez toujours le fichier dans le cas où vous auriez besoin de revenir de votre déploiement.
10. Accédez à Windows Update et installer les mises à jour importantes. Vous devrez peut-être exécuter plusieurs fois pour obtenir les mises à jour de Windows Update. (Parfois, vous installez une mise à jour, et cette mise à jour proprement dite nécessite une mise à jour).
10. L’image SYSPREP. À une invite de commandes avec élévation de privilèges, exécutez la commande suivante :

    **C:\Windows\System32\sysprep\sysprep.exe /generalize /oobe /shutdown**

    **Remarque :** N’utilisez pas le commutateur de **/mode:vm** de la commande SYSPREP, même s’il s’agit d’une machine virtuelle.


## <a name="next-steps"></a>Étapes suivantes ##
Maintenant que vous avez votre image du modèle personnalisé, vous devez télécharger cette image à votre collection de RemoteApp. Pour créer votre collection, utilisez les informations dans les articles suivants :


- [Comment créer une collection hybride de RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Comment créer une collection de nuage de RemoteApp](remoteapp-create-cloud-deployment.md)
 