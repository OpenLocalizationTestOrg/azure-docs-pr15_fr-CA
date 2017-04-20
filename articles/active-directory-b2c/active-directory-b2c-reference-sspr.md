<properties
    pageTitle="Azure B2C de répertoire actif : Réinitialisation de mot de passe libre-service | Microsoft Azure"
    description="Une rubrique qui expliquent comment définir mot de passe libre-service, réinitialiser permettant à vos consommateurs dans Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure B2C de répertoire actif : Définir mot de passe libre-service, permettant à vos consommateurs de réinitialisation

Avec la fonction de réinitialisation de mot de passe libre-service, vos consommateurs (qui ont souscrit pour les comptes locaux) peuvent réinitialiser leurs mots de passe eux-mêmes. Cela réduit considérablement la charge de votre équipe de support, surtout si votre application a des millions de consommateurs, l’utiliser sur une base régulière. Actuellement, nous ne prennent en charge à l’aide d’une adresse e-mail vérifié comme une méthode de récupération. Nous allons ajouter des méthodes de récupération supplémentaires (numéro de téléphone vérifiés, les questions de sécurité, etc.) à l’avenir.

> [AZURE.NOTE]
Cet article s’applique à un mot de passe utilisée dans le cadre d’une stratégie de réinitialisation. Si vous devez entièrement personnalisables mot de passe réinitialisé stratégies appelés à partir de votre application, consultez [cet article](./active-directory-b2c-reference-policies.md#create-a-password-reset-policy).

Par défaut, votre répertoire n’aura pas le mot de passe libre-service réinitialisation est activée. Pour l’activer, utilisez les étapes suivantes :

1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com/) comme l’administrateur d’abonnement. C’est le même travail ou le compte de l’établissement ou le même compte Microsoft que vous avez utilisé pour créer votre répertoire.
2. Accédez à l’extension de Active Directory sur la barre de navigation sur le côté gauche.
3. Trouver votre répertoire sous l’onglet **répertoire** , puis cliquez dessus.
4. Cliquez sur l’onglet **configurer** .
5. Faites défiler jusqu'à la section **stratégie de réinitialisation de mot de passe utilisateur** et activer l’option **Réinitialiser les utilisateurs de mot de passe** à **Oui**. Notez que l’option **Autre adresse de messagerie** est activée ; laisser tel quel.

    ![Réinitialisation du mot de passe libre-service](./media/active-directory-b2c-reference-sspr/sspr.png)

6. Cliquez sur **Enregistrer** en bas de la page. Vous avez terminé !

Pour tester, utilisez la fonctionnalité « Exécuter maintenant » sur toute stratégie de connexion qui possède des comptes locaux en tant que fournisseur d’identité. Dans le compte local signe-page (où vous entrez une adresse de messagerie et mot de passe ou un nom d’utilisateur et le mot de passe), cliquez sur **ne peut pas accéder à votre compte ?** pour vérifier l’expérience du consommateur.

> [AZURE.NOTE]
Les pages de réinitialisation de mot de passe libre-service peuvent être personnalisés à l’aide de la [fonctionnalité de personnalisation de société](../active-directory/active-directory-add-company-branding.md).
