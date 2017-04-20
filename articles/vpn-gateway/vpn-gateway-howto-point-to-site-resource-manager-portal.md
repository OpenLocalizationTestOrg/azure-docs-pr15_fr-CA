<properties 
   pageTitle="Configurer une connexion de passerelle de Point-à-Site VPN au réseau virtuel à l’aide du modèle de déploiement du Gestionnaire de ressources et le portail Azure | Microsoft Azure"
   description="Se connecter en toute sécurité à votre réseau virtuel Azure en créant une connexion de passerelle VPN de Site-à-Point à l’aide du Gestionnaire de ressources et le portail Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc" />

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurer une connexion Point-à-Site à une VNet de via le portail Azure

> [AZURE.SELECTOR]
- [Responsable de ressources - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Standard - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Une configuration de Point-à-Site (P2S) vous permet de créer une connexion sécurisée à partir d’un ordinateur client à un réseau virtuel. Une connexion P2S est utile lorsque vous souhaitez vous connecter à vos VNet à partir d’un emplacement distant, par exemple depuis le domicile ou une conférence, ou lorsque vous n’avez que quelques clients qui doivent se connecter à un réseau virtuel. 

Connexions point-à-Site ne nécessitent pas un périphérique VPN ou utiliser une adresse IP accessible au public. Une connexion VPN est établie par le démarrage de la connexion de l’ordinateur client. Pour plus d’informations sur les connexions Site-à-Point, consultez le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et de la [planification et conception](vpn-gateway-plan-design.md).

Cet article vous guide dans le processus de création d’un VNet avec une connexion Point-à-Site dans le modèle de déploiement Gestionnaire de ressources à l’aide du portail Azure.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modèles de déploiement et les méthodes pour les connexions de P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant présente les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations de P2S. Lorsqu’un article avec des étapes de configuration n’est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flux de travail 

![Point-à-Site-diagramme de] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-à-site")

### <a name="example"></a>Exemples de valeurs

- **Nom : VNet1**
- **Espace d’adressage : 192.168.0.0/16**<br>Pour cet exemple, nous utilisons uniquement un espace d’adressage. Vous pouvez avoir plus d’un espace d’adressage pour votre VNet.
- **Nom du sous-réseau : site Web frontal**
- **Plage d’adresses de sous-réseau : 192.168.1.0/24**
- **Abonnement :** Si vous avez plus d’un abonnement, vérifiez que vous utilisez celui qui convient.
- **Groupe de ressources : TestRG**
- **Emplacement : Américain de l'**
- **GatewaySubnet : 192.168.200.0/24**
- **Nom de la passerelle réseau virtuel : VNet1GW**
- **Type de passerelle : VPN**
- **Type de VPN : basée sur un itinéraire**
- **Adresse IP publique : VNet1GWpip**
- **Type de connexion : Point-à-site**
- **Pool d’adresses client : 172.16.201.0/24**<br>Les clients VPN qui se connectent à la VNet à l’aide de cette connexion Point-à-Site reçoivent une adresse IP à partir du pool d’adresse de client.

## <a name="before-beginning"></a>Avant de commencer

- Vérifiez que vous disposez d’un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
    
## <a name="createvnet"></a>Partie 1 - Création d’un réseau virtuel

Si vous créez cette configuration en guise d’exercice, vous pouvez faire référence aux [valeurs de l’exemple](#example).

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

### <a name="2-add-additional-address-space-and-subnets"></a>2. ajouter des sous-réseaux et l’espace d’adresses supplémentaires

Vous pouvez ajouter des sous-réseaux et espace d’adressage supplémentaires à votre VNet une fois qu’il a été créé.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

### <a name="3-create-a-gateway-subnet"></a>3. création d’un sous-réseau de passerelle

Avant de vous connecter à votre réseau virtuel à une passerelle, vous devez d’abord créer le sous-réseau de passerelle pour le réseau virtuel auquel vous voulez vous connecter. Dans la mesure du possible, il est préférable de créer un sous-réseau de passerelle à l’aide d’un bloc CIDR de /28 ou de /27 afin de fournir suffisamment d’adresses IP pour prendre en charge les besoins supplémentaires de configuration futures.

Les captures d’écran dans cette section sont fournis sous la forme d’un exemple de référence. Veillez à utiliser la plage d’adresses GatewaySubnet qui correspond avec les valeurs requises pour votre configuration.

#### <a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau de passerelle

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="dns"></a>4. Spécifiez un serveur DNS (facultatif)

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>Partie 2 - permet de créer une passerelle réseau virtuel

Connexions point-à-site requièrent les paramètres suivants :

- Type de passerelle : VPN
- Type de VPN : basée sur un itinéraire

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle réseau virtuel

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>Partie 3 - générer des certificats

Les certificats sont utilisés par Azure pour authentifier les clients VPN pour les connexions VPN de Site-à-Point. Vous exportez des données de certificat public (pas la clé privée) comme Base-64 encodé X.509 .cer fichier, soit en utilisant un certificat généré par une solution de certificat d’entreprise un certificat racine auto-signé. Vous ensuite importerez les données du certificat public à partir du certificat racine vers Azure. En outre, vous devez générer un certificat client à partir du certificat racine pour les clients. Chaque client qui souhaite se connecter au réseau virtuel à l’aide d’une connexion P2S doit disposer d’un certificat client a été généré à partir du certificat racine.

### <a name="getcer"></a>1. obtenir le fichier .cer pour le certificat racine

Si vous utilisez une solution d’entreprise, vous pouvez utiliser votre chaîne de certificat existant. Si vous n’utilisez pas une solution de l’autorité de certification d’entreprise, vous pouvez créer un certificat auto-signé. Méthode de création d’un certificat auto-signé est makecert.

- Si vous utilisez un système de certificats d’entreprise, procurez-vous le fichier .cer pour le certificat racine que vous voulez utiliser. 

- Si vous n’utilisez pas une solution de certificat d’entreprise, vous devez générer un certificat racine auto-signé. Pour obtenir la procédure Windows 10, vous pouvez faire référence à [l’utilisation des certificats racines auto-signés pour les configurations de Point-à-Site](vpn-gateway-certificates-point-to-site.md).

1. Pour obtenir un fichier .cer à partir d’un certificat, ouvrez **certmgr.msc** , puis localisez le certificat racine. Cliquez sur le certificat racine auto-signé et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**. L' **Assistant Exportation de certificat**s’ouvre.

2. Dans l’Assistant, cliquez sur **suivant**, sélectionnez **non, ne pas exporter la clé privée**, puis cliquez sur **suivant**.

3. Dans la page **Format de fichier d’exportation** , sélectionnez **Base-64 encodé X.509 (. CER).** Ensuite, cliquez sur **suivant**. 

4. Du **fichier à exporter**, **accédez** à l’emplacement auquel vous souhaitez exporter le certificat. **Nom de fichier**, nommez le fichier de certificat. Puis cliquez sur **suivant**.

5. Cliquez sur **Terminer** pour exporter le certificat.

### <a name="generateclientcert"></a>2. générer un certificat client

Vous pouvez générer un certificat unique pour chaque client qui se connecte, ou vous pouvez utiliser le même certificat sur plusieurs clients. L’avantage de générer des certificats de client unique est la possibilité de révoquer un certificat unique si nécessaire. Dans le cas contraire, si tout le monde utilise le même certificat de client et que vous devez révoquer le certificat pour un client, vous devez générer et installer de nouveaux certificats pour tous les clients qui utilisent ce certificat pour l’authentification.

- Si vous utilisez une solution de certificat d’entreprise, générer un certificat de client avec le format de la valeur nom commun 'name@yourdomain.com', et non au format « nom de domaine\nom ». 

- Si vous utilisez un certificat auto-signé, consultez [utilisation des certificats racines auto-signés pour les configurations de Point-à-Site](vpn-gateway-certificates-point-to-site.md) pour générer un certificat client.

### <a name="exportclientcert"></a>3. exporter le certificat client

Un certificat client est requis pour l’authentification. Après avoir généré le certificat du client, l’exporter. Vous exportez le certificat du client sera plus tard être installé sur chaque ordinateur client.

1. Pour exporter un certificat client, vous pouvez utiliser *certmgr.msc*. Cliquez droit sur le certificat du client que vous souhaitez exporter et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**.
2. Exporter le certificat client avec la clé privée. Il s’agit d’un fichier *.pfx* . Assurez-vous d’enregistrer ou de mémoriser le mot de passe (clé) que vous définissez pour ce certificat.

## <a name="addresspool"></a>Partie 4 - ajouter le pool d’adresses client

1. Une fois la passerelle réseau virtuel a été créée, accédez à la section **paramètres** de la lame de passerelle réseau virtuel. Dans la section **paramètres** , cliquez sur configuration de **Point-à-site** pour ouvrir la lame de **Configuration** .

    ![pointez sur la lame de site] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "pointez sur la lame de site")

2. **Pool d’adresses** est le pool d’adresses IP à partir de laquelle les clients qui se connectent recevront une adresse IP. Ajouter le pool d’adresses, puis cliquez sur **Enregistrer**.

    ![pool d’adresses client] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "pool d’adresses client")

