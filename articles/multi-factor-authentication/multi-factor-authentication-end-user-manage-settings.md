<properties
    pageTitle="Gérer vos paramètres de vérification en deux étapes | Microsoft Azure"
    description="Gérer comment vous utilisez Azure authentification à plusieurs facteurs, notamment la modification de vos informations de contact ou de la configuration de vos périphériques."
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

# <a name="manage-your-settings-for-two-step-verification"></a>Gérer les paramètres de vérification d’en deux étapes

Cet article répond aux questions sur la façon de mettre à jour les paramètres pour l’authentification en deux étapes de vérification ou de plusieurs facteurs. Si vous ne parvenez pas à vous connecter à votre compte, reportez-vous à [des difficultés avec la vérification de deux étapes](multi-factor-authentication-end-user-troubleshoot.md) pour la résolution des problèmes.


## <a name="where-to-find-the-settings-page"></a>Où trouver la page des paramètres
Selon la configuration de votre société Azure une authentification multifacteur, il y a quelques endroits où vous pouvez modifier vos paramètres comme votre numéro de téléphone.

Si votre administrateur informatique envoyé une URL spécifique ou les étapes pour gérer la vérification de deux étapes, suivez ces instructions. Dans le cas contraire, suivez les instructions ci-dessous doivent fonctionner pour les autres. Si vous suivez ces étapes mais que vous ne voyez pas les mêmes options, cela signifie que votre travail ou votre école personnalisé leur propre portail. Pour la liaison à votre portail d’authentification à plusieurs facteurs Azure, demandez à votre administrateur.


1. Se connecter à [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. Dans la partie supérieure, sélectionnez le **profil**.  
3. Sélectionnez la **vérification de sécurité supplémentaires**.  

    ![MyApps](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Charge de la page de vérification de sécurité supplémentaire avec vos paramètres.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)


## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Je veux modifier mon numéro de téléphone, ou ajouter un numéro secondaire

Il est important de configurer un numéro de téléphone secondaire de l’authentification.  Étant donné que votre numéro de téléphone principal et votre application mobile sont probablement sur le même téléphone, le numéro de téléphone secondaire est la seule façon, vous ne pourrez pas revenir à votre compte si votre téléphone est perdu ou volé.

> [AZURE.NOTE]
> Si vous n’ont accès à votre numéro de téléphone principal et avez besoin d’aide lors de l’obtention votre compte, consultez nos rubriques d’aide dans [des difficultés avec la vérification de deux étapes](multi-factor-authentication-end-user-troubleshoot.md).

**Pour modifier votre numéro de téléphone principal :**  

1. Sur la page de vérification de sécurité supplémentaires, sélectionnez la zone de texte avec votre numéro de téléphone et le modifier à nouveau votre numéro de téléphone.  
2. Cliquez sur **Enregistrer**.  
3. Si c’est le numéro que vous utilisez pour votre option préférée de vérification, vous devez vérifier le nouveau numéro avant de pouvoir l’enregistrer.  


**Pour ajouter un numéro de téléphone secondaire :**  

1. Sur la page de vérification de sécurité supplémentaires, activez la case à côté **téléphone d’autre authentification.**  
2. Entrez votre numéro de téléphone secondaire dans la zone de texte.  
3. Sélectionnez **Enregistrer** et vos modifications sont terminées.  


## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Comment nettoyer les Microsoft Authenticator de mon ancien périphérique et déplacer vers un autre ?
Lorsque vous désinstallez l’application à partir de votre appareil ou réinitialisez le périphérique, il ne supprime pas l’activation sur le back-end. Vous devez utiliser la procédure décrite dans le [passage à un nouveau périphérique](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app).

## <a name="next-steps"></a>Étapes suivantes
- Obtenir des conseils de dépannage et de l’aide sur [des problèmes avec la vérification de deux étapes](multi-factor-authentication-end-user-troubleshoot.md)
- Définir des [mots de passe app](multi-factor-authentication-end-user-app-passwords.md) pour les applications qui ne prennent pas en charge les vérification de deux étapes.
