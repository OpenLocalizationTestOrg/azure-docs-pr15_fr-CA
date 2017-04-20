<properties
    pageTitle="Configurer l’intégration de coffre-fort de clé Azure pour SQL Server sur Azure VM (classique)"
    description="Apprenez à automatiser la configuration de chiffrement de SQL Server pour une utilisation avec Azure clé coffre-fort. Cette rubrique explique comment utiliser Azure clé de chambre forte intégration avec SQL Server créer des ordinateurs virtuels dans le modèle de déploiement classique."
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
    ms.date="09/26/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-classic"></a>Configurer l’intégration de coffre-fort de clé Azure pour SQL Server sur Azure VM (classique)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-ps-sql-keyvault.md)
- [Classique](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Vue d’ensemble
Il existe plusieurs fonctionnalités de cryptage de SQL Server, telles que [le chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), le [cryptage de niveau colonne (Effacer)](https://msdn.microsoft.com/library/ms173744.aspx)et [cryptage de la sauvegarde](https://msdn.microsoft.com/library/dn449489.aspx). Ces formes de chiffrement vous obligent à gérer et stocker les clés de chiffrement utilisé pour le chiffrement. Le service de stockage en chambre forte de clé Azure (AKV) est conçu pour améliorer la sécurité et la gestion de ces clés dans un emplacement sécurisé et hautement disponible. Le [Connecteur de SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permet à SQL Server d’utiliser ces clés à partir de la chambre forte de clé Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Si vous exécutez SQL Server avec les ordinateurs locaux, il existe des [étapes que vous pouvez suivre pour accéder à Azure chambre forte de clé à partir de votre ordinateur de SQL Server sur site](https://msdn.microsoft.com/library/dn198405.aspx). Mais pour de SQL Server dans Azure VM, vous pouvez gagner du temps à l’aide de la fonctionnalité *d’Intégration de stockage en chambre forte de clé Azure* . Avec quelques Azure cmdlets pour activer cette fonctionnalité, vous pouvez automatiser la configuration nécessaire pour une VM de SQL accéder à votre clé vault.

Lorsque cette fonctionnalité est activée, elle automatiquement installe le connecteur de SQL Server configure le fournisseur EKM pour accéder Azure clé Vault et crée les informations d’identification pour pouvoir accéder à Password vault. Si vous avez regardé les étapes décrites dans la documentation sur les sites mentionnés précédemment, vous pouvez voir que cette fonctionnalité automatise les étapes 2 et 3. La seule chose que vous devez toujours faire manuellement est de créer le coffre-fort de clé et les clés. À partir de là, le programme d’installation complet de votre VM SQL est automatisée. Une fois que cette fonctionnalité a terminé cette installation, vous pouvez exécuter les instructions T-SQL pour crypter les sauvegardes des bases de données comme vous le feriez normalement.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurer l’intégration de AKV
Utilisation de PowerShell pour configurer l’intégration de stockage en chambre forte de clé Azure. Les sections suivantes fournissent une vue d’ensemble des paramètres requis, puis un exemple de script PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Installer l’Extension IaaS de SQL Server

Tout d’abord, [installez l’IaaS Extension de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Comprendre les paramètres d’entrée
Le tableau suivant répertorie les paramètres requis pour exécuter le script PowerShell dans la section suivante.

|Paramètre|Description|Exemple|
|---|---|---|
|**$akvURL**|**L’URL de la chambre forte de clé**|« https://contosokeyvault.vault.azure.net/ »|
|**$spName**|**Nom Principal de service**|« 5-4e11-af04eb07b669ccf2 fde2b411 - 33d »|
|**$spSecret**|**Secret de Principal du service**|« 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM = »|
|**$credName**|**Nom d’identification**: AKV intégration crée des informations d’identification au sein de SQL Server, ce qui permet de la machine virtuelle pour avoir accès à la chambre forte de clé. Choisissez un nom pour cette information d’identification.|« mycred1 »|
|**$vmName**|**Nom de la machine virtuelle**: le nom d’un VM SQL créé précédemment.|« myvmname »|
|**$serviceName**|**Nom du service**: nom du Service en nuage qui est associé à la VM SQL.|« mycloudservicename »|

### <a name="enable-akv-integration-with-powershell"></a>Activer l’intégration AKV avec PowerShell
L’applet de commande **New-AzureVMSqlServerKeyVaultCredentialConfig** crée un objet de configuration de la fonctionnalité d’intégration de stockage en chambre forte de clé Azure. **Jeu-AzureVMSqlServerExtension** configure cette intégration avec le paramètre **KeyVaultCredentialSettings** . Les étapes suivantes montrent comment utiliser ces commandes.

1. Dans PowerShell d’Azure, d’abord configurer les paramètres d’entrée avec vos valeurs spécifiques comme décrit dans les sections précédentes de cette rubrique. Le script suivant est un exemple.

        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2.  Puis utilisez le script suivant pour configurer et activer l’intégration de AKV.

        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

L’Extension de l’Agent SQL IaaS met à jour la VM SQL avec cette nouvelle configuration.

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
