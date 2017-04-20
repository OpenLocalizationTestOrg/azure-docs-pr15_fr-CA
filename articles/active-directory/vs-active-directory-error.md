<properties 
    pageTitle="Erreur lors de la détection de l’authentification" 
    description="L’Assistant de connexion active directory a détecté un type d’authentification incompatibles" 
    services="active-directory" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>
  
<tags 
    ms.service="active-directory" 
    ms.workload="web" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tarcher"/>

# <a name="error-during-authentication-detection"></a>Erreur lors de la détection de l’authentification

Lors de la détection de code précédent de l’authentification, l’Assistant a détecté un type d’authentification incompatibles.   

##<a name="what-is-being-checked"></a>Ce qui est en cours de vérification ?

**Remarque :** Afin de détecter correctement le code d’authentification précédente dans un projet, le projet doit être généré.  Si vous avez rencontré cette erreur et que vous n’avez pas un code d’authentification précédent dans votre projet, régénérez et réessayez.

###<a name="project-types"></a>Types de projets

L’Assistant vérifie le type de projet que vous développez et il peut injecter la logique d’authentification de droite dans le projet.  S’il existe un contrôleur qui dérive de `ApiController` dans le projet, le projet sera considéré comme un projet WebAPI.  S’il y a uniquement les contrôleurs qui dérivent de `MVC.Controller` dans le projet, le projet sera considéré comme un projet MVC.  Rien n’est pas pris en charge par l’Assistant.  Les projets Web Forms ne sont pas actuellement pris en charge.

###<a name="compatible-authentication-code"></a>Code d’authentification compatibles

L’Assistant vérifie également les paramètres d’authentification qui ont été précédemment configurés avec l’Assistant ou sont compatibles avec l’Assistant.  Si tous les paramètres sont présents, il est considéré comme un cas de ré-entrant et l’Assistant s’ouvre et affiche les paramètres des.  Si seulement certains paramètres sont présents, il est considéré comme un cas d’erreur.

Dans un projet MVC, l’Assistant vérifie les paramètres suivants, qui résultent de l’utilisation précédente de l’Assistant :

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

En outre, l’Assistant vérifie les paramètres suivants dans un projet Web API, qui résultent de l’utilisation précédente de l’Assistant :

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

###<a name="incompatible-authentication-code"></a>Code d’authentification incompatibles

Enfin, l’Assistant tente de détecter les versions de code d’authentification qui ont été configurées avec des versions antérieures de Visual Studio. Si vous avez reçu cette erreur, cela signifie que votre projet contient un type d’authentification incompatibles. L’Assistant détecte les types suivants d’authentification à partir de versions antérieures de Visual Studio :

* Authentification Windows 
* Comptes d’utilisateur individuels 
* Comptes d’organisation 
 

Pour détecter l’authentification Windows dans un projet MVC, l’Assistant recherche la `authentication` élément de votre fichier **web.config** .

<pre>
    &lt;configuration de&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;mode d’authentification = « Windows » /&gt;</span>
        &lt;/System.Web&gt;
    &lt;configuration&gt;
</pre>

Pour détecter l’authentification Windows dans un projet Web API, l’Assistant recherche la `IISExpressWindowsAuthentication` élément dans le fichier **.csproj** du projet :

<pre>
    &lt;Projet&gt;
&lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;activé&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup > &lt;/projet        &gt;
</pre>

Pour détecter l’authentification des comptes d’utilisateur individuels, l’Assistant recherche pour l’élément de package à partir de votre fichier **Packages.config** .

<pre>
    &lt;les packages&gt;
        <span style="background-color: yellow">&lt;id="Microsoft.AspNet.Identity.EntityFramework du package » version = « 2.1.0 » targetFramework = « net45 » /&gt;</span>
    &lt;/ensembles&gt;
</pre>

Pour détecter une ancienne forme d’authentification de compte d’organisation, l’Assistant recherche l’élément suivant dans **web.config**:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;Ajouter clé = « ida : domaine » value = "*** » /&gt;</span>
        &lt;/appSettings&gt;
    &lt;configuration&gt;
</pre>

Pour modifier le type d’authentification, supprimer le type d’authentification incompatibles et réexécutez l’Assistant.

Pour plus d’informations, consultez [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).