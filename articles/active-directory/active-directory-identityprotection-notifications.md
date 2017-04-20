<properties
    pageTitle="Les notifications de Protection de l’identité Active Directory Azure | Microsoft Azure"
    description="Découvrez comment les notifications prennent en charge vos activités d’enquête."
    services="active-directory"
    keywords="protection d’identité Azure ActiveDirectory, découverte d’application cloud, gestion des applications, sécurité, risques, le niveau de risque, vulnérabilité, stratégie de sécurité"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-notifications"></a>Notifications de Protection de l’identité Active Directory Azure 


La Protection d’identité de publicité Azure envoie deux types d’e-mails de notification automatisée pour vous aider à gérer les risques de l’utilisateur et risque :

- E-mail d’alerte en péril

- E-mail de résumé hebdomadaire

## <a name="user-compromised-alert-email"></a>E-mail d’alerte en péril

Une alerte de messagerie compromis utilisateur est générée lors de la Protection d’identité AD Azure identifie un compte comme compromis. Le message inclut un lien vers les utilisateurs marqués pour l’état des risques dans le tableau de bord de Protection d’identité. Nous vous recommandons d’immédiatement vérifier les notifications de compromis.


## <a name="weekly-digest-email"></a>E-mail de résumé hebdomadaire

L’e-mail de résumé hebdomadaire affiche un résumé de nouveaux événements de risque.<br>
Il comprend :

- Utilisateurs en péril
- Activités suspectes
- Vulnérabilités détectées
- Liens vers les rapports connexes dans la Protection de l’identité


<br>
![Mise à jour](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br> 

Vous pouvez basculer de l’envoi d’un e-mail de résumé hebdomadaire hors tension.
<br><br>
![Risques pour l’utilisateur](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>
 

**Pour ouvrir la boîte de dialogue de configuration connexes**:

1. Sur la lame de **Protection de l’identité de publicité Azure** , cliquez sur **paramètres**.
<br><br>
![Stratégie de risques d’utilisateur](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
<br>

2. Dans la section **Général** , cliquez sur **Notifications**.
<br><br>
![Stratégie de risques d’utilisateur](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
<br>




## <a name="see-also"></a>Voir aussi

- [Protection d’identité Azure Active Directory](active-directory-identityprotection.md) 

