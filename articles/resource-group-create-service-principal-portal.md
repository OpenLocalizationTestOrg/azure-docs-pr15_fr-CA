<properties
   pageTitle="Créer le service principal portail | Microsoft Azure"
   description="Décrit comment créer une nouvelle application d’Active Directory et le principal du service qui peut être utilisé avec le contrôle d’accès basé sur un rôle dans le Gestionnaire de ressources Azure pour gérer l’accès aux ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Utiliser le portail pour créer l’application d’Active Directory et les identités de service qui peuvent accéder aux ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI Azure](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)


Lorsque vous avez une application qui doit accéder à ou de modifier des ressources, vous devez configurer une application Active Directory (AD) et lui affecter les autorisations requises. Cette rubrique vous indique comment effectuer ces étapes via le portail. Actuellement, vous devez utiliser le portail classique et créez une nouvelle application Active Directory, puis basculer vers le portail Azure pour attribuer un rôle à l’application. 

> [AZURE.NOTE] Les étapes décrites dans cette rubrique s’appliquent uniquement lorsque vous utilisez le **portail classique** pour créer l’application d’Active Directory. **Si vous utilisez le portail Azure pour créer l’application d’Active Directory, ces étapes ne réussira pas.** 
>
> Vous pouvez plus facile configurer votre application de publicité et d’un service principal via [PowerShell](resource-group-authenticate-service-principal.md) ou [CLI d’Azure](resource-group-authenticate-service-principal-cli.md), surtout si vous souhaitez utiliser un certificat pour l’authentification. Cette rubrique n’affiche pas l’utilisation d’un certificat.

