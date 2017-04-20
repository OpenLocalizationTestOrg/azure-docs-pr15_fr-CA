<properties
   pageTitle="Cryptage disque Azure pour Windows et Linux IaaS VMs | Microsoft Azure"
   description="Le document fournit une vue d’ensemble du chiffrement Microsoft Azure disque pour Windows et Linux IaaS VMs."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="krkhan"/>


#<a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Cryptage disque Azure pour Windows et Linux IaaS VMs

Microsoft Azure s’engage à assurer votre confidentialité des données, relevant de la souveraineté données active vous permettent de contrôler votre Azure hébergé des données sur une plage d’avancées des technologies de cryptage, contrôler et gérer les clés de cryptage, d’audit et de contrôle des accès des données. Cette souplesse Azure clients pour choisir la solution qui répond le mieux à leurs besoins. Dans ce document, nous vous présenterai une nouvelle solution de technologie « Azure chiffrement de disque pour Windows et de Linux IaaS VM » pour protéger et protéger vos données pour répondre à vos engagements de conformité et sécurité de l’organisation. Le document fournit des instructions détaillées sur la façon d’utiliser les fonctionnalités de cryptage de disque Azure, y compris les scénarios pris en charge et l’utilisateur rencontre.

**Remarque**: certaines recommandations contenues dans cet article peuvent entraîner une augmentation des données, réseau ou utilisation des ressources calcul entraînant des coûts supplémentaires de licence ou d’un abonnement.

## <a name="overview"></a>Vue d’ensemble

