<properties
    pageTitle="Réinitialiser le mot de passe ou la configuration du Bureau à distance sur un ordinateur virtuel de Windows | Microsoft Azure"
    description="Découvrez comment réinitialiser un mot de passe ou des services Bureau à distance sur un ordinateur virtuel de Windows à l’aide du portail Azure ou Azure PowerShell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Comment faire pour réinitialiser le service de bureau à distance ou son mot de passe d’ouverture de session sur un ordinateur virtuel de Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si vous ne peut pas se connecter à un ordinateur virtuel de Windows (VM), vous pouvez réinitialiser le mot de passe d’administrateur local ou réinitialiser la configuration du service Bureau à distance. Vous pouvez utiliser soit l’extension de l’accès de l’ordinateur virtuel ou le portail Azure dans Azure PowerShell pour réinitialiser le mot de passe. Si vous utilisez PowerShell, assurez-vous que vous avez le dernier module PowerShell installé sur votre ordinateur de travail et que vous êtes connecté à votre abonnement Azure. Pour obtenir la procédure détaillée, lisez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

> [AZURE.TIP] Vous pouvez vérifier la version de que vous avez installés à l’aide de PowerShell`Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`

## <a name="windows-vms-in-resource-manager-deployment-model"></a>Machines virtuelles de Windows dans le Gestionnaire de ressources du modèle de déploiement

### <a name="azure-portal"></a>Azure Portal
Sélectionnez votre machine virtuelle en cliquant sur **Parcourir** > **machines virtuelles** > *votre machine Windows* > **tous les paramètres** > **Réinitialiser le mot de passe**. La lame de réinitialisation de mot de passe s’affiche :

