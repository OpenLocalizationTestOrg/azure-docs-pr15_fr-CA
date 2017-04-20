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

# <a name="router-configuration-samples-to-setup-and-manage-routing"></a>Exemples de configuration de routeur pour configurer et gérer le routage

Cette page fournit une interface et des exemples de configuration de routage pour les routeurs des séries Cisco IOS-XE et Juniper MX. Ces sont destinés à être des exemples à titre indicatif et ne doivent pas être utilisés en l’état. Vous pouvez travailler avec votre fournisseur afin d’identifier les configurations appropriées pour votre réseau. 

>[AZURE.IMPORTANT] Les exemples de cette page sont destinées à être uniquement à titre indicatif. Vous devez travailler avec l’équipe de vente et techniques de votre fournisseur et votre équipe réseau afin d’identifier les configurations appropriées à vos besoins. Microsoft ne prendra pas en charge les problèmes liés aux configurations répertoriées dans cette page. Vous devez contacter votre fournisseur de périphérique pour les problèmes de prise en charge.

Exemples de configuration de routeur ci-dessous s’appliquent à tous les peerings. Pour plus d’informations sur le routage, consultez [ExpressRoute peerings](expressroute-circuit-peerings.md) et des [exigences de gamme ExpressRoute](expressroute-routing.md) .

## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE en fonction des routeurs

Les exemples de cette section s’appliquent à n’importe quel routeur exécutant la famille XE de l’e/s du système d’exploitation.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configuration des interfaces et des sous-interfaces

Vous aurez besoin d’une interface de sub par homologation sur chaque routeur que vous vous connectez à Microsoft. Une interface sub peut être identifiée avec un ID de réseau VLAN ou empilé deux ID de VLAN et d’une adresse IP.

#### <a name="dot1q-interface-definition"></a>Définition de l’interface Dot1Q

Cet exemple fournit la définition d’interface secondaire pour une interface secondaire avec un seul ID de VLAN. L’ID VLAN est unique par l’homologation. Le dernier octet de votre adresse IPv4 sera toujours un nombre impair.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### <a name="qinq-interface-definition"></a>Définition de l’interface QinQ

Cet exemple fournit la définition d’interface secondaire pour une interface secondaire avec un ID de réseaux VLAN deux. L’ID VLAN externe (s-balise), si vous utilisez reste la même sur tous les peerings. L’ID VLAN interne (c-balise) est unique par l’homologation. Le dernier octet de votre adresse IPv4 sera toujours un nombre impair.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### <a name="2-setting-up-ebgp-sessions"></a>2. Configuration de sessions d’eBGP

Vous devez configurer une session BGP avec Microsoft pour chaque homologation. L’exemple ci-dessous vous permet de configurer une session BGP avec Microsoft. Si l’adresse IPv4 que vous avez utilisé pour votre interface sub était a.b.c.d, l’adresse IP du voisin BGP (Microsoft) sera a.b.c.d+1. Le dernier octet d’adresse IPv4 du voisin BGP sera toujours un nombre pair.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configuration des préfixes pour être publié sur la session de protocole BGP

Vous pouvez configurer votre routeur pour annoncer des préfixes select à Microsoft. Vous pouvez le faire à l’aide de l’exemple ci-dessous.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. mappe de route

Vous pouvez utiliser les mappages de l’itinéraire et des listes de préfixe pour les préfixes de filtre propagés dans votre réseau. Vous pouvez utiliser l’exemple ci-dessous pour accomplir la tâche. Vous assurer que les paramètres des listes de préfixe approprié.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Routeurs de série Juniper MX 

Les exemples de cette section s’appliquent à tous les routeurs de série Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configuration des interfaces et des sous-interfaces

#### <a name="dot1q-interface-definition"></a>Définition de l’interface Dot1Q

Cet exemple fournit la définition d’interface secondaire pour une interface secondaire avec un seul ID de VLAN. L’ID VLAN est unique par l’homologation. Le dernier octet de votre adresse IPv4 sera toujours un nombre impair.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


#### <a name="qinq-interface-definition"></a>Définition de l’interface QinQ

Cet exemple fournit la définition d’interface secondaire pour une interface secondaire avec un ID de réseaux VLAN deux. L’ID VLAN externe (s-balise), si vous utilisez reste la même sur tous les peerings. L’ID VLAN interne (c-balise) est unique par l’homologation. Le dernier octet de votre adresse IPv4 sera toujours un nombre impair.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Configuration de sessions d’eBGP

Vous devez configurer une session BGP avec Microsoft pour chaque homologation. L’exemple ci-dessous vous permet de configurer une session BGP avec Microsoft. Si l’adresse IPv4 que vous avez utilisé pour votre interface sub était a.b.c.d, l’adresse IP du voisin BGP (Microsoft) sera a.b.c.d+1. Le dernier octet d’adresse IPv4 du voisin BGP sera toujours un nombre pair.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configuration des préfixes pour être publié sur la session de protocole BGP

Vous pouvez configurer votre routeur pour annoncer des préfixes select à Microsoft. Vous pouvez le faire à l’aide de l’exemple ci-dessous.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. mappe de route

Vous pouvez utiliser les mappages de l’itinéraire et des listes de préfixe pour les préfixes de filtre propagés dans votre réseau. Vous pouvez utiliser l’exemple ci-dessous pour accomplir la tâche. Vous assurer que les paramètres des listes de préfixe approprié.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md) pour plus de détails.
