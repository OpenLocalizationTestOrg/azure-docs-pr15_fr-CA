<properties 
    pageTitle="Mise en route de l’API REST de Azure recherche gestion | Microsoft Azure | Service de recherche de nuage hébergé" 
    description="Administrer votre nuage hébergé service Azure recherche à l’aide d’une API REST de gestion" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="get-started-with-azure-search-management-rest-api"></a>Mise en route de l’API REST de Azure recherche gestion
> [AZURE.SELECTOR]
- [Portail](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

L’API de gestion reste de recherche Azure est une alternative de programmation à l’exécution de tâches administratives dans le portail. Les opérations de gestion de service incluent la création ou de suppression du service, le service de mise à l’échelle et gestion des clés. Ce didacticiel est livré avec un exemple d’application cliente qui montre l’API de gestion de service. Il inclut également les étapes de configuration nécessaires pour exécuter l’exemple dans votre environnement de développement local.

Pour terminer ce didacticiel, vous devez :

- Visual Studio 2012 ou 2013
- le téléchargement d’application exemple client

Dans le cadre de la fin de ce didacticiel, deux services seront déployés : recherche d’Azure et d’Azure Active Directory (AD). En outre, vous allez créer une application de publicité qui établit la relation d’approbation entre votre application cliente et le point de terminaison de gestionnaire de ressources dans Azure.

Vous devrez un compte Azure pour compléter ce didacticiel.


##<a name="download-the-sample-application"></a>Télécharger l’exemple d’application

Ce didacticiel est basé sur une application de console Windows écrite en C#, que vous pouvez modifier et exécuter dans Visual Studio 2012 ou 2013

Vous pouvez trouver l’application cliente sur Github à la [Démonstration de Azure recherche .NET Management API](https://github.com/Azure-Samples/search-dotnet-management-api/).


##<a name="configure-the-application"></a>Configuration de l’application

Avant de pouvoir exécuter l’exemple d’application, vous devez activer l’authentification afin que les demandes envoyées à partir de l’application cliente à l’extrémité de gestionnaire de ressources peuvent être acceptées. L’origine de la demande d’authentification avec le [Gestionnaire de ressources Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx), qui sert de base pour toutes les opérations liées au portail demandé via une API, y compris ceux liés à la gestion de service de recherche. L’API de gestion de service de recherche d’Azure est simplement une extension du Gestionnaire de ressources Azure et par conséquent hérite ses dépendances.  

Le Gestionnaire de ressources Azure nécessite service Azure Active Directory en tant que fournisseur d’identité. 

Pour obtenir un jeton d’accès qui autorise les demandes d’atteindre le Gestionnaire de ressources, l’application cliente contient un segment de code qui appelle Active Directory. Le segment de code, ainsi que les étapes préalables à l’utilisation du segment de code, ont été retirés de cet article : [les demandes d’authentification Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Vous pouvez suivre les instructions fournies dans le lien ci-dessus, ou utiliser les étapes de ce document si vous préférez parcourir le didacticiel étape par étape.

Dans cette section, vous allez effectuer les tâches suivantes :

1. Création d’un service Active Directory
1. Création d’une application de publicité
1. Configurer l’application AD en enregistrant les détails à propos de l’exemple d’application client que vous avez téléchargé
1. Charger l’exemple d’application cliente avec les valeurs qu’il utilisera pour obtenir l’autorisation pour ses demandes

> [AZURE.NOTE] Ces liens fournissent un arrière-plan à l’aide d’Azure Active Directory pour l’authentification des demandes des clients vers le Gestionnaire de ressources : [Azure le Gestionnaire de ressources](http://msdn.microsoft.com/library/azure/dn790568.aspx), les [demandes de l’authentification d’un gestionnaire de ressources Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx)et [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx).

###<a name="create-an-active-directory-service"></a>Créer un Service Active Directory

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com).

2. Faites défiler le volet de navigation gauche, puis cliquez sur **Active Directory**.

4. Cliquez sur **Nouveau** pour ouvrir les **Services de l’application** | **Active Directory**. Dans cette étape, vous créez un nouveau service Active Directory. Ce service va héberger l’application d’Active Directory que vous allez définir quelques étapes à partir de maintenant. Création d’un nouveau service permet d’isoler le didacticiel à partir d’autres applications que vous pouvez héberger déjà dans Azure.

5. Cliquez sur **le répertoire** | **créer des personnalisées**.

6. Entrez un nom de service, le domaine et la géolocalisation. Le domaine doit être unique. Cliquez sur la case à cocher pour créer le service.

     ![][5]

###<a name="create-a-new-ad-application-for-this-service"></a>Créez une nouvelle application d’Active Directory pour ce service

1. Sélectionnez le service Active Directory de « SearchTutorial » que vous venez de créer.

2. Dans le menu supérieur, cliquez sur **Applications**. 
 
3. Cliquez sur **Ajouter une Application**. Une application Active Directory stocke des informations sur les applications client qui va l’utiliser en tant que fournisseur d’identité.  
 
4. Cliquez sur **Ajouter une application à mon entreprise se développe**. Cette option fournit les paramètres d’enregistrement pour les applications qui ne sont pas publiées dans la bibliothèque de l’application. Étant donné que l’application cliente n’est pas partie de la bibliothèque de l’application, c’est le bon choix pour ce didacticiel.

     ![][6]
 
5. Entrez un nom, tel que « Gestionnaire de recherche Azure ».

6. Choisissez **l’application client natif** pour type d’application. Cette valeur correspond à l’exemple d’application ; Il se produit une application client (console) de Windows, pas une application web.

     ![][7]
 
7. Dans Rediriger URI, entrez « http://localhost/Azure-Search-Manager-App ». C’est un URI vers lequel Azure Active Directory redirige user-agent en réponse à une demande d’autorisation OAuth 2.0. La valeur ne doit pas nécessaire être un point de terminaison physique, mais doit être un URI valide. 

    Pour les besoins de ce didacticiel, la valeur peut être n’importe quoi, mais que vous entrez devient une entrée requise pour la connexion d’administration dans l’exemple d’application. 
 
7. Cliquez sur la case à cocher pour créer l’application d’Active Directory. Vous devez voir « Azure-recherche-Manager-App » dans le volet de navigation de gauche.

###<a name="configure-the-ad-application"></a>Configurer l’application d’Active Directory
 
9. Cliquez sur l’application de la publicité, « Azure-recherche-Manager-App », que vous venez de créer. Il doit se qu'affiche dans le volet de navigation de gauche.

10. Dans le menu supérieur, cliquez sur **configurer** .
 
11. Autorisations faites défiler et sélectionnez des **API de gestion d’Azure**. Dans cette étape, vous spécifiez l’API (dans ce cas, il s’agit de l’API du Gestionnaire de ressources Azure) que l’application cliente doit avoir accès à, ainsi que le niveau d’accès dont il a besoin.

12. Dans déléguer les autorisations, cliquez sur la liste déroulante et sélectionnez **Gestion des services accès Azure (aperçu**).
 
     ![][8]
 
13. Enregistrer les modifications. 

Maintenir la page de configuration d’application. À l’étape suivante, vous copiez les valeurs à partir de cette page et les entrer dans l’exemple d’application.

###<a name="load-the-sample-application-program-with-registration-and-subscription-values"></a>Charger l’exemple de programme d’application avec des valeurs d’inscription et d’abonnement

Dans cette section, vous allez modifier la solution dans Visual Studio, en remplaçant les valeurs obtenues à partir du portail.
Les valeurs que vous ajoutez apparaissent en haut de Program.cs :

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Si vous n’avez pas encore [téléchargé l’exemple d’application à partir de Github](https://github.com/Azure-Samples/search-dotnet-management-api/), vous en aurez besoin pour cette étape.

1. Dans Visual Studio, ouvrez le **ManagementAPI.sln** .

2. Ouvrez Program.cs.

3. Fournir des `ClientId`. À partir de la publicité page configuration de l’application reste ouvert à partir de l’étape précédente, copiez l’ID de Client à partir de la page de configuration d’application AD dans le portail et collez-le dans Program.cs.

4. Fournir des `RedirectUrl`. Copier l’URI de redirection à partir de la même page de portail et collez-le dans Program.cs.

    ![][9]

5. Fournir`TenantID.` 
    - Revenez à Active Directory | SearchTutorial (service). 
    - Cliquez sur **Applications** dans la barre supérieure. 
    - Cliquez sur **Les points de terminaison afficher** au bas de la page. 
    - Copier le point de terminaison OAUTH 2.0 d’autorisation au bas de la liste. 
    - Collez le point de terminaison TenantID, ajustement de la valeur de tous les paramètres URI, à l’exception de l’ID de client.

    Compte tenu de « https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0 », supprimez tout le contenu, à l’exception de « 55e324c7-1656-4afe-8dc3-43efcd4ffa50 ».

    ![][10]

6. Fournir des `SubscriptionID`.
    - Accédez à la page principale du portail.
    - Cliquez sur **paramètres** en bas du volet de navigation de gauche.
    - Sous l’onglet Abonnements, copiez l’ID de l’abonnement et collez-le dans Program.cs.

7. Enregistrez et générez la solution.


##<a name="explore-the-application"></a>Explorer l’application

Ajouter un point d’arrêt au premier appel de méthode, afin que vous pouvez exécuter le programme. Appuyez sur **F5** pour exécuter l’application et appuyez sur la touche **F11** pour parcourir le code.

L’exemple d’application crée un service de recherche d’Azure gratuit pour un abonnement Azure. S’il existe déjà un service gratuit pour votre abonnement, l’exemple d’application échouera. Un seul service de recherche gratuit par abonnement est autorisé.

1. Ouvrez Program.cs à partir de l’Explorateur de solutions et à la fonction Main (string [] void). 
 
3. Notez que **ExecuteArmRequest** est utilisé pour exécuter des demandes sur le point de terminaison du Gestionnaire de ressources Azure, `https://management.azure.com/subscriptions` d’un `subscriptionID`. Cette méthode est utilisée dans tout le programme pour effectuer des opérations à l’aide de l’API du Gestionnaire de ressources Azure ou l’API de gestion de recherche.

3. Demandes pour le Gestionnaire de ressources Azure doivent être authentifiées et autorisées. Pour cela, à l’aide de la méthode **GetAuthorizationHeader** , appelée par la méthode **ExecuteArmRequest** , empruntée à partir de [demandes d’authentification Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx). Notez que **GetAuthorizationHeader** appelle `https://management.core.windows.net` pour obtenir un jeton d’accès.

4. Vous êtes invité à vous connecter avec un nom d’utilisateur et le mot de passe valide pour votre abonnement.

5. Ensuite, un nouveau service de recherche d’Azure est enregistré avec le fournisseur du Gestionnaire de ressources Azure. À nouveau, il s’agit de la méthode **ExecuteArmRequest** , permet de créer le service de recherche sur Azure pour votre abonnement via cette fois `providers/Microsoft.Search/register`. 

6. Le reste du programme utilise l' [API REST de Azure recherche gestion](http://msdn.microsoft.com/library/dn832684.aspx). Notez que la `api-version` de cette API est différente de la version api Azure le Gestionnaire de ressources. Par exemple, `/listAdminKeys?api-version=2014-07-31-Preview` illustre les `api-version` de l’API REST de Azure recherche gestion.

    La série suivante d’opérations d’extraire la définition de service, vous venez de créer, l’api admin-clés, régénère récupère les clés, modifie la partition et les réplicas et enfin supprime le service.

    Lorsque vous modifiez le nombre de réplicas ou de la partition de service, il est prévu que cette action échoue si vous utilisez l’édition gratuite. Seule l’édition standard peut faire utiliser des réplicas des partitions supplémentaires.

    Suppression du service est la dernière opération.

##<a name="next-steps"></a>Étapes suivantes

Après avoir terminé ce didacticiel, vous pouvez souhaiter en savoir plus sur la gestion de services ou de l’authentification avec le service Active Directory :

- Pour en savoir plus sur l’intégration d’une application cliente avec Active Directory. Reportez-vous à la section [intégration des Applications dans Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151122.aspx).
- Obtenir des informations sur les autres opérations de gestion de service dans Azure. Reportez-vous à la section [gestion de vos Services](http://msdn.microsoft.com/library/azure/dn578292.aspx).

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md


 
