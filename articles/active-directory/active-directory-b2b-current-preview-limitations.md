<properties
   pageTitle="Limitations d’aperçu en cours pour la collaboration d’Azure Active Directory B2B | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-current-preview-limitations"></a>Aperçu de collaboration B2B de publicité Azure : limitations afficher l’aperçu en cours

- Plusieurs facteurs d’authentification (AMF) non pris en charge sur les utilisateurs externes. Par exemple, si Contoso a AMF, mais n’est pas le cas de l’organisation du partenaire, puis utilisateurs de l’organisation du partenaire ne peut pas être accordées AMF par une collaboration B2B.
- Invitations ne sont possibles que via CSV ; des invitations et accès à l’API ne sont pas pris en charge.
- Uniquement les administrateurs globaux Azure AD peut télécharger des fichiers .csv.
- Invitations à des adresses de messagerie consommateur (comme hotmail.com, Gmail.com ou comcast.net) ne sont actuellement pas pris en charge.
- Accès utilisateur externe aux locaux non testés.
- Les utilisateurs externes ne sont pas automatiquement nettoyés lors de la suppression de l’utilisateur réel à partir de son répertoire.
- Invitations aux listes de distribution ne sont pas pris en charge.
- 2 000 enregistrements maximum peut être téléchargé via CSV.

## <a name="related-articles"></a>Articles connexes
Parcourir nos autres articles sur Azure AD B2B collaboration :

- [Quelle est la collaboration d’Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
- [Référence de format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
