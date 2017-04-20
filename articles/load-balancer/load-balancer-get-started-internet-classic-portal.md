
<properties
   pageTitle="Commencer à créer un Internet face à équilibrage de la charge dans le modèle de déploiement classique de l’utilisation du portail classique Azure | Microsoft Azure"
   description="Apprenez à créer un interréseau en vis-à-vis d’équilibrage de la charge dans le modèle de déploiement classique de l’utilisation du portail classique Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Commencer à créer un Internet face à l’équilibrage de la charge (classic) dans Azure portal classique

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [apprendre à créer un équilibreur de charge à l’aide du Gestionnaire de ressources Azure d’ouvert sur Internet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configurer un équilibreur de charge pour les machines virtuelles via Internet

Pour charger équilibrer le trafic de réseau à partir d’Internet sur les machines virtuelles d’un service en nuage, vous devez créer un ensemble équilibré en charge. Cette procédure suppose que vous avez déjà créé des ordinateurs virtuels et qu’elles sont toutes dans le même service de cloud.

**Pour configurer un ensemble équilibré en charge pour les machines virtuelles**

1. Dans le portail Azure classique, cliquez sur des **Machines virtuelles**, puis cliquez sur le nom d’un ordinateur virtuel dans l’ensemble de l’équilibrage de charge.

2. Cliquez sur les **points de terminaison**, puis cliquez sur **Ajouter**.

3. Dans la page **Ajouter un point de terminaison à une machine virtuelle** , cliquez sur la flèche vers la droite.

4. Dans la page **spécifier les détails du point de terminaison** :

    * Dans la zone **nom**, tapez un nom pour le point de terminaison ou sélectionnez le nom de la liste des points de terminaison prédéfinis pour des protocoles communs.
    * Dans **protocole**, sélectionnez le protocole requis par le type du point de terminaison TCP ou UDP, selon vos besoins.
    * Dans **les ports publique et privée**, tapez les numéros de port que vous souhaitez que l’ordinateur virtuel à utiliser, le cas échéant. Vous pouvez utiliser le port privé et les règles de pare-feu sur l’ordinateur virtuel pour rediriger le trafic d’une manière qui est approprié pour votre application. Le port privé peut être le même que le port public. Par exemple, pour un point de terminaison pour le trafic web (HTTP), vous pouvez affecter le port 80 à la voie publique et privée.

5. Sélectionnez **créer un ensemble équilibré en charge**, puis cliquez sur la flèche vers la droite.

6. Sur la page **configurer l’ensemble de l’équilibrage de charge** , tapez un nom pour l’ensemble de l’équilibrage de charge et ensuite affecter les valeurs de comportement de sonde de l’équilibreur de charge Azure. L’équilibreur de charge utilise des sondes pour déterminer si les ordinateurs virtuels dans l’ensemble de l’équilibrage de charge sont disponibles pour recevoir le trafic entrant.

7. Cliquez sur la case à cocher pour créer le point de terminaison avec équilibrage de charge. Vous verrez **Oui** dans la colonne **nom de l’ensemble de l’équilibrage de charge** de la page **points de terminaison** pour l’ordinateur virtuel.

8. Dans le portail, cliquez sur les **Machines virtuelles**et cliquez sur le nom d’un ordinateur virtuel supplémentaire dans l’ensemble de l’équilibrage de charge, cliquez sur les **points de terminaison**, puis cliquez sur **Ajouter**.

9. Sur la page **Ajouter un point de terminaison à une machine virtuelle** , cliquez sur **Ajouter le point de terminaison à un jeu existant d’équilibrage de charge**, sélectionnez le nom de l’ensemble de l’équilibrage de charge et puis cliquez sur la flèche vers la droite.

10. Dans la page **spécifier les détails du point de terminaison** , tapez un nom pour le point de terminaison, puis cliquez sur la case à cocher.

Pour les ordinateurs virtuels supplémentaires dans l’ensemble de l’équilibrage de charge, répétez les étapes 8 à 10.



## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)

