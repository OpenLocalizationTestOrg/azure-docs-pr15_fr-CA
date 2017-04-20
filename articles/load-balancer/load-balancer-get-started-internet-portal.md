<properties
   pageTitle="Créer un équilibreur de charge faisant face à Internet dans le Gestionnaire de ressources en utilisant le portail Azure | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge faisant face à Internet dans le Gestionnaire de ressources à l’aide du portail Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="anavinahar"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="annahar" />

# <a name="creating-an-internet-facing-load-balancer-using-the-azure-portal"></a>Création d’un équilibreur de charge faisant face à Internet en utilisant le portail Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [apprendre à créer un équilibreur de charge faisant face à Internet en utilisant le déploiement classique](load-balancer-get-started-internet-classic-portal.md)

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Il s’agit de la séquence de tâches qu’il a à faire pour créer un équilibreur de charge et expliquez en détail ce qui est effectué pour atteindre l’objectif.

## <a name="what-is-required-to-create-an-internet-facing-load-balancer"></a>Ce qui est nécessaire pour créer un équilibreur de charge pour Internet ?

Vous devez créer et configurer les objets suivants pour déployer un équilibreur de charge.

- La configuration IP front-end - contient les adresses IP publiques pour le trafic réseau entrant.

- Pool d’adresses principal - contient des interfaces réseau (cartes réseau) pour les ordinateurs virtuels recevoir le trafic réseau à partir de l’équilibreur de charge.

- Règles d’équilibrage de la charge - contient les règles de mappage d’un port public de l’équilibreur de charge au port dans le pool d’adresses de back-end.

- NAT règles de trafic entrant - contient les règles de mappage d’un port public de l’équilibreur de charge à un port pour une machine virtuelle spécifique dans le pool d’adresses de back-end.

- Sondes - contient les sondes de santé permettant de vérifier la disponibilité des machines virtuelles des instances dans le pool d’adresses de back-end.

Vous pouvez obtenir plus d’informations sur la charge des composants équilibreur avec le Gestionnaire de ressources Azure à [Azure Resource Manager prend en charge de l’équilibrage de charge](load-balancer-arm.md).


## <a name="set-up-a-load-balancer-in-azure-portal"></a>Configurer dans Azure portal, un équilibreur de charge

> [AZURE.IMPORTANT] Cet exemple suppose que vous disposez d’un réseau virtuel appelé **myVNet**. Pour [créer des réseaux virtuels](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) pour ce faire, reportez-vous à la section. Il suppose également est un sous-réseau dans le **myVNet** appelé **Être LB-sous-réseau** et deux VM appelée **web1** et **web2** respectivement dans le même jeu de disponibilité appelée **myAvailSet** dans **myVNet**. Consultez [ce lien](../virtual-machines/virtual-machines-windows-hero-tutorial.md) pour créer des ordinateurs virtuels.


