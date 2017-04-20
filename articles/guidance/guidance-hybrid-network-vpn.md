<properties
   pageTitle="Implémentation d’une Architecture de réseau hybride avec VPN Azure et sur site | Microsoft Azure"
   description="Comment faire pour implémenter une architecture de réseau de site à site sécurisé qui s’étend sur un réseau virtuel Azure et un réseau local connecté à l’aide d’un VPN."
   services=""
   documentationCenter="na"
   authors="RohitSharma-pnp"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="roshar"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-and-on-premises-vpn"></a>Implémentation d’une Architecture de réseau hybride avec Azure et sur site VPN

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article présente un ensemble de méthodes conseillées pour l’extension d’un réseau local sur Azure à l’aide d’un réseau privé virtuel site-à-site (VPN). Le flux de trafic entre le réseau local et d’un réseau virtuel Azure (VNet) via un tunnel VPN IPSec. Cette architecture est appropriée pour des applications hybrides avec les caractéristiques suivantes :

- Parties de l’application exécutent sur site pendant que d’autres exécutent dans Azure.

- Le trafic entre le matériel sur site et le nuage est susceptible d’être la lumière, ou il est avantageux d’échanges latence légèrement étendue pour la flexibilité et la puissance de traitement du nuage.

- Le réseau étendu constitue un système fermé. Il n’y a aucun chemin d’accès direct à partir d’Internet pour le VNet d’Azure.

- Les utilisateurs se connectent au réseau local pour utiliser les services hébergés dans Azure. Le pont entre le réseau local et les services qui s’exécutent dans Azure est transparent pour les utilisateurs.

Voici quelques exemples de scénarios qui correspondent à ce profil :

- Applications métier d’utilisé au sein d’une organisation, où une partie de la fonctionnalité a été migré vers le nuage.

- Charges de travail de développement/test/atelier.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources Azure] [ resource-manager-overview] et classique. Ce modèle utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant illustre les composants de cette architecture :

> Un document Visio qui contient ce diagramme d’architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur « réseau hybride - VPN » page.

![[0]][0]

- **Réseau de locaux.** Il s’agit d’un réseau d’ordinateurs et de périphériques connectés via un réseau local privé en cours d’exécution au sein d’une organisation.

- **Appareil VPN.** Il s’agit d’un périphérique ou un service qui fournit la connectivité externe au réseau local. L’appareil VPN peut être un périphérique matériel, ou elle peut être une solution de logiciels tels que le routage et le Service d’accès distant (RRAS) de Windows Server 2012.

> [AZURE.NOTE] Pour une liste des équipements pris en charge de VPN et les informations sur la configuration des appareils VPN sélectionnés pour la connexion à une passerelle de VPN Azure, reportez-vous aux instructions pour le périphérique approprié dans la [liste des périphériques VPN pris en charge par Azure][vpn-appliance].

- **Application de nuage de N-couche.** Il s’agit de l’application hébergée dans Azure. Il peut inclure plusieurs niveaux, avec plusieurs sous-réseaux connectés via les équilibreurs de charge Azure. Le trafic de chaque sous-réseau peut être soumis à des règles définies à l’aide de [Groupes de sécurité de réseau Azure (NSGs)][azure-network-security-group]. Pour plus d’informations, consultez [mise en route avec Microsoft Azure sécurité][getting-started-with-azure-security].

> [AZURE.NOTE] Cet article décrit l’application de nuage comme une entité unique. Consultez [exécution d’une architecture à N niveaux sur Azure] [ implementing-a-multi-tier-architecture-on-Azure] pour plus d’informations.

- **[Réseau virtuel (VNet)][azure-virtual-network].** L’application en nuage et les composants de la passerelle VPN Azure se trouvent dans le même VNet.