Chiffrement de disque Azure est une nouvelle fonctionnalité qui vous permet de crypter vos disques de machine virtuelle Windows et Linux IaaS. Cryptage de disque Azure tire parti de la fonctionnalité [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) standard d’industrie de Windows et de la fonction [Crypt-DM](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le cryptage de volume pour le système d’exploitation et les disques de données. La solution est intégrée avec coffre-fort [Azure clé](https://azure.microsoft.com/documentation/services/key-vault/) pour vous aider à contrôler et de gérer les clés de cryptage de disque et les secrets de votre abonnement au coffre-fort clé, tout en s’assurant que toutes les données sur les disques de l’ordinateur virtuel sont cryptées au reste de votre stockage Azure.

Cryptage disque Azure pour Windows et Linux IaaS VMs est maintenant **Disponible** dans toutes les régions publiques Azure pour les ordinateurs virtuels et les ordinateurs virtuels Standard avec stockage de la prime.

### <a name="encryption-scenarios"></a>Scénarios de chiffrement

La solution de cryptage de disque Azure prend en charge les scénarios de client suivants :

- Activer le cryptage sur les nouveaux ordinateurs virtuels de IaaS créé à partir de VHD préalablement crypté et les clés de cryptage
- Activer le cryptage sur les nouveaux ordinateurs virtuels de IaaS créé à partir d’images de la galerie d’Azure
- Activer le cryptage sur les machines virtuelles IaaS existantes en cours d’exécution dans Azure
- Désactivez le cryptage sur les ordinateurs virtuels IaaS de Windows
- Désactivez le cryptage sur les lecteurs de données pour les machines virtuelles IaaS de Linux

La solution prend en charge les éléments suivants pour les machines virtuelles de IaaS lorsqu’il est activé dans Microsoft Azure :

- Intégration avec Azure coffre-fort de clé
- Couche standard de machines virtuelles - [A, D, DS, G, GS etc. série IaaS VMs](https://azure.microsoft.com/pricing/details/virtual-machines/)
- Activer le cryptage sur Windows et Linux IaaS VMs
- Désactiver le cryptage sur les lecteurs de système d’exploitation et des données pour les machines virtuelles IaaS de Windows
- Désactivez le cryptage sur les lecteurs de données pour les machines virtuelles IaaS de Linux
- Activer le cryptage sur les ordinateurs virtuels IaaS en exécutant le système d’exploitation du Client Windows
- Activer le cryptage sur des volumes avec les chemins de montage
- Activer le cryptage sur les ordinateurs virtuels de Linux configuré avec RAID basé sur le logiciel système 
- Activer le cryptage sur les ordinateurs virtuels de Windows configurée avec des espaces de stockage
- Toutes les régions publiques Azure sont pris en charge.

La solution ne supporte pas les scénarios suivants, fonctionnalités et technologies dans la version :

- Couche base IaaS VMs
- Désactiver le cryptage sur le lecteur du système d’exploitation pour les machines virtuelles IaaS de Linux
- IaaS machines virtuelles à l’aide de la méthode classique de création de machine virtuelle
- Intégration avec votre Service de gestion de clés sur site
- Présentation technique de Windows Server 2016 n’est pas pris en charge dans cette version
- Azure fichiers (partage de fichier Azure), du système de fichiers réseau (NFS), les volumes dynamiques, VM Windows configuré avec les systèmes RAID logiciel


### <a name="encryption-features"></a>Fonctionnalités de cryptage

Lorsque vous activez et déployez de cryptage de disque Azure pour les machines virtuelles de IaaS Azure, les fonctionnalités suivantes sont activées, en fonction de la configuration fournie :

- Chiffrement du volume du système d’exploitation afin de protéger le volume de démarrage au repos dans le stockage de client
- Cryptage de volume de données/s pour protéger les volumes de données inactives dans le stockage de client
- Désactiver le cryptage sur les lecteurs de système d’exploitation et des données pour les machines virtuelles IaaS de Windows
- Désactivez le cryptage sur les lecteurs de données pour les machines virtuelles IaaS de Linux
- Sauvegarder les clés de cryptage et les secrets du client les abonnement Azure coffre-fort de clé
- Indication de l’état de chiffrement de VM IaaS crypté
- Suppression des paramètres de configuration de chiffrement de disque à partir de l’ordinateur virtuel IaaS

Le cryptage de disque Azure pour IaaS VMS de solution Windows et Linux inclut l’extension de chiffrement de disque pour Windows, l’extension de chiffrement de disque pour Linux, les applets de commande PowerShell de cryptage disque, les applets de commande CLI de cryptage disque et les modèles de disque cryptage Azure le Gestionnaire de ressources. La solution de cryptage de disque Azure est pris en charge sur les machines virtuelles de IaaS fonctionnant sous Windows ou le système d’exploitation Linux. Pour plus de détails sur les systèmes d’exploitation pris en charge, consultez la section conditions préalables ci-dessous.

**Remarque**: il n’y a aucun frais supplémentaires pour le chiffrement des disques VM avec le cryptage de disque Azure.

### <a name="value-proposition"></a>Proposition de valeur

La solution de gestion de cryptage de disque Azure permet aux besoins des entreprises dans le nuage :

-   IaaS VM sont sécurisés au repos à l’aide de la technologie de cryptage standard pour les exigences de conformité et de sécurité de l’organisation adresse.
-   Démarrage de IaaS VM sous client contrôlée clés et des règles, et ils peuvent auditer leur utilisation dans le coffre-fort de la clé.


### <a name="encryption-workflow"></a>Chiffrement de flux de travail

Étapes requises pour activer le chiffrement de disque pour Windows et de Linux VM de haut niveau sont les suivantes :

1. Le client choisit un scénario de cryptage dans les scénarios de cryptage ci-dessus
2. Client opte pour l’activation du cryptage de disque via le modèle de gestionnaire de ressources de cryptage de disque Azure ou les applets de commande PS ou les commande CLI et spécifie la configuration de chiffrement

    - Pour le scénario de disque dur virtuel client crypté, les client les téléchargements du disque dur virtuel crypté à leur stockage compte et cryptage matériel de clé pour la clé vault et fournissent la configuration du cryptage pour activer le cryptage sur une VM IaaS nouveau
    - Pour la nouvelle machine virtuelle créée à partir de la galerie Azure et l’ordinateur virtuel existant en cours d’exécution dans Azure, client fournissent la configuration du cryptage pour activer le cryptage sur la VM IaaS

3. Client accorde l’accès à la plateforme Azure pour lire le matériel de clé de chiffrement (systèmes de clés de chiffrement BitLocker pour Windows et Linux mot de passe) à partir de leur clé coffre-fort pour activer le cryptage sur la VM IaaS
4. Client fournir l’identité de l’application d’écrire la clé de cryptage dans leur coffre-fort clé pour activer le cryptage sur le IaaS VM pour les scénarios mentionnés dans #2 ci-dessus Azure AD
5.  Azure met à jour le modèle de service VM grâce au cryptage et configuration de coffre-fort clé et dispositions crypté de machine virtuelle pour le client

![Logiciel anti-programme malveillant de Microsoft dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Le déchiffrement de flux de travail

Les étapes de haut niveau requis pour désactiver le cryptage de disque pour de IaaS VM sont :

1. Le client choisit de désactiver le cryptage (décryptage) sur une VM IaaS en cours d’exécution dans Azure via le modèle de gestionnaire de ressources de cryptage de disque Azure ou les applets de commande PS et spécifie la configuration de décryptage.
2. L’étape de chiffrement disable Désactive le cryptage du volume du système d’exploitation ou des données ou les deux sur la machine virtuelle de IaaS de Windows en cours d’exécution. Toutefois la désactivation OS disque cryptage pour Linux n'est pas pris en charge comme indiqué dans la documentation ci-dessus. L’étape de désactivation est autorisé uniquement pour les lecteurs de données sur les ordinateurs virtuels de Linux. 
4. Azure met à jour le modèle de service de machine virtuelle et la VM IaaS est déchiffrée. Le contenu de la machine virtuelle n’est pas crypté plus au repos.
5. L’opération de cryptage de désactivation ne supprime pas le coffre-fort de clé du client et du matériel de clé de cryptage,-les clés de chiffrement BitLocker pour Windows ou Linux mot de passe.

## <a name="prerequisites"></a>Conditions préalables

Voici les conditions requises pour activer le cryptage de disque d’Azure sur Azure IaaS VM pour les scénarios pris en charge dans la section vue d’ensemble

- Utilisateur doit avoir un abonnement Azure actif valide pour créer des ressources dans Azure dans les régions pris en charge
- Cryptage de disque Azure est pris en charge sur Windows serveur suivant SKU – Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2. Présentation technique de Windows Server 2016 n’est pas pris en charge dans cette version.
- Cryptage de disque Azure est pris en charge sur le Windows client suivant référence - Client de Windows 8 et Windows 10 Client.

**Remarque**: pour Windows Server 2008 R2, .net framework 4.5 doit être installé avant d’activer le chiffrement dans Azure. Vous pouvez l’installer à partir de Windows update en installant la mise à jour facultative « Microsoft.NET Framework 4.5.2 pour Windows Server 2008 R2 x 64-systèmes ([KB2901983](https://support.microsoft.com/kb/2901983)) »

- Chiffrement de disque Azure est pris en charge sur Linux serveur suivant SKU - Ubuntu, CentOS, SUSE et SUSE Linux Enterprise Server (SLES) et Red Hat Enterprise Linux.

**Remarque**: le chiffrement des disques du système d’exploitation de Linux est actuellement pris en charge sur les CentOS de distributions - 7.2 de RHEL, Linux 7.2, Ubuntu 16.04 suivants

- Toutes les ressources (Ex : clé de coffre-fort, stockage de compte, VM, etc.) doivent appartenir à la même région d’Azure et d’abonnement.

**Remarque**: le chiffrement de disque Azure requiert que le coffre-fort de la clé et les ordinateurs virtuels résident dans la même région d’Azure. Les configurer dans une zone distincte entraîne Échec de l’activation de la fonctionnalité de chiffrement disque Azure.

- Pour installer et configurer le coffre-fort de clé Azure pour l’utilisation du cryptage disque Azure, consultez la section **définition et configuration de coffre-fort Azure clé pour l’utilisation du cryptage disque Azure** dans la section *conditions préalables* de cet article.
- Pour installer et configurer les applications Azure AD dans Azure Active directory pour l’utilisation du cryptage disque Azure, consultez la section **d’installation de l’Application AD Azure dans Azure Active Directory** dans la section *conditions préalables* de cet article.
- Pour installer et configurer la stratégie d’accès de stockage en chambre forte de clé d’Application AD Azure, reportez-vous à la section **stratégie de paramètre de clé de coffre-fort accès pour l’Application de AD Azure** dans la section *conditions préalables* de cet article.
- Pour préparer un disque dur virtuel de Windows déjà cryptés, voir la section **Préparation d’un disque dur virtuel Windows Pre-crypté** dans l’appendice de cet article.
- Pour préparer un VHD Linux préalablement crypté, consultez la section **Préparation d’un disque dur virtuel Linux préalablement crypté** dans l’appendice de cet article.
- Plateforme Azure a besoin d’accéder aux clés de chiffrement ou secrets du client Azure clé coffre-fort afin de les rendre disponibles pour l’ordinateur virtuel pour démarrer et déchiffrer le volume de l’ordinateur virtuel du système d’exploitation. Pour accorder des autorisations à la plateforme Azure pour accéder le coffre-fort de la clé du client, les **enabledForDiskEncryption** propriété doit être définie sur le coffre-fort de la clé de cette exigence. Reportez-vous à la section **définition et configuration de coffre-fort Azure clé pour l’utilisation du cryptage disque Azure** dans l’appendice de cet article pour plus de détails.
- Le secret de la chambre forte de clé et URL de clé (KEK) de chiffrement à clé doivent être créée. Azure applique cette restriction de contrôle de version. Voir des exemples de secret valide et KEK URL ci-dessous :
    - Exemple d’URL secret valide :   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
    - Exemple de valide KRK KEK :   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Chiffrement de disque Azure ne supporte pas les numéros de port qui est spécifiés dans le cadre du secret de la chambre forte de clé et les URL KEK. Voir les exemples ci-dessous pour l’URL de coffre-fort de clé prises en charge :
    - URL de coffre-fort de clé non acceptées   *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
    - URL de coffre-fort de clé acceptées :   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Pour activer le cryptage de disque Azure fonctionnalité, les ordinateurs virtuels de IaaS doit répondre aux exigences de configuration de point de terminaison de réseau suivantes : 
    - Le IaaS VM doit être capable de se connecter au point de terminaison Azure Active Directory \[Login.windows.net\] pour obtenir un jeton pour se connecter à la chambre forte de clé Azure
    - Le IaaS VM doit être capable de se connecter au point de terminaison Azure clé coffre-fort pour écrire les clés de cryptage à la chambre forte de la clé de client
    - Le IaaS VM doit être capable de se connecter au point de terminaison de stockage Azure qui héberge le référentiel d’extension Azure et un compte de stockage Azure qui héberge les fichiers de disque dur virtuel

**Remarque :** Si votre stratégie de sécurité limite l’accès à Internet à partir d’ordinateurs virtuels d’Azure, vous pouvez résoudre l’URI ci-dessus, à laquelle vous avez besoin connectivité et configurez une règle spécifique pour permettre la connectivité sortante pour les adresses IP.

- Utiliser la dernière version de la version du Kit de développement logiciel Azure PowerShell pour configurer le cryptage de disque Azure. Télécharger la dernière version de la [mise à jour Azure PowerShell](https://github.com/Azure/azure-powershell/releases)

**Remarque :** Azure cryptage de disque n’est pas pris en charge sur [Azure PowerShell SDK version 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Si vous recevez une erreur liée à l’utilisation de PowerShell d’Azure 1.1.0, veuillez consulter l’article [Azure disque cryptage erreur aux Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

- Pour exécuter les commandes CLI d’Azure et l’associer à votre abonnement Azure, vous devez d’abord installer version Azure CLI :
    - Pour installer l’interface CLI d’Azure et l’associer à votre abonnement Azure, voir [Comment faire pour installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md)
    - À l’aide de la CLI Azure pour Mac, Linux et Windows avec le Gestionnaire de ressources Azure, consultez [ici](azure-cli-arm-commands.md)
- Solution de cryptage de disque Azure utiliser BitLocker protecteur de clé externe pour les machines virtuelles IaaS de Windows. Si vos ordinateurs virtuels sont joint au domaine, ne pas distribuer des stratégies de groupe qui appliquent les protecteurs de module de plateforme sécurisée. Reportez-vous à [cet article](https://technet.microsoft.com/library/ee706521) pour plus d’informations sur la stratégie de groupe pour « Autoriser BitLocker sans un module de plateforme sécurisée compatible ».
- Le disque Azure cryptage requis de script PowerShell pour créer l’application de AD Azure, créer nouveau coffre-fort clé ou coffre-fort de clé existant le programme d’installation et activer le cryptage se trouve [ici](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).

#### <a name="setup-the-azure-ad-application-in-azure-active-directory"></a>Programme d’installation l’Application Azure AD dans Azure Active Directory

Lorsque le cryptage doit être activé sur un ordinateur virtuel en cours d’exécution Azure, le chiffrement des disques Azure génère et écrit les clés de cryptage de votre coffre-fort de clé. Gestion des clés de cryptage dans le coffre-fort de la clé a besoin de l’authentification Active Directory Azure.

À cet effet, une application Azure AD doit être créée. Les étapes détaillées pour l’inscription d’une application vous trouverez ici, dans la section « Obtenir une identité d’Application » dans ce [billet de blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx).  Cette publication contient également un certain nombre d’exemples utiles sur la mise en service et la configuration de votre coffre-fort de clé. À des fins d’authentification, l’authentification basée sur le secret du client ou l’authentification client basée sur certificat de publicité Azure peut être utilisée.

##### <a name="client-secret-based-authentication-for-azure-ad"></a>Authentification pour Azure publicité basée sur le secret du client

Les sections qui suivent présentent les étapes nécessaires pour configurer une secret en fonction de l’authentification du client pour Azure AD.

##### <a name="create-a-new-azure-ad-app-using-azure-powershell"></a>Créer une nouvelle application Azure AD à l’aide de PowerShell d’Azure

Utilisez l’applet de commande PowerShell ci-dessous pour créer une nouvelle application de AD Azure :

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**Remarque :** $azureAdApplication.ApplicationId est l’identifiant du client AD Azure et $aadClientSecret est le code Secret que vous devez utiliser ultérieurement pour activer ADE du client. Vous devez sauvegarder le secret du client AD Azure correctement.


##### <a name="provisioning-the-azure-ad-client-id-and-secret-from-the-azure-classic-deployment-model-portal"></a>Mise en service de l’ID de client AD Azure et le secret à partir du modèle de déploiement classique Azure Portal

Azure AD Client ID et mot de passe peut également être configuré à l’aide du modèle de déploiement classique Azure Portal à https://manage.windowsazure.com, suivez les étapes ci-dessous pour effectuer cette tâche :

1.Cliquez sur l’onglet Active Directory comme indiqué dans la Figure ci-dessous :

![Chiffrement de disque Azure](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2.Cliquez sur Ajouter Application et tapez le nom de l’application, comme indiqué ci-dessous :

![Chiffrement de disque Azure](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. Cliquez sur le bouton de la flèche et configurer les propriétés de l’application, comme indiqué ci-dessous :

![Chiffrement de disque Azure](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Cliquez sur la case à cocher dans le coin inférieur gauche pour terminer. Page de configuration de l’application s’affiche. Notez que l’ID de Client AD de Azure se trouve dans la partie inférieure de la page, comme illustré à la figure ci-dessous.

![Chiffrement de disque Azure](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5.les enregistrer le mot de passe du client AD Azure par clic sur le bouton Enregistrer. Cliquez sur Enregistrer bouton et notez le secret à partir de la zone de texte clés, c’est le mot de passe du client AD Azure. Vous devez sauvegarder le secret du client AD Azure correctement.

![Chiffrement de disque Azure](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


**Remarque :** ce flux ci-dessus n’est pas pris en charge dans le portail.

##### <a name="use-an-existing-app"></a>Utiliser une application existante

Pour exécuter les commandes ci-dessous, vous devez le module PowerShell de publicité Azure, qui peut être obtenu à partir [d’ici](https://technet.microsoft.com/library/jj151815.aspx).

**Remarque :** les commandes ci-dessous doivent être exécutées à partir d’une nouvelle fenêtre de PowerShell. N’utilisez pas de Azure PowerShell ou dans la fenêtre du Gestionnaire de ressources Azure pour exécuter ces commandes. La raison de cette recommandation est parce que ces applets de commande dans le module de MSOnline ou l’Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Authentification de certificat basé pour Azure AD

> [AZURE.NOTE] Authentification par certificat de DAS n’est actuellement pas pris en charge sur les ordinateurs virtuels de Linux.

Les sections qui suivent présentent les étapes nécessaires pour configurer une authentification de certificat basé pour Azure AD.

##### <a name="create-a-new-azure-ad-app"></a>Créer une nouvelle application de AD Azure

Exécuter les applets de commande PowerShell ci-dessous pour créer une nouvelle application de AD Azure :

**Remarque :** Remplacer `yourpassword` chaîne ci-dessous avec votre mot de passe sécurisé et protéger le mot de passe.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Une fois que vous avez terminé cette étape, télécharger un fichier .pfx à clé coffre-fort et activer la stratégie d’accès nécessaire pour déployer un certificat à un ordinateur virtuel.

##### <a name="use-an-existing-azure-ad-app"></a>Utiliser une application Azure AD existante
Si vous configurez l’authentification de certificat basé pour une application existante, utilisez les applets de commande PowerShell ci-dessous. Veillez à les exécuter à partir d’une nouvelle fenêtre de PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Une fois que vous avez terminé cette étape, télécharger un fichier .pfx à clé coffre-fort et activer la stratégie d’accès nécessaire pour déployer un certificat à un ordinateur virtuel.

##### <a name="upload-a-pfx-file-to-key-vault"></a>Télécharger un fichier PFX à clé de coffre-fort
Vous pouvez lire ce [billet de blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) pour des explications détaillées sur le fonctionnement de ce processus. Toutefois, les applets de commande PowerShell ci-dessous sont que nécessaires pour cette tâche. Veillez à les exécuter à partir de la console PowerShell d’Azure :

**Remarque :** Remplacer `yourpassword` chaîne ci-dessous avec votre mot de passe sécurisé et protéger le mot de passe.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-key-vault-to-an-existing-vm"></a>Déployer un certificat dans le coffre-fort de la clé à un ordinateur virtuel existant
Après avoir terminé le téléchargement du PFX, utilisez les étapes ci-dessous pour déployer un certificat dans le coffre-fort de la clé à un ordinateur virtuel existant :

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### <a name="setting-key-vault-access-policy-for-the-azure-ad-application"></a>Définition de stratégie d’accès de stockage en chambre forte de clé d’Application Azure AD

Votre application Azure AD doit avoir des droits pour accéder aux clés ou aux secrets dans la chambre forte. Utilisez l’applet de commande [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) pour accorder des autorisations à l’application, à l’aide de l’Id de Client (qui a été généré lors de l’application a été enregistrée) comme valeur de paramètre – ServicePrincipalName. Vous pouvez lire [ce billet de blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) pour obtenir des exemples sur ce. Ci-dessous, vous avez également un exemple de la façon d’effectuer cette tâche via PowerShell :

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

**Remarque**: le chiffrement des disques Azure vous permettent de configurer les stratégies d’accès suivants à votre Application de Client DAS - nécessite les autorisations 'WrapKey' et 'Set'

## <a name="terminology"></a>Terminologie

Utilisez le tableau de la terminologie en tant que référence de comprendre certains termes couramment utilisés par cette technologie :

| Terminologie           | Définition                                                                                                                                                                                                                                   |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Annonce Azure                   | Azure est [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Compte Azure d’Active Directory est une condition préalable pour l’authentification, de stocker et de récupérer les secrets de la chambre forte de clé.                                                                                                        |
| Vault clé Azure [AKV] | Coffre-fort de clé Azure est un service de gestion de clés cryptographiques basé sur validés FIPS des Modules de sécurité matériels pour la sauvegarde de vos clés de chiffrement et les secrets sensibles en toute sécurité., reportez-vous à la documentation [Clé de coffre-fort](https://azure.microsoft.com/services/key-vault/) pour plus de détails.          |
| ARM                   | Gestionnaire de ressources Azure                                                                                                                                                                                                                       |
| BitLocker             | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) est qu'une industrie reconnu la technologie de chiffrement de volume de Windows utilisée pour activer le cryptage de disque sur les ordinateurs virtuels IaaS de Windows                                                                                                                  |
| BEK                   | Les clés de chiffrement BitLocker permet de chiffrer le volume de démarrage du système d’exploitation et les volumes de données. Les clés BitLocker sont la sauvegarde dans le coffre-fort de clé Azure du client en tant que secrets.                                                                              |
| CLI                   | [Interface de ligne de commande Azure](../xplat-cli-install.md)                                                                                                                                                                                                                 |
| DM-Crypt.              | [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) est le sous-système de chiffrement de disque Linux transparent utilisé pour activer le cryptage de disque sur les ordinateurs virtuels IaaS de Linux                                                                                                                           |
| KEK                   | Clé de cryptage est la clé asymétrique (RSA 2048) permet de protéger ou encapsuler le secret si vous le souhaitez. Vous pouvez fournir un HSM protégé par une clé ou protégé par le logiciel. Pour plus de détails, reportez-vous à la documentation [d’Azure clé de coffre-fort](https://azure.microsoft.com/services/key-vault/) pour plus de détails |
| PS applets de commande            | [Applets de commande PowerShell Azure](powershell-install-configure.md)                                                                                                                                                                                                                                           |

### <a name="setting-and-configuring-azure-key-vault-for-azure-disk-encryption-usage"></a>Définition et configuration de coffre-fort clé Azure pour Azure l’utilisation de cryptage du disque

Chiffrement de disque Azure protège les clés de cryptage de disque et les secrets de votre coffre-fort de clé Azure. Suivez les étapes sur chacun d’eux des sections ci-dessous pour le programme d’installation en chambre forte de la clé pour l’utilisation du cryptage disque Azure.

#### <a name="create-a-new-key-vault"></a>Créer un coffre-fort clé
Pour créer un nouveau coffre-fort de clé, utilisez une des options répertoriées ci-dessous :

- Utilisez le modèle de gestionnaire de ressources « 101-créer-KeyVault » qui se trouve [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)
- Utilisez PowerShell d’Azure [applets de commande de stockage en chambre forte de clé](https://msdn.microsoft.com/library/dn868052.aspx).
- Utiliser le portail de gestionnaire de ressources Azure.

**Remarque :** Si vous avez déjà une installation de stockage en chambre forte de clé pour votre abonnement, passez à la section suivante.

![Chambre forte de clé Azure](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="provisioning-a-key-encryption-key-optional"></a>Mise en service d’une clé de cryptage (facultatif)

Si vous souhaitez utiliser une clé de cryptage de clé (KEK) pour une couche de sécurité supplémentaire pour encapsuler les clés de chiffrement BitLocker, vous devez ajouter une KEK à votre coffre-fort de clé à utiliser dans le processus de déploiement.  L’applet de commande [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) permet de créer une nouvelle clé de cryptage dans le coffre-fort de la clé. Vous pouvez également importer des KEK à partir de votre gestionnaire de clés sur site HSM. Pour plus de détails, voir la [documentation de chambre forte de clé](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

La KEK peut être ajouté à partir du portail Azure le Gestionnaire de ressources aussi bien à l’aide d’Azure clé coffre-fort UX

![Chambre forte de clé Azure](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions-to-allow-the-azure-platform-access-to-the-keys-and-secrets"></a>Définir des autorisations pour permettre l’accès de la plateforme Azure pour les clés et les secrets en chambre forte de la clé

La plateforme Azure a besoin d’accéder aux clés de chiffrement ou secrets dans votre coffre-fort de clé Azure afin de les rendre disponibles pour l’ordinateur virtuel pour démarrer et décrypter les volumes. Pour accorder des autorisations à la plateforme Azure pour qu’il puisse accéder le coffre-fort de la clé, la propriété *enabledForDiskEncryption* doit être définie sur le coffre-fort de la clé. Vous pouvez définir la propriété enabledForDiskEncryption sur votre clé coffre-fort à l’aide de l’applet de commande PS de coffre-fort clé :

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Vous pouvez également définir la propriété *enabledForDiskEncryption* , visitez https://resources.azure.com. Comme mentionné précédemment, vous devez définir la propriété *enabledForDiskEncryption* sur votre coffre-fort de clé. Dans le cas contraire, le déploiement va échouer.

Vous pouvez configurer des stratégies d’accès à votre application de DAS à partir de l’expérience utilisateur en chambre forte de clé :

![Chambre forte de clé Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Chambre forte de clé Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Assurez-vous que le coffre-fort de clé est activé pour le cryptage de disque dans « Stratégies d’accès avancés » :

![Chambre forte de clé Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Scénarios de déploiement de cryptage de disque et de l’expérience utilisateur

Nombreux sont les scénarios que vous pouvez activer le cryptage de disque et les étapes peuvent varier selon le scénario. Les sections qui suivent couvre plus ces scénarios.

### <a name="enable-encryption-on-new-iaas-vms-created-from-the-azure-gallery"></a>Activer le cryptage sur de nouvelles IaaS VM créé à partir de la galerie d’Azure

Cryptage de disque peut être activé sur l’ordinateur virtuel Windows IaaS nouvelle à partir de la galerie Azure dans Azure à l’aide du modèle de gestionnaire de ressources publiées [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image). Cliquez sur le bouton « Déployer sur Azure » sur le modèle de démarrage rapide Azure, configuration de cryptage d’entrée de la lame de paramètres, puis cliquez sur OK. Sélectionnez l’abonnement, groupe de ressources, ressources emplacement, les termes juridiques et groupe accord et cliquez sur le bouton Créer pour activer le cryptage sur une VM IaaS nouvelle.

**Remarque :** Ce modèle crée un nouveau cryptée Windows VM à l’aide de l’image de la galerie Windows Server 2012.

Cryptage du disque dur peut être activé sur un nouvel ordinateur IaaS RedHat Linux 7.2 virtuel avec une baie RAID-0 de 200 Go à l’aide de [ce](https://aka.ms/fde-rhel) modèle de gestionnaire de ressources. Une fois que le modèle est déployé, vérifiez l’état de chiffrement de VM à l’aide du `Get-AzureRmVmDiskEncryptionStatus` cmdlet comme décrit dans la section «[lecteur de cryptage de système d’exploitation sur un ordinateur virtuel de Linux en cours d’exécution](#encrypting-os-drive-on-a-running-linux-vm)». Lorsque l’ordinateur retourne le statut `VMRestartPending`, redémarrez l’ordinateur virtuel.

Vous pouvez voir les détails de paramètres de modèle de gestionnaire de ressources pour la nouvelle machine virtuelle à partir de scénario Azure galerie à l’aide de l’ID Azure AD Client dans le tableau ci-dessous :

| Paramètre                        | Description|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName                 | Nom d’utilisateur administrateur de l’ordinateur virtuel                                                                                                                           |
| adminPassword                 | Mot de passe administrateur pour l’ordinateur virtuel                                                                                                                       |
| newStorageAccountName         | Nom du compte de stockage pour stocker le système d’exploitation et des données de disques durs virtuels                                                                                                             |
| vmSize                        | Taille de la machine virtuelle. Actuellement, seules les séries Standard A, D et G sont pris en charge                                                                                          |
| virtualNetworkName            | Nom de la VNet auquel doit appartenir le NIC VM.                                                                                                            |
| subnetName                    | Nom du sous-réseau dans le vNet auquel doit appartenir le NIC VM                                                                                               |
| AADClientID                   | ID de client de l’application Azure AD qui dispose d’autorisations pour écrire des secrets dans le coffre-fort de la clé                                                                                       |
| AADClientSecret               | Secret de client de l’application Azure AD qui dispose d’autorisations pour écrire des secrets dans le coffre-fort de la clé                                                                                   |
| keyVaultURL                   | URL de la chambre forte de clé sur lequel BitLocker clé doit être téléchargée pour. Vous pouvez obtenir à l’aide de l’applet de commande : (Get-AzureRmKeyVault - VaultName,-ResourceGroupName). VaultURI |
| keyEncryptionKeyURL           | URL de la clé de cryptage utilisée pour crypter la clé BitLocker générée. Cette option est facultative.                                                               |
| keyVaultResourceGroup         | Groupe de ressources de la chambre forte de clé                                                               |
| vmName                        | Nom de la machine virtuelle sur quel cryptage opération doit être effectuée


**Remarque :** KeyEncryptionKeyURL est un paramètre facultatif. Vous pouvez mettre votre propre KEK de sauvegarde supplémentaire de la clé de cryptage de données (mot de passe secret) dans le coffre-fort de la clé.

### <a name="enable-encryption-on-new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Activer le cryptage sur de nouvelles IaaS VM créé à partir de disque dur virtuel de client crypté et le cryptage des clés

Dans ce scénario, vous pouvez activer le cryptage à l’aide du modèle de gestionnaire de ressources, les applets de commande PowerShell ou les commandes CLI. Les sections ci-dessous explique de façon plus détaillée les commandes de la CLI et le modèle du Gestionnaire de ressources.

Suivez les instructions de l’une de ces sections pour la préparation d’images préalablement chiffrés qui peuvent être utilisés dans Azure. Une fois que l’image est créée, les étapes décrites dans la section suivante peuvent servir pour la création d’un crypté Azure VM.

- [Préparation d’un disque dur virtuel de Windows déjà chiffré](#preparing-a-pre-encrypted-windows-vhd)
- [Préparation d’un VHD Linux préalablement crypté](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-resource-manager-template"></a>À l’aide du Gestionnaire de ressources du modèle

Cryptage du disque dur peut être activé sur client crypté VHD à l’aide du modèle de gestionnaire de ressources publiées [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm). Cliquez sur le bouton « Déployer sur Azure » sur le modèle de démarrage rapide Azure, configuration de cryptage d’entrée de la lame de paramètres, puis cliquez sur OK. Sélectionnez l’abonnement, groupe de ressources, ressources emplacement, les termes juridiques et groupe accord et cliquez sur le bouton Créer pour activer le cryptage sur Nouveau IaaS VM.

Les détails de paramètres de modèle de gestionnaire de ressources pour le scénario de disque dur virtuel client crypté sont décrits dans le tableau ci-dessous :

| Paramètre                        | Description|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | Nom du compte de stockage pour stocker le disque dur virtuel de crypté du système d’exploitation. Ce compte de stockage doit ont déjà été créé dans le même groupe de ressources et le même emplacement que la machine virtuelle                                                     |
| osVhdUri              | Disque dur virtuel de l’URI du système d’exploitation à partir du compte de stockage                                                                                                                                                                                      |
| osType                | Type de produit du système d’exploitation (Windows/Linux)                                                                                                                                                                                         |
| virtualNetworkName    | Nom de la VNet auquel doit appartenir le NIC VM. Cela doit déjà créé dans le même groupe de ressources et le même emplacement que la machine virtuelle                                                                     |
| subnetName            | Nom du sous-réseau dans le vNet auquel doit appartenir le NIC VM                                                                                                                                                     |
| vmSize                | Taille de la machine virtuelle. Actuellement, seules les séries Standard A, D et G sont pris en charge                                                                                                                                                |
| keyVaultResourceID    | ResourceID identifiant la ressource clé coffre-fort dans ARM. Vous pouvez obtenir à l’aide de l’applet de commande PowerShell : (Get-AzureRmKeyVault - VaultName &lt;yourKeyVaultName&gt; - ResourceGroupName &lt;yourResourceGroupName&gt;). ResourceId |
| keyVaultSecretUrl     | URL de la clé de cryptage de disque mis en service dans une chambre forte de clé                                                                                                                                                                |
| keyVaultKekUrl        | URL de la clé de cryptage qui consiste à crypter la clé de cryptage de disque générée                                                                                                                                       |
| vmName               | Nom de l’ordinateur virtuel IaaS   |


#### <a name="using-powershell-cmdlets"></a>À l’aide des applets de commande PowerShell

Cryptage du disque dur peut être activé sur client crypté VHD à l’aide des applets de commande PS publié [ici](https://msdn.microsoft.com/library/azure/mt603746.aspx).  

#### <a name="using-cli-commands"></a>À l’aide de commandes CLI

Suivez les étapes ci-dessous pour activer le chiffrement de disque pour ce scénario à l’aide de commandes CLI :

1. Définir les stratégies d’accès sur la clé de stockage en chambre forte :
    - La valeur 'EnabledForDiskEncryption' indicateur :`azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
    - Définir des autorisations pour Azure AD app pour écrire des secrets dans KeyVault :`azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]`
2. Pour activer le cryptage sur un ordinateur virtuel d’existant/en cours d’exécution, tapez :  *azure vm activer disque-chiffrement--des groupes de ressources <resourceGroupName> --nom <vmName> --DAS-client-id <aadClientId> --DAS-client-secret <aadClientSecret> --disque-cryptage-clé-vault-url <keyVaultURL> --disque-cryptage-clé-vault-id <keyVaultResourceId> *
3. Obtenir l’état de cryptage : *« azure vm afficher-disque-cryptage-status--des groupes de ressources <resourceGroupName> --nom <vmName> --json »*
4. Pour activer le cryptage sur une machine virtuelle à partir de clients cryptées VHD, utiliser les paramètres avec la commande « Créer azure vm » ci-dessous :
    - disque-cryptage-clé-vault-id < disque-cryptage-clé-vault-id >
    - disque-cryptage-clé-url < disque-cryptage-clé-url >
    - cryptage-clé-vault-id de clé <-cryptage-clé-vault-id de clé >
    - clé de cryptage-clé-url < clé de chiffrement à clé-url >


### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Activer le cryptage sur existant ou en exécutant la machine virtuelle Windows de IaaS dans Azure

Dans ce scénario, vous pouvez activer le cryptage à l’aide du modèle de gestionnaire de ressources, les applets de commande PowerShell ou les commandes CLI. Les sections ci-dessous explique de façon plus détaillée comment activer à l’aide du Gestionnaire de ressources du modèle et les commandes CLI.

#### <a name="using-resource-manager-template"></a>À l’aide du Gestionnaire de ressources du modèle

Cryptage du disque dur peut être activé sur le Windows existant ou l’exécution IaaS VM dans Azure à l’aide du modèle de gestionnaire de ressources publiées [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm). Cliquez sur le bouton « Déployer sur Azure » sur le modèle de démarrage rapide Azure, configuration de cryptage d’entrée de la lame de paramètres, puis cliquez sur OK. Sélectionnez l’abonnement, groupe de ressources, ressources emplacement, les termes juridiques et groupe accord et cliquez sur le bouton Créer pour activer le cryptage sur existant ou l’exécution IaaS VM.

Les détails de paramètres de modèle de gestionnaire de ressources pour le scénario de machine virtuelle existante ou l’exécution à l’aide de l’ID Azure AD Client sont disponibles dans le tableau ci-dessous :

| Paramètre                 | Description|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AADClientID            | ID de client de l’application Azure AD qui dispose d’autorisations pour écrire des secrets dans le coffre-fort de la clé                                                                                                                                              |
| AADClientSecret         | Secret de client de l’application Azure AD qui dispose d’autorisations pour écrire des secrets dans le coffre-fort de la clé                                                                                                                                          |
| keyVaultName | Nom de la chambre forte de clé sur lequel BitLocker clé doit être téléchargée pour. Vous pouvez obtenir à l’aide de l’applet de commande : (Get-AzureRmKeyVault - ResourceGroupName <yourResourceGroupName>). Vaultname   |
|  keyEncryptionKeyURL   | URL de la clé de cryptage utilisée pour crypter la clé BitLocker générée. Cette étape est facultative si vous sélectionnez `nokek` dans la liste déroulante UseExistingKek. Si vous sélectionnez `kek` dans la liste déroulante UseExistingKek, vous devez entrer la valeur de keyEncryptionKeyURL                                                                                                                        |
| volumeType             | Type du volume sur lequel le cryptage opération est exécutée. Les valeurs valides sont « OS », « Données », « Tout »                                                                                                                     |
| sequenceVersion         | Version de la séquence de l’opération de BitLocker. Incrémenter ce numéro de version chaque fois qu’une opération de cryptage de disque est effectuée sur l’ordinateur virtuel même                                                                             |
| vmName                 | Nom de la machine virtuelle sur quel cryptage opération doit être effectuée


**Remarque :** KeyEncryptionKeyURL est un paramètre facultatif. Vous pouvez mettre votre propre KEK de sauvegarde supplémentaire de la clé de cryptage de données (secret de cryptage BitLocker) dans le coffre-fort de la clé.

#### <a name="using-powershell-cmdlets"></a>À l’aide des applets de commande PowerShell

Pour le **cryptage de disque Azure d’Explorer avec Azure PowerShell** blog post [partie 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) et [partie 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) pour plus d’informations sur la façon d’activer le cryptage en utilisant le cryptage de disque à l’aide des applets de commande PS Azure, reportez-vous à la section.

#### <a name="using-cli-commands"></a>À l’aide de commandes CLI

Suivez les étapes ci-dessous pour activer le cryptage sur existant ou l’exécution IaaS Windows VM dans Azure à l’aide de commandes CLI :

1. Définir les stratégies d’accès sur la clé de stockage en chambre forte :
    - La valeur 'EnabledForDiskEncryption' indicateur : « keyvault azure ensemble-stratégie--nom de coffre-fort <keyVaultName> ---de-disque-cryptage true »
    - Définir des autorisations à l’application Azure AD pour écrire des secrets dans KeyVault : « keyvault azure ensemble-stratégie--nom de coffre-fort <keyVaultName> --spn <aadClientID> --perms-clés de [\"tous les\"]--perms-secrets de [\"tous les\"] »
2. Pour activer le cryptage sur un ordinateur virtuel d’existant/en cours d’exécution, tapez :  *azure vm activer disque-chiffrement--des groupes de ressources <resourceGroupName> --nom <vmName> --DAS-client-id <aadClientId> --DAS-client-secret <aadClientSecret> --disque-cryptage-clé-vault-url <keyVaultURL> --disque-cryptage-clé-vault-id <keyVaultResourceId> *
3. Obtenir l’état de cryptage : *« azure vm afficher-disque-cryptage-status--des groupes de ressources <resourceGroupName> --nom <vmName> --json »*
4. Pour activer le cryptage sur une machine virtuelle à partir de clients cryptées VHD, utiliser les paramètres avec la commande « Créer azure vm » ci-dessous :
    - disque-cryptage-clé-vault-id < disque-cryptage-clé-vault-id >
    - disque-cryptage-clé-url < disque-cryptage-clé-url >
    - cryptage-clé-vault-id de clé <-cryptage-clé-vault-id de clé >
    - clé de cryptage-clé-url < clé de chiffrement à clé-url >


### <a name="enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure"></a>Activer le cryptage sur existant ou en exécutant la machine virtuelle Linux de IaaS dans Azure

Cryptage de disque peut être activé sur la machine virtuelle Linux de IaaS existant ou l’exécution dans Azure à l’aide du modèle de gestionnaire de ressources publiées [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). Cliquez sur le bouton « Déployer sur Azure » sur le modèle de démarrage rapide Azure, configuration de cryptage d’entrée de la lame de paramètres, puis cliquez sur OK. Sélectionnez l’abonnement, groupe de ressources, ressources emplacement, les termes juridiques et groupe accord et cliquez sur le bouton Créer pour activer le cryptage sur existant ou l’exécution IaaS VM.

Les détails de paramètres de modèle de gestionnaire de ressources pour le scénario de machine virtuelle existante ou l’exécution à l’aide d’Azure AD Client ID sont décrits dans le tableau ci-dessous :

| Paramètre                 | Description|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AADClientID            | ID de client de l’application Azure AD qui dispose d’autorisations pour écrire des secrets dans le coffre-fort de la clé                                                                                                                                              |
| AADClientSecret         | Secret de client de l’application Azure AD qui dispose d’autorisations pour écrire des secrets dans le coffre-fort de la clé                                                                                                                                          |
| keyVaultName | Nom de la chambre forte de clé sur lequel BitLocker clé doit être téléchargée pour. Vous pouvez obtenir à l’aide de l’applet de commande : (Get-AzureRmKeyVault - ResourceGroupName <yourResourceGroupName>). Vaultname   |
|  keyEncryptionKeyURL   | URL de la clé de cryptage utilisée pour crypter la clé BitLocker générée. Ceci est facultatif si vous sélectionnez « nokek » dans la liste déroulante UseExistingKek. Si vous sélectionnez « kek » dans la liste déroulante UseExistingKek, vous devez entrer la valeur keyEncryptionKeyURL                                                                                                                        |
| volumeType             | Type du volume sur lequel le cryptage opération est exécutée. Les valeurs prises en charge valides sont « OS » / « Tous » (pour RHEL 7.2, 7.2 de CentOS & Ubuntu 16.04) et « Données » pour tous les autres distros.                                                                                                                      |
| sequenceVersion         | Version de la séquence de l’opération de BitLocker. Incrémenter ce numéro de version chaque fois qu’une opération de cryptage de disque est effectuée sur l’ordinateur virtuel même                                                                             |
| vmName                 | Nom de la machine virtuelle sur quel cryptage opération doit être effectuée
| mot de passe              | Tapez un mot de passe fort comme la clé de cryptage de données                                                                                                                                                                       |                                                                                                                                                                                                                                                      

**Remarque :** KeyEncryptionKeyURL est un paramètre facultatif. Vous pouvez mettre votre propre KEK de sauvegarde supplémentaire de la clé de cryptage de données (mot de passe secret) dans le coffre-fort de la clé.

#### <a name="cli-commands"></a>Commandes CLI

Cryptage du disque dur peut être activé sur client crypté VHD à l’aide de la commande CLI installée à partir [d’ici](../xplat-cli-install.md). Suivez les étapes ci-dessous pour activer le cryptage sur existant ou l’exécution IaaS Linux VM dans Azure à l’aide de commandes CLI :

1. Définir les stratégies d’accès sur la clé de stockage en chambre forte :
    - La valeur 'EnabledForDiskEncryption' indicateur : « keyvault azure ensemble-stratégie--nom de coffre-fort <keyVaultName> ---de-disque-cryptage true »
    - Définir des autorisations à l’application Azure AD pour écrire des secrets dans KeyVault : « keyvault azure ensemble-stratégie--nom de coffre-fort <keyVaultName> --spn <aadClientID> --perms-clés de [\"tous les\"]--perms-secrets de [\"tous les\"] »
2. Pour activer le cryptage sur un ordinateur virtuel d’existant/en cours d’exécution, tapez :  *azure vm activer disque-chiffrement--des groupes de ressources <resourceGroupName> --nom <vmName> --DAS-client-id <aadClientId> --DAS-client-secret <aadClientSecret> --disque-cryptage-clé-vault-url <keyVaultURL> --disque-cryptage-clé-vault-id <keyVaultResourceId> *
3. Obtenir l’état de cryptage : « azure vm afficher-disque-cryptage-status--des groupes de ressources <resourceGroupName> --nom <vmName> --json »
4. Pour activer le cryptage sur une machine virtuelle à partir de clients cryptées VHD, utiliser les paramètres avec la commande « Créer azure vm » ci-dessous.
    - *disque-cryptage-clé-vault-id < disque-cryptage-clé-vault-id >*
    - *disque-cryptage-clé-url < disque-cryptage-clé-url >*
    - *cryptage-clé-vault-id de clé <-cryptage-clé-vault-id de clé >*
    - *clé de cryptage-clé-url < clé de chiffrement à clé-url >*

### <a name="get-encryption-status-of-an-encrypted-iaas-vm"></a>Obtenir l’état de cryptage d’une VM IaaS crypté

Vous pouvez obtenir l’état de chiffrement à l’aide du Gestionnaire de ressources Azure portal, les [applets de commande PowerShell](https://msdn.microsoft.com/library/azure/mt622700.aspx) ou les commandes CLI. Les sections ci-dessous vous explique comment utiliser le portail Azure et les commandes CLI pour obtenir l’état de chiffrement.

#### <a name="get-encryption-status-of-an-encrypted-windows-vm-using-azure-resource-manager-portal"></a>Obtenir l’état de cryptage d’une VM Windows cryptés à l’aide du Gestionnaire de ressources Azure portal

Vous pouvez obtenir l’état de chiffrement de IaaS VM à partir du Gestionnaire de ressources Azure portal. Ouvrir une session sur Azure portal à https://portal.azure.com/, cliquez sur le lien de machines virtuelles dans le menu de gauche pour afficher un récapitulatif des ordinateurs virtuels dans votre abonnement. Vous pouvez filtrer la vue de machines virtuelles en sélectionnant le nom d’abonnement dans la liste déroulante d’abonnement. Cliquez sur les colonnes situées en haut du menu page de machines virtuelles. Sélectionnez la colonne de cryptage du disque dur de la lame de la colonne choisir et cliquez sur mise à jour. Vous devriez voir la colonne de cryptage de disque indiquant l’état de cryptage « Activé » ou « Non activé » pour chaque machine virtuelle comme illustré dans la figure ci-dessous.

![Logiciel anti-programme malveillant de Microsoft dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-encryption-status-of-an-encrypted-windowslinux-iaas-vm-using-disk-encryption-ps-cmdlet"></a>Obtenir l’état de cryptage d’un crypté (Windows/Linux) IaaS VM à l’aide du cryptage de disque applet de commande PS
Vous pouvez obtenir l’état de chiffrement de IaaS VM à partir de l’applet de commande disque cryptage PS « Get-AzureRmVMDiskEncryptionStatus ». Pour obtenir les paramètres de chiffrement de votre ordinateur virtuel, tapez dans votre session PowerShell d’Azure :

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

La sortie de Get-AzureRmVMDiskEncryptionStatus peut être inspectée pour le cryptage de clé URL.
    
    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMNam
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey
    
    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

Les valeurs des paramètres OSVolumeEncrypted et DataVolumesEncrypted sont définies à « Crypté » indiquant que les deux volumes sont chiffrés à l’aide du cryptage de disque Azure. Pour le **cryptage de disque Azure d’Explorer avec Azure PowerShell** blog post [partie 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) et [partie 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) pour plus d’informations sur la façon d’activer le cryptage en utilisant le cryptage de disque à l’aide des applets de commande PS Azure, reportez-vous à la section.

**Remarque**: sur les ordinateurs virtuels de Linux, la `Get-AzureRmVMDiskEncryptionStatus` applet de commande prend 3-4 minutes pour présenter l’état de cryptage.

#### <a name="get-encryption-status-of-the-iaas-vm-from-disk-encryption-cli-command"></a>Obtenir l’état de chiffrement de la VM IaaS de cryptage de disque commande CLI

Vous pouvez obtenir l’état de chiffrement de IaaS VM de cryptage de disque *azure vm afficher-disque-cryptage-statut*de la commande CLI. Pour obtenir les paramètres de chiffrement de votre ordinateur virtuel, tapez dans la session CLI d’Azure :

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Désactiver le cryptage sur l’ordinateur virtuel IaaS de Windows en cours d’exécution

Vous pouvez désactiver le cryptage sur un ordinateur virtuel IaaS de Linux ou de Windows en cours d’exécution via le modèle de gestionnaire de ressources de cryptage de disque Azure ou les applets de commande PS et spécifie la configuration de décryptage.


##### <a name="windows-vm"></a>Mémoire virtuelle de Windows

L’étape de chiffrement disable Désactive le cryptage du volume du système d’exploitation ou des données ou les deux sur la machine virtuelle de IaaS de Windows en cours d’exécution. Impossible de désactiver le volume du système d’exploitation et de laisser le volume de données chiffré. Lors de l’étape de chiffrement désactiver, modèle de déploiement classique d’Azure met à jour le modèle de service de machine virtuelle et l’ordinateur virtuel IaaS de Windows est déchiffré. Le contenu de la machine virtuelle n’est pas crypté plus au repos. Le cryptage de désactivation ne supprime pas le coffre-fort de clé du client et du matériel de clé de cryptage, c'est-à-dire les clés de chiffrement BitLocker pour Windows et le mot de passe pour Linux. 

##### <a name="linux-vm"></a>Virtual Media Linux

L’étape de chiffrement disable Désactive le cryptage du volume de données sur l’ordinateur Linux IaaS virtuel en cours d’exécution

**Remarque**: la désactivation du chiffrement sur disque du système d’exploitation n’est pas autorisée sur les ordinateurs virtuels de Linux.

##### <a name="disable-encryption-on-existingrunning-iaas-vm-in-azure-using-resource-manager-template"></a>Désactivez le cryptage sur existant ou l’exécution IaaS VM dans Azure à l’aide du Gestionnaire de ressources du modèle

Cryptage du disque dur peut être désactivé sur l’ordinateur virtuel IaaS de Windows en utilisant le modèle de gestionnaire de ressources en cours d’exécution publiée [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm). Cliquez sur le bouton « Déployer sur Azure » sur le modèle de démarrage rapide Azure, configuration d’entrée de décryptage de la lame de paramètres et cliquez sur OK. Sélectionnez l’abonnement, groupe de ressources, ressources emplacement, les termes juridiques et groupe accord et cliquez sur le bouton Créer pour activer le cryptage sur une VM IaaS nouvelle.

Pour Linux, VM, [ce](https://aka.ms/decrypt-linuxvm) modèle peut être utilisé pour désactiver le cryptage.

Détails paramètres du modèle Gestionnaire de ressources pour la désactivation du cryptage sur IaaS VM en cours d’exécution :

| vmName         | Nom de la machine virtuelle sur quel cryptage opération doit être effectuée                                                                                                                                                                       |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| volumeType     | Type du volume sur lequel le décryptage opération est exécutée. Les valeurs valides sont « OS », « Data », « All ». **Remarque :** Vous ne pouvez pas désactiver le cryptage sur le volume du système d’exploitation Windows IaaS VM/de démarrage en cours d’exécution sans désactiver le chiffrement de volume « Données ». **Remarque**: la désactivation du chiffrement sur disque du système d’exploitation n’est pas autorisée sur les ordinateurs virtuels de Linux. |
| sequenceVersion | Version de la séquence de l’opération de BitLocker. Incrémenter ce numéro de version chaque fois qu’une opération de décryptage de disque est exécutée sur l’ordinateur virtuel même                                                                                          |

##### <a name="disable-encryption-on-existingrunning-iaas-vm-in-azure-using-ps-cmdlet"></a>Désactivez le cryptage sur existant ou l’exécution IaaS VM dans Azure à l’aide d’applet de commande PS

Pour désactiver à l’aide de l’applet de commande PS, applet de commande [Disable-AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx) désactive le cryptage sur une infrastructure en tant que service (IaaS) virtual machine. Cette applet de commande prend en charge à la fois Windows et les ordinateurs virtuels de Linux. Cette applet de commande installe une extension sur l’ordinateur virtuel afin de désactiver le cryptage. Si le paramètre Name n’est pas spécifié, une extension avec le nom par défaut « AzureDiskEncryption pour Windows VMs » est créée. 

Sur les ordinateurs virtuels Linux, l’extension « AzureDiskEncryptionForLinux » est utilisée.

**Remarque**: cette applet de commande redémarre l’ordinateur virtuel. 

## <a name="appendix"></a>Appendice

### <a name="connect-to-your-subscription"></a>Se connecter à votre abonnement

Veillez à consulter la section *conditions préalables* de ce document avant de continuer. Après vous être assuré que tous les composants requis ont été remplies, suivez les étapes ci-dessous pour vous connecter à votre abonnement :

1 démarrez une session PowerShell d’Azure et vous connecter à votre compte Azure avec la commande suivante :

    Login-AzureRmAccount

2 si vous disposez de plusieurs abonnements et que vous souhaitez spécifier un spécifique à utiliser, tapez ce qui suit pour afficher les abonnements de votre compte :

    Get-AzureRmSubscription

3 pour l’abonnement que vous souhaitez utiliser, tapez :

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4.les pour vérifier l’abonnement configuré est correct, tapez :

    Get-AzureRmSubscription

5 pour la boîte de dialogue Confirmer que les applets de commande de cryptage de disque Azure sont installés, tapez :

    Get-command *diskencryption*

6 vous devez voir le dessous de confirmation de l’installation de PowerShell de cryptage de disque Azure de sortie :

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     

### <a name="preparing-a-pre-encrypted-windows-vhd"></a>Préparation d’un disque dur virtuel de Windows déjà chiffré
Les sections qui suivent sont nécessaires pour préparer un disque dur virtuel de Windows déjà chiffré de déploiement comme un disque dur virtuel crypté dans Azure IaaS. Les étapes permettent de préparer et de démarrer un ordinateur virtuel (vhd) sur Hyper-V ou Azure de windows fraîches.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Mise à jour de la stratégie de groupe pour permettre non-module de plateforme sécurisée pour la protection du système d’exploitation
Vous devez configurer le paramètre de stratégie de groupe BitLocker appelé chiffrement de lecteur BitLocker, sous stratégie ordinateur Local \Computer administration\Composants Windows. Modifier ce paramètre pour : *Lecteurs de système d’exploitation - exiger une authentification supplémentaire au démarrage - Autoriser BitLocker sans un module de plateforme sécurisée compatible* comme illustré dans la figure ci-dessous :

![Logiciel anti-programme malveillant de Microsoft dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Installer la fonctionnalité BitLocker
Pour Windows Server 2012 et ci-dessus utilisent le dessous de commande :

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Pour l’utilisation de Windows Server 2008 R2 du dessous de commande :

    ServerManagerCmd -install BitLockers

#### <a name="prepare-os-volume-for-bitlocker-using-bdehdcfg"></a>Préparer le volume du système d’exploitation pour l’utilisation de BitLocker`bdehdcfg`

Exécutez la commande ci-dessous pour compresser la partition du système d’exploitation et de préparer l’ordinateur pour BitLocker.

    bdehdcfg -target c: shrink -quiet

#### <a name="using-bitlocker-to-protect-the-os-volume"></a>L’utilisation de BitLocker pour protéger le volume du système d’exploitation
Utilisez le [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) commande pour activer le cryptage sur le volume de démarrage à l’aide d’un protecteur de clé externe et placer la clé externe (fichier .bek) sur le disque externe ou un volume. Cryptage sera activé sur le volume système/de démarrage après le prochain redémarrage.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Remarque :** La machine virtuelle doit être préparé avec un disque dur virtuel de données/de ressource séparé pour l’obtention de la clé externe à l’aide de BitLocker.

#### <a name="encrypting-os-drive-on-a-running-linux-vm"></a>Le chiffrement de lecteur du système d’exploitation sur une VM Linux en cours d’exécution

Cryptage de lecteur du système d’exploitation sur une VM Linux en cours d’exécution est pris en charge sur les distros suivants :

- RHEL 7.2
- CentOS 7.2
- Ubuntu 16.04

Conditions requises pour le cryptage de disque du système d’exploitation :

- Machine virtuelle doit être créée à partir de l’image de la galerie d’Azure dans le Gestionnaire de ressources Azure portal.
- Azure VM au moins 4 Go de RAM (recommandé la taille est de 7 Go).
- (Pour RHEL et CentOS) SELinux doit être [désactivé](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) sur l’ordinateur virtuel. La machine virtuelle doit être redémarrée au moins une fois après la désactivation de SELinux.


##### <a name="steps"></a>Étapes

1. Créez un ordinateur virtuel à l’aide de le distros spécifié ci-dessus.

CentOS 7.2, cryptage de disque du système d’exploitation est pris en charge via une image particulière. Pour utiliser cette image, spécifiez « 7.2n » comme le point de stock lors de la création de la machine virtuelle :

    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"

2 configurer la machine virtuelle en fonction de vos besoins. Si vous envisagez de chiffrer tous les lecteurs doivent être / etc/fstab spécifié et montable à partir de données des disques (système d’exploitation et données).

> [AZURE.NOTE] Vous devez utiliser les UUID =... pour spécifier les données des lecteurs dans/etc/fstab au lieu de spécifier le nom du périphérique bloc, par exemple, /dev/sdb1. Au cours du chiffrement change l’ordre des disques sur l’ordinateur virtuel. Si votre ordinateur virtuel s’appuie sur un ordre spécifique de périphériques de bloc, il échouera à les monter après le chiffrement.

3 sessions de déconnexion de SSH.

4 pour chiffrer le système d’exploitation, spécifiez volumeType comme « tous » ou « OS » lors de [l’activation du cryptage](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

> [AZURE.NOTE] Tous les processus utilisateur-espace qui ne s’exécutent pas en tant que `systemd` services sont mis à mort avec un `SIGKILL`. La machine virtuelle doit être redémarrée. Veuillez planifier de temps d’arrêt de la machine virtuelle lors de l’activation de l’OS de disque le cryptage sur une machine virtuelle en cours d’exécution.

5.les périodiquement surveiller la progression de cryptage à l’aide des instructions dans la [section suivante](#monitoring-os-encryption-progress).

6 une fois Get-AzureRmVmDiskEncryptionStatus « VMRestartPending » s’affiche, redémarrez votre machine virtuelle par l’enregistrement d’une session ou via le portail/PowerShell/CLI.

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, please reboot the VM

Il est recommandé d’enregistrer les [diagnostics de démarrage](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/) de la machine virtuelle *avant* le redémarrage.

#### <a name="monitoring-os-encryption-progress"></a>Surveillance de la progression du cryptage du système d’exploitation

Il existe trois façons de surveiller la progression du cryptage du système d’exploitation.

1 Utilisez l’applet de commande Get-AzureRmVmDiskEncryptionStatus et examinez le champ ProgressMessage : 
 
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started

Une fois que la machine virtuelle atteint « Du système d’exploitation disque cryptage démarré » il prendra environ 40 à 50 minutes sur une prime de stockage sauvegardés VM.

En raison du [problème n° 388](https://github.com/Azure/WALinuxAgent/issues/388) dans WALinuxAgent, `OsVolumeEncrypted` et `DataVolumesEncrypted` s’affichent sous `Unknown` dans des distros. WALinuxAgent version 2.1.5 et au-dessus de ce problème est réglé automatiquement. Dans le cas où vous voyez `Unknown` dans la sortie, vous pouvez vérifier l’état de chiffrement de disque à l’aide d’afficheur de ressources Azure.

Accédez à la [Visionneuse de ressources Azure](https://resources.azure.com/), puis développez cette hiérarchie dans le panneau de sélection de gauche :

~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

Dans la InstanceView, faites défiler pour voir l’état de chiffrement de vos lecteurs.

![Vue d’Instance de machine virtuelle](./media/azure-security-disk-encryption/vm-instanceview.png)

2 Examinez [les diagnostics de démarrage](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/). Les messages à partir de l’extension de ADE sont précédés de `[AzureDiskEncryption]`.

3 ouverture de session sur la machine virtuelle via SSH et l’obtention du journal de l’extension à partir de

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

Il n’est pas recommandé d’ouvrir une session sur l’ordinateur virtuel alors que le cryptage de système d’exploitation est en cours. Par conséquent, les journaux doivent être copiés uniquement lorsque les deux autres méthodes ont échoué.

#### <a name="preparing-a-pre-encrypted-linux-vhd"></a>Préparation d’un VHD Linux préalablement crypté

##### <a name="ubuntu-16"></a>Ubuntu 16

###### <a name="configure-encryption-during-distro-install"></a>Configurer le cryptage lors de l’installation de distro

1 Sélectionnez « Configurer les volumes cryptés » lorsque le partitionnement de disques.

![Installation d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2 créer un lecteur de démarrage séparé qui ne doit pas être crypté. Chiffrer le lecteur racine.

![Installation d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3.les fournissent un mot de passe. Il s’agit du mot de passe que vous allez télécharger dans KeyVault.

![Installation d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4.les terminer le partitionnement.

![Installation d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5.les lors du démarrage de la machine virtuelle, le système vous demandera d’un mot de passe. Utilisez le mot de passe que vous avez fourni à l’étape 3.

![Installation d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6 préparer la machine virtuelle pour le téléchargement dans Azure à l’aide de [ces instructions](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). N’exécutez pas la dernière étape (mise hors service de la machine virtuelle) encore.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurer le cryptage pour travailler avec Azure

1. créer un fichier sous /usr/local/sbin/azure_crypt_key.sh, avec le contenu dans le script ci-dessous. Attention à la KeyFileName, car il est le nom de fichier de mot de passe par Azure.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do
        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2.les modifier la configuration du cryptage dans */etc/crypttab*. Il doit ressembler à ceci :

    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3 Si vous modifiez *azure_crypt_key.sh* dans Windows et copiée sur Linux, n’oubliez pas d’exécuter *dos2unix /usr/local/sbin/azure_crypt_key.sh*.

4 Ajoutez des autorisations exécutables au script :

    chmod +x /usr/local/sbin/azure_crypt_key.sh

4.les modifier */etc/initramfs-tools/modules* en ajoutant des lignes :

    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1

5 `update-initramfs -u -k all` mise à jour de l’initramfs pour rendre les `keyscript` prennent effet.
6.les maintenant, vous pouvez deprovision la machine virtuelle.

![Installation d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

7.les passez à l’étape suivante et de [télécharger votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2

###### <a name="configure-encryption-during-distro-install"></a>Configurer le cryptage lors de l’installation de distro

1 Sélectionnez « Crypter du groupe de volumes » lors du partitionnement des disques. Fournir un mot de passe. Il s’agit du mot de passe que vous allez télécharger dans KeyVault.

![le programme d’installation d’openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2 Amorcez la machine virtuelle à l’aide de votre mot de passe.

![le programme d’installation d’openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3 Préparation de machine virtuelle pour le téléchargement dans Azure à l’aide de [ces instructions](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). N’exécutez pas la dernière étape (mise hors service de la machine virtuelle) encore.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurer le cryptage pour travailler avec Azure

1 modifier les /etc/dracut.conf et ajoutez la ligne suivante :

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2.les commenter ces lignes à la fin du fichier « / usr/lib/dracut/modules.d/90crypt/module-setup.sh » :

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3.les ajouter la ligne suivante au début du fichier « / usr/lib/dracut/modules.d/90crypt/parse-crypt.sh »

    DRACUT_SYSTEMD=0

et remplacez toutes les occurrences de

    if [ -z "$DRACUT_SYSTEMD" ]; then

À

    if [ 1 ]; then

4.les modifier /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh et de l’ajouter après le « périphérique d’ouvrir LUKS # »

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5 le « / usr/sbin/dracut - f - v « mettre à jour l’initrd.

6.les maintenant vous pouvez deprovision la machine virtuelle et [télécharger votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

##### <a name="centos-7"></a>CentOS 7

###### <a name="configure-encryption-during-distro-install"></a>Configurer le cryptage lors de l’installation de distro

1 Sélectionnez « Crypter mes données » lors du partitionnement des disques.

![Programme d’installation de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2 Assurez-vous que « Crypter » est activée pour la partition racine.

![Programme d’installation de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3.les fournissent un mot de passe. Il s’agit du mot de passe que vous allez télécharger dans KeyVault.

![Programme d’installation de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4.les lors du démarrage de la machine virtuelle, le système vous demandera d’un mot de passe. Utilisez le mot de passe que vous avez fourni à l’étape 3.

![Programme d’installation de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5 préparer la machine virtuelle pour le téléchargement dans Azure à l’aide de [ces instructions](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). N’exécutez pas la dernière étape (mise hors service de la machine virtuelle) encore.

6.les maintenant vous pouvez deprovision la machine virtuelle et [télécharger votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurer le cryptage pour travailler avec Azure

1 modifier les /etc/dracut.conf et ajoutez la ligne suivante :

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2.les commenter ces lignes à la fin du fichier « / usr/lib/dracut/modules.d/90crypt/module-setup.sh » :

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3.les ajouter la ligne suivante au début du fichier « / usr/lib/dracut/modules.d/90crypt/parse-crypt.sh »

    DRACUT_SYSTEMD=0

et remplacez toutes les occurrences de

    if [ -z "$DRACUT_SYSTEMD" ]; then

À

    if [ 1 ]; then

4.les modifier /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh et de l’ajouter après le « périphérique d’ouvrir LUKS # »

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5 le « / usr/sbin/dracut - f - v « mettre à jour l’initrd.

![Programme d’installation de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Télécharger le VHD crypté vers un compte de stockage Azure
Une fois pr DM-Crypt de cryptage BitLocker est activé, le disque dur virtuel crypté local doit être téléchargé à votre compte de stockage.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-disk-encryption-secret-for-the-pre-encrypted-vm-to-key-vault"></a>Télécharger le secret de cryptage de disque de VM préalablement cryptée à clé de coffre-fort
Le mot de passe de cryptage de disque obtenu précédemment doit être téléchargé sous la forme d’un secret dans la chambre forte de clé. Le coffre-fort de la clé doit avoir les autorisations sont activées pour votre client DAS, ainsi que le cryptage du disque dur.


    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $KeyVault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Secret de cryptage de disque ne pas chiffré avec une KEK
[Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) permet de mettre en service le secret dans la chambre forte de clé. Dans le cas d’une machine virtuelle Windows, le fichier bek est codé sous la forme d’une chaîne en base 64 et ensuite téléchargé sur le coffre-fort de clé à l’aide de l’applet de commande Set-AzureKeyVaultSecret. Pour Linux, le mot de passe est codé sous la forme d’une chaîne en base 64 et puis téléchargé vers la chambre forte de clé. En outre, assurez-vous que les balises suivantes sont définies lors de la création du secret dans la chambre forte de clé.

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Le `$secretUrl` doit servir à l’étape suivante pour [attacher le disque du système d’exploitation sans l’aide de KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Secret de cryptage de disque chiffré avec un KEK

Le secret peut éventuellement être crypté avec une clé de cryptage avant de le télécharger vers la chambre forte de clé. [L’API](https://msdn.microsoft.com/library/azure/dn878066.aspx) retour à la ligne permet d’abord chiffrer le secret à l’aide de la clé de cryptage. Le résultat de cette opération de retour à la ligne est une chaîne d’URL codée en base64 qui est ensuite téléchargée sous la forme d’un secret à l’aide de l’applet de commande [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) .

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

La `$KeyEncryptionKey` et `$secretUrl` doit être utilisé à l’étape suivante pour [attacher le disque du système d’exploitation à l’aide de KEK](#using-a-kek).

### <a name="specify-secret-url-when-attaching-os-disk"></a>Spécifiez les URL secret lors de l’attachement du disque du système d’exploitation

#### <a name="without-using-a-kek"></a>Sans l’aide d’un KEK

Lors de l’attache du disque du système d’exploitation, `$secretUrl` doit être transmis. L’URL a été générée dans la section [« secret de cryptage de disque pas chiffré avec un KEK »](#disk-encryption-secret-not-encrypted-with-a-kek).

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>À l’aide d’un KEK

Lors de l’attache du disque du système d’exploitation, `$KeyEncryptionKey` et `$secretUrl` doivent être transmis. L’URL a été générée dans la section [« secret de cryptage de disque chiffré avec un KEK »](#disk-encryption-secret-encrypted-with-a-kek).

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>Téléchargez ce Guide
Vous pouvez télécharger ce guide à partir de la [Galerie TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).


## <a name="for-more-information"></a>Pour plus d’informations
[Explorez le cryptage de disque Azure avec PowerShell Azure](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Explorez le cryptage de disque Azure avec PowerShell Azure - partie 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
