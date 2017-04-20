<properties
   pageTitle="Exemple de zone DMZ – créer un réseau de périmètre pour protéger les réseaux avec un pare-feu, UDR et NSG | Microsoft Azure"
   description="Créer un réseau de périmètre avec un pare-feu, défini par l’utilisateur (UDR) de routage et les groupes de sécurité réseau (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemple 3 – Création d’un réseau de périmètre pour protéger les réseaux avec un pare-feu, UDR et NSG

[Revenir à la Page de pratiques meilleure sécurité limite][HOME]

Cet exemple crée un réseau de périmètre avec un pare-feu, quatre serveurs windows, utilisateur défini par le routage, le routage IP et les groupes de sécurité de réseau. Vous y découvrirez également comment chacune des commandes utiles pour fournir une meilleure compréhension de chaque étape. Il existe également une section de scénario de trafic pour fournir une profondeur détaillées comment le trafic se poursuit à travers les couches de défense de la zone DMZ. Enfin, dans les références de la section est le code complet et les instructions pour créer cet environnement pour tester et expérimenter différents scénarios. 

![DMZ bidirectionnelle avec UDR, NSG et à couloirs][1]

## <a name="environment-setup"></a>Configuration de l’environnement
Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

- Trois services en nuage : « SecSvc001 », « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel « CorpNetwork », avec les trois sous-réseaux : « SecNet », « Frontal » et « Back-end »
- Un matériel de réseau virtuel, dans cet exemple, un pare-feu, connecté au sous-réseau SecNet
- Un serveur Windows qui représente un serveur web d’application (« IIS01 »)
- Serveurs de deux fenêtres qui représentent l’application back end serveurs (« AppVM01 », « AppVM02 »)
- Un serveur Windows qui représente un serveur DNS (« DNS01 »)

Dans la section références ci-dessous est un script PowerShell qui générera la majeure partie de l’environnement décrite ci-dessus. Créer les ordinateurs virtuels et les réseaux virtuels, bien que sont effectuées par l’exemple de script, pas décrits en détail dans ce document.

Pour créer l’environnement :

  1.    Enregistrez le fichier xml de configuration réseau inclus dans la section Références (mis à jour avec IP, l’emplacement et les noms qui correspondent à un scénario donné)
  2.    Mettre à jour les variables d’utilisateur dans le script pour correspondre à l’environnement, que le script doit être exécuté sur (les abonnements, les noms de service, etc.)
  3.    Exécutez le script dans PowerShell

**Remarque**: la région signifiée dans le script PowerShell doit correspondre à la région indiquée dans le fichier xml de configuration réseau.

Une fois que le script s’exécute avec succès les étapes suivantes de scripts postérieur à peuvent être prises :

1.  Définir les règles de pare-feu, ce sujet est traité dans la section ci-dessous intitulée : Description de règle de pare-feu.
2.  Si vous le souhaitez dans la section références sont deux scripts pour configurer le serveur web et le serveur d’applications avec une application web simple permettant de tester cette configuration DMZ.

Une fois que le script s’exécute correctement le pare-feu règles devront être effectuées, ce sujet est traité dans la section intitulée : règles de pare-feu.

## <a name="user-defined-routing-udr"></a>Routage (UDR) défini par l’utilisateur
Par défaut, les itinéraires de système suivants sont définis comme :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

Le VNETLocal est toujours l’adresse définie prefix(es) de la VNet pour ce réseau spécifique (ie il passe de VNet à VNet en fonction de la manière dont chaque VNet spécifique est défini). Les itinéraires de système restantes sont statiques et par défaut comme indiqué ci-dessus.

En ce qui concerne la priorité, les itinéraires sont traités via la méthode de correspondance de préfixe plus long (LPM), donc l’itinéraire plus spécifique de la table s’appliquerait à une adresse de destination donné.

Par conséquent, le trafic du (par exemple pour le serveur DNS01, 10.0.2.4) pour le réseau local (10.0.0.0/16) serait routé sur le VNet vers sa destination en raison de la gamme 10.0.0.0/16. En d’autres termes, pour 10.0.2.4, la 10.0.0.0/16 itinéraire est le plus spécifique, même si le 10.0.0.0/8 et 0.0.0.0/0 peuvent s’appliquent également, mais dans la mesure où elles sont moins spécifiques qu’ils n’affectent pas ce trafic. Par conséquent le trafic 10.0.2.4 serait ont un tronçon suivant de le VNet local et simplement acheminer vers la destination.

Si le trafic a été conçu pour 10.1.1.1, par exemple, l’itinéraire 10.0.0.0/16 ne s’appliquent, mais le 10.0.0.0/8 serait la plus spécifique, et le trafic serait supprimée (« dipositif de noir ») dans la mesure où le saut suivant est de valeur Null. 

Si la destination n’a pas appliquer aux préfixes Null ou les préfixes VNETLocal, puis elle suit le moins spécifique Router, 0.0.0.0/0 et être routée vers Internet comme tronçon suivant et ainsi à bord d’internet d’Azure.

S’il y a deux préfixes identiques dans la table de routage, voici l’ordre de préférence, basée sur l’attribut « source » de routes :

1.  « VirtualAppliance » = un itinéraire de définie par l’utilisateur ajouté manuellement à la table
2.  « VPNGateway » = une gamme dynamique BGP (lorsqu’il est utilisé avec les réseaux hybrides), ajouté par un protocole de réseau dynamiques, ces itinéraires peuvent changer avec le temps que le protocole dynamique reflète automatiquement les modifications apportées aux ressources réseau
3.  « Par défaut » = le système et, le VNet local et les entrées statiques, comme indiqué dans la table d’itinéraires.

>[AZURE.NOTE] Vous pouvez maintenant utiliser routage défini par l’utilisateur (UDR) avec ExpressRoute et des passerelles VPN pour forcer entrant et sortant trafic entre les différents locaux d’être acheminé vers une appliance virtuelle du réseau (à couloirs).

#### <a name="creating-the-local-routes"></a>Création d’itinéraires locaux

Dans cet exemple, deux tables de routage sont nécessaires, un pour chacun des sous-réseaux frontaux et back-end. Chaque table est chargée avec des itinéraires statiques appropriés pour le sous-réseau donné. Pour cet exemple, chaque table possède trois routes :

1. Le trafic du sous-réseau local avec aucune zone de tronçon suivant définie pour autoriser le trafic du sous-réseau local à contourner le pare-feu
2. Trafic réseau virtuel avec un tronçon suivant définie en tant que pare-feu, il substitue à la règle par défaut qui autorise le trafic de VNet local d’acheminer directement
3. Tous les autres le trafic (0/0) avec un tronçon suivant définie comme le pare-feu

Une fois les tables de routage sont créés, ils sont liés à leurs sous-réseaux. Pour le sous-réseau frontal, table de routage, une fois créé et lié au sous-réseau doit ressembler à ceci :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Pour cet exemple, les commandes suivantes sont utilisées pour générer la table de routage, ajouter un itinéraire défini par l’utilisateur et puis de lier la table de routage pour un sous-réseau (Remarquez, tous les éléments ci-dessous en commençant par un signe dollar (par exemple : $BESubnet) sont des variables définies par l’utilisateur à partir du script dans la section Références de ce document) :

1.  Tout d’abord la table de routage de base doit être créée. Cet extrait de code illustre la création de la table pour le sous-réseau du serveur principal. Dans le script, une table correspondante est également créée pour le sous-réseau frontal.

        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"

