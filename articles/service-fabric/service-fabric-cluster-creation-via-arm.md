
<properties
   pageTitle="Créer un cluster de Service Fabric sécurisé à l’aide du Gestionnaire de ressources Azure | Microsoft Azure"
   description="Cet article explique comment configurer un cluster Service Fabric sécurisé dans Azure à l’aide de gestionnaire de ressources Azure Azure clé coffre-fort et Azure Active Directory (DAS) pour l’authentification du client."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Créer un cluster de Service Fabric dans Azure à l’aide du Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Gestionnaire de ressources Azure](service-fabric-cluster-creation-via-arm.md)
- [Azure portal](service-fabric-cluster-creation-via-portal.md)

Il s’agit d’un guide étape par étape qui vous guide à travers les étapes de configuration d’un cluster Service de Azure Fabric sécurisé dans Azure à l’aide du Gestionnaire de ressources Azure. Ce guide vous guide dans les étapes suivantes :

 - Configurer en chambre forte de la clé pour gérer les clés de sécurité de cluster et d’application.
 - Créer un cluster sécurisé dans Azure avec le Gestionnaire de ressources Azure.
 - Authentifier les utilisateurs avec Azure Active Directory (DAS) pour la gestion de cluster.

Un cluster sécurisé est un cluster qui empêche tout accès non autorisé à des opérations de gestion, qui comprend le déploiement, la mise à niveau et la suppression d’applications, les services et les données qu’ils contiennent. Un cluster non sécurisé est un cluster que toute personne peut se connecter à tout moment et effectuer des opérations de gestion. Bien qu’il soit possible de créer un cluster non sécurisé, il est **fortement recommandé de créer un cluster sécurisé**. Un cluster non sécurisé **ne peut pas être sécurisé ultérieurement** - un nouveau cluster doit être créé.

