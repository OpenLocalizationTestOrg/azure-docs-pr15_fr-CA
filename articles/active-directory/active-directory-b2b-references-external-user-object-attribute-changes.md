<properties
   pageTitle="Des modifications d’attributs objet utilisateur externe pour l’aperçu de collaboration Azure Active Directory B2B | Microsoft Azure"
   description="Azure B2B Directory Active prend en charge vos relations intersociétés en activant les partenaires commerciaux de manière sélective accéder à vos applications d’entreprise"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Aperçu de collaboration B2B de publicité Azure : modifications d’attribut objet utilisateur externe

Chaque utilisateur dans un annuaire AD Azure est représenté par un objet utilisateur. L’objet utilisateur dans Active Directory Azure subit des modifications d’attributs dans différents stades de la collaboration B2B échanger inviter les flux. L’objet utilisateur qui représente l’utilisateur partenaire dans le répertoire possède des attributs qui changent à échanger les temps, lorsque le partenaire utilisateur clique sur le lien dans le courriel d’invitation. En particulier :

- Les attributs **SignInName** et **AltSecId** sont remplis.
- L’attribut **DisplayName** modifie à partir du nom d’utilisateur Principal (user_fabrikam.com#EXT#@contoso.com) pour le nom d’utilisateur(user@fabrikam.com)

Suivi de ces attributs dans Active Directory Azure peut vous aider à résoudre les problèmes ou non un utilisateur partenaire a échangé leur invitation de collaboration B2B.

## <a name="related-articles"></a>Articles connexes
Parcourir nos autres articles sur Azure AD B2B collaboration :

- [Quelle est la collaboration d’Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
- [Référence de format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Limites d’aperçu en cours](active-directory-b2b-current-preview-limitations.md)
- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
