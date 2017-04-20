<properties
    pageTitle="Protection des serveurs pour Azure à l’aide de PowerShell d’Azure avec le Gestionnaire de ressources Azure | Microsoft Azure"
    description="Automatiser la protection des serveurs pour Azure avec récupération de Site Azure à l’aide de PowerShell et le Gestionnaire de ressources Azure."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>Réplication entre les machines virtuelles de Hyper-V sur site et Azure à l’aide de PowerShell et le Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-deploy-with-powershell-resource-manager.md)
- [Portail classique](site-recovery-hyper-v-site-to-azure-classic.md)



## <a name="overview"></a>Vue d’ensemble

Récupération de Site Azure contribue à votre stratégie de récupération après incident et de continuité d’activité entreprise par l’orchestration de réplication, le basculement et la restauration des ordinateurs virtuels dans un certain nombre de scénarios de déploiement. Pour obtenir une liste complète des scénarios de déploiement, consultez la [vue d’ensemble de la récupération de Site Azure](site-recovery-overview.md).

PowerShell Azure est un module qui fournit des applets de commande pour gérer les Azure via Windows PowerShell. Il peut fonctionner avec deux types de modules : le module profil d’Azure, ou le module de gestionnaire de ressources Azure.

Site récupération applets de commande PowerShell, disponibles avec Azure PowerShell pour le Gestionnaire de ressources d’Azure, vous aident à protéger et à récupérer vos serveurs dans Azure.

Cet article décrit comment utiliser Windows PowerShell, ainsi que le Gestionnaire de ressources d’Azure, déploiement de récupération de Site afin de configurer et d’orchestrer la protection des serveurs pour Azure. L’exemple utilisé dans cet article comment vous protéger, basculer et restaurer des machines virtuelles sur un hôte Hyper-V sur Azure, à l’aide de PowerShell d’Azure avec le Gestionnaire de ressources Azure.

> [AZURE.NOTE] Les applets de commande PowerShell de récupération de Site actuellement permettent de configurer les éléments suivants : un site de Virtual Machine Manager vers un autre, un site de Virtual Machine Manager pour Azure et un site Hyper-V vers Azure.