Les concepts sont identiques pour la création de clusters sécurisés, si les clusters sont les clusters Linux ou des clusters Windows. Pour plus d’informations et d’assistance scripts pour créer les clusters Linux sécurisés, reportez-vous à la section [Création de clusters sécurisés sous Linux](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>Ouvrez une session Azure
Ce guide utilise [Azure PowerShell][azure-powershell]. Lorsque vous démarrez une nouvelle session PowerShell, connectez-vous à votre compte Azure et sélectionnez votre abonnement avant l’exécution de commandes Azure.

Ouvrez une session votre compte azure :

```powershell
Login-AzureRmAccount
```

Sélectionnez votre abonnement :

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Configurer la clé de coffre-fort

Cette section décrit la création d’un coffre-fort de clé pour un cluster de Service Fabric dans Azure et pour les applications de Service Fabric. Pour un guide complet sur le coffre-fort de la clé, reportez-vous à la [chambre forte de clé de mise en route][key-vault-get-started].

Service Fabric utilise des certificats X.509 pour sécuriser un cluster et fournissent des fonctionnalités de sécurité d’application. Azure coffre-fort de clé est utilisée pour gérer les certificats pour les clusters de Service Fabric dans Azure. Lorsqu’un cluster est déployé dans Azure, le responsable de la création de clusters de tissu de Service de fournisseur de ressources Azure extrait les certificats de clé coffre-fort et les installe sur l’ordinateurs virtuels du cluster.

Le diagramme suivant illustre la relation entre la clé Vault, un cluster Service Fabric et le fournisseur de ressources Azure qui utilise les certificats stockés dans un coffre-fort de clé lors de la création d’un cluster :

![Installation du certificat][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

La première étape consiste à créer un groupe de ressources spécifiquement pour le stockage en chambre forte de clé. Mise en chambre forte de la clé dans son propre groupe de ressources est recommandé. Cela vous permet de supprimer les groupes de ressources de calcul et de stockage, y compris le groupe de ressources a votre cluster Service Fabric sans perte de vos clés et les secrets. Le groupe de ressources qui a votre coffre-fort de clé doit être dans la même région que le cluster qui l’utilise.

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Créer la clé coffre-fort 

Créer un coffre-fort de clé dans le nouveau groupe de ressources. Le coffre-fort de la clé **doit être activée pour le déploiement** à permettent au fournisseur de ressources de Service Fabric obtiennent leurs certificats auprès d’elle et installation sur des nœuds de cluster :

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

Si vous disposez d’un coffre-fort existant de la clé, vous pouvez l’activer pour le déploiement à l’aide de la CLI d’Azure :

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>Ajouter des certificats à clé de coffre-fort

Pour fournir l’authentification et cryptage pour sécuriser les divers aspects d’un cluster et ses applications, les certificats sont utilisés dans le Service Fabric. Pour plus d’informations sur l’utilisation des certificats dans un Service de Fabric, reportez-vous à la section [scénarios de sécurité de cluster Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificat de cluster et serveur (obligatoire) 

Ce certificat est nécessaire pour sécuriser un cluster et empêcher l’accès non autorisé. Il fournit une sécurité de cluster de deux manières :
 
 - **L’authentification cluster :** Authentifie la communication nœud à nœud pour la fédération de cluster. Seuls les nœuds qui peuvent prouver leur identité à l’aide de ce certificat peuvent rejoindre le cluster.
 - **L’authentification du serveur :** Authentifie les points de terminaison de gestion de cluster à un client de gestion afin que le client de gestion sache qu’il communique avec le cluster réel. Ce certificat fournit également SSL pour l’API de gestion HTTPS et Service Fabric Explorer via HTTPS.

Pour répondre à ces besoins, le certificat doit répondre aux exigences suivantes :

 - Le certificat doit contenir une clé privée.
 - Le certificat doit être créé pour l’échange de clés, exportable vers un fichier d’échange d’informations personnelles (.pfx).
 - Nom du sujet du certificat doit correspondre au domaine utilisé pour accéder au cluster Service Fabric. Ce matchng est nécessaire pour assurer les SSL pour les points de terminaison de gestion HTTPS et le Service Fabric Explorer du cluster. Vous ne pouvez pas obtenir un certificat SSL à partir d’une autorité de certification (CA) pour le `.cloudapp.azure.com` domaine. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat à une autorité de certification, le nom du sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

### <a name="application-certificates-optional"></a>Certificats de l’application (facultatifs)

N’importe quel nombre de certificats supplémentaires peut être installé sur un cluster pour des raisons de sécurité application. Avant de créer votre cluster, prenez en compte les scénarios de sécurité d’application qui nécessitent un certificat pour être installés sur les nœuds, tels que :

 - Chiffrement et déchiffrement des valeurs de configuration d’application
 - Cryptage des données sur les nœuds lors de la réplication 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Mise en forme de certificats à utiliser de fournisseur de ressources Azure

Fichiers de clé privée (.pfx) peuvent être ajoutés et utilisés directement par l’intermédiaire de chambre forte de clé. Toutefois, le fournisseur de ressources Azure nécessite les clés sont stockées dans un format JSON spécial qui inclut le .pfx en base-64 codé de chaîne et le mot de passe de clé privée. Pour répondre à ces exigences, les clés doivent être placés dans une chaîne au format JSON et ensuite stockés sous forme de *secrets* dans le coffre-fort de la clé.

Pour faciliter ce processus, un module PowerShell est [disponible sur GitHub][service-fabric-rp-helpers]. Procédez comme suit pour utiliser le module :

  1. Télécharger tout le contenu de la mis en pension dans un répertoire local. 
  2. Importer le module dans la fenêtre PowerShell :

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
Le `Invoke-AddCertToKeyVault` commande dans ce module PowerShell automatiquement met en forme une clé privée de certificat dans une chaîne au format JSON et le télécharge sur le coffre-fort de la clé. Il permet d’ajouter le certificat de cluster et de certificats d’application supplémentaire à la chambre forte de clé. Répétez cette étape pour tous les certificats supplémentaires à installer dans votre cluster.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Les chaînes ci-dessus sont tous les composants requis en chambre forte de la clé de configuration d’un modèle de gestionnaire de ressources de cluster Service Fabric qui installe les certificats pour l’authentification, sécurité de point de terminaison de gestion et d’authentification de nœuds et les fonctionnalités de sécurité supplémentaires de l’application qui utilisent des certificats X.509. À ce stade, vous devez maintenant avoir la configuration suivante dans Azure :

 - Groupe de ressources de stockage en chambre forte de clé
   - Chambre forte de clé
     - Certificat d’authentification de cluster serveur
     - Certificats d’application

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurer Azure Active Directory pour l’authentification du client

DAS permet aux entreprises de la (appelées locataires) pour gérer l’accès utilisateur aux applications, qui sont divisées en applications avec une interface utilisateur de connexion sur le web et une expérience client natif. Dans ce document, nous supposons que vous avez déjà créé un locataire. Dans le cas contraire, commencez par lire les [comment obtenir un locataire Azure Active Directory][active-directory-howto-tenant].

Un cluster de Service Fabric offre plusieurs points d’entrée pour ses fonctionnalités de gestion, y compris [Service Fabric Explorer] basée sur le web[ service-fabric-visualizing-your-cluster] et [Visual Studio][service-fabric-manage-application-in-visual-studio]. Par conséquent, vous créez deux applications DAS pour contrôler l’accès au cluster, une application web et une application native.

Pour simplifier certaines des étapes impliquées dans la configuration DAS avec un cluster Service Fabric, nous avons créé un ensemble de scripts de Windows PowerShell.

>[AZURE.NOTE] Vous devez effectuer ces étapes *avant de* créer le cluster, dans les cas où les scripts s’attendent les noms du cluster et les points de terminaison, ceux-ci doivent être les valeurs planifiées, pas ceux que vous avez déjà créé.

1. [Télécharger les scripts] [ sf-aad-ps-script-download] sur votre ordinateur.

2. Cliquez droit sur le fichier zip, choisissez **Propriétés**, puis activez la case à cocher **Débloquer** et appliquer.

3. Extrayez le fichier zip.

4. Exécutez `SetupApplications.ps1`, fournissant les TenantId, ClusterName et WebApplicationReplyUrl en tant que paramètres. Par exemple :

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Vous pouvez trouver votre **TenantId** en exécutant la commande PowerShell '' Get-AzureSubscription'' '. Ceci affichera la **TenantId** pour chaque abonnement.

    Le **ClusterName** est utilisée pour préfixer les applications DAS créées par le script. Il n’a pas besoin de faire correspondre le nom du cluster actuel tel qu’il est conçu uniquement pour faciliter le mappage entre les artefacts de DAS au cluster Service Fabric qui ils sont utilisés avec.

    Le **WebApplicationReplyUrl** est le point de terminaison par défaut qui retourne de DAS à vos utilisateurs à la fin du processus de connexion. Vous devez spécifier le point de terminaison de Service Fabric Explorer pour votre cluster, qui est par défaut :

    https://&lt;cluster_domain&gt;: 19080/Explorer

    Vous êtes invité à vous connecter à un compte qui dispose des privilèges d’administrateur pour le locataire DAS. Une fois que vous le faites, le script se poursuit pour créer le web et les applications natives pour représenter votre cluster Service Fabric. Si vous examinez les applications du locataire dans [Azure portal classique][azure-classic-portal], vous devez voir deux nouvelles entrées :

    - *Nom_cluster*\_Cluster
    - *Nom_cluster*\_Client

    Imprime le script PowerShell fenêtre reste le Json requis par le modèle de gestionnaire de ressources Azure lors de la création du cluster dans la section suivante.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Créer un modèle de gestionnaire de ressources de cluster Service Fabric

Dans cette section, la sortie des commandes PowerShell précédentes sont utilisés dans un modèle de gestionnaire de ressources de cluster Service Fabric.

Exemple de gestionnaire de ressource modèles sont disponibles dans la [Galerie de modèles de démarrage rapide Azure de sur GitHub][azure-quickstart-templates]. Ces modèles peuvent être utilisés comme point de départ pour votre modèle de cluster. 

### <a name="create-the-resource-manager-template"></a>Créer le modèle de gestionnaire de ressources

Ce guide utilise le [cluster à 5 nœuds sécurisé] [ service-fabric-secure-cluster-5-node-1-nodetype-wad] exemple de modèle et les paramètres de modèle. Télécharger `azuredeploy.json` et `azuredeploy.parameters.json` sur votre ordinateur et ouvrez les deux fichiers dans votre éditeur de texte.

### <a name="add-certificates"></a>Ajouter des certificats

En référençant le coffre-fort de la clé qui contient les clés de certificats, les certificats sont ajoutés à un modèle de gestionnaire de ressources de cluster. Il est recommandé que ces valeurs de clé coffre-fort sont placées dans un fichier de paramètres de modèle de gestionnaire de ressources pour conserver le Gestionnaire de ressources réutilisables des fichiers de modèle et de libérer des valeurs spécifiques à un déploiement.

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>Ajouter tous les certificats de l’osProfile VMSS

Chaque certificat doit être installé dans le cluster doit être configuré dans la section osProfile de la ressource VMSS (Microsoft.Compute/virtualMachineScaleSets). Cela indique au fournisseur de ressources pour installer le certificat sur les ordinateurs virtuels. Cela inclut le certificat du cluster ainsi que les certificats de sécurité d’application que vous prévoyez d’utiliser pour vos applications :

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-service-fabric-cluster-certificate"></a>Configurer des certificats de Fabric du Service cluster

Le certificat d’authentification de cluster doit également être configuré dans la ressource de cluster Service Fabric (Microsoft.ServiceFabric/clusters) et l’extension de Service Fabric pour VMSS dans la ressource VMSS. Ainsi, le fournisseur de ressources de Service Fabric à configurer pour l’utiliser pour l’authentification de cluster et serveur pour les points de terminaison de gestion.

##### <a name="vmss-resource"></a>Ressources VMSS :

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Ressource de service Fabric :

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>Insérer les configuration DAS

La configuration DAS créée précédemment peut être insérée directement dans votre modèle de gestionnaire de ressources, toutefois, il est recommandé d’extraire les valeurs en paramètres tout d’abord dans un fichier de paramètres pour conserver le modèle de gestionnaire de ressources réutilisables et de valeurs spécifiques à un déploiement.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### < un « configurer-bras » ></a>configurer le Gestionnaire de ressources des paramètres du modèle

Enfin, utilisez les valeurs de sortie à partir des commandes en chambre forte de la clé et DAS PowerShell pour remplir le fichier de paramètres :

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
À ce stade, vous devez maintenant disposer des éléments suivants :

 - Groupe de ressources de stockage en chambre forte de clé
    - Chambre forte de clé
    - Certificat d’authentification de cluster serveur
    - Certificat de chiffrement de données
 - Clients Active Directory Azure 
    - Application de DAS pour la gestion basée sur le web et le Service Fabric Explorer
    - Application DAS gestion native client
    - Utilisateurs avec les rôles attribués 
 - Modèle de gestionnaire de ressources de cluster service Fabric
    - Certificats configurés par le biais de coffre-fort de clé
    - Azure Active Directory configuré 

Le diagramme suivant montre où interviennent les configuration de coffre-fort de clé et le DAS dans votre modèle de gestionnaire de ressources.

![Carte de DEPENDANCE de gestionnaire de ressources][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Créer le cluster

Vous êtes maintenant prêt à créer le cluster à l’aide du [déploiement de ARM][resource-group-template-deploy].

#### <a name="test-it"></a>Tester

Pour tester votre modèle de gestionnaire de ressources avec un fichier de paramètres, utilisez la commande PowerShell suivante :

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Déployer

Si le test de modèle de gestionnaire de ressources réussit, utilisez la commande PowerShell suivante pour déployer votre modèle de gestionnaire de ressources avec un fichier de paramètres :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>Affecter des utilisateurs à des rôles

Une fois que vous avez créé les applications pour représenter votre cluster, vous devez affecter vos utilisateurs aux rôles pris en charge par le Service Fabric : en lecture seule et Admin. Cela à l’aide d' [Azure portal classique][azure-classic-portal].

1. Accédez à vos clients et choisir des Applications.
2. Choisissez l’application web qui possède un nom comme `myTestCluster_Cluster`.
3. Cliquez sur l’onglet utilisateurs.
4. Choisissez un utilisateur à affecter et cliquez sur le bouton **affecter** au bas de l’écran.

    ![Affecter des utilisateurs à un bouton de rôles][assign-users-to-roles-button]

5. Sélectionnez le rôle à assigner à l’utilisateur.

    ![Affecter des utilisateurs à des rôles][assign-users-to-roles-dialog]

>[AZURE.NOTE] Pour plus d’informations sur les rôles dans un Service de Fabric, consultez [contrôle d’accès par rôle de Fabric du Service clients](service-fabric-cluster-security-roles.md).

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>Créer des clusters sécurisés sur Linux

Pour faciliter le processus, un script d’assistance a été fourni [ici](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Pour utiliser ce script d’assistance, il est supposé que vous avez déjà installé Azure infrastructure du langage commun, et qu’il est dans votre chemin d’accès. Vérifiez que le script est autorisé à exécuter en exécutant `chmod +x cert_helper.py` après le téléchargement. La première étape consiste à ouvrir une session dans votre compte Azure à l’aide de l’interface CLI avec le `azure login` commande. Une fois la connexion à votre compte Azure, utilisez l’application d’assistance avec votre certificat signé d’autorité de certification, comme dans l’exemple de commande suivant :

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

Cette commande renvoie les trois chaînes suivantes comme sortie : 

1. SourceVaultID est l’ID de la nouvelle KeyVault ResourceGroup qu’il a créé pour vous. 

2. Un CertificateUrl pour l’accès au certificat.

3. CertificateThumbprint, qui est utilisé pour l’authentification.


L’exemple suivant montre comment utiliser la commande :

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
L’exécution de la commande précédente vous propose les trois chaînes comme suit :

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 Nom du sujet du certificat doit correspondre au domaine utilisé pour accéder au cluster Service Fabric. Cela est nécessaire pour assurer les SSL pour les points de terminaison de gestion HTTPS et le Service Fabric Explorer du cluster. Vous ne pouvez pas obtenir un certificat SSL à partir d’une autorité de certification (CA) pour le `.cloudapp.azure.com` domaine. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat à une autorité de certification le nom du sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Ce sont les entrées nécessaires à la création d’un cluster de fabric sécurisé service (sans DAS) comme indiqué dans [les paramètres de modèle de configuration du Gestionnaire de ressources](#configure-arm). Vous pouvez vous connecter au cluster sécurisé via les instructions à [l’authentification de l’accès client à un cluster](service-fabric-connect-to-secure-cluster.md). Les clusters Linux aperçu ne supportent pas l’authentification du DAS. Vous pouvez assigner des rôles d’administrateur et client comme décrit dans la section [attribuer des rôles aux utilisateurs](#assign-roles). Lors de la spécification des rôles administrateur et client pour un cluster aperçu de Linux, vous devez fournir les empreintes de certificats pour l’authentification (et non le nom du sujet, dans la mesure où aucune validation de la chaîne ou de la révocation n’est en cours d’exécution dans cette version d’évaluation).


Si vous souhaitez utiliser un certificat auto-signé pour le test, vous pouvez utiliser le même script pour générer un certificat auto-signé et le télécharger vers KeyVault, en fournissant l’indicateur `ss` au lieu de fournir le nom de certificat et du chemin d’accès du certificat. Par exemple, consultez la commande suivante pour créer et télécharger un certificat signé automatiquement :

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Cette commande renvoie les mêmes chaînes de trois, SourceVault, CertificateUrl et CertificateThumbprint, qui est utilisé pour créer un cluster Linux sécurisé, ainsi que l’emplacement où le certificat auto-signé a été placé. Vous devez le certificat auto-signé pour se connecter au cluster.  Vous pouvez vous connecter au cluster sécurisé via les instructions à [l’authentification de l’accès client à un cluster](service-fabric-connect-to-secure-cluster.md). Nom du sujet du certificat doit correspondre au domaine utilisé pour accéder au cluster Service Fabric. Cela est nécessaire pour assurer les SSL pour les points de terminaison de gestion HTTPS et le Service Fabric Explorer du cluster. Vous ne pouvez pas obtenir un certificat SSL à partir d’une autorité de certification (CA) pour le `.cloudapp.azure.com` domaine. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat à une autorité de certification le nom du sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Les paramètres fournis par le script d’assistance peuvent être renseignés le portail comme décrit dans la section [créer un cluster dans le portail Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-portal).

## <a name="next-steps"></a>Étapes suivantes

À ce stade, vous avez un cluster sécurisé avec l’authentification de gestion d’Azure Active Directory. Ensuite, [vous connecter à votre cluster](service-fabric-connect-to-secure-cluster.md) et découvrez comment [Gérer les secrets de l’application](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Résoudre les problèmes de configuration d’Azure Active Directory pour l’authentification du client

Si vous rencontrez un problème lors de la configuration d’Azure Active Directory pour l’authentification du client, passez en revue les suggestings suivants pour les solutions potentielles.

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Service de Fabric Explorer invite de sélection de certificat

#### <a name="problem"></a>Problème

Une fois la connexion correctement sur la page de connexion de DAS dans l’Explorateur de tissu de Service, le navigateur renvoie à la page d’accueil mais vous demande une boîte de dialogue permettant de sélectionner un certificat.

![Boîte de dialogue Sélectionner un certificat SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Raison

L’utilisateur n’a pas été affecté à un rôle dans une application en cluster DAS. Ainsi l’authentification DAS échoue sur un cluster de Service Fabric. Service de Fabric Explorer revient à l’authentification par certificat.

#### <a name="solution"></a>Solution

Suivez les instructions de configuration DAS et attribuer des rôles de l’utilisateur. « UTILISATEUR affectation obligatoire à accès application » est également recommandée pour être activé en tant que `SetupApplications.ps1` est.

### <a name="connect-with-powershell-fails-with-error-the-specified-credentials-are-invalid"></a>Se connecter avec PowerShell des échoue avec l’erreur : les informations d’identification spécifiées ne sont pas valides

#### <a name="problem"></a>Problème

Quand utiliser PowerShell pour se connecter à un cluster à l’aide du mode de sécurité « AzureActiveDirectory », une fois la connexion correctement sur la page de connexion DAS, connexion échoue avec l’erreur : les informations d’identification spécifiées ne sont pas valides indiqué.

#### <a name="solution"></a>Solution

Même que ci-dessus.

### <a name="service-fabric-explorer-signing-in-return-failure-aadsts50011"></a>Échec de la signature en retour l’Explorateur service Fabric : AADSTS50011

#### <a name="problem"></a>Problème

Après la connexion de la page dans l’Explorateur de tissu de Service de connexion DAS, page renvoie signe échec - AADSTS50011 : l’adresse de réponse &lt;url&gt; ne correspond pas à l’adresse de réponse configuré pour l’application : &lt;guid&gt;. 

![Adresse de réponse SFX ne correspondent pas][sfx-reply-address-not-match]

#### <a name="reason"></a>Raison

L’application cluster(web) représentant le Service Fabric Explorer tente de s’authentifier auprès de DAS, dans le cadre de la demande qu’il fournit l’URL de renvoi de redirection. Mais il n’est pas répertorié dans la liste des 'URL de réponse' application DAS.

#### <a name="solution"></a>Solution

Ajouter des url de Service de Fabric Explorer à 'URL de réponse' dans l’onglet Configuration de l’application de cluster(web) ou de remplacer un des éléments dans la liste. Puis enregistrez.

![Url de réponse d’application Web][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters"></a>Est-il possible de réutiliser le locataire DAS même pour plusieurs clusters ?

#### <a name="answer"></a>Réponse

Oui. Mais n’oubliez pas d’ajouter les URL de Service de Fabric Explorer à votre application cluster(web) Qu'explorer de tissu sinon Service ne fonctionne pas.

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled"></a>Pourquoi dois-je toujours certificat serveur alors que le DAS est activé ?

#### <a name="answer"></a>Réponse

FabricClient et FabricGateway effectuent une authentification mutuelle. En cas d’authentification DAS, intégration de DAS fournit l’identité du client au serveur et le certificat de serveur est utilisée pour vérifier l’identité du serveur. Pour plus d’informations sur le fonctionne du certificat sur le Service de Fabric, vérifier [les certificats X.509 et Service de Fabric][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png