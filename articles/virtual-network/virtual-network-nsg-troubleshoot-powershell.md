<properties 
   pageTitle="Résoudre les problèmes de réseau des groupes de sécurité - PowerShell | Microsoft Azure"
   description="Découvrez comment résoudre les groupes de sécurité de réseau dans le modèle de déploiement d’Azure le Gestionnaire de ressources à l’aide de PowerShell d’Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Résoudre les problèmes liés à des groupes de sécurité réseau à l’aide de PowerShell d’Azure

> [AZURE.SELECTOR]
- [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Si vous configuré des groupes de sécurité réseau (NSGs) sur votre machine virtuelle (VM) et que vous rencontrez des problèmes de connectivité de machine virtuelle, cet article fournit une vue d’ensemble des fonctionnalités de diagnostic pour NSGs afin de résoudre les autres.

NSGs vous permet de contrôler les types de trafic activer ce flux et vos machines virtuelles (VM). NSGs peuvent être appliqués à des sous-réseaux dans un réseau virtuel d’Azure (VNet), les interfaces de réseau (NIC) ou les deux. Règles efficaces sur une carte d’interface réseau sont le regroupement de règles qui existent dans les NSGs appliqués à une carte réseau et le sous-réseau qu’il est connecté à. Les règles au sein de ces NSGs peuvent parfois entrent en conflit et avoir un impact sur la connectivité de réseau d’un ordinateur virtuel.  

Vous pouvez afficher toutes les règles de sécurité efficace dans votre NSGs, appliqués sur les cartes d’interface réseau de votre ordinateur virtuel. Cet article explique comment résoudre les problèmes de connectivité de machine virtuelle à l’aide de ces règles dans le modèle de déploiement du Gestionnaire de ressources Azure. Si vous n’êtes pas familiarisé avec les concepts de VNet et NSG, lisez les articles de vue d’ensemble [des réseaux virtuels](virtual-networks-overview.md) et des [groupes de sécurité réseau](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>À l’aide des règles de sécurité efficaces pour résoudre les problèmes de flux de trafic de machine virtuelle

Le scénario qui suit est un exemple d’un problème de connexion courant :

Un ordinateur virtuel nommé *VM1* fait partie d’un sous-réseau nommé *Subnet1* dans un VNet nommé *WestUS-VNet1*. Échec de la tentative de connexion à la machine virtuelle à l’aide de RDP sur le port TCP 3389. NSGs sont appliquées simultanément à la carte d’interface réseau *- NIC1 VM1* et le sous-réseau *Subnet1*. Le trafic sur le port TCP 3389 est autorisé dans le NSG associé à l’interface réseau *VM1-NIC1*, cependant, TCP ping à Échec du port 3389 de VM1.

Bien que cet exemple utilise le port TCP 3389, les étapes suivantes peuvent servir à déterminer les échecs de connexion entrante et sortante sur n’importe quel port.

## <a name="detailed-troubleshooting-steps"></a>Étapes détaillées de dépannage
Effectuez les étapes suivantes pour résoudre les problèmes de NSGs pour une machine virtuelle :

1. Démarrer une session PowerShell d’Azure et une connexion vers Azure. Si vous n’êtes pas familiarisé avec l’utilisation de PowerShell d’Azure, lisez l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

2. Entrez la commande suivante pour renvoyer toutes les règles NSG appliqués à une carte réseau nommée *VM1-NIC1* dans le groupe de ressources *RG1*:

        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Si vous ne connaissez pas le nom d’une carte réseau, entrez la commande suivante pour récupérer les noms de toutes les cartes réseau dans un groupe de ressources : 
    
    >`Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`

    Le texte suivant est un exemple de la sortie de règles efficaces retournée pour le *NIC1 VM1* carte d’interface réseau :

        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
        
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]

    Notez les informations suivantes dans la sortie :

    - Il existe deux sections de **NetworkSecurityGroup** : un est associé à un sous-réseau (*Subnet1*) et associé à une carte réseau (*VM1-NIC1*). Dans cet exemple, un NSG a été appliqué à chacun.
    - **Association** indique la ressource (carte réseau ou sous-réseau) un NSG donné est associé. Si la ressource NSG est déplacé/dissocié immédiatement avant d’exécuter cette commande, vous devrez peut-être attendre quelques secondes pour que la modification en fonction de la sortie de commande. 
    - Les noms de règle qui sont précédés de *defaultSecurityRules*: lorsque NSG un est créé, plusieurs règles de sécurité par défaut sont créés au sein de celui-ci. Les règles par défaut ne peuvent pas être supprimées, mais elles peuvent être remplacées par les règles de priorité plus élevés.
     Lisez l’article [vue d’ensemble NSG](virtual-networks-nsg.md#default-rules) pour en savoir plus sur les règles de sécurité par défaut NSG.
    - **ExpandedAddressPrefix** développe les préfixes d’adresse pour les balises par défaut NSG. Balises représentent plusieurs préfixes d’adresse. Expansion des balises peut être utile lors de la résolution des problèmes de connectivité VM vers/à partir de préfixes d’adresses spécifiques. Par exemple, avec VNET homologation, balise VIRTUAL_NETWORK se développe pour afficher les ressources VNet préfixes dans la sortie précédente.

        >[AZURE.NOTE] Les commande uniquement affiche efficace des règles si un NSG est associé à un sous-réseau, une carte réseau ou les deux. Une machine virtuelle peut avoir plusieurs cartes réseau avec différents NSGs appliqués. Lors du dépannage, exécutez la commande pour chaque carte réseau.
        
3. Pour faciliter le filtrage sur un plus grand nombre de règles NSG, entrez les commandes suivantes pour le dépannage : 

        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView

    Un filtre pour le trafic RDP (le port TCP 3389), est appliqué à l’affichage de grille, comme illustré dans l’image suivante :

    ![Liste de règles](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
    
4. Comme vous pouvez le voir dans l’affichage de grille, il y a autorisation et refus des règles pour RDP. La sortie de l’étape 2 indique que la règle de *DenyRDP* est dans le NSG appliquée au sous-réseau. Pour les règles de trafic entrant, NSGs appliquées au sous-réseau sont traitées en premier. Si une correspondance est trouvée, le NSG appliqué à l’interface réseau n’est pas traité. Dans ce cas, la règle de *DenyRDP* à partir du sous-réseau bloque RDP de la machine virtuelle (**VM1**).

    >[AZURE.NOTE] Une machine virtuelle peut avoir plusieurs cartes réseau attaché. Chacun peut être connecté à un sous-réseau différent. Dans la mesure où les commandes dans les étapes précédentes sont exécutées par rapport à une carte réseau, il est important de s’assurer que vous spécifiez la carte réseau que vous êtes confronté à l’échec de la connexion à. Si vous n’êtes pas sûr, vous pouvez toujours exécuter les commandes sur chaque carte réseau attachée à la machine virtuelle.

5. À RDP dans VM1, modifier la règle de *Refus RDP (3389)* pour *Permettre des RDP(3389)* dans la **Subnet1-NSG** NSG. Vérifiez que le port TCP 3389 est ouvert par l’ouverture d’une connexion RDP de la machine virtuelle ou à l’aide de l’outil PsPing. Vous pouvez en savoir plus sur les PsPing en lisant la [page de téléchargement de PsPing](https://technet.microsoft.com/sysinternals/psping.aspx)

    Vous pouvez, ou supprimez des règles d’un NSG en utilisant les informations dans la sortie de la commande suivante :

        Get-Help *-AzureRmNetworkSecurityRuleConfig
        

## <a name="considerations"></a>Considérations relatives à la

Lors de la résolution des problèmes de connectivité, tenez compte des points suivants :

- Les règles par défaut NSG va bloquer l’accès entrant à partir d’internet et ne permettre que VNet le trafic entrant. Règles doivent être explicitement ajoutés pour permettre l’accès entrant à partir d’Internet, comme requis.
- S’il n’y a aucune règle de sécurité NSG à l’origine de la connectivité de réseau d’un ordinateur virtuel échoue, le problème peut être dû à :
    - Logiciel de pare-feu s’exécutant dans le système d’exploitation de l’ordinateur
    - Itinéraires configurés pour appliances virtuelles ou sur site. Le trafic Internet peut être redirigé vers les locaux via forcé de tunnel. Une connexion RDP/SSH à partir d’Internet à votre machine virtuelle peut ne pas fonctionne avec ce paramètre, en fonction de la manière dont le matériel de réseau local gère ce trafic. Lisez l’article de [Résolution des problèmes des itinéraires](virtual-network-routes-troubleshoot-powershell.md) pour savoir comment diagnostiquer les problèmes de routage peuvent être entraver le flux du trafic entrant et sortant de la machine virtuelle. 
- Si vous avez ressources VNets, par défaut, la balise VIRTUAL_NETWORK se développe automatiquement pour inclure les préfixes de ressources VNets. Vous pouvez afficher ces préfixes dans la liste **ExpandedAddressPrefix** , pour résoudre les problèmes liés à la connectivité d’homologation VNet. 
- Les règles de sécurité efficace ne sont affichées que s’il existe un NSG associé à de la machine virtuelle carte d’interface réseau et ou sous-réseau. 
- Si il n’y a aucun NSGs associés à la carte réseau ou sous-réseau et que vous avez une adresse IP publique affectée à votre machine virtuelle, tous les ports seront ouverts pour un accès entrant et sortant. Si la machine virtuelle a une adresse IP publique, l’application NSGs à la carte réseau ou un sous-réseau est fortement recommandé.  