## <a name="uploadfile"></a>Partie 5 : chargement du fichier .cer du certificat racine

Après création de la passerelle, vous pouvez télécharger le fichier .cer pour un certificat racine approuvé sur Azure. Vous pouvez télécharger des fichiers pour les certificats racine jusqu'à 20. Vous ne téléchargez pas la clé privée pour le certificat racine vers Azure. Une fois le fichier .cer est téléchargé, Azure utilise pour authentifier les clients qui se connectent au réseau virtuel.

1. Accédez à la configuration de **Point-à-site** blade. Vous allez ajouter les fichiers .cer dans la section de **certificat racine** de cette blade.

    ![pointez sur la lame de site] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "pointez sur la lame de site")

2. Assurez-vous que vous avez exporté le certificat racine comme Base-64 encodé X.509 (.cer) fichier de configuration. Vous devez exporter dans ce format, afin que vous puissiez ouvrir le certificat avec un éditeur de texte.
3. Avec un éditeur de texte tel que le bloc-notes, ouvrez le certificat. Copier uniquement la section suivante :

    ![données de certificat] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "données de certificat")

4. Collez les données de certificat dans la section **Des données de certificat Public** du portail. Placer le nom du certificat dans l’espace de **noms** , puis cliquez sur **Enregistrer**. Vous pouvez ajouter des certificats racine de confiance jusqu'à 20.

    ![téléchargement du certificat] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "téléchargement du certificat")

