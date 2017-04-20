<properties
  pageTitle="Gestion de l’accès aux applications à l’aide d’Active Directory Azure |  Microsoft Azure"
  description="Décrit comment Azure Active Directory permet aux organisations de spécifier les applications à laquelle chaque utilisateur a accès."
  services="active-directory"
  documentationCenter=""
  authors="femila"
  manager="femila"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/13/2016"
  ms.author="femila"/>


# <a name="managing-access-to-apps"></a>Gestion de l’accès aux applications

Gestion de l’accès en continu, évaluation de l’utilisation et le reporting toujours un défi après qu’une application est intégrée au système d’identité de votre organisation. Dans de nombreux cas, les administrateurs informatiques ou support technique doivent prendre un rôle actif en cours dans la gestion de l’accès à vos applications. Parfois, l’affectation est effectuée par une équipe informatique générale ou des division. Souvent, la décision d’attribution doit être déléguée à un décideur, leur agrément avant informatique rend l’affectation.  Autres organisations investissent dans l’intégration avec un automatisée des identités et des accès système de gestion, tels que le contrôle d’accès basée sur les rôles (RBAC) ou de contrôle d’accès basé sur un attribut (fichier ABAC). L’intégration et le développement de la règle ont tendance à être spécialisés et chers. Surveillance ou l’indication de l’approche de gestion est sa propre investissement séparé, coûteuse et complexe.

## <a name="how-does-azure-active-directory-help"></a>Comment aider les Azure Active Directory ?

 Azure AD prend en charge la gestion de l’accès complet pour les applications configurées, permettant aux organisations de réaliser facilement les stratégies de droit d’accès allant de délégation et comprenant de l’administrateur gestion affectation automatique, basée sur l’attribut (fichier ABAC ou RBAC scénarios). Avec AD Azure vous pouvez facilement atteindre des stratégies complexes, la combinaison de plusieurs modèles de gestion pour une application unique et pouvez même réutiliser des règles de gestion entre les applications avec le même public.

 - [Ajout de nouveaux ou d’applications existantes](active-directory-sso-integrate-saas-apps.md)


 Affectation d’applications Azure annonce se concentre sur deux modes d’affectation principale :

- **Affectation** Un administrateur informatique disposant d’autorisations d’administrateur Global de répertoire peut sélectionner des comptes d’utilisateur individuels et leur accorder l’accès à l’application.
- **Affectation de groupe (payée Azure annonce uniquement)** Un administrateur informatique disposant d’autorisations d’administrateur Global de répertoire peut affecter un groupe à l’application. L’accès des utilisateurs spécifiques est déterminé par ils s'sont membres du groupe au moment où qu'ils tentent d’accéder à l’application. En d’autres termes, un administrateur peut créer efficacement d’une règle d’affectation indiquant « tout membre d’un groupe affecté a accès à l’application ». À l’aide de cette option d’affectation, les administrateurs peuvent tirer à partir des options de gestion de groupe AD Azure, y compris [basée sur les attributs des groupes dynamiques](active-directory-accessmanagement-manage-groups.md), des groupes de système externe (par exemple, sur les sites Active Directory ou jour ouvré) ou gérés par l’administrateur ou automatique-service géré par groupes. Un groupe unique peut être affecté facilement à plusieurs applications, veiller à ce que les applications ayant une affinité d’affectation peuvent partager des règles d’affectation, réduisant la complexité de la gestion globale. Veuillez noter ce groupe imbriqué appartenances ne sont pas pris en charge pour l’affectation de groupe aux applications en ce moment.

À l’aide de ces modes d’affectation de deux, les administrateurs peuvent obtenir une approche de gestion souhaitable d’affectation.

Avec AD Azure, l’utilisation et rapports d’affectations est entièrement intégrée, permettant aux administrateurs de créer facilement des rapports sur l’état de l’affectation, erreurs d’affectation et d’utilisation de même.

## <a name="complex-application-assignment-with-azure-ad"></a>Affectation d’une application complexe avec Azure AD

