<properties 
    pageTitle="Comment déboguer basée sur SAML SSO pour les applications dans Active Directory de Azure | Microsoft Azure" 
    description="Découvrez comment déboguer SAML-en fonction de l’authentification unique pour applications dans Azure Active Directory " 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Comment faire pour déboguer SAML-en fonction de l’authentification unique pour applications dans Azure Active Directory

Lorsque vous déboguez une intégration d’application basée sur SAML, il est souvent utile d’utiliser un outil tel que [Fiddler](http://www.telerik.com/fiddler) pour voir la requête SAML, la réponse SAML et le jeton SAML réel qui est délivré à l’application. En examinant le jeton SAML, vous pouvez vous assurer que tous les attributs requis, le nom d’utilisateur dans l’objet SAML et l’URI de l’émetteur sont traversant comme prévu.

![][1]

La réponse à partir d’AD Azure qui contient le jeton SAML est généralement celui qui se produit après qu’un HTTP 302 redirection à partir de https://login.windows.net et qu’il est envoyé à **l’URL de la réponse** configuré de l’application. 
 
Vous pouvez afficher le jeton SAML en sélectionnant cette ligne, puis en sélectionnant la **inspecteurs > WebForms** dans le panneau de droit. À partir de là, la valeur **SAMLResponse** d’avec le bouton droit et sélectionnez **Envoyer à TextWizard**. Puis sélectionnez **Dans Base64** dans le menu de **transformation** pour le jeton de décoder et afficher son contenu.
 
**Remarque**: pour afficher le contenu de cette demande HTTP, Fiddler peut vous inviter à configurer le décryptage du trafic HTTPS, que vous devez faire.

## <a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Configuration de l’authentification unique pour les applications qui ne sont pas dans la galerie des applications Azure Active Directory](active-directory-saas-custom-apps.md)
- [Comment personnaliser les affirmations émises dans le jeton SAML pour des applications](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png