## <a name="clientconfig"></a>Partie 6 - télécharger et installer le package de configuration du client VPN

Les clients se connectant à Azure à l’aide de P2S doivent avoir un certificat client et un package de configuration du client VPN installé. Les packages de configuration des clients VPN sont disponibles pour les clients Windows. 

Le package du client VPN contient des informations pour configurer le logiciel client VPN intégré à Windows. La configuration est spécifique à la connexion VPN que vous souhaitez vous connecter. Le package n’installe pas de logiciel supplémentaire. Consultez le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) pour plus d’informations.

1. La configuration de **Point-à-site** lame, cliquez sur **Télécharger le VPN client** pour ouvrir la lame du **client VPN de télécharger** .

    ![Téléchargement du client VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "Téléchargement du client VPN")

2. Sélectionnez le lot approprié pour votre client, puis cliquez sur **Télécharger**. Pour les clients 64 bits, sélectionnez **AMD64**. Pour les clients 32 bits, sélectionnez **x86**.

3. Installez le package sur l’ordinateur client. Si vous obtenez un message SmartScreen, cliquez sur **plus d’infos**, puis **quand même exécuter** pour installer le package.

4. Sur l’ordinateur client, accédez à **Paramètres réseau** et cliquez sur **VPN**. Vous verrez la connexion répertoriée. Il affiche le nom du réseau virtuel auquel il se connectera et de recherche semblable à cet exemple : 

    ![Client VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "Client VPN")

