<properties
   pageTitle="Configurez toujours sur le groupe de disponibilité écouteurs – Microsoft Azure"
   description="Configurer les écouteurs de groupe de disponibilité sur le modèle de gestionnaire de ressources d’Azure, à l’aide d’un équilibreur de charge interne avec une ou plusieurs adresses IP."
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="10/20/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurer un ou plusieurs toujours sur Disponibilité groupe de ports d’écoute - Gestionnaire de ressources 

Cette rubrique montre comment effectuer les deux opérations :

- Créer un équilibreur de charge interne pour les groupes de disponibilité de SQL Server à l’aide des applets de commande PowerShell.

- Ajouter des adresses IP supplémentaires à prendre en charge plus d’un groupe de disponibilité de SQL Server, un équilibreur de charge. 

Dans SQL Server un écouteur de groupe de disponibilité est un nom de réseau virtuel que les clients se connectent à afin d’accéder à une base de données dans le réplica principal ou secondaire. Sur les machines virtuelles Azure, un équilibreur de charge conserve l’adresse IP pour l’écouteur. L’équilibreur de charge dirige le trafic vers l’instance de SQL Server qui est à l’écoute sur le port de la sonde. Dans la plupart des cas, un groupe de disponibilité utilise un équilibreur de charge interne. Un équilibreur de charge d’interne Azure peut héberger une ou plusieurs adresses IP. Chaque adresse IP utilise un port de sonde spécifique. Ce document indique comment utiliser PowerShell pour créer un nouveau équilibreur de charge, ou ajouter des adresses IP à un équilibreur de charge existant pour les groupes de disponibilité de SQL Server. 