- **[Passerelle VPN Azure][azure-vpn-gateway].** Le service de passerelle VPN vous permet de connecter le VNet au réseau local via une solution VPN. Pour plus d’informations, consultez [connecter un réseau local à un réseau virtuel Microsoft Azure][connect-to-an-Azure-vnet]. La passerelle VPN comprend les éléments suivants :

  - **Passerelle de réseau virtuel.** Il s’agit d’une ressource qui fournit une solution VPN virtuelle pour le VNet. Il est responsable pour acheminer le trafic du réseau local à la VNet.

  - **Passerelle du réseau local.** Il s’agit d’une abstraction de l’appareil VPN sur site. Le trafic du réseau à partir de l’application en nuage au réseau local est routé via cette passerelle.

  - **Connexion.** La connexion a des propriétés qui spécifient le type de connexion (IPSec) et la clé partagée avec la solution de VPN local pour crypter le trafic.

  - **Sous-réseau de passerelle.** La passerelle réseau virtuel se trouve dans son propre sous-réseau, qui est soumis à des exigences différentes, comme décrit dans la section recommandations ci-dessous.

- **Équilibreur de charge interne.** Le trafic du réseau à partir de la passerelle VPN est acheminé vers l’application cloud via un équilibreur de charge interne. L’équilibreur de charge se trouve dans le sous-réseau frontal de l’application.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être configuré de différentes façons. Nous avons fourni un modèle Azure Resource Manager pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir une recommandation ne correspondant pas à un besoin spécifique.

### <a name="vnet-and-gateway-subnet"></a>Sous-réseau de VNet et de la passerelle

Créer une VNet Azure pour maintenir les composants dans le nuage. L’espace d’adressage de le VNet d’Azure doit être assez grande pour contenir les adresses utilisées par les ordinateurs virtuels et les sous-réseaux dans le VNet. Assurez-vous que l’espace d’adressage VNet dispose de suffisamment d’espace pour la croissance si les ordinateurs virtuels supplémentaires sont susceptibles d’être nécessaires à l’avenir. L’espace d’adressage de la VNet ne doit pas se chevaucher avec le réseau local. Par exemple, le schéma ci-dessus utilise le 10.20.0.0/16 espace adresse pour le VNet.

Créer un sous-réseau nommé _GatewaySubnet_, avec une plage d’adresses de /27. Ce sous-réseau est requise par la passerelle de réseau virtuel, et affectation de 32 adresses de ce sous-réseau aidera à éviter que vous exécutez contre les limitations de taille de passerelle possible à l’avenir. Évitez de placer ce sous-réseau au milieu de l’espace d’adressage. Il est judicieux de définir l’espace d’adressage pour le sous-réseau de passerelle à l’extrémité supérieure de l’espace d’adressage VNet. L’exemple illustré utilise 10.20.255.224/27.  Une procédure rapide pour calculer le CIDR est la suivante :

1. Définir les bits variables dans l’espace d’adressage de le VNet à 1, jusqu'à les bits qui est utilisé par le sous-réseau de passerelle, puis définissez les bits restants à 0.

2. Convertir les bits qui en résulte en nombre décimal et l’exprimer sous la forme d’un espace d’adressage avec le jeu de longueur de préfixe à la taille du sous-réseau passerelle.

Par exemple, pour un VNet avec une plage d’adresses IP de 10.20.0.0/16, application étape #1 ci-dessus devient 10.20.0b11111111.0b11100000.  Que la conversion en nombre décimal et il exprimant comme un espace d’adressage donne 10.20.255.224/27

> [AZURE.WARNING] Ne déployez pas les autres machines virtuelles ou des instances de rôle pour le sous-réseau de passerelle. En outre, n’assignez pas un NSG à ce sous-réseau, car cela provoquera la passerelle afin de cesser de fonctionner.

### <a name="virtual-network-gateway"></a>Passerelle réseau virtuel

Affecter une adresse IP publique de la passerelle réseau virtuel.

Créer de la passerelle réseau virtuel dans le sous-réseau de la passerelle et l’affecter l’adresse IP publique nouvellement alloué. Utilisez le type de passerelle qui correspond le mieux à vos besoins et qui est activé par votre appareil VPN :

Créer une [passerelle basée sur la stratégie de] [ policy-based-routing] si vous devez contrôler avec précision la façon dont les demandes sont routés. en fonction de critères de stratégie comme préfixes d’adresse. Les passerelles basées sur une stratégie de recourir au routage statique et ne fonctionnent qu’avec des connexions de site à site.

