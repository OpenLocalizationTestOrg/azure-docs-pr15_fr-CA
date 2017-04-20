<properties
    pageTitle="Gestion des Applications avec Azure Active Directory | Microsoft Azure"
    description="Cet article les avantages de l’intégration d’Azure Active Directory des locaux, nuage et les applications SaaS."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/10/2016"
      ms.author="markvi"/>

# <a name="managing-applications-with-azure-active-directory"></a>Gestion des Applications avec Azure Active Directory

Au-delà du workflow en cours ou le contenu, les entreprises ont deux exigences de base pour toutes les applications :

1. Pour augmenter la productivité, les applications doivent être faciles à découvrir et à accéder

2. Pour activer la sécurité et gouvernance, l’organisation a besoin de contrôle et supervision sur qui peut et effectivement accède à chaque application

Dans le monde des applications du nuage ce peut être réalisé au mieux à l’aide de l’identité au contrôle «*qui est autorisé à faire quoi*».

Dans la terminologie de l’informatique :

- *Qui* est connu en tant qu' *identité* - la gestion des utilisateurs et des groupes

- *Quel* est connu sous le nom de *Gestion des accès* – la gestion de l’accès à des ressources protégées

Les deux composants ensemble sont appelés *des identités et des accès gestion (IAM)*, qui est défini par le groupe [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) en tant que «*la discipline de sécurité qui permet les bonnes personnes accéder aux ressources à droite heures pour les bonnes raisons*».

OK, alors, quel est le problème ? Si IAM n’est *pas géré* dans un seul endroit avec une solution intégrée :

- Les administrateurs d’identité doivent créer individuellement et de mettre à jour les comptes d’utilisateurs dans toutes les applications séparément, une activité redondante et prend beaucoup de temps.

- Les utilisateurs doivent mémoriser plusieurs informations d’identification pour accéder aux applications qu’ils ont besoin pour travailler avec. Par conséquent, les utilisateurs ont tendance à noter leurs mots de passe ou utiliser d’autres solutions de gestion de mot de passe qui introduit d’autres risques de sécurité des données.

- Les activités redondantes, beaucoup de temps réduisent les utilisateurs et les administrateurs travaillent sur les activités de l’entreprise qui augmentent la ligne du bas de votre entreprise.

Par conséquent, ce qui empêche généralement aux entreprises d’adopter des solutions IAM intégrées ?

- Des solutions plus techniques sont basées sur des plates-formes logicielles qui doivent être déployés et adapté par chaque organisation dans leurs propres applications.

- Applications en nuage sont souvent adoptées à un taux plus élevé que l’organisation informatique peut s’intégrer à des solutions IAM existantes.

- Sécurité et surveillance des outils nécessitent une personnalisation supplémentaire et l’intégration pour obtenir des scénarios E2E complètes.

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory intégré aux applications

Azure Active Directory est complète des identités de Microsoft comme une solution de Service (IDaaS) qui :

- Permet l’IAM sous la forme d’un service en nuage 

- Fournit la gestion des accès central, authentification unique (SSO) et de reporting 