2.  Une fois créée, la table de routage les itinéraires spécifiques définis par l’utilisateur peuvent être ajoutés. Dans cet extrait, tout le trafic (0.0.0.0/0) est routé par le biais de l’appliance virtuelle (une variable, $VMIP [0], est utilisée pour passer l’adresse IP affectée lorsque l’appliance virtuelle créée plus tôt dans le script). Dans le script, une règle correspondante est également créée dans le tableau de la partie frontale.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

3. L’entrée de gamme ci-dessus remplacera l’itinéraire par défaut « 0.0.0.0/0 », mais la règle de 10.0.0.0/16 défaut existant encore qui permettrait le trafic dans le VNet d’acheminer directement vers la destination et pas à l’Appliance virtuelle du réseau. Pour correct ce comportement de la règle suivante doit être ajouté.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

4. À ce stade, il est un choix à effectuer. Avec les deux itinéraires ci-dessus achemine tout le trafic au pare-feu pour évaluation, trafic même au sein d’un sous-réseau unique. Il peut être souhaitable, cependant, autorisent le trafic au sein d’un sous-réseau pour acheminer localement sans l’intervention du pare-feu une troisième, règle très spécifique peut être ajouté. Cet itinéraire indique que n’importe quelle adresse destine pour le sous-réseau local peut juste il route directement (NextHopType = VNETLocal).

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal

