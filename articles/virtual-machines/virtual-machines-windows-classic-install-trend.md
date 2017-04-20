<properties
    pageTitle="Installer Trend Micro Deep Security sur un ordinateur virtuel | Microsoft Azure"
    description="Cet article décrit comment installer et configurer la sécurité de Trend Micro sur une machine virtuelle créée avec le modèle de déploiement classique dans Azure."
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


# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Comment faire pour installer et configurer la sécurité Trend Micro en tant que Service sur un ordinateur virtuel de Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Cet article vous explique comment installer et configurer la sécurité Trend Micro en tant que Service sur un nouveau ou existant machine virtuelle (VM) exécutant Windows Server. Sécurité complète en tant que Service inclut la protection anti-logiciel malveillant, un pare-feu, un système de prévention des intrusions et surveillance de l’intégrité.

Le client est installé sous la forme d’une extension de sécurité via l’Agent de la machine virtuelle. Sur une nouvelle machine virtuelle, installation de l’Agent de machine virtuelle avec l’Agent de sécurité approfondie. Sur un ordinateur virtuel existant qui n’a pas l’Agent de la machine virtuelle, vous devez télécharger et installer. Cet article couvre les deux situations.

Si vous disposez d’un abonnement existant à partir de Trend Micro pour une solution sur site, vous pouvez l’utiliser pour aider à protéger vos ordinateurs virtuels Azure. Si vous n’êtes pas encore un client, vous pouvez vous inscrire pour un abonnement d’essai. Pour plus d’informations sur cette solution, consultez le blog de Trend Micro [Microsoft Azure VM l’Agent Extension de sécurité](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installer l’Agent de sécurité approfondie sur un nouvel ordinateur virtuel

Le [Azure portal classique](http://manage.windowsazure.com) vous permet d’installer l’Agent de la machine virtuelle et l’extension de sécurité Trend Micro, lorsque vous utilisez l’option **De la galerie** pour créer l’ordinateur virtuel. Si vous créez une machine virtuelle unique, l’utilisation du portail est un moyen facile pour ajouter une protection à partir de Trend Micro.

Cette option **De la galerie** s’ouvre un Assistant qui vous permet de configurer l’ordinateur virtuel. La dernière page de l’Assistant vous permet d’installer l’Agent de la machine virtuelle et l’extension de sécurité Trend Micro. Pour plus d’informations, consultez [créer un ordinateur virtuel exécutant Windows Azure portail classique](virtual-machines-windows-classic-tutorial.md). Lorsque vous arrivez à la dernière page de l’Assistant, effectuez les opérations suivantes :

1.  Sous **Agent de la machine virtuelle**, vérifier **l’Installation de l’Agent VM**.

2.  Sous les **Extensions de sécurité**, vérifier **l’Agent de sécurité approfondie de Trend Micro**.

    ![Installer l’Agent de la machine virtuelle et de l’Agent de sécurité](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)

3.  Cliquez sur la case à cocher pour créer l’ordinateur virtuel.

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installation de l’Agent de sécurité complète sur un ordinateur virtuel existant

Pour installer l’agent sur un ordinateur virtuel existant, vous devez les éléments suivants :

- Le module PowerShell de Azure, version 0.8.2 ou plus récente, installée sur votre ordinateur local. Vous pouvez vérifier la version de PowerShell Azure que vous avez installés à l’aide de la **azure de Get-Module | version de format-table** commande. Pour obtenir des instructions et un lien vers la dernière version, consultez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). Connectez-vous à votre abonnement Azure à l’aide de `Add-AzureAccount`.

- L’Agent de la machine virtuelle installé sur l’ordinateur virtuel cible.

Tout d’abord, vérifiez que l’Agent de la machine virtuelle est déjà installé. Renseignez le nom de service de cloud et le nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commandes de niveau administrateur Azure PowerShell. Remplacez tout entre guillemets, y compris les caractères < et >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Si vous ne connaissez pas le service en nuage et le nom de l’ordinateur virtuel, exécutez **Get-AzureVM** pour afficher ces informations pour tous les ordinateurs virtuels dans votre abonnement en cours.

Si la commande **d’écriture-hôte** renvoie la **valeur True**, l’Agent de la machine virtuelle est installé. Si elle retourne **False**, consultez les instructions et un lien pour le téléchargement dans le blog Azure valider [l’Agent de la machine virtuelle et Extensions - partie 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Si l’Agent de la machine virtuelle est installé, vous pouvez exécuter ces commandes.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes

Il faut quelques minutes pour que l’agent démarre l’exécution lorsqu’il est installé. Après cela, vous devez activer la sécurité sur l’ordinateur virtuel afin qu’il peut être géré par un gestionnaire de sécurité approfondie. Pour plus d’informations, consultez :

- Article de tendance sur cette solution, [Instant-On sécurité du nuage pour Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
- Un [exemple de script de Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) pour configurer l’ordinateur virtuel
- [Instructions](http://go.microsoft.com/fwlink/?LinkId=404099) pour l’exemple

## <a name="additional-resources"></a>Ressources supplémentaires

[Comment faire pour ouvrir une session sur un ordinateur virtuel exécutant Windows Server]

[Azure VM Extensions et fonctionnalités]


<!--Link references-->
[Comment faire pour ouvrir une session sur un ordinateur virtuel exécutant Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Azure VM Extensions et fonctionnalités]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
