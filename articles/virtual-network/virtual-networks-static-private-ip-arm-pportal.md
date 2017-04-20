<properties 
   pageTitle="Comment définir un IP privée statique en mode ARM via le portail Azure | Microsoft Azure"
   description="Présentation des adresses IP privée (DIP) et comment les gérer en mode ARM via le portail Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-in-the-azure-portal"></a>Comment définir une adresse IP privée dans le portail Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement classique](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les étapes de l’exemple ci-dessous s’attendent à un environnement simple déjà créé. Si vous souhaitez exécuter les étapes tels qu’ils sont affichés dans ce document, d’abord créer l’environnement de test décrit dans [Création d’un vnet](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Comment faire pour créer un ordinateur virtuel pour le test statique des adresses IP privées

Vous ne pouvez pas définir une adresse IP privée lors de la création d’une machine virtuelle dans le mode de déploiement du Gestionnaire de ressources à l’aide du portail Azure. Vous devez d’abord créer la machine virtuelle, la valeur quantité son IP privée statique.

Pour créer un ordinateur virtuel nommé *DNS01* dans le sous-réseau *frontal* d’un VNet, nommé *TestVNet*, suivez les étapes ci-dessous.

1. À partir d’un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous à votre compte Azure.
2. Cliquez sur **Nouveau** > **Calculer** > **Windows Server 2012 R2 Datacenter**, notez que la liste **Sélectionnez un modèle de déploiement** indique déjà **Le Gestionnaire de ressources**, puis cliquez sur **créer**, comme illustré dans la figure ci-dessous.

    ![Créer un ordinateur virtuel dans Azure portal](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. Dans la lame de **Notions de base** , entrez le nom de la machine virtuelle doit être créé (*DNS01* dans notre scénario), le compte d’administrateur local et le mot de passe, comme illustré dans la figure ci-dessous.

    ![Lame de notions de base](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Assurez-vous que l' **emplacement** sélectionné est *États-Unis centre*, puis cliquez sur **existant sélectionnez** sous le **groupe de ressources**, puis recliquez sur **groupe de ressources** , puis cliquez sur *TestRG*et puis cliquez sur **OK**.

    ![Lame de notions de base](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. De la lame de **Choisir une taille** , sélectionnez **A1 Standard**, puis cliquez sur **Sélectionner**.

    ![Choisissez une lame de taille](./media/virtual-networks-static-ip-arm-pportal/figure04.png) 

6. Dans la lame de **paramètres** , assurez-vous que les propriétés suivantes sont définies sont définies avec les valeurs ci-dessous, puis cliquez sur **OK**.

    -**Compte de stockage**: *vnetstorage*
    - **Réseau**: *TestVNet*
    - **Sous-réseau**: *site Web frontal*

    ![Choisissez une lame de taille](./media/virtual-networks-static-ip-arm-pportal/figure05.png)  

7. De la lame de **Résumé** , cliquez sur **OK**. Notez la mosaïque ci-dessous affichée dans votre tableau de bord.

    ![Créer un ordinateur virtuel dans Azure portal](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Comment faire pour extraire des informations statiques d’adresse IP privées pour un ordinateur virtuel

Pour afficher les informations d’adresse IP privées statiques pour la machine virtuelle créée avec les étapes ci-dessus, exécutez les étapes décrites ci-dessous.

1. À partir du portail Azure Azure, cliquez sur **Parcourir tous les** > **machines virtuelles** > **DNS01** > **tous les paramètres** > puis cliquez sur la seule interface réseau et les**interfaces réseau** répertoriés.

    ![Mosaïque de la machine virtuelle de déploiement](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. De la lame **d’interface réseau** , cliquez sur **tous les paramètres** > **les adresses IP** et notez les valeurs de **l’affectation** et **l’adresse IP** .

    ![Mosaïque de la machine virtuelle de déploiement](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Comment faire pour ajouter une adresse IP privée pour un ordinateur virtuel existant
Pour ajouter une adresse IP privée statique pour l’ordinateur virtuel créé à l’aide de la procédure ci-dessus, suivez les étapes ci-dessous :

1. À partir de la blade **d’adresses IP** ci-dessus, cliquez sur **statique** sous **affectation**.
2. Tapez *192.168.1.101* pour **l’adresse IP**, puis cliquez sur **Enregistrer**.

    ![Créer un ordinateur virtuel dans Azure portal](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] Si, après avoir cliqué sur **Enregistrer** vous notez que l’affectation a toujours la valeur **dynamique**, cela signifie que l’adresse IP que vous avez tapé est déjà en cours d’utilisation. Essayez une autre adresse IP.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Comment faire pour supprimer une adresse IP privée statique à partir d’un ordinateur virtuel
Pour supprimer l’adresse IP privée de l’ordinateur virtuel créé ci-dessus, suivez la procédure ci-dessous.
    
1. De la lame **d’adresses IP** ci-dessus, cliquez sur **dynamique** sous **affectation**, puis cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les adresses [IP publique réservées](virtual-networks-reserved-public-ip.md) .
- Obtenir des informations sur les adresses [IP publiques (ILPIP) au niveau de l’instance](virtual-networks-instance-level-public-ip.md) .
- Consultez les [API de reste IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).