Créer une [passerelle basée sur un itinéraire] [ route-based-routing] si vous vous connectez au réseau local à l’aide du service RRAS, prend en charge les connexions à plusieurs sites ou inter-région ou implémenter des connexions VNet à VNet (y compris les itinéraires qui passent par plusieurs VNets). Les passerelles basées sur l’itinéraire utilisent le routage dynamique pour diriger le trafic entre les réseaux. Ils peuvent tolérer les pannes dans le chemin d’accès de réseau mieux que les itinéraires statiques, car vous pouvez essayer d’autres itinéraires. Les passerelles basées sur l’itinéraire peuvent également réduire les frais de gestion, étant donné que les itinéraires ne doit pas nécessairement être mis à jour manuellement lors de la modification des adresses de réseau.

Pour une liste des équipements pris en charge de VPN, reportez-vous à la section [périphériques sur VPN pour les connexions de passerelle VPN de Site à Site][vpn-appliances].

> [AZURE.NOTE] Après avoir créé la passerelle, vous ne pouvez pas modifier entre types de passerelle sans supprimer et recréer la passerelle.

Sélectionnez la référence de passerelle VPN Azure qui correspond le mieux à vos besoins en matière de débit. Passerelle de VPN Azure est disponible dans trois références indiquées dans le tableau suivant. Chaque UGS fournit un débit différent, [sont perçus,] [ azure-gateway-charges] en fonction de la durée de la mise en service de la passerelle et disponible.

| POINT DE STOCK | Débit VPN | Tunnels IPSec de max|
|-----|----------------|------------------|
| Base | 100 Mbits/s | 10 |
| Standard | 100 Mbits/s | 10 |
| Hautes performances | 200 Mbits/s | 30 |

> [AZURE.NOTE] Le SKU de base n’est pas compatible avec le ExpressRoute d’Azure. Vous pouvez [Modifier la référence SKU de la] [ changing-SKUs] une fois la passerelle a été créée.

Créer des règles de routage pour le sous-réseau de passerelle que le trafic de l’application du entrant directe à partir de la passerelle de l’équilibreur de charge interne plutôt que d’autoriser les demandes de passer directement aux ordinateurs virtuels que vous implémentent l’application.

### <a name="on-premises-network-connection"></a>Connexion au réseau local

Créer une passerelle du réseau local. Spécifiez l’adresse IP publique de la solution de VPN sur site et de l’espace d’adressage du réseau local. Notez que la solution de VPN sur site doit avoir une adresse IP publique qui est accessible par la passerelle du réseau local dans Azure VPN passerelle. Impossible de localiser le périphérique VPN derrière un périphérique NAT.

Créer une connexion de site à site de la passerelle réseau virtuel et la passerelle du réseau local. Sélectionnez le type de connexion de Site à site (IPSec) et spécifier la clé partagée. Cryptage de site à site avec la passerelle VPN Azure est basé sur le protocole IPSec, à l’aide de clés prépartagées pour l’authentification. Vous spécifiez la clé lors de la création de la passerelle VPN Azure. Vous devez configurer l’appareil VPN local en cours d’exécution avec la même clé. Autres mécanismes d’authentification ne sont actuellement pas pris en charge.

Assurez-vous que sur site infrastructure de routage est configuré pour transférer les demandes destinées aux adresses dans le VNet Azure à l’appareil VPN.

Ouvrez les ports requis par l’application de cloud dans le réseau local.

Tester la connexion pour vérifier que :

- La solution de VPN local achemine correctement le trafic à l’application de nuage par le biais de la passerelle VPN Azure.

- Le VNet achemine correctement le trafic sur le réseau local.

- Interdit le trafic dans les deux sens est bloqué correctement.

## <a name="scalability-considerations"></a>Évolutivité

Vous pouvez obtenir une évolutivité verticale limitée par déplacement de la base ou le SKU de passerelle VPN Standard vers la référence SKU de haute Performance VPN.

Pour VNets qui attendent un gros volume de trafic VPN, envisagez de distribuer les charges de travail différentes dans VNets de plus petits séparés et configurez une passerelle VPN pour chacun d’eux.

