<properties
   pageTitle="Se connecter à un cluster privé sécurisé | Microsoft Azure"
   description="Cet article décrit comment sécuriser les communications au sein de l’autonome ou un cluster privé ainsi qu’entre les clients et le cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>Sécuriser un cluster autonome sous Windows à l’aide de certificats X.509

Cet article décrit comment faire pour sécuriser la communication entre les différents nœuds de votre cluster Windows autonome, ainsi que la façon dont les certificats pour authentifier les clients qui se connectent à ce cluster, à l’aide de X.509. Cela garantit que seuls les utilisateurs autorisés peuvent accéder au cluster, les applications déployées et effectuer des tâches de gestion.  Certificats de sécurité doit être activé sur le cluster lors de la création du cluster.  

Pour plus d’informations sur la sécurité telles que la sécurité de nœud à nœud du cluster, client-à-noeud sécurité et contrôle d’accès basé sur les rôles, voir [les scénarios de sécurité de Cluster](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need"></a>Les certificats dont vous aurez besoin ?

Utiliser, [Télécharger le package de cluster autonome](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) à l’un des nœuds de votre cluster. Dans le package téléchargé, vous trouverez un fichier **ClusterConfig.X509.MultiMachine.json** . Ouvrez le fichier et consulter la section de **sécurité** sous la section de **Propriétés** :

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
            "ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
            "HttpApplicationGatewayCertificate":{
                "Thumbprint": "[Thumbprint]",
                "X509StoreName": "My"
            }
        }
    }

Cette section décrit les certificats dont vous avez besoin pour la sécurisation de votre cluster de Windows autonome. Pour activer la sécurité basée sur le certificat de définie les valeurs de **ClusterCredentialType** et de **ServerCredentialType** à *X509*.

