<properties
   pageTitle="Configurer un réseau virtuel et une passerelle pour ExpressRoute le portail classique | Microsoft Azure"
   description="Cet article vous guide dans le processus de configuration d’un réseau virtuel pour ExpressRoute en utilisant le modèle de déploiement classique et le portail classique."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/20/2016"
   ms.author="cherylmc"/>

# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Créer un réseau virtuel pour ExpressRoute le portail classique

Les étapes décrites dans cet article vous guidera tout au long de la configuration d’un réseau virtuel et une passerelle de réseau virtuel pour une utilisation avec ExpressRoute en utilisant le modèle de déploiement classique et le portail classique.

Si vous recherchez des instructions pour le modèle de déploiement du Gestionnaire de ressources, vous pouvez utiliser les articles suivants : [créer un réseau virtuel à l’aide de PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) et [Ajouter une passerelle VPN à un VNet du Gestionnaire de ressources pour ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="create-a-classic-vnet-and-gateway"></a>Créer une VNet et une passerelle classique

Les étapes suivantes créent un VNet classique et une passerelle réseau virtuel. Si vous avez déjà un VNet classique, consultez la section [configurer un VNet classique existante](#config) dans cet article.

1. Ouvrez une session [Azure portal classique](http://manage.windowsazure.com).

2. Dans l’angle inférieur gauche de l’écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services de réseau**, puis cliquez sur **Réseau virtuel**. Cliquez sur **Créer des personnalisées** pour lancer l’Assistant de configuration.

3. Sur la page de **Détails de réseau virtuel** , entrez les informations suivantes :

    - **Nom** – nom du réseau virtuel. Vous utiliserez ce nom de réseau virtuel lorsque vous déployez vos instances de machines virtuelles et de PaaS, vous pouvez donc pas à rendre le nom plus compliqué.
    - **Emplacement** – l’emplacement est directement liée à l’emplacement physique (région) où vous souhaitez que vos ressources (VMs) se trouve. Par exemple, si vous souhaitez que les ordinateurs virtuels que vous déployez à ce réseau virtuel se trouver physiquement aux Etats-Unis orientale, sélectionnez cet emplacement. Vous ne pouvez pas modifier la région associée à votre réseau virtuel après que l’avoir créé.

4. Dans la page **serveurs DNS et la connectivité VPN** , entrez les informations suivantes, puis cliquez sur la flèche suivant dans le coin inférieur droit. 

    - **Serveurs DNS** - Entrez le nom du serveur DNS et l’adresse IP, ou sélectionnez un serveur DNS inscrit précédemment dans le menu contextuel. Ce paramètre ne crée pas un serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel.
    - **Connectivité de site à Site** - sélectionner la case à cocher pour **configurer une connexion VPN de site à site**.
    - **ExpressRoute** – sélectionnez la case à cocher **Utiliser ExpressRoute**. Cette option apparaît uniquement si vous avez choisi de **configurer un VPN de Site à Site**.
    - **Réseau local** - vous devez disposer d’un site de réseau local pour ExpressRoute. Toutefois, dans le cas d’une connexion ExpressRoute, les préfixes d’adresse spécifiées pour le site de réseau local seront ignorées. Au lieu de cela, les préfixes d’adresse publiés à Microsoft par le biais du circuit ExpressRoute seront utilisés pour le routage.<BR>Si vous disposez déjà d’un réseau local est créé pour votre connexion ExpressRoute, vous pouvez le sélectionner dans la liste déroulante. Si ce n’est pas le cas, sélectionnez **spécifier un nouveau réseau Local**.

5. La page de **connexion de Site à Site** s’affiche si vous avez sélectionné pour spécifier un nouveau réseau local à l’étape précédente. Pour configurer votre réseau local, entrez les informations suivantes et cliquez sur la flèche suivant. 

    - **Nom** : le nom que vous souhaitez appeler votre local (local) réseau de site.
    - **Espace d’adressage** - y compris IP de début et CIDR (nombre d’adresses). Vous pouvez spécifier n’importe quelle plage adresse tant qu’elle ne chevauche pas la plage d’adresses pour votre réseau virtuel. En général, cela devrait spécifier les plages d’adresses pour vos réseaux locaux, mais dans le cas de ExpressRoute, ces paramètres ne sont pas utilisés. Toutefois, ce paramètre est requis pour créer le réseau local lorsque vous utilisez le portail classique.
    - **Ajouter un espace adresse** - ce paramètre n’est pas pertinente pour ExpressRoute.


6. Sur la page des **Espaces d’adressage de réseau virtuel** , entrez les informations suivantes et cliquez sur la coche dans le coin inférieur droit pour configurer votre réseau. 

    - **Espace d’adressage** - y compris le nombre IP et l’adresse de démarrage. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas un des espaces d’adressage que vous avez sur votre réseau local.
    - **Ajouter sous-réseau** - en y compris IP de début et le nombre d’adresses. Des sous-réseaux supplémentaires ne sont pas nécessaires.
    - **Sous-réseau de passerelle ajouter** - cliquez ici pour ajouter le sous-réseau de passerelle. Le sous-réseau de passerelle est utilisé uniquement pour la passerelle réseau virtuel et est requis pour cette configuration.<BR>Le sous-réseau de passerelle CIDR (nombre d’adresses) pour ExpressRoute doit être /28 ou plus (/ 27/26 etc..). Cela permet de suffisamment d’adresses IP de ce sous-réseau permet la configuration à utiliser. Dans le portail classique, si vous avez sélectionné la case à cocher pour utiliser ExpressRoute, le portail spécifie un sous-réseau de passerelle avec /28.  Vous ne pouvez pas ajuster le nombre d’adresses CIDR dans le portail classique. Le sous-réseau de passerelle s’affiche en tant que **passerelle** dans le portail classique, bien que le nom réel du sous-réseau passerelle qui est créé est en fait **GatewaySubnet**. Vous pouvez afficher ce nom à l’aide de PowerShell ou dans le portail Azure.

7. Cliquez sur la case à cocher au bas de la page et commence à créer votre réseau virtuel. Lorsqu’elle est terminée, vous verrez **que créé** répertoriés sous **état** de la page **réseaux** dans le portail classique.

## <a name="gw"></a>Création de la passerelle

1. Dans la page **réseaux** , cliquez sur le réseau virtuel que vous venez de créer, puis cliquez sur le **tableau de bord** , en haut de la page.

2. En bas de la page de **tableau de bord** , cliquez sur **Créer une passerelle** et activez le **Routage dynamique**. Cliquez sur **Oui** pour confirmer que vous souhaitez créer une passerelle.

3. Lorsque la passerelle lance la création, vous verrez un message vous signalant que la passerelle a été démarrée. Il peut prendre jusqu'à 45 minutes de la passerelle à créer.

4. Lier votre réseau à un circuit. Suivez les instructions dans l’article [comment créer un lien VNets aux circuits de ExpressRoute](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Configurer un VNet classique existante pour ExpressRoute

Si vous avez déjà un VNet classique, vous pouvez le configurer pour se connecter à ExpressRoute dans le portail classique. Les paramètres de la même manière que les sections ci-dessus, sont donc lire ces sections pour vous familiariser avec les paramètres requis. Si vous souhaitez créer une connexion de coexistence ExpressRoute/Site à Site, consultez [cet article](expressroute-howto-coexist-classic.md) pour connaître les étapes. Elles sont différentes de la procédure décrite dans cet article.
 
1. Vous devez créer le réseau local avant de vous mettre à jour le reste de vos paramètres de VNet. Pour créer un réseau local, qui est requis lors de la configuration ExpressRoute via le portail classique, cliquez sur **Nouveau** **>** **Services réseau** **>** **Réseau virtuel** **>** **réseau local d’ajouter**. Suivez les étapes de l’Assistant pour créer le réseau local.

2. Utilisez **configurer** page pour le reste des paramètres de votre VNet de mettre à jour et à associer la VNet pour le réseau local.

3. Après avoir configuré les paramètres, consultez la section [créer la passerelle](#gw) de cet article pour créer la passerelle.


## <a name="next-steps"></a>Étapes suivantes

- Si vous souhaitez ajouter à votre réseau virtuel d’ordinateurs virtuels, consultez [parcours de formation de Machines virtuelles](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Si vous souhaitez en savoir plus sur ExpressRoute, consultez la [Vue d’ensemble de ExpressRoute](expressroute-introduction.md).


 
