#<a name="how-to-create-a-custom-virtual-machine"></a>Comment faire pour créer un ordinateur virtuel personnalisé

Un ordinateur virtuel *personnalisé* fait référence à un ordinateur virtuel que vous créez à l’aide de la méthode **De la galerie** , car il vous permet de configurer plus d’options que la méthode de **Création rapide** . Ces options sont les suivantes :

- Plus de choix de l’image à utiliser pour créer la machine virtuelle (VM)
- Connexion à un réseau virtuel de la machine virtuelle
- Ajout de la machine virtuelle à un service cloud existant
- Ajout de la machine virtuelle à un jeu de disponibilité

> [AZURE.IMPORTANT] Si vous souhaitez que votre machine virtuelle pour utiliser un réseau virtuel, afin de vous y connecter directement par nom d’hôte ou de l’ensemble des connexions de coexistence, assurez-vous que vous spécifiez le réseau virtuel lorsque vous créez l’ordinateur virtuel. Un ordinateur virtuel peut être configuré pour vous joindre à un réseau virtuel que lorsque vous créez l’ordinateur virtuel. Pour plus d’informations sur les réseaux virtuels, consultez [Vue d’ensemble du réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1. Connectez-vous au [portail Azure](http://manage.windowsazure.com).

2. Dans la barre de commandes, cliquez sur **Nouveau**.

3. Cliquez sur **Calculer**, cliquez sur **ordinateur virtuel**, puis cliquez sur **à partir de la galerie**.

4. Choisissez l’image que vous souhaitez utiliser, puis cliquez sur la flèche pour continuer.

5. Si plusieurs versions de l’image sont disponibles, dans la **Version Release Date**, sélectionnez la version que vous souhaitez utiliser.

6. Dans la zone **Nom de l’ordinateur virtuel**, tapez le nom que vous souhaitez utiliser pour la machine virtuelle.

7. Pour sélectionner la taille appropriée pour l’ordinateur virtuel, utilisez **niveau** et la **taille** . La taille que vous sélectionnez affecte la configuration maximale de la machine virtuelle, ainsi que la tarification. Pour plus d’informations, voir les [tailles de Service de Cloud pour Azure et de la Machine virtuelle](http://go.microsoft.com/fwlink/p/?LinkID=389844).

8. Dans la zone **Nouveau nom d’utilisateur**, tapez un nom pour le compte d’administrateur que vous souhaitez utiliser pour gérer le serveur.

9. Dans le **Nouveau mot de passe**, tapez un mot de passe fort pour le compte d’administrateur. Dans **Confirmer le mot de passe**, retapez le mot de passe.

10. Cliquez sur la flèche pour continuer.

11. Dans **Service Cloud**, effectuez l’une des opérations suivantes :

    - S’il s’agit de l’ordinateur virtuel premier ou uniquement dans le service cloud, sélectionnez **créer un nouveau Service en nuage**. Puis, dans la zone **Nom DNS du Service Cloud**, tapez un nom qui utilise entre 3 et 24 de minuscules et de chiffres. Ce nom devient partie intégrante de l’URI qui est utilisé pour contacter l’ordinateur virtuel via le service cloud.
    - Si cet ordinateur virtuel est ajouté à un service en nuage, sélectionnez-le dans la liste.

    > [AZURE.NOTE] Pour plus d’informations sur le placement des machines virtuelles dans le même service de cloud, voir [comment connecter les ordinateurs virtuels dans un service en nuage](https://azure.microsoft.com/manage/windows/how-to-guides/connect-to-a-cloud-service/).

12. Dans un **Réseau de groupe/virtuel / l’affinité de la région**, sélectionnez la région, groupe d’affinité ou réseau virtuel que vous souhaitez utiliser pour la machine virtuelle. Pour plus d’informations sur les groupes d’affinité, consultez [sur affinités pour réseau virtuel](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

13. Dans le **Compte de stockage**, sélectionnez un compte de stockage existant pour le fichier de disque dur virtuel, ou utilisez un compte de stockage généré automatiquement. Un seul compte de stockage par région est créé automatiquement. Tous les autres ordinateurs virtuels que vous créez avec ce paramètre se trouvent dans ce compte de stockage. Vous êtes limité à 20 comptes de stockage.

14. Si vous souhaitez que l’ordinateur virtuel appartenant à un jeu de disponibilité, **Disponibilité définie**, sélectionnez **jeu de disponibilité de la création** ou l’ajouter à un jeu existant de la disponibilité.

    **Remarque**: les ordinateurs virtuels dans un jeu de disponibilité sont déployés sur les domaines d’erreur différents. Placement de plusieurs machines virtuelles dans une disponibilité ensemble permet de garantir que votre application est disponible lors de défaillances réseau, défaillances matérielles de disque local et les temps d’arrêt planifié.

15.  Sous **points de terminaison**, passez en revue les nouveaux points de terminaison qui va être créées pour autoriser les connexions à l’ordinateur virtuel, par exemple par le biais de bureau à distance ou un client SSH (Secure Shell). Vous pouvez également ajouter des points de terminaison maintenant ou les créer plus tard. Pour obtenir des instructions sur la création de leur ultérieurement, consultez [comment définir les points de terminaison à une Machine virtuelle](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

16.  Sous **Agent de la machine virtuelle**, décider si vous souhaitez installer l’Agent de la machine virtuelle. Cet agent fournit l’environnement pour vous permet d’installer les extensions qui peuvent vous aider à interagir avec l’ordinateur virtuel. Pour plus d’informations, voir [Gérer les Extensions](http://go.microsoft.com/FWLink/p/?LinkID=390493).

17. Cliquez sur la flèche pour créer l’ordinateur virtuel.

    ![Création de VM personnalisée réussie](./media/howto-custom-create-vm/VMSuccessWindows.png)

##<a name="next-steps"></a>Étapes suivantes##
Une fois que la machine virtuelle est créée, elle est automatiquement démarré. Lorsque le portail affiche l’état en cours d’exécution, vous pouvez vous connecter à l’ordinateur virtuel. Pour obtenir des instructions, consultez les articles suivants :

- [Comment faire pour ouvrir une session sur un ordinateur virtuel exécutant Linux](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)
- [Comment faire pour ouvrir une session sur un ordinateur virtuel exécutant Windows Server](../articles/virtual-machines/virtual-machines-windows-classic-connect-logon.md)

