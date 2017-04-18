<properties
    pageTitle="Automatique utilisateur d’application SaaS provisionnement dans Azure annonce | Microsoft Azure"
    description="Une introduction à l’utilisation d’Active Directory Azure pour configurer automatiquement, mettre hors service et de mettre constamment à jour les comptes d’utilisateurs sur plusieurs applications SaaS de tiers."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatiser l’utilisateur mise en service et hors service pour les Applications SaaS avec Azure Active Directory

##<a name="what-is-automated-user-provisioning-for-saas-apps"></a>Est-il automatisé approvisionnement de l’utilisateur pour les applications SaaS ?

Azure Active Directory (AD Azure) vous permet d’automatiser la création, la maintenance et suppression des identités utilisateur dans les applications en nuage ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) tels que boîte de dépôt, force de vente, ServiceNow et bien plus encore.

**Voici quelques exemples de ce que cette fonctionnalité vous permet d’effectuer :**

- Créer automatiquement des comptes dans les applications SaaS droite de nouvelles personnes lorsqu’ils connectent à votre équipe.
- Désactiver automatiquement des comptes à partir d’applications de SaaS lorsque les utilisateurs quittent inévitablement l’équipe.
- Garantir que les identités de vos applications SaaS sont à jour en fonction des modifications dans l’annuaire.
- Provisionner les objets non-utilisateur, tels que des groupes, pour les applications SaaS qui les prennent en charge.

**La mise en service automatique utilisateur inclut également les fonctionnalités suivantes :**

- La possibilité de faire correspondre des identités existantes entre AD Azure et les applications SaaS.
- Options de personnalisation à l’aide d’Active Directory Azure ajustement les configurations des applications SaaS que votre organisation utilise actuellement.
- Alertes e-mail facultative pour les erreurs de mise en service.
- Journaux de création de rapports et de l’activité pour aider à la surveillance et le dépannage.

##<a name="why-use-automated-provisioning"></a>Pourquoi utiliser un provisionnement automatisé ?

Certains les motivations fréquentes pour l’utilisation de cette fonction sont les suivants :

- Pour éviter les coûts, les inefficacités et erreur humaine associé au processus de mise en service manuelle.
- Pour sécuriser votre entreprise en supprimant instantanément les identités des utilisateurs à partir des principales applications de SaaS lorsqu’ils quittent l’organisation.
- Pour facilement importer un nombre en bloc d’utilisateurs dans une application SaaS particulière.
- Pour profiter de la commodité d’avoir votre solution de provisioning fonctionnent sur les même application les stratégies d’accès que vous avez définie pour Azure AD Single Sign-On.

##<a name="frequently-asked-questions"></a>Forum aux Questions

**La fréquence à laquelle Azure AD n’écrit pas les modifications de répertoire dans l’application SaaS ?**

Azure Active Directory vérifie les modifications toutes les 5 à 10 minutes. Si l’application SaaS retourne plusieurs erreurs (comme dans le cas d’informations d’identification d’administration non valide), AD Azure ralentit progressivement sa fréquence à une fois par jour jusqu'à ce que les erreurs sont corrigées.

**Combien de temps faut-il pour configurer mes utilisateurs ?**

Les modifications incrémentielles se produisent presque instantanément, mais si vous tentez de mettre en service la plupart de votre annuaire, puis elle dépend du nombre d’utilisateurs et de groupes que vous avez. Petits répertoires ne prennent que quelques minutes, répertoires de taille moyenne peuvent prendre plusieurs minutes et répertoires très volumineux peuvent prendre plusieurs heures.

**Comment puis-je suivre l’état d’avancement de la tâche de déploiement actuelle ?**

Vous pouvez examiner le rapport de mise en service de compte sous la section rapports de votre répertoire. Une autre option consiste à consulter l’onglet tableau de bord pour l’application de SaaS qui vous sont provisionnement et regardez sous la section « État d’intégration » au bas de la page.

**Comment savoir si les utilisateurs ne parviennent pas à obtenir en service correctement ?**

À la fin de la configuration du provisionnement Assistant il est une option permettant de s’abonner à des notifications par courrier électronique pour les échecs de mise en service. Vous pouvez également vérifier le rapport d’erreurs de mise en service pour voir quels utilisateurs n’a pas pu être mis en service et pourquoi.

**Pouvez AD Azure écrivent les modifications à partir de l’application de SaaS dans le répertoire ?**

