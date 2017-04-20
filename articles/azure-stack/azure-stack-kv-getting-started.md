<properties
    pageTitle="Mise en route de la chambre forte de la clé de pile Azure | Microsoft Azure"
    description="Mise en route à l’aide de la clé de chambre forte de la pile Azure"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ricardom"/>


# <a name="getting-started-with-key-vault"></a>Mise en route de la chambre forte de clé

Cette section décrit les étapes pour créer un coffre-fort, gérer les clés et les secrets ainsi que pour autoriser des utilisateurs ou des applications d’appeler des opérations dans le coffre-fort dans la pile d’Azure. Les étapes suivantes supposent un abonnement de clients existe et service de KeyVault est enregistré au sein de cet abonnement. Toutes les commandes de l’exemple sont basées sur les applets de commande KeyVault disponibles dans le cadre du Kit de développement PowerShell Azure.

## <a name="enabling-the-tenant-subscription-for-vault-operations"></a>L’activation de l’abonnement de clients pour les opérations de stockage en chambre forte 

Avant de pouvoir émettre des opérations sur un coffre-fort, vous devez vous assurer que votre abonnement est activé pour les opérations de stockage en chambre forte. Vous pouvez confirmer qu’en émettant la commande PowerShell suivante :

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -AutoSize

La sortie de la commande ci-dessus doit signaler « Enregistrés » pour l’état « Inscription » de chaque ligne.

    ProviderNamespace RegistrationState ResourceTypes Locations
    Microsoft.KeyVault Registered {operations} {local}
    Microsoft.KeyVault Registered {vaults} {local}
    Microsoft.KeyVault Registered {vaults/secrets} {local}
    

 Si n’est pas le cas, vous devez appeler la commande suivante pour inscrire le service KeyVault dans votre abonnement :

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault

Et comme la sortie de la commande :
    
    ProviderNamespace : Microsoft.KeyVault
    RegistrationState : Registered
    ResourceTypes : {operations, vaults, vaults/secrets}
    Locations : {local}


>[AZURE.NOTE] Si vous obtenez l’erreur : «*l’abonnement n’est pas enregistré avec chambre de clé Azure*» lors de l’appel des applets de commande KeyVault, confirmez vous avez activé le fournisseur de ressources KeyVault par les instructions ci-dessus.


## <a name="creating-a-hardened-container-a-vault-in-azure-stack-to-store-and-manage-cryptographic-keys-and-secrets"></a>Création d’un conteneur de sécurité renforcé (un coffre-fort) dans la pile d’Azure pour stocker et gérer des clés de chiffrement et des secrets

Pour créer un coffre-fort, un client doit d’abord créer un groupe de ressources. Les commandes PowerShell suivantes créent un groupe de ressources, puis un coffre-fort dans ce groupe de ressources. L’exemple inclut également les sorties à partir de cette applet de commande.

### <a name="creating-a-resource-group"></a>Création d’un groupe de ressources :

    New-AzureRmResourceGroup -Name vaultrg010 -Location local -Verbose -Force

Sortie :

    VERBOSE: Performing the operation "Replacing resource group ..." on target "".
    VERBOSE: 12:52:51 PM - Created resource group 'vaultrg010' in location 'local'
    ResourceGroupName : vaultrg010
    Location : local
    ProvisioningState : Succeeded
    Tags :
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010
    

### <a name="creating-a-vault"></a>Création d’un coffre-fort :


    New-AzureRmKeyVault -VaultName vault010 -ResourceGroupName vaultrg010 -Location local -Verbose

Sortie :

    VaultUri : https://vault010.vault.azurestack.local
    TenantId : 5454420b-2e38-4b9e-8b56-1712d321cf33
    TenantName : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Sku : Standard
    EnabledForDeployment : False
    EnabledForTemplateDeployment : False
    EnabledForDiskEncryption : False
    AccessPolicies : {5454420b-2e38-4b9e-8b56-1712d321cf33}
    AccessPoliciesText :
    Tenant ID : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Object ID : ca342e90-f6aa-435b-a11c-dfe5ef0bfeeb
    Application ID :
    Display Name : Tenant Admin (tenantadmin1@msazurestack.onmicrosoft.com)
    Permissions to Keys : get, create, delete, list, update, import, backup, restore
    Permissions to Secrets : all
    OriginalVault : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010/providers/Microsoft.KeyVault/vaults/vault010
    VaultName : vault010
    ResourceGroupName : vaultrg010
    Location : local
    Tags : {}
    TagsTable :
    
