<properties
    pageTitle="Vue d’ensemble du protocole IPv6 pour l’équilibreur de charge Azure | Microsoft Azure"
    description="Comprendre la prise en charge de IPv6 pour l’équilibreur de charge Azure et équilibrage de charge les machines virtuelles."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="IPv6, équilibrage de la charge d’azure, double pile, adresse ip publique, ipv6 natif, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Vue d’ensemble du protocole IPv6 pour l’équilibreur de charge Azure

Les équilibreurs de charge faisant face à Internet peuvent être déployés avec une adresse IPv6. En plus de connectivité IPv4, ainsi les fonctionnalités suivantes :

* Bout en bout de connectivité IPv6 native entre des clients Internet publics et les Machines virtuelles de Azure (VMs) par l’intermédiaire de l’équilibreur de charge.
* Bout en bout de connectivité IPv6 native sortante entre les ordinateurs virtuels et des clients compatibles IPv6 de l’Internet publics.

L’image suivante illustre la fonctionnalité IPv6 pour Azure équilibreur de charge.

![Équilibrage de la charge Azure avec IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Une fois déployé, un client IPv4 ou IPv6 activé l’Internet peut communiquer avec les adresses IPv4 ou IPv6 publique (ou noms d’hôte) de l’équilibreur de charge pour Azure Internet. L’équilibreur de charge achemine les paquets IPv6 pour les adresses IPv6 privées des ordinateurs virtuels à l’aide de la traduction d’adresses réseau (NAT). Le client Internet IPv6 ne peuvent pas communiquer directement avec l’adresse IPv6 des ordinateurs virtuels.

## <a name="features"></a>Fonctionnalités

La prise en charge de IPv6 native pour les ordinateurs virtuels déployés via le Gestionnaire de ressources Azure fournit :

1. Équilibrage de charge de services IPv6 pour les clients sur Internet IPv6
2. Points de terminaison IPv4 et IPv6 natif sur les ordinateurs virtuels (« double empilée »)
3. Entrantes et sortant initié par connexions IPv6 natives
4. Activer la prise en charge des protocoles comme TCP, UDP et HTTP (S) une gamme complète d’architectures de service

## <a name="benefits"></a>Avantages

Cette fonctionnalité permet les avantages suivants :

* Respecter les réglementations gouvernementales exigent que les nouvelles applications soient accessibles aux clients IPv6 uniquement
* Activer mobile et Internet de développeurs de choses (IOT) à double pile (IPv4 + IPv6) Azure Virtual Machines permet de traiter la croissance mobile & IOT marchés

## <a name="details-and-limitations"></a>Détails et limites

Détails

* Le service DNS d’Azure contient des enregistrements de nom à la fois IPv4 et IPv6 AAAA et répond avec les deux enregistrements pour l’équilibreur de charge. Le client choisit l’adresse (IPv4 ou IPv6) pour communiquer avec.
* Lorsqu’une machine virtuelle initie une connexion à un périphérique connecté à l’Internet IPv6 public, adresse IPv6 de la source de la machine virtuelle est adresse réseau traduite (NAT) à l’adresse IPv6 publique de l’équilibreur de charge.
* Ordinateurs virtuels exécutant le système d’exploitation Linux doivent être configurés pour recevoir une adresse IPv6 IP via DHCP. La plupart des images dans la galerie d’Azure Linux sont déjà configurées pour prendre en charge IPv6 sans aucune modification. Pour plus d’informations, consultez [Configuration de DHCPv6 pour les machines virtuelles de Linux](load-balancer-ipv6-for-linux.md)
* Si vous choisissez d’utiliser une sonde de santé avec votre équilibreur de charge, créer une sonde IPv4 et l’utiliser avec les points de terminaison IPv4 et IPv6. Si le service sur votre ordinateur virtuel tombe en panne, les points de terminaison IPv4 et IPv6 sont prises de la rotation.

Limitations

* Vous ne pouvez pas ajouter de règles équilibrage de la charge de IPv6 dans Azure portal. Les règles peuvent être créés uniquement à travers le modèle, l’infrastructure du langage commun, PowerShell.
* Vous ne pouvez pas mettre à niveau des machines virtuelles existantes pour utiliser des adresses IPv6. Vous devez déployer de nouvelles machines virtuelles.
* Une seule adresse IPv6 peut être affectée à une seule interface réseau de chaque ordinateur virtuel.
* Les adresses IPv6 publiques ne peut pas être affectés à une machine virtuelle. Ils peuvent uniquement être affectés à un équilibreur de charge.
* Vous ne pouvez pas configurer la recherche DNS inversée pour les adresses IPv6 publiques.
* Les ordinateurs virtuels avec les adresses IPv6 ne peuvent pas être membres d’un Service de Cloud Azure. Ils peuvent être connectés à un réseau virtuel d’Azure (VNet) et communiquent avec eux sur leurs adresses IPv4.
* Les adresses IPv6 de privé peuvent être déployés sur des VM individuelles dans un groupe de ressources, mais ne peut pas être déployés dans un groupe de ressources via des ensembles de l’échelle.
* Azure VM ne peut pas se connecter via IPv6 sur des autres machines virtuelles, autres services Azure ou sur site. Ils peuvent uniquement communiquer entre l’équilibreur de charge Azure sur IPv6. Toutefois, ils peuvent communiquer avec les autres ressources à l’aide d’IPv4.
* Protection du groupe de sécurité réseau (NSG) pour IPv4 est pris en charge dans les déploiements de double pile (IPv4 + IPv6). NSGs ne s’appliquent pas aux points de terminaison IPv6.
* Le point de terminaison IPv6 sur l’ordinateur virtuel n’est pas exposé directement à internet. Il est derrière un équilibreur de charge. Seuls les ports spécifiés dans les règles d’équilibrage de la charge sont accessibles via IPv6.
* Modifier le paramètre IdleTimeout pour IPv6 est **actuellement pas pris en charge**. La valeur par défaut est de quatre minutes.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment déployer un équilibreur de charge avec IPv6.

* [Disponibilité du protocole IPv6 par région](https://go.microsoft.com/fwlink/?linkid=828357)
* [Déploiement d’un équilibreur de charge avec IPv6 à l’aide d’un modèle](load-balancer-ipv6-internet-template.md)
* [Déploiement d’un équilibreur de charge avec IPv6 à l’aide de PowerShell d’Azure](load-balancer-ipv6-internet-ps.md)
* [Déploiement d’un équilibreur de charge avec IPv6 à l’aide de la CLI d’Azure](load-balancer-ipv6-internet-cli.md)
