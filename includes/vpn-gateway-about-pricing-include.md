Vous payez pour deux choses : l’horaire de calcul des coûts de la passerelle réseau virtuel, et les données de sortie de transfert à partir de la passerelle réseau virtuel. Informations de tarification peuvent être trouvée dans la page [tarification](https://azure.microsoft.com/pricing/details/vpn-gateway) .

**Calculer les coûts de la passerelle réseau virtuel**<br>Chaque passerelle réseau virtuel a une base horaire de calculer les frais. Le prix est basé sur la passerelle de référence que vous spécifiez lorsque vous créez une passerelle réseau virtuel. Le coût est de la passerelle elle-même et est ainsi le transfert des données qui transitent via la passerelle.

**Les coûts de transfert de données**<br>Les coûts de transfert de données sont calculés en fonction du trafic de sortie à partir de la passerelle du réseau virtuel source.

- Si vous envoyez du trafic à votre périphérique VPN sur site, il sera débité avec le taux de transfert de données de sortie Internet.
- Si vous envoyez du trafic entre des réseaux virtuels dans différentes régions, la tarification est basée la région.
- Si vous envoyez le trafic uniquement entre des réseaux virtuels qui sont dans la même région, il n’existe aucun coût de données. Le trafic entre les VNets dans la même région est libre.