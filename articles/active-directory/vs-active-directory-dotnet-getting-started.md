<properties 
    pageTitle="Commencer avec Azure Active Directory et les services de Visual Studio connecté (projets MVC) | Microsoft Azure" 
    description="Comment commencer à utiliser Azure Active Directory dans les projets MVC après que la connexion ou la création d’une annonce Azure à l’aide de Visual Studio de services connectés" 
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

# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Mise en route avec Azure Active Directory et Visual Studio connecté services (projets MVC)

> [AZURE.SELECTOR]
> - [Mise en route](vs-active-directory-dotnet-getting-started.md)
> - [Que s'est-il passé](vs-active-directory-dotnet-what-happened.md)
 
##<a name="requiring-authentication-to-access-controllers"></a>Demande d’authentification à des contrôleurs d’accès 

Tous les contrôleurs de votre projet étaient accompagnées de l’attribut **Autoriser** . Cet attribut nécessite que l’utilisateur d’être authentifié avant d’accéder à ces contrôleurs. Pour permettre au contrôleur d’être accessibles de manière anonyme, supprimez cet attribut à partir du contrôleur. Si vous souhaitez définir les autorisations à un niveau plus granulaire, appliquez l’attribut à chaque méthode qui nécessite une autorisation au lieu de l’appliquer à la classe de contrôleur.
 
##<a name="adding-signin--signout-controls"></a>Ajout d’ouverture de session / SignOut contrôle 

Pour ajouter un les contrôles d’ouverture de session/SignOut à votre affichage, vous pouvez utiliser la vue partielle **_LoginPartial.cshtml** pour ajouter des fonctionnalités à l’un affichages de votre. Voici un exemple de la fonctionnalité ajoutée à l’affichage standard **_Layout.cshtml** . (Notez le dernier élément de la balise div avec barre de navigation-réduction de classe) :

<pre>
    &lt;! DOCTYPE html&gt; 
&lt;html&gt; 
&lt;head&gt; 
&lt;meta charset="utf-8" /&gt; 
&lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
&lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
@Styles.Render("~/Content/css") @Scripts.Render("~/bundles/modernizr") &lt;/head&gt; 
&lt;body&gt; 
&lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
&lt;div class="container"&gt; 
&lt;div class="navbar-header"&gt; 
&lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;/button&gt; 
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) &lt;/div&gt; 
&lt;div class="navbar-collapse collapse"&gt; 
&lt;ul class="nav navbar-nav"&gt; 
&lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
&lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/ div&gt; 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;div class="container body-content"&gt; 
@RenderBody() &lt;hr /&gt; 
&lt;footer&gt; 
&lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
&lt;/footer&gt; 
&lt;/div&gt; 
@Scripts.Render("~/bundles/jquery") @Scripts.Render("~/bundles/bootstrap") @RenderSection("scripts", required: false) &lt;/body&gt; 
&lt;/html                                                                                                                                                                                                                                                                                                                                                                                                                                                           &gt;
</pre>

[En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 
