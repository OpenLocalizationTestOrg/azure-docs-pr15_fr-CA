<properties 
   pageTitle="Connexion hybride avec les applications de niveau 2 | Microsoft Azure"
   description="Apprenez à déployer des appliances virtuelles et UDR pour créer un environnement à plusieurs niveaux application dans Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/05/2016"
   ms.author="jdial" />

# <a name="virtual-appliance-scenario"></a>Scénario de l’appliance virtuelle

Un scénario courant, parmi les plus grand client Azure est la nécessité de fournir une application à deux niveaux est exposée à Internet, tout en autorisant l’accès à la couche arrière à partir d’un centre de données sur site. Ce document vous guidera dans un scénario à l’aide d’itinéraires de défini par l’utilisateur (UDR), une passerelle VPN et équipements de réseau virtuel pour déployer un environnement à deux niveaux qui remplit les conditions suivantes :

- Application Web doit être accessible à partir de l’Internet public uniquement.
- Serveur Web hébergeant l’application doit être en mesure d’accéder à un serveur d’application back-end.
- Tout le trafic Internet vers l’application web doit passer par une appliance virtuelle de pare-feu. Cette appliance virtuelle sera utilisée pour le trafic Internet uniquement.
- Tout le trafic vers le serveur d’application doit passer par une appliance virtuelle de pare-feu. Cette appliance virtuelle sera utilisée pour l’accès au serveur principal fin et l’accès entrant à partir du réseau local via une passerelle VPN.
- Les administrateurs doivent être en mesure de gérer les appliances virtuelles de pare-feu à partir de leurs ordinateurs locaux, à l’aide d’un troisième pare-feu matériel virtuel est utilisé exclusivement à des fins de gestion.

Il s’agit d’un scénario de DMZ standard avec un réseau de périmètre et un réseau protégé. Ce scénario peut être construit dans Azure à l’aide de NSGs, les appliances virtuelles de pare-feu ou une combinaison des deux. Le tableau ci-dessous illustre certains des avantages et inconvénients entre NSGs et les appliances virtuelles de pare-feu.

||Professionnels de l’informatique|Inconvénients|
|---|---|---|
|NSG|Aucun coût. <br/>Intégré dans Azure RBAC. <br/>Les règles peuvent être créées dans les modèles ARM.|La complexité peut varier dans les environnements étendus. |
|Pare-feu|Contrôle total sur le plan des données. <br/>Gestion centralisée via une console de pare-feu.|Coût de la solution matérielle-logicielle de pare-feu. <br/>Pas intégré avec Azure RBAC.|

La solution ci-dessous utilise des appliances virtuelles des pare-feu pour implémenter un scénario de réseau DMZ/protégé.

## <a name="considerations"></a>Considérations relatives à la

Vous pouvez déployer l’environnement expliqué ci-dessus dans Azure à l’aide des différentes fonctionnalités disponibles aujourd'hui, comme suit.

- **Le réseau virtuel (VNet)**. Un VNet Azure se comporte de la même façon à un réseau local et peut être subdivisé en un ou plusieurs sous-réseaux pour fournir l’isolation du trafic et la séparation des intérêts.
- **Solution matérielle-logicielle virtuelle**. Plusieurs partenaires fournissent des appliances virtuelles sur le marché d’Azure qui peut être utilisé pour les trois pare-feu décrits ci-dessus. 
- **Itinéraires (UDR) défini par l’utilisateur**. UDRs utilisés par le réseau Azure pour contrôler le flux de paquets dans un VNet peuvent contenir des tables de routage. Ces tables de routage peuvent être appliqués aux sous-réseaux. Une des fonctionnalités plus récentes dans Azure est la possibilité d’appliquer une table de routage pour le GatewaySubnet, ce qui permet de transférer tout le trafic dans les VNet d’Azure à partir d’une connexion hybride à une appliance virtuelle.
- **Le routage IP**. Par défaut, le moteur de mise en réseau Azure transférer des paquets de cartes d’interface réseau virtuelle (NIC) uniquement si l’adresse IP de destination du paquet correspond à l’adresse IP de NIC. Par conséquent, si un UDR définit qu’un paquet doit être envoyé à une appliance virtuelle donnée, le moteur de mise en réseau Azure tomberaient de paquet. Pour garantir que le paquet est transmis à une machine virtuelle (dans ce cas une appliance virtuelle) qui n’est pas la destination réelle du paquet, vous devez activer le routage IP de l’appliance virtuelle.
- **Les groupes de sécurité réseau (NSGs)**. L’exemple ci-dessous ne fait pas usage de NSGs, mais vous pouvez utiliser NSGs appliqués aux sous-réseaux et/ou aux cartes réseau dans cette solution pour filtrer davantage le trafic et les sous-réseaux et les cartes réseau.


