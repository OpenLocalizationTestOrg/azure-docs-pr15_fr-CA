<properties 
   pageTitle="Configurer une connexion de passerelle VPN de Site-à-Point à l’utilisation du modèle de déploiement du Gestionnaire de ressources de réseau virtuel | Microsoft Azure"
   description="Connectez-vous en toute sécurité à votre réseau virtuel Azure en créant une connexion de passerelle VPN de Site-à-Point."
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

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurer une connexion Point-à-Site à un VNet à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Responsable de ressources - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Standard - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Une configuration de Point-à-Site (P2S) vous permet de créer une connexion sécurisée à partir d’un ordinateur client à un réseau virtuel. Une connexion P2S est utile lorsque vous souhaitez vous connecter à vos VNet à partir d’un emplacement distant, par exemple depuis le domicile ou une conférence, ou lorsque vous n’avez que quelques clients qui doivent se connecter à un réseau virtuel. 

Connexions point-à-Site ne nécessitent pas un périphérique VPN ou utiliser une adresse IP accessible au public. Une connexion VPN est établie par le démarrage de la connexion de l’ordinateur client. Pour plus d’informations sur les connexions Site-à-Point, consultez le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et de la [planification et conception](vpn-gateway-plan-design.md). 

Cet article vous guide dans le processus de création d’un VNet avec une connexion Point-à-Site dans le modèle de déploiement Gestionnaire de ressources à l’aide de PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modèles de déploiement et les méthodes pour les connexions de P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant présente les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations de P2S. Lorsqu’un article avec des étapes de configuration n’est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flux de travail 

![Point-à-Site-diagramme de] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-à-site")

Dans ce scénario, vous allez créer un réseau virtuel avec une connexion Point-à-Site. Les instructions vous aidera également à générer des certificats, qui sont nécessaires pour cette configuration. Une connexion de P2S est composée des éléments suivants : VNet A avec une passerelle VPN, un fichier .cer du certificat racine (clé publique), un certificat client et la configuration de VPN sur le client. 

Nous utilisons les valeurs suivantes pour cette configuration. Nous avons défini les variables dans la section [1](#declare) de l’article. Vous pouvez utiliser les étapes sous la forme d’une vue d’ensemble et employer les valeurs sans les modifier ou les modifier pour refléter votre environnement. 

### <a name="example"></a>Exemples de valeurs

- **Nom : VNet1**
- **L’espace d’adressage : 192.168.0.0/16** et **10.254.0.0/16**<br>Pour cet exemple, nous utilisons le plus d’un espace d’adressage pour illustrer que cette configuration fonctionne avec plusieurs espaces d’adressage. Toutefois, plusieurs espaces d’adressage ne sont pas requises pour cette configuration.
- **Nom du sous-réseau : site Web frontal**
    - **Plage d’adresses de sous-réseau : 192.168.1.0/24**
- **Nom du sous-réseau : back-end**
    - **Plage d’adresses de sous-réseau : 10.254.1.0/24**
- **Nom du sous-réseau : GatewaySubnet**<br>Le nom de sous-réseau *GatewaySubnet* est obligatoire pour la passerelle VPN fonctionner.
    - **Plage d’adresses de sous-réseau : 192.168.200.0/24** 
- **Pool d’adresses VPN client : 172.16.201.0/24**<br>Les clients VPN qui se connectent à la VNet à l’aide de cette connexion Point-à-Site reçoivent une adresse IP à partir du pool d’adresses VPN client.
- **Abonnement :** Si vous avez plus d’un abonnement, vérifiez que vous utilisez celui qui convient.
- **Groupe de ressources : TestRG**
- **Emplacement : Américain de l'**
- **Serveur DNS : adresse IP** du serveur DNS que vous souhaitez utiliser pour la résolution de nom.
- **Nom de GW : Vnet1GW**
- **IP publique, nom : VNet1GWPIP**
- **VpnType : RouteBased**

## <a name="before-beginning"></a>Avant de commencer

- Vérifiez que vous disposez d’un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
    
- Installez la dernière version de cmdlets PowerShell du Gestionnaire de ressources Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) . Lorsque vous travaillez avec PowerShell pour cette configuration, assurez-vous que vous exécutez en tant qu’administrateur. 

## <a name="declare"></a>Partie 1 - journal dans et jeu de variables

