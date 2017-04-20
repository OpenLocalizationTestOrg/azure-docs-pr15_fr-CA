<properties
    pageTitle="Azure Active Directory identité Protection - comment débloquer les utilisateurs | Microsoft Azure"
    description="Découvrez comment débloquer les utilisateurs qui ont été bloqués par une stratégie de Protection d’identité Azure Active Directory."
    services="active-directory"
    keywords="protection d’identité Azure ActiveDirectory, débloquer l’utilisateur"
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
    ms.date="09/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory identité Protection - comment débloquer les utilisateurs

Avec Protection d’identité Azure Active Directory, vous pouvez configurer des stratégies pour bloquer les utilisateurs si les conditions configurées sont remplies. En général, un utilisateur bloqué contacts d’assistance déblocage. Ces rubriques expliquent les étapes que vous pouvez effectuer pour débloquer un utilisateur bloqué.


## <a name="determine-the-reason-for-blocking"></a>Déterminer le motif du blocage

Dans un premier temps pour débloquer un utilisateur, vous devez déterminer le type de stratégie qui a bloqué l’utilisateur car les étapes suivantes sont associées. Avec Protection d’identité Azure Active Directory, un utilisateur peut être soit bloqué par une stratégie d’ouverture de session risque ou un risque utilisateur. 

Vous pouvez obtenir le type de stratégie qui a bloqué un utilisateur à partir de l’en-tête dans la boîte de dialogue qui a été présenté à l’utilisateur lors d’une tentative d’ouverture de session :

|Stratégie de | Boîte de dialogue utilisateur|
|--- | --- |
|Connexion à risque | ![Reconnectez-vous bloqués](./media/active-directory-identityprotection-unblock-howto/02.png) |
|Risque de l’utilisateur | ![Compte bloqué](./media/active-directory-identityprotection-unblock-howto/104.png) |


Un utilisateur qui est bloqué par :

- Une stratégie d’ouverture de session risque est également appelé signe dans suspects
- Une stratégie d’utilisateur risque est également appelé un compte à risque

 
## <a name="unblocking-suspicious-sign-ins"></a>Déblocage connexions suspectes

Pour débloquer une connexion suspecte sign-in, vous disposez des options suivantes :

1. **Reconnectez-vous à partir d’un emplacement de votre choix ou d’un périphérique** - une raison courante de signe-ins suspects bloqués sont tentatives de connexion à partir des emplacements inhabituels ou des périphériques. Les utilisateurs peuvent déterminer rapidement s’il s’agit de la raison du blocage en essayant de se connecter à partir d’un emplacement de votre choix ou d’un périphérique.


3. **Exclure de la stratégie** - si vous pensez que la configuration actuelle de votre stratégie d’authentification est à l’origine de problèmes pour des utilisateurs spécifiques, vous pouvez exclure les utilisateurs à partir de celui-ci. Voir [stratégie d’ouverture de session risque](active-directory-identityprotection.md#sign-in-risk-policy) pour plus de détails.
 
4. **Désactiver la stratégie** - si vous pensez que votre configuration de la stratégie est à l’origine de problèmes pour tous vos utilisateurs, vous pouvez désactiver la stratégie. Voir [stratégie d’ouverture de session risque](active-directory-identityprotection.md#sign-in-risk-policy) pour plus de détails.


## <a name="unblocking-accounts-at-risk"></a>Déblocage des comptes à risque

Pour débloquer un compte à risque, vous disposez des options suivantes :

1. **Réinitialiser le mot de passe** - vous pouvez réinitialiser le mot de passe de l’utilisateur. Consultez la [réinitialisation de mot de passe sécurisé manuel](active-directory-identityprotection.md#manual-secure-password-reset) pour plus de détails.

2. **Faire disparaître tous les événements risque** - la stratégie de l’utilisateur risque de bloque un utilisateur si le niveau de risque d’utilisateur configuré pour bloquer l’accès a été atteint. Vous pouvez réduire un utilisateur du niveau de risque en fermant manuellement signalé risque. Pour plus de détails, voir [clôture risque manuellement](active-directory-identityprotection.md#closing-risk-events-manually).

3. **Exclure de la stratégie** - si vous pensez que la configuration actuelle de votre stratégie d’authentification est à l’origine de problèmes pour des utilisateurs spécifiques, vous pouvez exclure les utilisateurs à partir de celui-ci. Reportez-vous à la section [stratégie risque de l’utilisateur](active-directory-identityprotection.md#user-risk-policy) pour plus de détails.
 
4. **Désactiver la stratégie** - si vous pensez que votre configuration de la stratégie est à l’origine de problèmes pour tous vos utilisateurs, vous pouvez désactiver la stratégie. Reportez-vous à la section [stratégie risque de l’utilisateur](active-directory-identityprotection.md#user-risk-policy) pour plus de détails.




## <a name="next-steps"></a>Étapes suivantes

 Vous souhaitez en savoir plus sur la Protection de l’identité de publicité Azure ? Découvrez la [Protection d’identité Azure Active Directory](active-directory-identityprotection.md).
 

