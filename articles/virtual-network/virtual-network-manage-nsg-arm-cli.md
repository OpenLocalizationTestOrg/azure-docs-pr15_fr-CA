<properties 
   pageTitle="Gérer les NSGs dans le Gestionnaire de ressources à l’aide de la CLI Azure | Microsoft Azure"
   description="Découvrez comment gérer les NSGs existants dans le Gestionnaire de ressources à l’aide de la CLI d’Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-the-azure-cli"></a>Gérer des NSGs à l’aide de la CLI d’Azure

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="retrieve-information"></a>Extraire des informations

Vous pouvez afficher votre NSGs existantes, récupérer les règles pour une NSG existant et savoir quelles ressources un NSG est associé à.

### <a name="view-existing-nsgs"></a>Afficher NSGs existants

Pour afficher la liste des NSGs dans un groupe de ressources spécifique, exécutez le `azure network nsg list` de commande comme indiqué ci-dessous. 

    azure network nsg list --resource-group RG-NSG

Sortie attendue :

    info:    Executing command network nsg list
    + Getting the network security groups
    data:    Name          Location
    data:    ------------  --------
    data:    NSG-BackEnd   westus
    data:    NSG-FrontEnd  westus
    info:    network nsg list command OK
         
### <a name="list-all-rules-for-an-nsg"></a>Liste de toutes les règles pour une NSG

Pour afficher les règles d’un NSG nommés **FrontEnd-NSG**, exécutez le `azure network nsg show` de commande comme indiqué ci-dessous. 

    azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd

Sortie attendue :
    
    info:    Executing command network nsg show
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Name                            : NSG-FrontEnd
    data:    Type                            : Microsoft.Network/networkSecurityGroups
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    Tags                            : displayName=NSG - Front End
    data:    Security group rules:
    data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
    data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
    data:    rdp-rule                       Internet           *            *               3389              Tcp       Inbound    Allow   100
    data:    web-rule                       Internet           *            *               80                Tcp       Inbound    Allow   101
    data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
    data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
    data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
    data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
    data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
    data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
    info:    network nsg show command OK

>[AZURE.NOTE] Vous pouvez également utiliser `azure network nsg rule list --resource-group RG-NSG --nsg-name NSG-FrontEnd` pour répertorier les règles du **NSG-FrontEnd** NSG.

### <a name="view-nsg-associations"></a>Afficher les associations NSG

Pour afficher les ressources du NSG **NSG-frontal** est associer, exécuter la `azure network nsg show` de commande comme indiqué ci-dessous. Notez que la seule différence est l’utilisation du paramètre **--json** .

    azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json

Recherchez les propriétés **networkInterfaces** et **sous-réseaux** , comme indiqué ci-dessous :

    "networkInterfaces": [],
    ...
    "subnets": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
        }
    ],
    ...

Dans l’exemple ci-dessus, le NSG n’est pas associé à toutes les interfaces réseau (cartes réseau), et il est associé à un sous-réseau nommé **FrontEnd**.

## <a name="manage-rules"></a>Gérer les règles

Vous pouvez ajouter des règles à un NSG existant, modifier les règles existantes et supprimer des règles.

### <a name="add-a-rule"></a>Ajouter une règle

Pour ajouter une règle autorisant le trafic **entrant** sur le port **443** à partir de n’importe quel ordinateur à **NSG-FrontEnd** NSG, exécutez le `azure network nsg rule create` de commande comme indiqué ci-dessous.

    azure network nsg rule create --resource-group RG-NSG \
        --nsg-name NSG-FrontEnd \
        --name allow-https \
        --description "Allow access to port 443 for HTTPS" \
        --protocol Tcp \
        --source-address-prefix * \
        --source-port-range * \
        --destination-address-prefix * \
        --destination-port-range 443 \
        --access Allow \
        --priority 102 \
        --direction Inbound     

Sortie attendue :

    info:    Executing command network nsg rule create
    + Looking up the network security rule "allow-https"
    + Creating a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : *
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule create command OK

### <a name="change-a-rule"></a>Modifier une règle

Pour modifier la règle créée ci-dessus pour autoriser le trafic entrant à partir d' **Internet** uniquement, exécutez le `azure network nsg rule set` de commande comme indiqué ci-dessous.

    azure network nsg rule set --resource-group RG-NSG \
        --nsg-name NSG-FrontEnd \
        --name allow-https \
        --source-address-prefix Internet

Sortie attendue :

    info:    Executing command network nsg rule set
    + Looking up the network security group "NSG-FrontEnd"
    + Setting a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : Internet
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule set command OK

### <a name="delete-a-rule"></a>Supprimer une règle

Pour supprimer la règle créée ci-dessus, exécutez le `azure network nsg rule delete` de commande comme indiqué ci-dessous.

    azure network nsg rule delete --resource-group RG-NSG \
        --nsg-name NSG-FrontEnd \
        --name allow-https \
        --quiet

>[AZURE.NOTE] La **--silencieux** paramètre garantit que vous n’avez pas besoin de confirmer la suppression.

Sortie attendue :

    info:    Executing command network nsg rule delete
    + Looking up the network security group "NSG-FrontEnd"
    + Deleting network security rule "allow-https"
    info:    network nsg rule delete command OK