5.  Enfin, avec la table de routage créé et rempli avec des itinéraires définis par l’utilisateur, le tableau doit maintenant être lié un sous-réseau. Dans le script, la table de routage de front-end est également liée au sous-réseau frontal. Voici le script de liaison pour le sous-réseau back-end.

        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
           -SubnetName $BESubnet `
           -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>Transfert IP
Fonctionnalité à UDR, est le routage IP. Il s’agit d’un paramètre sur une Appliance virtuelle qui permet de recevoir du trafic non spécifiquement adressé à l’appliance et puis transmettre ce trafic vers sa destination finale.

Par exemple, si le trafic à partir de AppVM01, une demande au serveur DNS01, UDR acheminent ce au pare-feu. Avec le routage IP activé, le trafic à la destination de DNS01 (10.0.2.4) sera accepté par la solution matérielle-logicielle (10.0.0.4), puis transféré vers sa destination finale (10.0.2.4). Sans le routage IP activé sur le pare-feu, le trafic ne serait pas accepté par la solution matérielle-logicielle même si la table de routage possède le pare-feu comme tronçon suivant. 

>[AZURE.IMPORTANT] Il est essentiel de garder à l’esprit activer le routage IP en conjonction avec le routage défini par l’utilisateur.

Configurer le routage IP est une commande unique et peut être effectuée au moment de la création de machine virtuelle. Pour le flux de cet exemple l’extrait de code est vers la fin du script et regroupées avec les commandes UDR :

1.  Appeler l’instance de machine virtuelle qui est dans ce cas de votre appliance virtuelle, le pare-feu et activer le routage IP (Remarquez, à n’importe quel élément en rouge en commençant par un signe dollar (par exemple : $VMName[0]) est une variable définie par l’utilisateur à partir du script dans la section Références de ce document. Le zéro de crochets, [0], représente la première virtuelle dans le tableau des machines virtuelles, pour l’exemple de script à fonctionner sans modification, la premier VM (machine virtuelle 0) doit être le pare-feu) :

        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
           Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Groupes de sécurité réseau (NSG)
Dans cet exemple, un groupe NSG est généré et ensuite chargé avec une seule règle. Ce groupe est ensuite lié uniquement aux sous-réseaux frontaux et back-end (pas le SecNet). Déclarative la règle suivante est en cours de génération :

1.  Tout le trafic (tous les ports) à partir d’Internet à l’ensemble VNet (tous les sous-réseaux) est refusé.

Bien que NSGs sont utilisés dans cet exemple, son principal objectif est comme une couche secondaire de défense contre une mauvaise configuration manuelle. Nous voulons bloquer tout trafic provenant d’internet soit Frontend ou sous-réseaux back-end, le trafic doivent circuler uniquement via le sous-réseau SecNet au pare-feu (et ensuite si approprié sur le site Web frontal ou back-end de sous-réseaux). De plus, avec les règles UDR en place, tout ayant effectué dans les sous-réseaux de système frontal ou back-end serait trafic vers le pare-feu (grâce à UDR). Le pare-feu voit ce sous la forme d’un flux asymétrique et tomberaient du trafic sortant. Par conséquent, il existe trois couches de sécurité protégeant les sous-réseaux frontaux et back-end ; 1) aucun point de terminaison ouvert sur les FrontEnd001 et les BackEnd001 services en nuage, 2) NSGs refuser le trafic provenant d’Internet, 3) le trafic asymétrique de suppression.

Un point intéressant concernant le groupe de sécurité réseau dans cet exemple est qu’il contient une seule règle, illustrée ci-dessous, qui consiste à refuser le trafic internet de l’ensemble du réseau virtuel qui contiendra le sous-réseau de sécurité. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Toutefois, étant donné que le NSG est lié uniquement aux sous-réseaux frontaux et back-end, la règle n’est pas traitée sur le trafic entrant pour le sous-réseau de sécurité. Par conséquent, même si la règle NSG n’indique aucun trafic Internet à n’importe quelle adresse sur le VNet, car le NSG n’était jamais lié au sous-réseau de sécurité, le trafic passera au sous-réseau de sécurité.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName
    
    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Règles de pare-feu
Sur le pare-feu, règles de transfert devra être créé. Étant donné que le pare-feu est le trafic blocage ou transfert de tous les entrant, sortant et intra-VNet de règles de pare-feu sont nécessaires. En outre, tout le trafic entrant sera atteint l’adresse IP publique de Service de sécurité (sur des ports différents), pour être traitées par le pare-feu. Il est recommandé de créer un diagramme de flux logiques avant de configurer les sous-réseaux et les règles de pare-feu pour éviter de reprendre ultérieurement. La figure suivante est une vue logique des règles de pare-feu pour que cet exemple :
 
![Vue logique de règles de pare-feu][2]

>[AZURE.NOTE] En fonction de l’Appliance virtuelle réseau utilisé, les ports de gestion peuvent varier. Dans cet exemple, qu'un pare-feu de NextGen Barracuda est référencé qui utilise les ports 22, 801 et 807. Veuillez consulter la documentation du fournisseur de matériel pour rechercher les ports exacts utilisés pour la gestion de l’appareil utilisé.

### <a name="logical-rule-description"></a>Description de la règle logique
Dans le diagramme logique ci-dessus, le sous-réseau de sécurité n’est pas affiché dans la mesure où le pare-feu est la seule ressource sur ce sous-réseau, et ce diagramme affiche les règles de pare-feu et comment ils logiquement autorisent ou refuser des flux de trafic et pas le chemin routé. En outre, les ports externes sélectionnés pour le trafic RDP sont plus élevés plage de ports (8014 – 8026) et ont été sélectionnés pour aligner un peu avec les deux derniers octets de l’adresse IP locale pour être plus lisibles (par exemple l’adresse de serveur local 10.0.1.4 est associé à un port externe 8014), cependant les ports non conflictuelles supérieurs peuvent être utilisées.

Pour cet exemple, nous devons 7 types de règles, ces types de règles sont décrites comme suit :

- Règles externes (pour le trafic entrant) :
  1.    Règle de gestion de pare-feu : Cette règle App redirection permet le trafic sur les ports de gestion du matériel réseau virtuel.
  2.    Règles de RDP (pour chaque serveur windows) : ces quatre règles (une pour chaque serveur) permet la gestion des serveurs individuels via RDP. Cela pourrait également regroupé dans une règle en fonction des capacités du matériel réseau virtuel en cours d’utilisation.
  3.    Application des règles de trafic : Il y a deux règles du trafic d’Application, la première pour le trafic web front-end et la seconde pour le trafic de back-end (par exemple serveur web au niveau des données). La configuration de ces règles sera dépendent de l’architecture du réseau (où sont placés vos serveurs) et les flux de trafic (direction dans laquelle les flux de trafic et quels ports sont utilisés).
      - La première règle autorisera le trafic d’application réel atteindre le serveur d’application. Tandis que les autres règles de sécurité, de gestion, etc., les règles d’Application sont qu’Autoriser les utilisateurs externes ou des services accéder aux applications. Pour cet exemple, il est un serveur web sur le port 80, donc une règle d’application unique pare-feu redirige le trafic entrant pour l’adresse IP externe, à l’adresse IP interne des serveurs web. La trafic redirigé la session devrait être NAT était vers le serveur interne.
      - La deuxième règle de trafic d’Application est la règle de back-end afin de permettre au serveur Web de communiquer avec le serveur de AppVM01 (mais pas AppVM02) via n’importe quel port.
- Règles internes (pour le trafic d’intra-VNet)
  4.    Sortant à Internet règle : cette règle autorise le trafic à partir de n’importe quel réseau pour transmettre vers les réseaux sélectionnés. Cette règle est généralement une règle par défaut déjà sur le pare-feu, mais dans un état désactivé. Cette règle doit être activée pour cet exemple.
  5.    Règle DNS : Cette règle permet uniquement le trafic DNS (port 53) pour le serveur DNS. Dans cet environnement, la plupart du trafic à partir de la partie frontale sur le serveur principal est bloqué, cette règle permet en particulier DNS à partir de n’importe quel sous-réseau local.
  6.    Règle de sous-réseau pour le sous-réseau : cette règle est de permettre à n’importe quel serveur sur le sous-réseau du back-end pour se connecter à n’importe quel serveur sur le sous-réseau de front-end (mais pas l’inverse).
- Règle de prévention de défaillance (pour le trafic qui ne répond pas à toutes les situations ci-dessus) :
  7.    Refuser toutes les règles de trafic : Cela doit toujours la règle finale (en termes de priorité) et en tant que tel si les flux de trafic de ne correspond pas à une des règles précédentes qu'il est supprimé par cette règle. Il s’agit d’une règle par défaut et aucune modification n’est généralement n’activée, est généralement nécessaires.

>[AZURE.TIP] Sur la deuxième règle de trafic de l’application, n’importe quel port est autorisé pour facile de cet exemple, dans un scénario réel le port plus spécifique et plages d’adresses doivent être utilisés pour réduire la surface d’attaque de cette règle.

<br />

>[AZURE.IMPORTANT] Une fois que toutes les règles ci-dessus sont créées, il est important de réviser la priorité de chaque règle pour assurer le trafic est autorisé ou refusé comme vous le souhaitez. Pour cet exemple, les règles sont dans l’ordre de priorité. Il est facile de verrouillé le pare-feu en raison de règles mal ordonnées. Au minimum, assurent la gestion du pare-feu lui-même la règle absolue de priorité plus élevée.

### <a name="rule-prerequisites"></a>Conditions préalables de règle
Un composant requis pour l’ordinateur virtuel en cours d’exécution du pare-feu sont des points de terminaison publics. Pour le pare-feu traiter le trafic, les points de terminaison publics appropriés doivent être ouverts. Il existe trois types de trafic dans cet exemple ; Trafic RDP 1) le trafic de gestion pour le contrôle du pare-feu et des règles de pare-feu, 2) pour contrôler les serveurs windows et le trafic des applications 3). Voici les trois colonnes de types de trafic dans la partie supérieure la moitié de la vue logique des règles de pare-feu ci-dessus.

>[AZURE.IMPORTANT] Un takeway clé ici est de se souvenir que **tout** le trafic sera communiquée à travers le pare-feu. Donc au Bureau à distance pour le serveur IIS01, bien qu’il soit dans le Service en nuage avant fin et sur le sous-réseau Front-End, pour accéder à ce serveur nous sera nécessaire RDP pour le pare-feu sur le port 8014 et puis permettre au pare-feu acheminer en interne de la demande RDP au Port RDP IIS01. Bouton de « Se connecter » du portail Azure ne fonctionnera pas car il n’y a aucun chemin d’accès direct du RDP pour IIS01 (pour autant que le portail peut voir). Cela signifie que toutes les connexions à partir d’internet sera au Service de sécurité et d’un Port, par exemple, secscv001.cloudapp.net:xxxx (le schéma ci-dessus pour le mappage du Port externe et interne IP et le Port de référence).

Un point de terminaison peut être ouvert soit au moment de la création de la machine virtuelle ou validez la build ainsi est fait dans l’exemple de script ci-dessous dans cet extrait de code (Remarquez, à n’importe quel élément qui commence par un signe dollar (par exemple : $VMName[$i]) est une variable définie par l’utilisateur à partir du script dans la section Références de ce document. Le « $i » entre crochets, [$i], représente le nombre de tableau d’une VM spécifique dans un tableau d’ordinateurs virtuels) :

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Bien que les pas clairement montré ici en raison de l’utilisation de variables, mais les points de terminaison sont **uniquement** ouverts sur le Service en nuage sécurité. Afin de garantir que tout le trafic entrant est géré (routé, du supprimé était, NAT) par le pare-feu.

Gestion client aura besoin doit être installé sur un ordinateur pour gérer le pare-feu et créer les configurations nécessaires. Comment gérer le périphérique, consultez le fournisseur de la documentation de votre pare-feu (ou autres à couloirs). Le reste de cette section et la section suivante, création de règles de pare-feu, décrit la configuration du pare-feu, via le client de gestion de fournisseurs (c'est-à-dire non Azure portal ou PowerShell).

Instructions de téléchargement du client et de la connexion à la Barracuda utilisé dans cet exemple se trouve ici : [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Une fois connecté sur le pare-feu, mais avant de créer des règles de pare-feu, il existe deux classes d’objet requis qui facilitent la création de règles plus facile ; Objets de Service et de réseau.

Pour cet exemple, trois objets de réseau nommé devraient être défini (pour le sous-réseau frontal et le sous-réseau de back-end, également un objet de réseau pour l’adresse IP du serveur DNS). Pour créer un réseau nommé ; à partir du tableau de bord de client Barracuda NG Admin, accédez à l’onglet configuration, dans la section de Configuration opérationnelle, cliquez sur règles, puis cliquez sur « Réseaux » dans le menu objets de pare-feu, puis cliquez sur Nouveau dans le menu Modifier les réseaux. L’objet de réseau peut être créé, en ajoutant le nom et le préfixe :

![Créer un objet de réseau du système frontal][3]
 
Cette opération crée un réseau nommé pour le sous-réseau frontal, un objet semblable doit être créé pour ce sous-réseau back-end. Maintenant les sous-réseaux peuvent être référencées plus facilement par nom dans les règles de pare-feu.

Pour l’objet de serveur DNS :

![Créer un objet de serveur DNS][4]

Cette référence d’adresse IP unique sera utilisée dans une règle de DNS, plus loin dans le document.

Les deuxième objets requis sont les Services. Elles représentent les ports de connexion RDP pour chaque serveur. Dans la mesure où l’objet de service RDP existant est lié au port RDP par défaut, 3389, nouveaux Services peuvent être créés pour autoriser le trafic à partir des ports externes (8014-8026). Les nouveaux ports peuvent également être ajoutés au service RDP existant, mais pour faciliter la démonstration, une règle individuelle pour chaque serveur peut être créée. Pour créer une nouvelle règle de protocole RDP d’un serveur ; à partir du tableau de bord de client Barracuda NG Admin, accédez à l’onglet configuration, dans la section de Configuration opérationnelle, cliquez sur le groupe de règles, puis cliquez sur « Services » dans le menu objets de pare-feu, défiler vers le bas de la liste des services et sélectionnez le service « RDP ». Avec le bouton droit et sélectionnez Copier, puis avec le bouton droit et sélectionnez Coller. Il existe désormais un objet de Service de Copie1-RDP qui peut être modifié. Droit sur RDP-Copie1 et sélectionnez Modifier, l’objet de Service modifier fenêtre s’affiche comme illustré ici :

![Copie de la règle de protocole RDP par défaut][5]

Les valeurs peuvent ensuite être modifiées pour représenter le service RDP pour un serveur spécifique. Pour AppVM01, la règle de protocole RDP par défaut ci-dessus doit être modifiée afin de refléter un nouveau nom de Service, la Description et le Port de protocole RDP externe utilisée dans le diagramme de la Figure 8 (Remarque : les ports sont modifiés à partir de la valeur par défaut 3389 RDP sur le port externe utilisé pour ce serveur spécifique, dans le cas de AppVM01 le Port externe est 8025) le service modifié est présenté ci-dessous :

![Règle de AppVM01][6]
 
Ce processus doit être répété pour créer des Services de RDP sur les autres serveurs ; AppVM02, DNS01 et IIS01. La création de ces Services permettra la création de règles plus simple et le plus évident dans la section suivante.

>[AZURE.NOTE] Un service RDP pour le pare-feu n’est pas nécessaire pour deux raisons. 1) le premier pare-feu VM est une image de Linux en fonction SSH sera utilisé sur la voie 22 pour la gestion de VM au lieu de RDP et le port 2) 22, et deux autres ports de gestion sont autorisés dans la première règle de gestion décrite ci-dessous pour autoriser pour la connectivité de gestion.

### <a name="firewall-rules-creation"></a>Création de règles de pare-feu
Il existe trois types de règles de pare-feu utilisées dans cet exemple, ils disposent tous des icônes distinctes :

La règle d’Application rediriger : ![Icône de rediriger l’Application][7]

La règle NAT de Destination : ![Icône NAT de Destination][8]

La règle passe : ![Passer une icône][9]

Vous trouverez plus d’informations sur ces règles sur le site web de Barracuda.

Pour créer les règles suivantes (ou vérifier les règles par défaut existant), à partir du tableau de bord de client Barracuda NG Admin, accédez à l’onglet configuration, dans la Configuration opérationnelle de section, cliquez sur groupe de règles. « Règles de Main » affiche une grille appelée, les règles existantes d’actifs et désactivés sur ce pare-feu. Dans le coin supérieur droit de cette grille est un petit, vert « + » bouton, cliquez sur ce bouton pour créer une nouvelle règle (Remarque : votre pare-feu peut être « verrouillé » pour les modifications, si vous voyez un bouton marqué « Verrouiller » et que vous ne pouvez pas créer ou modifier des règles, cliquez sur ce bouton pour « déverrouiller » l’ensemble de règles et d’autoriser la modification). Si vous souhaitez modifier une règle existante, sélectionnez cette règle, avec le bouton droit et sélectionnez Modifier la règle.

Une fois vos règles sont créés ou modifiés, ils doivent être diffusées sur le pare-feu et activés puis, si cela n’est pas les modifications de règle ne prendront effet. Le processus de transmission et d’activation est décrite ci-dessous les descriptions de règles de détails.

Les caractéristiques de chaque règle nécessaires à l’exécution de cet exemple sont décrites comme suit :

- **Règle de gestion de pare-feu**: rediriger à cette application la règle autorise le trafic à passer aux ports de gestion du matériel réseau virtuel, dans cet exemple, un pare-feu de NextGen Barracuda. Les ports de gestion sont 801, 807 et éventuellement 22. Les ports internes et externes sont identiques (c'est-à-dire aucune traduction de port). Cette règle, le programme d’installation-gestion-accès, est une règle par défaut et activée par défaut (dans la version 6.1 de Barracuda NextGen pare-feu).

    ![Règle de gestion de pare-feu][10]

>[AZURE.TIP] L’espace d’adresse source dans cette règle est que, si les plages d’adresses IP management sont connus, réduisant cette étendue permettrait également de limiter la surface d’attaque pour les ports de gestion.

- **Règles de RDP**: règles NAT de Destination ces permettra la gestion des serveurs individuels via RDP.
Il existe quatre champs critiques nécessaires à la création de cette règle :
  1.    Source : pour permettre de RDP depuis n’importe où, la référence « Quelconque » est utilisée dans le champ Source.
  2.    Service – utiliser l’objet de Service approprié créé précédemment, dans ce cas « AppVM01 RDP », les ports externes rediriger vers l’adresse IP des serveurs locaux et au port 3386 (le port de protocole RDP par défaut). Cette règle spécifique est pour l’accès RDP pour AppVM01.
  3.    Destination – doit être le *port local sur le pare-feu*, le « IP Local 1 DHCP » ou eth0 si vous utilisez des adresses IP statique. Le nombre ordinal (eth0, eth1, etc.) peut être différent si votre matériel réseau possède plusieurs interfaces locales. C’est le port envoie par le pare-feu à partir de (peut être le même que le port de réception), la destination de routage réelle est dans le champ de la liste cible.
  4.    La redirection – cette section indique l’appliance virtuelle où finalement rediriger ce trafic. La redirection de la plus simple est de placer l’adresse IP et le Port (facultatif) dans le champ de la liste cible. Si aucun port n’est pas utilisé le port de destination de la requête entrante sera utilisé (c'est-à-dire sans traduction), si le port est désigné dans un port sera également NAT serait ainsi que l’adresse IP adresse.

    ![Règle de pare-feu RDP][11]

    Un total de quatre règles RDP sera doivent être créées : 

  	|   Nom de la règle    | Serveur  |   Service   |  Liste cible  |
  	|----------------|---------|-------------|---------------|
  	| RDP pour IIS01   | IIS01   | IIS01 RDP   | 10.0.1.4:3389 |
  	| RDP-à-DNS01   | DNS01   | DNS01 RDP   | 10.0.2.4:3389 |
  	| RDP-à-AppVM01 | AppVM01 | AppVM01 RDP | 10.0.2.5:3389 |
  	| RDP-à-AppVM02 | AppVM02 | AppVm02 RDP | 10.0.2.6:3389 |
  
>[AZURE.TIP] Réduire l’étendue des champs Source et de Service permet de réduire la surface d’attaque. La portée plus limitée qui permettra de fonctionnalité doit être utilisée.

- **Règles de trafic d’applications**: il y a deux règles du trafic d’Application, la première pour le trafic web front-end et la seconde pour le trafic de back-end (par exemple serveur web au niveau des données). Ces règles seront dépendent de l’architecture du réseau (où sont placés vos serveurs) et les flux de trafic (direction dans laquelle les flux de trafic et quels ports sont utilisés).

    Tout d’abord vous trouverez la règle front-end pour le trafic web :

    ![Règle de pare-feu Web][12]
 
    Cette règle NAT de Destination autorise le trafic de l’application réelle atteindre le serveur d’application. Tandis que les autres règles de sécurité, de gestion, etc., les règles d’Application sont qu’Autoriser les utilisateurs externes ou des services accéder aux applications. Pour cet exemple, il est un serveur web sur le port 80, donc la règle d’application unique pare-feu redirige le trafic entrant pour l’adresse IP externe, à l’adresse IP interne des serveurs web.

    **Remarque**: qu’il n’y a aucun port affecté dans le champ de la liste des cibles, donc le port d’entrée 80 (ou 443 pour le Service sélectionné) sera utilisé dans la redirection du serveur web. Si le serveur web est à l’écoute sur un port différent, par exemple le port 8080, le champ de la liste cible peut être mis à jour à 10.0.1.4:8080 pour autoriser ainsi la redirection de Port.

    La règle du trafic de l’Application suivante est la règle back-end afin de permettre au serveur Web de communiquer avec le serveur de AppVM01 (mais pas AppVM02) via n’importe quel service :

    ![Règle de pare-feu AppVM01][13]

    Cette règle passe permet à tout serveur IIS sur le sous-réseau frontal pour atteindre le AppVM01 (adresse IP 10.0.2.5) sur n’importe quel port, à l’aide de n’importe quel protocole pour accéder aux données nécessaires à l’application web.

    Dans cette capture d’écran une «\<explicite dest\>» est utilisé dans le champ de Destination pour indiquer 10.0.2.5 comme destination. Il peut s’agir soit nommé explicite du comme illustré, ou un objet de réseau (comme dans les conditions requises pour le serveur DNS). C’est à l’administrateur du pare-feu qui méthode sera utilisée. Pour ajouter une Explict Desitnation 10.0.2.5, double-cliquez sur la première ligne vide sous \<explicite dest\> et saisissez l’adresse dans la fenêtre qui s’affiche.

    Cette règle passer aucun NAT n’est nécessaire dans la mesure où il s’agit du trafic interne, la méthode de connexion peut être définie sur « No SNAT ».

    **Remarque**: réseau de la Source de cette règle est une ressource quelconque sur le sous-réseau frontal, s’il n’y a un, ou un nombre spécifique connu de serveurs web, une ressource de l’objet réseau pu être créée pour être plus spécifique pour ces adresses IP exactes au lieu du sous-réseau frontal complet.

>[AZURE.TIP] Cette règle utilise le service « Any » pour faciliter l’installation et utilisez l’exemple d’application, cela permettra également de ICMPv4 (ping) dans une seule règle. Toutefois, ce n’est pas une pratique recommandée. Les ports et protocoles (« Services ») doivent être réduites à la valeur minimale possible qui permet le fonctionnement de l’application afin de réduire la surface d’attaque au-delà de cette limite.

<br />

>[AZURE.TIP] Bien que cette règle indique une référence explicite à destination en cours d’utilisation, une approche cohérente doit être utilisée tout au long de la configuration du pare-feu. Il est recommandé d’utiliser l’objet réseau nommé tout au long de la prise en charge et la lisibilité. La destination-explicite est utilisée ici uniquement à montrer une méthode alternative de référence et n’est généralement pas recommandée (en particulier pour des configurations complexes).

- **Règle Internet sortant**: passer ce règle autorise le trafic à partir de n’importe quel réseau Source à passer aux réseaux de Destination sélectionnés. Cette règle est une règle par défaut déjà généralement sur le pare-feu Barracuda NextGen, mais qu’il est dans un état désactivé. Avec le bouton droit sur cette règle peut accéder à la commande Activer la règle. La règle affichée ici a été modifiée pour ajouter les deux sous-réseaux locaux qui ont été créés en tant que références dans la section conditions préalable de ce document à l’attribut de la Source de cette règle.

    ![Règle de trafic sortant de pare-feu][14]

- **Règle de DNS**: règle de passer ce autorise uniquement le trafic DNS (port 53) pour le serveur DNS. Dans cet environnement, la plupart du trafic à partir de la partie frontale sur le serveur principal est bloqué, cette règle permet en particulier DNS.

    ![Règle de pare-feu DNS][15]

    **Remarque**: dans cet écran capture la méthode de connexion est incluse. Cette règle étant pour l’adresse IP interne pour le trafic des adresses IP interne, aucune utilisation n’est requise, cela la méthode de connexion est définie sur « No SNAT » pour cette règle passe.

- **Règle de sous-réseau pour le sous-réseau**: passer ce règle est une règle par défaut qui a été activée et modifiée afin de permettre à n’importe quel serveur sur le sous-réseau du back-end pour se connecter à n’importe quel serveur sur le sous-réseau du front-end. Cette règle est le trafic interne afin que la méthode de connexion peut être définie à No SNAT.

    ![Règle de pare-feu Intra-VNet][16]

    **Remarque**: la case à cocher bidirectionnelle n’est pas activée (et n’est pas vérifiée dans la plupart des règles), ceci est important pour cette règle, dans la mesure où il facilite cette règle « à une seule direction », une connexion peut être lancée à partir du sous-réseau du back-end pour le réseau de front-end, mais pas l’inverse. Si cette case à cocher a été activée, cette règle permettrait et bidirectionnelle trafic, qui, à partir de notre diagramme logique n’est pas souhaité.

- **Règle de trafic tous les refus**: il doit toujours être la dernière règle (en termes de priorité), et en tant que tel si un trafic circule ne correspond à aucune des précédentes de règles il est supprimé par cette règle. Il s’agit d’une règle par défaut et aucune modification n’est généralement n’activée, est généralement nécessaires. 

    ![Règle de refus du pare-feu][17]

>[AZURE.IMPORTANT] Une fois que toutes les règles ci-dessus sont créées, il est important de réviser la priorité de chaque règle pour assurer le trafic est autorisé ou refusé comme vous le souhaitez. Pour cet exemple, les règles sont dans l’ordre qu’ils doivent apparaître dans la grille principale de règles dans le Client de gestion Barracuda de transfert.

## <a name="rule-activation"></a>Activation de règle
Avec l’ensemble de règles modifié à la spécification du schéma logique, le groupe de règles est chargé sur le pare-feu, puis activé.

![Activation de règle de pare-feu][18]
 
Dans le coin supérieur droit du client de gestion sont un groupe de boutons. Cliquez sur le bouton « Envoyer les modifications » pour envoyer les règles modifiées au pare-feu, puis cliquez sur le bouton « Activer ».
 
Avec l’activation de l’ensemble de règles de pare-feu, création de l’environnement cet exemple est terminée.

## <a name="traffic-scenarios"></a>Scénarios de trafic
>[AZURE.IMPORTANT] Un takeway de clé est de garder à l’esprit que **tout** le trafic sera communiquée à travers le pare-feu. Donc au Bureau à distance pour le serveur IIS01, bien qu’il soit dans le Service en nuage avant fin et sur le sous-réseau Front-End, pour accéder à ce serveur nous sera nécessaire RDP pour le pare-feu sur le port 8014 et puis permettre au pare-feu acheminer en interne de la demande RDP au Port RDP IIS01. Bouton de « Se connecter » du portail Azure ne fonctionnera pas car il n’y a aucun chemin d’accès direct du RDP pour IIS01 (pour autant que le portail peut voir). Cela signifie que toutes les connexions à partir d’internet sera au Service de sécurité et d’un Port, par exemple, secscv001.cloudapp.net:xxxx.

Pour ces scénarios, les règles de pare-feu suivantes doivent être en place :

1.  Gestion du pare-feu
2.  RDP pour IIS01
3.  RDP pour DNS01
4.  RDP pour AppVM01
5.  RDP pour AppVM02
6.  Trafic d’application sur le Web
7.  Trafic d’application de AppVM01
8.  Sortant à Internet
9.  Site Web frontal à DNS01
10. Trafic intra-sous-réseau (back-end vers front-end uniquement)
11. Refuser tout

Le groupe de règles de pare-feu possèdent généralement de nombreuses autres règles de plus, les règles sur n’importe quel pare-feu donné aura également les numéros des différents niveaux de priorité que ceux répertoriés ici. Cette liste et les numéros associés consistent à fournir de pertinence entre simplement ces onze règles et la priorité relative entre eux. En d’autres termes ; sur le pare-feu, le « RDP pour IIS01 » peut être le numéro de la règle 5, mais tant qu’il est sous la règle de la « Gestion de pare-feu » et au-dessus de la règle « RDP pour DNS01 » il s’alignerait dans le but de cette liste. La liste vous aide également dans la ci-dessous les scénarios permettant de concision ; par exemple, « FW règle 9 (DNS) ». Également par souci de concision, les quatre règles RDP sera collectivement appelée, « règles RDP » lorsque le scénario du trafic n’est pas lié à RDP.

Rappelez-vous également que les groupes de sécurité de réseau sont en place pour le trafic internet entrant sur les sous-réseaux frontaux et back-end.

#### <a name="allowed-internet-to-web-server"></a>(Autorisé) Internet au serveur Web
1.  Page de Internet utilisateur demandes HTTP à partir de SecSvc001.CloudApp.Net (face Cloud Service Internet)
2.  Cloud service passe le trafic via le point de terminaison ouvert sur le port 80 pour l’interface du pare-feu sur 10.0.0.4:80
3.  Aucun NSG affectée au sous-réseau de sécurité, ce système NSG règles autorisent le trafic au pare-feu
4.  Le trafic atteint une adresse IP interne du pare-feu (10.0.1.4)
5.  Pare-feu commence le traitement de la règle :
  1.    FW règle 1 (gestion du Firmware) ne s’appliquent, passer à la règle suivante
  2.    Règles (règles de RDP) de 2-5 ne s’appliquent, passer à la règle suivante
  3.    FW règle 6 (application : Web) s’applique, le trafic est autorisé, un pare-feu NAT à 10.0.1.4 (IIS01)
6.  Le sous-réseau frontal commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’applique pas (ce trafic a été NAT serait par le pare-feu, l’adresse source est donc maintenant le pare-feu qui se trouve sur le sous-réseau de sécurité et affichées par le sous-réseau frontal NSG à trafic « local » et est donc autorisé), déplacer vers la règle suivante
  2.    Règles du NSG par défaut autoriser le trafic du sous-réseau pour le sous-réseau, le trafic est autorisé, d’arrêter le traitement des règles NSG
7.  IIS01 est à l’écoute du trafic web, reçoit cette demande et commence le traitement de la demande
8.  IIS01 tente lance une session FTP à AppVM01 sur le sous-réseau du serveur principal
9.  L’itinéraire UDR Frontend sous-réseau rend le pare-feu le saut suivant
10. Aucune règle sortante sur le sous-réseau du site Web frontal, le trafic est autorisé.
11. Pare-feu commence le traitement de la règle :
  1.    FW règle 1 (gestion du Firmware) ne s’appliquent, passer à la règle suivante
  2.    Règle FW (règles de RDP) de 2-5 ne s’appliquent, atteindre la règle suivante
  3.    FW règle 6 (application : Web) ne s’appliquent pas, atteindre la règle suivante
  4.    FW règle 7 (App : back-end) s’applique, le trafic est autorisé, le pare-feu transmet le trafic à 10.0.2.5 (AppVM01)
12. Le sous-réseau de back-end commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, passer à la règle suivante
  2.    Règles du NSG par défaut autoriser le trafic du sous-réseau pour le sous-réseau, le trafic est autorisé, d’arrêter le traitement des règles NSG
13.  AppVM01 reçoit la demande et lance la session et répond
14. L’itinéraire UDR sur le sous-réseau du back-end rend le pare-feu le saut suivant
15. Dans la mesure où il n’y a aucune règle NSG sortant sur le sous-réseau du back-end la réponse n’est autorisé.
16. Dans la mesure où cela retourne le trafic sur une session établie le pare-feu transmet la réponse sur le serveur web (IIS01)
17. Site Web frontal sous-réseau commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, passer à la règle suivante
  2.    Règles du NSG par défaut autoriser le trafic du sous-réseau pour le sous-réseau, le trafic est autorisé, d’arrêter le traitement des règles NSG
18. Le serveur IIS reçoit la réponse, effectue la transaction en AppVM01 et puis termine la création de la réponse HTTP, cette réponse HTTP est envoyée au demandeur
19. Dans la mesure où il n’y a aucune règle NSG sortant sur le sous-réseau frontal la réponse n’est autorisé.
20. La réponse HTTP atteint le pare-feu et dans la mesure où il s’agit de la réponse à une session de NAT établie est acceptée par le pare-feu
21. Le pare-feu redirige ensuite la réponse à l’utilisateur d’Internet
22. Puisqu’il n’existe pas sortant NSG règles ou tronçons UDR sur le sous-réseau frontal la réponse est autorisée et l’utilisateur Internet reçoit la page web demandée.

#### <a name="allowed-internet-rdp-to-backend"></a>(Autorisé) Internet RDP au back-end
1.  Administrateur de serveur sur internet demande session RDP pour AppVM01 de via SecSvc001.CloudApp.Net:8025, où 8025 est le numéro de port utilisateur affecté pour la règle de pare-feu « RDP pour AppVM01 »
2.  Le service en nuage transmet le trafic à travers le point de terminaison ouvert sur le port 8025 pour l’interface du pare-feu sur 10.0.0.4:8025
3.  Aucun NSG affectée au sous-réseau de sécurité, ce système NSG règles autorisent le trafic au pare-feu
4.  Pare-feu commence le traitement de la règle :
  1.    FW règle 1 (gestion du Firmware) ne s’appliquent, passer à la règle suivante
  2.    FW règle 2 (RDP IIS) ne s’appliquent, passer à la règle suivante
  3.    FW règle 3 (RDP DNS01) ne s’appliquent, passer à la règle suivante
  4.    FW règle 4 (RDP AppVM01) s’applique, le trafic est autorisé, un pare-feu NAT à 10.0.2.5:3386 (port RDP sur AppVM01)
5.  Le sous-réseau de back-end commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’applique pas (ce trafic a été NAT serait par le pare-feu, l’adresse source est donc maintenant le pare-feu qui se trouve sur le sous-réseau de sécurité et affichées par le sous-réseau back-end NSG à trafic « local » et est donc autorisé), déplacer vers la règle suivante
  2.    Règles du NSG par défaut autoriser le trafic du sous-réseau pour le sous-réseau, le trafic est autorisé, d’arrêter le traitement des règles NSG
6.  AppVM01 est à l’écoute du trafic RDP et répond
7.  Avec aucune règle NSG sortants, les règles par défaut s’appliquent et le trafic de retour est autorisé
8.  UDR achemine le trafic sortant vers le pare-feu comme tronçon suivant
9.  Dans la mesure où cela retourne le trafic sur une session établie le pare-feu transmet la réponse à l’utilisateur d’internet
10. Session RDP est activée.
11. AppVM01 demande de mot de passe utilisateur

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Autorisé) Recherche de DNS du serveur Web sur le serveur DNS
1.  Web serveur, IIS01, besoins de flux de données à www.data.gov, mais doit résoudre l’adresse.
2.  La configuration du réseau pour les listes de VNet DNS01 (10.0.2.4 sur le sous-réseau du back-end) comme serveur DNS principal, IIS01 envoie la requête DNS pour DNS01
3.  UDR achemine le trafic sortant vers le pare-feu comme tronçon suivant
4.  Aucune règle NSG sortant n’est liés au sous-réseau frontal, le trafic est autorisé.
5.  Pare-feu commence le traitement de la règle :
  1.    FW règle 1 (gestion du Firmware) ne s’appliquent, passer à la règle suivante
  2.    Règle FW (règles de RDP) de 2-5 ne s’appliquent, atteindre la règle suivante
  3.    FW règles 6 & 7 (règles d’application) ne s’appliquent, passer à la règle suivante
  4.    FW règle 8 (sur Internet) ne s’appliquent, passer à la règle suivante
  5.    FW règle 9 (DNS) s’applique, le trafic est autorisé, le pare-feu transfère le trafic vers 10.0.2.4 (DNS01)
6.  Le sous-réseau de back-end commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, passer à la règle suivante
  2.    Règles du NSG par défaut autoriser le trafic du sous-réseau pour le sous-réseau, le trafic est autorisé, d’arrêter le traitement des règles NSG
7.  Serveur DNS reçoit la requête
8.  Serveur DNS ne possède pas l’adresse mise en cache et demande à un serveur DNS racine sur internet
9.  UDR achemine le trafic sortant vers le pare-feu comme tronçon suivant
10. Aucune règle NSG sortant sur le sous-réseau du back-end, le trafic est autorisé.
11. Pare-feu commence le traitement de la règle :
  1.    FW règle 1 (gestion du Firmware) ne s’appliquent, passer à la règle suivante
  2.    Règle FW (règles de RDP) de 2-5 ne s’appliquent, atteindre la règle suivante
  3.    FW règles 6 & 7 (règles d’application) ne s’appliquent, passer à la règle suivante
  4.     FW règle 8 (sur Internet) s’applique, le trafic est autorisé, session SNAT out au serveur DNS de racine sur Internet
12. Serveur DNS Internet répond, dans la mesure où cette session a été ouverte à partir du pare-feu, la réponse est acceptée par le pare-feu
13. Comme il s’agit d’une session établie, le pare-feu transmet la réponse pour le serveur d’origine, DNS01
14. Le sous-réseau de back-end commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, passer à la règle suivante
  2.    Règles du NSG par défaut autoriser le trafic du sous-réseau pour le sous-réseau, le trafic est autorisé, d’arrêter le traitement des règles NSG
15. Le serveur DNS reçoit et met en cache la réponse et répond ensuite à la demande initiale à IIS01
16. L’itinéraire UDR sur le sous-réseau du back-end rend le pare-feu le saut suivant
17. Il n’existe aucune règle NSG sortant sur le sous-réseau du back-end, le trafic est autorisé.
18. Il s’agit d’une session établie sur le pare-feu, la réponse est transmise par le pare-feu sur le serveur IIS
19. Site Web frontal sous-réseau commence le traitement de la règle de trafic entrant :
  1.    Il n’y a aucune règle NSG qui s’applique à l’entrée le trafic du sous-réseau du serveur principal pour le sous-réseau frontal, donc aucun de du NSG règles s’appliquent
  2.    La règle du système par défaut autorisant le trafic entre sous-réseaux permettrait à ce trafic afin que le trafic est autorisé.
20. IIS01 reçoit la réponse DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Autorisé) Serveur principal au serveur frontal
1.  Un administrateur connecté à AppVM02 de via RDP demande un fichier directement depuis le serveur IIS01 via l’Explorateur windows
2.  L’itinéraire UDR sur le sous-réseau du back-end rend le pare-feu le saut suivant
3.  Dans la mesure où il n’y a aucune règle NSG sortant sur le sous-réseau du back-end la réponse n’est autorisé.
4.  Pare-feu commence le traitement de la règle :
  1.    FW règle 1 (gestion du Firmware) ne s’appliquent, passer à la règle suivante
  2.    Règle FW (règles de RDP) de 2-5 ne s’appliquent, atteindre la règle suivante
  3.    FW règles 6 & 7 (règles d’application) ne s’appliquent, passer à la règle suivante
  4.    FW règle 8 (sur Internet) ne s’appliquent, passer à la règle suivante
  5.    FW règle 9 (DNS) ne s’appliquent, passer à la règle suivante
  6.    FW règle 10 (Intra-sous-réseau) s’applique, le trafic est autorisé, pare-feu transmet le trafic à 10.0.1.4 (IIS01)
5.  Site Web frontal sous-réseau commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, passer à la règle suivante
  2.    Règles du NSG par défaut autoriser le trafic du sous-réseau pour le sous-réseau, le trafic est autorisé, d’arrêter le traitement des règles NSG
6.  En supposant que d’autorisation et authentification appropriée, IIS01 accepte la demande et répond
7.  L’itinéraire UDR Frontend sous-réseau rend le pare-feu le saut suivant
8.  Dans la mesure où il n’y a aucune règle NSG sortant sur le sous-réseau frontal la réponse n’est autorisé.
9.  Comme il s’agit d’une session existante sur le pare-feu cette réponse est autorisée et que le pare-feu renvoie la réponse au AppVM02
10. Sous-réseau de back-end commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, passer à la règle suivante
  2.    Règles du NSG par défaut autoriser le trafic du sous-réseau pour le sous-réseau, le trafic est autorisé, d’arrêter le traitement des règles NSG
11. AppVM02 reçoit la réponse

#### <a name="denied-internet-direct-to-web-server"></a>(Refusé) Internet directement vers le serveur Web
1.  Utilisateur Internet tente d’accéder au serveur de web, IIS01, via le service de FrontEnd001.CloudApp.Net
2.  Dans la mesure où il n’y a aucun point de terminaison ouvert pour le trafic HTTP, ce ne serait pas passer à travers le Service de Cloud et n’atteindre le serveur
3.  Si les points de terminaison ont été ouverts pour une raison quelconque, le NSG (bloc Internet) sur le sous-réseau frontal peut bloquer ce trafic
4.  Enfin, la gamme UDR de sous-réseau frontal envoie tout le trafic sortant à partir de IIS01 au pare-feu comme tronçon suivant, et le pare-feu doit voir ce que le trafic asymétrique et supprimer la réponse sortante, par conséquent, il existe au moins trois couches indépendantes de défense entre internet et IIS01 via son service de cloud empêchant l’accès non autorisé/inapproprié.

#### <a name="denied-internet-to-backend-server"></a>(Refusé) Internet pour le serveur principal
1.  Utilisateur Internet tente d’accéder à un fichier sur AppVM01 via le service de BackEnd001.CloudApp.Net
2.  Dans la mesure où il n’y a aucun point de terminaison ouvert pour le partage de fichiers, cela ne serait pas passer le Service en nuage et n’aurait pas d’atteindre le serveur
3.  Si les points de terminaison ont été ouverts pour une raison quelconque, le NSG (bloc Internet) peut bloquer ce trafic
4.  Enfin, l’itinéraire UDR envoie tout le trafic sortant à partir de AppVM01 pour le pare-feu que le tronçon suivant, et le pare-feu doit voir ce que le trafic asymétrique et supprimer la réponse sortante, par conséquent, il existe au moins trois couches indépendantes de défense entre internet et AppVM01 de via son service de cloud empêchant l’accès non autorisé/inapproprié.

#### <a name="denied-frontend-server-to-backend-server"></a>(Refusé) Serveur frontal au serveur principal
1.  Supposons que IIS01 a été compromise et de code malveillant tentent d’analyser les serveurs du sous-réseau back-end est en cours d’exécution.
2.  L’itinéraire UDR de sous-réseau frontal envoie tout le trafic sortant à partir de IIS01 au pare-feu comme tronçon suivant. Ce n’est pas quelque chose qui peut être modifiée par la machine virtuelle compromise.
3.  Le pare-feu doit traiter le trafic, si la demande a été pour AppVM01 ou pour le serveur DNS pour les recherches DNS, que le trafic pourrait potentiellement être autorisé par le pare-feu (en raison de FW règles 7 et 9). Tout autre trafic serait bloqué par FW règle 11 (refuser tous).
4.  Si les avancées de détection de menace a été activée sur le pare-feu (qui n’est pas abordé dans ce document, reportez-vous à la documentation du fournisseur de votre matériel réseau avancées de menace), le même trafic qui serait autorisé par les règles de transmission de base abordées dans ce document ne soient pas si le trafic contenue des signatures connues ou des motifs qui signale une règle avancée.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Refusé) Recherche DNS Internet sur le serveur DNS
1.  Utilisateur Internet essaie de rechercher un enregistrement DNS interne sur DNS01 via le service de BackEnd001.CloudApp.Net 
2.  Dans la mesure où il n’y a aucun point de terminaison ouvert pour le trafic DNS, cela passe pas par le Service en nuage et n’atteindre le serveur
3.  Si les points de terminaison ont été ouverts pour une raison quelconque, la règle NSG (bloc Internet) sur le sous-réseau frontal peut bloquer ce trafic
4.  Enfin, l’itinéraire UDR de sous-réseau back-end envoie tout le trafic sortant à partir de DNS01 pour le pare-feu que le tronçon suivant, et le pare-feu doit voir ce que le trafic asymétrique et supprimer la réponse sortante, par conséquent, il existe au moins trois couches indépendantes de défense entre internet et DNS01 de via son service de cloud empêchant l’accès non autorisé/inapproprié.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Refusé) Internet à accès SQL par l’intermédiaire de pare-feu
1.  Utilisateur Internet demande les données SQL à partir de SecSvc001.CloudApp.Net (face Cloud Service Internet)
2.  Dans la mesure où il n’y a aucun point de terminaison ouvert pour SQL, cela ne serait pas passer le Service en nuage et n’aboutiraient le pare-feu
3.  Si les points de terminaison SQL étaient ouverts pour une raison quelconque, le pare-feu serait commencer le traitement de la règle :
  1.    FW règle 1 (gestion du Firmware) ne s’appliquent, passer à la règle suivante
  2.    Règles (règles de RDP) de 2-5 ne s’appliquent, passer à la règle suivante
  3.    FW règle 6 & 7 (règles d’Application) ne s’appliquent, passer à la règle suivante
  4.    FW règle 8 (sur Internet) ne s’appliquent, passer à la règle suivante
  5.    FW règle 9 (DNS) ne s’appliquent, passer à la règle suivante
  6.    FW règle 10 (Intra-sous-réseau) ne s’appliquent, passer à la règle suivante
  7.    FW règle 11 (refuser tous) s’applique, le trafic est le traitement de la règle de blocage, arrêter


## <a name="references"></a>Références
### <a name="main-script-and-network-config"></a>Script principal et la configuration du réseau
Enregistrer le Script complet dans un fichier de script PowerShell. Enregistrer la configuration du réseau dans un fichier nommé « NetworkConf2.xml ».
Modifiez les variables définies par l’utilisateur si nécessaire. Exécutez le script, puis suivez les instructions d’installation pare-feu règle ci-dessus.

#### <a name="full-script"></a>Script complet
Ce script va, basé sur les variables définies par l’utilisateur :

1.  Se connecter à un abonnement Azure
2.  Créer un nouveau compte de stockage
3.  Créer un nouveau VNet et trois sous-réseaux tel que défini dans le fichier de configuration de réseau
4.  Créer cinq ordinateurs virtuels ; 1 pare-feu et 4 windows server VM
5.  Configurer UDR, y compris :
  1.    Création de deux nouvelles tables de routage
  2.    Ajoutez des itinéraires dans les tables
  3.    Lier des tables pour les sous-réseaux appropriés
6.  Activer le routage IP sur l’à couloirs
7.  Configurer NSG, y compris :
  1.    Création d’un NSG
  2.    Ajout d’une règle
  3.    Liaison du NSG aux sous-réseaux appropriés

Ce script PowerShell doit être exécuté localement sur qu'un internet connecté PC ou serveur.

>[AZURE.IMPORTANT] Lorsque ce script est exécuté, il peut être avertissements et autres messages d’information qui apparaît dans PowerShell. Seuls les messages erreur en rouge sont un motif de préoccupation.

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
    
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.
    
      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4
     
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"
    
    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #
    
      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }
    
    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan
    
      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"
    
      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal
    
      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName
    
     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
    
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
    

#### <a name="network-config-file"></a>Fichier de configuration de réseau
Enregistrer ce fichier xml avec l’emplacement de mise à jour et ajouter le lien vers ce fichier à la variable $NetworkConfigFile dans le script ci-dessus.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Exemples de Scripts Application
Si vous souhaitez installer un exemple d’application pour ce, ainsi que d’autres exemples de DMZ, une a été fournie à l’adresse suivante : [Exemple de Script de l’Application][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "DMZ bidirectionnelle avec UDR, NSG et à couloirs"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Vue logique de règles de pare-feu"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Créer un objet de réseau du système frontal"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Créer un objet de serveur DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Copie de la règle de protocole RDP par défaut"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Règle de AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Icône de rediriger l’application"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Icône NAT de destination"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Icône"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Règle de gestion de pare-feu"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Règle de pare-feu RDP"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Règle de pare-feu Web"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Règle de pare-feu AppVM01"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Règle de trafic sortant de pare-feu"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Règle de pare-feu DNS"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Règle de pare-feu Intra-VNet"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Règle de refus du pare-feu"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Activation de règle de pare-feu"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
