<properties
    pageTitle="Déployer un ordinateur virtuel avec un certificat à l’aide de la clé de chambre forte de la pile Azure | Microsoft Azure"
    description="Découvrez comment déployer un ordinateur virtuel et injecter un certificat de clé de chambre forte de la pile Azure"
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
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="create-vms-and-include-certificates-retrieved-from-key-vault"></a>Créer des ordinateurs virtuels et inclure les certificats récupérées à partir de la clé de coffre-fort

Dans la pile d’Azure, machines virtuelles sont déployées par le biais du Gestionnaire de ressources Azure, et vous pouvez maintenant stocker des certificats dans Azure pile clé coffre-fort. Pile d’Azure (fournisseur de ressources Microsoft.Compute pour être précis) exécute un push de leur dans vos ordinateurs virtuels lorsque les ordinateurs virtuels sont déployés. Certificats peuvent être utilisés dans de nombreux scénarios, y compris SSL, cryptage et authentification par certificat.

À l’aide de cette méthode, vous pouvez protéger le certificat. Il est désormais pas dans l’image de machine virtuelle, ou dans les fichiers de configuration de l’application ou à un autre emplacement non sécurisé. En définissant une stratégie d’accès approprié pour la chambre forte de clé, vous pouvez également contrôler le qui obtient l’accès à votre certificat. Un autre avantage est que vous pouvez gérer tous vos certificats à un seul endroit dans Azure pile clé coffre-fort.

Voici une vue d’ensemble rapide du processus :

-   Vous avez besoin d’un certificat au format .pfx.

-   Créer un coffre-fort de clé (à l’aide d’un modèle ou le script exemple suivant).

-   Assurez-vous que vous avez activé le commutateur EnabledForDeployment.

-   Télécharger le certificat sous la forme d’un secret.

## <a name="deploying-vms"></a>Déploiement de machines virtuelles

Cet exemple de script crée un coffre-fort de clé, puis stocke un certificat stocké dans le fichier .pfx dans un répertoire local, dans un coffre-fort clé sous la forme d’un secret.

    $vaultName = "contosovault"
    $resourceGroup = "contosovaultrg"
    $location = "local"
    $secretName = "servicecert"
    $fileName = "keyvault.pfx"
    $certPassword = "abcd1234"

    # =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

    $fileContentBytes = get-content \$fileName -Encoding Byte

    $fileContentEncoded =
    [System.Convert\]::ToBase64String(\$fileContentBytes)
    $jsonObject = @"
    {
    "data": "\$filecontentencoded",
    "dataType" :"pfx",
    "password": "\$certPassword"
    }
    @$jsonObjectBytes = [System.Text.Encoding\]::UTF8.GetBytes(\$jsonObject)
    $jsonEncoded = \[System.Convert\]::ToBase64String(\$jsonObjectBytes)
    Switch-AzureMode -Name AzureResourceManager
    New-AzureResourceGroup -Name \$resourceGroup -Location \$location
    New-AzureKeyVault -VaultName \$vaultName -ResourceGroupName
    $resourceGroup -Location \$location -sku standard -EnabledForDeployment
    $secret = ConvertTo-SecureString -String \$jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName \$vaultName -Name \$secretName -SecretValue \$secret

La première partie du script lit le fichier .pfx, puis qu’il stocke comme un JSON objet avec le fichier contenu encodé en base 64. L’objet JSON est également codée en base64.

Ensuite, il crée un nouveau groupe de ressources et crée ensuite un coffre-fort de clé. Notez le dernier paramètre à la commande New-AzureKeyVault, «-EnabledForDeployment », qui accorde l’accès à Azure (et en particulier au fournisseur de ressources Microsoft.Compute) pour lire les secrets de la clé de chambre forte pour les déploiements.

La dernière commande stocke simplement l’objet JSON codées en base64 dans le coffre-fort clé sous la forme d’un secret.

