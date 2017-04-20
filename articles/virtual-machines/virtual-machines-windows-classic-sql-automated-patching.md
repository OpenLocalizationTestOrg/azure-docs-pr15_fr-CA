<properties
    pageTitle="Automatisée des correctifs pour les machines virtuelles de SQL Server (standard) | Microsoft Azure"
    description="Explique la fonctionnalité automatisée de correctifs pour SQL Server Machines virtuelles en cours d’exécution dans Azure en utilisant le mode de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatisée de correctifs pour SQL Server sur des Machines virtuelles Azure (classique)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-patching.md)
- [Classique](virtual-machines-windows-classic-sql-automated-patching.md)

Correctifs automatisés établit une fenêtre de maintenance pour une Machine virtuelle Azure exécutant SQL Server. Mises à jour automatiques ne peuvent être installés que dans cette fenêtre de maintenance. Pour SQL Server, ainsi que les mises à jour et les redémarrages associés se produisent dans les meilleurs délais pour la base de données. Correctifs automatisés dépend de l' [Extension de l’Agent SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour afficher la version du Gestionnaire de ressources de cet article, reportez-vous à la section [Automatisée de correctifs pour SQL Server dans le Gestionnaire de ressources de Machines virtuelles Azure](virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser l’application automatique des correctifs, prenez en compte les conditions préalables suivantes :

**Système d’exploitation**:

- Windows Server 2012
- Windows Server 2012 R2

**Version de SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**PowerShell azure**:

- [Installer les dernières commandes de PowerShell d’Azure](../powershell-install-configure.md).

**Extension de IaaS de SQL Server**:

- [Installer l’Extension IaaS de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour l’application automatique des correctifs. Pour les machines virtuelles de classiques, vous devez utiliser PowerShell pour configurer ces paramètres.

|Paramètre|Valeurs possibles|Description|
|---|---|---|
|**Automatisée des correctifs**|Activer/désactiver (désactivé)|Active ou désactive l’application automatique des correctifs pour un ordinateur virtuel Azure.|
|**Planification de la maintenance**|Tous les jours, lundi, mardi, mercredi, jeudi, vendredi, samedi et dimanche|La planification pour le téléchargement et l’installation des mises à jour Windows et Microsoft SQL Server pour votre machine virtuelle.|
|**Heure de début de maintenance**|0-24|Heure de début local pour mettre à jour de la machine virtuelle.|
|**Durée de fenêtre de maintenance**|30-180|Le nombre de minutes autorisées pour terminer le téléchargement et l’installation des mises à jour.|
|**Catégorie du correctif**|Important|La catégorie des mises à jour à télécharger et à installer.|

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell

Dans l’exemple suivant, PowerShell est utilisé pour configurer l’application automatique des correctifs sur un VM de SQL Server existant. La commande **New-AzureVMSqlServerAutoPatchingConfig** configure une nouvelle fenêtre de gestion des mises à jour automatiques.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

En fonction de cet exemple, le tableau suivant décrit l’effet pratique sur la cible Azure VM :

|Paramètre|Effet|
|---|---|
|**Jour de la semaine**|Correctifs installés tous les jeudis.|
|**MaintenanceWindowStartingHour**|Début des mises à jour à 11 h 00.|
|**MaintenanceWindowsDuration**|Correctifs doivent être installés dans les 120 minutes. En fonction de l’heure de début, ils doivent compléter à 1:00 pm.|
|**PatchCategory**|La définition n’est possible que ce paramètre est « Important ».|

Elle peut prendre plusieurs minutes pour installer et configurer l’IaaS Agent de SQL Server.

Pour désactiver l’application automatique des correctifs, exécutez le même script sans paramètre - Activer à la AzureVMSqlServerAutoPatchingConfig de nouveau. À l’instar de l’installation, il peut prendre plusieurs minutes pour désactiver l’application automatique des correctifs.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres tâches d’automation disponibles, consultez [Extension de l’Agent SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur les ordinateurs virtuels d’Azure, consultez [SQL Server sur des ordinateurs virtuels Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