![Page de réinitialisation de mot de passe](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Entrez le nom d’utilisateur et un mot de passe, puis cliquez sur **Enregistrer**. Réessayez de vous connecter à votre machine virtuelle.

### <a name="vmaccess-extension-and-powershell"></a>Extension de VMAccess et PowerShell

Assurez-vous que vous avez Azure PowerShell 1.0 ou version ultérieure, et que vous êtes connecté à votre compte à l’aide du `Login-AzureRmAccount` applet de commande.

#### <a name="reset-the-local-administrator-account-password"></a>**Réinitialiser le mot de passe du compte administrateur local**

Vous pouvez réinitialiser le nom d’utilisateur ou de mot de passe administrateur à l’aide de la commande [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell.

Créer des informations d’identification de compte de votre administrateur local à l’aide de la commande suivante :

    $cred=Get-Credential

Si vous tapez un nom différent de celui du compte actuel, la commande d’extension VMAccess suivante renomme le compte administrateur local, affecte le mot de passe pour ce compte et génère un événement de fermeture de session Bureau à distance. Si le compte administrateur local est désactivé, l’extension VMAccess le permet.

L’extension d’accès aux machines virtuelles permet de définir les informations d’identification de nouveau comme suit :

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
        -Location WestUS -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


Remplacer `myRG`, `myVM`, `myVMAccess`et l’emplacement avec les valeurs appropriées à votre configuration.


#### <a name="reset-the-remote-desktop-service-configuration"></a>**Réinitialiser la configuration du service Bureau à distance**

Vous pouvez réinitialiser l’accès distant à votre ordinateur virtuel à l’aide de [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) ou [AzureRmVMAccessExtension de l’ensemble](https://msdn.microsoft.com/library/mt619447.aspx), comme suit. (Remplacez le `myRG`, `myVM`, `myVMAccess` et l’emplacement avec vos propres valeurs.)

    Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
        -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

Ou :<br>

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] Les deux commandes ajoutent un nouvel agent d’accès machine virtuelle nommé à l’ordinateur virtuel. À tout moment, un ordinateur virtuel peut avoir seulement un seul agent d’accès de machine virtuelle. Pour définir l’accès de machine virtuelle des propriétés de l’agent avec succès, supprimez l’agent de l’accès défini précédemment à l’aide `Remove-AzureRmVMAccessExtension` ou `Remove-AzureRmVMExtension`. À partir d’Azure PowerShell version 1.2.2, vous pouvez éviter cette étape lors de l’utilisation `Set-AzureRmVMExtension` avec un `-ForceRerun` option. Lorsque vous utilisez `-ForceRerun`, assurez-vous d’utiliser le même nom pour l’agent d’accès VM tel que défini par la commande précédente.

Si vous toujours ne peut pas se connecter à distance à votre machine virtuelle, reportez-vous à la section plus d’étapes pour essayer de [résoudre les problèmes de bureau à distance des connexions à un ordinateur basé sur Windows Azure virtuel](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="windows-vms-in-the-classic-deployment-model"></a>Machines virtuelles de Windows dans le modèle de déploiement classique

### <a name="azure-portal"></a>Azure portal

Pour les ordinateurs virtuels créés avec le modèle de déploiement classique, vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour réinitialiser le service de bureau à distance. Cliquez sur : **Parcourir** > **(classique) des machines virtuelles** > *votre machine Windows* > **Réinitialiser à distance...**. La page suivante s’affiche.

![Rétablir la page de configuration de RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Vous pouvez également essayer de réinitialiser le nom et le mot de passe du compte administrateur local. Cliquez sur : **Parcourir** > **(classique) des machines virtuelles** > *votre machine Windows* > **tous les paramètres** > **Réinitialiser le mot de passe**. La page suivante s’affiche.

![Page de réinitialisation de mot de passe](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Après avoir entré le nouveau nom d’utilisateur et le mot de passe, cliquez sur **Enregistrer**.

### <a name="vmaccess-extension-and-powershell"></a>Extension de VMAccess et PowerShell

Assurez-vous que l’Agent de l’ordinateur virtuel est installé sur l’ordinateur virtuel. L’extension VMAccess ne doit être installés avant de pouvoir utiliser, tant que l’Agent de l’ordinateur virtuel est disponible. Vérifiez que l’Agent de la machine virtuelle est déjà installé à l’aide de la commande suivante. (Remplacez « myCloudService » et « myVM » par le nom de votre service cloud et votre machine virtuelle, respectivement. Vous pouvez en savoir plus ces noms en exécutant `Get-AzureVM` sans paramètres.)

    $vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
    write-host $vm.VM.ProvisionGuestAgent

Si la commande **d’écriture-hôte** affiche **True**, l’Agent de la machine virtuelle est installé. S’il affiche **False**, consultez les instructions et un lien vers le téléchargement de blog Azure [VM Agent et Extensions - partie 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) .

Si vous avez créé l’ordinateur virtuel via le portail, vérifiez si `$vm.GetInstance().ProvisionGuestAgent` renvoie la **valeur True**. Si ce n’est pas le cas, vous pouvez la définir à l’aide de cette commande :

    $vm.GetInstance().ProvisionGuestAgent = $true

Cette commande empêche le message d’erreur suivant lorsque vous exécutez la commande **Set-AzureVMExtension** dans les étapes suivantes : « Agent d’invités de disposition doit être activé sur l’objet ordinateur virtuel avant de définir l’Extension d’accès aux IaaS VM. »

#### <a name="reset-the-local-administrator-account-password"></a>**Réinitialiser le mot de passe du compte administrateur local**

Créer une information d’identification de connexion avec le nom du compte administrateur local actuel et un nouveau mot de passe, puis exécutez le `Set-AzureVMAccessExtension` comme suit.

    $cred=Get-Credential
    Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Si vous tapez un nom différent de celui du compte actuel, l’extension VMAccess renomme le compte administrateur local, affecte le mot de passe pour ce compte et émet une déconnexion de bureau à distance. Si le compte administrateur local est désactivé, l’extension VMAccess le permet.

Ces commandes également à réinitialiser la configuration du service Bureau à distance.

#### <a name="reset-the-remote-desktop-service-configuration"></a>**Réinitialiser la configuration du service Bureau à distance**

Pour réinitialiser la configuration du service Bureau à distance, exécutez la commande suivante :

    Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

L’extension VMAccess exécute deux commandes sur l’ordinateur virtuel :

- `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Cette commande permet au groupe de pare-feu Windows intégré qui autorise le trafic entrant Bureau à distance, qui utilise le port TCP 3389.

- `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Cette commande définit les fDenyTSConnections valeur de Registre sur 0, ce qui permet des connexions Bureau à distance.


## <a name="next-steps"></a>Étapes suivantes

Si l’extension d’accès Azure VM ne répond pas et que vous ne parvenez pas à réinitialiser le mot de passe, vous pouvez [Réinitialiser le mot de Windows local en mode hors connexion](virtual-machines-windows-reset-local-password-without-agent.md). Cette méthode est un processus plus avancé et, vous devez connecter le disque dur virtuel de la machine virtuelle qui pose problème à un autre. Suivez les étapes décrites dans cet article tout d’abord et tente uniquement de la méthode de réinitialisation de mot de passe en mode hors connexion en dernier recours.

[Fonctionnalités et extensions VM azure](virtual-machines-windows-extensions-features.md)

[Se connecter à un ordinateur virtuel Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Résoudre les problèmes de connexions Bureau à distance à un ordinateur basé sur Windows Azure virtuel](virtual-machines-windows-troubleshoot-rdp-connection.md)
