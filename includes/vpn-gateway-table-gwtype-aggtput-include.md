|    | **Débit de passerelle VPN (1)** | **(2) des tunnels IPsec max de passerelle VPN** | **Débit de passerelle de ExpressRoute** | **Passerelle VPN et ExpressRoute coexister**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU de base (3)(5)**              |  100 Mbits/s | 10                         |  500 Mbps                           | N°   |
| **Point de stock standard (4)(5)**           |  100 Mbits/s | 10                         | 1000 Mbits/s                           | Oui  |
| **Hautes performances de référence (4)**   | 200 Mbits/s  | 30                         | Mbits/s de 2000                           | Oui  |

- (1) le débit VPN est une estimation grossière basée sur les mesures entre VNets dans la même région d’Azure. Il n’est pas un débit garanti pour les connexions entre sites sur Internet. Elle est la mesure de débit maximal possible.
- (2) consultez le nombre de tunnels VPN de RouteBased. Un VPN PolicyBased peut prendre uniquement en charge un tunnel VPN de Site à Site.
- (3) BGP n’est pas prise en charge de la base.
- (4) réseaux privés virtuels de PolicyBased ne sont pas pris en charge pour ce point de stock. Ils sont pris en charge uniquement pour la base.
- (5) connexions de passerelle VPN de S2S actif-actif ne sont pas pris en charge pour ce SKU. Actif-actif est pris en charge sur le SKU HighPerformance uniquement.