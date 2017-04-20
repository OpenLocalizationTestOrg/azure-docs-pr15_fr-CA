<properties
    pageTitle="Azure expérience AMF connexion avec authentification à plusieurs facteurs Azure"
    description="Cette page vous fournira des conseils sur où vous pouvez pour voir les différentes méthodes de connexion disponibles avec Azure AMF."
    keywords="authentification de l’utilisateur, l’expérience de connexion, connectez-vous à l’aide de téléphone mobile, connectez-vous à l’aide de téléphone"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>La connexion avec authentification à plusieurs facteurs Azure
> [AZURE.NOTE]  La documentation suivante est fournie sur cette page montre une expérience de connexion par défaut.  Pour vous aider à l’ouverture de session, consultez [rencontre des problèmes avec l’authentification à plusieurs facteurs Azure](multi-factor-authentication-end-user-manage-settings.md)



## <a name="what-will-your-sign-in-experience-be"></a>Quel sera votre expérience de connexion ?
En fonction de la connexion et l’utilisation de plusieurs facteurs d’authentification, votre expérience diffèrent.  Dans cette section, nous fournissons des informations sur ce qui se passe lorsque vous vous connectez.  Choisissez celle qui décrit le mieux ce que vous faites :


Qu'est-ce que tu fais ?|Description
:------------- | :------------- |
[La connexion avec un téléphone mobile ou de bureau](#signing-in-with-mobile-or-office-phone) | C’est ce que vous pouvez attendre de la connexion à l’aide de votre téléphone mobile ou office.
[La connexion avec l’application Microsoft Authenticator à l’aide de la notification](#signing-in-with-the-microsoft-authenticator-app-using-notification) | C’est ce que vous pouvez attendre avec les notifications à l’aide de l’application Microsoft Authenticator.
[La connexion avec l’application Microsoft Authenticator à l’aide du code de vérification](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|C’est ce que vous pouvez attendre à l’aide de la thapp Microsoft Authenticator avec un code de vérification.
[La connexion avec une autre méthode](#signing-in-with-an-alternate-method)|Vous verrez à quoi s’attendre si vous souhaitez utiliser une autre méthode.

## <a name="signing-in-with-mobile-or-office-phone"></a>La connexion avec un téléphone mobile ou de bureau

Les informations suivantes décrit l’expérience de l’utilisation d’une authentification multifacteur avec votre téléphone mobile ou office.

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>Connecter à l’aide d’un appel à votre bureau ou un téléphone mobile

- Se connecter à une application ou un service de type Office 365 à l’aide de votre nom d’utilisateur et le mot de passe.
- Microsoft vous appellera.

![Appels de Microsoft](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Répondre au téléphone et appuyez sur la touche #.

![Réponse](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- Vous devez maintenant être connecté.</li>

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>La connexion avec l’application Microsoft Authenticator à l’aide de la notification

Les informations suivantes décrit l’expérience de l’utilisation d’une authentification multifacteur avec l’application Microsoft Authenticator lorsque vous recevez une notification.

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>Pour vous connecter avec une notification envoyée à l’application de Microsoft Authenticator

- Se connecter à une application ou un service de type Office 365 à l’aide de votre nom d’utilisateur et le mot de passe.
- Microsoft vous envoie une notification.

![Microsoft envoie une notification](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Répondre au téléphone et appuyez sur la touche à vérifier.  Si votre société nécessite un code PIN vous demandera pour lui ici.

![Vérifier](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![Programme d’installation](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- Vous devez maintenant être connecté.


## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>La connexion avec l’application Microsoft Authenticator à l’aide du code de vérification

Les informations suivantes décrit l’expérience de l’utilisation d’une authentification multifacteur avec l’application Microsoft Authenticator lorsque vous l’utilisez avec un code de vérification.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Pour vous connecter à l’aide d’un code de vérification avec l’application Microsoft Authenticator

- Se connecter à une application ou un service de type Office 365 à l’aide de votre nom d’utilisateur et le mot de passe.
- Microsoft vous demandera de fournir un code de vérification.

![Entrez le code de vérification](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Ouvrez l’application Microsoft Authenticator sur votre téléphone et entrez le code dans la zone où vous vous connectez.

![Obtenir le code](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- Vous devez maintenant être connecté.


## <a name="signing-in-with-an-alternate-method"></a>La connexion avec une autre méthode


La section suivante va vous montrer comment vous connecter avec une autre méthode lorsque votre méthode principale n’est peut-être pas disponible.

### <a name="to-sign-in-with-an-alternate-method"></a>Pour vous connecter avec une autre méthode

- Se connecter à une application ou un service de type Office 365 à l’aide de votre nom d’utilisateur et le mot de passe.
- Sélectionnez utiliser une option de vérification.  Vous serez présent avec un choix d’options différentes. Le numéro vous voir va être basée sur le nombre que vous avez définis.

![Utilisez la méthode de remplacement](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Choisissez une autre méthode et reconnectez-vous.