>[AZURE.NOTE] Une [empreinte](https://en.wikipedia.org/wiki/Public_key_fingerprint) est l’identité principale d’un certificat. Lisez [comment récupérer l’empreinte d’un certificat](https://msdn.microsoft.com/library/ms734695.aspx) pour connaître de l’empreinte des certificats que vous créez.

Le tableau suivant répertorie les certificats dont vous avez besoin dans votre programme d’installation de cluster :

|**Paramètre de CertificateInformation**|**Description**|
|-----------------------|--------------------------|
|ClusterCertificate|Ce certificat est nécessaire pour sécuriser la communication entre les nœuds d’un cluster. Vous pouvez utiliser les deux certificats différents, principal et secondaire pour la mise à niveau. Définir l’empreinte numérique du certificat principal dans la section de **l’empreinte numérique** et celle de l’image secondaire dans les variables **ThumbprintSecondary** .|
|Certificats|Ce certificat est présenté au client lorsqu’il tente de se connecter à ce cluster. Pour plus de commodité, vous pouvez choisir d’utiliser le même certificat de *ClusterCertificate* et de *certificats*. Vous pouvez utiliser les deux certificats de serveur différent, principal et secondaire pour la mise à niveau. Définir l’empreinte numérique du certificat principal dans la section de **l’empreinte numérique** et celle de l’image secondaire dans les variables **ThumbprintSecondary** . |
|ClientCertificateThumbprints|Il s’agit d’un ensemble de certificats que vous souhaitez installer sur les clients authentifiés. Vous pouvez avoir un certain nombre de certificats client différents installés sur les ordinateurs que vous souhaitez autoriser l’accès au cluster. La valeur dans la variable **CertificateThumbprint** l’empreinte numérique de chaque certificat. Si vous affectez la valeur *true* **IsAdmin** , puis le client avec ce certificat est installé peut faire administrateur des activités de gestion sur le cluster. Si **IsAdmin** est *false*, le client avec ce certificat ne pouvez effectuer les actions autorisées sur les droits d’accès utilisateur, généralement en lecture seule. Pour plus d’informations sur les rôles de lecture [basée sur les rôles (RBAC) le contrôle d’accès](service-fabric-cluster-security.md/#role-based-access-control-rbac)  |
|ClientCertificateCommonNames|Définir le nom commun du certificat client premier pour le **CertificateCommonName**. Le **CertificateIssuerThumbprint** est l’empreinte de l’émetteur de ce certificat. Lire [des certificats](https://msdn.microsoft.com/library/ms731899.aspx) en savoir plus sur les noms communs et de l’émetteur.|
|HttpApplicationGatewayCertificate|Il s’agit d’un certificat facultatif qui peut être spécifié si vous souhaitez sécuriser votre passerelle d’Application Http. Assurez-vous que reverseProxyEndpointPort est défini dans nodeTypes si vous utilisez ce certificat.|

Voici exemple de configuration du cluster dans lequel les certificats de Cluster, le serveur et le Client ont été fournis.

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="aquire-the-x509-certificates"></a>Acquérir le X.509 des certificats
Pour sécuriser les communications au sein du cluster, vous devez d’abord obtenir des certificats X.509 pour les nœuds de votre cluster. En outre, pour limiter la connexion avec ce cluster d’ordinateurs/utilisateurs autorisés, vous devez obtenir et installer des certificats pour les ordinateurs clients.

Pour les clusters qui exécutent des charges de travail, vous devez utiliser une [Autorité de certification (CA)](https://en.wikipedia.org/wiki/Certificate_authority) signé le certificat X.509 utilisé pour sécuriser le cluster. Pour plus d’informations sur l’obtention de ces certificats, accédez à [Comment : obtenir un certificat](http://msdn.microsoft.com/library/aa702761.aspx).

Pour les clusters destiné à des fins de test, vous pouvez choisir d’utiliser un certificat auto-signé.

## <a name="optional-create-a-self-signed-certificate"></a>Facultatif : Créez un certificat auto-signé
Pour créer un certificat auto-signé qui peut être correctement sécurisé est d’utiliser le script *CertSetup.ps1* dans le dossier Service Fabric SDK dans le répertoire *C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Utilisez cette option pour créer un certificat avec un nom approprié et de modifier ce fichier.

Maintenant, exportez le certificat vers un fichier PFX avec un mot de passe protégé. Vous devez d’abord obtenir l’empreinte numérique du certificat. Exécutez l’application certmgr.exe. Accédez au dossier **Local\personnel** et trouver le certificat que vous venez de créer. Double-cliquez sur le certificat pour l’ouvrir, sélectionnez l’onglet *Détails* et défiler la liste pour le champ *d’empreinte* . Copier la valeur d’empreinte dans la ligne de commande PowerShell ci-dessous, supprimer les espaces.  Modifiez la valeur de *$pswd* pour un mot de passe sécurisé aptitude à protéger et exécution de PowerShell :

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Pour afficher les détails d’un certificat installé sur la machine, vous pouvez exécuter la commande PowerShell suivante :

```
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

Ou bien, si vous avez un abonnement Azure, suivez la section [Ajouter des certificats à clé de coffre-fort](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).

## <a name="install-the-certificates"></a>Installation des certificats
Une fois que vous avez ou des certificats, vous pouvez les installer sur les nœuds du cluster. Vos nœuds doivent avoir la plus récente de Windows PowerShell 3.x est installé sur ces derniers. Vous devez répéter ces étapes sur chaque nœud, pour le Cluster et le serveur de certificats et les certificats secondaires.

1. Copiez les fichiers .pfx dans le nœud.
2. Ouvrez une fenêtre PowerShell en tant qu’administrateur et entrez les commandes suivantes. Remplacez la *$pswd* avec le mot de passe que vous avez utilisé pour créer ce certificat. Remplacez le *$PfxFilePath* par le chemin d’accès complet de la .pfx copiés sur ce nœud.

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. Vous devez ensuite définir le contrôle d’accès sur ce certificat, afin que le processus de Service Fabric, qui s’exécute sous le compte Service réseau, peut l’utiliser en exécutant le script suivant. Fournir l’empreinte numérique du certificat et de « SERVICE réseau » pour le compte de service. Vous pouvez vérifier que les ACL sur le certificat sont correctes à l’aide de l’outil certmgr.exe et en examinant le gérer les clés privées sur le certificat.

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys\";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. Repeat the steps above for each server certificate. You can also use these steps to install the client certificates on the machines that you want to allow access to the cluster.

## Create the secure cluster
After configuring the **security** section of the **ClusterConfig.X509.MultiMachine.json** file, you can proceed to [Create your cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) section to configure the nodes and create the standalone cluster. Remember to use the **ClusterConfig.X509.MultiMachine.json** file while creating the cluster. For example, your command might look like the following:

```
.\CreateServiceFabricCluster.ps1 ClusterConfigFilePath -.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab - AcceptEULA $true
```

Once you have the secure standalone Windows cluster successfully running, and have setup the authenticated clients to connect to it, follow the section [Connect to a secure cluster using PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) to connect to it. For example:

```
Connexion-ServiceFabricCluster - ConnectionEndpoint 10.7.0.4:19000 - KeepAliveIntervalInSec 10 - X509Credential - ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 - FindType FindByThumbprint - FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 - StoreLocation CurrentUser - StoreName mon
```

If you are logged on to one of the machines in the cluster, since this already has the certificate installed locally you can simply run the Powershell command to connect to the cluster and show a list of nodes:

```
ServiceFabricCluster connecter Get-ServiceFabricNode
```
To remove the cluster call the following command:

```
.\RemoveServiceFabricCluster.ps1 ClusterConfigFilePath -.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab
```
