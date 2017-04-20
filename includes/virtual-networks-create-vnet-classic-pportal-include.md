## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Comment créer un VNet classique dans le portail Azure

Pour créer un VNet classique en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

1. À partir d’un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous à votre compte Azure.
2. Cliquez sur **Nouveau** > **réseau** > **réseau virtuel**, notez que la liste **Sélectionnez un modèle de déploiement** indique déjà **classique**, puis cliquez sur **créer**, comme illustré dans la figure ci-dessous.

    ![Créer VNet dans Azure portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. Sur la lame de **réseau virtuel** , tapez le **nom** de la VNet, puis cliquez sur **espace d’adressage**. Configurer vos paramètres d’espace d’adresse pour la VNet et son premier sous-réseau, puis cliquez sur **OK**. La figure ci-dessous illustre les paramètres de bloc CIDR pour notre scénario.

    ![Lame d’espace adresse](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. Cliquez sur le **Groupe de ressources** et sélectionnez un groupe de ressources pour ajouter la VNet à ou cliquez sur **créer un groupe de ressources** pour ajouter la VNet à un nouveau groupe de ressources. La figure ci-dessous illustre les paramètres de groupe pour un groupe de ressources appelé **TestRG**à la ressource. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

    ![Créer des lames de groupe de ressources](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. Si nécessaire, modifiez les paramètres **d’abonnement** et **l’emplacement** de votre VNet. 

6. Si vous ne souhaitez pas voir la VNet sous forme de mosaïque dans le **Startboard**, désactiver le **code Pin à Startboard**. 

7. Cliquez sur **créer** et notez la mosaïque nommée **Création virtuel réseau** comme indiqué dans la figure ci-dessous.

    ![Créer des VNet de portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. Attendez que le VNet doit être créé et lorsque la mosaïque ci-dessous, cliquez sur pour ajouter plusieurs sous-réseaux.

    ![Créer des VNet de portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. Vous devriez voir la **Configuration** de votre VNet comme indiqué ci-dessous. 

    ![Créer des VNet de portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. Cliquez sur **sous-réseaux** > **Ajouter**, puis tapez un **nom** et spécifier une **plage d’adresses (bloc CIDR)** de votre sous-réseau, puis cliquez sur **OK**. La figure ci-dessous illustre les paramètres de notre scénario en cours.

    ![Créer VNet dans Azure portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)