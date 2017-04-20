## <a name="virtual-network"></a>Réseau virtuel
Ressources virtuelles de réseaux (VNET) et de sous-réseaux aident à définir une limite de sécurité pour les charges de travail en cours d’exécution dans Azure. Un VNet est caractérisée par une collection d’espaces d’adressage, défini en tant que blocs CIDR. 

>[AZURE.NOTE] Les administrateurs réseau sont familiarisés avec la notation CIDR. Si vous n’êtes pas familiarisé avec CIDR, [en savoir plus sur elle](http://whatismyipaddress.com/cidr).

![VNet avec plusieurs sous-réseaux](./media/resource-groups-networking/Figure4.png)

VNets contient les propriétés suivantes.

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**à EMC**|Collection de préfixes d’adresses qui composent le VNet dans la notation CIDR|192.168.0.0/16|
|**sous-réseaux**|Collection des sous-réseaux qui composent le VNet|consultez les [sous-réseaux](#Subnets) ci-dessous.|
|**adresse IP**|Adresse IP affectée à un objet. Il s’agit d’une propriété en lecture seule.|104.42.233.77|

### <a name="subnets"></a>Sous-réseaux
Un sous-réseau est une ressource enfant d’une VNet, et permettant de définir les segments d’espaces d’adressage dans un bloc CIDR, à l’aide de préfixes d’adresses IP. Cartes réseau peut être ajoutées à des sous-réseaux et connectés aux ordinateurs virtuels, la fourniture de connectivité sur des charges de travail différentes.

Sous-réseaux contient les propriétés suivantes. 

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**addressPrefix**|Préfixe d’adresse unique qui constituent le sous-réseau en notation CIDR|192.168.1.0/24|
|**networkSecurityGroup**|NSG appliquée au sous-réseau|voir [NSGs](#Network-Security-Group)|
|**routeTable**|Table de routage appliquée au sous-réseau|reportez-vous à la section [UDR](#Route-table)|
|**ipConfigurations**|Collection d’objets de configuration IP utilisée par la carte réseau connectée au sous-réseau|reportez-vous à la section [UDR](#Route-table)|


VNet d’exemple au format JSON :

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Ressources supplémentaires

- Obtenir plus d’informations sur [VNet](../articles/virtual-network/virtual-networks-overview.md).
- Lisez la [documentation de référence API REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) de VNets.
- Lisez la [documentation de référence API REST](https://msdn.microsoft.com/library/azure/mt163618.aspx) de sous-réseaux.