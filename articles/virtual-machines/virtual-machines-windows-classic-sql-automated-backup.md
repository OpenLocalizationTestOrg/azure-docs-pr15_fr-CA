<properties
    pageTitle="Sauvegarde automatisée pour les Machines virtuelles de SQL Server (standard) | Microsoft Azure"
    description="Explique la fonction automatique de sauvegarde de SQL Server en cours d’exécution dans Azure Virtual Machines à l’aide du Gestionnaire de ressources. "
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

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Sauvegarde automatisée de SQL Server sur des Machines virtuelles Azure (classique)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-backup.md)
- [Classique](virtual-machines-windows-classic-sql-automated-backup.md)

Sauvegarde automatisée configure automatiquement [Des sauvegardes gérées par Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pour toutes les bases de données existantes et nouvelles sur une machine virtuelle de Azure exécutant SQL Server 2014 Standard ou Enterprise. Cela vous permet de configurer des sauvegardes régulières de la base de données qui utilisent le stockage durable blob Azure. Sauvegarde automatisée dépend de l' [Extension de l’Agent SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour afficher la version du Gestionnaire de ressources de cet article, reportez-vous à la section [Automatisé de sauvegarde pour SQL Server dans le Gestionnaire de ressources de Machines virtuelles Azure](virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser automatique de sauvegarde, prenez en compte les conditions préalables suivantes :

**Système d’exploitation**:

- Windows Server 2012
- Windows Server 2012 R2

**Édition/version de SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

>[AZURE.NOTE] 2016 de SQL Server n’est pas encore prise en charge pour une sauvegarde automatique.

**Configuration de la base de données**:

- Bases de données cible doivent utiliser le modèle de récupération complète.

**PowerShell azure**:

- [Installer les dernières commandes de PowerShell d’Azure](../powershell-install-configure.md).

**Extension de IaaS de SQL Server**:

- [Installer l’Extension IaaS de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour une sauvegarde automatique. Pour les machines virtuelles de classiques, vous devez utiliser PowerShell pour configurer ces paramètres.

|Paramètre|Plage (par défaut)|Description|
|---|---|---|
|**Sauvegarde automatisée**|Activer/désactiver (désactivé)|Active ou désactive automatisé de sauvegarde pour un ordinateur virtuel de Azure exécutant SQL Server 2014 Standard ou Enterprise.|
|**Période de rétention**|de 1 à 30 jours (30 jours)|Le nombre de jours de conservation d’une sauvegarde.|
|**Compte de stockage**|Compte de stockage Azure (le compte de stockage créé pour la machine virtuelle spécifiée)|Un compte de stockage Azure pour stocker les fichiers de sauvegarde de Automated dans le stockage blob. Un conteneur est créé à cet emplacement pour stocker tous les fichiers de sauvegarde. La convention d’attribution de nom de fichier de sauvegarde inclut la date, heure et nom de l’ordinateur.|
|**Cryptage**|Activer/désactiver (désactivé)|Active ou désactive le cryptage. Lorsque le chiffrement est activé, les certificats utilisés pour la restauration de la sauvegarde sont situés sur le compte de stockage spécifié dans le même conteneur d’automaticbackup à l’aide de la même convention d’affectation de noms. Si le mot de passe est modifié, un nouveau certificat est généré avec ce mot de passe, mais l’ancien certificat reste pour restaurer des sauvegardes précédentes.|
|**Mot de passe**|Texte de mot de passe (aucun)|Un mot de passe pour les clés de cryptage. Cette option est uniquement requise si le chiffrement est activé. Pour restaurer une sauvegarde chiffrée, vous devez disposer du mot de passe correct et certificat associé qui a été utilisée au moment où que la sauvegarde a été effectuée.|

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell

Dans l’exemple suivant de PowerShell, automatisé de sauvegarde est configuré pour une VM de 2014 SQL Server existant. La commande **New-AzureVMSqlServerAutoBackupConfig** configure les paramètres d’automatisé de sauvegarde pour stocker les sauvegardes dans le compte de stockage Azure spécifié par la variable $storageaccount. Ces sauvegardes seront conservées pendant 10 jours. La commande **Set-AzureVMSqlServerExtension** met à jour la machine virtuelle de Azure spécifié avec ces paramètres.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Elle peut prendre plusieurs minutes pour installer et configurer l’IaaS Agent de SQL Server.

Pour activer le chiffrement, modifiez le script précédent pour passer le paramètre EnableEncryption avec un mot de passe (chaîne sécurisée) pour le paramètre CertificatePassword. Le script suivant active les paramètres automatisé de sauvegarde dans l’exemple précédent et ajoute le chiffrement.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pour désactiver la sauvegarde automatique, exécutez le script, même sans le **-Activer** paramètre de **Nouvelle-AzureVMSqlServerAutoBackupConfig**. À l’instar de l’installation, il peut prendre plusieurs minutes pour désactiver la sauvegarde automatique.

>[AZURE.NOTE] La désactivation et la désinstallation de l’IaaS Agent de SQL Server ne suppriment pas les paramètres des sauvegardes gérées précédemment configurés. Vous devez désactiver automatisé de sauvegarde avant la désactivation ou la désinstallation de l’IaaS Agent de SQL Server.

## <a name="next-steps"></a>Étapes suivantes

Sauvegarde automatisée configure des sauvegardes gérées sur Azure VM. Il est donc important de [consulter la documentation pour les sauvegardes gérées par](https://msdn.microsoft.com/library/dn449496.aspx) de comprendre le comportement et les conséquences.

Vous pouvez trouver de sauvegarde supplémentaire et rétablir les conseils pour SQL Server sur les ordinateurs virtuels d’Azure dans la rubrique suivante : [sauvegarde et restauration pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Pour plus d’informations sur d’autres tâches d’automation disponibles, consultez [Extension de l’Agent SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur les ordinateurs virtuels d’Azure, consultez [SQL Server sur des ordinateurs virtuels Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