Dans cette section, vous connecter et de déclarez les valeurs utilisées pour cette configuration. Les valeurs déclarées sont utilisés dans les exemples de scripts. Modifier les valeurs pour représenter votre propre environnement. Ou bien, vous pouvez utiliser les valeurs déclarées et suivez les étapes comme un exercice.

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Cette applet de commande vous demande les informations d’identification de connexion pour votre compte Azure. Après la connexion, il télécharge les paramètres de votre compte afin qu’ils soient disponibles pour Azure PowerShell.

        Login-AzureRmAccount 

2. Obtenir la liste de vos abonnements Azure.

        Get-AzureRmSubscription

3. Spécifier l’abonnement que vous souhaitez utiliser. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Déclarez les variables que vous souhaitez utiliser. Utilisez l’exemple suivant, en remplaçant les valeurs de votre propre lorsque cela est nécessaire.

        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"
        
## <a name="ConfigureVNet"></a>Partie 2 : configurer un VNet 

1. Créer un groupe de ressources.

        New-AzureRmResourceGroup -Name $RG -Location $Location

2. Créer les configurations de sous-réseau pour le réseau virtuel, leur attribuer un nom *système frontal*, *back-end*et *GatewaySubnet*. Ces préfixes doivent appartenir à l’espace d’adressage VNet que vous avez déclaré.

        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. Créez le réseau virtuel. Le serveur DNS spécifié doit être un serveur DNS qui peut résoudre les noms pour les ressources que vous vous connectez à. Pour cet exemple, nous avons utilisé une adresse IP publique. Veillez à utiliser vos propres valeurs.
    
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. Spécifiez les variables pour le réseau virtuel que vous avez créé.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. Demander une adresse publique attribuée de manière dynamique. Cette adresse IP est nécessaire pour la passerelle fonctionne correctement. Vous connectez ultérieurement à la passerelle pour la configuration IP de la passerelle.
        
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="Certificates"></a>Partie 3 - certificats

Les certificats sont utilisés par Azure pour authentifier les clients VPN pour les connexions VPN de Site-à-Point. Vous exportez des données de certificat public (pas la clé privée) comme Base-64 encodé X.509 .cer fichier, soit en utilisant un certificat généré par une solution de certificat d’entreprise un certificat racine auto-signé. Vous ensuite importerez les données du certificat public à partir du certificat racine vers Azure. En outre, vous devez générer un certificat client à partir du certificat racine pour les clients. Chaque client qui souhaite se connecter au réseau virtuel à l’aide d’une connexion P2S doit disposer d’un certificat client a été généré à partir du certificat racine.

### <a name="cer"></a>1. obtenir le fichier .cer pour le certificat racine

Vous devez obtenir le certificat racine que vous voulez utiliser les données de certificat public.

- Si vous utilisez un système de certificats d’entreprise, procurez-vous le fichier .cer pour le certificat racine que vous voulez utiliser. 

- Si vous n’utilisez pas une solution de certificat d’entreprise, vous devez générer un certificat racine auto-signé. Pour obtenir la procédure Windows 10, vous pouvez faire référence à [l’utilisation des certificats racines auto-signés pour les configurations de Point-à-Site](vpn-gateway-certificates-point-to-site.md).


1. Pour obtenir un fichier .cer à partir d’un certificat, ouvrez **certmgr.msc** , puis localisez le certificat racine. Cliquez sur le certificat racine auto-signé et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**. L' **Assistant Exportation de certificat**s’ouvre.

2. Dans l’Assistant, cliquez sur **suivant**, sélectionnez **non, ne pas exporter la clé privée**, puis cliquez sur **suivant**.

3. Dans la page **Format de fichier d’exportation** , sélectionnez **Base-64 encodé X.509 (. CER).** Ensuite, cliquez sur **suivant**. 

4. Du **fichier à exporter**, **accédez** à l’emplacement auquel vous souhaitez exporter le certificat. **Nom de fichier**, nommez le fichier de certificat. Puis cliquez sur **suivant**.

5. Cliquez sur **Terminer** pour exporter le certificat.

### <a name="generate"></a>2. générer le certificat client

Ensuite, générez les certificats clients. Vous pouvez générer un certificat unique pour chaque client qui se connecte, ou vous pouvez utiliser le même certificat sur plusieurs clients. L’avantage de générer des certificats de client unique est la possibilité de révoquer un certificat unique si nécessaire. Dans le cas contraire, si tout le monde utilise le même certificat de client et que vous devez révoquer le certificat pour un client, vous devez générer et installer de nouveaux certificats pour tous les clients qui utilisent le certificat pour l’authentification. Les certificats clients sont installés sur chaque ordinateur client plus loin dans cet exercice.