- Gestion des accès intégré prend en charge des [milliers d’applications](https://azure.microsoft.com/marketplace/active-directory/) dans la galerie d’application, y compris la force de vente, Google Apps, case, Concur et bien plus encore. 


Avec Azure Active Directory, toutes les applications que vous publiez pour vos partenaires et de clients (entreprise ou particuliers) ont la même identité et accéder aux fonctions de gestion.<br> Vous pouvez ainsi réduire considérablement vos coûts d’exploitation.

Que se passe-t-il si vous avez besoin d’implémenter une application qui ne figure pas encore dans la bibliothèque de l’application ? Alors que c’est un peu plus de temps que la configuration de l’authentification unique pour les applications à partir de la bibliothèque de l’application, AD Azure fournit un Assistant qui vous aide à la configuration.

La valeur de publicité Azure au-delà des applications en nuage « juste ». Vous pouvez également l’utiliser avec des applications sur site en fournissant un accès distant sécurisé. Accès à distance sécurisé, vous pouvez éliminer le besoin de réseaux privés virtuels ou d’autres implémentations de gestion d’accès à distance traditionnel.

Grâce à la gestion des accès central et l’authentification unique (SSO) pour toutes vos applications, AD Azure fournit la solution pour les problèmes de sécurité et la productivité de données principale.

- Les utilisateurs peuvent accéder à plusieurs applications avec une seule ouverture de session en donnant plus de temps aux revenus génération ou d’entreprise activités d’opérations réalisées.

- Administrateurs d’identité peuvent gérer l’accès aux applications à un seul endroit.

L’avantage pour l’utilisateur et de votre société est évident. Prenons l’examiner de plus près les avantages pour un administrateur de l’identité et de l’organisation.

## <a name="integrated-application-benefits"></a>Avantages de l’application intégrée

Le processus d’authentification comporte deux étapes :

- Authentification, le processus de validation de l’identité de l’utilisateur.

- Autorisation, la décision d’activer ou bloquer l’accès à une ressource avec une stratégie d’accès.

Lorsque vous utilisez AD Azure pour gérer des applications et activer l’authentification unique :

- L’authentification est effectuée (par exemple publicité) sur le site de l’utilisateur ou compte Azure AD.

- L’autorisation s’exécute sur la stratégie AD Azure affectation et protection garantissant l’expérience utilisateur cohérente et qui vous permet d’ajouter une affectation, les emplacements et les conditions de l’AMF sur n’importe quelle application, indépendamment de ses capacités internes.

Il est important de comprendre que la façon dont l’autorisation est activée dans l’application cible dépend de comment l’application a été intégrée avec AD Azure.

- **Les applications intégrées préalablement par le fournisseur de services** Comme Office 365 et Azure, ce sont des applications créées directement sur les annonces Azure et s’appuyer dessus pour leurs fonctionnalités complètes de gestion des identités et des accès. L’accès à ces applications est activé via les informations d’annuaire et d’émission de jeton.

- **Les applications intégrées préalable par Microsoft et les applications personnalisées** Ce sont des applications de cloud indépendant qui s’appuient sur un répertoire d’application interne et peuvent fonctionner indépendamment des annonces Azure. L’accès à ces applications est activé à l’aide d’une application spécifique d’informations d’identification mappées à un compte de l’application. Selon les fonctionnalités de l’application, les informations d’identification peuvent être un jeton de fédération ou le nom d’utilisateur et le mot de passe d’un compte qui a été précédemment configuré dans l’application.

- **Applications sur site** Les applications publiées via le proxy d’application Azure AD principalement permettant l’accès aux applications sur site. Ces applications reposent sur un central dans l’annuaire de site comme Windows Server Active Directory. L’accès à ces applications est activé en déclenchant le proxy pour fournir le contenu de l’application pour l’utilisateur final tout en respectant la nécessité d’ouverture de session locale.

Par exemple, si un utilisateur joint à votre organisation, vous devez créer un compte pour l’utilisateur dans AD Azure pour les opérations d’ouverture de session principales. Si cet utilisateur doit accéder à une application managée, tels que la force de vente, vous devez également créer un compte d’utilisateur dans la force de vente et le lier au compte Azure pour utiliser l’authentification unique. Lorsque l’utilisateur quitte votre organisation, il est recommandé de supprimer le compte Azure AD et tous les comptes de contrepartie dans la page IAM stocke les applications que l’utilisateur a eu accès à.

## <a name="access-detection"></a>Détection à l’accès

Dans les entreprises modernes, les départements informatiques ne sont souvent pas connaissance de toutes les applications de cloud qui sont utilisées. Conjointement avec la découverte des applications de Cloud, AD Azure fournit une solution pour détecter ces applications.

## <a name="account-management"></a>Gestion des comptes

En règle générale, la gestion des comptes dans les différentes applications sont un processus manuel effectué par informatique ou du support technique personnel dans l’organisation. Annonce Azure entièrement automatisée de gestion des comptes sur toutes les applications de fournisseur intégré de services et les applications pré-intégrées par Microsoft prenant en charge la mise en service de l’utilisateur automatisées ou SAML JIT.

## <a name="automated-user-provisioning"></a>La mise en service automatique d’utilisateur

Certaines applications fournissent des interfaces d’automatisation pour la création et suppression ou désactivation de comptes. Si un fournisseur prend en charge une telle interface, il est exploité par AD Azure. Cela permet de réduire vos coûts d’exploitation, car les tâches administratives sont effectuées automatiquement et améliore la sécurité de votre environnement, car elle diminue les risques d’accès non autorisé.

## <a name="access-management"></a>Gestion des accès

À l’aide d’Active Directory Azure vous pouvez gérer les accès aux applications à l’aide des ou par des affectations de règle. Vous pouvez également déléguer l’accès de gestion aux personnes appropriées dans l’organisation assurant le meilleur contrôle et allège la charge sur le support technique.

## <a name="on-premises-applications"></a>Applications sur site

Intégré dans l’application proxy vous permet de publier vos applications sur site pour vos utilisateurs cohérente accès expérience avec l’application en nuage modernes et les avantages des fonctions de surveillance, le reporting et la sécurité Azure AD.

## <a name="reporting-and-monitoring"></a>Reporting et surveillance

AD Azure fournit déjà intégrées de création de rapports et de surveillance des fonctionnalités qui vous permettent de savoir qui a accès aux applications et lorsqu’ils réellement utilisées.

## <a name="related-capabilities"></a>Fonctions connexes

Avec AD Azure, vous pouvez sécuriser vos applications avec les stratégies d’accès granulaire et pré-intégrées AMF. Pour en savoir plus sur Azure AMF voir [Azure AMF](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Mise en route

Pour commencer à intégrer des applications Azure AD, examinez le [Guide d’intégration Azure d’Active Directory avec les applications de mise en route](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Voir aussi

[Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