Vous pouvez partitionner le VNet horizontalement ou verticalement. Pour partitionner horizontalement, déplacer certaines instances de machine virtuelle à partir de chaque couche en sous-réseaux de la VNet de nouveau. Le résultat est que chaque VNet possède la même structure et les mêmes fonctionnalités. Pour partitionner verticalement, recréer chaque niveau pour diviser les fonctionnalités dans les différentes zones logiques (par exemple, la gestion des commandes, facturation, gestion des comptes clients et ainsi de suite). Chaque zone fonctionnelle peut alors être placé dans son propre VNet.

Réplication d’un contrôleur de domaine Active Directory sur site dans le VNet et mise en œuvre de DNS dans le VNet, peuvent contribuer à réduire certains du trafic lié à la sécurité et d’administration découlant des locaux vers le nuage.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Si vous devez vous assurer que le réseau local reste disponible à la passerelle VPN Azure, implémenter un cluster de basculement pour la passerelle VPN locale.

Si votre organisation a plusieurs sites sur site, créer des [connexions de plusieurs sites] [ vpn-gateway-multi-site] à un ou plusieurs VNets d’Azure. Cette approche nécessite un routage dynamique (basée sur un itinéraire), assurez-vous que la passerelle VPN locale prend en charge cette fonctionnalité.

Consultez le [contrat SLA pour la passerelle VPN] [ sla-for-vpn-gateway] pour les détails sur les SLA de passerelle VPN.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Surveiller les informations de diagnostic à partir d’appareils de VPN sur site. Ce processus varie selon les fonctionnalités fournies par l’appareil VPN. Par exemple, si vous utilisez le routage et le Service d’accès distant de Windows Server 2012, vous devez activer la [journalisation de RRAS][rras-logging].

Utilisez les [diagnostics de passerelle VPN de Azure] [ gateway-diagnostic-logs] pour capturer des informations sur les problèmes de connectivité. Ces journaux peuvent être utilisés pour suivre les informations de la source et les destinations de connexion, le protocole utilisé, ou et les demandes comment la connexion a été établie (pourquoi la tentative a échoué).

Surveiller les journaux des opérations de la passerelle VPN Azure en utilisant les journaux d’audit dans le portail Azure. Des journaux distincts sont disponibles pour la passerelle du réseau local, la passerelle réseau Azure et la connexion. Cette information peut être utilisée pour effectuer le suivi de toutes les modifications apportées à la passerelle et peut être utile si une passerelle fonctionne déjà cesse de fonctionner pour une raison quelconque.

![[2]][2]

Surveiller la connectivité et le suivi des événements d’échec de connexion. Vous pouvez utiliser un package de surveillance tels que les [Nagios] [ nagios] de capturer et de signaler ces informations.

## <a name="security-considerations"></a>Considérations sur la sécurité

Générer une clé partagée différente pour chaque passerelle VPN. Utiliser une clé partagée forte pour résister à des attaques de force brute.

> [AZURE.NOTE] Actuellement, vous ne permet pas Azure clé coffre-fort passerelle de VPN d’Azure des clés prépartagées.

Assurez-vous que l’appareil VPN sur site utilise une méthode de cryptage qui est [compatible avec la passerelle VPN Azure][vpn-appliance-ipsec]. Routage basé sur la stratégie, la passerelle VPN Azure prend en charge les algorithmes de cryptage AES256, AES128 et 3DES. Les passerelles basées sur l’itinéraire prend en charge AES256 et 3DES.

Si votre solution de VPN sur site se trouve sur un réseau de périmètre avec un pare-feu entre le réseau de périmètre et Internet, vous devrez peut-être configurer des [règles de pare-feu supplémentaires] [ additional-firewall-rules] pour permettre la connexion VPN de site à site.

Si l’application dans le VNet envoie des données à Internet, envisagez [l’implémentation de tunneling forcé] [ forced-tunneling] pour acheminer le trafic lié aux Internet via le réseau local. Cette approche vous permet d’auditer des demandes sortantes effectuées par l’application à partir de l’infrastructure sur site.

> [AZURE.NOTE] Tunneling forcé peut affecter la connectivité aux services Azure (le Service de stockage, par exemple) et le Gestionnaire de licence de Windows.

