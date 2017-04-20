<properties
    pageTitle="Répliquer les machines virtuelles de Hyper-V dans des nuages VMM à l’aide de la récupération de Site Azure et PowerShell | Microsoft Azure"
    description="Apprenez à automatiser la réplication des machines virtuelles de Hyper-V dans des nuages VMM à l’aide de la récupération de Site et de PowerShell."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell---classic"></a>Répliquer les machines virtuelles de Hyper-V dans les nuages VMM à Azure à l’aide de Powershell - classique

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-vmm-to-azure.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portail classique](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - classique](site-recovery-deploy-with-powershell.md)


## <a name="overview"></a>Vue d’ensemble

Récupération de Site Azure contribue à votre stratégie d’entreprise après incident et de continuité d’activité (BCDR) de récupération par l’orchestration de réplication, le basculement et la restauration des ordinateurs virtuels dans un certain nombre de scénarios de déploiement. Pour une liste complète des scénarios de déploiement, consultez la [vue d’ensemble de la récupération de Site Azure](site-recovery-overview.md).

Cet article vous indique comment utiliser PowerShell pour automatiser les tâches courantes à effectuer lorsque vous configurez la récupération de Site Azure pour répliquer des ordinateurs virtuels de Hyper-V dans des nuages de System Center VMM pour stockage Azure.

L’article inclut les conditions préalables pour le scénario et vous montre comment configurer un coffre-fort de restauration de Site, installez le fournisseur de récupération de Site Azure sur le serveur VMM de source, inscrire le serveur dans le coffre-fort, ajouter un compte de stockage Azure, installer l’agent de Services de récupération Azure sur les serveurs hôtes Hyper-V, configurer les paramètres de protection pour les nuages VMM qui seront appliquées à tous les ordinateurs virtuels protégés , puis activez la protection des machines virtuelles. Terminez en tester le basculement sur incident pour vous assurer que tout fonctionne comme prévu.

