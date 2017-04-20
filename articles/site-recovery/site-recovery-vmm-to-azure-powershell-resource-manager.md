<properties
    pageTitle="Répliquer les machines virtuelles de Hyper-V dans des nuages VMM à l’aide de PowerShell (responsable de ressources) et la récupération de Site d’Azure | Microsoft Azure"
    description="Répliquer les machines virtuelles de Hyper-V dans des nuages VMM à l’aide de la récupération de Site Azure et PowerShell"
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="rajanaki"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Répliquer les machines virtuelles de Hyper-V dans les nuages VMM à Azure à l’aide de PowerShell et le Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-vmm-to-azure.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portail classique](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - classique](site-recovery-deploy-with-powershell.md)



## <a name="overview"></a>Vue d’ensemble

Récupération de Site Azure contribue à votre stratégie d’entreprise après incident et de continuité d’activité (BCDR) de récupération par l’orchestration de réplication, le basculement et la restauration des ordinateurs virtuels dans un certain nombre de scénarios de déploiement. Pour une liste complète des scénarios de déploiement, consultez la [vue d’ensemble de la récupération de Site Azure](site-recovery-overview.md).

Cet article vous indique comment utiliser PowerShell pour automatiser les tâches courantes à effectuer lorsque vous configurez la récupération de Site Azure pour répliquer des ordinateurs virtuels de Hyper-V dans des nuages de System Center VMM pour stockage Azure.

L’article inclut les conditions préalables pour le scénario et vous montre

- Comment faire pour configurer un coffre-fort de Services de récupération
- Installez le fournisseur de récupération de Site Azure sur le serveur VMM de source
- Inscrire le serveur dans le coffre-fort, ajoutez un compte de stockage Azure
- Installer l’agent de Services de récupération Azure sur les serveurs hôtes Hyper-V
- Configurer les paramètres de protection pour les nuages VMM, qui seront appliquées à tous les ordinateurs virtuels protégés
- Activer la protection pour ces ordinateurs virtuels.
- Tester le basculement pour vous assurer que tout fonctionne comme prévu.

