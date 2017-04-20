<properties 
    pageTitle="Protection de SQL Server avec SQL Server après sinistre et de récupération de Site Azure | Microsoft Azure" 
    description="Cet article décrit la procédure de réplication de SQL Server à l’aide des fonctionnalités de reprise sur sinistre Azure Site récupération de SQL Server." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.workload="backup-recovery" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="raynew"/>


# <a name="protect-sql-server-with-sql-server-disaster-recovery-and-azure-site-recovery"></a>Protection de SQL Server avec SQL Server après sinistre et de récupération de Site Azure 


Le service de récupération de Site Azure contribue à votre stratégie de récupération (BCDR) de continuité d’activité et de reprise après sinistre de l’entreprise par l’orchestration de réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire sur site. Pour une vue d’ensemble rapide de lecture [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md).

 Cet article décrit comment protéger le SQL Server back-end d’une application à l’aide d’une combinaison de technologies de BCDR de SQL Server et de la récupération de Site Azure. Vous devez disposer d’une bonne compréhension de la reprise après sinistre de SQL Server (base de données de basculement de cluster, les groupes de disponibilité AlwaysOn, la mise en miroir, envoi de journaux) et de la récupération de Site Azure, avant de déployer les scénarios décrits dans cet article.



## <a name="overview"></a>Vue d’ensemble

De nombreuses charges de travail utilisent SQL Server comme base. Des applications telles que SAP, Dynamics et SharePoint permet de mettre en œuvre des services de données de SQL Server.  Applications pour déployer SQL Server de plusieurs façons différentes :

- **Autonome SQL Server**: SQL Server et les bases de données sont hébergées sur un même ordinateur (physique ou virtuel). Lorsque virtualisé, mise en clusters hôte est utilisé pour la haute disponibilité locale. Aucun au niveau des invités de haute disponibilité n’est implémentée.
- **Instances de clusters SQL Server avec basculement (toujours sur FCI)**: deux ou plusieurs nœuds d’instances de serveur SQL avec les disques partagés sont configuré dans un cluster de basculement Windows. Si toutes les instances de cluster est arrêté, le cluster peut basculer de SQL Server à une autre instance. Ce paramétrage est généralement utilisé pour la haute disponibilité sur un site principal. Il ne protège pas contre la défaillance ou une panne dans la couche de stockage partagé. Disque partagé peut être implémentée à l’aide d’ISCSI, Fibre channel ou partagée de VHDx.
- **SQL toujours sur les groupes de disponibilité**: dans cette configuration, les deux nœuds sont le programme d’installation dans un fichier partagé rien du cluster avec les bases de données SQL Server configurés dans un groupe de disponibilité avec la réplication synchrone et basculement automatique.

Dans les éditions Enterprise, SQL Server fournit également après sinistre natif technologies de récupération pour récupérer des bases de données vers un site distant. Dans cet article, nous allons exploiter et intégrer ces technologies de récupération après incident SQL natifs : 

- SQL toujours sur les groupes de disponibilité pour la récupération d’urgence pour SQL Server 2012 ou 2014 Enterprise Edition.
- SQL mise en miroir de base de données en mode de sécurité élevée pour l’Édition Standard de SQL Server (toute version) ou SQL Server 2008 R2.


Récupération de site peut protéger SQL Server comme décrit dans le tableau.

 |**Local à local** | **Sur le site vers Azure** 
---|---|---
**Hyper-V** | Oui | Oui
**VMware** | Oui | Oui 
**Serveur physique** | Oui | Oui


## <a name="support-and-integration"></a>Prise en charge et intégration

Ces versions de SQL Server sont pris en charge par les scénarios proposés dans cet article :


- Standard et Enterprise de 2014 de SQL Server
- Standard et SQL Server 2012 Enterprise
- Standard et Enterprise de SQL Server 2008 R2


Récupération de site peut être intégrée aux technologies natives de SQL Server BCDR résumées dans le tableau ci-dessous pour fournir une solution de reprise après sinistre.

