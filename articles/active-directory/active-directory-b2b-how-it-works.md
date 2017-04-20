<properties
   pageTitle="Aperçu de collaboration B2B de publicité Azure : fonctionnement | Microsoft Azure"
   description="Décrit comment Azure Active Directory B2B collaboration prend en charge vos relations intersociétés en activant les partenaires commerciaux de manière sélective accéder à vos applications d’entreprise"
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

# <a name="azure-ad-b2b-collaboration-preview-how-it-works"></a>Aperçu de collaboration B2B de publicité Azure : fonctionnement
La collaboration AD B2B Azure est basé sur une invitation et échanger le modèle. Vous fournissez les adresses électroniques des parties que vous souhaitez utiliser, ainsi que les applications que vous souhaitez utiliser. Annonce Azure les envoie une invitation par courrier électronique avec un lien. L’utilisateur partenaire, suit le lien et est invité à vous connecter en utilisant leur compte Azure AD ou un signe pour une nouvelle annonce Azure compte.

1. Votre administrateur invite les utilisateurs partenaire en téléchargeant [un fichier .csv structuré](active-directory-b2b-references-csv-file-format.md) via le portail Azure.
2. Le portail envoie invite des e-mails aux utilisateurs de ces partenaires.
3. Les utilisateurs partenaire cliquez sur le lien dans le message électronique et êtes invités à ouvrir une session à l’aide de leurs informations d’identification de travail (si elles sont déjà dans Azure AD), ou pour vous inscrire en tant qu’un utilisateur de collaboration Azure AD B2B.
4. Les utilisateurs partenaire sont redirigés vers l’application qu’ils ont été invités, où ils ont accès.

## <a name="directory-operations"></a>Opérations d’annuaire
Les utilisateurs partenaire existent dans votre annonce Azure en tant qu’utilisateurs externes. Cela signifie que votre administrateur peut configurer les licences, affecter l’appartenance au groupe et plus accorder l’accès à des applications d’entreprise via le portail Azure ou à l’aide de PowerShell d’Azure comme pour les utilisateurs de votre société.

Lors de la publicité payée Azure abonnement (Basic ou Premium) n’est pas nécessaire d’utiliser Azure AD B2B, les locataires ayant un payé abonnement Azure AD (Basic ou Premium) reçoivent les avantages supplémentaires suivants :

 - Administrateurs peuvent affecter des groupes aux applications, prévoyant la simplification de la gestion de l’accès de l’utilisateur invité.
 - Locataire Admin de personnalisation est utilisé pour marquer les e-mails d’invitation et expérience de remboursement, fournir plus de contexte aux invités des utilisateurs partenaire.

## <a name="related-articles"></a>Articles connexes
 Parcourir nos autres articles sur la collaboration d’Azure AD B2B

 - [Quelle est la collaboration d’Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
 - [Référence de format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
 - [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
 - [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Limites d’aperçu en cours](active-directory-b2b-current-preview-limitations.md)
 - [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
