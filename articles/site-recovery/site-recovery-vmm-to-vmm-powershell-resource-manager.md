<properties
    pageTitle="Répliquer les machines virtuelles de Hyper-V dans les nuages VMM sur un site secondaire de VMM à l’aide de PowerShell (responsable de ressources) | Microsoft Azure"
    description="Décrit comment déployer la récupération de Site Azure pour orchestrer la réplication, le basculement et la récupération de machines virtuelles de Hyper-V dans les nuages VMM sur un site secondaire de VMM à l’aide de PowerShell (responsable de ressources)"
    services="site-recovery"
    documentationCenter=""
    authors="sujaytalasila"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="sutalasi"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Répliquer les machines virtuelles de Hyper-V dans les nuages VMM sur un site secondaire de VMM à l’aide de PowerShell (responsable de ressources)

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-vmm-to-vmm.md)
- [Portail classique](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bienvenue dans restauration du Site Azure ! Utilisez cet article si vous souhaitez répliquer les machines virtuelles de Hyper-V sur site géré dans des nuages de System Center Virtual Machine Manager (VMM) vers un site secondaire. 

Cet article vous indique comment utiliser PowerShell pour automatiser les tâches courantes à effectuer lorsque vous configurez la récupération de Site Azure pour répliquer des ordinateurs virtuels de Hyper-V dans des nuages de System Center VMM sur nuages de System Center VMM de site secondaire.

L’article inclut les conditions préalables pour le scénario et vous montre 

- Comment faire pour configurer un coffre-fort de Services de récupération
- Installez le fournisseur de récupération de Site Azure sur le serveur VMM de source et le serveur VMM de cible
- Enregistrer l’ou les serveurs VMM dans la chambre forte
- Configurer la stratégie de réplication pour le nuage VMM. Les paramètres de réplication de la stratégie s’appliquera à tous les ordinateurs virtuels protégés 
- Activer la protection pour les ordinateurs virtuels. 
- Tester le basculement d’ordinateurs virtuels individuellement ou dans le cadre d’un plan de récupération pour vous assurer que tout fonctionne comme prévu.
- Effectuer planifié ou un basculement non planifié de machines virtuelles, individuellement ou dans le cadre d’un plan de récupération pour vous assurer que tout fonctionne comme prévu.

Si vous avez des problèmes de configuration de ce scénario, envoyez vos questions sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et l’utilisation des ressources : le Gestionnaire de ressources Azure et classique. Azure dispose également de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement. Cet article décrit le modèle de déploiement du Gestionnaire de ressources.



## <a name="on-premises-prerequisites"></a>Conditions préalables du local

Voici ce que vous devez sur les sites principal et secondaire sur site pour déployer ce scénario :

**Conditions préalables** | **Détails** 
--- | ---
**VMM** | Nous vous recommandons de que déployer un serveur VMM sur le site principal et un serveur VMM sur le site secondaire.<br/><br/> Vous pouvez également [répliquer entre des clouds sur un seul serveur VMM](site-recovery-single-vmm.md). Pour ce faire, vous devez au moins deux clouds configurés sur le serveur VMM.<br/><br/> Serveurs VMM doivent être en cours d’exécution au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Chaque serveur VMM doit disposer d’un ou plusieurs nuages configurés et tous les nuages doivent avoir le profil de capacité de Hyper-V. <br/><br/>Nuages doivent contenir un ou plusieurs groupes d’hôtes VMM.<br/><br/>Pour en savoir plus sur la configuration des nuages VMM dans la [configuration de la structure du nuage VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), et [procédure pas à pas : création des nuages privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Serveurs VMM doivent avoir accès à internet. 
**Hyper-V** | Serveurs Hyper-V doivent être en cours d’exécution au moins Windows Server 2012 avec le rôle Hyper-V et installé les dernières mises à jour.<br/><br/> Un serveur Hyper-V doit contenir les ordinateurs virtuels d’un ou plusieurs.<br/><br/>  Serveurs de hôte Hyper-V doivent être placés dans les groupes hôtes dans les nuages VMM principales et secondaires.<br/><br/> Si vous utilisez Hyper-V dans un cluster Windows Server 2012 R2, vous devez installer [la mise à jour de 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Si vous utilisez Hyper-V dans un cluster de Windows Server 2012 Remarque que broker de cluster n’est pas créé automatiquement si vous disposez d’un cluster de base d’adresses IP statique. Vous devez configurer manuellement le service broker de cluster. [Pour en savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Fournisseur** | Lors du déploiement de la récupération de Site, vous installez le fournisseur de récupération de Site Azure sur les serveurs VMM. Le fournisseur communique avec récupération de Site sur 443 HTTPS pour orchestrer la réplication. Réplication de données a lieu entre les serveurs Hyper-V principales et secondaires sur le réseau local ou une connexion VPN.<br/><br/> Le fournisseur qui s’exécute sur le serveur VMM a besoin d’accéder à ces URL : *. hypervrecoverymanager.windowsazure.com ; *. AccessControl.Windows.NET ; *. backup.windowsazure.com ; *. BLOB.Core.Windows.NET ; *. store.core.windows.net.<br/><br/> En outre, autoriser la communication de pare-feu à partir des serveurs VMM pour les [plages IP de centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et autoriser le protocole HTTPS (443).

### <a name="network-mapping-prerequisites"></a>Conditions préalables du mappage réseau
Mappages de mappage réseau entre réseaux VMM VM sur les serveurs VMM principales et secondaires pour :

- Optimal, placez VMs de réplica sur les hôtes Hyper-V secondaires après le basculement.
- Connexion VM de réplica à des réseaux d’ordinateur virtuel appropriés.
- Si vous ne configurez pas les réplicas de mappage réseau qu'ordinateurs virtuels ne sont pas connectés à un réseau après le basculement.
- Si vous souhaitez configurer réseau lors de la récupération de Site de mappage déploiement ici est ce dont vous aurez besoin :

    - Assurez-vous que les ordinateurs virtuels sur le serveur hôte de Hyper-V source sont connectés à un réseau de VMM VM. Ce réseau doit être lié à un réseau logique qui est associé avec le nuage.
    - Vérifiez que le nuage secondaire que vous allez utiliser pour la restauration possède un réseau de machine virtuelle correspondant configuré. Ce réseau de la machine virtuelle doit être lié à un réseau logique associé le nuage secondaire.


Pour en savoir plus sur la configuration des réseaux VMM dans les articles suivants de la

- [Comment faire pour configurer des réseaux logiques dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Comment faire pour configurer les passerelles et les réseaux de l’ordinateur virtuel dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[En savoir plus](site-recovery-network-mapping.md) sur le fonctionne du mappage réseau.

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

1. Créer un groupe de ressources du Gestionnaire de ressources Azure si vous n’en avez pas déjà

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Créer un nouveau coffre-fort de Services de récupération et enregistrer l’objet de stockage en chambre forte ASR créé dans une variable (sera utilisé ultérieurement). Vous pouvez également récupérer la création d’un post d’objet de coffre-fort ASR à l’aide de l’applet de commande Get-AzureRMRecoveryServicesVault :-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## <a name="step-3-set-the-recovery-services-vault-context"></a>Étape 3 : Définir le contexte de la chambre forte de Services de récupération

1.  Si vous disposez d’un coffre-fort déjà créé, exécutez le dessous de commande pour obtenir le coffre-fort.

        $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname

2.  Définir le contexte de la chambre forte en exécutant la commande au-dessous de.

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


## <a name="step-5-create-and-associate-a-replication-policy"></a>Étape 5 : Créer et associer une stratégie de réplication

1.  Créer une stratégie de réplication de Hyper-V 2012 R2 en exécutant la commande suivante :

    
        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod 

    > [AZURE.NOTE] Le nuage VMM peut contenir des hôtes Hyper-V fonctionnant sous différentes versions de Windows Server (comme indiqué dans les conditions préalables de Hyper-V), mais la stratégie de réplication est la version du système d’exploitation spécifique. Si vous avez des hôtes différents sur les versions de système d’exploitation différent, puis créer des stratégies de réplication séparée pour chaque type de version du système d’exploitation. Pour, par exemple : Si vous avez cinq hôtes s’exécutant sur Windows serveurs 2012 et trois Windows Server 2012 R2, créez deux stratégies de réplication : un pour chaque type de versions de système d’exploitation.

2.  Obtenir le conteneur de protection primaire (primary VMM nuage) et le conteneur de protection de récupération (récupération nuage VMM) en exécutant les commandes suivantes :
    
        $PrimaryCloud = "testprimarycloud"
        $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

        $RecoveryCloud = "testrecoverycloud"
        $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
  
3.  Extraire la stratégie que vous avez créé à l’étape 1 avec le nom convivial de la stratégie

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Démarrer l’association du conteneur de protection (nuage VMM) avec la stratégie de réplication :

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer

5.  Attendez la fin du travail stratégie association. Vous pouvez vérifier si la tâche est terminée à l’aide de l’extrait de code PowerShell suivant.
   
        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
            $isJobLeftForProcessing = $true;
        }

    Une fois que la tâche est terminée, exécutez la commande suivante :

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)


Pour vérifier la fin de l’opération, suivez les étapes de la [Surveillance de l’activité](#monitor).

## <a name="step-5-configure-network-mapping"></a>Étape 5 : Configuration de mappage réseau

1. La première commande Obtient des serveurs pour le coffre-fort Azure récupération de Site en cours. La commande stocke les serveurs de récupération de Site Microsoft Azure dans la variable du tableau $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. Le sous commandes obtenir le réseau de récupération de site pour le serveur VMM de source et le serveur VMM de cible.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    
    > [AZURE.NOTE] Le serveur VMM source peut être le premier ou le deuxième élément dans le tableau serveurs. Vérifiez les noms des serveurs VMM et d’obtenir de manière appropriée les réseaux


4. L’applet de commande finale crée un mappage entre le réseau principal et la récupération. L’applet de commande spécifie le réseau principal en tant que premier élément de la $PrimaryNetworks et le réseau de récupération en tant que premier élément de $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-6-configure-storage-mapping"></a>Étape 6 : Configuration de mappage du stockage

1. Le sous commande Obtient la liste des catégories de stockage en $storageclassifications variable.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification


2. Le sous commandes obtenir la classification de la source dans la classification de variable et de la cible de $SourceClassificaion en $TargetClassification variable. 

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    
    > [AZURE.NOTE] Les classifications de source et cible peuvent être n’importe quel élément dans le tableau. Référence à la sortie de la sous commande, à l’index de la source et la cible des classifications dans un tableau de $storageclassifications. 
    
    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object - propriété FriendlyName, Id | Format-Table


3. La ci-dessous l’applet de commande crée un mappage entre le classement de la source et la classification de la cible. 

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-7-enable-protection-for-virtual-machines"></a>Étape 7 : Activez la protection des machines virtuelles

Une fois que les serveurs, les nuages et les réseaux sont correctement configurés, vous pouvez activer la protection pour les ordinateurs virtuels dans le nuage. 


  1. Pour activer la protection, exécutez la commande suivante pour obtenir le conteneur de protection :
    
            $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
    
  2. Obtenez l’entité de protection (VM) en exécutant la commande suivante :
    
            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
        
  3. Activer la réplication de la machine virtuelle en exécutant la commande suivante :

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy


## <a name="test-your-deployment"></a>Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération qui se compose de plusieurs machines virtuelles et exécuter un test de basculement pour le plan. Basculement de test simule votre mécanisme de basculement et de récupération dans un réseau isolé. 

> [AZURE.NOTE] Vous pouvez créer un plan de récupération pour votre application dans Azure portal.

Pour vérifier la fin de l’opération, suivez les étapes de la [Surveillance de l’activité](#monitor).


### <a name="run-a-test-failover"></a>Exécutez un test de basculement

1.  Exécuter le dessous des applets de commande pour obtenir des vos ordinateurs virtuels vers le réseau de la machine virtuelle à laquelle vous voulez tester le basculement.

        $Servers = Get-AzureRmSiteRecoveryServer
        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2.  Effectuez un test de basculement d’un ordinateur virtuel en procédant comme suit :
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1] 

2.  Effectuez un test de basculement d’un plan de récupération en procédant comme suit :
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1] 

### <a name="run-a-planned-failover"></a>Exécuter un basculement planifié

1. Effectuez un basculement planifié d’un ordinateur virtuel en procédant comme suit :
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Effectuez un basculement planifié d’un plan de récupération en procédant comme suit :
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié

1. Effectuez un basculement non planifié d’un ordinateur virtuel en procédant comme suit :
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 

2 effectuez un basculement non planifié d’un plan de récupération en procédant comme suit :
        
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 
    
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

