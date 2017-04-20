## <a name="scenario"></a>Scénario

Pour mieux illustrer comment créer un VNet et des sous-réseaux, ce document utilisera le scénario ci-dessous.

![Scénario de VNet](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

Dans ce scénario, vous allez créer un VNet nommé **TestVNet** avec un bloc CIDR réservé de **192.168.0.0./16**. Votre VNet contiendra les sous-réseaux suivants : 

- **Site Web frontal**, en utilisant les **192.168.1.0/24** comme son bloc CIDR.
- **Back-end**, en utilisant **192.168.2.0/24** comme son bloc CIDR.

 