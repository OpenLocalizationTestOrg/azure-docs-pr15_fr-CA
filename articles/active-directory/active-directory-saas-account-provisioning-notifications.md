<properties
    pageTitle="Mise en service des notifications de compte | Microsoft Azure"
    description="Apprenez à vous assurer que vous êtes averti des problèmes liés à la mise en service de l’utilisateur qui requièrent votre attention en activant les notifications de mise en service de compte."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="account-provisioning-notifications"></a>Mise en service des Notifications de compte

Avec l’approvisionnement de l’utilisateur, vous pouvez automatiser le processus de gestion des utilisateurs dans les applications tierces de SaaS. <br>
Alors que c’est un processus automatisé, votre interaction avec ce processus est de temps en temps requise. <br>
Il s’agit, par exemple le cas lorsque le mot de passe du compte que vous avez configuré pour échanger des données avec une troisième partie SaaS application a expiré. 

En activant les notifications de mise en service de compte, vous pouvez vous assurer que vous êtes averti des problèmes liés à la mise en service de l’utilisateur qui nécessitent votre attention.

Vous activez ou désactivez la mise en service des notifications dans le cadre de votre configuration d’un fournisseur tiers SaaS application de mise en service de compte.

![Mise en service de l’utilisateur][1] 



Pour activer les notifications de mise en service de compte, activez la case à cocher associée sur la page de dialogue de **Confirmation** , tapez l’alias de messagerie du destinataire.

![Mise en service des Notifications de compte][2]
 


Vous pouvez entrer une liste de distribution en tant que destinataire ; Toutefois, il est important de noter que l’e-mail de notification contient des liens vers les rapports qui ne sont accessibles que par les administrateurs AD Azure.

Si vous avez activées des notifications de mise en service de compte, vous recevrez par e-mail sur les principaux problèmes liés à la mise en service de l’utilisateur. Toutefois, pour éviter une surcharge de la messagerie électronique, vous recevez uniquement un e-mail de notification par jour pour chaque application SaaS pour que l’e-mail de notification est activée.


##<a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Automatiser l’utilisateur mise en service/hors service pour les applications SaaS](active-directory-saas-app-provisioning.md)
- [Personnalisation des mappages d’attributs pour l’approvisionnement de l’utilisateur](active-directory-saas-customizing-attribute-mappings.md)
- [Écriture d’Expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Portée des filtres pour l’approvisionnement de l’utilisateur](active-directory-saas-scoping-filters.md)
- [Pour activer la mise en service automatique des utilisateurs et des groupes d’Azure Active Directory pour les applications à l’aide de SCIM](active-directory-scim-provisioning.md)
- [Liste des didacticiels sur la façon d’intégrer les applications SaaS](active-directory-saas-tutorial-list.md)



<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png