Voici un exemple de sortie du script précédent :

    VERBOSE:  – Created resource group 'contosovaultrg' in
    location
    'eastus'
    ResourceGroupName : contosovaultrg
    Location          : eastus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions NotActions
                        ======= ==========
                        \*
    ResourceId        :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56/re
    sourceGroups/contosovaultrg
    VaultUri             : https://contosovault.vault.azure.net
    TenantId             : xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
    TenantName           : xxxxxxxx
    Sku                  : standard
    EnabledForDeployment : True
    AccessPolicies       : {xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47}
    AccessPoliciesText   :
                           Tenant ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
                           Object ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-b092cebf0c80
                           Application ID         :
                           Display Name           : Derick Developer  (derick@contoso.com)
                           Permissions to Keys    : get, create, delete,
                           list, update, import, backup, restore
                           Permissions to Secrets : all
    OriginalVault        : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId           :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56                 
    /resourceGroups/contosovaultrg/providers/Microsoft.KeyV
                           ault/vaults/contosovault
    VaultName            : contosovault
    ResourceGroupName    : contosovaultrg
    Location             : eastus
    Tags                 : {}
    TagsTable            :
    SecretValue     : System.Security.SecureString
    SecretValueText :
    ew0KImRhdGEiOiAiTUlJSkN3SUJBekNDQ01jR0NTcUdTSWIzRFFFSEFh
    Q0NDTGdFZ2dpME1JSUlzRENDQmdnR0NTcUdTSWIzRFFFSEFhQ0NCZmtF           
    Z2dYMU1JSUY4VENDQmUwR0N5cUdTSWIzRFFFTUNnRUNvSUlFL2pDQ0JQ
    &lt;&lt;&lt; Output truncated… &gt;&gt;&gt;
    Attributes      :
    Microsoft.Azure.Commands.KeyVault.Models.SecretAttributes
    VaultName       : contosovault
    Name            : servicecert
    Version         : e3391a126b65414f93f6f9806743a1f7
    Id              :
    https://contosovault.vault.azure.net:443/secrets/servicecert
                      /e3391a126b65414f93f6f9806743a1f7

Nous sommes maintenant prêts à déployer un modèle de machine virtuelle. Notez l’URI de la clé secrète à partir de la sortie (comme en surbrillance dans la sortie précédente en vert).

Vous aurez besoin d’un modèle situé ici. Les paramètres d’un intérêt particulier (outre les paramètres habituels de machine virtuelle) sont le nom de la chambre forte, le groupe de ressources de stockage en chambre forte et l’URI de secret. Bien sûr, vous pouvez également le télécharger à partir de GitHub et apportez les modifications nécessaires.

Lorsque cet ordinateur virtuel est déployé, Azure injecte le certificat dans la machine virtuelle.
Sous Windows, les certificats dans le fichier .pfx sont ajoutés avec la clé privée non exportable. Le certificat est ajouté pour le magasin de certificats LocalMachine, avec le magasin de certificats fournis par l’utilisateur. Sous Linux, le fichier de certificat est placé dans le répertoire /var/lib/waagent, avec le nom de fichier &lt;UppercaseThumbprint&gt;.crt pour le X509 le fichier de certificat, et &lt;UppercaseThumbprint&gt;.prv de la clé privée.
Ces deux fichiers sont mis en forme de .pem.

Généralement, l’application recherche le certificat à l’aide de l’empreinte numérique et n’a pas besoin de modification.

## <a name="retiring-certificates"></a>Suppression de certificats


Dans la section précédente, nous vous a montré comment distribuer un nouveau certificat pour vos ordinateurs virtuels existants. Mais votre ancien certificat est encore dans la machine virtuelle et ne peut pas être supprimé. Pour plus de sécurité, vous pouvez modifier l’attribut pour l’ancien secret sur « Désactivé », afin que même si un ancien modèle essaie de créer une machine virtuelle avec cette ancienne version de certificat, il sera. Voici la façon dont vous définissez une version spécifique de secret à désactiver :

    Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0

## <a name="conclusion"></a>Conclusion


Avec cette nouvelle méthode, le certificat peut être séparé de l’image de machine virtuelle ou la charge de l’application. Par conséquent, nous avons supprimé un point de l’exposition.

Le certificat peut également être renouvelé et téléchargé vers la chambre forte de clé sans avoir à recréer l’image de machine virtuelle ou le package de déploiement d’application. L’application doit toujours être fourni avec le nouvel URI pour cette nouvelle version de certificat cependant.

En séparant le certificat à partir de la machine virtuelle ou la charge de l’application, nous avons maintenant réduit le nombre de personnes ayant un accès direct au certificat.

Comme un avantage supplémentaire, vous disposez désormais d’un seul endroit dans le stockage en chambre forte de la clé pour gérer tous vos certificats, y compris toutes les versions qui ont été déployées au fil du temps.

## <a name="next-steps"></a>Étapes suivantes

[Déployer un ordinateur virtuel avec un mot de passe du coffre-fort de clé](azure-stack-kv-deploy-vm-with-secret.md)

[Autoriser une application à accéder à clé Vault](azure-stack-kv-sample-app.md)