<properties
   pageTitle="Liste de votre application dans la galerie des applications Azure Active Directory"
   description="Comment répertorier une application qui prend en charge l’ouverture de session unique dans la galerie d’Azure Active Directory | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>


# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Liste de votre application dans la galerie des applications Azure Active Directory

Pour afficher une application qui prend en charge de l’authentification unique avec Azure Active Directory dans la [Galerie d’Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), l’application doit tout d’abord implémenter l’un des modes d’intégration suivants :

* **OpenID connecter** - intégration directe avec Azure AD à l’aide de OpenID se connecter pour l’authentification et l’API de consentement AD Azure pour la configuration. Si vous commencez une intégration et que votre application ne prend pas en charge SAML, c’est le mode recommandé.

* **SAML** – déjà votre application a la possibilité de configurer les fournisseurs d’identité tiers utilisant le protocole SAML.

Exigences relatives au référencement pour chaque mode sont inférieurs.

##<a name="openid-connect-integration"></a>OpenID connecter intégration

Pour intégrer votre application Azure AD, en suivant les [instructions du développeur](active-directory-authentication-scenarios.md). Répondez aux questions ci-dessous, puis envoyer à waadpartners@microsoft.com.

* Fournir des informations d’identification pour un compte ou un client de test avec votre application utilisable par l’équipe AD Azure pour tester l’intégration.  

* Fournir des instructions sur la façon dont l’équipe AD Azure connecter et connecter une instance d’Azure annonce à votre application en utilisant le [AD Azure consentement framework](active-directory-integrating-applications.md#overview-of-the-consent-framework). 

* Fournir des instructions supplémentaires requises pour l’équipe AD Azure tester single sign-on avec votre application. 

* Fournir les informations ci-dessous :

> Nom de la société :
> 
> Site Web d’entreprise :
> 
> Nom de l’application :
> 
> Description de l’application (256 caractères maximum) :
> 
> Site Web application (information) :
> 
> Site Web du Support technique application ou informations de Contact :
> 
> ID de client de l’application, comme indiqué dans les détails de l’application à https://manage.windowsazure.com :
> 
> URL de l’application d’abonnement dans lequel les clients vont voir se pour inscrire aux et et/ou l’application d’achat :
> 
> Sélectionnez jusqu'à trois catégories à votre application pour figurer sous (pour voir l’Azure Active Directory Marketplace de catégories disponibles) :
> 
> Attacher l’Application petite icône (fichier PNG, 45px par 45px, couleur d’arrière-plan unie) :
> 
> Attacher l’Application grande icône (fichier PNG, 215px par 215px, couleur d’arrière-plan unie) :
> 
> Joindre le Logo de l’Application (fichier PNG, 150px par 122px, couleur d’arrière-plan transparente) :

##<a name="saml-integration"></a>Intégration de SAML

N’importe quelle application prenant en charge SAML 2.0 peut être intégrée directement avec un locataire Azure AD à l’aide de [ces instructions pour ajouter une application personnalisée](active-directory-saas-custom-apps.md). Une fois que vous avez testé que l’intégration de votre application fonctionne avec AD Azure, envoyer les informations suivantes à <waadpartners@microsoft.com>.

* Fournir des informations d’identification pour un compte ou un client de test avec votre application utilisable par l’équipe AD Azure pour tester l’intégration.  

* Fournir les valeurs de (service de consommateur d’assertion) SAML session URL, URL d’émetteur (ID de l’entité) et les URL de réponse pour votre application, comme décrit [ici](active-directory-saas-custom-apps.md). Si vous fournissez généralement ces valeurs en tant que partie d’un fichier de métadonnées SAML, puis envoyez qui ainsi.

* Décrivez brièvement comment configurer AD Azure comme fournisseur d’identité dans votre application à l’aide de SAML 2.0. Si votre application prend en charge la configuration AD Azure comme fournisseur d’identité via un portail d’administration de libre service, puis vérifiez que les informations d’identification fournies ci-dessus incluent la possibilité de le configurer.

* Fournir les informations ci-dessous :

> Nom de la société :
> 
> Site Web d’entreprise :
> 
> Nom de l’application :
> 
> Description de l’application (256 caractères maximum) :
> 
> Site Web application (information) :
> 
> Site Web du Support technique application ou informations de Contact :
> 
> URL de l’application d’abonnement dans lequel les clients vont voir se pour inscrire aux et et/ou l’application d’achat :
> 
> Choisissez jusqu'à trois catégories à votre application pour figurer sous (pour les catégories disponibles, consultez l' [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))) :
> 
> Attacher l’Application petite icône (fichier PNG, 45px par 45px, couleur d’arrière-plan unie) :
> 
> Attacher l’Application grande icône (fichier PNG, 215px par 215px, couleur d’arrière-plan unie) :
> 
> Joindre le Logo de l’Application (fichier PNG, 150px par 122px, couleur d’arrière-plan transparente) :
