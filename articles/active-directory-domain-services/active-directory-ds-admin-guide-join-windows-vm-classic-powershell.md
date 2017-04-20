<properties
    pageTitle="Services de domaine ActiveDirectory Azure : Guide d’Administration | Microsoft Azure"
    description="Participez à une machine virtuelle Windows d’un domaine géré à l’aide de PowerShell d’Azure et le modèle de déploiement classique."
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="maheshu"/>


# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Joindre un ordinateur virtuel de Windows Server pour un domaine géré à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Azure portal classique - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : [le Gestionnaire de ressources et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement classique. Azure Services de domaine Active Directory ne prend pas en charge le modèle de gestionnaire de ressources.

Ces étapes vous indiquent comment personnaliser un ensemble de commandes PowerShell d’Azure que de créer et de préconfigurer un ordinateur basé sur Windows Azure virtuel à l’aide d’une approche modulaire. Ces étapes vous permettent de créer un ordinateur basé sur Windows Azure virtuel et le joindre à un domaine géré de Services de domaine Active Directory de Azure.

Ces étapes suivent une approche de remplissage-in-the-vides pour la création de jeux de commandes PowerShell d’Azure. Cette approche peut être utile si vous êtes nouveau dans PowerShell ou que vous souhaitez savoir quelles valeurs spécifier pour une configuration réussie. Les utilisateurs expérimentés de PowerShell peuvent prendre les commandes et leurs propres valeurs pour les variables de remplacement (lignes commençant par « $»).

Si vous ne l’avez pas déjà fait, suivez les instructions dans [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) pour installer PowerShell d’Azure sur votre ordinateur local. Ensuite, ouvrez une invite de commande Windows PowerShell.

## <a name="step-1-add-your-account"></a>Étape 1 : Ajouter votre compte

1. À l’invite de PowerShell, tapez **Add-AzureAccount** et appuyez sur **entrée**.
2. Saisissez l’adresse e-mail associée à votre abonnement Azure et cliquez sur **Continuer**.
3. Tapez le mot de passe pour votre compte.
4. Cliquez sur **connexion**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Étape 2 : Définir votre abonnement et votre compte de stockage

Définissez votre abonnement Azure et un compte de stockage en exécutant ces commandes à l’invite de commande Windows PowerShell. Remplacez tout entre guillemets, y compris les caractères, avec les noms corrects < et >.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Vous pouvez obtenir le nom d’abonnement correct à partir de la propriété SubscriptionName de la sortie de la commande **Get-AzureSubscription** . Vous pouvez obtenir le nom de compte de stockage correct à partir de la propriété de l’étiquette de la sortie de la commande **Get-AzureStorageAccount** après l’exécution de la commande **Select-AzureSubscription** .


## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Étape 3 : Procédure pas à pas - configurer l’ordinateur virtuel et joignez-le au domaine géré
Voici la commande PowerShell d’Azure correspondante définie pour créer cet ordinateur virtuel, avec des lignes blanches entre chaque bloc pour une meilleure lisibilité.

Spécifiez les informations relatives à la machine virtuelle Windows pour être mis en service.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Pour les valeurs de InstanceSize pour le D-, DS ou machines virtuelles de la série G, reportez-vous à la section [ordinateur virtuel et des tailles de Service Cloud pour Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Fournissent des informations sur le domaine géré.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Spécifiez le nom du service nuage.

    $svcname="Contoso100-test"

Spécifiez le nom du réseau virtuel dans lequel la machine virtuelle doit être jointes. Assurez-vous que le domaine géré DAS-DS est disponible dans ce réseau virtuel.

    $vnetname="MyPreviewVnet"

Sélectionnez l’image de machine virtuelle permettant de mettre en service la machine virtuelle.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Configurer la machine virtuelle - machine virtuelle nom, taille d’instance & image destinée à être utilisée.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Obtenir des informations d’identification de l’administrateur local de la machine virtuelle. Choisissez un mot de passe administrateur local fort.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Obtenir des informations d’identification pour un compte d’utilisateur appartenant au groupe « Administrateurs du contrôleur de domaine DAS » pour joindre des VM pour le domaine géré. Ne spécifiez pas le nom de domaine - par exemple, dans notre exemple, nous spécifions 'bob' comme nom d’utilisateur.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Configurer l’ordinateur virtuel, spécifiez la condition de jointure de domaine et les informations d’identification requises.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Définissez un sous-réseau pour l’ordinateur virtuel.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Facultatif : Pointez sur l’adresse IP du domaine. Si vous définissez les adresses IP du domaine des Services de domaine Active Directory de Azure géré comme les serveurs DNS pour le réseau virtuel, cette étape n’est pas requise.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Maintenant, configurez la machine virtuelle de Windows à un domaine.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Script à mettre en service un ordinateur virtuel de Windows et automatiquement la joindre à un domaine géré DAS domaine Services
Ce jeu de commandes PowerShell crée un ordinateur virtuel d’un serveur de métier qui :

- Utilise l’image de Windows Server 2012 R2 Datacenter.
- Est une machine virtuelle très petite.
- A le nom contoso-test.
- Est automatiquement joint domaine pour le domaine géré contoso100.
- Est ajouté au même réseau virtuel que le domaine géré.

Voici l’exemple complet de script pour créer la machine virtuelle Windows et la joindre au domaine des Services de domaine Active Directory de Azure géré automatiquement.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Contenu associé
- [Services de domaine Active Directory Azure - guide de mise en route](./active-directory-ds-getting-started.md)

- [Administrer un domaine géré de Services de domaine Active Directory de Azure](./active-directory-ds-admin-guide-administer-domain.md)
