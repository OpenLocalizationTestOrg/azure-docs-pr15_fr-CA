<properties
    pageTitle="Extension de l’Agent SQL Server pour les machines virtuelles de SQL Server (Gestionnaire de ressources) | Microsoft Azure"
    description="Cette rubrique décrit la procédure pour gérer l’extension de l’agent SQL Server, qui automatise les tâches d’administration spécifiques de SQL Server. Citons notamment automatisé de sauvegarde automatisée de correctifs et Azure clé de chambre forte intégration. Cette rubrique utilise le mode de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
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
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-resource-manager"></a>Extension de l’Agent SQL Server pour les machines virtuelles de SQL Server (Gestionnaire de ressources)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-server-agent-extension.md)
- [Classique](virtual-machines-windows-classic-sql-server-agent-extension.md)

L’Extension de l’Agent SQL Server IaaS (SQLIaaSExtension) s’exécute sur les machines virtuelles Azure pour automatiser les tâches d’administration. Cette rubrique fournit une vue d’ensemble des services pris en charge par l’extension ainsi que des instructions d’installation, d’état et de suppression.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique. Pour afficher la version classique de cet article, consultez [Extension de l’Agent SQL Server pour les machines virtuelles de SQL Server classique](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Services pris en charge

L’Extension de l’Agent SQL Server IaaS prend en charge les tâches d’administration suivantes :

| Fonction d’administration | Description |
|---------------------|-------------------------------|
| **Sauvegarde automatisée de SQL** | Automatise la planification des sauvegardes des bases de données pour l’instance par défaut de SQL Server dans la machine virtuelle. Pour plus d’informations, voir [sauvegarde automatisée pour SQL Server de Machines virtuelles Azure (responsable de ressources)](virtual-machines-windows-sql-automated-backup.md).|
| **SQL automatisée de correctifs** | Configure une fenêtre de maintenance au cours de laquelle les mises à jour pour votre ordinateur virtuel peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe pour votre charge de travail. Pour plus d’informations, reportez-vous à la section [automatisée des correctifs pour SQL Server de Machines virtuelles Azure (responsable de ressources)](virtual-machines-windows-sql-automated-patching.md).|
| **Intégration de la chambre forte de clé Azure** | Vous permet d’installer et de configurer le coffre-fort de clé Azure sur votre ordinateur virtuel de SQL Server automatiquement. Pour plus d’informations, voir [Configurer l’intégration Azure clé de coffre-fort pour SQL Server sur Azure VM (responsable de ressources)](virtual-machines-windows-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Conditions préalables

Configuration requise pour utiliser l’Extension de l’Agent SQL Server IaaS sur votre ordinateur virtuel :

**Système d’exploitation**:

- Windows Server 2012
- Windows Server 2012 R2

**Les versions de SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**PowerShell azure**:

- [Téléchargez et configurez les dernières commandes de PowerShell d’Azure](../powershell-install-configure.md)

## <a name="installation"></a>Installation

L’Extension de l’Agent SQL Server IaaS est installée automatiquement lorsque vous configurez une des images de galerie de machine virtuelle de SQL Server.

Si vous créez un ordinateur virtuel d’uniquement du système d’exploitation Windows Server, vous pouvez installer l’extension manuellement à l’aide de l’applet de commande PowerShell **Set-AzureVMSqlServerExtension** . Par exemple, la commande suivante installe l’extension sur un seul système d’exploitation Windows Server VM et le nomme « SQLIaaSExtension ».

    Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

Si vous mettez à jour vers la dernière version de l’Extension de l’Agent SQL IaaS, vous devez redémarrer votre machine après la mise à jour de l’extension.

>[AZURE.NOTE] Si vous installez l’Extension de l’Agent SQL Server IaaS de manuellement sur une machine virtuelle de Windows Server, vous devez utiliser et gérer ses fonctionnalités à l’aide des commandes PowerShell. L’interface du portail est disponible uniquement pour les images de la galerie de SQL Server.

## <a name="status"></a>État

Une pour vérifier que l’extension est installée consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez **tous les paramètres** de la lame d’ordinateur virtuel, puis cliquez sur **extensions**. Vous devez voir l’extension **SQLIaaSExtension** répertoriée.

![SQL Server Agent de IaaS Extension dans Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Vous pouvez également utiliser l’applet de commande **Get-AzureVMSqlServerExtension** Powershell d’Azure.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

La commande précédente confirme l’agent est installé et fournit des informations générales. Vous pouvez également obtenir des informations d’état spécifiques sur la sauvegarde d’automatisée et de correctifs avec les commandes suivantes.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Suppression   

Dans le portail d’Azure, vous pouvez désinstaller l’extension en cliquant sur le bouton de sélection sur la blade **d’Extensions** de vos propriétés de la machine virtuelle. Puis cliquez sur **Supprimer**.

![Désinstaller l’Extension SQL Server Agent de IaaS Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser l’applet de commande Powershell **Remove-AzureRmVMSqlServerExtension** .

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Étapes suivantes

Commencer à utiliser un des services pris en charge par l’extension. Pour plus d’informations, consultez les rubriques figurant dans la section [prise en charge des services](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server Azure machines virtuelles en fonctionnement, voir [SQL Server sur des ordinateurs virtuels Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