Pour une explication des concepts relatifs à Active Directory, consultez [les objets Application et Service Principal](./active-directory/active-directory-application-objects.md). Pour plus d’informations sur l’authentification Active Directory, consultez [Scénarios d’authentification pour Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Pour obtenir la procédure détaillée sur l’intégration d’une application dans Azure pour la gestion des ressources, consultez le [guide du développeur d’autorisation avec l’API du Gestionnaire de ressources Azure](resource-manager-api-authentication.md).

## <a name="create-an-active-directory-application"></a>Création d’une application d’Active Directory

1. Connectez-vous à votre compte Azure via le [portail classique](https://manage.windowsazure.com/). 

2. Assurez-vous que vous connaissez la valeur par défaut de Active Directory pour votre abonnement. Vous pouvez limiter l’accès pour les applications dans le même répertoire que votre abonnement. Sélectionnez les **paramètres** et recherchez le nom du répertoire associé à votre abonnement.  Pour plus d’informations, consultez [comment Azure abonnements sont associés à Active Directory de Azure](./active-directory/active-directory-how-subscriptions-associated-directory.md).
   
     ![Rechercher le répertoire par défaut](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. Dans le volet gauche, sélectionnez **Active Directory** .

     ![Sélectionnez Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Sélectionnez Active Directory que vous souhaitez utiliser pour la création de l’application. Si vous avez plus d’un Active Directory, créer l’application dans le répertoire par défaut pour votre abonnement.   

     ![choix d’un répertoire](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. Pour afficher les applications dans votre répertoire, sélectionnez **Applications**.

     ![afficher les applications](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Si vous n’avez pas créé une application dans ce répertoire avant, vous devriez voir quelque chose de similaire à l’image suivante. Sélectionnez **Ajouter une APPLICATION**

     ![Ajouter une application](./media/resource-group-create-service-principal-portal/create-application.png)

     Ou bien, cliquez sur **Ajouter** dans le volet inférieur.

     ![ajouter](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Sélectionnez le type d’application que vous souhaitez créer. Pour ce didacticiel, sélectionnez **Ajouter une application à mon entreprise se développe**. 

     ![nouvelle application](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Fournissez un nom pour l’application, puis sélectionnez le type d’application que vous souhaitez créer. Pour ce didacticiel, créez un **WEB APPLICATION et/ou WEB API** et cliquez sur le bouton suivant. Si vous sélectionnez **APPLICATION CLIENT d’origine**, les étapes restantes de cet article ne correspondront pas à votre expérience.

     ![application de nom](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Renseignez les propriétés de votre application. Pour **Les URL de session**, fournir l’URI à un site web qui décrit votre application. L’existence du site web n’est pas validé. Pour l' **URI d’ID APP**, fournissent l’URI qui identifie votre application.

     ![propriétés de l’application](./media/resource-group-create-service-principal-portal/app-properties.png)

Vous avez créé votre application.

## <a name="get-client-id-and-authentication-key"></a>Obtenir des clé d’identification et d’authentification de client

Lors de la connexion par programme, vous devez l’id de votre application. Si l’application s’exécute sous ses propres informations d’identification, vous avez également besoin d’une clé d’authentification.

1. Sélectionnez l’onglet **configurer** pour configurer le mot de passe de votre application.

     ![configurer l’application](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Copiez l' **ID de CLIENT**.
  
     ![id de client](./media/resource-group-create-service-principal-portal/client-id.png)

3. Si l’application s’exécute sous ses propres informations d’identification, faites défiler jusqu'à la section **clés** et sélectionnez la durée pendant laquelle vous souhaitez que votre mot de passe soit valide.

     ![clés](./media/resource-group-create-service-principal-portal/create-key.png)

4. Sélectionnez **Enregistrer** pour créer votre clé.

     ![Enregistrer](./media/resource-group-create-service-principal-portal/save-icon.png)

     La clé enregistrée s’affiche et vous pouvez le copier. Vous n’êtes pas en mesure de récupérer la clé ultérieurement donc copier maintenant.

     ![Enregistrer la clé](./media/resource-group-create-service-principal-portal/save-key.png)

## <a name="get-tenant-id"></a>Obtenir l’id de client

Lors de la connexion par programme, vous devez passer l’id de client avec votre demande d’authentification. Pour les applications Web et des applications d’API Web, vous pouvez récupérer l’id du client en sélectionnant des **points de terminaison afficher** au bas de l’écran et d’extraction de l’id, comme illustré dans l’image suivante.  

   ![id de clients](./media/resource-group-create-service-principal-portal/save-tenant.png)

Vous pouvez également récupérer l’id de client par le biais de PowerShell :

    Get-AzureRmSubscription

Ou bien, Azure CLI :

    azure account show --json

## <a name="set-delegated-permissions"></a>Délégué de définir des autorisations

Si votre application accède aux ressources pour le compte d’un utilisateur connecté, vous devez accorder à votre application l’autorisation déléguée pour accéder aux autres applications. Vous accordez cet accès dans la section **autorisations à d’autres applications** de l’onglet **configurer** . Par défaut, une autorisation déléguée est déjà activée pour Azure Active Directory. Laissez cette autorisation déléguée est inchangée.

1. Sélectionnez **Ajouter application**.

2. Dans la liste, sélectionnez l' **API de gestion de Service de Windows Azure**. Ensuite, sélectionnez l’icône complète.

      ![Sélectionnez l’application](./media/resource-group-create-service-principal-portal/select-app.png)

3. Dans la liste déroulante autorisations déléguées, sélectionnez **Gestion des services Azure Access en tant qu’organisation**.

      ![Sélectionnez l’autorisation](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Enregistrer les modifications.

## <a name="assign-application-to-role"></a>Attribuer des applications à un rôle

Si votre application s’exécute sous ses propres informations d’identification, vous devez affecter l’application à un rôle. Décider quel rôle représente les autorisations appropriées pour l’application. Pour obtenir des informations sur les rôles disponibles, reportez-vous à la section [RBAC : intégrées dans les rôles](./active-directory/role-based-access-built-in-roles.md). 

Pour affecter un rôle à une application, vous devez disposer des autorisations appropriées. Plus précisément, vous devez disposer de `Microsoft.Authorization/*/Write` accès accordé par le rôle de [propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur d’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Le rôle de collaborateur n’a pas l’accès approprié.

Vous pouvez définir l’étendue au niveau de l’abonnement, le groupe de ressources ou la ressource. À des niveaux inférieurs de la portée, les autorisations sont héritées. Par exemple, l’ajout d’une application au rôle de lecteur pour un groupe de ressources signifie qu’il peut lire le groupe de ressources et les ressources qu’il contient.

1. Pour affecter l’application à un rôle, passez à partir du portail classique sur le [portail Azure](https://portal.azure.com).

1. Vérifiez vos autorisations pour vous assurer que vous pouvez affecter l’entité du service à un rôle. Sélectionnez **Mes autorisations** pour votre compte.

    ![Sélectionnez mes autorisations](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. Permet d’afficher les autorisations attribuées à votre compte. Comme indiqué précédemment, vous devez appartenir aux rôles propriétaire ou administrateur d’accès utilisateur, ou disposer d’un rôle personnalisé qui accorde un accès en écriture pour Microsoft.Authorization. L’image suivante montre un compte qui est affecté au rôle Contributeur pour l’abonnement, ce qui n’est pas des autorisations adéquates pour attribuer une application à un rôle.

    ![Afficher mes autorisations](./media/resource-group-create-service-principal-portal/show-permissions.png)

     Si vous n’avez les autorisations correctes pour accorder l’accès à une application, que vous devez soit demander à votre administrateur d’abonnement l’ajoute au rôle administrateur d’accès utilisateur, vous, ou qui demande un administrateur accorde l’accès à l’application.

1. Accéder au niveau de l’étendue que vous souhaitez affecter l’application. Pour affecter un rôle au niveau de la portée de l’abonnement, sélectionnez **abonnements**.

     ![Sélectionnez l’abonnement](./media/resource-group-create-service-principal-portal/select-subscription.png)

     Sélectionnez l’abonnement particulier pour affecter l’application.

     ![Sélectionnez l’abonnement pour l’affectation](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

     Sélectionnez l’icône **d’accès** dans le coin supérieur droit.

     ![Sélectionnez accès](./media/resource-group-create-service-principal-portal/select-access.png)
     
     Ou bien, pour affecter un rôle à une portée de groupe de ressources, accédez à un groupe de ressources. À partir de la blade de groupe de ressource, sélectionnez le **contrôle d’accès**.

     ![Sélectionnez les utilisateurs](./media/resource-group-create-service-principal-portal/select-users.png)

     Les étapes suivantes sont les mêmes pour n’importe quelle portée.

2. Sélectionnez **Ajouter**.

     ![Sélectionnez l’option Ajouter](./media/resource-group-create-service-principal-portal/select-add.png)

3. Sélectionnez le rôle de **lecteur** (ou quel que soit le rôle que vous souhaitez affecter l’application).

     ![Sélectionnez le rôle](./media/resource-group-create-service-principal-portal/select-role.png)

4. Lorsque vous consultez tout d’abord la liste des utilisateurs, que vous pouvez ajouter le rôle, vous ne verrez pas les applications. Vous ne verrez que les groupes et les utilisateurs.

     ![afficher les utilisateurs](./media/resource-group-create-service-principal-portal/show-users.png)

5. Pour rechercher votre application, vous devez le rechercher. Commencez à taper le nom de votre application, et modifie la liste des options disponibles. Sélectionnez votre application lorsque vous le voyez dans la liste.

     ![affecter au rôle](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Sélectionnez **OK** pour terminer l’affectation du rôle. Vous devez maintenant voir votre application dans la liste des utilisations assignés à un rôle pour le groupe de ressources.


Pour plus d’informations sur l’affectation des utilisateurs et des applications à des rôles via le portail, consultez [utiliser les affectations de rôles pour gérer l’accès aux ressources de votre abonnement Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## <a name="sample-applications"></a>Exemples d’applications

Les exemples d’applications suivantes montrent comment ouvrir une session en tant que l’entité de sécurité du service.

**.NET**

- [Déployer un SSH activé l’ordinateur virtuel avec un modèle avec .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gérer les ressources Azure et les groupes de ressources avec .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Mise en route de ressources - déployer à l’aide du modèle de gestionnaire de ressources Azure - dans Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Mise en route de ressources - gérer le groupe de ressources - dans Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Déployer un SSH activé l’ordinateur virtuel à un modèle dans les Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gestion des ressources Azure et des groupes de ressources avec les Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Déployer un SSH activé l’ordinateur virtuel à un modèle dans Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gérer les ressources et groupes avec Node.js ressources Azure](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Déployer un SSH activé l’ordinateur virtuel avec un modèle en Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gestion des ressources Azure et Ruby, les groupes de ressources](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la spécification des stratégies de sécurité, consultez [Contrôle d’accès basé sur les rôles d’Azure](./active-directory/role-based-access-control-configure.md).  
- Pour une démonstration vidéo de ces étapes, consultez [Activation de la gestion par programme d’une ressource d’Azure avec Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