**Fonctionnalité** |**Détails** | **Version de SQL Server** 
---|---|---
**Groupe de disponibilité AlwaysOn** | Plusieurs instances autonomes de SQL Server à chaque exécutent d’un cluster de basculement qui a plusieurs nœuds.<br/><br/>Bases de données peuvent être regroupés en groupes de basculement qui peuvent être copiés (en miroir) sur des instances de SQL Server afin qu’aucun stockage partagé n’est nécessaire.<br/><br/>Assure la restauration après sinistre entre un site principal et un ou plusieurs sites secondaires. Deux nœuds permet de configurer dans un partagé rien cluster avec les bases de données SQL Server est configuré dans un groupe de disponibilité avec la réplication synchrone et basculement automatique. | SQL Server 2014 & 2012 Enterprise edition
**Basculement de cluster (AlwaysOn FCI)** | SQL Server s’appuie sur Windows clusters de basculement pour une disponibilité élevée des charges de travail sur site de SQL Server.<br/><br/>Nœuds exécutant des instances de SQL Server avec les disques partagés sont configurés dans un cluster de basculement. Si une instance est en panne le cluster bascule vers un autre.<br/><br/>Ne protège pas contre les défaillances ou pannes le stockage partagé dans le cluster. Le disque partagé peut être implémenté avec iSCSI, Fibre channel, ou partagé VHDXs. | Éditions entreprise de SQL Server<br/><br/>SQL Server Standard edition (limitée à deux nœuds uniquement)
**Base de données mise en miroir de (mode haute sécurité)** | Protège une seule base de données à une seule copie secondaire. Disponible dans les deux haute sécurité (synchrone) et les modes de réplication (asynchrone) hautes performances. Ne nécessite pas un cluster de basculement. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise toutes les éditions
**Autonome SQL Server** | Le SQL Server et la base de données sont hébergées sur un serveur unique (physique ou virtuel). Mise en clusters hôte est utilisé pour une haute disponibilité si le serveur est virtuel. Pas au niveau des invités de haute disponibilité. | Édition Enterprise ou Standard

## <a name="deployment-recommendations"></a>Recommandations de déploiement


Ce tableau résume nos recommandations pour l’intégration des technologies de BCDR de SQL Server avec la récupération de Site.

**Version** |**Édition** | **Déploiement** | **Prem à sur prem** | **Prem vers Azure** 
---|---|---|---|---
SQL Server 2014 ou 2012 | Entreprise | Instance de cluster avec basculement | Groupes de disponibilité AlwaysOn | Groupes de disponibilité AlwaysOn
 | Entreprise | Groupes de disponibilité AlwaysOn pour une haute disponibilité | Groupes de disponibilité AlwaysOn | Groupes de disponibilité AlwaysOn
 | Standard | Instance de cluster de basculement (FCI) | Réplication de récupération de site avec un miroir local | Réplication de récupération de site avec un miroir local
 | Enterprise ou Standard | Autonome | Réplication de récupération de site | Réplication de récupération de site
SQL Server 2008 R2 | Enterprise ou Standard | Instance de cluster de basculement (FCI) | Réplication de récupération de site avec un miroir local | Réplication de récupération de site avec un miroir local
 | Enterprise ou Standard | Autonome | Réplication de récupération de site | Réplication de récupération de site
SQL Server (toute version) | Enterprise ou Standard | Instance de cluster avec basculement - application de DTC | Réplication de récupération de site | Non pris en charge


## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement

Voici ce que vous devez pouvoir démarrer :