![Connectivité IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

- 2 groupes de ressources, ne pas illustrés dans le diagramme. 
    - **ONPREMRG**. Contient toutes les ressources nécessaires pour simuler un réseau local.
    - **AZURERG**. Contient toutes les ressources nécessaires pour l’environnement de réseau virtuel Azure. 
- Un VNet est nommé **onpremvnet** , utilisé pour imiter un centre de données sur site segmentée comme indiqué ci-dessous.
    - **onpremsn1**. Sous-réseau contenant une machine virtuelle (VM) exécutant Ubuntu pour imiter un serveur local.
    - **onpremsn2**. Sous-réseau contenant une machine virtuelle exécutant Ubuntu pour simuler un ordinateur local utilisé par un administrateur.
- Il existe une appliance virtuelle de pare-feu nommée **OPFW** sur **onpremvnet** permet de maintenir un tunnel vers **azurevnet**.
- Un VNet nommé **azurevnet** segmentée comme indiqué ci-dessous.
    - **azsn1**. Sous-réseau de pare-feu externe utilisé exclusivement pour le pare-feu externe. Tout le trafic Internet arriveront à ce sous-réseau. Ce sous-réseau contient uniquement une carte réseau liée au pare-feu externe.
    - **azsn2**. Sous-réseau frontal hébergeant un ordinateur virtuel en cours d’exécution sous la forme d’un serveur web qui est accessibles à partir d’Internet.
    - **azsn3**. Sous-réseau principal hébergeant un ordinateur virtuel qui exécute un serveur application back-end accessible par le serveur web frontal.
    - **azsn4**. Sous-réseau de gestion utilisé exclusivement pour fournir l’accès à toutes les appliances virtuelles de pare-feu. Ce sous-réseau contient uniquement une carte réseau pour chaque appliance virtuelle de pare-feu utilisé dans la solution.
    - **GatewaySubnet**. Sous-réseau de connexion Azure hybride permettant de ExpressRoute et de passerelle VPN pour fournir la connectivité entre les VNets d’Azure et d’autres réseaux. 
- Il existe 3 boîtiers virtuels de pare-feu sur le réseau **azurevnet** . 
    - **AZF1**. Pare-feu externe exposé à Internet public à l’aide d’une ressource d’adresse IP publique dans Azure. Vous devez vous assurer de que vous disposez d’un modèle à partir du marché, ou directement auprès de votre fournisseur de matériel, ces dispositions une appliance virtuelle 3-carte d’interface réseau.
    - **AZF2**. Pare-feu interne utilisé pour contrôler le trafic entre **azsn2** et **azsn3**. C’est également une appliance virtuelle 3-carte d’interface réseau.
    - **AZF3**. Pare-feu de gestion accessible aux administrateurs du centre de données sur site et connecté à un sous-réseau de gestion permet de gérer tous les équipements du pare-feu. Vous pouvez trouver des modèles de l’appliance virtuelle 2-carte d’interface réseau sur le marché, ou la demande directement par votre fournisseur de matériel.

## <a name="user-defined-routing-udr"></a>Routage (UDR) défini par l’utilisateur

Chaque sous-réseau dans Azure peut être lié à une table UDR utilisée pour définir comment le trafic initié sous-réseau est routées. Si aucune UDRs ne sont définis, Azure utilise les itinéraires par défaut pour autoriser le trafic d’un sous-réseau à un autre. Pour mieux comprendre les UDRs, consultez [Quels sont les itinéraires définis par l’utilisateur et le routage IP](./virtual-networks-udr-overview.md#ip-forwarding).

Pour garantir la communication s’effectue par le biais de l’appliance de pare-feu approprié, en fonction de la dernière demande ci-dessus, vous devez créer la table d’itinéraires suivante contenant UDRs dans **azurevnet**.

### <a name="azgwudr"></a>azgwudr

Dans ce scénario, le seul trafic transitant sur site vers Azure sera utilisé pour gérer le pare-feu en vous connectant à **AZF3**, et que le trafic doit passer par le pare-feu interne, **AZF2**. Par conséquent, qu’une seule route est nécessaire dans le **GatewaySubnet** comme indiqué ci-dessous.

|Destination|Tronçon suivant|Explication|
|---|---|---|
|10.0.4.0/24|10.0.3.11|Permet au trafic local atteindre le pare-feu de gestion **AZF3**|

### <a name="azsn2udr"></a>azsn2udr

|Destination|Tronçon suivant|Explication|
|---|---|---|
|10.0.3.0/24|10.0.2.11|Autorise le trafic vers le sous-réseau du back-end qui héberge le serveur d’applications par le biais de **AZF2**|
|0.0.0.0/0|10.0.2.10|Autorise tout autre trafic d’être routé par l’intermédiaire de **AZF1**|

### <a name="azsn3udr"></a>azsn3udr

|Destination|Tronçon suivant|Explication|
|---|---|---|
|10.0.2.0/24|10.0.3.10|Autorise le trafic vers les **azsn2** de circuler à partir du serveur d’applications pour le serveur Web par l’intermédiaire de **AZF2**|

Vous devez également créer des tables de routage pour les sous-réseaux dans **onpremvnet** pour imiter le centre de données sur site.

### <a name="onpremsn1udr"></a>onpremsn1udr

|Destination|Tronçon suivant|Explication|
|---|---|---|
|192.168.2.0/24|192.168.1.4|Autorise le trafic à **onpremsn2** via **OPFW**|

### <a name="onpremsn2udr"></a>onpremsn2udr

|Destination|Tronçon suivant|Explication|
|---|---|---|
|10.0.3.0/24|192.168.2.4|Autorise le trafic du sous-réseau sauvegardé dans Azure via **OPFW**|
|192.168.1.0/24|192.168.2.4|Autorise le trafic à **onpremsn1** via **OPFW**|

## <a name="ip-forwarding"></a>Transfert IP 

UDR et le routage IP sont des fonctionnalités que vous pouvez utiliser conjointement pour permettre à des boîtiers virtuels à utiliser pour contrôler le flux de trafic dans un VNet d’Azure.  Une appliance virtuelle n’est rien de plus qu’un ordinateur virtuel qui exécute une application permet de gérer le trafic réseau d’une certaine manière, par exemple un pare-feu ou un périphérique NAT.

Cette appliance virtuelle VM doit être capable de recevoir le trafic entrant qui n’est pas adressé à lui-même. Pour permettre à un ordinateur virtuel recevoir le trafic adressé vers d’autres destinations, vous devez activer le routage IP de la machine virtuelle. Il s’agit d’un paramètre, pas un système d’exploitation invité de Azure. Votre appliance virtuelle doit toujours s’exécuter tout type d’application à gérer le trafic entrant et l’acheminer correctement.

Pour plus d’informations sur le routage IP, consultez [Quels sont les itinéraires définis par l’utilisateur et le routage IP](./virtual-networks-udr-overview.md#ip-forwarding).

Par exemple, imaginez que vous avez la configuration suivante dans un vnet Azure :

- Sous-réseau **onpremsn1** contient un ordinateur virtuel nommé **onpremvm1**.
- Sous-réseau **onpremsn2** contient un ordinateur virtuel nommé **onpremvm2**.
- Une appliance virtuelle nommée **OPFW** est connectée à **onpremsn1** et **onpremsn2**.
- Un itinéraire défini par l’utilisateur lié à **onpremsn1** Spécifie que tout le trafic vers les **onpremsn2** doit être envoyé à **OPFW**.

À ce stade, si **onpremvm1** tente d’établir une connexion avec **onpremvm2**, le UDR sera utilisé et le trafic sera envoyé à **OPFW** comme tronçon suivant. Gardez à l’esprit que la destination du paquet réelle n’est pas modifiée, elle indique toujours **onpremvm2** est la destination. 

Sans le routage IP activé pour **OPFW**, la logique de mise en réseau virtuelle Azure rejettent les paquets, dans la mesure où elle autorise uniquement les paquets envoyés à un ordinateur virtuel si l’adresse IP de l’ordinateur est la destination du paquet.

Avec le routage IP, la logique d’Azure réseau virtuel transmet les paquets à OPFW, sans modifier son adresse de destination d’origine. **OPFW** doit traiter les paquets et déterminer comment procéder avec eux.

Pour le scénario ci-dessus pour travailler, vous devez activer le routage IP sur les cartes réseau pour **OPFW**, **AZF1**, **AZF2**et **AZF3** qui sont utilisés pour le routage (toutes les cartes d’interface réseau, à l’exception de ceux liés au sous-réseau de gestion). 

## <a name="firewall-rules"></a>Règles de pare-feu

Comme décrit ci-dessus, le routage IP uniquement garantit les paquets sont envoyés vers les appliances virtuelles. Votre solution matérielle-logicielle doit toujours décider quoi faire avec ces paquets. Dans le scénario ci-dessus, vous devez créer les règles suivantes dans vos applications :

### <a name="opfw"></a>OPFW

OPFW représente un périphérique local contenant les règles suivantes :

- **Itinéraire**: tout le trafic vers 10.0.0.0/16 (**azurevnet**) doit être envoyé via le tunnel **ONPREMAZURE**.
- **Stratégie**: autoriser le trafic bidirectionnel entre **port2** et **ONPREMAZURE**.
 
### <a name="azf1"></a>AZF1

AZF1 représente une appliance virtuelle Azure contenant les règles suivantes :

- **Stratégie**: autoriser le trafic bidirectionnel entre **port1** et **port2**.

### <a name="azf2"></a>AZF2

AZF2 représente une appliance virtuelle Azure contenant les règles suivantes :

- **Itinéraire**: tout le trafic vers 10.0.0.0/16 (**onpremvnet**) doit être envoyé à l’adresse IP de passerelle Azure (c'est-à-dire 10.0.0.1) par le biais de **port1**.
- **Stratégie**: autoriser le trafic bidirectionnel entre **port1** et **port2**.

## <a name="network-security-groups-nsgs"></a>Groupes de sécurité réseau (NSGs)

Dans ce scénario, NSGs ne sont pas utilisés. Toutefois, vous pouvez appliquer NSGs à chaque sous-réseau pour restreindre le trafic entrant et sortant. Par exemple, vous pouvez appliquer les règles NSG suivantes au sous-réseau FW externe.

**Entrant**

- Autoriser tout le trafic TCP provenant d’Internet vers le port 80 sur un ordinateur virtuel dans le sous-réseau.
- Refuser tout le trafic provenant d’Internet.

**Sortant**
- Refuser tous les trafics à Internet.

## <a name="high-level-steps"></a>Étapes de niveau élevés

Pour déployer ce scénario, suivez les étapes de haut niveau ci-dessous.

1.  Connexion à votre abonnement Azure.
2.  Si vous souhaitez déployer un VNet pour reproduire le réseau local, mettre en service les ressources qui font partie de **ONPREMRG**.
3.  Configurer les ressources qui font partie de **AZURERG**.
4.  Mise à disposition du tunnel à partir de **onpremvnet** à **azurevnet**.
5.  Une fois que toutes les ressources sont mis en service, ouvrez une session sur **onpremvm2** et ping 10.0.3.101 pour tester la connectivité entre **onpremsn2** et **azsn3**.
