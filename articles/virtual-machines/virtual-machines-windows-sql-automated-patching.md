<properties
    pageTitle="Automatisée des correctifs pour les machines virtuelles de SQL Server (Gestionnaire de ressources) | Microsoft Azure"
    description="Explique la fonctionnalité automatisée de correctifs pour SQL Server Machines virtuelles en cours d’exécution dans Azure à l’aide du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatisée de correctifs pour SQL Server sur des Machines virtuelles Azure (responsable de ressources)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-patching.md)
- [Classique](virtual-machines-windows-classic-sql-automated-patching.md)

Correctifs automatisés établit une fenêtre de maintenance pour une Machine virtuelle Azure exécutant SQL Server. Mises à jour automatiques ne peuvent être installés que dans cette fenêtre de maintenance. Pour SQL Server, cette rescriction permet de s’assurer que les mises à jour et les redémarrages associés se produisent dans les meilleurs délais pour la base de données. Correctifs automatisés dépend de l' [Extension de l’Agent SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique. Pour afficher la version classique de cet article, reportez-vous à la section [Automatisée de correctifs pour SQL Server dans Azure Machines virtuelles classique](virtual-machines-windows-classic-sql-automated-patching.md).

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

- Si vous envisagez de configurer l’application automatique des correctifs avec PowerShell, [installez les dernières commandes de PowerShell d’Azure](../powershell-install-configure.md) .

>[AZURE.NOTE] Correctifs automatisé s’appuie sur l’Extension de l’Agent SQL Server IaaS. Images de la galerie actuelles machine virtuelle SQL ajouter cette extension par défaut. Pour plus d’informations, consultez [Extension de l’Agent SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour l’application automatique des correctifs. Les étapes de configuration varient selon que vous utilisez les commandes de Azure Windows PowerShell ou le portail Azure.

|Paramètre|Valeurs possibles|Description|
|---|---|---|
|**Automatisée des correctifs**|Activer/désactiver (désactivé)|Active ou désactive l’application automatique des correctifs pour un ordinateur virtuel Azure.|
|**Planification de la maintenance**|Tous les jours, lundi, mardi, mercredi, jeudi, vendredi, samedi et dimanche|La planification pour le téléchargement et l’installation des mises à jour Windows et Microsoft SQL Server pour votre machine virtuelle.|
|**Heure de début de maintenance**|0-24|Heure de début local pour mettre à jour de la machine virtuelle.|
|**Durée de fenêtre de maintenance**|30-180|Le nombre de minutes autorisées pour terminer le téléchargement et l’installation des mises à jour.|
|**Catégorie du correctif**|Important|La catégorie des mises à jour à télécharger et à installer.|

## <a name="configuration-in-the-portal"></a>Configuration du portail
Vous pouvez utiliser le portail Azure pour configurer l’application automatique des correctifs au cours de la mise en service ou pour les machines virtuelles existantes.

### <a name="new-vms"></a>Nouvelles machines virtuelles
Le portail Azure permet de configurer l’application automatique des correctifs lorsque vous créez une nouvelle Machine virtuelle de SQL Server dans le modèle de déploiement du Gestionnaire de ressources.

Dans la lame de **paramètres de SQL Server** , sélectionnez **l’application automatique des correctifs**. L’Azure portail capture d’écran suivante illustre la lame **SQL application automatique des correctifs** .

![Automated correction de SQL Azure Portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Pour le contexte, voir la rubrique complète sur [une machine virtuelle de SQL Server dans Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Machines virtuelles existantes
Pour les machines virtuelles de SQL Server existantes, sélectionnez votre ordinateur virtuel de SQL Server. Sélectionnez la section de **configuration de SQL Server** de la lame de **paramètres** .

![Correction automatique de SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

De la lame de la **configuration de SQL Server** , cliquez sur le bouton **Modifier** dans l’automatique de correction de section.

![Configurer l’application de patchs automatisée SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Lorsque vous avez terminé, cliquez sur le bouton **OK** au bas de la lame de **configuration de SQL Server** pour enregistrer vos modifications.

Si vous activez automatisée de correctifs pour la première fois, Azure configure l’IaaS Agent de SQL Server dans l’arrière-plan. Pendant ce temps, le portail Azure n’affiche pas toujours que l’application automatique de correctifs est configuré. Attendez quelques minutes, l’agent peut être installé, configuré. Après que le portail Azure reflète les nouveaux paramètres.

>[AZURE.NOTE] Vous pouvez également configurer automatisée de correctifs à l’aide d’un modèle. Pour plus d’informations, consultez [modèle quickstart Azure pour l’application automatique des correctifs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell

Après avoir déployé votre VM SQL, utiliser PowerShell pour configurer l’application automatique des correctifs.

Dans l’exemple suivant, PowerShell est utilisé pour configurer l’application automatique des correctifs sur un VM de SQL Server existant. La commande **AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig** configure une nouvelle fenêtre de gestion des mises à jour automatiques.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

En fonction de cet exemple, le tableau suivant décrit l’effet pratique sur la cible Azure VM :

|Paramètre|Effet|
|---|---|
|**Jour de la semaine**|Correctifs installés tous les jeudis.|
|**MaintenanceWindowStartingHour**|Début des mises à jour à 11 h 00.|
|**MaintenanceWindowsDuration**|Correctifs doivent être installés dans les 120 minutes. En fonction de l’heure de début, ils doivent compléter à 1:00 pm.|
|**PatchCategory**|La définition n’est possible que ce paramètre est **Important**.|

Elle peut prendre plusieurs minutes pour installer et configurer l’IaaS Agent de SQL Server.

Pour désactiver l’application automatique des correctifs, exécutez le script, même sans le **-Activer** paramètre pour **AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig**. L’absence de la **-Activer** paramètre signale la commande pour désactiver la fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres tâches d’automation disponibles, consultez [Extension de l’Agent SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur les ordinateurs virtuels d’Azure, consultez [SQL Server sur des ordinateurs virtuels Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