Pour la plupart des applications SaaS, le provisionnement est sortant uniquement, ce qui signifie que les utilisateurs sont écrites à partir du répertoire de l’application, et les modifications de l’application ne peut pas être réécrites sur le répertoire. Pour la [journée de travail](https://msdn.microsoft.com/library/azure/dn762434.aspx), toutefois, le provisionnement est entrant uniquement, ce qui signifie que les utilisateurs sont importés dans le répertoire à partir de la journée de travail, et de la même façon, les modifications dans le répertoire ne pas obtenir réécrites dans la journée de travail.

**Comment puis-je envoyer des commentaires à l’équipe technique ?**

Veuillez nous contacter via le [forum de commentaires Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="how-does-automated-provisioning-work"></a>Quels sont les tâches de dimensionnement automatique ?

Annonce Azure dispositions aux utilisateurs d’applications de SaaS en vous connectant à la mise en service des points de terminaison fournis par chaque fournisseur de l’application. Ces points de terminaison permettent AD Azure créer, mettre à jour et supprimer des utilisateurs par programme. Vous trouverez ci-dessous une brève présentation des différentes étapes AD Azure permet d’automatiser le provisionnement.

1. Lorsque vous activez la mise en service d’une application pour la première fois, les actions suivantes sont exécutées :
 - Azure AD tentera de correspondre à tous les utilisateurs existants dans l’application SaaS avec leurs identités correspondantes dans le répertoire. Lorsqu’un utilisateur est mis en correspondance, qu’ils ne sont *pas* automatiquement activés pour l’ouverture de session unique. Afin qu’un utilisateur ait accès à l’application, ils doivent recevoir explicitement à l’application dans Azure AD, soit directement, soit via l’appartenance au groupe.
 - Si vous avez déjà spécifié les utilisateurs doivent être affectés à l’application, et en cas d’échec d’Azure AD rechercher les comptes existants pour les utilisateurs, AD Azure met en service des comptes pour eux dans l’application.
2. Une fois la synchronisation initiale est terminée comme décrit ci-dessus, AD Azure vérifie toutes les 10 minutes pour que les modifications suivantes :
 - Si des utilisateurs ont été affectés à l’application (soit directement, soit par le biais de l’appartenance au groupe), puis ils seront mis en service d’un nouveau compte dans l’application SaaS.
 - Si l’accès d’un utilisateur a été supprimé, puis leur compte dans l’application SaaS sera marqué comme désactivé (les utilisateurs ne sont jamais totalement supprimés, qui vous protège contre les pertes de données dans le cas d’une configuration incorrecte).
 - Si un utilisateur a été récemment affecté à l’application et qu’ils avaient déjà un compte dans l’application SaaS, ce compte sera marqué comme activé, et certaines propriétés de l’utilisateur peuvent être mis à jour si elles sont obsolètes par rapport à l’annuaire.
 - Si les informations d’un utilisateur (par exemple, le numéro de téléphone, emplacement du bureau, etc.) a été modifiées dans le répertoire, puis ces informations met également à jour dans l’application SaaS.

Pour plus d’informations sur la façon dont les attributs sont mappés entre AD Azure et votre application SaaS, voir l’article sur la [Personnalisation des mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md).

##<a name="list-of-apps-that-support-automated-user-provisioning"></a>Liste des applications qui prennent en charge l’attribution automatisée

Cliquez sur une application pour afficher un didacticiel sur la configuration de la mise en service automatisée pour qu’il :

- [Zone](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Se concerter](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Boîte de dépôt pour les entreprises](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Force de vente](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Sandbox de la force de vente](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [Journée de travail](http://go.microsoft.com/fwlink/?LinkId=690250) (mise en service entrant)

Dans l’ordre d’une application prendre en charge la mise en service de l’utilisateur automatisées, il doit d’abord fournir les points de terminaison nécessaires permettant aux programmes externes automatiser la création, la maintenance et la suppression des utilisateurs. Par conséquent, pas toutes les applications SaaS sont compatibles avec cette fonctionnalité. Pour les applications qui prennent en charge cela, l’équipe d’ingénierie AD Azure puis sera en mesure de créer un connecteur de mise en service de ces applications, et ce travail est prioritaire par les besoins de clients actuels et potentiels.

Pour contacter l’équipe d’ingénierie AD Azure pour demander la prise en charge de mise en service des applications supplémentaires, veuillez envoyer un message via le [forum de commentaires Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Personnalisation des mappages d’attributs pour l’approvisionnement de l’utilisateur](active-directory-saas-customizing-attribute-mappings.md)
- [Écriture d’Expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Portée des filtres pour l’approvisionnement de l’utilisateur](active-directory-saas-scoping-filters.md)
- [Pour activer la mise en service automatique des utilisateurs et des groupes d’Azure Active Directory pour les applications à l’aide de SCIM](active-directory-scim-provisioning.md)
- [Mise en service des Notifications de compte](active-directory-saas-account-provisioning-notifications.md)
- [Liste des didacticiels sur la façon d’intégrer les applications SaaS](active-directory-saas-tutorial-list.md)