1. À partir d’un navigateur, accédez au portail Azure : [http://portal.azure.com](http://portal.azure.com) et connectez-vous avec votre compte Azure.

2. Sur le côté supérieur gauche de l’écran, sélectionnez **Nouveau** > **réseau** > **équilibreur de charge.**

3. Dans la lame **créer l’équilibreur de charge** , tapez un nom pour votre équilibreur de charge. Ici, il est appelé **myLoadBalancer**.

4. Sous **Type**, sélectionnez **Public**.

5. Sous **adresse IP publique**, créer une nouvelle adresse IP publique appelée **myPublicIP**.

6. Sous le groupe de ressources, sélectionnez **myRG**. Puis sélectionnez un **emplacement**approprié, puis cliquez sur **OK**. L’équilibreur de charge, puis commence à déployer et prendra quelques minutes pour réussir le déploiement.

![Mise à jour du groupe de ressources de l’équilibreur de charge](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-a-back-end-address-pool"></a>Créer un pool d’adresses de back-end

1. Une fois votre équilibreur de charge a été déployée avec succès, sélectionnez-le depuis vos ressources. Sous paramètres, sélectionnez Pools de back-end. Tapez un nom pour votre pool de back-end. Cliquez ensuite sur le bouton **Ajouter** dans la partie supérieure de la lame qui s’affiche.

2. Cliquez sur **Ajouter un ordinateur virtuel** dans la lame de **pool back-end d’ajouter** .  Sélectionnez **Choisir un jeu de disponibilité** sous **l’ensemble de la disponibilité** et **myAvailSet**. Ensuite, sélectionnez **Choisir les ordinateurs virtuels** dans la section de Machines virtuelles de la lame et cliquez sur **web1** et **web2**, deux ordinateurs virtuels créés pour l’équilibrage de charge. Assurez-vous que les deux ont des coches bleues à gauche comme indiqué dans l’image ci-dessous. Ensuite, cliquez sur **Sélectionner** dans cette blade suivie de OK dans la lame **Choisissez Virtual machines** , puis sur **OK** dans la lame **Ajouter un pool back-end** .

    ![Ajout au pool d’adresses back-end- ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Vérifiez que les notifications de zone de liste déroulante a une mise à jour concernant l’enregistrement du pool de back-end d’équilibrage de la charge en plus de la mise à jour de l’interface réseau pour les ordinateurs virtuels **web1** et **web2**.


## <a name="create-a-probe-lb-rule-and-nat-rules"></a>Créer des règles NAT, une sonde et règle de livres

1. Créer une sonde de santé.

    Sous paramètres de votre équilibreur de charge, sélectionnez les sondes. Cliquez ensuite sur **Ajouter** située en haut de la lame.

    Il existe deux façons de configurer une sonde : HTTP ou TCP. Cet exemple indique à HTTP, mais TCP peut être configuré de la même manière.
    Mettre à jour les informations nécessaires. Comme mentionné, **myLoadBalancer** se charge d’équilibrer le trafic sur le Port 80. Le chemin d’accès sélectionné est HealthProbe.aspx, est de 15 secondes et seuil défectueux est 2. Une fois que vous avez terminé, cliquez sur **OK** pour créer la sonde.

    Placez le pointeur sur le 'i' icône pour en savoir plus sur ces configurations individuelles et comment ils peuvent être modifiés pour répondre à vos besoins.

    ![Ajout d’une sonde](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

2. Créer une règle d’équilibrage de la charge.

    Cliquez sur règles dans la section Paramètres de l’équilibrage de la charge d’équilibrage de charge. Dans la nouvelle blade, cliquez sur **Ajouter**. Nom de votre règle. Ici, il est HTTP. Choisissez la ports frontaux et back-end. Ici, 80 est choisie pour les deux. Choisissez votre pool back-end et le précédemment créé **HealthProbe** comme la sonde **LB-back-end** . Autres configurations peuvent être définies en fonction de vos besoins. Cliquez ensuite sur OK pour enregistrer la règle d’équilibrage de la charge.

    ![Ajout d’une règle d’équilibrage de la charge](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

3. Créer des règles NAT entrantes

    Cliquez sur règles de trafic entrant de NAT sous la section Paramètres de votre équilibreur de charge. Dans la nouvelle blade, cliquez sur **Ajouter**. Nommez votre règle NAT entrante. Ici, il est appelé **inboundNATrule1**. La destination doit être l’adresse IP publique créée précédemment. Sélectionnez personnalisé sous Service et sélectionnez le protocole que vous souhaitez utiliser. Ici l’option TCP est sélectionnée. Entrez le port, 3441 et le port de cible, dans ce cas, 3389. Cliquez ensuite sur OK pour enregistrer cette règle.

    Une fois que la première règle est créée, répétez cette étape pour la deuxième règle NAT entrante appelée inboundNATrule2 à partir du port 3442 au port 3389 de la cible.

    ![Ajout d’une règle NAT entrante](./media/load-balancer-get-started-internet-portal/6-load-balancer-inbound-nat-rules.png)

## <a name="remove-a-load-balancer"></a>Supprimer un équilibreur de charge

Pour supprimer un équilibreur de charge, sélectionnez l’équilibreur de charge que vous souhaitez supprimer. De la lame de *l’Équilibreur de charge* , cliquez sur **Supprimer** située en haut de la lame. Sélectionnez **Oui** lorsque vous y êtes invité.

## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-cli.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)