Si vous avez des problèmes de configuration de ce scénario, envoyez vos questions sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : [le Gestionnaire de ressources et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement du Gestionnaire de ressources.

## <a name="before-you-start"></a>Avant de commencer

Assurez-vous que vous avez ces conditions préalables en place :

### <a name="azure-prerequisites"></a>Conditions préalables Azure

- Vous devez disposer d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Si vous n’en avez pas, démarrez avec un [compte gratuit](https://azure.microsoft.com/free). En outre, vous pouvez lire sur la [tarification d’Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Vous aurez besoin un abonnement du fournisseur de services cryptographiques si vous essayez de la réplication à un scénario d’abonnement du fournisseur de services cryptographiques. Pour en savoir plus sur le programme fournisseur de services cryptographiques pour [inscrire dans le programme fournisseur de services cryptographiques](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
- Vous aurez besoin d’un compte de stockage (responsable de ressources) v2 Azure pour stocker les données répliquées vers Azure. Le compte doit géo-réplication est activée. Il doit être dans la même région, comme le service de récupération de Site Azure et être associé à l’abonnement de même ou de l’abonnement fournisseur de services cryptographiques. Pour plus d’informations sur la configuration de stockage Azure, consultez [Introduction au stockage Azure de Microsoft](../storage/storage-introduction.md) pour référence.
- Vous devez vous assurer que les ordinateurs virtuels que vous souhaitez protéger satisfont avec les [conditions requises d’Azure VM](site-recovery-best-practices.md#azure-virtual-machine-requirements).

> [AZURE.NOTE] Actuellement, seules les opérations de niveau machine virtuelle sont possibles via Powershell. Prise en charge pour les opérations de niveau de plan de récupération est bientôt.  Pour l’instant, vous êtes limité à la réalisation des basculements uniquement à un niveau de granularité 'protected VM' et pas à un niveau de Plan de récupération.

### <a name="vmm-prerequisites"></a>Composants requis VMM
- Vous devez le serveur VMM sur System Center 2012 R2.
- Le fournisseur de récupération de Site Azure doit exécuter n’importe quel serveur VMM contenant les machines virtuelles que vous souhaitez protéger. Il est installé au cours du déploiement de la récupération de Site Azure.
- Vous devez au moins un nuage sur le serveur VMM, que vous souhaitez protéger. Le nuage doit contenir :
    - Un ou plusieurs groupes hôtes VMM.
    - Un ou plusieurs serveurs d’hôte Hyper-V ou clusters dans chaque groupe hôte.
    - Un ou plusieurs ordinateurs virtuels sur le serveur source Hyper-V.
- Pour en savoir plus sur la configuration des nuages VMM :
    - En savoir plus sur les nuages VMM privés dans [Nouveautés dans le nuage privé avec System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) et [VMM 2012 et les nuages](http://go.microsoft.com/fwlink/?LinkId=324956).
    - En savoir plus sur la [configuration de la structure du nuage VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
    - Une fois vos éléments de fabric cloud en place savoir comment créer des nuages privés dans la [Création d’un cloud privé dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) et du [procédure pas à pas : création des nuages privés avec System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).


### <a name="hyper-v-prerequisites"></a>Conditions préalables de Hyper-V

- Les serveurs hôtes Hyper-V doivent être en cours d’exécution au moins **Windows Server 2012** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012** et installé les dernières mises à jour.
- Si vous utilisez Hyper-V dans une note de cluster que broker de cluster n’est pas créé automatiquement si vous disposez d’un cluster de base d’adresses IP statique. Vous devez configurer manuellement le service broker de cluster. Pour
- Pour plus d’informations, consultez [comment configurer un courtier de réplica de Hyper-V](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
- N’importe quel serveur d’hôte Hyper-V ou le cluster pour lequel vous souhaitez gérer la protection doit être inclus dans un nuage VMM.

### <a name="network-mapping-prerequisites"></a>Conditions préalables du mappage réseau
Lorsque vous protégez des ordinateurs virtuels dans Azure, les mappages du mappage réseau réseaux de l’ordinateur virtuel sur le serveur VMM de source et ciblez les réseaux Azure pour activer les éléments suivants :

- Tous les ordinateurs qui basculent sur le même réseau peuvent se connecter entre eux, quel que soit le plan de récupération, ils sont en cours.
- Si une passerelle réseau est configuré sur la cible de réseau Azure, machines virtuelles peuvent se connecter à d’autres ordinateurs virtuels de locaux.
- Si vous ne configurez pas mappage réseau, uniquement les ordinateurs virtuels qui basculent dans le même plan de récupération sera en mesure de se connecter à l’autre après le basculement vers Azure.

Si vous souhaitez déployer le mappage réseau, vous devez les éléments suivants :

- Les ordinateurs virtuels que vous souhaitez protéger sur le serveur VMM de source doit être connectés à un réseau de la machine virtuelle. Ce réseau doit être lié à un réseau logique qui est associé avec le nuage.
- Un réseau Azure auquel les machines virtuelles dupliquées peuvent se connecter après le basculement. Vous devez sélectionner ce réseau au moment du basculement. Le réseau doit être dans la même région en tant que votre abonnement Azure récupération de Site.

En savoir plus sur le mappage réseau dans

- [Comment faire pour configurer des réseaux logiques dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Comment faire pour configurer les passerelles et les réseaux de l’ordinateur virtuel dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Comment faire pour configurer et surveiller des réseaux virtuels dans Azure](https://azure.microsoft.com/documentation/services/virtual-network/)


###<a name="powershell-prerequisites"></a>Conditions préalables de PowerShell
Vérifiez que vous disposez de PowerShell d’Azure prêt à l’emploi. Si vous utilisez déjà PowerShell, vous devrez mettre à niveau vers la version 0.8.10 ou ultérieure. Pour plus d’informations sur le paramétrage de PowerShell, consultez le [Guide pour installer et configurer Azure PowerShell](../powershell-install-configure.md). Une fois que vous avez installé et configuré de PowerShell, vous pouvez afficher toutes les applets de commande disponibles pour le service [ici](https://msdn.microsoft.com/library/dn850420.aspx).

Pour en savoir plus sur les astuces qui peuvent aider à qu'utiliser les applets de commande, par exemple comment les valeurs de paramètre, les entrées et les sorties sont généralement traités dans Azure PowerShell, consultez le [Guide de mise en route avec les applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Étape 1 : Définir l’abonnement

1. À partir de powershell Azure, connectez-vous à votre compte Azure : à l’aide des applets de commande suivant

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred


2. Obtenir la liste de vos abonnements. Il répertorie également les subscriptionIDs pour chaque abonnement. Notez le subscriptionID de l’abonnement dans lequel vous souhaitez créer le coffre-fort de services de récupération

        Get-AzureRmSubscription

3. Définir l’abonnement dans lequel le coffre-fort de services de récupération doit être créé en mentionnant l’ID d’abonnement

        Set-AzureRmContext –SubscriptionID <subscriptionId>


## <a name="step-2-create-a-recovery-services-vault"></a>Étape 2 : Créer un coffre-fort de Services de récupération

1. Créer un groupe de ressources dans le Gestionnaire de ressources Azure, si vous n’en avez pas déjà

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Créer un nouveau coffre-fort de Services de récupération et enregistrer l’objet de stockage en chambre forte ASR créé dans une variable (sera utilisé ultérieurement). Vous pouvez également récupérer la création d’un post d’objet de coffre-fort ASR à l’aide de l’applet de commande Get-AzureRMRecoveryServicesVault :-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Étape 3 : Définir le contexte de la chambre forte de Services de récupération

1.  Définir le contexte de la chambre forte en exécutant la commande au-dessous de.

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Étape 4 : Installer le fournisseur de récupération de Site Azure

1.  Sur l’ordinateur VMM, créez un répertoire en exécutant la commande suivante :

        New-Item c:\ASR -type directory

2.  Extraire les fichiers à l’aide du fournisseur de téléchargé en exécutant la commande suivante

        pushd C:\ASR\
        .\AzureSiteRecoveryProvider.exe /x:. /q


3.  Installez le fournisseur à l’aide des commandes suivantes :

        .\SetupDr.exe /i
        $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
        do
        {
                        $isNotInstalled = $true;
                        if(Test-Path $installationRegPath)
                        {
                                        $isNotInstalled = $false;
                        }
        }While($isNotInstalled)

    Attendez que l’installation se termine.

4.  Inscrire le serveur dans le coffre-fort à l’aide de la commande suivante :

        $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
        pushd $BinPath
        $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice


## <a name="step-5-create-an-azure-storage-account"></a>Étape 5 : Créer un compte de stockage Azure

1. Si vous n’avez pas un compte de stockage Azure, créer un compte géo-réplication est activée dans la même geo la chambre forte en exécutant la commande suivante :

        $StorageAccountName = "teststorageacc1" #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"  
        $ResourceGroupName =  “myRG”            #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Notez que le compte de stockage doit être dans la même région, comme le service de récupération de Site d’Azure et si elle doit être associée à l’abonnement même.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Étape 6 : Installation de l’Agent de Services de récupération Azure

1. Téléchargez l’agent des Services de récupération Azure à [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) et l’installer sur chaque serveur hôte Hyper-V dans les nuages VMM à protéger.

2. Exécutez la commande suivante sur tous les hôtes VMM :

    marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Étape 7 : Configuration cloud paramètres de protection

1.  Créer une stratégie de réplication pour Azure en exécutant la commande suivante :


        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.  Obtenir un conteneur de protection en exécutant les commandes suivantes :

        $PrimaryCloud = "testcloud"
        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

3.  Obtenir les détails de la stratégie à une variable à l’aide de la tâche qui a été créée et mentionner le nom convivial de stratégie :

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Démarrer l’association du conteneur de protection avec la stratégie de réplication :

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.  Une fois la tâche terminée, exécutez la commande suivante :

        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
        $isJobLeftForProcessing = $true;
        }

6.  Une fois que la tâche est terminée, exécutez la commande suivante :

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)

Pour vérifier la fin de l’opération, suivez les étapes de la [Surveillance de l’activité](#monitor).

## <a name="step-8-configure-network-mapping"></a>Étape 8 : Configuration de mappage réseau

Avant de commencer la mappage réseau Vérifiez que les ordinateurs virtuels sur le serveur VMM de source sont connectés à un réseau de la machine virtuelle. En outre, créer un ou plusieurs réseaux virtuels Azure.

En savoir plus sur la création d’un réseau virtuel à l’aide d’Azure Resource Manager et PowerShell, de [créer un réseau virtuel avec une connexion VPN de site à site à l’aide du Gestionnaire de ressources Azure et PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Notez que plusieurs réseaux de l’ordinateur virtuel peuvent être mappés à un seul réseau Azure. Si le réseau cible a plusieurs sous-réseaux et de ces sous-réseaux a le même nom que le sous-réseau sur lequel se trouve l’ordinateur virtuel source, l’ordinateur virtuel de réplica sera connecté à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, l’ordinateur virtuel va être connecté pour le premier sous-réseau dans le réseau.

1. La première commande Obtient des serveurs pour le coffre-fort Azure récupération de Site en cours. La commande stocke les serveurs de récupération de Site Microsoft Azure dans la variable du tableau $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. La deuxième commande Obtient le réseau de récupération de site pour le premier serveur dans le tableau $Servers. La commande stocke les réseaux dans la variable $Networks.


        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. La troisième commande Obtient des réseaux virtuels Azure, puis cette valeur dans la variable $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. L’applet de commande finale crée un mappage entre le réseau principal et le réseau des machines virtuelles Azure. L’applet de commande spécifie le réseau principal en tant que premier élément de $Networks. L’applet de commande spécifie un réseau de la machine virtuelle en tant que premier élément de $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## <a name="step-9-enable-protection-for-virtual-machines"></a>Étape 9 : Activez la protection des machines virtuelles

Une fois que les serveurs, les nuages et les réseaux sont correctement configurés, vous pouvez activer la protection pour les ordinateurs virtuels dans le nuage.

 Notez les points suivants :

 - Machines virtuelles doit répondre aux exigences d’Azure. Vérifiez ces [conditions préalables et la prise en charge](site-recovery-best-practices.md) dans le guide de planification.

 - Pour activer la protection, le système d’exploitation et le système d’exploitation, les propriétés de disque doivent être définies pour l’ordinateur virtuel. Lorsque vous créez un ordinateur virtuel dans VMM à l’aide d’un modèle d’ordinateur virtuel, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes sous les onglets **Général** et la **Configuration matérielle** des machine virtuelle de propriétés. Si vous ne définissez ces propriétés dans VMM, vous serez en mesure de les configurer dans le portail de récupération de Site Azure.


  1. Pour activer la protection, exécutez la commande suivante pour obtenir le conteneur de protection :

            $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName

  2. Obtenez l’entité de protection (VM) en exécutant la commande suivante :

            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer

  3. Activer la reprise après sinistre de la machine virtuelle en exécutant la commande suivante :

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## <a name="test-your-deployment"></a>Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement sur incident pour une seule machine virtuelle, ou créer un plan de récupération qui se compose de plusieurs machines virtuelles et exécuter un test de basculement sur incident pour le plan. Basculement test simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez que :

- Si vous souhaitez vous connecter à l’ordinateur virtuel dans Azure à l’aide du Bureau à distance après le basculement, activer la connexion Bureau à distance sur l’ordinateur virtuel avant d’exécuter le test de basculement.
- Après le basculement, vous utiliserez une adresse IP publique pour se connecter à l’ordinateur virtuel dans Azure à l’aide du Bureau à distance. Si vous souhaitez le faire, assurez-vous que vous n’avez pas les stratégies de domaine qui vous empêchent de vous connecter à un ordinateur virtuel à l’aide d’une adresse publique.

Pour vérifier la fin de l’opération, suivez les étapes de la [Surveillance de l’activité](#monitor).


### <a name="run-a-test-failover"></a>Exécutez un test de basculement

1.  Démarrer le basculement de test en exécutant la commande suivante :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Exécuter un basculement planifié

1. Démarrer le basculement planifié en exécutant la commande suivante :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié

1. Démarrer le basculement non planifié en exécutant la commande suivante :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  


## <a name=monitor></a>Surveillance de l’activité

Utilisez les commandes suivantes pour surveiller l’activité. Notez que vous devez attendre entre les tâches de la fin du traitement.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Étapes suivantes

[Pour en savoir plus](https://msdn.microsoft.com/library/azure/mt637930.aspx) sur la récupération de Site Azure avec les applets de commande PowerShell de gestionnaire de ressources Azure.
