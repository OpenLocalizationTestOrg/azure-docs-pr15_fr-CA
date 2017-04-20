<properties
    pageTitle="Sauvegarde automatisée pour les Machines virtuelles de SQL Server (Gestionnaire de ressources) | Microsoft Azure"
    description="Explique la fonction automatique de sauvegarde de SQL Server en cours d’exécution dans Azure Virtual Machines à l’aide du Gestionnaire de ressources. "
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
    ms.date="07/14/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Sauvegarde automatisée de SQL Server sur des Machines virtuelles Azure (responsable de ressources)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-backup.md)
- [Classique](virtual-machines-windows-classic-sql-automated-backup.md)

Sauvegarde automatisée configure automatiquement [Des sauvegardes gérées par Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pour toutes les bases de données existantes et nouvelles sur une machine virtuelle de Azure exécutant SQL Server 2014 Standard ou Enterprise. Cela vous permet de configurer des sauvegardes régulières de la base de données qui utilisent le stockage durable blob Azure. Sauvegarde automatisée dépend de l' [Extension de l’Agent SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique. Pour afficher la version classique de cet article, reportez-vous à la section [Automatisé de sauvegarde pour SQL Server dans Azure Machines virtuelles classique](virtual-machines-windows-classic-sql-automated-backup.md).

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser automatique de sauvegarde, prenez en compte les conditions préalables suivantes :

**Système d’exploitation**:

- Windows Server 2012
- Windows Server 2012 R2

**Édition/version de SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

**Configuration de la base de données**:

- Bases de données cible doivent utiliser le modèle de récupération complète

**PowerShell azure**:

- Si vous envisagez de configurer automatisé de sauvegarde avec PowerShell, [installez les dernières commandes de PowerShell d’Azure](../powershell-install-configure.md) .

>[AZURE.NOTE] Sauvegarde automatisée s’appuie sur l’Extension de l’Agent SQL Server IaaS. Images de la galerie actuelles machine virtuelle SQL ajouter cette extension par défaut. Pour plus d’informations, consultez [Extension de l’Agent SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour une sauvegarde automatique. Les étapes de configuration varient selon que vous utilisez les commandes de Azure Windows PowerShell ou le portail Azure.

|Paramètre|Plage (par défaut)|Description|
|---|---|---|
|**Sauvegarde automatisée**|Activer/désactiver (désactivé)|Active ou désactive automatisé de sauvegarde pour un ordinateur virtuel de Azure exécutant SQL Server 2014 Standard ou Enterprise.|
|**Période de rétention**|de 1 à 30 jours (30 jours)|Le nombre de jours de conservation d’une sauvegarde.|
|**Compte de stockage**|Compte de stockage Azure (le compte de stockage créé pour la machine virtuelle spécifiée)|Un compte de stockage Azure pour stocker les fichiers de sauvegarde de Automated dans le stockage blob. Un conteneur est créé à cet emplacement pour stocker tous les fichiers de sauvegarde. La convention d’attribution de nom de fichier de sauvegarde inclut la date, heure et nom de l’ordinateur.|
|**Cryptage**|Activer/désactiver (désactivé)|Active ou désactive le cryptage. Lorsque le chiffrement est activé, les certificats utilisés pour la restauration de la sauvegarde sont situés sur le compte de stockage spécifié dans le même conteneur d’automaticbackup à l’aide de la même convention d’affectation de noms. Si le mot de passe est modifié, un nouveau certificat est généré avec ce mot de passe, mais l’ancien certificat reste pour restaurer des sauvegardes précédentes.|
|**Mot de passe**|Texte de mot de passe (aucun)|Un mot de passe pour les clés de cryptage. Cette option est uniquement requise si le chiffrement est activé. Pour restaurer une sauvegarde chiffrée, vous devez disposer du mot de passe correct et certificat associé qui a été utilisée au moment où que la sauvegarde a été effectuée.|

## <a name="configuration-in-the-portal"></a>Configuration du portail
Vous pouvez utiliser le portail Azure pour configurer automatisé de sauvegarde au cours de la mise en service ou pour les machines virtuelles existantes.

### <a name="new-vms"></a>Nouvelles machines virtuelles
Le portail Azure permet de configurer la sauvegarde d’automatique lorsque vous créez une nouvelle Machine virtuelle SQL Server 2014 dans le modèle de déploiement du Gestionnaire de ressources.

De la lame de **paramètres de SQL Server** , sélectionnez **sauvegarde automatisée**. L’Azure portail capture d’écran suivante illustre la lame de **Sauvegarde automatisée de SQL** .

![Configuration de la sauvegarde automatisée de SQL Azure Portal](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Pour le contexte, voir la rubrique complète sur [une machine virtuelle de SQL Server dans Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Machines virtuelles existantes
Pour les machines virtuelles de SQL Server existantes, sélectionnez votre ordinateur virtuel de SQL Server. Sélectionnez la section de **configuration de SQL Server** de la lame de **paramètres** .

![Sauvegarde automatisée de SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

De la lame de la **configuration de SQL Server** , cliquez sur le bouton **Modifier** dans la section sauvegarde automatisée.

![Configurer la sauvegarde automatisée de SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Lorsque vous avez terminé, cliquez sur le bouton **OK** au bas de la lame de **configuration de SQL Server** pour enregistrer vos modifications.

Si vous activez automatisé de sauvegarde pour la première fois, Azure configure l’IaaS Agent de SQL Server dans l’arrière-plan. Pendant ce temps, le portail Azure n’affiche pas toujours qu’automatisé de sauvegarde est configuré. Attendez quelques minutes, l’agent peut être installé, configuré. Après que le portail Azure reflètent les nouveaux paramètres.

>[AZURE.NOTE] Vous pouvez également configurer la sauvegarde automatisée à l’aide d’un modèle. Pour plus d’informations, consultez [modèle quickstart Azure pour une sauvegarde automatique](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell

Après avoir déployé votre VM SQL, utiliser PowerShell pour configurer automatisé de sauvegarde.

Dans l’exemple suivant de PowerShell, automatisé de sauvegarde est configuré pour une VM de 2014 SQL Server existant. La commande **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** configure les paramètres d’automatisé de sauvegarde pour stocker les sauvegardes dans le compte de stockage Azure associé à la machine virtuelle. Ces sauvegardes seront conservées pendant 10 jours. La commande **Set-AzureRmVMSqlServerExtension** met à jour la machine virtuelle de Azure spécifié avec ces paramètres.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Elle peut prendre plusieurs minutes pour installer et configurer l’IaaS Agent de SQL Server.

Pour activer le chiffrement, modifiez le script précédent pour passer le paramètre **EnableEncryption** avec un mot de passe (chaîne sécurisée) pour le paramètre **CertificatePassword** . Le script suivant active les paramètres automatisé de sauvegarde dans l’exemple précédent et ajoute le chiffrement.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Pour désactiver la sauvegarde automatique, exécutez le script, même sans le **-Activer** paramètre à la commande de **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** . L’absence de la **-Activer** paramètre signale la commande pour désactiver la fonctionnalité. À l’instar de l’installation, il peut prendre plusieurs minutes pour désactiver la sauvegarde automatique.

>[AZURE.NOTE] Suppression de l’IaaS Agent de SQL Server ne supprime pas les paramètres automatisé de sauvegarde précédemment configurés. Vous devez désactiver automatisé de sauvegarde avant la désactivation ou la désinstallation de l’IaaS Agent de SQL Server.

## <a name="next-steps"></a>Étapes suivantes

Sauvegarde automatisée configure des sauvegardes gérées sur Azure VM. Il est donc important de [consulter la documentation pour les sauvegardes gérées par](https://msdn.microsoft.com/library/dn449496.aspx) de comprendre le comportement et les conséquences.

Vous pouvez trouver de sauvegarde supplémentaire et rétablir les conseils pour SQL Server sur les ordinateurs virtuels d’Azure dans la rubrique suivante : [sauvegarde et restauration pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Pour plus d’informations sur d’autres tâches d’automation disponibles, consultez [Extension de l’Agent SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur les ordinateurs virtuels d’Azure, consultez [SQL Server sur des ordinateurs virtuels Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
