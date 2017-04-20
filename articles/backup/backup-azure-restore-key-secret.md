<properties
    pageTitle="Restaurer le secret et la clé de la chambre forte de clé pour VMs cryptés à l’aide de la sauvegarde d’Azure | Microsoft Azure"
    description="Apprenez à restaurer la clé de la chambre forte de clé et un code secret dans sauvegarde Azure à l’aide de PowerShell"
    services="backup"
    documentationCenter=""
    authors="JPallavi"
    manager="vijayts"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="JPallavi" />

# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurer le secret et la clé de la chambre forte de clé pour VMs cryptés à l’aide de la sauvegarde d’Azure
Cet article traite de l’à l’aide d’Azure VM sauvegarde pour effectuer la restauration d’ordinateurs virtuels de Azure crypté, si votre secret et la clé n’existent pas dans la chambre forte de clé. Ces étapes peuvent également servir si vous souhaitez conserver une copie distincte de clé (clé de cryptage) et un code secret (clé de chiffrement BitLocker) pour la machine virtuelle restaurée.

## <a name="pre-requisites"></a>Conditions préalables

1. **Sauvegarde cryptée des machines virtuelles** - crypté de machines virtuelles Azure ont été sauvegardés à l’aide de la sauvegarde d’Azure. Pour plus d’informations sur la procédure de sauvegarde cryptée Azure VM, reportez-vous à l’article [Gérer la sauvegarde et la restauration de machines virtuelles d’Azure à l’aide de PowerShell](backup-azure-vms-automation.md) .

2. **Configuration de coffre-fort de clé Azure** – Assurez-vous que coffre-fort dans lequel les clés et les secrets doivent être restaurés de la clé est déjà présent. Pour plus d’informations sur la gestion des clés de coffre-fort, reportez-vous à l’article [Mise en route de la chambre forte de clé Azure](../key-vault/key-vault-get-started.md) .

## <a name="setup-recovery-services-vault"></a>Le programme d’installation de coffre-fort de services de récupération 
Utilisez les étapes suivantes pour vous connecter au PowerShell et définir le contexte de coffre-fort de services de récupération

### <a name="log-in-to-azure-powershell"></a>Connexion à PowerShell Azure 

Connectez-vous au compte Azure à l’aide de l’applet de commande suivant

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Définir le contexte vault services récupération

Une fois connecté, utilisez l’applet de commande suivante pour obtenir la liste de vos abonnements disponibles

```
PS C:\> Get-AzureRmSubscription
```

Sélectionnez l’abonnement dans lequel les ressources sont disponibles

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Définir le contexte de la chambre forte à l’aide de coffre-fort de Services de récupération où la sauvegarde a été activée pour les machines virtuelles cryptés

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Obtient le point de récupération 

Sélectionnez le conteneur dans la chambre forte qui représente Azure de machine virtuelle cryptée

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

À l’aide de ce conteneur, revenir d’article pour la machine virtuelle correspondante

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Obtenir un tableau de points de récupération pour l’élément de sauvegarde sélectionné dans la variable rp

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Restaurer la machine virtuelle cryptée
Utilisez les étapes suivantes pour restaurer les VM crypté, sa clé et le secret.

### <a name="restore-key"></a>Restaurer la clé

Le tableau $rp ci-dessus, est trié dans l’ordre inverse de temps avec le dernier point de récupération à l’index 0. Par exemple : $rp [0] sélectionne le dernier point de récupération.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [AZURE.NOTE]
Une fois cette applet de commande s’exécute correctement, un fichier blob est généré dans le dossier spécifié sur l’ordinateur où il est exécuté. Ce fichier blob représente la clé de cryptage sous forme cryptée.

Restaurer la clé dans le coffre-fort de clé à l’aide de l’applet de commande suivant. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Restaurer le secret

Restaurer les données secrètes de point de récupération obtenu ci-dessus

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [AZURE.NOTE]
Le texte avant vault.azure.net représente le nom de coffre-fort clé d’origine. Le texte après secrets / représente le nom de secret. 

Obtenir le nom du secret et la valeur à partir de la sortie de l’applet de commande Exécuter ci-dessus, au cas où vous souhaitez utiliser le même nom de secret. Dans les autres cas, $secretname ci-dessous doivent être mis à jour pour utiliser le nouveau nom du secret. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Définir des balises pour le secret, en cas de machine virtuelle doit être restaurée ainsi. Pour la balise DiskEncryptionKeyFileName, valeur doit contenir le nom de la clé secrète que vous prévoyez d’utiliser. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [AZURE.NOTE]
Valeur de DiskEncryptionKeyFileName est la même que secrète nom obtenu ci-dessus. Valeur de DiskEncryptionKeyEncryptionKeyURL peut être obtenue à partir de la clé coffre-fort après restauration des clés de retour et à l’aide d’applet de commande [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx)   

Jeu de DOS dans le coffre-fort de clé secrète

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Restaurer l’ordinateur virtuel
Les applets de commande PowerShell ci-dessus vous aideront à restaurer clé et secrète principale dans le coffre-fort clé, si vous avez sauvegardé VM crypté à l’aide de la sauvegarde de machine virtuelle Azure. Après la restauration, reportez-vous à l’article [Gérer la sauvegarde et la restauration de machines virtuelles d’Azure à l’aide de PowerShell](backup-azure-vms-automation.md) pour restaurer des ordinateurs virtuels cryptés.