Considérez une application comme la force de vente. Dans de nombreuses organisations, la force de vente est principalement utilisé par les organisations de marketing et de ventes. Souvent, les membres de l’équipe marketing ont très un accès privilégié à la force de vente, alors que l’accès sont limité aux membres de l’équipe de vente. Dans de nombreux cas, une vaste population de travailleurs de l’information ont un accès limité à l’application. Les exceptions à ces règles compliquent. Il est souvent la prérogative des équipes de direction marketing ou de vente à accorder un accès utilisateur ou de changer leurs rôles indépendamment de ces règles génériques.

Avec AD Azure, applications telles que la force de vente peuvent être préconfigurées pour l’authentification unique (SSO) et de provisionnement automatisé. Une fois que l’application est configurée, un administrateur peut prendre l’action unique pour créer et affecter des groupes appropriés. Dans cet exemple, un administrateur peut exécuter les affectations suivantes :

- [Les groupes dynamiques](active-directory-accessmanagement-manage-groups.md) peuvent être définis pour représenter automatiquement de tous les membres des équipes de marketing et de ventes à l’aide d’attributs tels que le service ou rôle :

    - Tous les membres de groupes marketing seraient affectées au rôle « marketing » dans la force de vente

    - Tous les membres de l’équipe de vente groupes devrait être affectés au rôle « vente » dans la force de vente. Une amélioration peut utiliser plusieurs groupes qui représentent des équipes de ventes locales affectées à différents rôles de la force de vente.

- Pour activer le mécanisme d’exception, un groupe de libre service pourrait être créé pour chaque rôle. Par exemple, le groupe de « Exception de commercialisation de force de vente » peut être créé comme un groupe de libre-service. Le groupe peut être affecté au rôle de marketing force de vente et les propriétaires peut être effectuée à l’équipe du leadership de marketing. Les membres de l’équipe du leadership de marketing peuvent ajouter ou supprimer des utilisateurs, définir une stratégie de jointure, approuver ou refuser les demandes des utilisateurs individuels pour rejoindre. Cela est pris en charge via une expérience appropriée du travailleur d’informations qui ne nécessite pas de formation spécialisée pour les propriétaires ou les membres.

Dans ce cas, tous les utilisateurs affectés devrait être configurés automatiquement pour la force de vente, lorsqu’ils sont ajoutés à des groupes différents de que leur affectation de rôle est mis à jour dans une force de vente. Les utilisateurs pourront découvrir et force de vente via le panneau d’accès application Microsoft, les clients Office web, ou même en accédant à leur page de connexion de force de vente d’organisation. Les administrateurs pourront facilement afficher l’état de l’utilisation et l’affectation à l’aide de création de rapports AD Azure.

Les administrateurs peuvent recourir à [accès conditionnel de publicité Azure](active-directory-conditional-access.md) pour définir des stratégies d’accès pour des rôles spécifiques. Ces stratégies peuvent comprendre si l’accès est autorisé en dehors de l’environnement d’entreprise en matière de même plusieurs facteurs d’authentification ou le périphérique pour obtenir des accès dans divers cas.

## <a name="how-can-i-get-started"></a>Comment puis-je commencer ?

Tout d’abord, si vous n’utilisez pas déjà Azure AD et que vous êtes un administrateur informatique :

 - [Essayez-le !](https://azure.microsoft.com/trial/get-started-active-directory/) -Vous pouvez vous inscrire pour un essai gratuit de 30 jours dès aujourd'hui et déployer votre première solution de cloud de moins de 5 minutes à l’aide de ce lien

Les fonctionnalités Active Directory Azure qui permettent le partage de compte :

- [Affectation de groupe](active-directory-accessmanagement-self-service-group-management.md)
- Ajout d’applications à AD Azure
- Mise en route avec affectation
- Forum aux questions sur affectation d’applications
- [Application du tableau de bord/rapports d’utilisation](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>Où puis-je obtenir plus ?

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Protection des applications à accès conditionnel](active-directory-conditional-access.md)
- [Groupe de libre service gestion/SSA](active-directory-accessmanagement-self-service-group-management.md)
