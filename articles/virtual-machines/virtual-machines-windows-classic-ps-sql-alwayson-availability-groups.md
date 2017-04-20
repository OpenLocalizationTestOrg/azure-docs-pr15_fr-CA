<properties
    pageTitle="Configurez toujours sur le groupe de disponibilité dans Azure VM avec PowerShell"
    description="Ce didacticiel utilise les ressources créées avec le modèle de déploiement classique et PowerShell pour créer un groupe toujours sur disponibilité dans Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm-with-powershell"></a>Configurez toujours sur le groupe de disponibilité dans Azure VM avec PowerShell

> [AZURE.SELECTOR]
- [Le Gestionnaire de ressources : modèle](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Le Gestionnaire de ressources : manuel](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Classique : l’interface utilisateur](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Classique : PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Les machines virtuelles Azure (VM) peut aider les administrateurs de base de données pour implémenter de réduire le coût d’un système de SQL Server à haute disponibilité. Ce didacticiel vous montre comment implémenter un groupe de disponibilité à l’aide de SQL Server Always On de bout en bout au sein d’un environnement d’Azure. À la fin du didacticiel, votre solution SQL Server Always On dans Azure se compose des éléments suivants :

- Un réseau virtuel contenant plusieurs sous-réseaux, y compris un front-end et un back-end sous-réseau

- Un contrôleur de domaine à un domaine Active Directory (AD)

- Ordinateurs virtuels déployés sur le sous-réseau du back-end et le joint au domaine AD de deux SQL Server

- Un cluster WSFC 3-nœud avec le modèle de quorum nœud majoritaire

- Un groupe de disponibilité avec deux réplicas validation synchrone d’une base de données de disponibilité

Ce scénario est choisi pour sa simplicité, pas pour sa rentabilité ou d’autres facteurs sur Azure. Par exemple, vous pouvez réduire le nombre de machines virtuelles pour un groupe de disponibilité de deux répliques pour pouvoir enregistrer sur le calcul des heures dans Azure à l’aide du contrôleur de domaine en tant que témoin de partage de fichier de quorum dans un cluster à 2 nœuds WSFC. Cette méthode réduit le nombre de machines virtuelles par un à partir de la configuration ci-dessus.

Ce didacticiel est conçu pour vous montrer les étapes requises pour configurer la solution décrite ci-dessus sans donne des détails sur les détails de chaque étape. Par conséquent, au lieu d’afficher les étapes de configuration de l’interface utilisateur graphique, il utilise PowerShell scripting pour accéder rapidement à travers chaque étape. Il suppose que les éléments suivants :

- Vous avez déjà un compte Azure avec l’abonnement de la machine virtuelle.

- Vous avez installé les [applets de commande PowerShell d’Azure](../powershell-install-configure.md).

- Vous avez déjà une compréhension solide de toujours sur les groupes de disponibilité pour des solutions sur site. Pour plus d’informations, consultez [Toujours sur les groupes de disponibilité (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Se connecter à votre abonnement Azure et créez le réseau virtuel

1. Dans une fenêtre PowerShell sur votre ordinateur local, importer le module Azure, télécharger un fichier de paramètres de publication sur votre ordinateur et connecter votre session PowerShell à votre abonnement Azure en important les paramètres de publication téléchargées.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    La commande **Get-AzurePublishgSettingsFile** génère automatiquement une gestion de certificat avec Azure qu’il télécharge sur votre ordinateur. Un navigateur s’ouvre automatiquement et vous êtes invité à entrer les informations d’identification de compte Microsoft pour votre abonnement Azure. Le fichier de téléchargement **.publishsettings** contient toutes les informations dont vous avez besoin gérer votre abonnement Azure. Après avoir enregistré ce fichier dans un répertoire local, l’importer à l’aide de la commande **d’Importation-AzurePublishSettingsFile** .

    >[AZURE.NOTE] Le fichier publishsettings contient vos informations d’identification (non codées) qui sont utilisées pour administrer vos abonnements Azure et les services. La meilleure pratique de sécurité pour ce fichier est pour stocker temporairement en dehors de vos répertoires source (par exemple dans le dossier Libraries\Documents), puis le supprimer une fois l’importation terminée. Un accès utilisateur malveillant dans le fichier publishsettings peut modifier, créer et supprimer vos services Azure.

1. Définir une série de variables que vous utiliserez pour créer votre infrastructure informatique de nuage.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Faites attention à ce qui suit pour vous assurer que vos commandes réussira ultérieurement :

    - Les variables **$storageAccountName** et **$dcServiceName** doivent être uniques car ils sont utilisés pour identifier de votre nuage nuage et compte de serveur de stockage, respectivement, sur internet.

    - Les noms spécifiés pour les variables **$affinityGroupName** et **$virtualNetworkName** sont configurés dans le document de configuration de réseau virtuel que vous utiliserez ultérieurement.

    - **$sqlImageName** Spécifie le nom de mise à jour de l’image de machine virtuelle qui contient l’édition entreprise de SQL Server 2012 Service Pack 1.

    - Pour plus de simplicité, **Contoso ! 000** est le mot de passe utilisé tout au long de la totalité du didacticiel.

1. Créer un groupe d’affinité.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

1. Créer un réseau virtuel en important un fichier de configuration.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Le fichier de configuration contient le document XML suivant. En bref, il spécifie un réseau virtuel appelé **ContosoNET** dans le groupe d’affinité appelé **ContosoAG**et il a le d’espace adresse **10.10.0.0/16** et a deux sous-réseaux, **10.10.1.0/24** et **10.10.2.0/24**, qui sont le sous-réseau avant et arrière de sous-réseau, respectivement. Le sous-réseau avant est où vous pouvez placer des applications clientes telles que Microsoft SharePoint, et le sous-réseau précédent où vous placez les ordinateurs virtuels de SQL Server. Si vous modifiez les variables **$affinityGroupName** et **$virtualNetworkName** précédemment, vous devez également modifier les noms correspondants ci-dessous.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

1. Créer un compte de stockage qui est associé au groupe de l’affinité, vous avez créé et définissez-le comme le compte de stockage actuel de votre abonnement.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

1. Créer le serveur de contrôleur de domaine dans le nouveau jeu de service et de disponibilité de nuage.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Cette série de commandes de canal effectuer les opérations suivantes :

    - **Nouveau-AzureVMConfig** crée une configuration de machine virtuelle.

    - **Add-AzureProvisioningConfig** fournit les paramètres de configuration d’un serveur de Windows autonome.

    - **Ajouter-AzureDataDisk** ajoute le disque de données que vous utiliserez pour stocker les données Active Directory, avec la mise en cache option est définie sur aucun.

    - **Nouveau-AzureVM** crée un nouveau service de cloud et crée le nouveau Azure VM dans le nouveau service de cloud.

1. Attendez que la nouvelle machine virtuelle être entièrement mis en service et téléchargez le fichier Bureau à distance à votre répertoire de travail. Dans la mesure où la nouvelle Azure VM prend beaucoup de temps à la disposition, la boucle while continue à l’interroger la nouvelle machine virtuelle jusqu'à ce qu’il soit prêt à l’emploi.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

Le serveur de contrôleur de domaine est désormais correctement configuré. Ensuite, vous allez configurer le domaine Active Directory sur ce serveur de contrôleur de domaine. Laissez la fenêtre PowerShell ouverte sur votre ordinateur local. Vous l’utiliserez ultérieurement pour créer les ordinateurs virtuels de deux SQL Server.

## <a name="configure-the-domain-controller"></a>Configurer le contrôleur de domaine

1. Se connecter au serveur contrôleur de domaine en exécutant le fichier Bureau à distance. Utilisez le nom d’utilisateur AzureAdmin et le mot de passe de l’administrateur de la machine **Contoso ! 000**, que vous avez spécifiée lors de la création de la nouvelle machine virtuelle.

1. Ouvrez une fenêtre PowerShell en mode administrateur.

1. Exécutez la commande suivante **DCPROMO. EXE** commande pour configurer le domaine **corp.contoso.com** , avec les répertoires de données sur le lecteur M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Une fois la commande terminée, l’ordinateur virtuel redémarre automatiquement.

1. Connecter au serveur contrôleur de domaine à nouveau en exécutant le fichier Bureau à distance. Cette fois, connectez-vous en tant que **Corp\Administrateur**.

1. Ouvrez une fenêtre PowerShell en mode administrateur et importer le module Active Directory PowerShell à l’aide de la commande suivante :

        Import-Module ActiveDirectory

1. Exécutez les commandes suivantes pour ajouter les trois utilisateurs au domaine.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** est utilisé pour configurer tout ce qui concerne les instances de service SQL Server, le cluster WSFC et le groupe de disponibilité. **CORP\SQLSvc1** et **CORP\SQLSvc2** sont utilisés comme comptes de service SQL Server pour les ordinateurs virtuels de deux SQL Server.

1. Ensuite, exécutez les commandes suivantes pour donner des autorisations pour créer des objets ordinateur dans le domaine **CORP\Install** .

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Le GUID spécifié ci-dessus est le GUID pour le type de l’objet ordinateur. Le compte **CORP\Install** a besoin de l’autorisation de **Lire toutes les propriétés** et **Créer des objets ordinateur** pour créer les objets Active Direct pour le cluster WSFC. L’autorisation de **Lire toutes les propriétés** a déjà été donnée à CORP\Install par défaut, vous n’avez pas besoin du lui accorder explicitement. Pour plus d’informations sur les autorisations nécessaires pour créer le cluster WSFC, reportez-vous à la section [Guide pas à pas de Cluster de basculement : configuration des comptes dans Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Maintenant que vous avez terminé de configurer les objets Active Directory et l’utilisateur, vous créez deux machines virtuelles de SQL Server et les ajouter dans ce domaine.

## <a name="create-the-sql-server-vms"></a>Créer des ordinateurs virtuels SQL Server

1. Continuer à utiliser la fenêtre PowerShell qui est ouverte sur votre ordinateur local. Définissez les variables supplémentaires suivantes :

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    Le d’adresse IP **10.10.0.4** est généralement affecté à la première virtuelle que vous créez dans le **10.10.0.0/16** de sous-réseau de votre réseau virtuel Azure. Vous devez vérifier que ceci est l’adresse de votre serveur de contrôleur de domaine en exécutant **IPCONFIG**.

1. Exécutez les commandes suivantes de canal pour créer la première virtuelle du cluster WSFC, nommé **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Notez les informations suivantes concernant la commande ci-dessus :

    - **Nouveau-AzureVMConfig** crée une configuration de machine virtuelle avec le nom de jeu de disponibilité souhaité. Les ordinateurs virtuels suivants seront créés avec le même nom de jeu de disponibilité afin qu’ils appartiennent au même jeu de disponibilité.

    - **Ajouter-AzureProvisioningConfig** joint la machine virtuelle pour le domaine Active Directory que vous avez créé.

    - **Ensemble-AzureSubnet** place la machine virtuelle dans le sous-réseau précédent.

    - **Nouveau-AzureVM** crée un nouveau service de cloud et crée le nouveau Azure VM dans le nouveau service de cloud. Le paramètre **DnsSettings** Spécifie que le serveur DNS pour les serveurs dans le service en nuage a l' adresse IP **10.10.0.4**qui est l’adresse IP du serveur contrôleur de domaine. Ce paramètre est nécessaire pour activer les nouveaux ordinateurs virtuels dans le service cloud joindre au domaine Active Directory avec succès. Sans ce paramètre, vous devez définir manuellement les paramètres IPv4 dans votre machine virtuelle pour utiliser le serveur de contrôleur de domaine comme serveur DNS principal après la mise en service de la machine virtuelle et puis joindre l’ordinateur virtuel au domaine Active Directory.

1. Exécutez les commandes suivantes redirigés pour créer les ordinateurs SQL Server virtuels, nommés **ContosoSQL1** et **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Notez les informations suivantes concernant les commandes ci-dessus :

    - **Nouveau-AzureVMConfig** utilise le même nom de jeu de disponibilité que le serveur de contrôleur de domaine et utilise l’image de l’édition entreprise de SQL Server 2012 Service Pack 1 dans la galerie de la machine virtuelle. Il définit également le disque du système d’exploitation la mise en cache en lecture uniquement (aucun cache d’écriture). Il est recommandé de migrer les fichiers de base de données sur un disque de données que vous attachez à la machine virtuelle et configurez avec aucune lecture ou le cache d’écriture. Toutefois, la meilleure chose suivante est à supprimer du cache d’écriture sur le disque du système d’exploitation, utilisez dans la mesure où vous ne pouvez pas supprimer la mise en cache sur le disque du système d’exploitation.

    - **Ajouter-AzureProvisioningConfig** joint la machine virtuelle pour le domaine Active Directory que vous avez créé.

    - **Ensemble-AzureSubnet** place la machine virtuelle dans le sous-réseau précédent.

    - **Ajouter-AzureEndpoint** ajoute des points de terminaison de l’accès afin que les applications clientes peuvent accéder à ces instances de services de SQL Server sur internet. ContosoSQL1 et ContosoSQL2 sont indiquées par des ports différents.

    - **Nouveau-AzureVM** crée la nouvelle machine virtuelle de de SQL Server dans le même service de cloud en tant que ContosoQuorum. Si vous souhaitez qu’ils soient dans le même jeu de disponibilité, vous devez placer les ordinateurs virtuels dans le même service de cloud.

1. Attendez que chaque machine virtuelle être entièrement mis en service et télécharger son fichier Bureau à distance à votre répertoire de travail. La boucle parcourt les trois nouvelles machines virtuelles, et exécute les commandes comprises dans les accolades de niveau supérieur pour chacun d’eux.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Les ordinateurs virtuels de SQL Server sont maintenant configurés et en cours d’exécution, mais ils sont installés avec SQL Server avec les options par défaut.

## <a name="initialize-the-wsfc-cluster-vms"></a>Initialiser le VMs de Cluster WSFC

Dans cette section, vous devez modifier les trois serveurs à qu'utiliser dans le cluster WSFC et de l’installation de SQL Server. En particulier :

- (Tous les serveurs) Vous devez installer la fonctionnalité **Clustering avec basculement** .

- (Tous les serveurs) Vous devez ajouter **CORP\Install** comme l' **administrateur**de la machine.

- (ContosoSQL1 et ContosoSQL2 uniquement) Vous devez ajouter **CORP\Install** comme un rôle **sysadmin** dans la base de données par défaut.

- (ContosoSQL1 et ContosoSQL2 uniquement) Vous devez ajouter **NT AUTHORITY\System** comme une ouverture de session avec les autorisations suivantes :

    - Modifier un groupe de disponibilité

    - Connexion SQL

    - Affichage État du serveur

- (ContosoSQL1 et ContosoSQL2 uniquement) Le protocole **TCP** est déjà activé sur la machine virtuelle de SQL Server. Toutefois, vous devrez ouvrir le pare-feu pour l’accès distant de SQL Server.

Maintenant, vous êtes prêt à démarrer. À partir de **ContosoQuorum**, procédez comme suit :

1. Se connecter à **ContosoQuorum** en lançant des histoires de bureau à distance. Utilisez le nom d’utilisateur **AzureAdmin** et le mot de passe de l’administrateur de la machine **Contoso ! 000**, que vous avez spécifiée lors de la création d’ordinateurs virtuels.

1. Vérifiez que les ordinateurs ont été correctement joint pour **corp.contoso.com**.

1. Attendez que l’installation de SQL Server terminer l’exécution des tâches d’initialisation automatique avant de continuer.

1. Ouvrez une fenêtre PowerShell en mode administrateur.

1. Installer la fonctionnalité Clustering de basculement Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Ajouter des **CORP\Install** en tant qu’administrateur local.

        net localgroup administrators "CORP\Install" /Add

1. Se déconnecter de ContosoQuorum. Vous avez maintenant terminé avec ce serveur.

        logoff.exe

Initialisez ensuite **ContosoSQL1** et **ContosoSQL2**. Suivez les étapes ci-dessous, qui sont identiques pour les deux ordinateurs virtuels SQL Server.

1. Se connecter aux deux ordinateurs SQL Server virtuels en exécutant les fichiers du Bureau à distance. Utilisez le nom d’utilisateur **AzureAdmin** et le mot de passe de l’administrateur de la machine **Contoso ! 000**, que vous avez spécifiée lors de la création d’ordinateurs virtuels.

1. Vérifiez que les ordinateurs ont été correctement joint pour **corp.contoso.com**.

1. Attendez que l’installation de SQL Server terminer l’exécution des tâches d’initialisation automatique avant de continuer.

1. Ouvrez une fenêtre PowerShell en mode administrateur.

1. Installer la fonctionnalité Clustering de basculement Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Ajouter des **CORP\Install** en tant qu’administrateur local

        net localgroup administrators "CORP\Install" /Add

1. Importer le fournisseur PowerShell de SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking

1. Ajouter des **CORP\Install** en tant que rôle sysadmin pour l’instance de SQL Server par défaut.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. Ajouter **NT AUTHORITY\System** comme une connexion d’accès avec les trois autorisations décrites ci-dessus.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. Ouvrir le pare-feu pour l’accès distant de SQL Server.

        netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. Ouvrez une session sur les deux ordinateurs virtuels.

        logoff.exe

Enfin, vous êtes prêt à configurer le groupe de disponibilité. Vous utiliserez le fournisseur PowerShell de SQL Server pour effectuer tout le travail sur **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurer le groupe de disponibilité

1. Se connecter à **ContosoSQL1** à nouveau lancement des histoires de bureau à distance. Au lieu de se connecter en utilisant le compte d’ordinateur, connectez-vous à l’aide de **CORP\Install**.

1. Ouvrez une fenêtre PowerShell en mode administrateur.

1. Définir les variables suivantes :

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"

1. Importer le fournisseur PowerShell de SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking

1. Modifier le compte de service SQL Server pour ContosoSQL1 à CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Modifier le compte de service SQL Server pour ContosoSQL2 à CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Téléchargez **CreateAzureFailoverCluster.ps1** à partir de [Créer le Cluster WSFC pour toujours sur les groupes de disponibilité dans Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) vers le répertoire de travail local. Ce script vous permet de vous aider à créer un cluster WSFC fonctionnel. Pour plus d’informations sur l’interaction de WSFC avec le réseau d’Azure, reportez-vous à la section [haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

1. Placez-vous dans le répertoire de travail et créer le cluster WSFC avec le script téléchargé.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. Activez toujours sur les groupes de disponibilité pour les instances de SQL Server par défaut sur **ContosoSQL1** et **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Créer un répertoire de sauvegarde et accorder des autorisations pour les comptes de service SQL Server. Ce répertoire vous permet de préparer la base de données de disponibilité sur le réplica secondaire.

        $backup = "C:\backup"
        New-Item $backup -ItemType directory
        net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
        icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. Créer une base de données sur **ContosoSQL1** appelé **MyDB1**, prendre à la fois une sauvegarde complète et une sauvegarde du journal et les restaurer sur **ContosoSQL2** avec l’option **WITH NORECOVERY** .

        Invoke-SqlCmd -Query "CREATE database $db"
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. Créer la disponibilité des points de terminaison de groupe sur les ordinateurs virtuels de SQL Server et définir les autorisations appropriées sur les points de terminaison.

        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server1\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"
        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server2\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"

        Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
        Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2

1. Créer les répliques de disponibilité.

        $primaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server1 `
            -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate
        $secondaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server2 `
            -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate

1. Enfin, créez le groupe de disponibilité et joindre le réplica secondaire pour le groupe de disponibilité.

        New-SqlAvailabilityGroup `
            -Name $ag `
            -Path "SQLSERVER:\SQL\$server1\Default" `
            -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
            -Database $db
        Join-SqlAvailabilityGroup `
            -Path "SQLSERVER:\SQL\$server2\Default" `
            -Name $ag
        Add-SqlAvailabilityDatabase `
            -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
            -Database $db

## <a name="next-steps"></a>Étapes suivantes
Vous avez maintenant correctement implémenté SQL Server Always On en créant un groupe de disponibilité dans Azure. Pour configurer un port d’écoute pour ce groupe de disponibilité, consultez [configurer un écouteur ILB pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Pour plus d’informations sur l’utilisation de SQL Server dans Azure, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