## <a name="manage-associations"></a>Gérer les associations

Vous pouvez associer un NSG à des sous-réseaux et des cartes réseau. Vous pouvez également dissocier un NSG à partir de n’importe quelle ressource qu'il est associé.

### <a name="associate-an-nsg-to-a-nic"></a>Associer un NSG à une carte réseau

Pour associer les **NSG-FrontEnd** NSG à la **TestNICWeb1** de carte réseau, exécutez le `azure network nic set` de commande comme indiqué ci-dessous.

    azure network nic set --resource-group RG-NSG \
        --name TestNICWeb1 \
        --network-security-group-name NSG-FrontEnd

Sortie attendue :

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Looking up the network security group "NSG-FrontEnd"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-nic"></a>Dissocier un NSG à partir d’une carte réseau

Pour dissocier **NSG-FrontEnd** NSG à partir de la carte d’interface réseau **TestNICWeb1** , exécutez le `azure network nic set` de commande comme indiqué ci-dessous.

    azure network nic set --resource-group RG-NSG --name TestNICWeb1 --network-security-group-id ""

>[AZURE.NOTE] Notez le « » (vide) valeur pour le paramètre **sécurité réseau-id groupe** . Voilà comment supprimer une association à une NSG. Vous ne pouvez pas faire de même avec le paramètre **sécurité-groupe-nom de réseau** .

Résultat attendu :

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-subnet"></a>Dissocier un NSG à partir d’un sous-réseau

Pour dissocier **NSG-FrontEnd** NSG à partir du sous-réseau **frontal** , exécutez le `azure network vnet subnet set` de commande comme indiqué ci-dessous.

    azure network vnet subnet set --resource-group RG-NSG \
        --vnet-name TestVNet \
        --name FrontEnd \
        --network-security-group-id ""

Sortie attendue :

    info:    Executing command network vnet subnet set
    + Looking up the subnet "FrontEnd"
    + Setting subnet "FrontEnd"
    + Looking up the subnet "FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:    Type                            : Microsoft.Network/virtualNetworks/subnets
    data:    ProvisioningState               : Succeeded
    data:    Name                            : FrontEnd
    data:    Address prefix                  : 192.168.1.0/24
    data:    IP configurations:
    data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
    data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
    data:
    info:    network vnet subnet set command OK

### <a name="associate-an-nsg-to-a-subnet"></a>Associer un NSG à un sous-réseau

Pour associer à nouveau les **NSG-FrontEnd** NSG au sous-réseau **FronEnd** , exécutez le `azure network vnet subnet set` de commande comme indiqué ci-dessous.

    azure network vnet subnet set --resource-group RG-NSG \
        --vnet-name TestVNet \
        --name FrontEnd \
        --network-security-group-name NSG-FronEnd

>[AZURE.NOTE] La commande ci-dessus ne fonctionne que parce que le NSG **NSG-frontal** est dans le même groupe de ressources que le réseau virtuel **TestVNet**. Si le NSG est dans un groupe de ressources différent, vous devez utiliser le **-réseau-sécurité-group-id** paramètre au lieu de cela et prévoir l’id complet du NSG. Vous pouvez extraire l’id **réseau azure nsg afficher--des groupes de ressources RG-NSG--nom du NSG-FrontEnd--json** en cours d’exécution puis cliquez sur la propriété **id** . 

Sortie attendue :

        info:    Executing command network vnet subnet set
        + Looking up the subnet "FrontEnd"
        + Looking up the network security group "NSG-FrontEnd"
        + Setting subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    IP configurations:
        data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
        data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
        data:
        info:    network vnet subnet set command OK

## <a name="delete-an-nsg"></a>Supprimer un NSG

Vous ne pouvez supprimer un NSG que si elle n’a pas été associé à une ressource. Pour supprimer un NSG, suivez les étapes ci-dessous.

1. Pour vérifier les ressources associées à un NSG, exécutez le `azure network nsg show` comme indiqué dans les [associations d’affichage NSGs](#View-NSGs-associations).
2. Si le NSG est associée à des cartes réseau, exécutez le `azure network nic set` comme indiqué dans [Dissociate un NSG à partir d’une carte réseau](#Dissociate-an-NSG-from-a-NIC) pour chaque carte réseau. 
3. Si le NSG est associée à un sous-réseau, exécutez les `azure network vnet subnet set` comme indiqué dans [Dissociate un NSG à partir d’un sous-réseau](#Dissociate-an-NSG-from-a-subnet) pour chaque sous-réseau.
4. Pour supprimer du NSG, exécutez le `azure network nsg delete` de commande comme indiqué ci-dessous.

        azure network nsg delete --resource-group RG-NSG --name NSG-FrontEnd --quiet

    Sortie attendue :

        info:    Executing command network nsg delete
        + Looking up the network security group "NSG-FrontEnd"
        + Deleting network security group "NSG-FrontEnd"
        info:    network nsg delete command OK

## <a name="next-steps"></a>Étapes suivantes

- [Activer la journalisation](virtual-network-nsg-manage-log.md) pour NSGs.