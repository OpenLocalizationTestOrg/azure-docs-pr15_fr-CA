<properties
   pageTitle="Configurer une connexion de passerelle de Point-à-Site VPN à un réseau virtuel Azure via le portail Azure | Microsoft Azure"
   description="Se connecter en toute sécurité à votre réseau virtuel Azure en créant une connexion de passerelle VPN de Point-à-Site via le portail Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurer une connexion Point-à-Site à une VNet de via le portail Azure

> [AZURE.SELECTOR]
- [Responsable de ressources - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Standard - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Cet article vous guide dans le processus de création d’un VNet avec une connexion Point-à-Site dans le modèle de déploiement classique en utilisant le portail Azure. Une configuration de Point-à-Site (P2S) vous permet de créer une connexion sécurisée à partir d’un ordinateur client à un réseau virtuel. Une connexion P2S est utile lorsque vous souhaitez vous connecter à votre VNet à partir d’un emplacement distant, par exemple d’accueil ou d’une conférence. Ou bien, lorsque vous avez uniquement quelques clients qui doivent se connecter à un réseau virtuel.

Connexions point-à-Site ne nécessitent pas un périphérique VPN ou utiliser une adresse IP accessible au public. Une connexion VPN est établie par le démarrage de la connexion de l’ordinateur client. Pour plus d’informations sur les connexions Site-à-Point, consultez le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et [Sur la passerelle de VPN](vpn-gateway-about-vpngateways.md#point-to-site).

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modèles de déploiement et les méthodes pour les connexions de P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant présente les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations de P2S. Lorsqu’un article avec des étapes de configuration n’est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flux de travail 

![Point-à-Site-diagramme de] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-à-site")


Les sections suivantes vous guident tout au long de la procédure de création d’une connexion Point-à-Site sécurisée à un réseau virtuel. 

1. Créer un réseau virtuel et une passerelle VPN
2. Générer des certificats
3. Télécharger le fichier .cer
4. Générer le package de configuration du client VPN
5. Configurer l’ordinateur client
6. Se connecter à Azure

### <a name="example-settings"></a>Exemples de paramètres

Vous pouvez utiliser les paramètres de l’exemple suivant :

- **Nom : VNet1**
- **Espace d’adressage : 192.168.0.0/16**
- **Nom du sous-réseau : site Web frontal**
- **Plage d’adresses de sous-réseau : 192.168.1.0/24**
- **Abonnement :** Si vous avez plus d’un abonnement, vérifiez que vous utilisez celui qui convient.
- **Groupe de ressources : TestRG**
- **Emplacement : Américain de l'**
- **Type de connexion : Point-à-site**
- **Espace d’adresse client : 172.16.201.0/24**. Les clients VPN qui se connectent à la VNet à l’aide de cette connexion Point-à-Site reçoivent une adresse IP à partir du pool spécifié.
- **GatewaySubnet : 192.168.200.0/24**. Le sous-réseau de la passerelle doit utiliser le nom « GatewaySubnet ».
- **Taille :** Sélectionnez la référence que vous souhaitez utiliser de passerelle.
- **Type de routage : dynamique**

## <a name="vnetvpn"></a>Section 1 - créer un réseau virtuel et une passerelle VPN

### <a name="createvnet"></a>Partie 1 : Créer un réseau virtuel

Si vous ne disposez pas d’un réseau virtuel, créez-en un. Captures d’écran sont fournis comme exemples. Veillez à remplacer les valeurs de votre propre. Pour créer un VNet à l’aide du portail Azure, procédez comme suit : 

1. À partir d’un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous à votre compte Azure.

2. Cliquez sur **Nouveau**. Dans le champ de **recherche sur le marché** , tapez « Réseau virtuel ». Recherchez un **Réseau virtuel** à partir de la liste retournée et cliquez sur pour ouvrir la lame de **Réseau virtuel** .

    ![Recherche de lame de réseau virtuel] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Recherche de lame de réseau virtuel")

3. Dans la partie inférieure de la lame de réseau virtuel, dans la liste **Sélectionnez un modèle de déploiement** , sélectionnez **classique**, puis cliquez sur **créer**.

    ![Sélectionnez le modèle de déploiement] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Sélectionnez le modèle de déploiement")

4. Sur la lame de **créer des réseaux virtuels** , configurer les paramètres de VNet. Dans cette blade, vous allez ajouter votre premier espace d’adresse et d’une plage d’adresses de sous-réseau unique. Lorsque vous avez terminé la création de la VNet, vous pouvez revenir en arrière et ajouter des espaces d’adressage et des sous-réseaux supplémentaires.

    ![Lame de réseau virtuel créer] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Lame de réseau virtuel créer")

5. Vérifiez que l' **abonnement** est correcte. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.

6. Cliquez sur le **groupe de ressources** et soit sélectionner un groupe de ressources existant ou créez-en un nouveau en tapant un nom pour votre nouveau groupe de ressources. Si vous créez un nouveau groupe, nommez le groupe de ressources en fonction des valeurs de votre configuration envisagée. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

7. Ensuite, sélectionnez les paramètres **d’emplacement** pour votre VNet. Détermine l’emplacement dans lequel se trouve les ressources que vous déployez sur ce VNet.

8. Sélectionnez **Ajouter au tableau de bord** si vous voulez être en mesure de trouver votre VNet facilement sur le tableau de bord, puis cliquez sur **créer**.
    
    ![Fixer au tableau de bord] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Fixer au tableau de bord")


9. Après avoir cliqué sur Créer, vous verrez une mosaïque sur votre tableau de bord qui reflète la progression de votre VNet. La mosaïque change à mesure que le VNet est en cours de création.

    ![Mosaïque de création des réseaux virtuels] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Mosaïque de création des réseaux virtuels")

10. Après avoir créé votre réseau virtuel, vous pouvez ajouter l’adresse IP d’un serveur DNS afin de gérer la résolution de nom. Ouvrir les paramètres de votre réseau virtuel et cliquez sur serveurs DNS, ajoutez l’adresse IP du serveur DNS que vous souhaitez utiliser. Ce paramètre ne crée pas un nouveau serveur DNS. Veillez à ajouter un serveur DNS capable de communiquer avec vos ressources.

Une fois votre réseau virtuel a été créé, vous verrez **que créé** répertoriés sous **état** de la page réseaux dans Azure portal classique.

### <a name="gateway"></a>Partie 2 : Créer le sous-réseau de passerelle et de la passerelle routage dynamique

Dans cette étape, vous allez créer un sous-réseau de passerelle et d’une passerelle de routage dynamique. Dans le portail Azure pour le modèle de déploiement classique, création du sous-réseau de passerelle et de la passerelle peut être effectuée via les même lames de configuration.

1. Dans le portail, accédez au réseau virtuel pour lequel vous souhaitez créer une passerelle.

2. Sur la lame pour le réseau virtuel, sur la lame de **vue d’ensemble** , dans la section de connexions VPN, cliquez sur **passerelle**.

    ![Cliquez ici pour créer une passerelle] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Cliquez ici pour créer une passerelle")


3. Sur la lame de la **Nouvelle connexion VPN** , sélectionnez **Point-à-site**.

    ![Type de connexion P2S] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "Type de connexion P2S")

4. Pour l' **Espace d’adresse Client**, ajoutez la plage d’adresses IP. Il s’agit de la plage à partir de laquelle les clients VPN reçoivent une adresse IP lors de la connexion. Supprimer la plage de remplissage automatique et ajoutez vos propres.

    ![Espace d’adresse client] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Espace d’adresse client")

5. Activez la case à cocher de la **passerelle de créer immédiatement** .

    ![Passerelle de créer immédiatement] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Passerelle de créer immédiatement")

6. Cliquez sur **configuration de la passerelle facultatif** pour ouvrir la lame de la **configuration de la passerelle** .

    ![Cliquez sur configuration de la passerelle facultatif] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Cliquez sur configuration de la passerelle facultatif")

7. Cliquez sur la **configuration de sous-réseau des paramètres requis** pour ajouter le **sous-réseau de passerelle**. S’il est possible de créer un sous-réseau passerelle aussi petit que /29, nous vous conseillons de créer un sous-réseau plus grand qui inclut plusieurs adresses en sélectionnant au moins /28 ou /27. Ainsi, pour un nombre d’adresses suffisant pour s’adapter à des configurations supplémentaires possibles que vous pouvez par la suite.

    >[AZURE.IMPORTANT] Lorsque vous travaillez avec des sous-réseaux de la passerelle, éviter d’associer un groupe de sécurité réseau (NSG) pour le sous-réseau de passerelle. Association d’un groupe de sécurité réseau de ce sous-réseau risque de votre passerelle VPN cesser de fonctionner comme prévu. Pour plus d’informations sur les groupes de sécurité réseau, consultez [ce qu’est un groupe de sécurité réseau ?](../articles/virtual-network/virtual-networks-nsg.md)

    ![Ajouter GatewaySubnet] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Ajouter GatewaySubnet")

8. Sélectionnez la **taille**de la passerelle. Il s’agit de la passerelle SKU que vous utiliserez pour créer votre passerelle réseau virtuel. Dans le portail, la référence par défaut est la **base**. Pour plus d’informations sur les références SKU de passerelle, consultez [Paramètres passerelle VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![taille de la passerelle](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)

9. Sélectionnez le **Type de routage** de la passerelle. Les configurations de P2S nécessitent un type de routage **dynamique** . Cliquez sur **OK** lorsque vous avez terminé de configurer cette blade.

    ![Type de gamme de configuration] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Type de gamme de configuration")

10. Sur la lame de la **Nouvelle connexion VPN** , cliquez sur **OK** au bas de la lame pour commencer à créer votre passerelle réseau virtuel. Cette opération peut prendre jusqu'à 45 minutes. 


## <a name="generatecerts"></a>Section 2 - générer des certificats

Les certificats sont utilisés par Azure pour authentifier les clients VPN pour les connexions VPN de Site-à-Point. Vous exportez des données de certificat public (pas la clé privée) comme Base-64 encodé X.509 .cer fichier, soit en utilisant un certificat généré par une solution de certificat d’entreprise un certificat racine auto-signé. Vous ensuite importerez les données du certificat public à partir du certificat racine vers Azure. En outre, vous devez générer un certificat client à partir du certificat racine pour les clients. Chaque client qui souhaite se connecter au réseau virtuel à l’aide d’une connexion P2S doit disposer d’un certificat client a été généré à partir du certificat racine.

### <a name="cer"></a>Partie 1 : Obtenir le fichier .cer pour le certificat racine


Si vous utilisez une solution d’entreprise, vous pouvez utiliser votre chaîne de certificat existant. Si vous n’utilisez pas une solution de l’autorité de certification d’entreprise, vous pouvez créer un certificat auto-signé. Méthode de création d’un certificat auto-signé est makecert.

- Si vous utilisez un système de certificats d’entreprise, procurez-vous le fichier .cer pour le certificat racine que vous voulez utiliser. 

- Si vous n’utilisez pas une solution de certificat d’entreprise, vous devez générer un certificat racine auto-signé. Pour obtenir la procédure Windows 10, vous pouvez faire référence à [l’utilisation des certificats racines auto-signés pour les configurations de Point-à-Site](vpn-gateway-certificates-point-to-site.md).

1. Pour obtenir un fichier .cer à partir d’un certificat, ouvrez **certmgr.msc** , puis localisez le certificat racine. Cliquez sur le certificat racine auto-signé et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**. L' **Assistant Exportation de certificat**s’ouvre.

2. Dans l’Assistant, cliquez sur **suivant**, sélectionnez **non, ne pas exporter la clé privée**, puis cliquez sur **suivant**.

3. Dans la page **Format de fichier d’exportation** , sélectionnez **Base-64 encodé X.509 (. CER).** Ensuite, cliquez sur **suivant**. 

4. Du **fichier à exporter**, **accédez** à l’emplacement auquel vous souhaitez exporter le certificat. **Nom de fichier**, nommez le fichier de certificat. Puis cliquez sur **suivant**.

5. Cliquez sur **Terminer** pour exporter le certificat.


### <a name="genclientcert"></a>Partie 2 : Générer un certificat client

Vous pouvez générer un certificat unique pour chaque client qui se connecte, ou vous pouvez utiliser le même certificat sur plusieurs clients. L’avantage de générer des certificats de client unique est la possibilité de révoquer un certificat unique si nécessaire. Dans le cas contraire, si tout le monde utilise le même certificat de client et que vous devez révoquer le certificat pour un client, vous devez générer et installer de nouveaux certificats pour tous les clients qui utilisent ce certificat pour l’authentification.

- Si vous utilisez une solution de certificat d’entreprise, générer un certificat de client avec le format de la valeur nom commun 'name@yourdomain.com', et non au format « nom de domaine\nom ». 

- Si vous utilisez un certificat auto-signé, consultez [utilisation des certificats racines auto-signés pour les configurations de Point-à-Site](vpn-gateway-certificates-point-to-site.md) pour générer un certificat client.

### <a name="exportclientcert"></a>Partie 3 : Exporter le certificat client

Installer un certificat client sur chaque ordinateur que vous souhaitez connecter au réseau virtuel. Un certificat client est requis pour l’authentification. Vous pouvez automatiser l’installation du certificat de client, ou vous pouvez l’installer manuellement. Les étapes suivantes vous aide à exporter et installer le certificat client manuellement.

1. Pour exporter un certificat client, vous pouvez utiliser *certmgr.msc*. Cliquez droit sur le certificat du client que vous souhaitez exporter et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**.
2. Exporter le certificat client avec la clé privée. Il s’agit d’un fichier *.pfx* . Assurez-vous d’enregistrer ou de mémoriser le mot de passe (clé) que vous définissez pour ce certificat.

## <a name="upload"></a>Section 3 - Télécharger le fichier .cer du certificat racine

Après création de la passerelle, vous pouvez télécharger le fichier .cer pour un certificat racine approuvé sur Azure. Vous pouvez télécharger des fichiers pour les certificats racine jusqu'à 20. Vous ne téléchargez pas la clé privée pour le certificat racine vers Azure. Une fois le fichier .cer est téléchargé, Azure utilise pour authentifier les clients qui se connectent au réseau virtuel.

1. Dans la section **connexions VPN** de la lame pour votre VNet, cliquez sur le graphique de **clients** pour ouvrir la **Point-à-site VPN connexion** blade.

    ![Clients] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Clients")

2. Sur la connexion **Point-à-site** lame, cliquez sur **Gérer les certificats** pour ouvrir la lame de **certificats** .<br>

    ![Lame de certificats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>


3. Sur la lame de **certificats** , cliquez sur **Télécharger** pour ouvrir la lame de **Télécharger le certificat** .<br>

    ![Téléchargement de lame de certificats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>


4. Cliquez sur l’image de dossier pour rechercher le fichier .cer. Sélectionnez le fichier, puis cliquez sur **OK**. Actualisez la page pour voir le certificat chargé sur la lame de **certificats** .

    ![Télécharger le certificat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>
    

## <a name="vpnclientconfig"></a>Section 4 – générer le package de configuration du client VPN

Pour vous connecter au réseau virtuel, vous devez également configurer un client VPN. L’ordinateur client requiert un certificat client et le package de configuration du client VPN approprié pour se connecter.

Le package du client VPN contient des informations de configuration pour configurer le logiciel client VPN intégré à Windows. Le package n’installe pas de logiciel supplémentaire. Les paramètres sont spécifiques au réseau virtuel que vous souhaitez vous connecter. Pour la liste des systèmes d’exploitation clients pris en charge, consultez les connexions [Point-à-Site](vpn-gateway-vpn-faq.md#point-to-site-connections) section de la Foire aux questions de la passerelle VPN. 

### <a name="to-generate-the-vpn-client-configuration-package"></a>Pour générer le package de configuration du client VPN

1. Dans le portail d’Azure, dans la **vue d’ensemble** de lame pour votre VNet, dans les **connexions VPN**, cliquez sur le graphique de client pour ouvrir la **Point-à-site VPN connexion** blade.
2. En haut de la **Point-à-site VPN connexion** lame, cliquez sur le package de téléchargement qui correspond au système d’exploitation client sur lequel il sera installé :

 - Pour les clients 64 bits, sélectionnez **Le Client VPN (64 bits)**.
 - Pour les clients 32 bits, sélectionnez **Le Client VPN (32 bits)**.

    ![Télécharger le package de configuration du client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>


3. Vous verrez un message que Azure génère le package de configuration du client VPN pour le réseau virtuel. Après quelques minutes, le package est généré et vous verrez un message sur votre ordinateur local que le package a été téléchargé. Enregistrez le fichier de package de configuration. Vous l’installerez sur chaque ordinateur client qui se connectera au réseau virtuel à l’aide de P2S.


## <a name="clientconfiguration"></a>Section 5 - configurer l’ordinateur client

### <a name="part-1-install-the-client-certificate"></a>Partie 1 : Installer le certificat du client

Chaque ordinateur client doit disposer d’un certificat client pour s’authentifier. Lors de l’installation du certificat de client, vous devez le mot de passe qui a été créé lorsque le certificat client a été exporté.

1. Copiez le fichier .pfx sur l’ordinateur client.
2. Double-cliquez sur le fichier .pfx pour l’installer. Ne modifiez pas l’emplacement d’installation.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Partie 2 : Installer le package de configuration du client VPN

Vous pouvez utiliser le même package de configuration de client VPN sur chaque ordinateur client, sous réserve que la version correspond à l’architecture pour le client.

1. Copiez le fichier de configuration localement sur l’ordinateur que vous souhaitez connecter à votre réseau virtuel et double-cliquez sur le fichier .exe. 

2. Une fois que le package a été installé, vous pouvez démarrer la connexion VPN. Le package de configuration n’est pas signé par Microsoft. Vous pouvez signer le package à l’aide du service de signature de votre organisation, ou signer vous-même à l’aide de [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Il est OK pour utiliser le package sans signature. Toutefois, si le package n’est pas signé, un avertissement s’affiche lorsque vous installez le package.

3. Sur l’ordinateur client, accédez à **Paramètres réseau** et cliquez sur **VPN**. Vous verrez la connexion répertoriée. Il affiche le nom du réseau virtuel qu’il doit se connecter et le ressemblera à ceci : 

    ![Client VPN] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "VNet VPN client")

## <a name="connect"></a>Section 6 – se connecter vers Azure

### <a name="connect-to-your-vnet"></a>Se connecter à votre VNet

1. Pour vous connecter à votre VNet, sur l’ordinateur client, accédez à connexions VPN et localiser la connexion VPN que vous avez créé. Il porte le même nom que votre réseau virtuel. Cliquez sur **se connecter**. Un message peut s’afficher qui fait référence à l’aide du certificat. Dans ce cas, cliquez sur **Continuer** pour utiliser des privilèges élevés. 

2. Dans la page État de la **connexion** , cliquez sur **connexion** pour démarrer la connexion. Si vous voyez un écran **Sélectionner un certificat** , vérifiez que le certificat client affichant est celui que vous souhaitez utiliser pour vous connecter. S’il n’est pas le cas, utilisez la flèche de liste déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

    ![Se connecter] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "Connexion du client VPN")

3. Votre connexion doit maintenant être établie.

    ![Connexion de fondation] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Connexion établie")

### <a name="verify-the-vpn-connection"></a>Vérifiez que la connexion VPN

1. Pour vérifier que votre connexion VPN est active, ouvrez une invite de commandes avec élévation de privilèges et exécutez *ipconfig/all*.
2. Permet d’afficher les résultats. Notez que l’adresse IP que vous avez reçu est une des adresses dans la plage d’adresses de connexion Point-à-Site que vous avez spécifié lorsque vous avez créé votre VNet. Les résultats doivent être quelque chose de semblable à ceci :

Exemple :



    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter des machines virtuelles au réseau virtuel. Apprendre [à créer un ordinateur virtuel personnalisé](../virtual-machines/virtual-machines-windows-classic-createportal.md).