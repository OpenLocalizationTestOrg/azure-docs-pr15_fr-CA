Les étapes de cette tâche utilisent un VNet basée sur les valeurs ci-dessous. Les noms et les paramètres supplémentaires sont également présentées dans cette liste. Nous n’utilisons pas cette liste directement dans la procédure, bien que nous ajouter les variables basées sur les valeurs de cette liste. Vous pouvez copier la liste à utiliser en tant que référence, remplacer les valeurs par le vôtre.

Liste de référence de configuration :
    
- Nom de réseau virtuel = « TestVNet »
- Espace d’adressage de réseau virtuel = 192.168.0.0/16
- Groupe de ressource = « TestRG »
- Nom de Subnet1 = « Frontal » 
- Espace d’adressage Subnet1 = « 192.168.0.0/16 »
- Nom du sous-réseau passerelle : « GatewaySubnet », vous devez toujours nommer un sous-réseau de la passerelle *GatewaySubnet*.
- Espace d’adressage de sous-réseau de passerelle = « 192.168.200.0/26 »
- Région = « US orientale »
- Nom de la passerelle = « GW »
- Nom de la passerelle IP = « GWIP »
- Configuration IP de la passerelle nom = « gwipconf »
-  Type = « ExpressRoute » ce type est requis pour une configuration ExpressRoute.
- Nom de IP passerelle Public = « gwpip »


## <a name="add-a-gateway"></a>Ajouter une passerelle

1. Se connecter à votre abonnement Azure. 

        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. Déclarez vos variables pour cet exercice. Cet exemple utilise l’utilisation les variables dans l’exemple ci-dessous. Veillez à modifier pour refléter les paramètres que vous souhaitez utiliser. 
        
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"

3. Stocker l’objet réseau virtuel en tant que variable.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. Ajouter un sous-réseau de passerelle pour votre réseau virtuel. Le sous-réseau de passerelle doit être nommé « GatewaySubnet ». Vous voudrez créer une passerelle est /27 ou supérieur (/ 26/25, etc..).
            
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. Définir la configuration.

            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. Stocker le sous-réseau de passerelle en tant que variable.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. Demander une adresse IP publique. L’adresse IP est requise avant la création de la passerelle. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. elle est affectée dynamiquement. Vous allez utiliser cette adresse IP dans la section de configuration suivante. Le AllocationMethod doit être dynamique.

        $pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic

8. Créer la configuration de la passerelle. La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Dans cette étape, vous spécifiez la configuration qui sera utilisée lors de la création de la passerelle. Cette étape ne crée pas en fait l’objet de la passerelle. L’exemple ci-dessous permet de créer la configuration de votre passerelle. 

        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

9. Créez la passerelle. Dans cette étape, le **GatewayType -** est particulièrement important. Vous devez utiliser la valeur **ExpressRoute**. Notez qu’après l’exécution de ces applets de commande, la passerelle peut prendre 20 minutes ou plus pour créer.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>Vérifier que la passerelle a été créée.

Utilisez la commande suivante pour vérifier que la passerelle a été créée.

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>Redimensionner une passerelle

Il existe un certain nombre de [Références SKU de passerelle](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Vous pouvez utiliser la commande suivante pour modifier la référence SKU de la passerelle à tout moment.

>[AZURE.IMPORTANT] Cette commande ne fonctionne pas pour la passerelle des UltraPerformance. Pour modifier votre passerelle vers une passerelle UltraPerformance, supprimez d’abord la passerelle ExpressRoute existante et puis créer une nouvelle passerelle UltraPerformance. Pour déclasser votre passerelle d’une passerelle UltraPerformance, supprimez d’abord la passerelle UltraPerformance et puis créer une nouvelle passerelle.

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>Supprimer une passerelle

Utilisez la commande ci-dessous pour supprimer une passerelle

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  