Si vous avez des problèmes de configuration de ce scénario, envoyez vos questions sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : [le Gestionnaire de ressources et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement standard.



## <a name="before-you-start"></a>Avant de commencer

Assurez-vous que vous avez ces conditions préalables en place :

### <a name="azure-prerequisites"></a>Conditions préalables Azure

- Vous devez disposer d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- Vous devez posséder un compte de stockage Azure pour stocker les données répliquées. Le compte doit géo-réplication est activée. Il doit être dans la même région, comme le coffre-fort de la récupération de Site Azure et être associé à l’abonnement de même. [En savoir plus sur le stockage Azure](../storage/storage-introduction.md).
- Vous devez vous assurer que les ordinateurs virtuels que vous souhaitez protéger satisfont aux [conditions préalables d’Azure VM](site-recovery-best-practices.md#virtual-machines).

### <a name="vmm-prerequisites"></a>Composants requis VMM
- Vous devez le serveur VMM sur System Center 2012 R2.
- Vous devez au moins un nuage sur le serveur VMM, que vous souhaitez protéger. Le nuage doit contenir :
    - Un ou plusieurs groupes hôtes VMM.
    - Un ou plusieurs serveurs d’hôte Hyper-V ou clusters dans chaque groupe hôte.
    - Un ou plusieurs ordinateurs virtuels sur le serveur source Hyper-V.

### <a name="hyper-v-prerequisites"></a>Conditions préalables de Hyper-V

- Les serveurs hôtes Hyper-V doivent être en cours d’exécution au moins **Windows Server 2012** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012** et installé les dernières mises à jour.
- Si vous utilisez Hyper-V dans une note de cluster que broker de cluster n’est pas créé automatiquement si vous disposez d’un cluster de base d’adresses IP statique. Vous devez configurer manuellement le service broker de cluster. Pour ce faire, dans le Gestionnaire de serveur > Gestionnaire du Cluster de basculement, connectez-vous au cluster, cliquez sur **Configurer le rôle** et sélectionnez **Broker de réplica de Hyper-V** dans l’écran **Sélectionnez le rôle** de l’Assistant haute disponibilité.
- N’importe quel serveur d’hôte Hyper-V ou le cluster pour lequel vous souhaitez gérer la protection doit être inclus dans un nuage VMM.

### <a name="network-mapping-prerequisites"></a>Conditions préalables du mappage réseau
Lorsque vous protégez des ordinateurs virtuels dans les mappages du mappage réseau Azure entre les réseaux de machine virtuelle sur le serveur VMM de source et ciblez les réseaux Azure pour activer les éléments suivants :

- Tous les ordinateurs qui basculent sur le même réseau peuvent se connecter entre eux, quel que soit le plan de récupération, ils sont en cours.
- Si une passerelle réseau est configuré sur la cible de réseau Azure, machines virtuelles peuvent se connecter à d’autres ordinateurs virtuels de locaux.
- Si vous ne configurez pas réseau mappage uniquement d’ordinateurs virtuels qui basculent dans le même plan de restauration pourront se connecter à l’autre après le basculement vers Azure.

Si vous souhaitez déployer le mappage réseau, vous devez les éléments suivants :

- Les ordinateurs virtuels que vous souhaitez protéger sur le serveur VMM de source doit être connectés à un réseau de la machine virtuelle. Ce réseau doit être lié à un réseau logique qui est associé avec le nuage.
- Un réseau Azure auquel les machines virtuelles dupliquées peuvent se connecter après le basculement. Vous devez sélectionner ce réseau au moment du basculement. Le réseau doit être dans la même région en tant que votre abonnement Azure récupération de Site.
- [En savoir plus](site-recovery-network-mapping.md) sur le mappage de réseau :

###<a name="powershell-prerequisites"></a>Conditions préalables de PowerShell
Vérifiez que vous disposez de PowerShell d’Azure prêt à l’emploi. Si vous utilisez déjà PowerShell, vous devrez mettre à niveau vers la version 0.8.10 ou ultérieure. Pour plus d’informations sur le paramétrage de PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md). Une fois que vous avez installé et configuré de PowerShell, vous pouvez afficher toutes les applets de commande disponibles pour le service [ici](https://msdn.microsoft.com/library/dn850420.aspx).

Pour en savoir plus sur les astuces qui peuvent aider à qu'utiliser les applets de commande, par exemple comment les valeurs de paramètre, les entrées et les sorties sont généralement traités dans Azure PowerShell, consultez [Mise en route avec les applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Étape 1 : Définir l’abonnement

Dans PowerShell, exécutez ces applets de commande :

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Remplacer les éléments de la « < > » par vos informations spécifiques.

## <a name="step-2-create-a-site-recovery-vault"></a>Étape 2 : Créer un coffre-fort de récupération de Site

Dans PowerShell, remplacez les éléments au sein de la « < > » avec des informations spécifiques et exécuter ces commandes :

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>Étape 3 : Générer une clé d’enregistrement de coffre-fort

Générer une clé d’enregistrement dans la chambre forte. Après le téléchargement du fournisseur de récupération de Site Azure et l’installer sur le serveur VMM, vous utiliserez cette clé pour inscrire le serveur VMM dans la chambre forte.

1.  Obtenir le fichier de configuration de coffre-fort et définir le contexte :

    ```

    $VaultName = "<testvault123>"
    $VaultGeo  = "<Southeast Asia>"
    $OutputPathForSettingsFile = "<c:\>"

    $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

    ```

2.  Définir le contexte de la chambre forte en exécutant les commandes suivantes :

    ```

    $VaultSettingFilePath = $vaultSetingsFile.FilePath
    $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

    ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Étape 4 : Installer le fournisseur de récupération de Site Azure

1.  Sur l’ordinateur VMM, créez un répertoire en exécutant la commande suivante :

    ```

    pushd C:\ASR\

    ```

2.  Extraire les fichiers à l’aide du fournisseur de téléchargé en exécutant la commande suivante

    ```

    AzureSiteRecoveryProvider.exe /x:. /q

    ```

3.  Installez le fournisseur à l’aide des commandes suivantes :

    ```

    .\SetupDr.exe /i

    ```

    ```

    $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
    do
    {
        $isNotInstalled = $true;
        if(Test-Path $installationRegPath)
        {
            $isNotInstalled = $false;
        }
    }While($isNotInstalled)

    ```

    Attendez que l’installation se termine.

4.  Inscrire le serveur dans le coffre-fort à l’aide de la commande suivante :

    ```

    $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
    pushd $BinPath
    $encryptionFilePath = "C:\temp\"
    .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

    ```

## <a name="step-5-create-an-azure-storage-account"></a>Étape 5 : Créer un compte de stockage Azure

Si vous n’avez pas un compte de stockage Azure, créez un compte de géo-réplication activée en exécutant la commande suivante :

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Notez que le compte de stockage doit être dans la même région, comme le service de récupération de Site d’Azure et si elle doit être associée à l’abonnement même.


## <a name="step-6-install-the-azure-recovery-services-agent"></a>Étape 6 : Installation de l’Agent de Services de récupération Azure

À partir du portail Azure, installez l’agent de Services de récupération Azure sur chaque serveur hôte Hyper-V dans les nuages VMM à protéger.

Exécutez la commande suivante sur tous les hôtes VMM :

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>Étape 7 : Configuration cloud paramètres de protection

1.  Créer un profil de protection de nuage et Azure en exécutant la commande suivante :

    ```

    $ReplicationFrequencyInSeconds = "300";
    $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds  $ReplicationFrequencyInSeconds;

    ```

2.  Obtenir un conteneur de protection en exécutant les commandes suivantes :

    ```

    $PrimaryCloud = "testcloud"
    $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

    ```

3.  Démarrer l’association du conteneur de protection avec le nuage :

    ```

    $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

    ```

4.  Une fois la tâche terminée, exécutez la commande suivante :


        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


5.  Une fois que la tâche est terminée, exécutez la commande suivante :


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



Pour vérifier la fin de l’opération, suivez les étapes de la [Surveillance de l’activité](#monitor).

## <a name="step-8-configure-network-mapping"></a>Étape 8 : Configuration de mappage réseau
Avant de commencer la mappage réseau Vérifiez que les ordinateurs virtuels sur le serveur VMM de source sont connectés à un réseau de la machine virtuelle. En outre, créer un ou plusieurs réseaux virtuels Azure. Notez que plusieurs réseaux de l’ordinateur virtuel peuvent être mappés à un seul réseau Azure.

Notez que si le réseau cible a plusieurs sous-réseaux et de ces sous-réseaux a le même nom que le sous-réseau sur lequel se trouve l’ordinateur virtuel source, puis l’ordinateur virtuel de réplica sera connecté au sous-réseau cible après le basculement. Si il n’est pas un sous-réseau cible avec un nom correspondant, l’ordinateur virtuel va être connecté pour le premier sous-réseau dans le réseau.

La première commande Obtient des serveurs pour le coffre-fort Azure récupération de Site en cours. La commande stocke les serveurs de récupération de Site Microsoft Azure dans la variable du tableau $Servers.

    $Servers = Get-AzureSiteRecoveryServer


La deuxième commande Obtient le réseau de récupération de site pour le premier serveur dans le tableau $Servers. La commande stocke les réseaux dans la variable $Networks.


    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

La troisième commande obtient vos abonnements Azure à l’aide de l’applet de commande Get-AzureSubscription, puis stocke cette valeur dans la variable $Subscriptions.

    $Subscriptions = Get-AzureSubscription



La quatrième commande obtient Azure réseaux virtuels à l’aide de l’applet de commande Get-AzureVNetSite et ensuite cette valeur dans la variable $AzureVmNetworks.


    $AzureVmNetworks = Get-AzureVNetSite



L’applet de commande finale crée un mappage entre le réseau principal et le réseau des machines virtuelles Azure. L’applet de commande spécifie le réseau principal en tant que premier élément de $Networks. L’applet de commande spécifie un réseau de la machine virtuelle en tant que premier élément de $AzureVmNetworks à l’aide de son ID. La commande inclut votre ID d’abonnement Azure.


    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>Étape 9 : Activez la protection des machines virtuelles

Une fois que les serveurs, les réseaux et les nuages sont correctement configurés, vous pouvez activer la protection pour les ordinateurs virtuels dans le nuage. Notez les points suivants :

Machines virtuelles doivent répondre aux [conditions requises d’Azure VM](site-recovery-best-practices.md#virtual-machines).

Pour activer la protection du système d’exploitation et le système d’exploitation, les propriétés de disque doivent être définies pour l’ordinateur virtuel. Lorsque vous créez un ordinateur virtuel dans VMM à l’aide d’un modèle d’ordinateur virtuel, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes sous les onglets **Général** et la **Configuration matérielle** des machine virtuelle de propriétés. Si vous ne définissez ces propriétés dans VMM, vous serez en mesure de les configurer dans le portail de récupération de Site Azure.



1.  Pour activer la protection, exécutez la commande suivante pour obtenir le conteneur de protection :

        $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName



2. Obtenez l’entité de protection (VM) en exécutant la commande suivante :


        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



3. Activer la reprise après sinistre de la machine virtuelle en exécutant la commande suivante :



        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity  -Protection Enable -Force



## <a name="test-your-deployment"></a>Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération qui se compose de plusieurs machines virtuelles et exécuter un test de basculement pour le plan. Basculement de test simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez que :

- Si vous souhaitez vous connecter à l’ordinateur virtuel dans Azure à l’aide du Bureau à distance après le basculement, activer la connexion Bureau à distance sur l’ordinateur virtuel avant d’exécuter le test de basculement.
- Après le basculement, vous utiliserez une adresse IP publique pour se connecter à l’ordinateur virtuel dans Azure à l’aide du Bureau à distance. Si vous souhaitez le faire, assurez-vous que vous n’avez pas les stratégies de domaine qui vous empêchent de vous connecter à un ordinateur virtuel à l’aide d’une adresse publique.

Pour vérifier la fin de l’opération, suivez les étapes de la [Surveillance de l’activité](#monitor).

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Créer un fichier .xml comme modèle pour votre plan de récupération en utilisant les données ci-dessous et enregistrez-le en tant que « C:\RPTemplatePath.xml ».
2. Modifier l’Id de nœud RecoveryPlan, le nom, PrimaryServerId et SecondaryServerId.
3. Modifiez le nœud ProtectionEntity PrimaryProtectionEntityId (vmid à partir de VMM).
4. Vous pouvez ajouter davantage d’ordinateurs virtuels en ajoutant plus de nœuds ProtectionEntity.



        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"  PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"  SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03- cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"  Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"  After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



4. Remplir les données dans le modèle :


        $TemplatePath = "C:\RPTemplatePath.xml";



5. Créer le RecoveryPlan :

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;



### <a name="run-a-test-failover"></a>Exécutez un test de basculement

1.  Obtenir l’objet RecoveryPlan en exécutant la commande suivante :

        $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;


2.  Démarrer le basculement de test en exécutant la commande suivante :


        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


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

[Pour en savoir plus](https://msdn.microsoft.com/library/dn850420.aspx) sur les applets de commande PowerShell de récupération de Site Azure. </a>.
