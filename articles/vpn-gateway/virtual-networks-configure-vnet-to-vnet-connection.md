<properties
   pageTitle="Configurer une connexion de VNet-à-VNet pour le modèle de déploiement classique | Microsoft Azure"
   description="Comment se connecter à des réseaux virtuels Azure à l’aide de PowerShell et le portail classique Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>Configurer une connexion de VNet-à-VNet pour le modèle de déploiement classique

> [AZURE.SELECTOR]
- [Responsable de ressources - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Standard - portail classique](virtual-networks-configure-vnet-to-vnet-connection.md)



Cet article vous guide à travers les étapes pour créer et connecter des réseaux virtuels à l’aide du modèle de déploiement classique (également appelé gestion des services). La procédure suivante utilise le portail classique Azure pour créer VNets et passerelles et PowerShell pour configurer la connexion VNet-à-VNet. Vous ne pouvez pas configurer la connexion dans le portail.

![VNet diagramme de connectivité VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)


### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modèles de déploiement et les méthodes pour les connexions de VNet-à-VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant montre les modèles de déploiement actuellement disponibles et les méthodes pour les configurations VNet-à-VNet. Lorsqu’un article avec des étapes de configuration n’est disponible, nous attacher directement à partir de cette table.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>À propos des connexions de VNet-à-VNet

Connexion d’un réseau virtuel à un autre réseau virtuel (VNet à VNet) est similaire à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité permet de fournir un tunnel sécurisé à l’aide de IPsec/IKE une passerelle VPN. 

Le VNets que vous vous connectez peut être dans différents abonnements et des régions différentes. Vous pouvez combiner VNet pour communications VNet avec des configurations sur plusieurs sites. Cela vous permet d’établir des topologies de réseau qui associent la connectivité de coexistence avec une connectivité réseau entre virtuel.


### <a name="why-connect-virtual-networks"></a>Pourquoi connecter des réseaux virtuels ?

Voulez-vous vous connecter des réseaux virtuels pour les raisons suivantes :

- **Redondance entre région géographique et géo-présence**
    - Vous pouvez configurer votre propre géo-réplication ou de synchronisation avec une connectivité sécurisée sans passer par les points de terminaison faisant face à Internet.
    - Avec équilibrage de la charge Azure et Microsoft ou technologie de clustering tierce-partie, vous pouvez configurer la charge de travail hautement disponible avec redondance géographique dans plusieurs régions Azure. Un exemple consiste à paramétrer SQL toujours sur des groupes de disponibilité répartissant sur plusieurs régions Azure.

- **Applications n-tier régionale avec une limite d’isolement forte**
    - Dans la même région, vous pouvez configurer des applications multicouches avec plusieurs VNets connecté avec isolation renforcée et sécuriser la communication inter-couche.

- **Coupe l’abonnement, la communication inter organisationnelle dans Azure**
    - Si vous avez plusieurs abonnements Azure, vous pouvez vous connecter à partir de souscriptions différentes charges de travail ensemble en toute sécurité entre les réseaux virtuels.
    - Pour les entreprises ou les fournisseurs de services, vous pouvez activer la communication inter-organisation avec la technologie VPN sécurisée dans Azure.

### <a name="vnet-to-vnet-faq-for-classic-vnets"></a>VNet à VNet Forum aux questions sur VNets classique

- Les réseaux virtuels peuvent être dans les abonnements identiques ou différents.

- Les réseaux virtuels peuvent être dans les régions Azure identiques ou différentes (emplacements).

- Un service en nuage ou une point de terminaison d’équilibrage de la charge ne peut pas s’étendre sur des réseaux virtuels, même s’ils sont connectés entre eux.

- Les périphériques VPN ne requiert pas relier plusieurs réseaux virtuels.

- VNet-à-VNet prend en charge la connexion de réseaux virtuels Azure. Il ne pas en charge les machines virtuelles qui se connecte ou services qui ne sont pas déployés sur un réseau virtuel en nuage.

- VNet-à-VNet requiert des passerelles de routage dynamiques. Passerelles de routage statiques Azure ne sont pas pris en charge.

- Connectivité de réseau virtuel peut être utilisée simultanément avec les réseaux privés virtuels de plusieurs sites. Il existe un maximum de 10 tunnels VPN de passerelle réseau virtuel VPN connectant à d’autres réseaux virtuels, soit les sites locaux.

- Les espaces d’adressage des sites de réseau local virtuels local et les réseaux ne doivent pas se chevaucher. Chevauchement des espaces d’adressage entraîne la création de réseaux virtuels ou les fichiers de configuration netcfg téléchargement échoue.

- Tunnels redondants entre deux réseaux virtuels ne sont pas pris en charge.

- Tous les tunnels VPN pour le VNet, y compris les P2S VPN, partagent la bande passante disponible pour la passerelle VPN et la même disponibilité de passerelle VPN SLA dans Azure.

- VNet-à-VNet trafic transite sur la dorsale Azure.


## <a name="step1"></a>Étape 1 : planifiez vos plages d’adresses IP

Il est important de décider les plages que vous utiliserez pour configurer vos réseaux virtuels. Pour cette configuration, il se peut que vous devez vous assurer qu’aucun de vos plages VNet se chevauchent avec eux, ou avec n’importe lequel des réseaux locaux auxquelles ils se connectent à.

Le tableau suivant montre comment définir votre VNets. Utilisez les plages à titre indicatif uniquement. Notez que les plages de vos réseaux virtuels. Vous avez besoin de ces informations pour les étapes ultérieures.

**Exemples de paramètres**

|Réseau virtuel  |Espace d’adressage               |Région      |Se connecte à un site de réseau local|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1            |VNet1 (10.1.0.0/16)         |États-Unis Ouest     |VNet2Local (10.2.0.0/16)      |
|VNet2            |VNet2 (10.2.0.0/16)         |Nord-est du Japon  |VNet1Local (10.1.0.0/16)      |
  
## <a name="step-2---create-vnet1"></a>Étape 2 - Création de VNet1

Dans cette étape, nous avons créer VNet1. Lorsque vous utilisez un des exemples, veillez à entrer vos propres valeurs. Si votre VNet existe déjà, vous n’avez pas besoin d’effectuer cette étape. Mais, vous n’avez pas besoin de vérifier que les plages d’adresses IP ne se chevauchent pas avec les plages de votre deuxième VNet, ou avec un de l’autre VNets auquel vous souhaitez vous connecter.

