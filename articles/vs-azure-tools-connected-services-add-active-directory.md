<properties 
   pageTitle="Ajout d’Azure Active Directory à l’aide de Services connectés dans Visual Studio | Microsoft Azure"
   description="Ajouter un Azure Active Directory à l’aide de la boîte de dialogue Visual Studio ajouter Services connectés"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="active-directory"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Ajout d’Azure Active Directory à l’aide de Services connectés dans Visual Studio 

##<a name="overview"></a>Vue d’ensemble
Grâce à Azure Active Directory (AD Azure), vous pouvez prend en charge Single Sign-On (SSO) pour les applications ASP.NET MVC, ou l’authentification d’Active Directory dans les services de l’API Web. Avec l’authentification AD Azure, vos utilisateurs peuvent utiliser leurs comptes dans Active Directory Azure pour vous connecter à vos applications web. Les avantages de l’authentification Active Directory Azure avec les API Web incluent sécurité renforcée des données lors de l’exposition d’une API à partir d’une application web. Avec AD Azure, il est inutile de gérer un système d’authentification distinct avec sa propre gestion de compte et d’utilisateur.

## <a name="supported-project-types"></a>Types de projet pris en charge

Vous pouvez utiliser la boîte de dialogue Services connectés pour se connecter à AD Azure dans les types de projet suivants.

- Projets ASP.NET MVC

- Projets ASP.NET Web API


### <a name="connect-to-azure-ad-using-the-connected-services-dialog"></a>Se connecter à Azure AD à l’aide de la boîte de dialogue Services connectés

1. Assurez-vous que vous disposez d’un compte Azure. Si vous n’avez pas un compte Azure, vous pouvez vous inscrire pour un [essai gratuit](http://go.microsoft.com/fwlink/?LinkId=518146).

1. Dans Visual Studio, ouvrez le menu contextuel du nœud **références** dans votre projet et cliquez sur **Ajouter des Services connectés**.
1. Sélectionnez **l’authentification Active Directory Azure** , puis choisissez **configurer**.

    ![Cliquez sur Ajouter l’authentification Active Directory Azure](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Sur la première page de l', **Azure de configurer l’authentification Active Directory**, vérifiez la **configuration de l’authentification unique à l’aide d’Active Directory Azure**.

    Si votre projet est configuré avec une autre configuration de l’authentification, l’Assistant vous avertit que la poursuite de la configuration précédente.

    ![Configurer Active Directory Azure dans l’Assistant](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  Sur la deuxième page, sélectionnez un domaine dans la liste déroulante de **domaine** . La liste des domaines contient tous les domaines accessibles par les comptes répertoriés dans la boîte de dialogue Paramètres du compte. Comme alternative, vous pouvez entrer un nom de domaine si vous ne trouvez pas celui que vous recherchez, par exemple mydomain.onmicrosoft.com. Vous pouvez choisir l’option pour créer une nouvelle annonce Azure app ou utiliser les paramètres d’une application Azure AD existante. 

    ![Configurer Active Directory Azure dans l’Assistant](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. Sur la troisième page de l’Assistant, assurez-vous que **lire les données de l’annuaire** est activée. L’Assistant renseigne le **secret du Client**. 

    ![Configurer Active Directory Azure dans l’Assistant](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Cliquez sur le bouton **Terminer** . La boîte de dialogue ajoute le code de configuration nécessaires et des références pour activer votre projet pour l’authentification Active Directory Azure. Vous pouvez voir le domaine AD sur le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Passez en revue la page de démarrage qui s’affiche dans votre navigateur pour obtenir des idées sur les prochaines étapes et la page de ce qui est arrivé pour voir comment votre projet a été modifié. Si vous souhaitez vérifier que tout fonctionne, ouvrez un des fichiers de configuration modifiés et vérifiez que les paramètres mentionnés dans ce qui est arrivé n’y sont. Par exemple, le fichier web.config principal, un projet ASP.NET MVC aura ces paramètres ajoutés :

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## <a name="how-your-project-is-modified"></a>Comment votre projet est modifié

Lorsque vous exécutez l’Assistant, Visual Studio ajoute AD Azure et associés à des références à votre projet. Fichiers de configuration et les fichiers de code dans votre projet sont également modifiés pour prendre en charge Active Directory Azure. Les modifications spécifiques que Visual Studio effectue varient selon le type de projet. Pour obtenir des informations détaillées sur la façon dont les projets ASP.NET MVC sont modifiés, consultez [Quels sont les projets survenu – MVC](http://go.microsoft.com/fwlink/p/?LinkID=513809). Pour les projets d’API Web, voir [ce qui s’est passé – projets d’API Web](http://go.microsoft.com/fwlink/p/?LinkId=513810).

##<a name="next-steps"></a>Étapes suivantes

Poser des questions et obtenir de l’aide.

 - [Forum MSDN : Annonce d’Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Documentation d’AD Azure](https://azure.microsoft.com/documentation/services/active-directory/)

 - [Billet de blog : L’introduction à Active Directory Azure](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

