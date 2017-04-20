<properties 
   pageTitle="Configurez une passerelle VPN dans le portail classique Azure | Microsoft Azure"
   description="Cet article vous guide à travers la configuration de votre passerelle VPN de réseau virtuel et la modification d’une type de routage VPN de passerelle."
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
   ms.date="08/11/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vpn-gateway-for-the-classic-deployment-model"></a>Configurez une passerelle VPN pour le modèle de déploiement classique


Si vous souhaitez créer une connexion sécurisée de la coexistence entre Azure et votre emplacement sur site, vous devez configurer une connexion de passerelle VPN. Dans le modèle de déploiement classique, une passerelle peut être un des deux types VPN routage : statique ou dynamique. Le type que vous choisissez dépend de votre plan de conception de réseau et le périphérique VPN sur site que vous souhaitez utiliser. 

Par exemple, certaines options de connectivité, telle qu’une connexion point-à-site, nécessitent une passerelle de routage dynamique. Si vous souhaitez configurer votre passerelle pour prendre en charge les connexions point-à-site (P2S) et une connexion (S2S) de site à site, vous devez configurer une passerelle de routage dynamique, même si le site à site peut être configuré avec un type de routage VPN de passerelle. 

En outre, devez vous assurer que le périphérique que vous souhaitez utiliser pour votre connexion prend en charge le type de routage VPN que vous souhaitez créer. [Sur les périphériques VPN](vpn-gateway-about-vpn-devices.md), reportez-vous à la section.


**À propos de cet article** 