1. Ouvrez une session [Azure portal classique](https://manage.windowsazure.com). Dans cet article, nous utilisons le portail classique car certains des paramètres de configuration requis ne sont pas encore disponibles dans le portail Azure.

2. Dans l’angle inférieur gauche de l’écran, cliquez sur **Nouveau** > **Services réseau** > **Réseau virtuel** > **Personnalisé créer** pour lancer l’Assistant de configuration. Mesure que vous parcourez l’Assistant, ajoutez les valeurs spécifiées pour chaque page.

### <a name="virtual-network-details"></a>Détails du réseau virtuel

Sur la page de détails de réseau virtuel, entrez les informations suivantes :

  ![Détails du réseau virtuel](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nom** - nom du réseau virtuel. Par exemple, VNet1.
  - **Emplacement** – lorsque vous créez un réseau virtuel, vous associez à un emplacement Azure (région). Par exemple, si vous souhaitez que vos ordinateurs virtuels qui sont déployés dans votre réseau virtuel se trouver physiquement aux Etats-Unis Ouest, sélectionnez cet emplacement. Vous ne pouvez pas modifier l’emplacement associé à votre réseau virtuel après que l’avoir créé.

### <a name="dns-servers-and-vpn-connectivity"></a>Les serveurs DNS et la connectivité VPN

Dans la page serveurs DNS et la connectivité VPN, entrez les informations suivantes, puis cliquez sur la flèche suivant dans le coin inférieur droit.

  ![Les serveurs DNS et la connectivité VPN](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

- **Serveurs DNS** - Entrez le nom du serveur DNS et l’adresse IP, ou sélectionnez un serveur DNS inscrit précédemment dans la liste déroulante. Ce paramètre ne crée pas un serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel. Si vous souhaitez que la résolution de nom entre vos réseaux virtuels, vous devez configurer votre propre serveur DNS plutôt que d’utiliser la résolution de nom qui est fournie par Azure.
- Ne sélectionnez aucune les cases à cocher pour une connectivité P2S ou S2S. Cliquez sur la flèche dans le coin inférieur droit pour passer à l’écran suivant.

### <a name="virtual-network-address-spaces"></a>Espaces d’adressage de réseau virtuel

Dans la page des espaces d’adressage de réseau virtuel, spécifiez la plage d’adresses que vous souhaitez utiliser pour votre réseau virtuel. Ce sont les adresses IP dynamiques (DIP) qui seront affectés aux ordinateurs virtuels et les autres instances de rôle que vous déployez à ce réseau virtuel. 

Si vous créez un VNet qui aura également une connexion à votre réseau local, il est particulièrement important sélectionner une plage qui ne se chevauche pas avec toutes les plages qui sont utilisés pour votre réseau local. Dans ce cas, vous devez coordonner avec votre administrateur réseau. Votre administrateur de réseau devra peut-être imaginer une plage d’adresses IP à partir de votre espace d’adresses de réseau local à utiliser pour votre VNet.

  ![Page d’espaces d’adressage de réseau virtuel](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **Espace d’adressage** - y compris IP de début et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne se chevauchent pas avec tous les espaces d’adressage que vous avez sur votre réseau local. Pour cet exemple, nous utilisons 10.1.0.0/16 pour VNet1.
  - **Ajouter sous-réseau** - en y compris IP de début et le nombre d’adresses. Des sous-réseaux supplémentaires ne sont pas obligatoires, mais vous souhaiterez peut-être créer un sous-réseau distinct pour les machines virtuelles qui auront creux statiques. Ou vous souhaiterez peut-être que vos ordinateurs virtuels dans un sous-réseau distinct à partir de vos autres instances de rôle.
 
**Cliquez sur la coche** dans le coin inférieur droit de la page et votre réseau virtuel commence à créer. Lorsqu’elle est terminée, vous verrez « Créé » répertoriés sous état de la page réseaux.

## <a name="step-3---create-vnet2"></a>Étape 3 – Création des VNet2

Ensuite, répétez les étapes précédentes pour créer un autre VNet. Dans les étapes suivantes, vous allez connecter les deux VNets. Vous pouvez consulter les [exemples de paramètres](#step1) à l’étape 1. Si votre VNet existe déjà, vous n’avez pas besoin d’effectuer cette étape. Toutefois, vous devez vérifier que les plages d’adresses IP ne se chevauchent pas avec tous les autres réseaux locaux ou de VNets que vous souhaitez vous connecter à.

## <a name="step-4---add-the-local-network-sites"></a>Étape 4 – ajouter les sites de réseau local

Lorsque vous créez une configuration VNet à VNet, vous devez configurer les sites de réseau local, qui sont affichés dans la page **Réseaux locaux** du portail. Azure utilise les paramètres spécifiés dans chaque site de réseau local pour déterminer comment router le trafic entre les VNets. Vous déterminez le nom que vous voulez utiliser pour faire référence à chaque site de réseau local. Il est préférable d’utiliser un nom descriptif, lorsque vous sélectionnez la valeur dans une liste déroulante dans les étapes ultérieures.

Par exemple, VNet1 se connecte à un site de réseau local que vous créez nommé « VNet2Local ». Les paramètres pour VNet2Local contiennent les préfixes d’adresse pour VNet2 et une adresse IP publique de la passerelle VNet2. VNet2 se connecte à vous créez un site de réseau local nommé « VNet1Local » qui contient les préfixes d’adresse pour l’adresse IP publique de la passerelle VNet1 et de VNet1.

### <a name="localnet"></a>Ajouter le site réseau local VNet1Local

1. Dans l’angle inférieur gauche de l’écran, cliquez sur **Nouveau** > **Services réseau** > **Réseau virtuel** > **Ajouter un réseau Local**.

2. Dans la page **spécifier les détails de votre réseau local** , pour **nom**, entrez un nom que vous souhaitez utiliser pour représenter le réseau auquel vous souhaitez vous connecter. Dans cet exemple, vous pouvez utiliser « VNet1Local » pour désigner les plages d’adresses IP et la passerelle pour VNet1.

3. Pour **les adresses IP des périphériques VPN (facultatif)**, spécifiez une adresse IP publique valide. En général, vous utiliseriez la véritable adresse IP externe pour un périphérique VPN. Pour les configurations VNet à VNet, vous utilisez l’adresse IP publique qui est affectée à la passerelle pour votre VNet. Toutefois, étant donné que vous n’avez pas encore créé la passerelle, vous pouvez spécifier une adresse IP publique valide comme espace réservé. Ne pas laisser ce champ vide, il n’est pas facultatif pour cette configuration. Dans une étape ultérieure, vous revenez à ces paramètres et les configurez avec les adresses IP de passerelle correspondants une fois, Azure génère. Cliquez sur la flèche pour passer à l’écran suivant.

4. Dans la **page Spécifiez l’adresse**, saisissez l’IP adresse plage et adresse count pour VNet1. Cet attribut doit correspondre exactement à la plage qui est configurée pour VNet1. Azure utilise les plages d’adresses IP que vous spécifiez pour acheminer le trafic destiné à VNet1. Cliquez sur la case à cocher pour créer le réseau local.

### <a name="add-the-local-network-site-vnet2local"></a>Ajouter le site réseau local VNet2Local

Suivez les instructions ci-dessus pour créer le site de réseau local « VNet2Local ». Vous pouvez faire référence aux valeurs dans les [paramètres de l’exemple](#step1) à l’étape 1, si nécessaire.

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>Configurer chaque VNet pour pointer vers un réseau local

Chaque VNet doit pointer vers le réseau local respectif à acheminer le trafic vers. 

#### <a name="for-vnet1"></a>Pour VNet1

1. Accédez à la page de **configuration** de réseau virtuel **VNet1**. 
2. Sous connexion de site à site, sélectionnez « Connexion au réseau local » et sélectionnez **VNet2Local** comme le réseau local dans la liste déroulante. 
3. Enregistrer vos paramètres.

#### <a name="for-vnet2"></a>Pour VNet2

1. Accédez à la page de **configuration** de réseau virtuel **VNet2**. 
2. Sous connexion de site à site, sélectionnez « Connexion au réseau local », puis sélectionnez **VNet1Local** dans la liste déroulante comme le réseau local. 
3. Enregistrer vos paramètres.

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>Étape 5 - Configuration d’une passerelle pour chaque VNet

Configurer une passerelle de routage dynamique pour chaque réseau virtuel. Cette configuration ne gère pas les passerelles de routage statique. Si vous utilisez VNets qui ont été configurés précédemment et qui possèdent déjà des passerelles de routage dynamique, vous n’avez pas besoin d’effectuer cette étape. Si vos passerelles sont le routage statique, vous devez les supprimer et les recréer en tant que passerelles de routage dynamique. Si vous supprimez une passerelle, publié l’Obtient l’adresse IP publique affectée, et vous devez revenir en arrière et de reconfigurer vos réseaux locaux et les équipements VPN avec la nouvelle adresse IP publique pour la nouvelle passerelle.

1. Dans la page **réseaux** , vérifiez que la colonne d’état pour le réseau virtuel est **créé**.

2. Dans la colonne **nom** , cliquez sur le nom du réseau virtuel. Pour cet exemple, nous utilisons des « VNet1 ».

3. Sur la page de **tableau de bord** , notez que cette VNet n’a pas de passerelle configurée encore. Vous verrez cet état changent à mesure que vous parcourez les étapes de configuration de votre passerelle.

4. En bas de la page, cliquez sur **Créer une passerelle** et le **Routage dynamique**. Lorsque le système vous invite à confirmer que vous souhaitez que la passerelle créée, cliquez sur Oui.

    ![Type de passerelle](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  

5. Lors de la création de votre passerelle, notez le graphisme de passerelle dans la page devient jaune et indique « Création de la passerelle ». Il faut généralement environ 30 minutes pour la passerelle à créer.

6. Répétez les mêmes étapes pour VNet2. Vous ne devez pas la première passerelle VNet à effectuer avant de commencer à créer la passerelle pour vos autre VNet.

7. Lorsque l’état de la passerelle devient « Connexion », l’adresse IP publique pour chaque passerelle est visible dans le tableau de bord. Notez l’adresse IP qui correspond à chaque VNet, en prenant soin de ne pas pour les mélanger. Ce sont les adresses IP qui sont utilisées lorsque vous modifiez les adresses IP de votre espace réservé pour le périphérique VPN pour chaque réseau local.

## <a name="step-6---edit-the-local-network"></a>Étape 6 - modifier le réseau local

1. Dans la page **Réseaux locaux** , cliquez sur le nom du nom de réseau Local que vous souhaitez modifier, puis cliquez sur **Modifier** au bas de la page. Pour l' **adresse IP du périphérique VPN**, entrez l’adresse IP de la passerelle qui correspond à la VNet. Par exemple, pour VNet1Local, placez l’adresse IP de passerelle affectée à VNet1. Cliquez sur la flèche vers le bas de la page.

2. Sur la page **spécifier l’espace d’adressage** , cliquez sur la coche dans le coin inférieur droit sans apporter de modifications.

## <a name="step-7---create-the-vpn-connection"></a>Étape 7 - créer la connexion VPN

Lorsque toutes les étapes précédentes ont été effectuées, définir les clés prépartagées IKE d’IPsec et de créer la connexion. Cet ensemble d’étapes utilise PowerShell et ne peut pas être configuré dans le portail. Pour plus d’informations sur l’installation des applets de commande PowerShell d’Azure, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) . Veillez à télécharger la dernière version des applets de commande Services Management (SM). 

1. Ouvrir Windows PowerShell et connectez-vous.

        Add-AzureAccount

2. Sélectionnez l’abonnement résidant dans votre VNets.

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"

3. Créez les connexions. Dans les exemples, notez que la clé partagée est exactement le même. La clé partagée doit toujours correspondre.


    VNet1 VNet2 connexion

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    VNet2 VNet1 connexion

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Attendez que les connexions à initialiser. Une fois la passerelle a été initialisé, la passerelle ressemble à l’illustration suivante.

    ![État de la passerelle - connecté](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  

    [AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter des ordinateurs virtuels à vos réseaux virtuels. Consultez la [documentation de Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/) pour plus d’informations.



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 