## <a name="installclientcert"></a>Partie 7 - installer le certificat du client

Chaque ordinateur client doit disposer d’un certificat client pour s’authentifier. Lors de l’installation du certificat de client, vous devez le mot de passe qui a été créé lorsque le certificat client a été exporté.

1. Copiez le fichier .pfx sur l’ordinateur client.
2. Double-cliquez sur le fichier .pfx pour l’installer. Ne modifiez pas l’emplacement d’installation.

## <a name="connect"></a>Partie 8 - se connecter vers Azure

1. Pour vous connecter à votre VNet, sur l’ordinateur client, accédez à connexions VPN et localiser la connexion VPN que vous avez créé. Il porte le même nom que votre réseau virtuel. Cliquez sur **se connecter**. Un message peut s’afficher qui fait référence à l’aide du certificat. Dans ce cas, cliquez sur **Continuer** pour utiliser des privilèges élevés. 

2. Dans la page État de la **connexion** , cliquez sur **connexion** pour démarrer la connexion. Si vous voyez un écran **Sélectionner un certificat** , vérifiez que le certificat client affichant est celui que vous souhaitez utiliser pour vous connecter. S’il n’est pas le cas, utilisez la flèche de liste déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

    ![Client VPN 2] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Connexion du client VPN")

3. Votre connexion doit maintenant être établie.

    ![Client VPN 3] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Connexion de client VPN 2")

## <a name="verify"></a>Partie 9 - vérifier votre connexion

1. Pour vérifier que votre connexion VPN est active, ouvrez une invite de commandes avec élévation de privilèges et exécutez *ipconfig/all*.

2. Permet d’afficher les résultats. Notez que l’adresse IP que vous avez reçu est une des adresses dans le Pool d’adresses Point-à-Site VPN Client que vous avez spécifié dans votre configuration. Les résultats doivent être quelque chose de semblable à ceci :
    
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>Pour ajouter ou supprimer des certificats racine de confiance

Vous pouvez supprimer des certificats racine de confiance d’Azure. Lorsque vous supprimez un certificat de confiance, les certificats de client qui ont été générés à partir du certificat racine ne seront plus capable de se connecter à Azure via Site-à-Point. Si vous souhaitez que les clients puissent se connecter, ils doivent installer un nouveau certificat de client qui est généré à partir d’un certificat approuvé dans Azure.

Vous pouvez gérer la liste des certificats clients révoqués sur la configuration de **Point-à-site** blade. Il s’agit de la blade de que vous avez utilisé pour [télécharger un certificat de racine de confiance](#uploadfile).

## <a name="revokeclient"></a>Pour gérer la liste de certificats clients révoqués

Vous pouvez révoquer des certificats clients. La liste de révocation de certificats vous permet de refuser de manière sélective connexion Point-à-Site basée sur des certificats clients individuels. Si vous supprimez un .cer de certificat racine d’Azure, il révoque l’accès de tous les certificats du client généré/signé par le certificat révoqué. Si vous souhaitez révoquer un certificat client spécifique, pas à la racine, vous pouvez le faire. De cette façon, les autres certificats qui ont été générés à partir du certificat racine seront toujours valable. 

La pratique courante consiste à utiliser le certificat racine pour gérer l’accès aux niveaux d’équipe ou de l’organisation, lors de l’utilisation de certificats clients révoqués pour le contrôle d’accès précis sur les utilisateurs individuels.

Vous pouvez gérer la liste des certificats clients révoqués sur la configuration de **Point-à-site** blade. Il s’agit de la blade de que vous avez utilisé pour [télécharger un certificat de racine de confiance](#uploadfile).


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter un ordinateur virtuel au réseau virtuel. Étapes, consultez [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .