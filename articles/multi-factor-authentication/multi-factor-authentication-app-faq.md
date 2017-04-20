<properties
    pageTitle="L’authentificateur de Microsoft app Forum aux questions"
    description="Fournit une liste de questions fréquemment posées et des réponses liés à l’app de Microsoft Authentication et l’authentification à plusieurs facteurs Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar, librown"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator-application-faq"></a>Forum aux questions de Microsoft Authenticator application

L’application Microsoft Authenticator remplacé l’application Azure authentificateur et est l’application recommandée lorsque vous utilisez l’authentification à plusieurs facteurs Azure. Cette application est disponible pour iOS, Android et Windows Phone.

## <a name="frequently-asked-questions"></a>Forum aux questions

- **Qu’est devenu l’Azure authentificateur, authentification de plusieurs facteurs et applications de compte Microsoft ?**

    L’application Microsoft Authenticator eux remplace ces applications. Authentificateur Azure mis à niveau vers Microsoft Authenticator. Si vous utilisez l’authentification de plusieurs facteurs ou applications de compte Microsoft, installez Microsoft Authenticator et ajoutez de nouveau vos comptes. Veillez à terminer l’ajout de vos comptes à la nouvelle application avant de supprimer les anciennes.

- **J’utilise déjà l’application Microsoft Authenticator pour les codes de vérification. Comment utiliser les notifications de transmission d’un seul clic ?**  

    Approbation d’une connexion à l’aide de notification de transmission n’est disponible que pour les comptes Microsoft, pas pour les comptes de tiers tels que Google ou Facebook. Pour les comptes Microsoft de travail ou à l’école, votre organisation peut choisir de désactiver cette option, cependant.

    Si vous utilisez un compte Microsoft pour votre compte personnel et que vous voulez basculer vers les notifications de transmission, vous devez ajouter votre compte à nouveau. Réinscrire l’appareil avec votre compte et de notifications de type Pousser.  

    Si votre compte n’a pas de vérification en deux étapes activée, voir la rubrique [sur la vérification des deux étapes](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) pour décider si elle est adaptée à vos besoins.  

- **Lorsque vous être en mesure d’utiliser les notifications de transmission d’un clic sur un iPhone ou iPad ?**  

    Cette fonctionnalité est en version bêta, jusqu'à la fin du mois d’août, lorsqu’il devient disponible pour les comptes de Microsoft. Si vous souhaitez rejoindre notre programme bêta, envoyer un e-mail à msauthenticator@microsoft.com. Inclure votre prénom, nom et ID de Apple dans votre message.  

- **Les notifications de transmission d’un seul clic fonctionnent pour les comptes non Microsoft ?**  

    Non, les notifications de type Pousser fonctionnent uniquement avec les comptes Microsoft Azure comptes et Active Directory. Si votre travail ou votre école utilise les comptes Active Directory Azure, ils peuvent désactiver cette fonctionnalité.  

- **J’ai restauré mes périphériques à partir d’une sauvegarde, et les codes de mon compte sont manquant ou ne fonctionne ne pas. Que s'est-il passé ?**  

    Pour des raisons de sécurité, nous ne restaurer des comptes à partir de sauvegardes de l’application. Si vous restaurez l’application iOS à partir d’une sauvegarde, vos comptes sont toujours affichés, mais ils ne fonctionnent pas pour recevoir des vérifications de connexion ou de générer des codes de sécurité. Après la restauration de l’application, supprimez vos comptes et ajoutez-les à nouveau.

- **J’ai obtenu un nouveau périphérique. Comment supprimer mon ancien périphérique l’application Microsoft Authenticator et déplacer vers le nouveau ?**

    Ajout de l’application Microsoft Authenticator sur un nouveau périphérique ne supprime pas automatiquement il à partir d’autres périphériques. Pour gérer les périphériques sont configurés pour votre compte, visitez le site Web de même que vous permet de gérer la vérification de deux étapes et que vous choisissez de supprimer les anciennes applications.

    Pour des comptes personnels Microsoft, ce site Web est la page de [sécurité du compte](https://account.microsoft.com/security) . Pour les comptes Microsoft de travail ou à l’école, ce site Web peut être [MyApps](https://myapps.microsoft.com) ou un portail personnalisé que votre entreprise a installé.

## <a name="contact-us"></a>Nous contacter

Si votre question n’était pas réponse ici, laissez un commentaire en bas de la page. Ou bien, [contactez le support technique](https://support.microsoft.com/contactus) et nous répondrons à votre problème dès que possible.

Si vous contactez le support technique, incluent aussi bien des informations suivantes que vous pouvez :

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

- [Forum aux questions sur Azure plusieurs facteurs d’authentification](multi-factor-authentication-faq.md)  
- [À propos de la vérification des deux étapes](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) pour les comptes de Microsoft
- [Des difficultés avec la vérification de deux étapes ?](multi-factor-authentication-end-user-troubleshoot.md)
