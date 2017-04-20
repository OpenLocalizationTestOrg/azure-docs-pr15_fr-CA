## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Déployer le modèle ARM pour déployer à l’aide de clic

Vous pouvez réutiliser le téléchargement de modèles prédéfini ARM dans un référentiel github géré par Microsoft et l’ouvrir à la Communauté. Ces modèles peuvent être déployés directement de github, ou téléchargés et modifiés pour répondre à vos besoins. Pour déployer un modèle qui crée un VNet avec deux sous-réseaux, suivez les étapes ci-dessous.

1. À partir d’un navigateur, accédez à [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Faites défiler la liste des modèles, puis cliquez sur **101-vnet-2-sous-réseaux**. Vérifiez le fichier **README.md** , comme illustré ci-dessous.

    ![Fichier de READEME.md dans github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Cliquez sur **déployer sur Azure**. Si nécessaire, entrez vos informations d’identification de connexion Azure. 
4. Dans la lame de **paramètres** , entrez les valeurs que vous souhaitez utiliser pour créer votre nouveau VNet et puis cliquez sur **OK**. La figure ci-dessous indique les valeurs pour notre scénario.

    ![Paramètres de modèle ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Cliquez sur le **groupe de ressources** et sélectionnez un groupe de ressources pour ajouter la VNet à ou cliquez sur **créer** pour ajouter la VNet à un nouveau groupe de ressources. La figure ci-dessous illustre les paramètres de groupe pour un groupe de ressources appelé **TestRG**à la ressource.

    ![Groupe de ressources](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Si nécessaire, modifiez les paramètres **d’abonnement** et **l’emplacement** de votre VNet.
6. Si vous ne souhaitez pas voir la VNet sous forme de mosaïque dans le **Startboard**, désactiver le **code Pin à Startboard**.
5. Cliquez sur **termes de Leagl**, lisez le contrat et cliquez sur **acheter** pour accepter. 
6. Cliquez sur **créer** pour créer le VNet.

    ![Mosaïque de déploiement présentant dans portail d’aperçu](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. Une fois que le déploiement est terminé, cliquez sur **TestVNet** > **tous les paramètres** > **sous-réseaux** pour afficher les propriétés des sous-réseaux, comme illustré ci-dessous.

    ![Créer des VNet dans le portail de l’aperçu](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)