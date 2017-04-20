<properties
    pageTitle="Forum aux questions sur Azure Active Directory | Microsoft Azure"
    description="Azure FAQ Active Directory qui répond aux questions en conjonction avec l’accès d’Azure et Azure Active Directory, accès de gestion et l’application de mot de passe."
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
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markusvi"/>

# <a name="azure-active-directory-faq"></a>Forum aux questions sur Azure Active Directory

Azure Active Directory est une identité complète sous la forme d’une solution de Service (IDaaS) qui couvre tous les aspects de l’identité, la gestion des accès et la sécurité.


Pour plus d’informations, consultez [Nouveautés Azure Active Directory ?](active-directory-whatis.md).



## <a name="accessing-azure-and-azure-active-directory"></a>L’accès d’Azure et Azure Active Directory


**Q : je reçois « aucun abonnement ne trouvé » lorsque j’essaie d’accéder à des annonces Azure dans Azure portal classique (https://manage.windowsazure.com)**

**A:** L’accès au portail classique Azure impose que chaque utilisateur dispose des autorisations sur un abonnement Azure. Si vous avez un payé Office 365 ou un AD Azure accédez à [http://aka.ms/accessAAD](http://aka.ms/accessAAD) pour une étape d’activation, sinon vous devrez activer une complète [Azure d’essai](https://azure.microsoft.com/pricing/free-trial/) ou un abonnement payant. 

Pour plus d’informations, consultez :

- [Comment les abonnements Azure Azure Active Directory associés](active-directory-how-subscriptions-associated-directory.md)

- [Gérer l’annuaire d’abonnement à Office 365 dans Azure](active-directory-manage-o365-subscription.md)

---

**Q : quelle est la relation entre une annonce Azure, Office 365 et Azure ?**

**A:** Azure Active Directory vous offre des possibilités d’identités et des accès communes à tous les services en ligne Microsoft. Si vous utilisez Office 365, Microsoft Azure, Intune ou autres personnes, vous utilisez déjà une annonce Azure pour activer l’ouverture de session et d’accéder à la gestion de tous ces services. 

En fait, tous les utilisateurs que vous avez activé pour Microsoft Online services sont définies comme des comptes utilisateur dans une ou plusieurs instances de AD Azure. Vous pouvez activer ces fonctionnalités comptes AD gratuitement Azure, tels que l’accès aux applications de cloud.
 
En outre, AD Azure payantes (par exemple : AD Azure basic, Premium, EMS, etc.) complètent des autres services tels que Office 365 et de Microsoft Azure avec les solutions de gestion et de sécurité échelle entreprise complète en ligne.


---



## <a name="getting-started-with-hybrid-azure-ad"></a>Mise en route avec hybride Azure AD


**Q : Comment puis-je me connecter mon répertoire local AD Azure ?**

**A:** Vous pouvez vous connecter à votre répertoire local pour Azure AD à l’aide **d’Azure Connect d’Active Directory**. 

Pour plus d’informations, voir [intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).


---

**Q : comment configurer l’authentification unique entre mon annuaire local et de mes applications en nuage ?**

**A:** Vous devez uniquement configurer l’authentification unique entre le répertoire de votre local et AD Azure. Dans la mesure où vous accédez à vos applications en nuage via AD Azure, le service automatiquement les lecteurs vos utilisateurs de s’authentifier correctement avec leurs informations d’identification sur site.

Mise en œuvre de l’authentification unique sur site peut être facilement traitée avec des solutions de fédération ADFS ou en configurant la synchronisation de hachage de mot de passe. Vous pouvez facilement déployer les deux options à l’aide de l’Assistant de configuration Azure Connect d’Active Directory.
  

Pour plus d’informations, voir [intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
  

---

**Q : Azure Active Directory fournit-il un portail libre-service pour les utilisateurs de mon organisation ?**

**A:** Oui, Azure Active Directory vous offre le [Panneau AD Azure](http://myapps.microsoft.com) pour utilisateur libre-service et l’accès de l’application. Si vous êtes un client Office 365, vous trouverez la plupart des mêmes fonctionnalités dans le portail Office 365. 

Pour plus d’informations, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md). 



---

**Q : n’annonce Azure m’aide à gérer mon infrastructure sur site ?**

**A:** Oui, c’est le cas. L’édition Premium de publicité Azure fournit **La santé se connecter**. Santé de connexion AD Azure permet de contrôler et de comprendre votre infrastructure d’identité sur les sites et les services de synchronisation.  

Pour plus d’informations, consultez [services d’infrastructure et de la synchronisation d’identité dans le nuage moniteur vos locaux](active-directory-aadconnect-health.md).  

---

## <a name="password-management"></a>Gestion de mot de passe

**Q : puis-je utiliser la fonctionnalité AD Azure mot de passe en écriture différée sans la synchronisation de mot de passe ? (AKA, j’aimerais utiliser Azure AD SSPR avec mot de passe différée, mais je ne veux pas mes mots de passe stockés dans le cloud?)**

**A:** Vous n’avez pas besoin de synchroniser vos mots de passe Active Directory vers Active Directory Azure pour activer l’écriture différée. Dans un environnement fédéré, Azure AD SSO s’appuie sur le répertoire local pour authentifier l’utilisateur. Ce scénario ne requiert pas le mot de passe local pour être suivis dans Azure AD.

---

**Q : combien de temps faut-il pour être réécrites vers Active Directory sur site un mot de passe ?**

**A:** Écriture différée de mot de passe fonctionne en temps réel. 

Pour plus d’informations, consultez [mise en route avec la gestion de mot de passe](active-directory-passwords-getting-started.md) 


---

**Q : puis-je utiliser différée de mot de passe mots de passe qui est gérés par un administrateur ?**

**A:** Oui, si vous possédez le mot de passe à écriture différée est activée, les opérations de mot de passe effectuées par un administrateur sont écrites à votre environnement sur site.  

Pour obtenir des réponses plus au mot de passe des questions connexes, consultez le [Forum aux Questions de mot de passe gestion](active-directory-passwords-faq.md).

---

## <a name="application-access"></a>Accès aux applications


**Q : où puis-je trouver une liste des applications qui sont déjà intégrées avec AD Azure et leurs fonctions ?**

**A:** Annonce Azure a plus 2600 applications déjà intégrées dans Microsoft, les fournisseurs de services d’application ou les partenaires. Toutes les applications déjà intégrées prennent en charge l’authentification unique. L’authentification unique vous permet d’utiliser vos informations d’identification d’organisation pour accéder à vos applications. Certaines applications prennent également en charge automatisée mise en service et mise hors service

Pour obtenir une liste complète des applications déjà intégrées, consultez le [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).


---

**Q : que se passe-t-il si l’application que j’ai besoin n’est pas sur le marché de la publicité Azure ?**

**A:** Avec Azure Premium d’Active Directory, vous pouvez ajouter et configurer une application que vous souhaitez. Selon les fonctionnalités de votre application et vos préférences, vous pouvez configurer l’authentification unique et provisionnement automatisé.  

Pour plus d’informations, consultez :

- [Configuration de l’authentification unique pour les applications qui ne sont pas dans la galerie des applications Azure Active Directory](active-directory-saas-custom-apps.md)
- [Pour activer la mise en service automatique des utilisateurs et des groupes d’Azure Active Directory pour les applications à l’aide de SCIM](active-directory-scim-provisioning.md) 


---

**Q : comment inscrire les utilisateurs dans les applications à l’aide d’Azure Active Directory ?**
 
**A:** Azure Active directory offre plusieurs méthodes pour les utilisateurs d’afficher et d’accéder à leurs applications telles que :

- Le panneau d’accès AD Azure

- Le Lanceur d’applications Office 365

- Direct-ouverture de session à des applications fédérées

- Liens des fédérés, mot de passe ou les applications existantes

Pour plus d’informations, consultez [déploiement AD Azure intégré des applications aux utilisateurs](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**Q : quelles sont les différentes méthodes Azure Active Directory permet l’authentification et l’authentification unique pour applications ?**
 
**A:** Azure Active Directory prend en charge de nombreux protocoles standardisés pour l’authentification et l’autorisation tels que SAML 2.0 OpenID connecter, OAuth 2.0 et WS-Federation. Annonce Azure prend également en charge le stockage du mot de passe et les capacités de connexion automatiques pour les applications qui prennent uniquement en charge l’authentification basée sur les formulaires.  

Pour plus d’informations, voir :

- [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md)

- [Protocoles d’authentification Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Comment single sign-on avec travail d’Azure Active Directory ?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**Q : puis-je ajouter des applications que j’exécute sur site ?**

**A:** Le Proxy d’Application AD Azure vous offre un accès facile et sécurisé aux applications web local que vous choisissez. Vous pouvez accéder à ces applications de la même façon que vous accédez à vos applications SaaS dans Azure Active Directory. N’est pas nécessaire pour un réseau privé virtuel ou de la modification de votre infrastructure réseau.  

Pour plus d’informations, consultez [comment fournir l’accès à distance sécurisé à des applications sur site](active-directory-application-proxy-get-started.md).


--- 

**Q : comment exiger des AMF pour les utilisateurs accédant à une application particulière ?**

**A:** Avec accès conditionnel de publicité Azure, vous pouvez affecter une stratégie d’accès unique pour chaque application. Dans votre stratégie, vous pouvez exiger AMF à tout moment, ou lorsque les utilisateurs ne sont pas connectés au réseau local.  

Pour plus d’informations, consultez [sécurisation des accès à Office 365 et autres applications connectées à Azure Active Directory](active-directory-conditional-access.md).


---

**Q : qu’est automatisé approvisionnement de l’utilisateur pour les applications SaaS ?**

**A:** Azure Active Directory vous permet d’automatiser la création, la maintenance et suppression des identités utilisateur dans de nombreuses applications populaires cloud (SaaS). 

Pour plus d’informations, voir [automatiser la mise en service des utilisateurs et Deprovisioning aux Applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md)

---