- Si vous utilisez une solution de certificat d’entreprise, générer un certificat de client avec le format de la valeur nom commun 'name@yourdomain.com', plutôt que le format de NetBIOS 'Domaine\nom_utilisateur'. 

- Si vous utilisez un certificat auto-signé, consultez [utilisation des certificats racines auto-signés pour les configurations de Point-à-Site](vpn-gateway-certificates-point-to-site.md) pour générer un certificat client.

### <a name="exportclientcert"></a>3. exporter le certificat client

Un certificat client est requis pour l’authentification. Après avoir généré le certificat du client, l’exporter. Vous exportez le certificat du client sera plus tard être installé sur chaque ordinateur client.

1. Pour exporter un certificat client, vous pouvez utiliser *certmgr.msc*. Cliquez droit sur le certificat du client que vous souhaitez exporter et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**.
2. Exporter le certificat client avec la clé privée. Il s’agit d’un fichier *.pfx* . Assurez-vous d’enregistrer ou de mémoriser le mot de passe (clé) que vous définissez pour ce certificat.

### <a name="upload"></a>4. télécharger le fichier .cer du certificat racine

Déclarez la variable pour le nom du certificat, remplaçant la valeur par votre propre :

        $P2SRootCertName = "Mycertificatename.cer"

Ajouter les données de certificat publique pour le certificat racine vers Azure. Vous pouvez télécharger des fichiers pour les certificats racine jusqu'à 20. Vous ne téléchargez pas la clé privée pour le certificat racine vers Azure. Une fois le fichier .cer est téléchargé, Azure utilise pour authentifier les clients qui se connectent au réseau virtuel. 

Remplacer le chemin d’accès de fichier par votre propre et ensuite exécuter les applets de commande.
    
        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>Partie 4 - créer la passerelle VPN

Configurez et créez la passerelle réseau virtuels pour votre VNet. Le *GatewayType -* doit être **Vpn** et la *-VpnType* doit être **RouteBased**. Cette opération peut prendre jusqu'à 45 minutes.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>Partie 5 : téléchargement du package de configuration du client VPN

Les clients se connectant à Azure à l’aide de P2S doivent avoir un certificat de client et un package de configuration du client VPN installé. Les packages de configuration des clients VPN sont disponibles pour les clients Windows. Le package du client VPN contient des informations pour configurer le logiciel client VPN intégré à Windows et est spécifique à la connexion VPN que vous souhaitez vous connecter. Le package n’installe pas de logiciel supplémentaire. Consultez le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) pour plus d’informations.

1. Une fois la passerelle a été créée, vous pouvez télécharger le package de configuration du client. Cet exemple télécharge le package de clients 64 bits. Si vous souhaitez télécharger le client 32 bits, remplacez « Amd64 » par « x86 ». Vous pouvez également télécharger le client VPN à l’aide du portail Azure.

        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. L’applet de commande PowerShell renvoie un lien URL. Il s’agit d’un exemple de quel le recherche URL retourné comme :

        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Copiez et collez le lien qui est renvoyé à un navigateur web pour télécharger le package. Puis, installez le package sur l’ordinateur client. Si vous obtenez un message SmartScreen, cliquez sur **plus d’infos**, puis **quand même exécuter** pour installer le package.

4. Sur l’ordinateur client, accédez à **Paramètres réseau** et cliquez sur **VPN**. Vous verrez la connexion répertoriée. Il affiche le nom du réseau virtuel auquel il se connectera et de recherche semblable à cet exemple : 

    ![Client VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "Client VPN")


## <a name="clientcertificate"></a>Partie 6 - installer le certificat du client

Chaque ordinateur client doit disposer d’un certificat client pour s’authentifier. Lors de l’installation du certificat de client, vous devez le mot de passe qui a été créé lorsque le certificat client a été exporté.

1. Copiez le fichier .pfx sur l’ordinateur client.
2. Double-cliquez sur le fichier .pfx pour l’installer. Ne modifiez pas l’emplacement d’installation.

## <a name="connect"></a>Partie 7 - se connecter vers Azure

1. Pour vous connecter à votre VNet, sur l’ordinateur client, accédez à connexions VPN et localiser la connexion VPN que vous avez créé. Il porte le même nom que votre réseau virtuel. Cliquez sur **se connecter**. Un message peut s’afficher qui fait référence à l’aide du certificat. Dans ce cas, cliquez sur **Continuer** pour utiliser des privilèges élevés. 