La sortie de cette applet de commande affiche les propriétés de la chambre forte clée que vous venez de créer. Les deux propriétés les plus importantes sont les suivantes :

-   **Nom de coffre-fort**: dans l’exemple, il s’agit de **vault010**. Vous utiliserez ce nom pour d’autres applets de commande de stockage en chambre forte de clé.

-   **URI du coffre-fort**: dans l’exemple, il s’agit de https://vault010.vault.azurestack.local. Les applications qui utilisent votre coffre-fort via son API REST doivent utiliser cet URI.

Votre compte Azure est maintenant autorisé à effectuer des opérations sur ce coffre-fort de clé. Encore, personne d’autre ne l’est.


## <a name="operating-on-keys-and-secrets"></a>Fonctionnement sur des clés et des Secrets

Après la création d’un coffre-fort, suivez les opérations suivantes pour créer gérer les clés et les secrets :

### <a name="creating-a-key"></a>Création d’une clé

Pour créer une clé, utilisez l' **Add-AzureKeyVaultKey** par l’exemple ci-dessous. Après la création réussie de la clé, l’applet de commande affiche les détails de la clé qui vient d’être créés.

    Add-AzureKeyVaultKey -VaultName \$vaultName -Name\$keyVaultKeyName -Verbose -Destination Software

Voici la sortie de l’applet de commande *Add-AzureKeyVaultKey* :

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff
    
Vous pouvez maintenant référencer cette clé que vous avez créé ou téléchargé dans Azure coffre-fort de clé, à l’aide de son URI. Utiliser des **clés/https://vault010.vault.azurestack.local:443/keyVaultKeyName001** de toujours disposer de la version actuelle ; et **https://vault010.vault.azurestack.local:443/clés/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff** permet d’obtenir cette version spécifique.

### <a name="retrieving-a-key"></a>Récupération d’une clé

Utilisez **AzureKeyVaultKey-Get** pour récupérer une clé et ses détails par l’exemple suivant :

    Get-AzureKeyVaultKey -VaultName vault010 -Name keyVaultKeyName001

Voici la sortie de Get-AzureKeyVaultKey

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff

### <a name="setting-a-secret"></a>Définition d’un secret

    $secretvalue = ConvertTo-SecureString 'User@123' -AsPlainText -Force
    Set-AzureKeyVaultSecret -Name MySecret-VaultName vault010 -SecretValue $secretvalue
    
Sortie

    Vault Name : vault010
    Name : MySecret
    Version : 65a387f2ed4a416180e852b970846f5b
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret/65a387f2ed4a416180e852b970846f5b
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags : 

### <a name="retrieving-a-secret"></a>Récupération d’un secret

    Get-AzureKeyVaultSecret -VaultName vault010

Sortie

    Vault Name : vault010
    Name : MySecret
    Version :
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags :

Désormais, votre coffre-fort de clé et de la clé ou le secret est prêt pour les applications à utiliser.
Vous devez autoriser les applications à utiliser.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorisez l’application à utiliser la clé ou le secret 

Pour autoriser l’application à accéder à la clé ou le secret dans la chambre forte, utilisez l’applet de commande Set -**AzureRmKeyVaultAccessPolicy** .

Par exemple, si le nom de votre coffre-fort est *ContosoKeyVault* et l’application que vous souhaitez autoriser a un *ID de Client* *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*et que vous souhaitez autoriser l’application à décrypter et signer avec des clés dans votre chambre forte, exécutez la commande suivante :

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Si vous souhaitez autoriser cette même application à lire des secrets dans votre chambre forte, exécutez la commande suivante :

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get


## <a name="next-steps"></a>Étapes suivantes
[Déployer un ordinateur virtuel avec un mot de passe du coffre-fort de clé](azure-stack-kv-deploy-vm-with-secret.md)

[Déployer un ordinateur virtuel avec un certificat de clé coffre-fort](azure-stack-kv-push-secret-into-vm.md)