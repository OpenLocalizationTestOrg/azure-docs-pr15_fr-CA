<properties
    pageTitle="Installation de Symantec Endpoint Protection sur une machine virtuelle | Microsoft Azure"
    description="Découvrez comment installer et configurer l’extension de sécurité Symantec Endpoint Protection sur un Azure VM nouveau ou existant, créé avec le modèle de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="iainfou"/>

# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Comment faire pour installer et configurer Symantec Endpoint Protection sur un ordinateur virtuel de Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Cet article vous explique comment installer et configurer le client de Symantec Endpoint Protection sur un ordinateur virtuel existant (VM) exécutant Windows Server. C’est le client complet, qui inclut des services tels que les antivirus et antispyware, pare-feu et prévention des intrusions. Le client est installé sous la forme d’une extension de sécurité à l’aide de l’Agent de la machine virtuelle.

Si vous disposez d’un abonnement existant de Symantec pour une solution sur site, vous pouvez l’utiliser pour protéger vos ordinateurs virtuels Azure. Si vous n’êtes pas encore un client, vous pouvez vous inscrire pour un abonnement d’essai. Pour plus d’informations sur cette solution, reportez-vous à la section [Symantec Endpoint Protection sur la plate-forme de Microsoft Azure][Symantec]. Cette page contient également des liens vers des informations relatives aux licences et des instructions pour l’installation du client, si vous êtes un client de Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Installation de Symantec Endpoint Protection sur un ordinateur virtuel existant

Avant de commencer, vous devez les éléments suivants :

- Le module PowerShell de Azure, version 0.8.2 ou une version ultérieure sur votre ordinateur de travail. Vous pouvez vérifier la version de PowerShell Azure que vous avez installé avec le **azure de Get-Module | version de format-table** commande. Pour obtenir des instructions et un lien vers la dernière version, voir [Comment faire pour installer et configurer le PowerShell Azure][PS]. Connectez-vous à votre abonnement Azure à l’aide de `Add-AzureAccount`.

- L’Agent de la machine virtuelle en cours d’exécution sur l’ordinateur virtuel Azure.

Tout d’abord, vérifiez que l’Agent de la machine virtuelle est déjà installé sur l’ordinateur virtuel. Renseignez le nom de service de cloud et le nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commandes de niveau administrateur Azure PowerShell. Remplacez tout entre guillemets, y compris les caractères < et >.

> [AZURE.TIP] Si vous ne connaissez pas les noms de machine virtuelle et le service en nuage, exécutez **Get-AzureVM** pour répertorier les noms de tous les ordinateurs virtuels de votre abonnement en cours.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Si la commande **d’écriture-hôte** affiche **True**, l’Agent de la machine virtuelle est installé. S’il affiche **False**, consultez les instructions et un lien pour le téléchargement dans le blog Azure valider [l’Agent de la machine virtuelle et Extensions - partie 2][Agent].

Si l’Agent de la machine virtuelle est installé, vous pouvez exécuter ces commandes à l’installation de l’agent de Symantec Endpoint Protection.

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Pour vérifier que l’extension de sécurité Symantec a été installée et qu’il est mis à jour :

1.  Ouvrez une session sur l’ordinateur virtuel. Pour obtenir des instructions, voir [Comment faire pour ouvrir une session sur un ordinateur virtuel exécutant Windows Server][Logon].
2.  Pour Windows Server 2008 R2, cliquez sur **Démarrer > Symantec Endpoint Protection**. Pour Windows Server 2012 ou de Windows Server 2012 R2, à partir de l’écran de démarrage, **Symantec**, puis tapez **Symantec Endpoint Protection**.
3.  À partir de l’onglet **état** de la fenêtre **d’État-Symantec Endpoint Protection** , appliquer des mises à jour ou redémarrez si nécessaire.

## <a name="additional-resources"></a>Ressources supplémentaires

[Comment faire pour ouvrir une session sur un ordinateur virtuel exécutant Windows Server][Logon]

[Fonctionnalités et Extensions de azure VM][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493