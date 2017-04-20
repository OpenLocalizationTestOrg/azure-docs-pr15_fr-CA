<properties
    pageTitle="Qu’est-il advenu de mon projet MVC (Visual Studio Azure Active Directory connecté service) | Microsoft Azure "
    description="Décrit ce qui arrive à votre projet MVC lorsque vous vous connectez à Azure AD à l’aide des services de connexion de Visual Studio"
    services="active-directory"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Qu’est-il advenu de mon projet MVC (Visual Studio Azure Active Directory connecté le service) ?

> [AZURE.SELECTOR]
> - [Mise en route](vs-active-directory-dotnet-getting-started.md)
> - [Que s'est-il passé](vs-active-directory-dotnet-what-happened.md)



## <a name="references-have-been-added"></a>Références ont été ajoutées.

### <a name="nuget-package-references"></a>Références de package NuGet

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>Références .NET

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>Code a été ajouté.

### <a name="code-files-were-added-to-your-project"></a>Les fichiers de code ont été ajoutés à votre projet

Une classe de démarrage de l’authentification, **App_Start/Startup.Auth.cs** a été ajouté à votre projet contenant la logique de démarrage pour l’authentification Active Directory Azure. En outre, une classe de contrôleur, Controllers/AccountController.cs a été ajoutée qui contient des méthodes **SignIn()** et **SignOut()** . Enfin, une vue partielle, **Views/Shared/_LoginPartial.cshtml** a été ajouté, contenant un lien d’action pour l’ouverture de session/SignOut.

### <a name="startup-code-was-added-to-your-project"></a>Code de démarrage a été ajouté à votre projet

Si vous avez déjà une classe de démarrage de votre projet, la méthode de **Configuration** a été mis à jour pour inclure un appel à **ConfigureAuth(app)**. Dans le cas contraire, une classe de démarrage a été ajoutée à votre projet.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>Votre fichier app.config ou le web.config a les nouvelles valeurs de configuration

Les entrées de configuration suivantes ont été ajoutées.


    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>Une application Azure Active Directory (AD) a été créée.
Une Application Azure d’Active Directory a été créée dans le répertoire que vous avez sélectionné dans l’Assistant.

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Si vous *désactivez l’authentification de comptes d’utilisateur individuels*sélectionnées, les modifications supplémentaires apportées à mon projet ?
Références de package NuGet ont été supprimées, et les fichiers ont été supprimés et sauvegardées. Selon l’état de votre projet, vous devrez supprimer des références supplémentaires ou des fichiers manuellement, ou modifier le code si nécessaire.

### <a name="nuget-package-references-removed-for-those-present"></a>Références de package NuGet supprimés (pour ceux présents)

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Fichiers de code sauvegardé et supprimé (pour ceux présents)

Chacun des fichiers suivants a été sauvegardé et supprimé du projet. Fichiers de sauvegarde sont situés dans un dossier de « Sauvegarde » à la racine du répertoire du projet.

- **App_Start\IdentityConfig.cs**
- **Controllers\ManageController.cs**
- **Models\IdentityModels.cs**
- **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>Fichiers de code sauvegardés (pour ceux présents)

Chacun des fichiers suivants ont été sauvegardée avant d’être remplacé. Fichiers de sauvegarde sont situés dans un dossier de « Sauvegarde » à la racine du répertoire du projet.

- **Startup.cs**
- **App_Start\Startup.auth.cs**
- **Controllers\AccountController.cs**
- **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Si j’ai vérifié les *données de l’annuaire en lecture*, les modifications supplémentaires apportées à mon projet ?

Des références supplémentaires ont été ajoutés.

###<a name="additional-nuget-package-references"></a>Références supplémentaires du package NuGet

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###<a name="additional-net-references"></a>Autres références .NET

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###<a name="additional-code-files-were-added-to-your-project"></a>Les fichiers de Code supplémentaires ont été ajoutés à votre projet

Deux fichiers ont été ajoutés pour prendre en charge la mise en cache de jeton : **Models\ADALTokenCache.cs** et **Models\ApplicationDbContext.cs**.  Un affichage et un contrôleur supplémentaire ont été ajoutées pour illustrer l’accès aux informations de profil utilisateur à l’aide des API de graphique Azure.  Ces fichiers sont **Controllers\UserProfileController.cs** et **Views\UserProfile\Index.cshtml**.

###<a name="additional-startup-code-was-added-to-your-project"></a>Code de démarrage supplémentaire a été ajouté à votre projet

Dans le fichier **startup.auth.cs** , un nouvel objet **OpenIdConnectAuthenticationNotifications** a été ajouté au membre de la **OpenIdConnectAuthenticationOptions**des **Notifications** .  Cela consiste à activer le code OAuth reçu et l’échanger contre un jeton d’accès.

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Des modifications ont été apportées à votre fichier app.config ou le web.config

Les entrées de configuration supplémentaires suivantes ont été ajoutées.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

Les sections de configuration suivantes et la chaîne de connexion ont été ajoutés.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


###<a name="your-azure-active-directory-app-was-updated"></a>Votre application de répertoire actif Azure a été mis à jour.
Votre application de répertoire actif Azure a été mis à jour pour inclure l’autorisation de *lire les données de répertoire* et une clé supplémentaire a été créée, qui a été ensuite utilisé comme l' *ida : ClientSecret* dans le fichier **web.config** .

[En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
