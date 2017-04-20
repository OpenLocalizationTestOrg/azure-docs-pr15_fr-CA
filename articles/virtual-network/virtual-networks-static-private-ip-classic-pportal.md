<properties 
   pageTitle="Comment définir un IP privée statique en mode classique, l’utilisation du portail Azure | Microsoft Azure"
   description="Présentation statique IPs privé et comment les gérer en mode classique, l’utilisation du portail Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-the-azure-portal"></a>Comment définir une adresse IP privée (classic) dans le portail Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [gérer une adresse IP statique privée, dans le modèle de déploiement du Gestionnaire de ressources](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les étapes de l’exemple ci-dessous s’attendent à un environnement simple déjà créé. Si vous souhaitez exécuter les étapes tels qu’ils sont affichés dans ce document, d’abord créer l’environnement de test décrit dans [Création d’un vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Comment spécifier une adresse IP privée lors de la création d’une machine virtuelle
Pour créer un ordinateur virtuel nommé *DNS01* dans le sous-réseau *frontal* d’un VNet nommé *TestVNet* avec un IP statique privée de *192.168.1.101*, suivez les étapes ci-dessous :

1. À partir d’un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous à votre compte Azure.
2. Cliquez sur **Nouveau** > **Calculer** > **Windows Server 2012 R2 Datacenter**, notez que la liste **Sélectionnez un modèle de déploiement** présente déjà **classique**, puis cliquez sur **créer**.

    ![Créer un ordinateur virtuel dans Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. Dans la lame de **Créer la machine virtuelle** , entrez le nom de la machine virtuelle doit être créé (*DNS01* dans notre scénario), le compte d’administrateur local et le mot de passe.

    ![Créer un ordinateur virtuel dans Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Cliquez sur **Configuration de l’option** > **réseau** > **Réseau virtuel**, puis cliquez sur **TestVNet**. Si **TestVNet** n’est pas disponible, assurez-vous que vous utilisez l’emplacement *Central des États-Unis* et que vous avez créé l’environnement de test décrit au début de cet article.

    ![Créer un ordinateur virtuel dans Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. Dans la carte **réseau** , assurez-vous que le sous-réseau actuellement sélectionné est la *partie frontale*, puis cliquez sur **adresses IP**sous **attribution de l’adresse IP** et cliquez sur **statique**, puis entrez *192.168.1.101* pour **l’Adresse IP** , comme indiqué ci-dessous.

    ![Créer un ordinateur virtuel dans Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure04.png)   

6. Cliquez sur **OK** dans la blade **d’adresses IP** , puis cliquez sur **OK** dans la carte **réseau** et cliquez sur **OK** dans la lame de **configuration facultative** .
7. De la lame de **Créer un ordinateur virtuel** , cliquez sur **créer**. Notez la mosaïque ci-dessous affichée dans votre tableau de bord.

    ![Créer un ordinateur virtuel dans Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Comment faire pour extraire des informations statiques d’adresse IP privées pour un ordinateur virtuel

Pour afficher les informations d’adresse IP privées statiques pour la machine virtuelle créée avec les étapes ci-dessus, exécutez les étapes décrites ci-dessous.

1. À partir du portail Azure Azure, cliquez sur **Parcourir tous les** > **les machines virtuelles (classique)** > **DNS01** > **tous les paramètres** > **les adresses IP** et la notification de l’attribution d’adresses IP et l’adresse IP comme indiqué ci-dessous.

    ![Créer un ordinateur virtuel dans Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Comment faire pour supprimer une adresse IP privée statique à partir d’un ordinateur virtuel
Pour supprimer l’adresse IP privée de l’ordinateur virtuel créé ci-dessus, suivez les étapes ci-dessous.
    
1. À partir de la blade **d’adresses IP** ci-dessus, cliquez sur **dynamique** à droite de l' **Attribution d’adresses IP**, puis cliquez sur **Enregistrer**, puis cliquez sur **Oui**.

    ![Créer un ordinateur virtuel dans Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Comment faire pour ajouter une adresse IP privée pour un ordinateur virtuel existant
Pour ajouter une adresse IP privée statique pour l’ordinateur virtuel créé à l’aide de la procédure ci-dessus, suivez les étapes ci-dessous :

1. À partir de la blade **d’adresses IP** ci-dessus, cliquez sur **statique** à droite de **l’Attribution d’adresses IP**.
2. Tapez *192.168.1.101* pour l' **adresse IP**, puis cliquez sur **Enregistrer**, puis cliquez sur **Oui**.

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les adresses [IP publique réservées](virtual-networks-reserved-public-ip.md) .
- Obtenir des informations sur les adresses [IP publiques (ILPIP) au niveau de l’instance](virtual-networks-instance-level-public-ip.md) .
- Consultez les [API de reste IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).