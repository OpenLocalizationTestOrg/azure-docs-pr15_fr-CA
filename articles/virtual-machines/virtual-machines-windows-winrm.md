<properties
    pageTitle="Configuration de l’accès WinRM pour les Machines virtuelles dans le Gestionnaire de ressources Azure | Microsoft Azure"
    description="Comment configurer l’accès à WinRM pour une utilisation avec une machine virtuelle de gestionnaire de ressources Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/16/2016"
    ms.author="singhkay"/>

# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Comment configurer l’accès de WinRM pour les Machines virtuelles dans le Gestionnaire de ressources Azure

## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM dans Visual Studio Azure le Gestionnaire de ressources de la gestion des services Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique

* Pour une vue d’ensemble du Gestionnaire de ressources Azure, consultez cet [article](../azure-resource-manager/resource-group-overview.md)
* Les différences entre la gestion des services Azure et d’Azure le Gestionnaire de ressources, consultez cet [article](../resource-manager-deployment-model.md)

La principale différence réside dans la configuration de WinRM entre les deux piles de configuration est la façon dont le certificat est installé sur l’ordinateur virtuel. Dans la pile d’Azure le Gestionnaire de ressources, les certificats sont modelées en tant que ressources gérées par le fournisseur de ressources de chambre forte de clé. Par conséquent, l’utilisateur doit fournir son propre certificat et le télécharger vers un coffre-fort de clé avant de l’utiliser dans une machine virtuelle.

Voici les étapes à suivre pour configurer un ordinateur virtuel avec une connectivité de WinRM

1. Créer un coffre-fort de clé
2. Créer un certificat auto-signé
3. Télécharger votre certificat auto-signé à la chambre forte de clé
4. Obtenir l’URL de votre certificat auto-signé dans le coffre-fort de la clé
5. Faire référence à l’URL des certificats auto-signés lors de la création d’une machine virtuelle

## <a name="step-1-create-a-key-vault"></a>Étape 1 : Créer un coffre-fort de clé

Vous pouvez utiliser le ci-dessous pour créer le coffre-fort de la clé

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Étape 2 : Créer un certificat auto-signé
Vous pouvez créer un certificat auto-signé à l’aide de ce script PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Étape 3 : Télécharger votre certificat auto-signé dans le coffre-fort de la clé

Avant de télécharger le certificat dans le coffre-fort de la clé créée à l’étape 1, il faut converti dans un format que le fournisseur de ressources Microsoft.Compute va comprendre. La ci-dessous PowerShell script permettra de vous faire

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Étape 4 : Obtenir l’URL de votre certificat auto-signé dans le coffre-fort de la clé

Le fournisseur de ressources de Microsoft.Compute doit être une URL vers le secret à l’intérieur de la chambre forte de clé lors de la mise en service de la machine virtuelle. Ainsi, le fournisseur de ressources Microsoft.Compute télécharger le secret et créer le certificat équivalent sur l’ordinateur virtuel.

>[AZURE.NOTE]L’URL de la clé secrète doit inclure la version ainsi. Un exemple d’URL se présente comme ci-dessous https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### <a name="templates"></a>Modèles

Vous pouvez obtenir le lien vers l’URL dans le modèle à l’aide du sous code

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell

Vous pouvez obtenir cette URL à l’aide du dessous de commande PowerShell

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Étape 5 : Faire référence à l’URL des certificats auto-signés lors de la création d’une machine virtuelle

#### <a name="azure-resource-manager-templates"></a>Modèles de gestionnaire de ressources Azure

Lors de la création d’une machine virtuelle grâce à des modèles, le certificat obtient référencé dans la section secrets et la section winRM comme indiqué ci-dessous :

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Un exemple de modèle pour les éléments ci-dessus, consultez ici [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Code source de ce modèle, vous pouvez trouver sur [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell

    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Étape 6 : Se connecter à la machine virtuelle
Avant de vous connecter à la machine virtuelle que vous aurez besoin pour vous assurer que votre ordinateur est configuré pour la gestion à distance de WinRM. Démarrer PowerShell en tant qu’administrateur et exécuter le dessous de commande pour vous assurer que vous avez défini une.

    Enable-PSRemoting -Force

>[AZURE.NOTE] Vous devrez peut-être vous assurer que le service WinRM est en cours d’exécution si ce qui précède ne fonctionne pas. Vous pouvez le faire en utilisant`Get-Service WinRM`

Une fois l’installation terminée, vous pouvez vous connecter à la machine virtuelle à l’aide du dessous de commande

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate