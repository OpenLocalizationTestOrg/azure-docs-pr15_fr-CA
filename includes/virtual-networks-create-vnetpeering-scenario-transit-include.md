## <a name="service-chaining---transit-through-peered-vnet"></a>Chaînage de service - passage des ressources VNet

Bien que l’utilisation d’itinéraires du système facilite le trafic automatiquement pour votre déploiement, il existe des cas dans lesquels vous voulez contrôler le routage des paquets via l’appliance virtuelle.
Dans ce scénario, il existe deux VNets un abonnement, HubVNet et VNet1, comme indiqué dans le schéma ci-dessous. Vous déployez Appliance(NVA) virtuel de réseau dans VNet HubVNet. Après avoir établi l’homologation entre HubVNet et VNet1 de VNet, vous pouvez configurer des itinéraires définis par l’utilisateur et spécifier à couloirs du prochain saut dans le HubVNet.

![Transit d’à couloirs](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] Pour la simplicité, supposons que tous les VNets ici sont dans le même abonnement. Mais elle fonctionne également pour le scénario de cross-abonnement.

La propriété de clé pour activer le routage de Transit est le paramètre « Autoriser le trafic transmis ». Ainsi, accepter et envoyer le trafic depuis/vers l’à couloirs dans le ressources VNet.  