## <a name="troubleshooting-considerations"></a>Considérations relatives à la résolution des problèmes

> [AZURE.NOTE] Visitez le routage et le Blog de l’accès distant pour des informations générales sur le [Dépannage des erreurs courantes liées à VPN][troubleshooting-vpn-errors].

Si le trafic ne peut traverser la connexion VPN, vérifiez les points suivants :

### <a name="on-premises-vpn-appliance"></a>Appareil de VPN sur site :

**La solution de VPN local fonctionne correctement ?**

Vérifiez les fichiers journaux générés par la solution VPN pour les erreurs et les pannes. L’emplacement de ces informations varie en fonction de votre solution matérielle-logicielle. Par exemple, si vous utilisez le service RRAS de Windows Server 2012, vous pouvez utiliser la commande PowerShell suivante pour afficher des informations sur l’événement Erreur du service RRAS :

```
Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
```

La propriété de _Message_ de chaque entrée fournit une description de l’erreur. Certains exemples courants sont :

- Impossibilité de se connecter, probablement en raison d’une adresse IP incorrecte spécifiée pour la passerelle VPN Azure dans la configuration de l’interface réseau RRAS VPN :

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {41, 3, 0, 0}
Index              : 14231
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: The network connection between your computer and
                     the VPN server could not be established because the remote server is not responding. This could
                     be because one of the network devices (e.g, firewalls, NAT, routers, etc) between your computer
                     and the remote server is not configured to allow VPN connections. Please contact your
                     Administrator or your service provider to determine which device may be causing the problem.
Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                     your computer and the VPN server could not be established because the remote server is not
                     responding. This could be because one of the network devices (e.g, firewalls, NAT, routers, etc)
                     between your computer and the remote server is not configured to allow VPN connections. Please
                     contact your Administrator or your service provider to determine which device may be causing the
                     problem.}
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:26:02 PM
TimeWritten        : 3/18/2016 1:26:02 PM
UserName           :
Site               :
Container          :
```

- La mauvaise clé partagée spécifiée dans la configuration de l’interface réseau RRAS VPN :

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {233, 53, 0, 0}
Index              : 14245
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: IKE authentication credentials are unacceptable

Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                     unacceptable
                     }
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:34:22 PM
TimeWritten        : 3/18/2016 1:34:22 PM
UserName           :
Site               :
Container          :
```

Vous pouvez également obtenir des informations de journal des événements sur essaie de se connecter via le service RRAS à l’aide de la commande PowerShell suivante :

```
Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
```

En cas d’échec de connexion, ce journal contient des erreurs similaires à ce qui suit :

```
EventID            : 20227
MachineName        : on-prem-vm
Data               : {}
Index              : 4203
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                     AzureGateway which has failed. The error code returned on failure is 809.
Source             : RasClient
ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
InstanceId         : 20227
TimeGenerated      : 3/18/2016 1:29:21 PM
TimeWritten        : 3/18/2016 1:29:21 PM
UserName           :
Site               :
Container          :
```

**Est le VPN application routage correctement le trafic via la passerelle VPN Azure ?**

Utilisez un outil tel que [PsPing] [ psping] pour vérifier la connectivité et du routage sur la passerelle VPN. Par exemple, pour tester la connectivité à partir d’un ordinateur local vers un serveur web situé sur le VNet, exécutez la commande suivante (remplacez `<<web-server-address>>` avec l’adresse du serveur web) :

```
PsPing -t <<web-server-address>>:80
```

Si l’ordinateur local peut acheminer le trafic vers le serveur web, vous devez voir une sortie similaire à ce qui suit :

```
D:\PSTools>psping -t 10.20.0.5:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.0.5:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.0.5:80 (warmup): 6.21ms
Connecting to 10.20.0.5:80: 3.79ms
Connecting to 10.20.0.5:80: 3.44ms
Connecting to 10.20.0.5:80: 4.81ms

  Sent = 3, Received = 3, Lost = 0 (0% loss),
  Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
```

Si l’ordinateur local ne peut pas communiquer avec la destination spécifiée, vous verrez les messages comme suit :

```
D:\PSTools>psping -t 10.20.1.6:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.1.6:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80:
  Sent = 3, Received = 0, Lost = 3 (100% loss),
  Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
```

