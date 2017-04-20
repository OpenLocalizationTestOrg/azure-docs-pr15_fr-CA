<properties
   pageTitle="Exemples de configuration de routeur de clients ExpressRoute | Microsoft Azure"
   description="Cette page fournit des exemples de configuration de routeur pour les routeurs Cisco et Juniper."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="router-configuration-samples-to-setup-and-manage-nat"></a>Exemples de configuration de routeur pour configurer et gérer des NAT

Cette page fournit des exemples de configuration NAT pour les routeurs de série ASA de Cisco et Juniper SRX. Ces sont destinés à être des exemples à titre indicatif et ne doivent pas être utilisés en l’état. Vous pouvez travailler avec votre fournisseur afin d’identifier les configurations appropriées pour votre réseau. 

>[AZURE.IMPORTANT] Les exemples de cette page sont destinées à être uniquement à titre indicatif. Vous devez travailler avec l’équipe de vente et techniques de votre fournisseur et votre équipe réseau afin d’identifier les configurations appropriées à vos besoins. Microsoft ne prendra pas en charge les problèmes liés aux configurations répertoriées dans cette page. Vous devez contacter votre fournisseur de périphérique pour les problèmes de prise en charge.

Exemples de configuration de routeur ci-dessous s’appliquent aux peerings Public Azure et Microsoft. Vous ne devez pas configurer NAT pour homologation privé Azure. Passez en revue [les exigences de NAT de ExpressRoute](expressroute-nat.md) et de [ExpressRoute peerings](expressroute-circuit-peerings.md) pour plus de détails.

**Remarque :** Vous devez utiliser des pools séparés NAT IP pour la connectivité à internet et ExpressRoute. L’utilisation du même pool IP NAT via internet et ExpressRoute entraîne le routage asymétrique et la perte de connectivité.

## <a name="cisco-asa-firewalls"></a>Pare-feux de Cisco ASA

### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Configuration de PAT pour le trafic réseau client à Microsoft

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Configuration de PAT pour le trafic à partir de Microsoft vers le réseau client

#### <a name="interfaces-and-direction"></a>Les interfaces et la Direction :
    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

#### <a name="configuration"></a>Configuration :
Pool NAT :

    object network outbound-PAT
        host <NAT-IP>

Serveur de la cible :

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Groupe d’objets pour les adresses IP de client

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>
    
    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

Commandes NAT :

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Routeurs de série Juniper SRX 

### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. créer des interfaces Ethernet redondants pour le cluster

    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. Créez deux zones de sécurité

 - Zone de confiance pour le réseau interne et Zone de certificats non approuvés pour le réseau externe vers les routeurs de bordure
 - Affecter des interfaces appropriées aux zones
 - Autoriser les services sur les interfaces


    sécurité {zones de confiance de sécurité-zone {{hôte--trafic entrant {-services système {ping ;                  } {bgp ; de protocoles                  interfaces}} {reth0.100 ;              }} certificats non approuvés de sécurité-zone {hôte--trafic entrant {-services système {ping ;                  } {bgp ; de protocoles                  interfaces}} {reth1.100 ;              }          }      }  }


### <a name="3-create-security-policies-between-zones"></a>3. créer des stratégies de sécurité entre les zones
 
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. Configuration de stratégies NAT
 - Créer deux pools NAT. Un servira à NAT le trafic sortant à Microsoft et autre à partir de Microsoft pour le client.
 - Créer des règles pour NAT le trafic respectif

        security {
            nat {
                source {
                    pool SNAT-To-ExpressRoute {
                        routing-instance {
                            External-ExpressRoute;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    pool SNAT-From-ExpressRoute {
                        routing-instance {
                            Internal;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    rule-set Outbound_NAT {
                        from routing-instance Internal;
                        to routing-instance External-ExpressRoute;
                        rule SNAT-Out {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-To-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                    rule-set Inbound-NAT {
                        from routing-instance External-ExpressRoute;
                        to routing-instance Internal;
                        rule SNAT-In {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-From-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }


### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Configurez BGP pour annoncer des préfixes sélectifs dans chaque direction.

Consultez les exemples dans la page des [exemples de configuration de routage](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. créer des stratégies

    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md) pour plus de détails.
