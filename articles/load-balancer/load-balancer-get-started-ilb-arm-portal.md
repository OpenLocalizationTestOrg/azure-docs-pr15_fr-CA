<properties
   pageTitle="Commencer à créer un équilibreur de charge interne dans le Gestionnaire de ressources à l’aide du portail Azure | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge interne dans le Gestionnaire de ressources à l’aide du portail Azure"
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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Créer un équilibreur de charge interne dans le portail Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Démarrer la création d’un équilibreur de charge interne à l’aide du portail Azure

Utilisez les étapes suivantes pour créer un équilibreur de charge interne à partir du portail Azure.

1. Ouvrez un navigateur, accédez au [portail Azure](http://portal.azure.com)et connectez-vous à votre compte Azure.
2. Sur le côté supérieur gauche de l’écran, cliquez sur **Nouveau** > **réseau** > **équilibreur de charge**.
3. De la lame **créer l’équilibreur de charge** , entrez un **nom** pour votre équilibreur de charge.
4. Sous **modèle**, cliquez sur **interne**.
5. Cliquez sur **réseau virtuel**et sélectionnez le réseau virtuel dans lequel vous souhaitez créer l’équilibreur de charge.

    >[AZURE.NOTE] Si vous ne voyez pas le réseau virtuel que vous souhaitez utiliser, vérifiez l' **emplacement** que vous utilisez pour l’équilibrage de charge et modifiez en conséquence.

6. Cliquez sur **sous-réseau**et sélectionnez le sous-réseau où vous souhaitez créer l’équilibreur de charge.
7. **Attribution d’adresses IP**, cliquez sur **dynamique** ou **statique**, en fonction de si vous souhaitez que l’adresse IP de l’équilibreur de charge être résolus (statique).

    >[AZURE.NOTE] Si vous choisissez d’utiliser une adresse IP statique, vous devez fournir une adresse pour l’équilibreur de charge.

8. Sous le **groupe de ressources** soit spécifier le nom d’un nouveau groupe de ressources pour l’équilibrage de la charge, ou cliquez sur **Sélectionnez existante** et sélectionnez un groupe de ressources existant.
9. Cliquez sur **créer**.

## <a name="configure-load-balancing-rules"></a>Configurer les règles d’équilibrage de charge

Après la création d’équilibrage de la charge, accédez à la ressource d’équilibrage de la charge pour le configurer.
Vous devez d’abord configurer un pool d’adresses de back-end et une sonde avant de configurer une règle d’équilibrage de la charge.

### <a name="step-1-configure-a-back-end-pool"></a>Étape 1 : Configurer un pool back-end

1. Dans le portail Azure, cliquez sur **Parcourir** > **équilibreurs de charge**, puis cliquez sur l’équilibrage de charge que vous avez créé ci-dessus.
2. De la lame de **paramètres** , cliquez sur **pools de back-end**.
3. Dans la lame **back-end les pools d’adresses** , cliquez sur **Ajouter**.
4. Dans la lame **Ajouter back-end pool** , entrez un **nom** pour le pool de serveur principal et puis cliquez sur **OK**.

### <a name="step-2-configure-a-probe"></a>Étape 2 : Configurer une sonde

1. Dans le portail Azure, cliquez sur **Parcourir** > **équilibreurs de charge**, puis cliquez sur l’équilibrage de charge que vous avez créé ci-dessus.
2. De la lame de **paramètres** , cliquez sur **des sondes**.
3. De la lame de **sondes** , cliquez sur **Ajouter**.
4. Dans la lame de **sonde d’ajouter** , entrez le **nom** de la sonde.
5. Sous **protocoles**, sélectionnez **HTTP** (pour les sites web) ou **TCP** (pour les autres applications TCP en fonction).
6. Sous **Port**, spécifiez le port à utiliser lors de l’accès de la sonde.
7. Sous **chemin d’accès** (pour les sondes HTTP uniquement), indiquez le chemin à utiliser comme une sonde.
8. Sous **intervalle de** spécifier la fréquence de l’application de la sonde.
9. Sous **seuil défectueux**, spécifiez le nombre de tentatives doivent échouer avant que la machine virtuelle de back-end est marquée comme défectueux.
10. Cliquez sur **OK** pour créer la sonde.

### <a name="step-3-configure-load-balancing-rules"></a>Étape 3 : Configurer les règles d’équilibrage de charge

1. Dans le portail Azure, cliquez sur **Parcourir** > **équilibreurs de charge**, puis cliquez sur l’équilibrage de charge que vous avez créé ci-dessus.
2. De la lame de **paramètres** , cliquez sur **règles d’équilibrage de charge**.
3. Dans la lame **règles d’équilibrage de charge** , cliquez sur **Ajouter**.
4. De la lame **Ajouter charger l’équilibrage de la règle** , entrez un **nom** pour la règle.
5. Sous **protocoles**, sélectionnez **HTTP** (pour les sites web) ou **TCP** (pour les autres applications TCP en fonction).
6. Sous **Port**, spécifiez que les clients port se connectent à de l’équilibreur de charge.
7. Sous **port principal**, spécifiez le port à utiliser dans le pool de serveur principal (en règle générale, le port d’équilibrage de la charge et le port back-end sont identiques).
8. Sous **pool du serveur principal**, sélectionnez le pool back-end que vous avez créé ci-dessus.
9. Sous la **persistance de la Session**, sélectionnez la méthode de sessions pour rendre persistantes.
10. Dans le **délai d’inactivité (minutes)**, indiquez le délai d’inactivité.
11. **Flottante IP (retour de serveur direct)**, cliquez sur **désactivé** ou **activé**.
12. Cliquez sur **OK**.

## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)