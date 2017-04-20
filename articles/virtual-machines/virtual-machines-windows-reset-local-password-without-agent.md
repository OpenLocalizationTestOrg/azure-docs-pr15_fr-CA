<properties
   pageTitle="Réinitialiser un mot de passe Windows local lorsque l’agent d’Azure invité n’est pas installé | Microsoft Azure"
   description="Comment faire pour réinitialiser le mot de passe d’un compte d’utilisateur Windows local lorsque l’agent d’Azure invité n’est pas installé ou ne fonctionne sur une machine virtuelle"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="iainfou"/>

# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Comment faire pour réinitialiser le mot de passe Windows local pour Azure VM
Vous pouvez réinitialiser le mot de passe Windows local d’un ordinateur virtuel dans Azure à l’aide d' [Azure portal ou Azure PowerShell](virtual-machines-windows-reset-rdp.md) fourni que l’agent Azure invité est installé. Cette méthode est le principal moyen pour réinitialiser un mot de passe pour un ordinateur virtuel d’Azure. Si vous rencontrez des problèmes avec l’agent Azure invité ne répond ne pas ou échouent à installer après le téléchargement d’une image personnalisée, vous pouvez réinitialiser manuellement un mot de passe Windows. Cet article explique comment réinitialiser un mot de passe du compte local en attachant le disque virtuel source du système d’exploitation à l’autre. 

> [AZURE.WARNING] N’utilisez ce processus qu’en dernier recours. Toujours essayer de réinitialiser un mot de passe à l’aide d' [Azure portal ou Azure PowerShell](virtual-machines-windows-reset-rdp.md) tout d’abord.


## <a name="overview-of-the-process"></a>Vue d’ensemble du processus
Les étapes de base pour l’exécution d’un local réinitialisation mot de passe pour un ordinateur virtuel Windows Azure lorsqu’il n’y a aucun accès à l’agent d’Azure invité est comme suit :

- Supprimer la machine virtuelle de la source. Les disques virtuels sont conservés.
- Attacher le disque du système d’exploitation de l’ordinateur source à un autre au sein de votre abonnement Azure. Cet ordinateur virtuel est appelé la machine virtuelle de résolution des problèmes.
- À l’aide de la machine virtuelle de dépannage, de créer des fichiers de configuration sur le disque du système d’exploitation de l’ordinateur source.
- Détacher le disque du système d’exploitation de la machine virtuelle à partir de la machine virtuelle de résolution des problèmes.
- Utiliser un modèle de gestionnaire de ressources pour créer un ordinateur virtuel, à l’aide du disque virtuel d’origine.
- Lors du démarrage de la nouvelle machine virtuelle, vous créez les fichiers de configuration mettre à jour le mot de passe de l’utilisateur requis.


## <a name="detailed-steps"></a>Procédure détaillée
Toujours essayer de réinitialiser un mot de passe à l’aide d' [Azure portal ou Azure PowerShell](virtual-machines-windows-reset-rdp.md) avant d’essayer les étapes suivantes. Assurez-vous que vous disposez d’une sauvegarde de votre machine virtuelle avant de commencer. 

1. Supprimer la VM affectée dans Azure portal. Suppression de la machine virtuelle ne supprime que les métadonnées, la référence de la machine virtuelle dans Azure. Les disques virtuels sont conservés lors de la suppression de la machine virtuelle :

    - Sélectionnez la machine virtuelle dans Azure portal, cliquez sur *Supprimer*:

    ![Supprimer l’ordinateur virtuel existant](./media/virtual-machines-windows-reset-local-password-without-guest-agent/delete_vm.png)

2. Attacher le disque du système d’exploitation de l’ordinateur source à la machine virtuelle de résolution des problèmes. La machine virtuelle de dépannage doit être dans la même région en tant que disque de système d’exploitation de l’ordinateur source (tel que `West US`) :

    - Sélectionnez la machine virtuelle de résolution des problèmes dans le portail Azure. Cliquez sur *les disques* | *Attach existant*:

    ![Attacher le disque existant](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_attach_existing.png)

    Sélectionnez le *Fichier de disque dur virtuel* , puis sélectionnez le compte de stockage qui contient la source de votre machine virtuelle :

    ![Sélectionnez le compte de stockage](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_storageaccount.PNG)

    Sélectionnez le conteneur source. Le conteneur source est généralement des *disques durs virtuels*:

    ![Sélectionnez le conteneur de stockage](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_container.png)

    Sélectionnez le disque dur virtuel de système d’exploitation à joindre. Cliquez sur *Sélectionner* pour terminer le processus :

    ![Sélectionnez le disque virtuel source](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_source_vhd.png)

3. Se connecter à la VM de dépannage à l’aide du Bureau à distance et assurez-vous que le disque du système d’exploitation de l’ordinateur source est visible :

    - Sélectionnez la machine virtuelle de résolution des problèmes dans le portail Azure et cliquez sur *se connecter*.
    - Ouvrez le fichier RDP qui télécharge. Entrez le nom d’utilisateur et le mot de passe de la machine virtuelle de résolution des problèmes.
    - Dans l’Explorateur de fichiers, recherchez le disque de données que vous avez joint. Si la source de disque dur virtuel de l’ordinateur virtuel est le disque de données uniquement attaché à la machine virtuelle de dépannage, il doit être le lecteur F: :

    ![Disque de données liée](./media/virtual-machines-windows-reset-local-password-without-guest-agent/troubleshooting_vm_fileexplorer.png)

