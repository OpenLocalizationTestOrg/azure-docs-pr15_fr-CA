## <a name="prerequisites"></a>Conditions préalables

Avant que nous pouvons écrire le code de gestion CDN, nous devons effectuer certaines tâches de préparation pour permettre à notre code interagir avec le Gestionnaire de ressources Azure.  Pour ce faire, vous devez :

* Créer un groupe de ressources contenant le profil CDN que nous créer dans ce didacticiel
* Configurer Active Directory pour fournir l’authentification pour notre application de Azure
* Appliquer des autorisations au groupe de ressources afin que seuls les utilisateurs autorisés à partir de nos clients AD Azure peuvent interagir avec notre profil CDN

### <a name="creating-the-resource-group"></a>Création du groupe de ressources

1. Journal du [portail Azure](https://portal.azure.com).

2. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche, puis **gestion**et **Groupe de ressources**.
    
    ![Création d’un nouveau groupe de ressources](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)

3. Appelez votre groupe de ressources *CdnConsoleTutorial*.  Sélectionnez votre abonnement et choisissez un emplacement près de chez vous.  Si vous le souhaitez, vous pouvez cliquer sur la case à cocher **Ajouter au tableau de bord** pour afficher le groupe de ressources pour le tableau de bord dans le portail.  Cela rend plus facile à trouver ultérieurement.  Une fois que vous avez effectué vos sélections, cliquez sur **créer**.

    ![Nommer le groupe de ressources](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)

4. Une fois le groupe de ressources est créé, si vous n’avez pas épingler à votre tableau de bord, vous pouvez le rechercher en cliquant sur **Parcourir**, puis **Groupes de ressources**.  Cliquez sur le groupe de ressources pour l’ouvrir.  Prenez note de votre **ID d’abonnement**.  Nous l’avons besoin ultérieurement.

    ![Nommer le groupe de ressources](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Création de l’application Azure AD et l’application des autorisations

Il existe deux approches d’authentification d’application avec Azure Active Directory : les utilisateurs individuels ou à une entité de sécurité du service. Un service principal est similaire à un compte de service dans Windows.  Plutôt que d’accorder des autorisations pour interagir avec les profils CDN un utilisateur particulier, nous accordons à la place les autorisations pour le service principal.  Entités de service sont généralement utilisées pour des processus automatisés et non interactif.  Bien que ce didacticiel est écrit une application de console interactive, nous allons nous concentrer sur l’approche de principal du service.

Création d’une entité de service se compose de plusieurs étapes, y compris la création d’une application Azure Active Directory.  Pour ce faire, nous allons [suivre ce didacticiel](../articles/resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Veillez à suivre toutes les étapes du [didacticiel lié](../articles/resource-group-create-service-principal-portal.md).  Il est *extrêmement important* procédez exactement comme indiqué.  Notez votre **ID de client**, **nom de domaine client** (généralement un *. onmicrosoft.com* domaine sauf si vous avez spécifié un domaine personnalisé), **ID de client**et la **clé d’authentification de client**, comme nous aurons besoin de ces informations ultérieurement.  Veillez à protéger votre **ID client** et la **clé d’authentification de client**, ces informations d’identification peuvent être utilisée par tous les utilisateurs d’exécuter des opérations comme l’entité du service. 
>   
> Lorsque vous arrivez à l’étape de [configuration d’application mutualisée](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application), sélectionnez **non**.
> 
> Lorsque vous arrivez à l’étape [attribuer des applications à un rôle](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role), utilisez le groupe de ressources que nous avons créée précédemment, *CdnConsoleTutorial*, mais au lieu du rôle de **lecteur** , attribuer le rôle de **Collaborateur de profil CDN** .  Après avoir attribué à l’application le rôle **Collaborateur du profil CDN** sur votre groupe de ressources, revenir à ce didacticiel. 

Une fois que vous avez créé votre service principal et le rôle de **Collaborateur de profil CDN** , la lame **d’utilisateurs** de votre groupe de ressources doit ressembler à ceci.

![Lame d’utilisateurs](./media/cdn-app-dev-prep/cdn-service-principal-include.png)


### <a name="interactive-user-authentication"></a>Authentification de l’utilisateur interactif

Si, au lieu d’une entité de service, vous préférez que l’authentification d’utilisateur interactif, le processus est très similaire à celui d’une entité de service.  En fait, vous devez suivre la même procédure, mais d’apporter quelques modifications mineures.

> [AZURE.IMPORTANT] Seulement ces étapes suivant si vous avez choisi d’utiliser l’authentification d’utilisateur individuel au lieu d’un service principal.

1. Lors de la création de votre application, au lieu de l' **Application Web**, sélectionnez **l’application Native**. 
    
    ![Application native](./media/cdn-app-dev-prep/cdn-native-application-include.png)
    
2. Sur la page suivante, vous demandera une **URI de redirection**.  L’URI ne sont pas validées, mais gardez à l’esprit que vous avez saisi.  Vous en aurez besoin ultérieurement. 

3. Il est inutile de créer une **clé d’authentification de client**.

4. Au lieu d’assigner une entité de service au rôle **Contributeur du profil CDN** , nous allons affecter des utilisateurs individuels ou des groupes.  Dans cet exemple, vous pouvez voir que j’ai attribué *Canada-démonstration utilisateur* au rôle **Contributeur du profil CDN** .  
    
    ![Accès des utilisateurs individuels](./media/cdn-app-dev-prep/cdn-aad-user-include.png)

