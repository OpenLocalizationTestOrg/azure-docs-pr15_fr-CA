<properties
   pageTitle="Collaboration d’Active Directory B2B Azure | Microsoft Azure"
   description="Collaboration d’Active Directory B2B Azure permet à des partenaires commerciaux pour accéder à vos applications d’entreprise, avec chacun de leurs utilisateurs représentés par une publicité Azure compte"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="azure-active-directory-b2b-collaboration"></a>Collaboration d’Active Directory B2B Azure

Collaboration d’Active Directory (AD Azure) B2B Azure vous permet d’activer l’accès à vos applications d’entreprise à partir des identités gérés par des partenaires. Vous pouvez créer des relations de la société croisée à inviter et l’autorisation des utilisateurs à partir de sociétés partenaires pour accéder à vos ressources. La complexité est réduite étant donné que chaque entreprise se fédère qu’une seule fois avec Azure Active Directory et chaque utilisateur est représenté par un seul compte Azure AD. Si vos partenaires professionnels gérer leurs comptes dans Active Directory Azure car l’accès est refusé lorsque les utilisateurs partenaire sont terminées à partir de leurs organisations et accès involontaire via l’appartenance dans les répertoires internes ne peut pas la sécurité est augmentée. Pour les partenaires commerciaux qui n’ont pas d’annonce d’Azure, B2B collaboration a une expérience agréable d’abonnement pour fournir les comptes Active Directory Azure à vos partenaires commerciaux.

-   Vos partenaires commerciaux utilisent leurs propres informations de connexion, qui vous libère de la gestion d’un répertoire des partenaires externes et de la nécessité de supprimer l’accès lorsque les utilisateurs quittent l’organisation du partenaire.

-   Vous gérez l’accès à vos applications indépendamment de compte du cycle de vie de votre partenaire commercial. Cela signifie, par exemple, que vous pouvez révoquer l’accès sans avoir à demander le service informatique de votre partenaire commercial rien à faire.

## <a name="capabilities"></a>Fonctionnalités

Collaboration de B2B simplifie la gestion et améliore la sécurité d’accès du partenaire à des ressources de l’entreprise, notamment les applications SaaS par Office 365, la force de vente, les Services Azure et chaque mobile, en nuage et les applications prenant en charge les revendications sur site. Permet de collaboration B2B partenaires gérer leurs propres comptes et les entreprises peuvent appliquer des stratégies de sécurité d’accès du partenaire.

Azure B2B de répertoire Active la collaboration est simple à configurer avec simplifié d’abonnement pour les partenaires de toutes tailles, même s’ils n’ont pas leur propre Azure d’Active Directory via un processus de vérification de messagerie. Il est également facile de mettre à jour des annuaires externes ou par les configurations des partenaires de fédération.

## <a name="b2b-collaboration-process"></a>Processus de collaboration B2B

1. Collaboration de publicité B2B Azure permet à un administrateur de société à inviter et autoriser un ensemble d’utilisateurs externes en téléchargeant un fichier de valeurs séparées par des virgules (CSV) de pas plus de 2000 lignes sur le portail de collaboration B2B.

  ![Boîte de dialogue Téléchargement de fichier CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. Le portail enverra des invitations électroniques à ces utilisateurs externes.

3. L’utilisateur invité va vous connecter à un compte de travail existante avec Microsoft (géré dans Azure AD), soit obtenir un nouveau compte dans Active Directory Azure.

4. Une fois connecté, l’utilisateur sera redirigé vers l’application qui a été partagée avec eux.

Invitations à des adresses de messagerie des consommateurs (par exemple, Gmail ou [*comcast.net*](http://comcast.net/)) ne sont actuellement pas pris en charge.

Pour plus d’informations sur le fonctionne de la collaboration de B2B, regardez [cette vidéo](http://aka.ms/aadshowb2b).

## <a name="next-steps"></a>Étapes suivantes
Parcourir nos autres articles sur la collaboration d’Azure AD B2B.

- [Quelle est la collaboration d’Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
- [Référence de format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limites d’aperçu en cours](active-directory-b2b-current-preview-limitations.md)
- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
