1. Ouvrez une session sur le [portail Azure].

2. Cliquez sur **+ Nouveau** > **Web + Mobile** > **Mobile App**, puis donnez un nom à votre back-end de l’application Mobile.

3. Pour le **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un nouveau (à l’aide du même nom que votre application.) 
 
    Vous pouvez sélectionner un autre plan de Service de l’application ou créer un nouveau. Pour plus d’informations sur les Services d’application plans et comment créer un plan dans un autre système de tarification de niveau et dans votre emplacement de votre choix, consultez [présentation approfondie des plans de Service d’application Azure](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Pour le **service d’application**, le plan par défaut (dans la [couche Standard](https://azure.microsoft.com/pricing/details/app-service/)) est sélectionné. Vous pouvez également sélectionner un autre plan, ou [créer un nouveau](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Du plan Service de l’application déterminent l' [emplacement, de fonctionnalités, de coûts et les ressources de calcul](https://azure.microsoft.com/pricing/details/app-service/) associés à votre application. 

    Après avoir décidé du plan, cliquez sur **créer**. Cette opération crée le back-end de l’application Mobile. 
    
6. De la lame de **paramètres** pour le nouveau back-end d’application Mobile, cliquez sur **démarrage rapide** > votre plate-forme d’application client > **connexion d’une base de données**. 

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. De la lame **d’Ajouter de connexion de données** , cliquez sur **Base de données de SQL** > **créer une nouvelle base de données**, tapez le **nom**de la base de données, choisissez un niveau de tarification, puis cliquez sur **le serveur**.  Vous pouvez réutiliser cette nouvelle base de données. Si vous disposez déjà d’une base de données dans le même emplacement, vous préférez **utiliser une base de données existante**. En raison des coûts de bande passante et à latence plus élevée n’est pas recommandé d’utiliser une base de données dans un emplacement différent.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. Dans le **nouveau serveur** lame, tapez un nom unique dans le champ **nom du serveur** , fournir une connexion et un mot de passe, vérifiez les **services azure d’autoriser à accéder aux serveurs**et cliquez sur **OK**. Cela crée la nouvelle base de données.

9. Dans la lame **Ajouter de connexion de données** , cliquez sur la **chaîne de connexion**, tapez les valeurs de nom d’utilisateur et le mot de passe pour votre base de données et cliquez sur **OK**. Attendez quelques minutes pour la base de données à déployer avec succès avant de continuer.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