- Un déploiement de SQL Server sur site exécute une version de SQL Server prises en charge. En général vous devez également Active Directory pour votre serveur SQL.
- Les conditions préalables pour le scénario que vous souhaitez déployer. Vous trouverez les conditions préalables dans chaque article du déploiement. Des liens vers ces sont fournis dans la [Vue d’ensemble de récupération de Site](site-recovery-overview.md).
- Si vous souhaitez configurer la récupération dans Azure, vous devez exécuter l’outil [Readiness Assessment de la Machine virtuelle Azure](http://www.microsoft.com/download/details.aspx?id=40898) sur vos machines virtuelles de SQL Server pour vous assurer qu’elles sont compatibles avec Azure et récupération de Site.


## <a name="set-up-active-directory"></a>Installer Active Directory

Vous aurez besoin d’Active Directory sur le site de récupération secondaire pour SQL Server pour s’exécuter correctement. Il existe deux options :

- **Petite entreprise**— si vous avez un petit nombre d’applications et un seul contrôleur de domaine du site local et que vous souhaitez basculer l’ensemble du site, nous vous conseillons repication de récupération de Site pour répliquer le contrôleur de domaine pour le centre de données secondaire ou Azure.

- **Moyennes et grandes entreprises**: Si vous avez un grand nombre d’applications, vous exécutez une forêt Active Directory et que vous voulez basculer en application ou de la charge de travail, nous vous recommandons de configurer un contrôleur de domaine supplémentaire dans le centre de données secondaire ou dans Azure. Notez que si vous utilisez des groupes de disponibilité AlwaysOn à récupérer sur un site distant nous vous recommandons de que configurer un autre contrôleur de domaine supplémentaire sur le site secondaire ou l’Azure, à utiliser pour l’instance de SQL Server récupéré.

Les instructions de ce document supposent qu’un contrôleur de domaine est disponible dans l’emplacement secondaire. [Pour en savoir plus](site-recovery-active-directory.md) sur la protection de Active Directory avec récupération de Site.

## <a name="integrate-protection-with-sql-server-always-on-on-premises-to-azure"></a>Intégrer la protection avec la sacoche de SQL Server (local vers Azure)


Récupération de site prend en charge en mode natif les AlwaysOn de SQL. Si vous avez créé un groupe de disponibilité de SQL avec une machine virtuelle Azure configuré comme « Secondaire », vous pouvez utiliser la restauration du Site pour gérer le basculement des groupes de disponibilité. 

>[AZURE.NOTE] Cette fonctionnalité est actuellement en mode Aperçu et disponible lorsque les serveurs hôtes Hyper-V dans le centre de données principal sont gérés dans les nuages VMM et lorsque le programme d’installation de VMware est géré par un [Serveur de Configuration](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Pour le moment, cette fonctionnalité n’est pas disponible dans le nouveau portail Azure.

#### <a name="prerequisites"></a>Conditions préalables

Voici ce que vous devez intégrer AlwaysOn de SQL avec la récupération de Site :

- Un SQL Server local (serveur autonome ou un cluster de basculement).
- Une ou plusieurs machines virtuelles Azure avec SQL Server installé
- Un groupe de disponibilité de SQL définie entre un site sur SQL Server et SQL Server en cours d’exécution dans Azure
- PowerShell distant doit être activé sur l’ordinateur SQL Server local. Le serveur VMM ou le serveur de Configuration doit être en mesure d’effectuer des appels distants de PowerShell pour le SQL Server.
- Un compte d’utilisateur doit être ajouté sur le SQL Server local, dans ces groupes d’utilisateurs SQL au moins les autorisations :
    - MODIFIER le groupe de disponibilité : autorisations [ici](https://msdn.microsoft.com/library/hh231018.aspx)et [ici](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - ALTER DATABASE - autorisations[ici](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- Un compte RunAs doit être créé sur le serveur VMM ou un compte doit être créé sur le serveur de Configuration à l’aide de la CSPSConfigtool.exe pour l’utilisateur mentionné à l’étape précédente 
- Le module SQL PS doit être installé sur les serveurs SQL locaux et sur les machines virtuelles Azure
- L’Agent de la machine virtuelle doit être installé de machines virtuelles en cours d’exécution sur Azure
- NTAUTHORITY\System doit avoir suivant les autorisations dans SQL Server s’exécutant sur des machines virtuelles dans Azure :
    - MODIFIER le groupe de disponibilité - autorisations [ici](https://msdn.microsoft.com/library/hh231018.aspx)et [ici](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - ALTER DATABASE - autorisations [ici](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  <a name="step-1-add-a-sql-server"></a>Étape 1 : Ajouter un SQL Server


1. Cliquez sur **Ajouter de SQL** pour ajouter une nouvelle de SQL Server. 

    ![Ajouter SQL](./media/site-recovery-sql/add-sql.png)

2. **Configurer**les paramètres de SQL > **nom de** fournir un nom convivial pour faire référence à la SQL Server.
3. **Dans SQL Server (nom de domaine complet)** permet de spécifier le nom de domaine complet de la source de SQL Server que vous souhaitez ajouter. Dans le cas où le SQL Server est installé sur un Cluster de basculement, puis indiquez le nom de domaine complet du cluster et non d’un des nœuds du cluster.  
4. Dans **l’Instance de SQL Server** , sélectionnez l’instance par défaut ou fournir le nom de l’instance personnalisée.
5. Dans **Gestion de serveur** , sélectionnez un serveur de Configuration enregistrés dans le coffre-fort de la récupération de Site ou le serveur VMM. Récupération de site utilise ce serveur d’administration pour communiquer avec le SQL Server.
6. Dans la zone **Exécuter en tant que compte de** fournir le nom d’un compte de RunAs qui a été créé sur le serveur VMM spécifié ou le compte qui a été créé sur le serveur Configuraaaon. Ce compte est utilisé pour accéder à la SQL Server et doit disposer d’autorisations en lecture et le basculement des groupes de disponibilité sur la machine SQL Server.

    ![Ajouter la boîte de dialogue SQL](./media/site-recovery-sql/add-sql-dialog.png)

Après avoir ajouté le SQL Server, il apparaîtra dans l’onglet **Serveurs de SQL** . 

![Liste de SQL Server](./media/site-recovery-sql/sql-server-list.png)


#### <a name="step-2-add-a-sql-availability-group"></a>Étape 2 : Ajouter un groupe de disponibilité de SQL

1. Après le SQL Server machine est ajouté à que l’étape suivante consiste à ajouter les groupes de disponibilité pour la récupération de Site. Pour ce faire, un zoom avant dans le SQL Server ajoutée à l’étape précédente, puis cliquez sur Ajouter un groupe de disponibilité de SQL. 

    ![Ajouter SQL AG](./media/site-recovery-sql/add-sqlag.png)

2. Groupe de disponibilité de SQL peut répliquer une ou plusieurs machines virtuelles dans Azure. Lorsque vous ajoutez le groupe de disponibilité de sql, vous devrez fournir le nom et l’abonnement de la machine virtuelle Azure où vous souhaitez que le groupe disponibilité basculé vers lors de la récupération de Site.

    ![Ajouter la boîte de dialogue SQL AG](./media/site-recovery-sql/add-sqlag-dialog.png)

3. Dans l’exemple ci-dessus deviendrait AG DB1 disponibilité groupe primaire sur en cours d’exécution à l’intérieur d’abonnement DevTesting2 sur un basculement de la machine virtuelle SQLAGVM2. 

>[AZURE.NOTE] Seuls les groupes de disponibilité sont la principale sur le SQL Server ajoutée à l’étape ci-dessus sont disponibles à ajouter à la récupération de Site. Si vous avez effectué un principal groupe de disponibilité sur le SQL Server ou si vous avez ajouté plusieurs groupes de disponibilité sur le SQL Server une fois qu’il a été ajouté, Actualiser sur le SQL Server à l’aide de l’option d’actualisation disponible.

#### <a name="step-3-create-a-recovery-plan"></a>Étape 3 : Créer un Plan de récupération

L’étape suivante consiste à créer un plan de récupération à l’aide de machines virtuelles et les groupes de disponibilité. Sélectionnez le serveur de VMM ou le serveur de Configuration que vous avez utilisé dans l’étape 1 comme source et Microsoft Azure comme cible.

![Créer le Plan de récupération](./media/site-recovery-sql/create-rp1.png)

![Créer le Plan de récupération](./media/site-recovery-sql/create-rp2.png)

Dans l’exemple de l’application Sharepoint se compose de 3 ordinateurs virtuels qui utilisent un groupe de disponibilité de SQL comme sa back-end. Dans ce plan de récupération que nous pouvons sélectionner à la fois le groupe de disponibilité ainsi que la machine virtuelle qui constituent l’application. 

Vous pouvez personnaliser davantage le plan de récupération en déplaçant des machines virtuelles à des groupes différents de basculement à la séquence de l’ordre de basculement. Le groupe de disponibilité est basculé toujours premier comme il doit être utilisé comme un serveur principal d’une application. 

![Personnaliser le Plan de récupération](./media/site-recovery-sql/customize-rp.png)

### <a name="step-4--fail-over"></a>Étape 4 : Basculement

Options de basculement différents sont disponibles une fois qu’un groupe de disponibilité a été ajouté à un Plan de récupération.

Basculement sur incident | Détails
--- | ---
**Basculement planifié** | Basculement planifié n’implique un aucun basculement de perte de données. Pour obtenir le Mode de disponibilité de ce groupe de disponibilité de SQL est tout d’abord la valeur synchrone et un basculement sur incident est déclenchée pour rendre le groupe de disponibilité principal sur l’ordinateur virtuel fourni lors de l’ajout du groupe de disponibilité pour la récupération de Site. Une fois le basculement terminé, le Mode de disponibilité a la même valeur telle qu’elle était avant le basculement planifié a été déclenchée.
**Basculement non planifié** | Basculement non planifié peut se traduire en perte de données. Lors du déclenchement du basculement non planifié, le mode de disponibilité du groupe de disponibilité n’est pas modifié et l’informatique devient le serveur primaire sur l’ordinateur virtuel fourni lors de l’ajout du groupe de disponibilité pour la récupération de Site. Une fois le basculement non planifié est terminée et que le serveur local exécutant SQL Server est de nouveau disponible, inverser la réplication doit être déclenchée sur le groupe de disponibilité. Notez que cette action n’est pas disponible sur le plan de récupération et peut être prise sur le groupe de disponibilité de SQL sous l’onglet serveurs de SQL
**Basculement de test** | Basculement de test pour le groupe de disponibilité de SQL n’est pas pris en charge. Si vous de déclencher le basculement des tests d’un Plan de récupération contenant le groupe de disponibilité de SQL, basculement serait ignorée pour le groupe de disponibilité.


Pensez à ces options de basculement.

Option | Détails
--- | ---
**Option 1** | 1. effectuez un test de basculement de l’application et les niveaux frontaux.<br/><br/>2. mise à jour de la couche application pour accéder à la copie du réplica en lecture seule et effectuez un test en lecture seule de l’application.
**Option 2** | 1. Créez une copie de l’instance de machine virtuelle réplica SQL Server (à l’aide de clone VMM de site à site ou Azure sauvegarde) et les porter dans un réseau de test<br/><br/> 2. effectuez le basculement de test en utilisant le plan de récupération.

Étape 5 : Restauration

Si vous souhaitez que le groupe de disponibilité nouveau principal sur le SQL Server local puis vous pouvez le faire par déclenchement prévu de basculement sur le Plan de récupération et de choisir la direction de Microsoft Azure au serveur VMM de locaux.

>[AZURE.NOTE] Après un basculement non planifié réplication inverse doit être déclenchée sur le groupe de disponibilité de reprendre la réplication. La réplication reste en pause jusqu'à ce que cela.



### <a name="protect-machines-without-a-vmm-server-or-a-configuration-server"></a>Protéger les ordinateurs sans un serveur VMM ou un serveur de Configuration

Pour les environnements qui ne sont pas gérés par un serveur VMM ou un serveur de Configuration, les procédures opérationnelles de Automation Azure peut servir pour configurer un basculement sur incident par script de groupes de disponibilité de SQL. Vous trouverez ci-dessous les étapes de configuration qui :

1.  Créer un fichier local pour le script de basculement d’un groupe de disponibilité. Cet exemple de script spécifie un chemin d’accès pour le groupe de disponibilité sur le réplica Azure et bascule à cette instance de réplica. Ce script va être exécuté sur le SQL Server machine virtuelle de réplica en passant est doté de l’extension de script personnalisé.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.  Télécharger le script à un objet blob dans un compte de stockage Azure. Utilisez cet exemple :

        $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.  Créer une procédure opérationnelle d’Azure automation pour appeler les scripts sur l’ordinateur de virtuel du réplica SQL Server dans Azure. Pour ce faire, utilisez cet exemple de script. [En savoir plus](site-recovery-runbook-automation.md) sur l’utilisation de procédures opérationnelles d’automatisation dans les plans de récupération. 

        workflow SQLAvailabilityGroupFailover
        {
            param (
                [Object]$RecoveryPlanContext
            )

            $Cred = Get-AutomationPSCredential -name 'AzureCredential'
    
            #Connect to Azure
            $AzureAccount = Add-AzureAccount -Credential $Cred
            $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
            Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
            InLineScript
            {
            #Update the script with name of your storage account, key and blob name
            $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
            $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
            Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
            if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                #Skipping TFO in this version.
                #We will update the script in a follow-up post with TFO support
                Write-output "tfo: Skipping SQL Failover";
                }
            else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.
       
                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
                Write-output "Completed AG Failover";

                }
        
            }
        }

4.  Lorsque vous créez un plan de récupération d’application Ajouter une étape de script « Pre-groupe 1 démarrage » qui appelle la procédure opérationnelle d’automation pour le basculement des groupes de disponibilité.

## <a name="integrate-protection-with-sql-alwayson-on-premises-to-on-premises"></a>Intégration de protection avec SQL AlwaysOn (local à local)

Si le SQL Server utilise des groupes de disponibilité pour une haute disponibilité, ou une instance de cluster de basculement, nous recommandons l’utilisation de groupes de disponibilité sur le site de récupération ainsi. Notez que ce guide est destiné aux applications qui n’utilisent pas les transactions distribuées.

1. [Bases de données de configuration](https://msdn.microsoft.com/library/hh213078.aspx) dans les groupes de disponibilité.
2. Créer un nouveau réseau virtuel sur le site secondaire.
3. Configurer un VPN de site à site entre le nouveau réseau virtuel et le site principal.
4. Créer un ordinateur virtuel sur le site de récupération et installer SQL Server.
5. Étendre les groupes de disponibilité AlwaysOn existants vers le nouvel ordinateur virtuel de SQL Server. Configuration de cette instance de SQL Server sous la forme d’une copie de duplication asynchrone.
6. Créer un écouteur de groupe de disponibilité ou mise à jour de l’écouteur existant afin d’inclure la machine virtuelle de duplication asynchrone.
7. Assurez-vous que la batterie est le programme d’installation à l’aide de l’écouteur. Si c’est le programme d’installation à l’aide du nom du serveur de base de données, veuillez mettre à jour pour utiliser l’écouteur afin que vous n’avez pas besoin de reconfigurer celui-ci après le basculement.

Pour les applications qui utilisent des transactions distribuées nous recommandation que vous utilisez la [Restauration du Site avec la réplication SAN](site-recovery-vmm-san.md) ou [réplication de site à site server VMWare/physique](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considérations sur la récupération plan

1. Ajoutez cet exemple de script à la bibliothèque VMM sur les sites principales et secondaires.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Lorsque vous créez un plan de récupération d’application Ajouter une étape de script « Pre-groupe 1 démarrage » qui appelle le script de basculement de groupes de disponibilité.



## <a name="protect-a-standalone-sql-server"></a>Protéger une autonome de SQL Server

Dans cette configuration, nous vous recommandons de que réplication de récupération de Site vous permet de protéger l’ordinateur SQL Server. Les étapes exactes à suivre varient si SQL Server est configuré en tant que machine virtuelle ou physique et si vous souhaitez répliquer vers Azure ou secondaire sur site site. Suivez les instructions pour tous les scénarios de déploiement dans la [Vue d’ensemble de récupération de Site](site-recovery-overview.md).


## <a name="protect-a-sql-server-cluster-standard-or-2008-r2"></a>Protéger un cluster de SQL Server (Standard ou 2008 R2)

Pour un cluster exécutant SQL Server Standard edition ou SQL Server 2008 R2, nous vous recommandons de que réplication de la récupération de Site vous permet de protéger SQL Server.

### <a name="on-premises-to-on-premises"></a>Local à local

- Si l’application utilise des transactions distribuées, nous vous recommandons de que déployer [Récupération de Site avec la réplication SAN](site-recovery-vmm-san.md) pour un environnement de Hyper-V et le [serveur VMware/physiques à VMware](site-recovery-vmware-to-vmware.md) pour environnement VMware.

- Pour les applications non-DTC, tirer parti de l’approche ci-dessus pour récupérer le cluster comme un serveur autonome en tirant parti d’une mise en miroir de base de données locale haute sécurité.

### <a name="on-premises-to-azure"></a>Sur le site vers Azure

Récupération de site ne prend pas en charge la prise en charge de cluster invités lors de la réplication vers Azure. SQL Server ne fournissent également une reprise après sinistre économique pour la Standard edition. Nous vous recommandons de vous protégez le cluster SQL Server sur site autonome de SQL Server et les restaurer dans Azure.


1. Configurer une instance de SQL Server autonome supplémentaire sur le site local.
2. Configurer cette instance comme une mise en miroir des bases de données nécessitant une protection. Configurer la mise en miroir en mode de sécurité élevée.
3.  Configurer la récupération de Site sur le site local en fonction de l’environnement ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [serveur VMware/physique](site-recovery-vmware-to-azure-classic.md).
4.  Réplication de récupération de Site permet de répliquer la nouvelle instance de SQL Server vers Azure. Il s’agit d’une copie miroir de niveau de sécurité élevé et donc il vous être synchronisé avec le cluster principal, mais il vous être répliqué sur Azure à l’aide de la réplication de la récupération de Site.

Le graphique suivant illustre cette configuration.

![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### <a name="failback-considerations"></a>Considérations relatives à la restauration automatique

Pour les clusters de SQL standard, restauration après un basculement non planifié nécessitent une sauvegarde SQL et restaurer à partir de l’instance en miroir pour le cluster d’origine et en rétablissant le miroir.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus](site-recovery-best-practices.md) sur l’obtention de prêt pour le déploiement de récupération de Site.










 