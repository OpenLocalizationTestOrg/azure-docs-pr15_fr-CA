Lorsque vous créez une passerelle réseau virtuel, vous devez spécifier la passerelle point de stock que vous souhaitez utiliser. Lorsque vous sélectionnez une passerelle plue SKU, plus de processeurs et de la bande passante du réseau sont allouées à la passerelle, et par conséquent, la passerelle peut prendre en charge un débit réseau supérieur au réseau virtuel.

Passerelle VPN peut utiliser les références suivantes :

- Base
- Standard
- Ultraperformante

Lorsque vous sélectionnez une référence SKU, considérez les points suivants :

- Si vous souhaitez utiliser un type de PolicyBased VPN, vous devez utiliser la référence SKU de base. Réseaux privés virtuels PolicyBased (précédemment appelé routage statique) ne sont pas pris en charge sur n’importe quel autre SKU.
- BGP n’est pas pris en charge sur le SKU de base.
- ExpressRoute-VPN passerelle coexister configurations ne sont pas pris en charge sur le SKU de base.
- Connexions de passerelle VPN de S2S actif peuvent être configurées sur le SKU HighPerformance uniquement.
