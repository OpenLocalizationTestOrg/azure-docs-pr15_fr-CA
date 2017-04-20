Un équilibreur de charge Azure est un équilibreur de charge de couche-4 (TCP, UDP). L’équilibreur de charge fournit une haute disponibilité en répartissant le trafic entrant sur les instances de service en bon état dans les services en nuage ou de machines virtuelles dans un jeu d’équilibrage de la charge. Azure équilibreur de charge peut également présenter ces services sur plusieurs ports, plusieurs adresses IP ou les deux.

Vous pouvez configurer un équilibreur de charge pour :

* Charger le trafic Internet entrant du solde pour les machines virtuelles (VM). Nous faisons référence à un équilibreur de charge dans ce scénario comme un [équilibreur de charge faisant face à Internet](../articles/load-balancer/load-balancer-internet-overview.md).
* Charge le trafic de solde entre les machines virtuelles dans un réseau virtuel (VNet), entre les ordinateurs virtuels dans les services en nuage ou entre les ordinateurs local et des ordinateurs virtuels dans un réseau virtuel de coexistence. Nous faisons référence à un équilibreur de charge dans ce scénario comme un [équilibreur de charge interne (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
* Transférer le trafic externe à une instance spécifique de la machine virtuelle.
