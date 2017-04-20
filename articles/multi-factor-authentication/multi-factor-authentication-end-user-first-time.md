<properties
    pageTitle="Configurer la vérification en deux étapes pour mon compte de travail ou de l’école"
    description="Lorsque votre société configure Azure, plusieurs facteurs d’authentification, le système vous demandera d’inscription pour la vérification des deux étapes. Apprenez à configurer. "
    services="multi-factor-authentication"
    keywords="Comment faire pour utiliser le répertoire azure, active directory dans le nuage, didacticiel d’active directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="set-up-my-account-for-two-step-verification"></a>Configurer mon compte pour la vérification des deux étapes

Vérification d’en deux étapes est une mesure de sécurité supplémentaire qui permet de protéger votre compte en le rendant plus difficile pour d’autres personnes rompre de. Si vous êtes en train de lire cet article, vous avez probablement un courrier électronique à partir de votre administrateur de travail ou de l’école à propos de l’authentification à plusieurs facteurs. Ou peut-être vous avez essayé de vous connecter et un message vous invitant à configurer la vérification de sécurité supplémentaires. Si c’est le cas, **que vous ne pouvez pas vous connecter jusqu'à ce que vous avez terminé le processus d’inscription automatique**.

Cet article vous aide à configurer votre **travail ou compte d’établissement**. Si vous souhaitez activer la vérification en deux étapes pour votre propre compte personnel de Microsoft, reportez-vous à la section [sur la vérification de deux étapes](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="determine-how-you-will-use-multi-factor-authentication"></a>Déterminez comment vous allez utiliser une authentification multifactorielle

Vérification de deux étapes fonctionne par vous inviter pour deux pièces d’identification lorsque vous vous connectez. Tout d’abord, nous vous demandons votre nom d’utilisateur et le mot de passe comme d’habitude. Ensuite, nous contacter un téléphone qui nous savons appartient à vous et vous confirmer que la tentative de connexion a été légitime.  

Pour vous familiariser avec le processus d’installation, essayez de vous connecter à votre compte comme vous le faites habituellement. Si votre administrateur a configuré votre compte pour la vérification de deux étapes, vous devrez lancer le processus d’inscription automatique. Commencez ce processus en cliquant sur **configurer maintenant.**

![Programme d’installation](./media/multi-factor-authentication-end-user-first-time/first.png)

La première question de la procédure d’inscription est comment vous contacter. Examinez les options de la table et utilisez les liens pour accéder aux étapes de configuration pour chaque méthode.

| Méthode de contact | Description |
| --- | --- |
[Application mobile](#use-a-mobile-app-as-the-contact-method) | - **Recevoir des notifications pour la vérification.** Cette option envoie une notification à l’application de l’authentificateur sur votre smartphone ou votre Tablet PC. Afficher la notification et, si elle est légitime, sélectionnez **authentifier** dans l’application. Votre travail ou votre école peut nécessiter que vous entrez un code confidentiel avant de vous authentifiez.<br>- **Utilisez le code de vérification.** Dans ce mode, l’application l’authentificateur génère un code de vérification qui met à jour toutes les 30 secondes. Entrez le code de vérification plus récent dans l’interface utilisateur.<br>L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
[Téléphone mobile ou un texte](#use-your-mobile-phone-as-the-contact-method) | - **Appel téléphonique** place un appel automatisé de messages vocaux pour vous fournissez le numéro de téléphone. Répondre à l’appel et appuyez sur la touche # dans le clavier de téléphone pour l’authentification.<br>- **Message texte** se termine un message texte contenant un code de vérification. À la suite de l’invite de commandes dans le texte, répondez au message texte ou entrez le code de vérification fourni dans l’interface utilisateur. |  
[Appel téléphonique de bureau](#use-your-office-phone-as-the-contact-method) | Place un appel automatisé de messages vocaux pour vous fournissez le numéro de téléphone. Répondre à l’appel et appuie sur # dans le clavier de téléphone pour l’authentification. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Utiliser une application mobile comme méthode de contact

À l’aide de cette méthode nécessite que vous installez une application authentificateur sur votre téléphone ou votre Tablet PC. Les étapes décrites dans cet article sont basées sur l’application de Microsoft Authenticator, qui est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Dans la liste déroulante, sélectionnez **une application Mobile** .
2. Sélectionnez **recevoir des notifications pour la vérification** ou **utiliser le code de vérification**, puis sélectionnez **configurer**.

    ![Écran de vérification de sécurité supplémentaires](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. Sur votre téléphone ou votre Tablet PC, ouvrez l’application et sélectionnez **+** pour ajouter un compte. (Sur les appareils Android, sélectionnez trois points, puis **Ajouter un compte**.)
4. Spécifier que vous souhaitez ajouter un compte de travail ou l’école. L’Analyseur de code QR sur votre téléphone s’ouvre. Si votre appareil photo ne fonctionne pas correctement, vous pouvez sélectionner pour entrer manuellement les informations de votre société. Pour plus d’informations, consultez [Ajouter un compte manuellement](#add-an-account-manually).  
5. Numérisez l’image de code QR apparaissant à l’écran de configuration de l’application mobile.  Cliquez sur **terminé** pour fermer l’écran de code QR.  

    ![Écran de code QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

6. Lorsque l’activation est terminée sur le téléphone, sélectionnez **Contact moi**.  Cette étape envoie une notification ou sur un code de vérification à votre téléphone. Sélectionnez **Vérifier**.  
7. Si votre société nécessite un code confidentiel pour l’approbation de vérification, entrez-le.

    ![Zone permettant d’entrer un code confidentiel](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. Une fois le code PIN est terminée, sélectionnez **Fermer**. À ce stade, la vérification doit réussir.
9. Nous recommandons d’entrer votre numéro de téléphone portable en cas de perte de l’accès à votre application mobile. Spécifiez votre pays dans la liste déroulante et entrez votre numéro de téléphone portable dans la zone en regard du nom de pays. Cliquez sur **suivant**.
10. À ce stade, vous êtes invité à définir des mots de passe de app pour les applications autres que des navigateurs tels qu’Outlook 2010 ou antérieure, ou l’application de messagerie électronique natif sur les périphériques Apple. C’est parce que certaines applications ne prennent en charge la vérification de deux étapes. Si vous n’utilisez pas ces applications, cliquez sur **Terminer** et ignorez le reste de la procédure.
11. Si vous n’utilisez pas ces applications, copier le mot de passe d’application fourni et le coller dans votre application au lieu de votre mot de passe standard. Vous pouvez utiliser le même mot de passe d’application pour plusieurs applications. Pour plus d’informations, [aide de mots de passe app].
12. Cliquez sur **terminé**.


### <a name="add-an-account-manually"></a>Ajouter un compte manuellement
Si vous souhaitez ajouter d’un compte pour l’application mobile manuellement, au lieu d’utiliser le lecteur QR, procédez comme suit.

1. Cliquez sur le bouton **saisir le compte manuellement** .  
2. Entrez le code et l’URL sont fournies sur la même page que celle qui vous montre le code à barres. Cette information va dans les zones de **Code** et **l’URL** de l’application mobile.

    ![Programme d’installation](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. Une fois l’activation terminée, sélectionnez **Contact moi**. Cette étape envoie une notification ou sur un code de vérification à votre téléphone. Sélectionnez **Vérifier**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Utilisez votre téléphone mobile en tant que la méthode de contact

1. Sélectionnez **Authentification téléphone** dans la liste déroulante.  

    ![Programme d’installation](./media/multi-factor-authentication-end-user-first-time-mobile-phone/phone.png)  

2. Sélectionnez votre pays dans la liste déroulante et entrez votre numéro de téléphone portable.
3. Sélectionnez la méthode que vous préférez utiliser avec votre téléphone portable - appel ou du texte.
4. Sélectionnez le **Contact moi** pour vérifier votre numéro de téléphone. Selon le mode sélectionné, nous vous envoyer un texte ou vous appeler. Suivez les instructions affichées à l’écran, puis sélectionnez **Vérifier**.
5. À ce stade, vous êtes invité à définir des mots de passe de app pour les applications autres que des navigateurs tels qu’Outlook 2010 ou antérieure, ou l’application de messagerie électronique natif sur les périphériques Apple. C’est parce que certaines applications ne prennent en charge la vérification de deux étapes. Si vous n’utilisez pas ces applications, cliquez sur **Terminer** et ignorez le reste de la procédure.
6. Si vous n’utilisez pas ces applications, copier le mot de passe d’application fourni et le coller dans votre application au lieu de votre mot de passe standard. Vous pouvez utiliser le même mot de passe d’application pour plusieurs applications. Pour plus d’informations, [aide de mots de passe app].
7. Cliquez sur **terminé**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Utilisez votre téléphone de bureau en tant que la méthode de contact

1. Sélectionner un **Téléphone de bureau** dans la liste déroulante.  

    ![Programme d’installation](./media/multi-factor-authentication-end-user-first-time-office-phone/office.png)  

2. La zone numéro de téléphone est automatiquement renseignée avec les informations de contact de votre société. Si le numéro est erroné ou manquant, demandez à votre administrateur pour apporter des modifications.
4. Sélectionnez le **Contact moi** pour vérifier votre numéro de téléphone, et nous vous appellerons à votre numéro. Suivez les instructions affichées à l’écran, puis sélectionnez **Vérifier**.
5. À ce stade, vous êtes invité à définir des mots de passe de app pour les applications autres que des navigateurs tels qu’Outlook 2010 ou antérieure, ou l’application de messagerie électronique natif sur les périphériques Apple. C’est parce que certaines applications ne prennent en charge la vérification de deux étapes. Si vous n’utilisez pas ces applications, cliquez sur **Terminer** et ignorez le reste de la procédure.
6. Si vous n’utilisez pas ces applications, copier le mot de passe d’application fourni et le coller dans votre application au lieu de votre mot de passe standard. Vous pouvez utiliser le même mot de passe d’application pour plusieurs applications. Pour plus d’informations, consultez [que sont les mots de passe App](multi-factor-authentication-end-user-app-passwords.md).
7. Cliquez sur **terminé**.

## <a name="next-steps"></a>Étapes suivantes

- Modifier vos options préférées et [gérer vos paramètres de vérification d’en deux étapes](multi-factor-authentication-end-user-manage-settings.md)
- Définir des [mots de passe app](multi-factor-authentication-end-user-app-passwords.md) pour les applications de périphérique natives qui ne prennent pas en charge les vérification de deux étapes.
- Extraire l' [authentificateur de Microsoft app](multi-factor-authentication-microsoft-authenticator.md) pour authentification rapide et sécurisée, même lorsque vous n’avez pas service de cellule.
