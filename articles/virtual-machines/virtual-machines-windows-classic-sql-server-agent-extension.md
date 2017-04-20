<properties
    pageTitle="Extension de l’Agent SQL Server pour les machines virtuelles de SQL Server (standard) | Microsoft Azure"
    description="Cette rubrique décrit la procédure pour gérer l’extension de l’agent SQL Server, qui automatise les tâches d’administration spécifiques de SQL Server. Citons notamment automatisé de sauvegarde automatisée de correctifs et Azure clé de chambre forte intégration. Cette rubrique utilise le mode de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-classic"></a>Extension de l’Agent SQL Server pour les machines virtuelles de SQL Server (classique)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-server-agent-extension.md)
- [Classique](virtual-machines-windows-classic-sql-server-agent-extension.md)

L’Extension de l’Agent SQL Server IaaS (SQLIaaSAgent) s’exécute sur les machines virtuelles Azure pour automatiser les tâches d’administration. Cette rubrique fournit une vue d’ensemble des services pris en charge par l’extension ainsi que des instructions d’installation, d’état et de suppression.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour afficher la version du Gestionnaire de ressources de cet article, consultez [Extension de l’Agent SQL Server du Gestionnaire de ressources machines virtuelles de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Services pris en charge

L’Extension de l’Agent SQL Server IaaS prend en charge les tâches d’administration suivantes :

| Fonction d’administration | Description |
|---------------------|-------------------------------|
| **Sauvegarde automatisée de SQL** | Automatise la planification des sauvegardes des bases de données pour l’instance par défaut de SQL Server dans la machine virtuelle. Pour plus d’informations, reportez-vous à la section [automatisé de sauvegarde pour SQL Server de Machines virtuelles Azure (classique)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **SQL automatisée de correctifs** | Configure une fenêtre de maintenance au cours de laquelle les mises à jour pour votre ordinateur virtuel peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe pour votre charge de travail. Pour plus d’informations, reportez-vous à la section [automatisée de correctifs pour SQL Server de Machines virtuelles Azure (classique)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Intégration de la chambre forte de clé Azure** | Vous permet d’installer et de configurer le coffre-fort de clé Azure sur votre ordinateur virtuel de SQL Server automatiquement. Pour plus d’informations, voir [Configurer l’intégration Azure clé de coffre-fort pour SQL Server sur Azure VM (classique)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Conditions préalables

Configuration requise pour utiliser l’Extension de l’Agent SQL Server IaaS sur votre ordinateur virtuel :

### <a name="operating-system"></a>Système d'exploitation :

- Windows Server 2012
- Windows Server 2012 R2

### <a name="sql-server-versions"></a>Versions de SQL Server :

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### <a name="azure-powershell"></a>PowerShell Azure :

[Téléchargez et configurez les dernières commandes de PowerShell d’Azure](../powershell-install-configure.md).

Démarrez Windows PowerShell et connectez-le à votre abonnement Azure avec la commande **Add-AzureAccount** .

    Add-AzureAccount

Si vous disposez de plusieurs abonnements, permet de **Sélectionner-AzureSubscription** pour sélectionner l’abonnement qui contient votre cible VM classique.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

À ce stade, vous pouvez obtenir une liste des ordinateurs virtuels classiques et leurs noms de service associé avec la commande **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Installation

Pour les machines virtuelles de classiques, vous devez utiliser PowerShell pour installer l’Extension de l’Agent SQL Server IaaS et configurer ses services associés. Utilisez l’applet de commande PowerShell **Set-AzureVMSqlServerExtension** pour installer l’extension. Par exemple, la commande suivante installe l’extension sur un ordinateur de serveur virtuel Windows (classique) et le nomme « SQLIaaSExtension ».

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Si vous mettez à jour vers la dernière version de l’Extension de l’Agent SQL IaaS, vous devez redémarrer votre machine après la mise à jour de l’extension.

>[AZURE.NOTE] Les machines virtuelles classiques ont une option permettant d’installer et de configurer l’Extension de l’Agent SQL IaaS via le portail.

## <a name="status"></a>État

Une pour vérifier que l’extension est installée consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez **tous les paramètres** de la lame d’ordinateur virtuel, puis cliquez sur **extensions**. Vous devez voir l’extension **SQLIaaSAgent** répertoriée.

![SQL Server Agent de IaaS Extension dans Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Vous pouvez également utiliser l’applet de commande **Get-AzureVMSqlServerExtension** Powershell d’Azure.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Suppression   

Dans le portail d’Azure, vous pouvez désinstaller l’extension en cliquant sur le bouton de sélection sur la blade **d’Extensions** de vos propriétés de la machine virtuelle. Puis cliquez sur **Supprimer**.

![Désinstaller l’Extension SQL Server Agent de IaaS Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser l’applet de commande Powershell **Remove-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes

Commencer à utiliser un des services pris en charge par l’extension. Pour plus d’informations, consultez les rubriques figurant dans la section [prise en charge des services](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server Azure machines virtuelles en fonctionnement, voir [SQL Server sur des ordinateurs virtuels Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