**Le pare-feu local permet-il à passer le trafic VPN ? Les ports corrects ont été ouverts ?**

Vérifiez que l’appareil VPN sur site utilise une méthode de cryptage qui est [compatible avec la passerelle VPN Azure][vpn-appliance].

Routage basé sur la stratégie, la passerelle VPN Azure prend en charge les algorithmes de cryptage AES256, AES128 et 3DES. Les passerelles basées sur l’itinéraire prend en charge AES256 et 3DES.

### <a name="azure-vnet-gateway"></a>Passerelle de VNet Azure :

Examinez les [journaux de diagnostic Azure VPN passerelle] [ gateway-diagnostic-logs] de problèmes potentiels.

**Les Azure VPN passerelle et sur site VPN configurée avec la même clé d’authentification partagée ?**

Vous pouvez afficher la clé partagée est stockée par la passerelle VPN Azure à l’aide de la commande CLI d’Azure suivante :

```
azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
```

Utilisez la commande appropriée pour votre solution de VPN sur site pour afficher la clé partagée est configurée pour cette application.

Vérifiez que le sous-réseau _GatewaySubnet_ maintenant que la passerelle VPN Azure n’est pas associée à un NSG.

Vous pouvez afficher les détails du sous-réseau à l’aide de la commande CLI d’Azure suivante :

```
azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
```

Assurez-vous qu’aucun champ de données ne nommé _id du groupe de sécurité réseau_. L’exemple suivant montre les résultats par exemple de la _GatewaySubnet_ ayant une NSG affectée (_Groupe de passerelle VPN_). Cela peut entraîner des empêcher la passerelle de fonctionner correctement s’il existe des règles définies pour ce NSG :

```
C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
    info:    Executing command network vnet subnet show
    + Looking up virtual network "profx-vnet"
    + Looking up the subnet "GatewaySubnet"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
    data:    Name                            : GatewaySubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.20.3.0/27
    data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
    info:    network vnet subnet show command OK
```

**Les ordinateurs virtuels dans le VNet Azure sont configurés pour autoriser le trafic provenant l’extérieur de la VNet ? Consultez les règles NSG associés aux sous-réseaux contenant ces machines virtuelles.**

Vous pouvez afficher toutes les règles NSG à l’aide de la commande CLI d’Azure suivante :

```
azure network nsg show -g <<resource-group>> -n <<nsg-name>>
```

**La passerelle VPN Azure a été déconnectée ?**

Vous pouvez utiliser la commande Azure PowerShell suivante pour vérifier l’état actuel de la connexion VPN d’Azure. Le `<<connection-name>>` paramètre est le nom de la connexion VPN de Azure qui lie la passerelle de réseau virtuel et de la passerelle locale.

```
Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
```

Les extraits de code suivants en surbrillance la sortie générée si la passerelle est connectée (le premier exemple) et déconnectée (le deuxième exemple) :

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : Connected
EgressBytesTransferred     : 55254803
IngressBytesTransferred    : 32227221
ProvisioningState          : Succeeded
...
```

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : NotConnected
EgressBytesTransferred     : 0
IngressBytesTransferred    : 0
ProvisioningState          : Succeeded
...
```

### <a name="host-vm-configuration-network-bandwidth-utilization-and-application-performance"></a>Configuration de machine virtuelle d’hôte, l’utilisation de la bande passante réseau et les performances de l’application :

Vérifiez que le pare-feu dans le système d’exploitation invité s’exécutant sur des ordinateurs virtuels dans le sous-réseau Azure sont correctement configurés pour autoriser le trafic autorisé à partir de plages IP local.

**Est le volume de trafic proche de la limite de la bande passante disponible vers la passerelle VPN Azure ?**

Outillage dépend des infrastructures disponibles pour votre solution VPN local en cours d’exécution. Par exemple, si vous utilisez le service RRAS de Windows Server 2012, vous pouvez utiliser l’Analyseur de performances à suivre le volume des données reçues et transmises sur la connexion VPN ; à l’aide de l’objet de _Service d’accès distant Total_ , sélectionnez les compteurs _Octets reçus/s_ et des _Octets transmis/s_ :

