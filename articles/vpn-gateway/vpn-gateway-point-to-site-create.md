<properties
   pageTitle="Configurer une connexion de passerelle de Point-à-Site VPN à un réseau virtuel Azure via le portail classique | Microsoft Azure"
   description="Connectez-vous en toute sécurité à votre réseau virtuel Azure en créant une connexion de passerelle VPN de Site-à-Point."
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

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal"></a>Configurer une connexion Point-à-Site à un VNet à l’aide du portail classique

> [AZURE.SELECTOR]
- [Responsable de ressources - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Standard - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
- [Standard - portail classique](vpn-gateway-point-to-site-create.md)

Une configuration de Point-à-Site (P2S) vous permet de créer une connexion sécurisée à partir d’un ordinateur client à un réseau virtuel. Une connexion P2S est utile lorsque vous souhaitez vous connecter à vos VNet à partir d’un emplacement distant, par exemple depuis le domicile ou une conférence, ou lorsque vous n’avez que quelques clients qui doivent se connecter à un réseau virtuel.

Cet article vous guide dans le processus de création d’un VNet avec une connexion Point-à-Site dans le **modèle de déploiement classique** de l’utilisation du **portail classique**.

Connexions point-à-Site ne nécessitent pas un périphérique VPN ou utiliser une adresse IP accessible au public. Une connexion VPN est établie par le démarrage de la connexion de l’ordinateur client. Pour plus d’informations sur les connexions Site-à-Point, consultez le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et de la [planification et conception](vpn-gateway-plan-design.md).


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modèles de déploiement et les méthodes pour les connexions de P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant présente les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations de P2S. Lorsqu’un article avec des étapes de configuration n’est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 



## <a name="basic-workflow"></a>Flux de travail

![Point-à-Site-diagramme de] (./media/vpn-gateway-point-to-site-create/p2sclassic.png "point-à-site")
 
Les étapes suivantes vous guident tout au long de la procédure de création d’une connexion Point-à-Site sécurisée à un réseau virtuel. 

La configuration d’une connexion Point-à-Site est divisée en quatre sections. L’ordre dans lequel vous configurez chacune de ces sections est important. Ne pas ignorer les étapes ou passer tout de suite.

- **Section 1** Créez un réseau virtuel et une passerelle VPN.
- **Section 2** Créer les certificats utilisés pour l’authentification et les télécharger.
- **Section 3** Exporter et installer des certificats de votre client.
- **Section 4** Configurer votre client VPN.

## <a name="vnetvpn"></a>Section 1 - créer un réseau virtuel et une passerelle VPN

### <a name="part-1-create-a-virtual-network"></a>Partie 1 : Créer un réseau virtuel

1. Ouvrez une session [Azure portal classique](https://manage.windowsazure.com/). Ces étapes utilisent le portail classique, pas le portail Azure. Actuellement, vous ne pouvez pas créer une connexion P2S l’aide du portail Azure.

2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services de réseau**, puis cliquez sur **Réseau virtuel**. Cliquez sur **Créer des personnalisées** pour lancer l’Assistant de configuration.

3. Sur la page de **Détails de réseau virtuel** , entrez les informations suivantes, puis cliquez sur la flèche suivant dans le coin inférieur droit.
    - **Nom**: nom du réseau virtuel. Par exemple, 'VNet1'. C’est le nom que vous allez faire lorsque vous déployez des ordinateurs virtuels sur ce VNet.
    - **Emplacement**: l’emplacement est directement liée à l’emplacement physique (région) où vous souhaitez que vos ressources (VMs) se trouve. Par exemple, si vous souhaitez que les ordinateurs virtuels que vous déployez à ce réseau virtuel se trouver physiquement aux Etats-Unis orientale, sélectionnez cet emplacement. Vous ne pouvez pas modifier la région associée à votre réseau virtuel après que l’avoir créé.

4. Dans la page **serveurs DNS et la connectivité VPN** , entrez les informations suivantes, puis cliquez sur la flèche suivant dans le coin inférieur droit.
    - **Serveurs DNS**: entrez le nom du serveur DNS et l’adresse IP, ou sélectionnez un serveur DNS inscrit précédemment dans le menu contextuel. Ce paramètre ne crée pas un serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel. Si vous souhaitez utiliser le service de résolution de nom par défaut Azure, laissez cette section vide.
    - **Configurer Point-To-Site VPN**: activez la case à cocher.

5. Sur la page de **Connexion de Point-à-Site** , spécifiez la plage d’adresses IP à partir de laquelle vos clients VPN reçoit une adresse IP lorsque vous êtes connecté. Il existe quelques règles en ce qui concerne les plages d’adresses que vous spécifiez. Il est important de vérifier que la plage que vous spécifiez ne se chevauche pas avec une des plages que qui se trouve sur votre réseau local.

6. Entrez les informations suivantes, puis cliquez sur la flèche suivant.
 - **Espace d’adressage**: inclure l’adresse IP de début et le CIDR (nombre d’adresses).
 - **Ajouter un espace adresse**: ajouter l’espace d’adressage uniquement s’il est requis pour la conception de votre réseau.

7. Dans la page des **Espaces d’adressage de réseau virtuel** , spécifiez la plage d’adresses que vous souhaitez utiliser pour votre réseau virtuel. Ce sont les adresses IP dynamiques (DIP) qui seront affectés aux ordinateurs virtuels et les autres instances de rôle que vous déployez à ce réseau virtuel.<br><br>Il est particulièrement important sélectionner une plage qui ne se chevauche pas avec toutes les plages qui sont utilisés pour votre réseau local. Vous devez coordonner avec votre administrateur réseau, qui peut-être à imaginer une plage d’adresses IP à partir de votre espace d’adresses de réseau local à utiliser pour votre réseau virtuel.

8. Entrez les informations suivantes, puis cliquez sur la coche pour commencer à créer votre réseau virtuel.
 - **Espace d’adressage**: ajouter la plage d’adresses IP interne que vous souhaitez utiliser pour ce réseau virtuel, y compris IP de début et le nombre. Il est important de sélectionner une plage qui ne se chevauche pas avec toutes les plages qui sont utilisés pour votre réseau local. 
 - **Ajouter sous-réseau**: sous-réseaux supplémentaires ne sont pas obligatoires, mais vous souhaiterez peut-être créer un sous-réseau distinct pour les machines virtuelles qui auront creux statiques. Ou vous souhaiterez peut-être que vos ordinateurs virtuels dans un sous-réseau distinct à partir de vos autres instances de rôle.
 - **Sous-réseau de passerelle ajouter**: le sous-réseau de passerelle est requis pour un réseau privé virtuel Site-à-Point. Cliquez pour ajouter le sous-réseau de passerelle. Le sous-réseau de passerelle est utilisé uniquement pour la passerelle réseau virtuel.

9. Une fois votre réseau virtuel a été créé, vous verrez **que créé** répertoriés sous **état** de la page réseaux dans Azure portal classique. Une fois votre réseau virtuel a été créé, vous pouvez créer votre passerelle de routage dynamique.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Partie 2 : Créer une passerelle de routage dynamique

Le type de passerelle doit être configuré comme dynamique. Les passerelles de routage statiques ne fonctionnent pas avec cette fonctionnalité.

1. Dans le portail Azure classique, dans la page **réseaux** , cliquez sur le réseau virtuel que vous avez créée, accédez à la page de **tableau de bord** .

2. Cliquez sur **Créer une passerelle**, située au bas de la page de **tableau de bord** . Un message s’affiche et vous demande **Voulez-vous créer une passerelle de réseau virtuel « VNet1 »**. Cliquez sur **Oui** pour commencer la création de la passerelle. Il peut prendre environ 15 minutes pour la passerelle à créer.

## <a name="generate"></a>Section 2 - générer et télécharger les certificats

Les certificats sont utilisés pour authentifier les clients VPN pour les connexions VPN de Site-à-Point. Vous pouvez utiliser un certificat généré par une solution de certificat d’entreprise, ou vous pouvez utiliser un certificat auto-signé. Vous pouvez télécharger les certificats racine jusqu'à 20 à Azure. Une fois le fichier .cer est téléchargé, Azure peut utiliser les informations qu’il contient pour authentifier les clients qui ont installé un certificat de client. Le certificat client doit être généré à partir du même certificat représentant le fichier .cer.

Dans cette section, vous exécuterez la commande suivante :

- Obtenir le fichier .cer pour un certificat racine. Il peut s’agir d’un certificat signé automatiquement, ou vous pouvez utiliser le système de certificat de votre entreprise.
- Télécharger le fichier .cer dans Azure.
- Générer des certificats de client.

### <a name="root"></a>Partie 1 : Obtenir le fichier .cer pour le certificat racine

Si vous utilisez un système de certificats d’entreprise, procurez-vous le fichier .cer pour le certificat racine que vous voulez utiliser. Dans la [partie 3](#createclientcert), vous générez les certificats de client à partir du certificat racine.

Si vous n’utilisez pas une solution de certificat d’entreprise, vous devez générer un certificat racine auto-signé. Pour obtenir la procédure Windows 10, vous pouvez faire référence à [l’utilisation des certificats racines auto-signés pour les configurations de Point-à-Site](vpn-gateway-certificates-point-to-site.md). L’article vous guide tout au long de l’utilisation de makecert pour générer un certificat auto-signé et exportez le fichier .cer.

### <a name="upload"></a>Partie 2 : Téléchargement fichier .cer certificat racine sur le portail classique Azure

Ajouter un certificat approuvé pour Azure. Lorsque vous ajoutez un fichier codé en Base64 X.509 (.cer) vers Azure, vous indiquez à Azure pour approuver le certificat racine représentant le fichier.

1. Dans le portail Azure classique, sur la page **certificats** pour votre réseau virtuel, cliquez sur **télécharger un certificat racine**.

2. Sur la page de **Téléchargement des certificats** , recherchez le certificat .cer, puis cliquez sur la coche.

### <a name="createclientcert"></a>Partie 3 : Générer un certificat client

Ensuite, générez les certificats clients. Vous pouvez générer un certificat unique pour chaque client qui se connecte, ou vous pouvez utiliser le même certificat sur plusieurs clients. L’avantage de générer des certificats de client unique est la possibilité de révoquer un certificat unique si nécessaire. Dans le cas contraire, si tout le monde utilise le même certificat de client et que vous devez révoquer le certificat pour un client, vous devez générer et installer de nouveaux certificats pour tous les clients qui utilisent le certificat pour l’authentification.

- Si vous utilisez une solution de certificat d’entreprise, générer un certificat de client avec le format de la valeur nom commun 'name@yourdomain.com', plutôt que le format de NetBIOS 'Domaine\nom_utilisateur'. 

- Si vous utilisez un certificat auto-signé, consultez [utilisation des certificats racines auto-signés pour les configurations de Point-à-Site](vpn-gateway-certificates-point-to-site.md) pour générer un certificat client.

## <a name="installclientcert"></a>Section 3 : exportation et installer le certificat du client

Installer un certificat client sur chaque ordinateur que vous souhaitez connecter au réseau virtuel. Un certificat client est requis pour l’authentification. Vous pouvez automatiser l’installation du certificat de client, ou vous pouvez installer manuellement. Les étapes suivantes vous aide à exporter et installer le certificat client manuellement.

1. Pour exporter un certificat client, vous pouvez utiliser *certmgr.msc*. Cliquez droit sur le certificat du client que vous souhaitez exporter et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**.
2. Exporter le certificat client avec la clé privée. Il s’agit d’un fichier *.pfx* . Assurez-vous d’enregistrer ou de mémoriser le mot de passe (clé) que vous définissez pour ce certificat.
3. Copiez le fichier *.pfx* sur l’ordinateur client. Sur l’ordinateur client, double-cliquez sur le fichier *.pfx* pour l’installer. Entrez le mot de passe lors d’une demande. Ne modifiez pas l’emplacement d’installation.

## <a name="vpnclientconfig"></a>Section 4 – configurer votre client VPN

Pour vous connecter au réseau virtuel, vous devez également configurer un client VPN. Le client requiert un certificat client et la bonne configuration du client VPN pour se connecter. Pour configurer un client VPN, procédez comme suit, dans l’ordre.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Partie 1 : Créer le package de configuration du client VPN

1. Dans le portail Azure classique, sur la page de **tableau de bord** pour votre réseau virtuel, accédez au menu Aperçu rapide dans le coin droit. Pour la liste des systèmes d’exploitation clients pris en charge, consultez les connexions [Point-à-Site](vpn-gateway-vpn-faq.md#point-to-site-connections) section de la Foire aux questions de la passerelle VPN. Le package du client VPN contient des informations de configuration pour configurer le logiciel client VPN intégré à Windows. Le package n’installe pas de logiciel supplémentaire. Les paramètres sont spécifiques au réseau virtuel que vous souhaitez vous connecter.<br><br>Sélectionnez le package de téléchargement qui correspond au système d’exploitation client sur lequel il sera installé :
 - Pour les clients 32 bits, sélectionnez **Télécharger le Package de VPN Client 32 bits**.
 - Pour les clients 64 bits, sélectionnez **Télécharger le Package de VPN Client 64 bits**.

2. Il faut quelques minutes pour créer votre package client. Une fois que le package est terminé, vous pouvez télécharger le fichier. Le fichier *.exe* que vous téléchargez peut être stocké en toute sécurité sur votre ordinateur local.

3. Après avoir généré et télécharger le package du client VPN à partir du portail classique Azure, vous pouvez installer le package du client sur l’ordinateur client à partir de laquelle vous souhaitez vous connecter à votre réseau virtuel. Si vous envisagez d’installer le package du client VPN vers plusieurs ordinateurs clients, assurez-vous que chaque également qu’un certificat client est installé.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Partie 2 : Installer le package de configuration VPN sur le client

1. Copiez le fichier de configuration localement sur l’ordinateur que vous souhaitez connecter à votre réseau virtuel et double-cliquez sur le fichier .exe. 

2. Une fois que le package a été installé, vous pouvez démarrer la connexion VPN. Le package de configuration n’est pas signé par Microsoft. Vous pouvez signer le package à l’aide du service de signature de votre organisation, ou signer vous-même à l’aide de [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Il est OK pour utiliser le package sans signature. Toutefois, si le package n’est pas signé, un avertissement s’affiche lorsque vous installez le package.

3. Sur l’ordinateur client, accédez à **Paramètres réseau** et cliquez sur **VPN**. Vous verrez la connexion répertoriée. Il affiche le nom du réseau virtuel qu’il doit se connecter et le ressemblera à ceci : 

    ![Client VPN] (./media/vpn-gateway-point-to-site-create/vpn.png "Client VPN")


### <a name="part-3-connect-to-azure"></a>Partie 3 : Connecter vers Azure

1. Pour vous connecter à votre VNet, sur l’ordinateur client, accédez à connexions VPN et localiser la connexion VPN que vous avez créé. Il porte le même nom que votre réseau virtuel. Cliquez sur **se connecter**. Un message peut s’afficher qui fait référence à l’aide du certificat. Dans ce cas, cliquez sur **Continuer** pour utiliser des privilèges élevés. 

2. Dans la page État de la **connexion** , cliquez sur **connexion** pour démarrer la connexion. Si vous voyez un écran **Sélectionner un certificat** , vérifiez que le certificat client affichant est celui que vous souhaitez utiliser pour vous connecter. S’il n’est pas le cas, utilisez la flèche de liste déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

    ![Client VPN 2] (./media/vpn-gateway-point-to-site-create/clientconnect.png "Connexion du client VPN")

3. Votre connexion doit maintenant être établie.

    ![Client VPN 3] (./media/vpn-gateway-point-to-site-create/connected.png "Connexion de client VPN 2")

### <a name="part-4-verify-the-vpn-connection"></a>Partie 4 : Vérification de la connexion VPN

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

Si vous souhaitez plus d’informations sur les réseaux virtuels, consultez la page de [Documentation de réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) .
