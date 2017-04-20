Ressources|Limite par défaut|Limite maximale
---|---|---
[Machines virtuelles](../articles/virtual-machines/virtual-machines-linux-about.md) par cloud service<sup>1</sup>|50|50
Points de terminaison d’entrée par le cloud service<sup>2</sup>|150|150

<sup>1</sup> Les machines virtuelles créées dans la gestion de Service (et non le Gestionnaire de ressources) sont automatiquement stockés dans un service en nuage. Vous pouvez ajouter plus de machines virtuelles à ce service de cloud pour l’équilibrage de charge et la disponibilité. Découvrez [comment connecter des Machines virtuelles avec un réseau virtuel ou d’un Service en nuage](../articles/virtual-machines/virtual-machines-linux-classic-connect-vms.md).

<sup>2</sup> Les points de terminaison d’entrée autorisent les communications à une machine virtuelle à partir de hors service de cloud de la machine virtuelle. Service de machines virtuelles dans le même nuage ou de réseau virtuel peut communiquer automatiquement avec l’autre. Reportez-vous à la section [comment configurer des points de terminaison à une Machine virtuelle](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md). 
