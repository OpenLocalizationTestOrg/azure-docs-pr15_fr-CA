<properties
   pageTitle="Créer un package de prise en charge StorSimple | Microsoft Azure"
   description="Apprenez à créer, déchiffrer et modifier un lot de prise en charge de votre périphérique StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />


# <a name="create-and-manage-a-storsimple-support-package"></a>Créer et gérer un package de prise en charge de StorSimple

## <a name="overview"></a>Vue d’ensemble

Un package de prise en charge de StorSimple est un mécanisme de facile à utiliser qui collecte tous les journaux appropriés pour aider le Support Microsoft sur le dépannage des problèmes de périphérique StorSimple. Les fichiers journaux collectés sont cryptés et compressés.

Ce didacticiel comprend des instructions étape par étape pour créer et gérer la prise en charge.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Créer et télécharger un package de prise en charge dans Azure portal classique

Vous pouvez créer et télécharger un package de prise en charge pour le site du Support Microsoft par le biais de la page de **Maintenance** du service dans le portail classique Azure.

> [AZURE.NOTE] Le téléchargement nécessite une clé de la prise en charge. Ingénieur du support technique doit fournir à vous dans un message électronique.

Une prise en charge chiffré et compressé (fichier .cab) est créé et téléchargé sur le site de prise en charge. Le technicien du support technique peut alors extraire ce package à partir du site de prise en charge pour résoudre le problème.

Effectuez les opérations suivantes dans le portail classique pour créer un package de prise en charge.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Pour créer un package de prise en charge dans Azure portal classique

1. Sélectionnez les **périphériques** > **Maintenance**.

2. Dans la section **prise en charge du package** , sélectionnez **créer et télécharger les packages de support**.

3. Dans la boîte de dialogue **créer et téléchargement du package d’assistance** , effectuez le des opérations suivantes :

    ![Créer le package de prise en charge](./media/storsimple-create-manage-support-package/IC740923.png)

    - Dans la zone de texte **Clé de la prise en charge** , entrez la clé de sécurité. Ingénieur du support technique Microsoft doit envoyer cette clé d’accès pour vous dans le courrier électronique.

    - Activez la case à cocher pour fournir l’autorisation pour télécharger automatiquement le package de prise en charge pour le site de Support de Microsoft.

    - Cliquez sur l’icône de vérification ![Icône de contrôle](./media/storsimple-create-manage-support-package/IC740895.png).


## <a name="manually-create-a-support-package"></a>Créer manuellement un package de prise en charge

Dans certains cas, vous devrez créer manuellement le package de prise en charge par le biais de Windows PowerShell pour StorSimple. Par exemple :

- Si vous souhaitez supprimer des informations sensibles à partir de vos fichiers journaux avant de partager le support de Microsoft.

- Si vous avez des difficultés à télécharger le package en raison de problèmes de connectivité.

Vous pouvez partager votre package de prise en charge générée manuellement avec Support Microsoft par courrier électronique. Procédez comme suit pour créer un package de prise en charge de Windows PowerShell pour StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Pour créer un package de prise en charge de Windows PowerShell pour StorSimple

1. Pour démarrer une session Windows PowerShell en tant qu’administrateur sur l’ordinateur distant qui est utilisé pour se connecter à votre périphérique de StorSimple, entrez la commande suivante :

    `Start PowerShell`

2. Dans la session Windows PowerShell, vous connecter à la SSAdmin Console de votre périphérique :

    - À l’invite de commandes, entrez :

        `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`

    1. Dans la boîte de dialogue qui s’ouvre, entrez votre mot de passe administrateur. Le mot de passe par défaut est :

        `Password1`

        ![Boîte de dialogue informations d’identification de PowerShell](./media/storsimple-create-manage-support-package/IC740962.png)

    2. Sélectionnez **OK**.
    1. À l’invite de commandes, entrez :

        `Enter-PSSession $MS`

3. Dans la session qui s’ouvre, saisissez la commande appropriée.

    - Pour les partages réseau qui sont protégés par un mot de passe, entrez :

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

        Vous serez invité pour un mot de passe, un chemin d’accès au dossier partagé en réseau et un mot de passe de cryptage (parce que le package de prise en charge est chiffré). Un package de prise en charge est ensuite créé dans le dossier spécifié.

    - Pour les partages qui ne sont pas protégés par un mot de passe, vous n’avez pas besoin du `-Credential` paramètre. Entrez les informations suivantes :

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

        Le package de prise en charge est créé pour les deux contrôleurs dans le dossier partagé du réseau spécifié. Il s’agit d’un fichier compressé chiffré qui peut être envoyé au Support Microsoft de résolution des problèmes. Pour plus d’informations, voir [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).


### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Les paramètres d’applet de commande exportation-HcsSupportPackage
Vous pouvez utiliser les paramètres suivants avec l’applet de commande exportation-HcsSupportPackage.

| Paramètre            | Obligatoire ou facultatif | Description                                                                                                                                                             |
|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-Path`                 | Obligatoire          | Permet d’indiquer l’emplacement du dossier réseau partagé dans lequel le package de prise en charge est placé.                                                                 |
| `-EncryptionPassphrase` | Obligatoire          | Permet de fournir un mot de passe pour aider à chiffrer le package de prise en charge.                                                                                                        |
| `-Credential`           | Facultatif          | Permet de fournir des informations d’identification d’accès pour le dossier partagé du réseau.                                                                                        |
| `-Force`                | Facultatif          | Utilisez cette option pour ignorer l’étape de confirmation de mot de passe de cryptage.                                                                                                                |
| `-PackageTag`           | Facultatif          | Utilisez cette option pour spécifier un répertoire sous le *chemin d’accès* dans lequel le package de prise en charge est placé. La valeur par défaut est [nom du périphérique]-[date du jour et time:yyyy-MM-dd-HH-mm-ss].       |
| `-Scope`                | Facultatif          | Spécifiez en tant que **Cluster** (par défaut) pour créer un package de prise en charge pour les deux contrôleurs. Si vous souhaitez créer un package uniquement pour le contrôleur actuel, spécifiez le **contrôleur**. |


## <a name="edit-a-support-package"></a>Modification d’un lot de prise en charge

Une fois que vous avez généré un package de prise en charge, vous devrez modifier le lot pour supprimer des informations sensibles. Cela peut inclure les noms de volume, adresses IP de périphériques et sauvegarde des fichiers journaux.

> [AZURE.IMPORTANT] Vous ne pouvez modifier qu’un package de prise en charge qui a été généré par le biais de Windows PowerShell pour StorSimple. Vous ne pouvez pas modifier un lot créé dans le portail Azure classique avec le service Gestionnaire de StorSimple.

Pour modifier un package de prise en charge avant de le télécharger sur le site de Support de Microsoft, le package de prise en charge d’abord décrypter, modifier les fichiers, puis crypter à nouveau. Effectuez les opérations suivantes.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Pour modifier un package de prise en charge de Windows PowerShell pour StorSimple

1. Générer un package de prise en charge comme indiqué précédemment, pour [créer un package de prise en charge de Windows PowerShell pour StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).

2. [Télécharger le script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localement sur le client.

3. Importer le module de Windows PowerShell. Spécifiez le chemin d’accès du dossier local dans lequel vous avez téléchargé le script. Pour importer le module, entrez :

    `Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Tous les fichiers sont des fichiers *.aes* compressés et cryptés. Pour décompresser et décrypter des fichiers, entrez :

    `Open-HcsSupportPackage <Path to the folder that contains support package files>`

    Notez que les extensions de fichier sont maintenant affichées pour tous les fichiers.

    ![Modifier le package de prise en charge](./media/storsimple-create-manage-support-package/IC750706.png)

5. Lorsque vous êtes invité à entrer le mot de passe de cryptage, entrez le mot de passe que vous avez utilisé lorsque le package de prise en charge a été créé.

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:EncryptionPassphrase: ****

6. Recherchez le dossier qui contient les fichiers journaux. Étant donné que les fichiers journaux sont maintenant décompressés, déchiffrés ces aura des extensions de fichier d’origine. Modifiez ces fichiers pour supprimer les informations spécifiques au client, telles que les noms de volume et les adresses IP de périphériques et d’enregistrer les fichiers.

7. Fermez les fichiers afin de les compresser avec gzip et de les crypter avec AES-256. Il s’agit de la vitesse et la sécurité de transfert le package de prise en charge sur un réseau. Pour compresser et crypter les fichiers, entrez les informations suivantes :

    `Close-HcsSupportPackage <Path to the folder that contains support package files>`

    ![Modifier le package de prise en charge](./media/storsimple-create-manage-support-package/IC750707.png)

8. Lorsque vous y êtes invité, fournir un mot de passe de cryptage pour le package de prise en charge de modification.

        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****

9. Notez le nouveau mot de passe, afin que vous pouvez le partager avec le Support de Microsoft lors d’une demande.


### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exemple : Modification de fichiers dans un package de prise en charge sur un partage protégé par mot de passe

L’exemple suivant montre comment déchiffrer, de modifier et de chiffrer à nouveau un package de prise en charge.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser la prise en charge des packages et des journaux de périphérique pour résoudre les problèmes de déploiement de votre périphérique](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

- Découvrez comment [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
