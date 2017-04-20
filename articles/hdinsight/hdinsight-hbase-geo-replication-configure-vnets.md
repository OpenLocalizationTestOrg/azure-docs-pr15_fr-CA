<properties 
   pageTitle="Configurer la connexion VPN entre deux réseaux virtuels | Microsoft Azure" 
   description="Apprenez à configurer des connexions VPN et résolution de noms de domaine entre deux réseaux virtuels Azure et comment configurer la réplication geo HBase." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-a-vpn-connection-between-two-azure-virtual-networks"></a>Configurer une connexion VPN entre deux réseaux virtuels Azure  

> [AZURE.SELECTOR]
- [Configurez la connectivité VPN](hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurer le service DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurer la réplication de HBase](hdinsight-hbase-geo-replication.md) 

Connectivité de site à site Azure réseau virtuel utilise une passerelle VPN afin de fournir un tunnel sécurisé à l’aide de Ipsec/IKE. La VNets peut être dans différents abonnements et des régions différentes. Vous pouvez même combiner VNet pour communications VNet avec des configurations sur plusieurs sites. Il existe plusieurs raisons pour VNet de VNet de la connectivité :

- Redondance entre région géographique et géo-présence 
- Applications n-tier régionale avec une limite d’isolement forte 
- Coupe l’abonnement, la communication inter organisationnelle dans Azure

Pour plus d’informations, consultez [configurer une VNet à VNet connexion](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). 

Pour voir la vidéo :

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

Ce didacticiel est une partie de la [série] [ hdinsight-hbase-replication] sur la création de HBase géo-réplication. 

- Configurer une connexion VPN entre deux réseaux virtuels (ce didacticiel)
- [Configurer le service DNS pour les réseaux virtuels][hdinsight-hbase-geo-replication-dns]
- [Configurer la réplication de zone géographique HBase][hdinsight-hbase-geo-replication]

Le diagramme suivant illustre les deux réseaux virtuels, que vous allez créer dans ce didacticiel :

![Diagramme de réseau virtuel de réplication HDInsight HBase][img-vnet-diagram]
 

##<a name="prerequisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Une station de travail avec PowerShell d’Azure**.

    Avant d’exécuter des scripts PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l’aide de l’applet de commande suivante :

        Add-AzureAccount

    Si vous avez plusieurs abonnements d’Azure, utilisez l’applet de commande suivante pour définir l’abonnement actuel :

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


>[AZURE.NOTE] Les noms de service Azure et machine virtuelle doit être unique. Le nom utilisé dans ce didacticiel est Contoso-[nom du Service/VM Azure]-[UE / États-Unis]. Par exemple, Contoso-VNet-UE est le réseau virtuel Azure dans le centre de données en Europe du Nord ; Contoso-États-Unis-DNS sont le serveur DNS VM dans le centre de données américain d’Extrême-Orient. Vous devez trouver vos propres noms.
 

##<a name="create-two-azure-vnets"></a>Créer deux Azure VNets



**Pour créer un réseau virtuel appelé Contoso-VNet-UE-Europe du Nord**

1.  Connectez-vous à l' [Azure Portal classique][azure-portal].
2.  Cliquez sur **Nouveau**, **SERVICES réseau**, **réseau virtuel**, **Création personnalisée**.
3.  Entrez :

    - **Nom**: Contoso-VNet-UE
    - **Emplacement**: Europe du Nord

        Ce didacticiel utilise des centres de données en Europe du Nord et les États-Unis. Vous pouvez choisir vos propres centres de données.
