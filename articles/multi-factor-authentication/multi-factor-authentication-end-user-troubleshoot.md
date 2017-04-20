<properties
    pageTitle="Résoudre les problèmes de vérification de deux étapes | Microsoft Azure"
    description="Ce document fournit aux utilisateurs des informations sur que faire en cas de problème avec l’authentification à plusieurs facteurs Azure."
    services="multi-factor-authentication"
    keywords = "client d’authentification multifactorielle, problème d’authentification, ID de corrélation"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="having-trouble-with-two-step-verification"></a>Des difficultés avec la vérification d’en deux étapes

Cet article décrit certains problèmes que vous pouvez rencontrer avec vérification de deux étapes. Si le problème que vous rencontrez n’est pas inclus ici, veuillez fournir des commentaires détaillés dans la section commentaires afin que nous puissions améliorer.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>Il a été volé ou de perdre mon téléphone

Il existe deux façons pour revenir votre compte. Le premier est pour vous connecter à l’aide de votre numéro de téléphone d’authentification alternatives, si vous avez défini un. La seconde est de demander à l’administrateur pour effacer vos paramètres.

Si votre téléphone a été perdu ou volé, nous recommandons également que votre administrateur de réinitialiser votre mot de passe app et désactivez les périphériques à retenir. Si votre administrateur ne sait pas comment procéder, dirigez-le vers cet article : [Gérer les utilisateurs et les périphériques](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords).


### <a name="use-an-alternate-phone-number"></a>Utilisez un autre numéro de téléphone

Si vous avez configuré plusieurs options de vérification, y compris un numéro de téléphone secondaire ou une application d’authentificateur sur un autre périphérique, vous pouvez utiliser une de ces pour vous connecter.

Pour vous connecter à l’autre numéro de téléphone, procédez comme suit :

1. Ouvrez une session comme vous le feriez normalement.
2. Lorsque vous êtes invité à vérifier votre compte, cliquez sur **utiliser une option de vérification**.

    ![De vérification](./media/multi-factor-authentication-end-user-manage/differentverification.png)

3. Sélectionnez le numéro de téléphone que vous avez accès à.

    ![Autre téléphone](./media/multi-factor-authentication-end-user-manage/altphone2.png)

4. Lorsque vous avez dans votre compte, [gérer vos paramètres](multi-factor-authentication-end-user-manage-settings.md) pour modifier votre numéro de téléphone de l’authentification.

>[AZURE.IMPORTANT]
>Il est important de configurer un numéro de téléphone secondaire de l’authentification. Si votre numéro de téléphone principal et votre application mobile se trouvent sur le même téléphone, vous devez une troisième option si votre téléphone est perdu ou volé.

### <a name="clear-your-settings"></a>Effacer vos paramètres

Si vous n’avez pas configuré un numéro de téléphone secondaire de l’authentification, vous devez contacter votre administrateur pour obtenir une aide. D’effacer vos paramètres pour la prochaine fois que vous vous connectez, vous serez invité à [configurer votre compte](multi-factor-authentication-end-user-first-time.md) à nouveau.


## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Je ne reçois pas un texte ou que vous appelez sur mon téléphone

Il existe plusieurs raisons pourquoi vous essayez ouvrir une session, mais ne reçoit pas le texte ou l’appel téléphonique. Si vous avez correctement reçu les textes ou les appels téléphoniques vers votre téléphone dans le passé, puis il s’agit sans doute un problème avec le fournisseur de téléphone, pas votre compte. Assurez-vous que vous avez signal de cellule bonne, et si vous essayez de recevoir un message de texte vous assurer que votre plan de service et le téléphone prend en charge les messages au format texte.

Si vous avez attendu plusieurs minutes pour un texte ou d’un appel, le moyen le plus rapide pour accéder à votre compte est d’essayer une autre option.

1. Sélectionnez **utiliser une option de vérification** sur la page qui est en attente de vérification.

    ![De vérification](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

2. Sélectionnez la méthode de livraison ou le numéro de téléphone à utiliser.

    Si vous avez reçu plusieurs codes de vérification, seul le plus récent fonctionne.

Si vous n’avez pas une autre méthode configurée, contactez votre administrateur et demandez-lui pour effacer vos paramètres. La prochaine fois que vous vous connectez, vous devrez [configurer une authentification multifacteur](multi-factor-authentication-end-user-first-time.md) à nouveau.


Si vous avez souvent des retards suite à un signal de cellule incorrecte, nous vous recommandons de qu'utiliser l' [application de l’authentificateur de Microsoft](multi-factor-authentication-microsoft-authenticator.md) sur votre smartphone. L’application peut générer des codes de sécurité aléatoire qui vous permet de vous connecter, et si ces codes ne nécessitent pas une connexion internet ou le signal de cellule.


## <a name="app-passwords-are-not-working"></a>Les mots de passe App ne fonctionnent pas

Tout d’abord, assurez-vous que vous avez entré correctement le mot de passe d’application.  Si elle ne fonctionne toujours pas essayez d’ouverture de session et [créer un nouveau mot de passe d’application](multi-factor-authentication-end-user-app-passwords.md).  Si cela ne pas utiliser, contactez votre administrateur et les [Supprimer de vos mots de passe existants de l’application](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) et que vous pouvez créer un nouveau.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Vous n’avez pas de trouver une réponse à mon problème.

Si vous avez essayé de ces étapes de dépannage mais sont toujours en cours d’exécution dans des problèmes, contactez votre administrateur ou la personne qui a configuré plusieurs facteurs d’authentification pour vous. Ils doivent être en mesure de vous aider.

En outre, vous pouvez publier une question sur les [Forums de publicité Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou [contactez le support](https://support.microsoft.com/contactus) et nous vous répondrons à votre problème dès que possible.

Si vous contactez le support technique, inclure les informations suivantes :

- De **L’ID utilisateur** – quelle est l’adresse de messagerie que vous avez essayé de vous connecter avec ?
- **Description générale de l’erreur** , le message d’erreur exact avez-vous voir ?  Si aucun message d’erreur s’est produite, décrire le comportement inattendu est remarqué, dans le détail.
- **Page** -page étaient vous lorsque vous l’avez vu l’erreur (inclut l’URL) ?
- **Code d’erreur** - le code d’erreur que vous recevez.
- **ID de session** - l’id de session spécifiques que vous recevez.
- **ID de corrélation** – en quel a été le code id de corrélation généré lorsque l’utilisateur vu l’erreur.
- **Horodatage** – quelle était la date précise et l’heure que vous avez vu l’erreur (inclure le fuseau horaire) ?

La plupart de ces informations peut être trouvée sur votre page de connexion. Lorsque vous ne vérifiez votre connexion à dans le temps, sélectionnez **Afficher les détails**.

![Signer dans les détails de l’erreur](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Y compris ces informations nous aident à résoudre le problème aussi rapidement que possible.

## <a name="related-topics"></a>Rubriques connexes
- [Gérer les paramètres de vérification d’en deux étapes](multi-factor-authentication-end-user-manage-settings.md)  
- [Forum aux questions de Microsoft Authenticator application](multi-factor-authentication-app-faq.md)
