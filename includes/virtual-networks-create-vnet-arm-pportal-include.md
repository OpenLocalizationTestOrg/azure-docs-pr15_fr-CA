## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Comment créer un VNet dans le portail Azure

Pour créer un VNet en fonction du scénario ci-dessus à l’aide du portail aperçu Azure, suivez les étapes ci-dessous.

1. À partir d’un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous à votre compte Azure.
2. Cliquez sur **Nouveau** > **réseau** > **réseau virtuel**, puis cliquez sur **Gestionnaire de ressources** dans la liste **Sélectionnez un modèle de déploiement** , puis cliquez sur **créer**, comme illustré dans la figure ci-dessous.

    ![Créer VNet dans Azure portal](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. Sur la lame de **créer des réseaux virtuels** , configurer les paramètres de la VNet, comme illustré dans la figure ci-dessous.

    ![Créer des lames de réseau virtuel](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Cliquez sur le **groupe de ressources** et sélectionnez un groupe de ressources pour ajouter la VNet à ou cliquez sur **créer** pour ajouter la VNet à un nouveau groupe de ressources. La figure ci-dessous illustre les paramètres de groupe pour un groupe de ressources appelé **TestRG**à la ressource. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](../articles/resource-group-overview.md#resource-groups).

    ![Groupe de ressources](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Si nécessaire, modifiez les paramètres **d’abonnement** et **l’emplacement** de votre VNet. 

6. Si vous ne souhaitez pas voir la VNet sous forme de mosaïque dans le **Startboard**, désactiver le **code Pin à Startboard**. 

7. Cliquez sur **créer** et notez la mosaïque nommée **Création virtuel réseau** comme indiqué dans la figure ci-dessous.

    ![Création de mosaïque du réseau virtuel](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. Attendre le VNet à créer, puis dans la lame de **réseau virtuel** , **tous les paramètres** > **sous-réseaux** > **Ajouter** comme indiqué ci-dessous.

    ![Ajout de sous-réseau dans le portail Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. Spécifier les paramètres de sous-réseau pour le sous-réseau *back-end* , comme indiqué ci-dessous, puis cliquez sur **OK**. 

    ![Paramètres de sous-réseau](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. Notez la liste des sous-réseaux, comme indiqué dans la figure ci-dessous.

    ![Liste des sous-réseaux de VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)
