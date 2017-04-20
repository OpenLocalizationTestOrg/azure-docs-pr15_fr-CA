<properties
    pageTitle="Supprimer des serveurs et de désactiver la protection | Microsoft Azure" 
    description="Cet article décrit comment faire pour annuler l’inscription des serveurs à partir d’un coffre-fort de restauration de Site et désactiver la protection pour les ordinateurs virtuels et les serveurs physiques." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="remove-servers-and-disable-protection"></a>Supprimer des serveurs et de désactiver la protection

Le service de récupération de Site Azure contribue à votre stratégie de récupération (BCDR) de continuité d’activité et de reprise après sinistre de l’entreprise par l’orchestration de réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire sur site. Pour une vue d’ensemble rapide de lecture [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md)

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment faire pour annuler l’inscription des serveurs dans le coffre-fort de la récupération de Site et comment faire pour désactiver la protection pour les ordinateurs virtuels protégés par la restauration du Site. 

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-vmm-server"></a>Annuler l’inscription d’un serveur VMM

Annuler l’inscription d’un serveur VMM à partir d’un stockage en chambre forte en supprimant le serveur dans l’onglet **serveurs** dans le portail de récupération de Site Azure. Notez que :

-  **Serveur connecté de VMM**: nous vous recommandons de vous désinscrire le serveur VMM, lorsqu’il est connecté à Azure. Cela garantit que les paramètres sur le serveur VMM sur site et les serveurs VMM associés (serveurs VMM qui contiennent des nuages qui sont mappés sur les nuages sur le serveur que vous voulez supprimer) sont correctement nettoyées. Nous vous recommandons de que vous supprimez uniquement un serveur non connecté s’il existe un problème permanent grâce à la connectivité.
- **Serveur VMM non connecté**: si le serveur VMM n’est pas connecté lorsque vous la supprimez, vous devez exécuter un script manuellement pour effectuer le nettoyage. Le script est disponible dans la [Galerie de Microsoft](http://aka.ms/asr-cleanup-script-vmm). Notez l’ID de VMM du serveur pour terminer le processus de nettoyage manuel.
- **Serveur VMM cluster**: Si vous devez annuler l’inscription d’un serveur VMM qui est déployé dans un cluster, effectuez les opérations suivantes :

    - Si le serveur de connexion, supprimez le serveur VMM connecté sous l’onglet **serveurs** . Pour désinstaller le fournisseur sur le serveur, connectez-vous sur chaque nœud du cluster et la désinstaller à partir du Panneau de configuration. Exécutez le script de nettoyage référencé dans la section précédente sur tous les nœuds passifs du cluster pour supprimer les entrées d’inscription.
    - Si le serveur n’est pas connecté, vous devrez exécuter le script de nettoyage sur tous les nœuds du cluster.

### <a name="unregister-an-unconnected-vmm-server"></a>Annuler l’inscription d’un serveur VMM non connecté

Sur le serveur VMM, vous souhaitez supprimer :

1. Annuler l’inscription du serveur VMM à partir du portail Azure.
2. Sur le serveur VMM, téléchargez le script de nettoyage.
3. Ouvrez PowerShell à l’exécuter en tant qu’administrateur permet de modifier la stratégie d’exécution pour la portée par défaut (LocalMachine).
4. Suivez les instructions dans le script. 

Sur les serveurs VMM ont des nuages qui sont associés à des nuages sur le serveur que vous supprimez :

1. Exécutez le script de nettoyage, suivez les étapes 2 à 4.
2. Spécifiez l’ID de VMM pour le serveur VMM qui a été supprimée. 
3. Ce script supprime les informations d’enregistrement pour le serveur VMM et du cloud, informations sur l’association.


## <a name="unregister-a-hyper-v-server-in-a-hyper-v-site"></a>Annuler l’inscription d’un serveur Hyper-V dans un site d’Hyper-V

Lors de la récupération de Site Azure est déployée pour protéger les ordinateurs virtuels situés sur un serveur Hyper-V dans un site d’Hyper-V (avec aucun serveur VMM), vous pouvez désinscrire un serveur Hyper-V à partir d’une chambre forte comme suit :

1. Désactiver la protection pour les ordinateurs virtuels situé sur le serveur Hyper-V.
2. Sous l’onglet **serveurs** dans le portail de récupération de Site d’Azure, sélectionnez le serveur > Supprimer. Le serveur ne doit être connecté à Azure lors de cette opération.
3. Exécutez le script suivant pour nettoyer les paramètres sur le serveur et annuler son inscription à partir de la chambre forte. 

        pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }
    
            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host
        
            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }
        
            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }
        
            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
        
            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."  
                    Remove-Item -Recurse -Path $registrationPath
                }
    
                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }
    
                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }
    
            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {   
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0] 
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd


## <a name="stop-protecting-a-hyper-v-virtual-machine"></a>Arrêter la protection d’un ordinateur virtuel Hyper-V

Si vous souhaitez arrêter la protection d’un ordinateur virtuel Hyper-V, vous devez supprimer la protection. Selon la façon dont vous supprimez la protection vous devrez nettoyer manuellement sur l’ordinateur, les paramètres de protection. 

### <a name="remove-protection"></a>Suppression de la protection

1. Dans l’onglet **ordinateurs virtuels** des propriétés du nuage, sélectionnez l’ordinateur virtuel > **Supprimer**.
2. Dans la page **Confirmer la suppression de Virtual Machine** vous disposez de deux options :

    - **Désactiver la protection**: Si vous activez et enregistrez cette option l’ordinateur virtuel sera n’est plus protégé par la récupération de Site. Paramètres de protection de l’ordinateur virtuel seront automatiquement nettoyés.
    - **Supprimer de la chambre forte**— si vous sélectionnez cette option, l’ordinateur virtuel sera retiré uniquement le coffre-fort de la récupération de Site. Paramètres de protection des locaux de l’ordinateur virtuel ne seront pas affectées. Vous devrez nettoyer les paramètres manuellement pour supprimer les paramètres de protection et de supprimer l’ordinateur virtuel à partir de l’abonnement Azure et de supprimer des paramètres de protection que vous devrez les nettoyer manuellement en suivant les instructions ci-dessous.