Cet article a été écrit pour le modèle de déploiement classique à l’aide du [portail classique](https://manage.windowsazure.com) (pas le portail Azure). 

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-overview"></a>Vue d’ensemble de la configuration

Les étapes suivantes vous guident dans la configuration de votre passerelle VPN dans Azure portal classique. Ces étapes s’appliquent aux passerelles pour les réseaux virtuels qui ont été créés à l’aide du modèle de déploiement classique. Actuellement, tous les paramètres de configuration pour les passerelles sont disponibles dans le portail Azure. Lorsqu’ils le sont, nous allons créer un nouveau jeu d’instructions qui s’appliquent au portail Azure.


1. [Créer une passerelle VPN pour votre VNet](#create-a-vpn-gateway)

1. [Collecter des informations pour la configuration de vos périphériques VPN](#gather-information-for-your-vpn-device-configuration)

1. [Configurer votre périphérique VPN](#configure-your-vpn-device)

1. [Vérifiez vos plages réseau local et l’adresse IP de passerelle VPN](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### <a name="before-you-begin"></a>Avant de commencer

Avant de configurer la passerelle, vous devez d’abord créer votre réseau virtuel. Pour créer un réseau virtuel pour la connectivité de coexistence, consultez [configuration d’un réseau virtuel avec une connexion VPN de site à site](vpn-gateway-site-to-site-create.md), ou de [configurer un réseau virtuel avec une connexion VPN site-à-point](vpn-gateway-point-to-site-create.md). Puis, utilisez les étapes suivantes pour configurer la passerelle VPN et de recueillir les informations nécessaires configurer votre périphérique VPN. 

Si vous disposez déjà d’une passerelle VPN et que vous souhaitez modifier le type de routage VPN, reportez-vous à la section [comment modifier le type de routage de la passerelle VPN](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Créer une passerelle VPN

1. Dans [Azure portal classique](https://manage.windowsazure.com), dans la page **réseaux** , vérifiez que la colonne d’état pour le réseau virtuel est **créé**.

1. Dans la colonne **nom** , cliquez sur le nom du réseau virtuel.

1. Sur la page de **tableau de bord** , notez que cette VNet n’a pas de passerelle configurée encore. Vous verrez cet état tout au long de la procédure de configuration de votre passerelle.

![N’a ne pas créé de passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


Ensuite, en bas de la page, cliquez sur **Créer une passerelle**. Vous pouvez sélectionner soit *Un routage statique* ou *Dynamique*. Le type de routage VPN que vous sélectionnez dépend de plusieurs facteurs. Par exemple, que votre périphérique VPN prend en charge et si vous avez besoin prendre en charge des connexions site-à-point. Vérifiez [Sur les périphériques VPN pour les connexions de réseau virtuel](vpn-gateway-about-vpn-devices.md) pour vérifier le type de routage VPN dont vous avez besoin. Une fois la passerelle a été créée, vous ne pouvez pas modifier entre types de routage VPN passerelle sans supprimer et recréer la passerelle. Lorsque le système vous invite à confirmer que vous souhaitez que la passerelle créée, cliquez sur **Oui**.

![Type de routage de passerelle VPN](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Lors de la création de votre passerelle, notez le graphisme de passerelle dans la page devient jaune et indique que la *Création d’une passerelle*. Il peut prendre jusqu'à 45 minutes de la passerelle à créer. Patientez jusqu'à ce que la passerelle soit terminée avant de pouvoir déplacer vers l’avant avec d’autres paramètres de configuration.

![Création de la passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Lorsque la passerelle passe à la *connexion*, vous pouvez rassembler les informations que vous avez besoin pour votre périphérique VPN.

![Connexion de la passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="gather-information-for-your-vpn-device-configuration"></a>Collecter des informations pour la configuration de vos périphériques VPN

Après création de la passerelle, collecter des informations pour la configuration de vos périphériques VPN. Ces informations se trouvent sur la page de **tableau de bord** pour votre réseau virtuel :

1. **Adresse IP de la passerelle :** L’adresse IP peut être trouvée sur la page de **tableau de bord** . Vous ne pourrez voir qu’une fois votre passerelle a terminé la création.

1. **Clé de Shared-** Cliquez sur **Gérer la clé** en bas de l’écran. Cliquez sur l’icône en regard de la clé à copier dans le Presse-papiers, puis coller et enregistrer la clé. Ce bouton fonctionne uniquement lorsqu’il y a un seul tunnel VPN de S2S. Si vous avez plusieurs tunnels VPN de S2S, utilisez l' *Obtenir virtuel réseau passerelle clé partagée* API ou l’applet de commande PowerShell.

![Gestion de clé](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## <a name="configure-your-vpn-device"></a>Configurer votre périphérique VPN

Une fois les étapes précédentes, vous ou votre administrateur réseau devrez configurer l’appareil VPN pour créer la connexion. Pour plus d’informations sur les périphériques VPN, reportez-vous à la section [Sur les périphériques VPN pour les connexions de réseau virtuel](vpn-gateway-about-vpn-devices.md) .

Une fois le périphérique VPN a été configuré, vous pouvez afficher vos informations de connexion mis à jour sur la page de tableau de bord pour votre VNet.

Vous pouvez également exécuter les commandes suivantes pour tester votre connexion :

|                      | Cisco ASA             | Cisco ISR/ASR         | Juniper SSG/ISG | Juniper SRX/J                            |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **Vérification des associations de sécurité de mode principal**  | afficher les clés de cryptage isakmp sa | afficher les clés de cryptage isakmp sa | obtenir le cookie d’ike  | afficher des association de sécurité ike   |
| **Vérification des associations de sécurité en mode rapide** | afficher les clés de cryptage ipsec sa  | afficher les clés de cryptage ipsec sa  | obtenir sa          | afficher la sécurité-association de sécurité ipsec |


## <a name="verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Vérifiez vos plages réseau local et l’adresse IP de passerelle VPN

### <a name="verify-your-vpn-gateway-ip-address"></a>Vérifiez que votre adresse IP de la passerelle VPN

Pour la passerelle pour se connecter, l’adresse IP de votre périphérique VPN doit être correctement configuré pour le réseau Local que vous avez spécifié pour votre configuration de coexistence. En général, ceci est configuré lors du processus de configuration de site à site. Toutefois, si vous avez utilisé précédemment ce réseau local avec un autre périphérique, ou l’adresse IP a été modifiée pour ce réseau local, modifier les paramètres pour spécifier l’adresse IP de la passerelle.

1. Pour vérifier votre adresse IP de la passerelle, cliquez sur **les réseaux** dans le volet gauche de portail et puis sélectionnez **Réseaux locaux** en haut de la page. Vous verrez l’adresse de la passerelle VPN pour chaque réseau local que vous avez créé. Pour modifier l’adresse IP, sélectionnez le VNet et cliquez sur **Modifier** au bas de la page.

1. Dans la page **spécifier les détails de votre réseau local** , modifiez l’adresse IP, puis cliquez sur la flèche suivant en bas de la page.

1. Dans la page **spécifier l’espace d’adressage** , cliquez sur la coche dans le coin inférieur droit pour enregistrer vos paramètres.

### <a name="verify-the-address-ranges-for-your-local-networks"></a>Vérifiez que les plages d’adresses pour vos réseaux locaux

Pour le trafic passe par la passerelle vers votre emplacement sur site, vous devez vérifier que chaque plage d’adresses IP est spécifié. Chaque plage doit être répertorié dans votre configuration Azure **Réseaux locaux** . Selon la configuration réseau de votre emplacement local, il peut s’avérer quelque peu volumineux. Le trafic qui est lié à une adresse IP qui est contenue dans les plages listées sera envoyé par l’intermédiaire de la passerelle VPN de réseau virtuel. Les plages de liste vous n’avez pas à être les plages privées, bien que vous voulez vérifier que votre configuration sur site peut recevoir du trafic entrant.

Pour ajouter ou modifier les plages pour un réseau Local, procédez comme suit.

1. Pour modifier les plages d’adresses IP pour un réseau local, cliquez sur **les réseaux** dans le volet gauche de portail et puis sélectionnez **Réseaux locaux** en haut de la page. Dans le portail, le moyen le plus simple d’afficher les plages que vous avez répertoriés est sur la page à **Modifier** . Pour consulter vos plages, sélectionnez le VNet et cliquez sur **Modifier** au bas de la page.

1. Dans la page **spécifier les détails de votre réseau local** , ne modifiez pas. Cliquez sur la flèche suivant en bas de la page.

1. Sur la page **spécifier l’espace d’adressage** , apportez vos modifications d’espace d’adresse réseau. Cliquez sur la coche pour enregistrer votre configuration.

## <a name="how-to-view-gateway-traffic"></a>Comment faire pour afficher le trafic de passerelle

Vous pouvez afficher votre passerelle et le trafic de passerelle à partir de votre page de réseau virtuel, **tableau de bord** .

Sur la page de **tableau de bord** , vous pouvez afficher les éléments suivants :

- La quantité de données qui s’écoule à travers votre passerelle, à la fois des données dans et des données.

- Les noms des serveurs DNS qui sont spécifiés pour votre réseau virtuel.

- La connexion entre votre passerelle et à votre périphérique VPN.

- La clé partagée qui est utilisée pour configurer votre connexion de passerelle à l’appareil VPN.


## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Comment faire pour modifier le type de routage de la passerelle VPN

Étant donné que certaines configurations de connectivité sont disponibles uniquement pour certains types de routage de passerelle, vous pouvez constater que vous devez modifier la type de routage VPN d’une passerelle VPN existante de la passerelle. Par exemple, vous souhaiterez ajouter une connectivité point-à-site à une connexion de site à site déjà existante qui dispose d’une passerelle statique. Connexions point-à-site nécessitent une passerelle dynamique. Cela signifie que pour configurer une connexion P2S, vous devez modifier votre type de routage VPN de passerelle à partir de static dynamique.

Si vous avez besoin de modifier une type de routage VPN de passerelle, vous allez supprimer la passerelle existante et puis créer une nouvelle passerelle avec le nouveau type de routage. Vous n’avez pas besoin de supprimer la totalité du réseau virtuel pour modifier le type de routage de passerelle.

Avant de modifier votre type de routage VPN de passerelle, veillez à vérifier que votre périphérique VPN prend en charge le type de routage que vous souhaitez utiliser. Pour télécharger des exemples de configuration de routage nouvelle et vérifier la configuration de périphérique VPN, reportez-vous à la section [Sur les périphériques VPN pour les connexions de réseau virtuel](vpn-gateway-about-vpn-devices.md).

>[AZURE.IMPORTANT] Lorsque vous supprimez une passerelle VPN de réseau virtuel, l’adresse IP virtuelle affectée à la passerelle est libéré. Lors de la recréation de la passerelle, un nouveau VIP lui est assigné.

1. **Supprimez la passerelle VPN existante.**

    Sur la page de **tableau de bord** pour votre réseau virtuel, naviguez jusqu’au bas de la page, puis cliquez sur **Supprimer la passerelle**. Attendez que la notification que la passerelle a été supprimée. Une fois que vous recevez la notification sur l’écran que votre passerelle a été supprimée, vous pouvez créer une nouvelle passerelle.

1. **Création d’une passerelle VPN.**

    Utilisez la procédure en haut de la page pour créer une nouvelle passerelle : [créer une passerelle VPN](#create-a-vpn-gateway).


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter des machines virtuelles au réseau virtuel. Apprendre [à créer un ordinateur virtuel personnalisé](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Si vous souhaitez configurer une connexion VPN de site-à-point, consultez [configurer une connexion VPN site-à-point](vpn-gateway-point-to-site-create.md).

 
