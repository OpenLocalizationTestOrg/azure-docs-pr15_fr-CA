Pour créer un VNet dans le modèle de déploiement du Gestionnaire de ressources à l’aide du portail Azure, suivez les étapes ci-dessous. Les captures d’écran sont fournis comme exemples. Veillez à remplacer les valeurs de votre propre. Pour plus d’informations sur l’utilisation de réseaux virtuels, consultez la [Vue d’ensemble de réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

1. À partir d’un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous à votre compte Azure.

2. Cliquez sur **Nouveau**. Dans le champ de **recherche sur le marché** , tapez « Réseau virtuel ». Recherchez un **Réseau virtuel** à partir de la liste retournée et cliquez sur pour ouvrir la lame de **Réseau virtuel** .

    ![Lame de ressource de localiser un réseau virtuel] (./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Lame de ressource réseau virtuel rechercher")

3. Dans la partie inférieure de la lame de réseau virtuel, dans la liste **Sélectionnez un modèle de déploiement** , sélectionnez **Le Gestionnaire de ressources**, puis cliquez sur **créer**.


    ![Sélectionnez le Gestionnaire de ressources] (./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Sélectionnez le Gestionnaire de ressources")

4. Sur la lame de **créer des réseaux virtuels** , configurer les paramètres de VNet. Lorsque vous renseignez les champs, le point d’exclamation rouge deviendra une coche verte lorsque les caractères entrés dans le champ sont valides.

    ![Validation de champ] (./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Validation de champ")

5. La lame de **créer des réseaux virtuels** est similaire à l’exemple suivant. Il peut y avoir des valeurs qui sont remplis automatiquement. Si tel est le cas, remplacez les valeurs par vous-même.

    ![Lame de réseau virtuel créer] (./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Lame de réseau virtuel créer")

6. **Nom**: entrez le nom de votre réseau virtuel.

7. **Espace d’adressage**: entrez l’espace d’adressage. Si vous avez plusieurs espaces d’adressage pour ajouter, ajouter votre premier espace d’adressage. Vous pouvez ajouter des espaces d’adressage supplémentaires ultérieurement, après avoir créé le VNet.
 
8. **Nom du sous-réseau**: ajouter le nom de sous-réseau et l’adresse du sous-réseau. Vous pouvez ajouter des sous-réseaux supplémentaires ultérieurement, après avoir créé le VNet.

10. **Abonnement**: Vérifiez que l’abonnement répertoriée est correcte. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.

11. **Groupe de ressources**: sélectionnez un groupe de ressources existant ou créez-en un nouveau en tapant un nom pour votre nouveau groupe de ressources. Si vous créez un nouveau groupe, nommez le groupe de ressources en fonction des valeurs de votre configuration envisagée. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](resource-group-overview.md#resource-groups).

12. **Emplacement**: sélectionnez l’emplacement de votre VNet. L’emplacement détermine où les ressources que vous déployez sur cette VNet résidera.

13. Sélectionnez **Ajouter au tableau de bord** si vous voulez être en mesure de trouver votre VNet facilement sur le tableau de bord, puis cliquez sur **créer**.
    
    ![Fixer au tableau de bord] (./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "fixer au tableau de bord")

14. Après avoir cliqué sur **créer**, vous verrez une mosaïque sur votre tableau de bord qui reflète la progression de votre VNet. La mosaïque change à mesure que le VNet est en cours de création.

    ![Mosaïque de création des réseaux virtuels] (./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Mosaïque de création des réseaux virtuels")