Si vous choisissez de supprimer la machine virtuelle et ses disques durs, ils seront supprimés à partir de l’emplacement cible.

### <a name="clean-up-protection-settings-manually-between-vmm-sites"></a>Nettoyer les paramètres de protection manuellement (entre sites-VMM)

Si vous avez choisi **d’Arrêter la gestion de l’ordinateur virtuel**, nettoyer manuellement les paramètres :

1. Sur le serveur principal, exécutez ce script à partir de la console VMM pour nettoyer les paramètres de l’ordinateur virtuel principal. Dans la console, cliquez sur le bouton PowerShell pour ouvrir la console VMM PowerShell. Remplacez SQLVM1 par le nom de votre machine virtuelle.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Sur le serveur VMM secondaire, exécutez ce script pour nettoyer les paramètres de l’ordinateur virtuel secondaire :

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force

3. Sur le serveur VMM secondaire, après avoir exécuté le script actualiser les ordinateurs virtuels sur le serveur hôte Hyper-V pour l’ordinateur virtuel secondaire obtient nouveau détecté dans la console VMM.
4. Les étapes ci-dessus effacera le serveur VMM réplication des paramètres uniquement. Si vous souhaitez supprimer la réplication de machine virtuelle pour l’ordinateur virtuel. Vous devrez effectuer des étapes à la fois du serveur primaire et secondaire ci-dessous machines virtuelles. Exécutez le script ci-dessous pour supprimer la réplication et remplacez SQLVM1 par le nom de votre machine virtuelle.

        Remove-VMReplication –VMName “SQLVM1”


### <a name="clean-up-protection-settings-manually-between-on-premises-vmm-sites-and-azure"></a>Nettoyer les paramètres de protection manuellement (entre les sites VMM sur site et Azure)

1. Sur le serveur VMM de source, exécutez ce script pour nettoyer les paramètres de l’ordinateur virtuel principal :

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Les étapes ci-dessus effacera le serveur VMM réplication des paramètres uniquement. Une fois que vous avez supprimé la réplication de serveur VMM garantir pour supprimer la réplication de l’ordinateur virtuel en cours d’exécution sur le serveur hôte Hyper-V avec ce script. Remplacez SQLVM1 par le nom de votre machine virtuelle et de host01.contoso.com avec le nom du serveur hôte Hyper-V.

        $vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

### <a name="clean-up-protection-settings-manually-between-hyper-v-sites-and-azure"></a>Nettoyer les paramètres de protection manuellement (entre les sites de Hyper-V et Azure)

1. Sur le serveur hôte de Hyper-V de source, pour supprimer la réplication de l’ordinateur virtuel utiliser ce script. Remplacez SQLVM1 par le nom de votre machine virtuelle.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

## <a name="stop-protecting-a-vmware-virtual-machine-or-a-physical-server"></a>Arrêter la protection d’une machine virtuelle VMware ou un serveur physique

Si vous souhaitez arrêter la protection d’une machine virtuelle VMware ou un serveur physique, vous devez supprimer la protection. Selon la façon dont vous supprimez la protection vous devrez nettoyer manuellement sur l’ordinateur, les paramètres de protection. 

### <a name="remove-protection"></a>Suppression de la protection

1. Dans l’onglet **ordinateurs virtuels** des propriétés du nuage, sélectionnez l’ordinateur virtuel > **Supprimer**.
2. Sur la **Supprimer un ordinateur virtuel** , sélectionnez une des options :

    - **Désactiver la protection (utilisation de redimensionnement de perçage et de volume de récupération)**— vous seulement voyez et activez cette option si vous avez :
        - **Resized le volume de l’ordinateur virtuel**— lorsque vous redimensionnez un volume de l’ordinateur virtuel passe dans un état critique. Dans ce cas, sélectionnez cette option. Elle désactive la protection tout en conservant les points de récupération dans Azure. Lorsque vous réactivez la protection de l’ordinateur les données du volume redimensionnée sera en Azure.
        - **Exécuter un basculement sur incident**, une fois que vous avez testé votre environnement en exécutant un basculement sur incident à partir des machines virtuelles VMware sur site ou des serveurs physiques vers Azure, sélectionnez cette option pour commencer à protéger vos ordinateurs virtuels de local à nouveau. Cette option désactive à chaque machine virtuelle, et vous devrez alors réactiver la protection pour les. Notez que :
            - La désactivation de l’ordinateur virtuel avec ce paramètre n’affecte pas la machine virtuelle de réplica dans Azure.
            - Vous ne désinstaller le service de la mobilité de la machine virtuelle.
    
    - **Désactiver la protection**: Si vous activez et enregistrez cette option la machine sera n’est plus protégée par la récupération de Site. Paramètres de protection de l’ordinateur seront automatiquement nettoyés.
    - **Supprimer de la chambre forte**— si vous sélectionnez cette option, l’ordinateur sera retiré uniquement le coffre-fort de la récupération de Site. Paramètres de protection des locaux de l’ordinateur ne seront pas affectées. Pour supprimer les paramètres de l’ordinateur et de supprimer l’ordinateur virtuel de l’Azure abonnement et vous devrez nettoyer les paramètres en désinstallant le service de la mobilité.
    
        ![Supprimer les options](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

