Vous n’avez pas besoin d’être un expert de PowerShell pour cet article, mais vous n’avez pas besoin de comprendre les concepts de base, tels que les modules, les applets de commande et les sessions. Pour plus d’informations sur Windows PowerShell, reportez-vous à la section [Mise en route avec Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Vous pouvez également en savoir plus sur [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).

> [AZURE.NOTE] Les partenaires Microsoft qui sont inclus dans le programme fournisseur de solutions de Cloud (CSP) peuvent configurer et gérer la protection des serveurs de leurs clients à respectifs CSP abonnements (clients) leurs clients.

## <a name="before-you-start"></a>Avant de commencer

Assurez-vous que vous avez ces conditions préalables en place :

- Un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). En outre, vous pouvez lire sur [la tarification Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell 1.0. Pour plus d’informations sur cette version et comment l’installer, consultez [Azure PowerShell 1.0.](https://azure.microsoft.com/)
- Les modules [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) et [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) . Vous pouvez obtenir les dernières versions de ces modules à partir de la [Galerie de PowerShell](https://www.powershellgallery.com/)

Cet article illustre l’utilisation d’avec le Gestionnaire de ressources Azure Azure Powershell pour configurer et gérer la protection de vos serveurs. L’exemple utilisé dans cet article vous indique comment protéger un ordinateur virtuel en cours d’exécution sur un hôte Hyper-V, Azure. Les conditions préalables suivantes sont spécifiques à cet exemple. Pour obtenir un ensemble plus complet de la configuration requise pour les différents scénarios de restauration de Site, reportez-vous à la documentation se rapportant à ce scénario.

- Un hôte Hyper-V de Windows Server 2012 R2 ou Microsoft Hyper-V Server 2012 R2 contenant un ou plusieurs ordinateurs virtuels en cours d’exécution.
- Serveurs Hyper-V est connecté à Internet, soit directement, soit via un serveur proxy.
- Les ordinateurs virtuels que vous souhaitez protéger doivent être conformes avec les [conditions préalables de Machine virtuelle](site-recovery-best-practices.md#virtual-machines).


## <a name="step-1-sign-in-to-your-azure-account"></a>Étape 1 : Se connecter à votre compte Azure


1. Ouvrez une console PowerShell et exécutez la commande suivante pour vous connecter à votre compte Azure. L’applet de commande ouvre une page web qui vous invite à indiquer vos informations d’identification du compte.

        Login-AzureRmAccount

    Alternativement, vous pouvez également inclure vos informations d’identification de compte en tant que paramètre à la `Login-AzureRmAccount` applet de commande, à l’aide de la `-Credential` paramètre.

    Si vous êtes le partenaire fournisseur de services cryptographiques pour le compte d’un client, spécifiez le client comme un client, à l’aide de leur nom de domaine principal tenantID ou le locataire.

        Login-AzureRmAccount -Tenant "fabrikam.com"

2. Un compte peut disposer de plusieurs abonnements, donc vous devez associer l’abonnement que vous souhaitez utiliser avec le compte.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Vérifiez que votre abonnement est inscrit pour utiliser les fournisseurs Azure pour les Services de récupération et de restauration de Site, en utilisant les commandes suivantes :

    - `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
    -  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

    Dans la sortie de ces commandes, si l' **RegistrationState** est **enregistré**, vous pouvez procéder à l’étape 2. Si ce n’est pas le cas, vous devez inscrire le fournisseur manquant dans votre abonnement.

    Pour enregistrer le fournisseur Azure pour les Services de récupération et de la restauration de Site, exécutez les commandes suivantes :

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

    Vérifiez que les fournisseurs enregistrement avec succès à l’aide des commandes suivantes : `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` et `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.



## <a name="step-2-set-up-the-recovery-services-vault"></a>Étape 2 : Configurer le coffre-fort de Services de récupération

1. Créer un groupe de ressources Azure le Gestionnaire de ressources dans lequel vous allez créer le coffre-fort, ou utiliser un groupe de ressources existant. Vous pouvez créer un nouveau groupe de ressources à l’aide de la commande suivante :

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    où la variable $ResourceGroupName contient le nom du groupe de ressources que vous souhaitez créer, et la variable $Geo contient la région Azure dans lequel créer le groupe de ressources (par exemple, « sud du Brésil »).

    Vous pouvez obtenir une liste des groupes de ressources dans votre abonnement à l’aide de la `Get-AzureRmResourceGroup` applet de commande.

2. Créez un nouveau coffre-fort de Services de récupération Azure comme suit :

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Vous pouvez récupérer une liste des coffres-forts existants à l’aide de la `Get-AzureRmRecoveryServicesVault` applet de commande.

> [AZURE.NOTE] Si vous souhaitez effectuer des opérations sur les coffres-forts de récupération de Site créés à l’aide du portail classique ou le module PowerShell de gestion de Service Azure, vous pouvez récupérer une liste de ces coffres-forts à l’aide de la `Get-AzureRmSiteRecoveryVault` applet de commande. Vous devez créer un nouveau coffre-fort de Services de récupération de toutes les opérations de nouveau. Les coffres-forts de récupération de Site que vous avez créé précédemment sont pris en charge, mais n’ont pas les dernières fonctionnalités.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Étape 3 : Définir le contexte de stockage en chambre forte de Services de récupération

1.  Définir le contexte de la chambre forte en exécutant la commande suivante :

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>Étape 4 : Créer un site de Hyper-V et générer une nouvelle clé d’enregistrement de coffre-fort pour le site.

1. Créez un nouveau site de Hyper-V comme suit :

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Cette applet de commande démarre une tâche de récupération de Site pour créer le site et renvoie un objet de travail de récupération de Site. Attendez que la tâche se termine et vérifiez que la tâche est terminée avec succès.

    Vous pouvez récupérer l’objet de traitement et ainsi de vérifier l’état actuel de la tâche, à l’aide de l’applet de commande Get-AzureRmSiteRecoveryJob.
2. Générer et télécharger une clé d’enregistrement pour le site, comme suit :

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Copiez la clé téléchargée à l’hôte Hyper-V. Vous avez besoin de la clé pour inscrire l’hôte Hyper-V sur le site.

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>Étape 5 : Installer le fournisseur de récupération de Site Azure et Agent de récupération de Services Azure sur votre hôte Hyper-V

1. Téléchargez le programme d’installation de la dernière version du fournisseur de [Microsoft](https://aka.ms/downloaddra).

2. Exécuter le programme d’installation sur votre hôte Hyper-V et à la fin de l’installation de se poursuivre à l’étape d’enregistrement.

3. Lorsque vous y êtes invité, fournissez la clé d’enregistrement de site téléchargés et terminer l’inscription de l’hôte Hyper-V sur le site.

4. Vérifiez que l’hôte Hyper-V est enregistré sur le site à l’aide de la commande suivante :

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>Étape 6 : Créer une stratégie de réplication et de l’associer avec le conteneur de protection

1. Créez une stratégie de réplication comme suit :

        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Vérifier le travail retourné pour vous assurer que la création de stratégies de réplication réussit.

    >[AZURE.IMPORTANT] Le compte de stockage spécifié doit être dans la même région Azure comme votre coffre-fort de Services de restauration et doit avoir des géo-réplication est activée.
    >
    > - Si le compte de stockage de récupération spécifié est de type Azure Storage (standard), le basculement des ordinateurs protégés récupérer la machine pour Azure IaaS (classique).
    > - Si le compte de stockage de récupération spécifié est de type Azure Storage (responsable de ressources Azure), le basculement des ordinateurs protégés récupérer la machine pour Azure IaaS (responsable de ressources Azure).

2. Permet d’obtenir le conteneur de protection correspondant au site, comme suit :

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.  Démarrez l’association du conteneur de protection avec la stratégie de réplication, comme suit :

        $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

    Attendez la fin du travail d’association et vous assurer qu’il s’est terminée correctement.

##<a name="step-7-enable-protection-for-virtual-machines"></a>Étape 7 : Activez la protection des machines virtuelles

1. Obtenez l’entité de protection correspondant à la machine virtuelle que vous souhaitez protéger, comme suit :

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Démarrer la protection de l’ordinateur virtuel, comme suit :

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

    >[AZURE.IMPORTANT] Le compte de stockage spécifié doit être dans la même région Azure comme votre coffre-fort de Services de restauration et doit avoir des géo-réplication est activée.
    >
    > - Si le compte de stockage de récupération spécifié est de type Azure Storage (standard), le basculement des ordinateurs protégés récupérer la machine pour Azure IaaS (classique).
    > - Si le compte de stockage de récupération spécifié est de type Azure Storage (responsable de ressources Azure), le basculement des ordinateurs protégés récupérer la machine pour Azure IaaS (responsable de ressources Azure).

    > Si la machine virtuelle que vous protégez possède plus d’un disque, spécifiez le disque du système d’exploitation en utilisant le paramètre *OSDiskName* .

3. Attendez que les ordinateurs virtuels atteindre un état de protection après la réplication initiale. Cela peut prendre un certain temps, en fonction de facteurs tels que la quantité de données à répliquer et de la bande passante disponible en amont vers Azure. L’état de la tâche et ÉtatDescription sont mis à jour comme suit, sur la machine virtuelle atteint un état de protection.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed

4. Mettre à jour les propriétés de restauration, tels que la taille du rôle VM et le réseau Azure pour joindre des cartes d’interface réseau de la machine virtuelle pour en cas de basculement.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob


        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Étape 8 : Exécuter un test de basculement

1. Exécuter un travail de basculement test, comme suit :

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Vérifiez que le test de la machine virtuelle est créée dans Azure. (Le travail de basculement test est suspendu, après avoir créé le test de machine virtuelle dans Azure. La tâche se termine en nettoyant les artefacts créés lors de la reprise de la tâche, comme illustré dans l’étape suivante.)

3. Effectuer le basculement de test, comme suit :

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob


##<a name="next-steps"></a>Étapes suivantes

[Pour en savoir plus](https://msdn.microsoft.com/library/azure/mt637930.aspx) sur la récupération de Site Azure avec les applets de commande PowerShell de gestionnaire de ressources Azure.