2. Dans la page État de la **connexion** , cliquez sur **connexion** pour démarrer la connexion. Si vous voyez un écran **Sélectionner un certificat** , vérifiez que le certificat client affichant est celui que vous souhaitez utiliser pour vous connecter. S’il n’est pas le cas, utilisez la flèche de liste déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

    ![Connexion du client VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Connexion du client VPN")

3. Votre connexion doit maintenant être établie.

    ![Connexion établie] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Connexion établie")

## <a name="verify"></a>Partie 8 - vérifier votre connexion

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

## <a name="addremovecert"></a>Pour ajouter ou supprimer un certificat racine de confiance

Les certificats sont utilisés pour authentifier les clients VPN pour les connexions VPN de Site-à-Point. Les étapes suivantes vous guident dans l’ajout et la suppression des certificats racines. Lorsque vous ajoutez un fichier codé en Base64 X.509 (.cer) vers Azure, vous indiquez à Azure pour approuver le certificat racine représentant le fichier. 

Vous pouvez ajouter ou supprimer des certificats racine de confiance à l’aide de PowerShell ou dans le portail Azure. Si vous souhaitez effectuer cette opération via le portail d’Azure, accédez à votre **passerelle réseau virtuel > Paramètres > configuration de Point-à-site > certificats racine**. Les étapes suivantes vous guident tout au long de ces tâches à l’aide de PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Ajouter un certificat racine de confiance

Vous pouvez ajouter maximum de 20 fichiers .cer de certificat racine de confiance pour Azure. Suivez les étapes ci-dessous pour ajouter un certificat racine.

1. Créer et préparer le nouveau certificat racine que vous ajouterez à Azure. Exporter la clé publique comme Base-64 encodé X.509 (. CER) et l’ouvrir avec un éditeur de texte. Puis copier uniquement la section illustrée ci-dessous. 
 
    Copiez les valeurs, comme illustré dans l’exemple suivant :

    ![certificat] (./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificat")
    
2. Spécifiez le nom de certificat et les informations de la clé en tant que variable. Remplacer les informations par exemple de votre propre, comme illustré dans le code suivant :

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Ajouter le nouveau certificat racine. Vous ne pouvez ajouter qu’un seul certificat à la fois.

        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. Vous pouvez vérifier que le nouveau certificat a été correctement ajouté à l’aide de l’applet de commande suivant.

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Pour supprimer un certificat racine de confiance

Vous pouvez supprimer des certificats racine de confiance d’Azure. Lorsque vous supprimez un certificat de confiance, les certificats de client qui ont été générés à partir du certificat racine ne seront plus capable de se connecter à Azure via Site-à-Point. Si vous souhaitez que les clients puissent se connecter, ils doivent installer un nouveau certificat de client qui est généré à partir d’un certificat approuvé dans Azure.

1. Pour supprimer un certificat racine approuvé, modifiez l’exemple suivant :

    Déclarez les variables.

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Supprimer le certificat.

        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Utilisez l’applet de commande suivante pour vérifier que le certificat a été supprimé avec succès. 

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>Pour gérer la liste de certificats clients révoqués

Vous pouvez révoquer des certificats clients. La liste de révocation de certificats vous permet de refuser de manière sélective connexion Point-à-Site basée sur des certificats clients individuels. Si vous supprimez un .cer de certificat racine d’Azure, il révoque l’accès de tous les certificats du client généré/signé par le certificat révoqué. Si vous souhaitez révoquer un certificat client spécifique, pas à la racine, vous pouvez le faire. De cette façon, les autres certificats qui ont été générés à partir du certificat racine seront toujours valable.

La pratique courante consiste à utiliser le certificat racine pour gérer l’accès aux niveaux d’équipe ou de l’organisation, lors de l’utilisation de certificats clients révoqués pour le contrôle d’accès précis sur les utilisateurs individuels.

### <a name="revoke-a-client-certificate"></a>Révoquer un certificat client

1. Obtenir l’empreinte de certificat du client à révoquer.

        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Ajoutez l’empreinte numérique à la liste de l’empreinte numérique révoqué.

        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Vérifiez que l’empreinte a été ajoutée à la liste de révocation de certificats. Vous devez ajouter une empreinte à la fois.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Réactiver un certificat client

Vous pouvez rétablir un certificat client en supprimant l’empreinte à partir de la liste de certificats clients révoqués.

1.  Supprimer l’empreinte de la liste de l’empreinte de certificat client révoqué.

        Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Vérifiez si l’empreinte est supprimée de la liste de révocation.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter un ordinateur virtuel au réseau virtuel. Étapes, consultez [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .