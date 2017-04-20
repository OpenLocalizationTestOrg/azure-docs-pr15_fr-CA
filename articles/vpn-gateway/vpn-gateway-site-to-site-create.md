<properties
   pageTitle="Créer un réseau virtuel avec une connexion de passerelle VPN de site à site en utilisant le portail classique Azure | Microsoft Azure"
   description="Créer un VNet avec une connexion de passerelle VPN de S2S pour la coexistence et configurations hybride utilisant le modèle de déploiement classique."
   services="vpn-gateway"
   documentationCenter=""
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
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-classic-portal"></a>Créer un VNet avec une connexion de Site à Site via le portail classique Azure

> [AZURE.SELECTOR]
- [Responsable de ressources - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Standard - portail classique](vpn-gateway-site-to-site-create.md)

Cet article vous guide dans le processus de création d’un réseau virtuel et une connexion de passerelle VPN site à site sur votre réseau local en utilisant le modèle de déploiement classique et le portail classique. Les connexions de site à Site peuvent être utilisées pour la coexistence et hybride configurations.

![Diagramme de site à Site] (./media/vpn-gateway-site-to-site-create/site2site.png "site à site")


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modèles de déploiement et les méthodes pour les connexions de Site à Site

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant montre les modèles de déploiement actuellement disponibles et les méthodes pour les configurations de Site à Site. Lorsqu’un article avec des étapes de configuration n’est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurations supplémentaires 

Si vous souhaitez connecter entre eux les VNets, voir [configuration d’une connexion VNet-à-VNet pour le modèle de déploiement classique](virtual-networks-configure-vnet-to-vnet-connection.md). Si vous souhaitez ajouter une connexion de Site à Site à un VNet qui dispose déjà d’une connexion, consultez [Ajouter une connexion de S2S d’un VNet avec une connexion de passerelle VPN existante](vpn-gateway-multi-site.md).
 
## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments suivants avant de commencer la configuration.

- Un périphérique VPN compatible et une personne est en mesure de le configurer. [Sur les périphériques VPN](vpn-gateway-about-vpn-devices.md), reportez-vous à la section. Si vous n’êtes pas familiarisé avec la configuration de votre périphérique VPN, ou que vous ne connaissez pas l’adresse IP des plages qui se trouvent dans vos locaux sur configuration du réseau, vous devez coordonner avec une personne qui pourra vous fournir ces informations.

- Adresse IP publique tourné vers l’extérieur pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.

- Un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).


## <a name="CreateVNet"></a>Créer votre réseau virtuel

1. Ouvrez une session [Azure portal classique](https://manage.windowsazure.com/).

2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services de réseau**, puis cliquez sur **Réseau virtuel**. Cliquez sur **Créer des personnalisées** pour lancer l’Assistant de configuration.

3. Pour créer votre VNet, entrez vos paramètres de configuration dans les pages suivantes :

## <a name="Details"></a>Page de détails de réseau virtuel

Entrez les informations suivantes :

- **Nom**: nom du réseau virtuel. Par exemple, *EastUSVNet*. Vous utiliserez ce nom de réseau virtuel lorsque vous déployez vos instances de machines virtuelles et de PaaS, vous pouvez donc pas à rendre le nom plus compliqué.
- **Emplacement**: l’emplacement est directement liée à l’emplacement physique (région) où vous souhaitez que vos ressources (VMs) se trouve. Par exemple, si vous souhaitez que les ordinateurs virtuels que vous déployez à ce réseau virtuel se trouver physiquement aux *Etats-Unis orientale*, sélectionnez cet emplacement. Vous ne pouvez pas modifier la région associée à votre réseau virtuel après que l’avoir créé.

## <a name="DNS"></a>Les serveurs DNS et la page de connexion VPN

Entrez les informations suivantes, puis cliquez sur la flèche suivant dans le coin inférieur droit.

- **Serveurs DNS**: entrez le nom du serveur DNS et l’adresse IP, ou sélectionnez un serveur DNS inscrit précédemment dans le menu contextuel. Ce paramètre ne crée pas un serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel.
- **Configurer une connexion VPN Site à Site**: sélectionnez la case à cocher pour **configurer une connexion VPN de site à site**.
- **Réseau local**: un réseau local représente votre emplacement physique sur site. Vous pouvez sélectionner un réseau local que vous avez créé précédemment, ou vous pouvez créer un réseau local. Toutefois, si vous choisissez d’utiliser un réseau local que vous avez créé précédemment, accédez à la page de configuration des **Réseaux locaux** et vérifiez que l’adresse IP du périphérique VPN (adresse IPv4 en vis-à-vis publique) pour le périphérique VPN est exacte.

## <a name="Connectivity"></a>Page de connexion de site à site

Si vous créez un nouveau réseau local, vous verrez la page de **Connexion de Site à Site** . Si vous souhaitez utiliser un réseau local que vous avez créé précédemment, cette page n’apparaît pas dans l’Assistant, et vous pouvez passer à la section suivante.

Entrez les informations suivantes, puis cliquez sur la flèche suivant.

-   **Nom**: le nom que vous souhaitez appeler votre local (local) réseau de site.
-   **Adresse IP du périphérique VPN**: l’adresse IPv4 publique en vis-à-vis de votre périphérique VPN local que vous utilisez pour vous connecter à Azure. Impossible de trouver le périphérique VPN derrière un NAT.
-   **Espace d’adressage**: inclure le démarrage IP et CIDR (nombre d’adresses). Vous spécifiez l’adresse range(s) que vous souhaitez envoyer par l’intermédiaire de la passerelle réseau virtuel vers l’emplacement de votre site local. Si une adresse IP de destination se situe dans les plages que vous spécifiez ici, il est acheminé par l’intermédiaire de la passerelle réseau virtuel.
-   **Ajouter un espace adresse**: Si vous avez plusieurs plages d’adresses que vous souhaitez envoyer par l’intermédiaire de la passerelle réseau virtuel, spécifiez chaque plage d’adresses supplémentaires. Vous pouvez ajouter ou supprimer des plages plus loin sur la page **Réseau Local** .

## <a name="Address"></a>Page d’espaces d’adresse de réseau virtuel

Spécifiez la plage d’adresses que vous souhaitez utiliser pour votre réseau virtuel. Ce sont les adresses IP dynamiques (DIP) qui seront affectés aux ordinateurs virtuels et les autres instances de rôle que vous déployez à ce réseau virtuel.

Il est particulièrement important sélectionner une plage qui ne se chevauche pas avec toutes les plages qui sont utilisés pour votre réseau local. Vous devez coordonner avec votre administrateur réseau. Votre administrateur de réseau devra peut-être imaginer une plage d’adresses IP à partir de votre espace d’adresses de réseau local à utiliser pour votre réseau virtuel.

Entrez les informations suivantes, puis cliquez sur la coche dans le coin inférieur droit pour configurer votre réseau.

- **Espace d’adressage**: inclure le démarrage d’adresse IP et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas un des espaces d’adressage que vous avez sur votre réseau local.
- **Ajouter sous-réseau**: incluent un IP de début et le nombre d’adresses. Des sous-réseaux supplémentaires ne sont pas obligatoires, mais vous souhaiterez peut-être créer un sous-réseau distinct pour les machines virtuelles qui auront creux statiques. Ou vous souhaiterez peut-être que vos ordinateurs virtuels dans un sous-réseau distinct à partir de vos autres instances de rôle.
- **Sous-réseau de passerelle ajouter**: cliquez ici pour ajouter le sous-réseau de passerelle. Le sous-réseau de passerelle est utilisé uniquement pour la passerelle réseau virtuel et est requis pour cette configuration.

Cliquez sur la case à cocher au bas de la page et commence à créer votre réseau virtuel. Lorsqu’elle est terminée, vous verrez **que créé** répertoriés sous **statut** dans la page **réseaux** dans le portail classique d’Azure. Après que le VNet a été créé, vous pouvez ensuite configurer votre passerelle réseau virtuel.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="VNetGateway"></a>Configurez votre passerelle réseau virtuel

Configurez la passerelle réseau virtuel pour créer une connexion de site à site sécurisée. Voir [configuration d’une passerelle de réseau virtuel dans Azure portal classique](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à des réseaux virtuels. Consultez la documentation de [Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/) pour plus d’informations.