4. Créer des `gpt.ini` de `\Windows\System32\GroupPolicy` sur le lecteur de l’ordinateur source (si le fichier gpt.ini existe, attribuez-lui gpt.ini.bak) :

    > [AZURE.WARNING] Veillez à ne pas accidentellement créer les fichiers suivants dans C:\Windows, le lecteur du système d’exploitation de la machine virtuelle de résolution des problèmes. Créer les fichiers suivants dans le lecteur du système d’exploitation de votre machine virtuelle qui est attachée comme un disque de données de la source.

    - Ajoutez les lignes suivantes dans le `gpt.ini` fichier créé :

    ```
    [General]
    gPCFunctionalityVersion=2
    gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
    Version=1
    ```

    ![Créez le fichier gpt.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_gpt_ini.png)
 
5. Créer `scripts.ini` dans `\Windows\System32\GroupPolicy\Machine\Scripts`. Assurez-vous que les dossiers cachés sont affichés. Si nécessaire, créez le `Machine` ou `Scripts` dossiers.

    - Ajoutez les lignes suivantes le `scripts.ini` fichier créé :

    ```
    [Startup]
    0CmdLine=C:\Windows\System32\FixAzureVM.cmd
    0Parameters=
    ```

    ![Créer des scripts.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_scripts_ini.png)
 
6. Créer `FixAzureVM.cmd` dans `\Windows\System32` avec le contenu suivant, en remplaçant `<username>` et `<newpassword>` avec vos propres valeurs :

    ```
    NET USER <username> <newpassword>
    ```

    ![Créer des FixAzureVM.cmd](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_fixazure_cmd.png)

    Vous devez remplir les exigences de complexité de mot de passe configuré pour votre ordinateur virtuel lorsque vous définissez le nouveau mot de passe.

7. Dans Azure portal, détacher le disque à partir de la machine virtuelle de dépannage :

    - Sélectionnez la machine virtuelle de dépannage dans Azure portal, cliquez sur *les disques*.
    - Sélectionnez le disque de données associé à l’étape 2, cliquez sur *Détacher*:

    ![Détacher le disque](./media/virtual-machines-windows-reset-local-password-without-guest-agent/detach_disk.png)

8. Avant de créer un ordinateur virtuel, obtenir l’URI de votre disque de système d’exploitation source :

    - Sélectionnez le compte de stockage dans le portail Azure, cliquez sur les *objets BLOB*.
    - Sélectionnez le conteneur. Le conteneur source est généralement des *disques durs virtuels*:

    ![Sélectionnez l’objet blob de compte de stockage](./media/virtual-machines-windows-reset-local-password-without-guest-agent/select_storage_details.png)

    Sélectionnez votre source de disque dur virtuel de mémoire virtuelle du système d’exploitation et cliquez sur le bouton *Copier* le nom de *l’URL* :

    ![Copier une disquette URI](./media/virtual-machines-windows-reset-local-password-without-guest-agent/copy_source_vhd_uri.png)

9. Créer un ordinateur virtuel à partir du disque du système d’exploitation de l’ordinateur source :

    - Utilisez [ce modèle de gestionnaire de ressources Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) pour créer un ordinateur virtuel à partir d’un disque dur virtuel spécialisé. Cliquez sur le `Deploy to Azure` pour ouvrir le portail Azure avec les détails basé sur un modèle remplis pour vous.
    - Si vous souhaitez conserver tous les paramètres de la machine virtuelle, sélectionnez *Modifier le modèle* de fournir votre VNet existante, sous-réseau, carte réseau ou adresse IP publique.
    - Dans le `OSDISKVHDURI` zone de texte paramètre, coller l’URI de votre disque dur virtuel source de procurer à l’étape précédente :

    ![Créer un ordinateur virtuel à partir du modèle](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_new_vm_from_template.png)

10. Après l’exécution de la nouvelle machine virtuelle, vous connecter à la machine virtuelle à l’aide du Bureau à distance avec le nouveau mot de passe que vous avez spécifié dans le `FixAzureVM.cmd` script.

11. À partir de votre session à distance à la nouvelle machine virtuelle, supprimez les fichiers suivants pour nettoyer l’environnement :

    - À partir de %windir%\System32
        - supprimer FixAzureVM.cmd
    - À partir de %windir%\System32\GroupPolicy\Machine\
        - supprimer scripts.ini
    - À partir de %windir%\System32\GroupPolicy
        - supprimer fichier gpt.ini (si gpt.ini existait avant, et que vous avez renommé gpt.ini.bak, renommer le fichier .bak au fichier gpt.ini)

## <a name="next-steps"></a>Étapes suivantes
Si vous ne pouvez pas toujours vous connecter à l’aide du Bureau à distance, consultez le [guide de dépannage de RDP](virtual-machines-windows-troubleshoot-rdp-connection.md). Le [guide de dépannage détaillé RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md) ressemble à la résolution des problèmes de méthodes au lieu des étapes spécifiques. Vous pouvez également [Ouvrir une demande de prise en charge Azure](https://azure.microsoft.com/support/options/) pour obtenir une assistance pratique.