![[3]][3]

Vous devez comparer les résultats avec la bande passante disponible vers la passerelle VPN (200 Mbits/s pour la haute Performance et 100 Mbits/s de base et les points de stock Standard) :

![[4]][4]

**Un des ordinateurs virtuels dans le VNet Azure fonctionnent lentement ? Sont qu'ils surchargés, sont suffisamment de pouvoir gérer la charge, sont les équilibreurs de charge tout configurés correctement ?**

Cette opération nécessite [la capture et l’analyse des informations de diagnostic][azure-vm-diagnostics]. Vous pouvez examiner les résultats de l’utilisation du portail Azure, mais de nombreux outils tiers sont également disponibles qui peut fournir des informations détaillées dans les données de performance.

**Est l’application efficace d’utiliser les ressources de cloud ?**

Code de l’application instrument en cours d’exécution sur chaque machine virtuelle afin de déterminer si les applications effectuent la meilleure utilisation des ressources. Vous pouvez utiliser des outils tels que des [Idées d’Application] [ application-insights] pour aider à effectuer ces tâches.

## <a name="solution-deployment"></a>Déploiement de la solution

Si vous disposez d’une infrastructure sur site déjà configurée avec une solution de VPN, vous pouvez déployer l’architecture de référence en suivant ces étapes :

1. Cliquez avec le bouton droit de la souris sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déployer vers Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn%2Fazuredeploy.json)

2. Attendez que le lien pour ouvrir le portail Azure, puis procédez comme suit : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : cliquez sur **Créer** et saisissez `ra-hybrid-vpn-rg` dans la zone de texte.
    - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
    - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
    - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement à effectuer.

## <a name="next-steps"></a>Étapes suivantes

- [L’installation de contrôleurs de domaine supplémentaire pour un domaine d’Active Directory sur site à l’aide d’ordinateurs virtuels dans un VNet d’Azure][installing-ad].

- [Instructions pour le déploiement de Windows Server Active Directory sur Azure VM][deploying-ad].

- [Création d’un serveur DNS dans une VNet][creating-dns].

- [Configuration et gestion du système DNS dans un VNet][configuring-dns].

- [À l’aide des appliances de sécurisation du réseau local Stormshield via un VPN][stormshield].

- [Implémentation d’une Architecture de réseau hybride avec Azure ExpressRoute][expressroute].

<!-- links -->

[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[arm-templates]: ../resource-group-authoring-templates.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-virtual-network]: ../virtual-network/virtual-networks-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-network-security-group]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: ../vpn-gateway/vpn-gateway-multi-site.md
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_0/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[create-azure-vnet]: ../virtual-network/virtual-networks-create-vnet-classic-cli.md
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: ../application-insights/app-insights-overview-usage.md
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[vpn-appliances]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[installing-ad]: ../active-directory/active-directory-install-replica-active-directory-domain-controller.md
[deploying-ad]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[creating-dns]: https://blogs.msdn.microsoft.com/mcsuksoldev/2014/03/04/creating-a-dns-server-in-azure-iaas/
[configuring-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[stormshield]: https://azure.microsoft.com/marketplace/partners/stormshield/stormshield-network-security-for-cloud/
[vpn-appliance-ipsec]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec-parameters
[expressroute]: ./guidance-hybrid-network-expressroute.md
[naming conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/deploy-reference-architecture.sh
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetwork.parameters.json
[virtualNetworkGateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetworkGateway.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/blueprints/hybrid-network-vpn.png "Structure d’un hybride réseau couvrant les locaux et les infrastructures de cloud"
[1]: ./media/guidance-hybrid-network-vpn/partitioned-vpn.png "Partitionnement d’un VNet pour améliorer l’évolutivité"
[2]: ./media/guidance-hybrid-network-vpn/audit-logs.png "Journaux d’audit dans le portail Azure"
[3]: ./media/guidance-hybrid-network-vpn/RRAS-perf-counters.png "Compteurs de performance pour surveiller le trafic du réseau VPN"
[4]: ./media/guidance-hybrid-network-vpn/RRAS-perf-graph.png "Exemple de graphique de performances VPN réseau"