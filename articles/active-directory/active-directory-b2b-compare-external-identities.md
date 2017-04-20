<properties
   pageTitle="Comparaison des fonctions de gestion des identités externes à l’aide de Active Directory de Azure | Microsoft Azure"
   description="Compare la collaboration d’Azure Active Directory B2B, B2C et mutualisée App de prise en charge de l’authentification et l’autorisation des identités externes"
   services="active-directory"
   documentationCenter="" 
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/24/2016"
   ms.author="asuthar"/>

# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>Comparaison des fonctionnalités de gestion des identités externes à l’aide d’Azure Active Directory

En plus de la gestion de l’accès de mobilité pour les applications SaaS, Azure Active Directory (AD Azure) peut aider votre organisation à partager des ressources avec des partenaires commerciaux et fournir des applications à des entreprises et des consommateurs.

## <a name="developing-applications-for-businesses"></a>Développement d’applications pour entreprises

Fournir un service ou une application, telle qu’un service de liste de paie, pour les entreprises ? AD Azure fournit la plate-forme de l’identité qui vous permet de créer des applications qui s’intègrent parfaitement avec des millions d’entreprises qui ont déjà configurés Azure AD dans le cadre du déploiement de Office 365 ou d’autres services de l’entreprise.

**Exemple :** Un distributeur PHARMACEUTIQUE fournit des fournitures médicales et des systèmes d’information pour le secteur de la santé. Il faut champ une application analytique cabinets médicaux et clients voulus pour gérer leur propre identité. Cette entreprise a choisi AD Azure comme la plate-forme d’identité pour l’application de gestion de leurs pratiques, fournir des identités AD Azure à leurs clients signe des lorsque cela est nécessaire. Pour plus d’informations, consultez le [guide du développeur d’Azure Active Directory](active-directory-developers-guide.md).

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>L’activation de l’accès des partenaires commerciaux aux ressources d’entreprise

Avez-vous des partenaires commerciaux ou autres personnes à l’extérieur de votre entreprise qui ont besoin d’accéder aux ressources de votre entreprise, par exemple un site SharePoint ou votre système ERP ? AD Azure permet d’administrateurs d’accorder à des utilisateurs externes (qui peuvent ou peut ne pas existent dans Active Directory Azure) un seul signe sur l’accès aux applications d’entreprise. Cela améliore la sécurité que les utilisateurs perdent l’accès lorsqu’ils quittent l’organisation partenaire, pendant que vous contrôlez les stratégies d’accès au sein de votre organisation. Cela simplifie également l’administration que vous n’avez pas besoin de gérer un partenaire externe répertoire ou par des relations de fédération partenaire.

**Exemple :** Une société d’imagerie fournit aux détaillants d’image des services photo et opère le plus grand réseau de vente au détail de kiosques d’impression. Ils ont choisi AD Azure pour activer des milliers d’utilisateurs à leurs partenaires commerciaux de vente au détail à utiliser leurs propres informations d’identification pour télécharger le dernier matériel marketing partenaire et réorganiser des blocs à partir du fournisseur de la société extranet de retouche photo. Pour plus d’informations, consultez [Azure AD B2B collaboration](active-directory-b2b-what-is-azure-ad-b2b.md).

## <a name="developing-applications-for-consumers"></a>Développement d’applications pour les consommateurs

Vous avez besoin en toute sécurité et à moindre coût publier des applications en ligne, par exemple une devanture de magasin de vente au détail, à des millions de consommateurs ? AD Azure fournit une plate-forme sociale et signe dans nom d’utilisateur/mot de passe, inscription libre-service marque et réinitialisation de mot de passe libre-service pour les consommateurs de votre application. Cela augmente la commodité pour vos consommateurs tout en réduisant la charge sur vos développeurs.

**Exemple :** Le \#1 sports franchise dans le monde permettant de discuter avec ses ventilateurs 450 millions. Pour ce faire, ils construit une application mobile à l’aide d’Azure AD pour le stockage de l’authentification et de profil utilisateur. Ventilateurs obtenir l’enregistrement simplifié et à la connexion à l’aide de comptes sociaux tels que Facebook, ou ils peuvent utiliser le nom d’utilisateur/mots de passe traditionnels pour une expérience transparente sur iOS, Android et Windows téléphones. Sur la liste établie plateforme Azure publicitaire réduits code personnalisé tout en donnant la franchise personnalisé pour soulager les préoccupations concernant les violations de données, sécurité et d’évolutivité et de personnalisation. Pour plus d’informations, consultez [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).

## <a name="comparison-of-azure-ad-capabilities"></a>Comparaison des fonctionnalités d’Active Directory Azure

Chacun des scénarios déjà présentés dans cet article est adressée par les fonctions dans l’annonce d’Azure. Ce tableau devrait aider à clarifier les capacités sont plus importantes pour vous :

| **Tenez compte de ce produit...**       | [Application de SaaS mutualisée AD Azure](active-directory-developers-guide.md)    | [Collaboration de publicité B2B Azure](active-directory-b2b-what-is-azure-ad-b2b.md)        | [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)                |
|-----------------------|-------------------------|----------------------------|------------------------|
| **Si je dois fournir...** | un service aux entreprises | accès du partenaire à mes applications  | un service aux consommateurs |
| **Et je suis semblable à...**  | Distributeur de Pharma      | Société d’imagerie            | Franchise de sports       |
| **Déploiement d’une application pour...**  | Pratiques de gestion     | Fournisseur extranet          | Fans de football            |
| **Ciblage...**        | Bureaux du médecin        | Partenaires agréés | Toute personne ayant un e-mail      |
| **Lors de l’accès...**      | Autorisations d’administrateur de client | Mes invitations admin           | Le consommateur inscrit      |