Permet d’attribuer plusieurs adresses IP à un équilibreur de charge interne est nouveau dans Azure et est uniquement disponible dans le modèle de gestionnaire de ressources. Pour effectuer cette tâche, vous devez disposer d’un groupe de disponibilité de SQL Server déployé sur Azure ordinateurs virtuels dans le Gestionnaire de ressources du modèle. Les deux machines virtuelles de SQL Server doivent appartenir au même jeu de disponibilité. Vous pouvez utiliser le [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) crée automatiquement le groupe de disponibilité dans le Gestionnaire de ressources Azure. Ce modèle crée automatiquement le groupe de disponibilité, y compris l’équilibreur de charge interne pour vous. Si vous préférez, vous pouvez [configurer manuellement un groupe de disponibilité AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Cette rubrique nécessite que vos groupes de disponibilité sont déjà configurés.  

Rubriques connexes :

- [Configurer des groupes de disponibilité AlwaysOn dans Azure VM (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   

- [Configurer une connexion VNet à VNet en utilisant le Gestionnaire de ressources Azure et PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Configurer le pare-feu Windows

Configurer le pare-feu Windows pour autoriser l’accès de SQL Server. Vous devez configurer le pare-feu pour autoriser les connexions TCP à l’utilisation de ports par l’instance de SQL Server, ainsi que le port utilisé par la sonde de l’écouteur. Pour des instructions détaillées, consultez [configurer un pare-feu Windows pour l’accès au moteur de base de données](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Créer une règle de trafic entrant pour le port SQL Server et pour le port de la sonde.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Exemple de Script : Créer un équilibreur de charge interne avec PowerShell

> [AZURE.NOTE] Si vous avez créé votre groupe de disponibilité avec le [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) la charge, vous n’avez pas besoin d’effectuer cette étape. 

Le script PowerShell suivant crée un équilibreur de charge interne, configure la règles d’équilibrage de charge et affecte une adresse IP pour l’équilibrage de charge. Pour exécuter le script, ouvrir Windows PowerShell ISE et collez le script dans la fenêtre de Script. Utilisez `Login-AzureRMAccount` pour la connexion à PowerShell. Si vous avez plusieurs abonnements d’Azure, utilisez `Select-AzureRmSubscription ` pour définir l’abonnement. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>Exemple de script : ajout d’une adresse IP à un équilibreur de charge existants avec PowerShell

Pour utiliser plus d’un groupe de disponibilité, utiliser PowerShell pour ajouter une adresse IP supplémentaire à un équilibreur de charge existant. Chaque adresse IP requiert ses propres règles, le port de sonde et port frontal d’équilibrage de la charge.

Le port frontal est le port que les applications utilisent pour se connecter à l’instance de SQL Server. Les adresses IP pour les groupes de disponibilité différents peuvent utiliser le même port de front-end.

>[AZURE.NOTE] Pour les groupes de disponibilité de SQL Server, chaque adresse IP nécessite un port de sonde spécifique. Par exemple, si une adresse IP sur un équilibreur de charge utilise le port de sonde 59999, aucuns autres adresses IP sur l’équilibrage de la charge ne peuvent utiliser port de sonde 59999. 

- Pour plus d’informations sur l’équilibrage de la charge limite voir **privé avant fin IP par l’équilibreur de charge** sous [Limites de mise en réseau - Azure le Gestionnaire de ressources](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

- Pour plus d’informations sur la disponibilité des limites de groupe voir [Restrictions (groupes de disponibilité)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Le script suivant ajoute une nouvelle adresse IP à un équilibreur de charge existant. Mettre à jour les variables de votre environnement. L’ILB utilise le port d’écoute du port front-end équilibrage de charge. Ce port peut être que SQL Server est à l’écoute sur le port. Pour les instances par défaut de SQL Server, c’est le port 1433. La règle pour un groupe de disponibilité d’équilibrage de charge nécessite un IP flottante (retour de serveur direct) pour le port back-end est le même que le port front-end.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurer le cluster pour utiliser l’adresse IP d’équilibrage de la charge 

L’étape suivante consiste à configurer le port d’écoute sur le cluster et l’écouteur en ligne. Pour ce faire, effectuez les opérations suivantes : 

1. Créer l’écouteur de groupe de disponibilité sur le cluster de basculement  

2. Mettre le récepteur en ligne

## <a name="1-create-the-availability-group-listener-on-the-failover-cluster"></a>1. création de l’écouteur de groupe de disponibilité sur le cluster de basculement

Dans cette étape, vous ajoutez un point d’accès client au gestionnaire du Cluster de basculement avec cluster de basculement et ensuite utilisez PowerShell pour configurer la ressource de cluster pour écouter sur le port de la sonde. 

1. Utiliser le protocole RDP pour se connecter à l’ordinateur virtuel Azure qui héberge le réplica principal. 

2. Ouvrez le Gestionnaire du Cluster de basculement.

3. Sélectionnez le nœud **réseaux** et notez le nom de réseau du cluster. Utiliser ce nom dans la `$ClusterNetworkName` variable dans le script PowerShell.

4. Développez le nom du cluster, puis cliquez sur **rôles**.

5. Dans le volet de **rôles** , cliquez sur le nom de groupe de disponibilité, puis sélectionnez **Ajouter une ressource** > **Point d’accès Client**.

6. Dans la zone **nom** , attribuez un nom à ce nouveau port d’écoute, puis cliquez deux fois sur **suivant** puis cliquez sur **Terminer**. Ne mettez pas le port d’écoute ou de la ressource en ligne à ce stade.
 
    Le nom pour le nouveau port d’écoute est le nom de réseau que les applications utiliseront pour se connecter aux bases de données dans le groupe de disponibilité de SQL Server.

7. Cliquez sur l’onglet **ressources** , puis développez le Point d’accès Client vous venez de créer. Avec le bouton droit de la ressource IP et cliquez sur Propriétés. Notez le nom de l’adresse IP. Vous utiliserez ce nom dans la `$IPResourceName` variable dans le script PowerShell.

8. Sous **Adresse IP** , cliquez sur **Adresse IP statique** et définir l’adresse IP statique à la même adresse que vous avez utilisé lorsque vous définissez l’adresse IP d’équilibrage de la charge sur le portail Azure. 

9. Désactiver NetBIOS pour cette adresse et cliquez sur **OK**. Répétez cette étape pour chaque ressource IP si votre solution couvre plusieurs VNets d’Azure. 

10. Vérifiez les ressources du groupe de disponibilité de SQL Server dépendant de l’adresse IP. Clic droit sur la ressource dans le Gestionnaire de cluster, il s’agit sur l’onglet **ressources** dans **d’Autres ressources**. 

11. Sur le nœud de cluster qui héberge actuellement le réplica principal, ouvrez une PowerShell ISE élevés et collez les commandes suivantes dans un nouveau script. Dans l’onglet **dépendances** , cliquez sur le nom de l’écouteur.
 
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
 
6. Mettre à jour les variables et exécuter le script PowerShell pour configurer l’adresse IP et le port pour le nouveau port d’écoute.

 >[AZURE.NOTE] Si vos serveurs SQL se trouvent dans des zones distinctes, vous devez exécuter le script PowerShell deux fois. La première fois utiliser le nom réseau du cluster, nom de la ressource cluster IP et charger l’adresseIP d’équilibrage du premier groupe de ressources. La deuxième fois utiliser le nom réseau du cluster, nom de la ressource cluster IP et charger l’adresseIP d’équilibrage du deuxième groupe de ressources.

Désormais, le cluster a une ressource de port d’écoute de groupe de disponibilité.

## <a name="2-bring-the-listener-online"></a>2. Mettez l’écouteur en ligne

Avec la ressource de port d’écoute disponibilité groupe configurée, vous pouvez mettre l’écouteur en ligne afin que les applications peuvent se connecter aux bases de données dans le groupe de disponibilité avec le port d’écoute.

1. Naviguer vers le Gestionnaire des clusters de basculement. Développez **rôles** et mettez en surbrillance le groupe de votre disponibilité. Sous l’onglet **ressources** , cliquez sur le nom de l’écouteur et cliquez sur **Propriétés**.

1. Cliquez sur l’onglet **dépendances** . S’il existe plusieurs ressources répertoriées, vérifiez que les adresses IP ou non et dépendances. Cliquez sur **OK**.

1. Double-cliquez sur le nom du port d’écoute, puis cliquez sur **Mettre en ligne**.

1. Une fois que l’écouteur est en ligne, à partir de l’onglet **ressources** , cliquez sur le groupe de disponibilité, puis cliquez sur **Propriétés**.

1. Créer une dépendance sur la ressource de nom de port d’écoute (pas le IP adresse nom de ressources). Cliquez sur **OK**.

1. Lancement de SQL Server Management Studio et connectez-vous au réplica principal.

1. Atteindre **AlwaysOn haute disponibilité** | **groupes de disponibilité** | **groupe de disponibilité écouteurs**. 

1. Vous devez maintenant voir le nom de l’écouteur que vous avez créé dans le Gestionnaire de Cluster de basculement. Double-cliquez sur le nom du port d’écoute, puis cliquez sur **Propriétés**.

1. Dans la zone **Port** , spécifiez le numéro de port pour l’écouteur de groupe de disponibilité à l’aide de la $EndpointPort utilisée précédemment (1433 était la valeur par défaut), puis cliquez sur **OK**.

Vous disposez maintenant d’un groupe de disponibilité de SQL Server dans Azure des machines virtuelles en cours d’exécution en mode de Gestionnaire des ressources. 

## <a name="test-the-connection-to-the-listener"></a>Tester la connexion pour le port d’écoute

Pour tester la connexion :

1. RDP à un SQL Server qui se trouve dans le même réseau virtuel, mais qui ne possède pas de réplica. Cela peut être l’autre de SQL Server dans le cluster.

2. Utilisez l’utilitaire **sqlcmd** pour tester la connexion. Par exemple, le script suivant établit une connexion **sqlcmd** pour le réplica principal par l’intermédiaire de l’écouteur avec l’authentification Windows :

    ```
    sqlmd -S <listenerName> -E
    ```

    Si l’écouteur utilise un port autre que la valeur par défaut le port (1433), indiquez le port dans la chaîne de connexion. Par exemple, la commande sqlcmd suivante se connecte à un port d’écoute sur le port 1435 : 
    
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

La connexion SQLCMD se connecte automatiquement à quelle instance de SQL Server héberge le réplica principal. 

>[AZURE.NOTE] Assurez-vous que le port spécifié est ouvert sur le pare-feu des deux serveurs SQL. Les deux serveurs requièrent une règle de trafic entrant pour le port TCP que vous utilisez. Pour plus d’informations, voir [Ajouter ou modifier une règle de pare-feu](http://technet.microsoft.com/library/cc753558.aspx) . 

## <a name="guidelines-and-limitations"></a>Instructions et limitations

Notez que les instructions suivantes sur port d’écoute disponibilité groupe dans Azure à l’aide interne l’équilibreur de charge :

- Avec un équilibreur de charge interne vous permet uniquement d’accéder l’écouteur au sein du même réseau virtuel.

## <a name="for-more-information"></a>Pour plus d’informations

Pour plus d’informations, voir [configurer toujours sur la disponibilité de groupe dans Azure VM manuellement](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### <a name="powershell-cmdlets"></a>Applets de commande PowerShell

Les applets de commande PowerShell suivante permet de créer un équilibreur de charge interne pour les machines virtuelles Azure.

- `New-AzureRmLoadBalancer`Crée un équilibreur de charge. Pour plus d’informations, reportez-vous à la section [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) . 

- `New-AzureRMLoadBalancerFrontendIpConfig`Crée une configuration IP front-end pour un équilibreur de charge. Pour plus d’informations, reportez-vous à la section [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) .

- `New-AzureRmLoadBalancerRuleConfig`Crée une configuration de la règle pour un équilibreur de charge. Pour plus d’informations, reportez-vous à la section [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) . 

- `New-AzureRMLoadBalancerBackendAddressPoolConfig`Crée une configuration de pool d’adresse back-end pour un équilibreur de charge. Pour plus d’informations, reportez-vous à la section [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) . 

- `New-AzureRmLoadBalancerProbeConfig`Crée une configuration de sonde pour un équilibreur de charge. Pour plus d’informations, reportez-vous à la section [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) .

Si vous devez supprimer un équilibreur de charge à partir d’un groupe de ressources d’Azure, utilisez `Remove-AzureRmLoadBalancer`. Pour plus d’informations, consultez [Supprimer-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).