4.  Entrez :

    - **Serveur DNS**: (laisser vide) 
    
        Vous aurez besoin de votre propre serveur DNS pour la résolution de nom au sein des réseaux virtuels. Pour plus d’informations sur le moment d’utiliser la résolution de nom Azure fourni et quand utiliser votre propre serveur DNS, reportez-vous à [La résolution de nom (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Pour obtenir des instructions configurer la résolution de nom entre VNets, voir [Configurer le serveur DNS entre deux réseaux virtuels Azure][hdinsight-hbase-dns].
  
    - **Configurer un réseau privé virtuel site-à-point**: (désactivé)

        Point-à-site ne s’applique pas à ce scénario.

    - **Configurer un VPN de site à site**: (désactivé)
    
        Vous allez configurer la connexion VPN de site à site au réseau virtuel Azure dans le centre de données américain d’Extrême-Orient.
5.  Entrez :

    -   **Adresse IP de départ espace**: 10.1.0.0
    -   **Adresse espace CIDR**: / 16
    -   **Début de-1 du sous-réseau IP**: 10.1.0.0
    -   **Routage inter-domaines sans classe de sous-réseau-1**: / 24

    L’espace d’adressage peut ne se chevauchent pas avec le réseau virtuel aux États-Unis.  

**Pour créer un réseau virtuel appelé Contoso-VNet-UE en Europe de l’ouest**

- Répétez la dernière procédure avec les valeurs suivantes :

    - **Nom**: Contoso-VNet-US
    - **Emplacement**: américains Extrême-Orient
     
    - **Serveur DNS**: (laisser vide)
    - **Configurer un réseau privé virtuel site-à-point**: (désactivé)
    - **Configurer un VPN de site à site**: (désactivé)
     
    - **Adresse IP de départ espace**: 10.2.0.0
    - **Adresse espace CIDR**: / 16
    - **Début de-1 du sous-réseau IP**: 10.2.0.0
    - **Routage inter-domaines sans classe de sous-réseau-1**: / 24

















##<a name="configure-a-vpn-connection-between-the-two-vnets"></a>Configurer une connexion VPN entre les deux VNets

###<a name="create-local-networks"></a>Créer des réseaux locaux

Lorsque vous créez un VNet pour la configuration de VNet, vous devez configurer chaque VNet pour identifier l’autre comme un site de réseau local. Dans cette section, vous allez configurer chaque VNet sous la forme d’un réseau local. Les réseaux locaux partagent les mêmes espaces d’adressage IP avec le VNet correspondante.

![Configuration des Azure VPN site à site - azure réseaux locaux][img-vnet-lnet-diagram]


**Pour créer un réseau local appelé Contoso-LNet-Union européenne correspondant à l’espace d’adressage réseau Contoso-VNet-UE**

1. À partir du portail classique d’Azure, cliquez sur **Nouveau**, **SERVICES réseau**, **Réseau virtuel**, **Ajouter un réseau LOCAL**.
3. Entrez :

    - **Nom**: Contoso-LNet-UE
    - **Adresse IP du périphérique VPN**: 192.168.0.1 (cette adresse doit être mis à jour ultérieurement)

        En général, vous utiliseriez la véritable adresse IP externe pour un périphérique VPN. De VNet à VNet des configurations, vous allez utiliser l’adresse IP de la passerelle VPN. Étant donné que vous n’avez créé les passerelles VPN pour les deux VNets encore, vous entrez une adresse IP d’arbitraires et revenez à corriger.
4.  Entrez :

    - **Adresse IP de départ espace :** 10.1.0.0
    - **Routage inter-domaines sans classe espace adresse :** /16
    
    Cet attribut doit correspondre exactement à la plage que vous avez spécifié précédemment pour Contoso-VNet-EU.

**Pour créer un réseau local appelé Contoso-LNet-US correspondant à l’espace d’adressage réseau Contoso-VNet-US**

- Répétez la dernière procédure avec les paramètres suivants :

    - **Nom**: Contoso-LNet-US
    - **Adresse IP du périphérique VPN**: 192.168.0.1 (cette adresse doit être mis à jour ultérieurement)
     
    - **Adresse IP de départ espace**: 10.2.0.0
    - **Adresse espace CIDR**: / 16


###<a name="create-vpn-gateways"></a>Créer des passerelles VPN

Il y a deux parties dans cette configuration. Tout d’abord vous configurez une connexion de site à site VNet à un réseau local, puis vous créez un VPN de routage dynamique. VNet à VNet nécessite des passerelles VPN d’Azure avec VPN de routage dynamiques. Azure VPN de routage statiques ne sont pas pris en charge.

**Pour configurer la connexion de site à site Contoso-VNet-UE à Contoso-LNet-US**

1.  À partir du portail classique d’Azure, cliquez sur **réseaux** dans le volet de gauche,
2.  Cliquez sur **Contoso-VNet-UE**.
3.  Cliquez sur l’onglet **CONFIGUE** .
4.  Vérifiez la **connexion au réseau local**.
5.  Dans un **Réseau LOCAL**, sélectionnez **Contoso-LNet-US**.
6.  Cliquez sur le **sous-réseau de passerelle d’ajouter** dans la section d’espaces d’adresse de réseau virtuel.
7.  Cliquez sur **Enregistrer**.
8.  Cliquez sur **OK** pour confirmer.


**Pour créer une passerelle VPN pour Contoso-VNet-UE**

1.  À partir du portail classique d’Azure, cliquez sur l’onglet **tableau de bord** .
4.  Cliquez sur **Créer une passerelle** sur le bas de la page, puis cliquez sur **Routage dynamique**.
5.  Cliquez sur **Oui** pour confirmer. Notez le graphique de passerelle dans la page devient jaune et indique que la création d’une passerelle. Il faut environ 15 minutes pour la passerelle créer.

    Lorsque l’état de la passerelle passe à la connexion, l’adresse IP pour chaque passerelle sera visible dans le tableau de bord. Notez l’adresse IP qui correspond à chaque VNet, en prenant soin de ne pas pour les mélanger. Ce sont les adresses IP qui seront utilisées lorsque vous modifiez les adresses IP de votre espace réservé pour le périphérique VPN dans les réseaux locaux.

6.  Effectuez une copie de l' **Adresse IP de la passerelle**. Vous allez l’utiliser pour configurer l’adresse IP de la passerelle VPN pour Contoso-VNet-EU, dans la section suivante.

**Pour créer une passerelle VPN pour Contoso-VNet-UE**

- Répétez la procédure deux derniers pour configurer la connectivité de site à site Contoso-VNet-US à Contoso-LNet-UE et du créer une passerelle VPN pour Contoso-Vnet-US. Lorsque vous avez terminé, vous aurez l’adresse IP de la passerelle VPN pour Contoso-VNet-US.


### <a name="set-the-vpn-device-ip-addresses-for-local-networks"></a>Définir le périphérique VPN des adresses IP pour les réseaux locaux
Dans la dernière section, vous créez une passerelle VPN pour chacun de la VNets. Vous avez reçu les adresses IP des passerelles VPN. Maintenant vous pouvez revenir en arrière pour configurer des adresses IP de réseau local VPN périphérique.

**Pour configurer l’adresse IP du périphérique VPN pour Contoso-LNet-UE** 

1.  À partir du portail classique d’Azure, cliquez sur **les réseaux** dans le volet gauche.
2.  Cliquez sur **Réseaux locaux** à partir du haut.
3.  Cliquez sur **Contoso-LNet-UE**et puis cliquez sur **Modifier** dans la partie inférieure.
4.  Mise à jour **d’adresse IP du périphérique VPN**.  Il s’agit de l’adresse que vous obtenez à partir de l’onglet de tableau de bord de Contoso-VNET-UE.
5.  Cliquez sur le bouton de droite.
6.  Cliquez sur le bouton de vérification.

**Pour configurer l’adresse IP du périphérique VPN pour Contoso-LNet-US** 

- Répétez la dernière procédure pour configurer l’adresse IP du périphérique VPN pour Contoso-LNet-US.

###<a name="set-vnet-gateway-keys"></a>Définir des clés de passerelle VNet

Les passerelles Vnet utilisent une clé partagée pour authentifier les connexions entre les réseaux virtuels. La clé ne peut pas être configurée à partir du portail classique d’Azure. Vous devez utiliser PowerShell ou le Kit de développement .NET.

**Pour définir les clés**

1. À partir de votre station de travail, ouvrir **Windows PowerShell ISE** ou la console Windows PowerShell.
2. Mettre à jour les paramètres de ce script de suivre et de l’exécuter :

        Add-AuzreAccount
        Select-AzureSubscription -[AzureSubscriptionName]
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##<a name="check-the-vpn-connection"></a>Vérifiez la connexion VPN 

Sans les ordinateurs virtuels déployés à la VNets, vous pouvez utiliser le diagramme visuel de réseau virtuel la page tableau de bord de VNet sur le portail classique Azure pour vérifier l’état de la connexion :

![Réseau virtuel de réplication HDInsight HBase état de la connexion VPN][img-vpn-status]
  



##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à configurer une connexion VPN entre deux réseaux virtuels Azure. Les autres deux articles dans la couverture de série :

- [Configurer le service DNS entre deux réseaux virtuels Azure][hdinsight-hbase-geo-replication-dns]
- [Configurer la réplication de zone géographique HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-dns.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-lnet-diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-status.png 
