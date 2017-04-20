<properties
    pageTitle="B2C d’Azure Active Directory : Authentification à plusieurs facteurs | Microsoft Azure"
    description="Comment faire pour activer une authentification multifactorielle dans les applications pour les consommateurs sécurisées par Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure B2C de répertoire actif : Activer une authentification multifactorielle dans vos applications pour les consommateurs

Azure B2C d’Active Directory (AD Azure) s’intègre directement avec [Azure une authentification multifacteur](../multi-factor-authentication/multi-factor-authentication.md) afin que vous pouvez ajouter un second niveau de sécurité pour les expériences d’inscription et ouverture de session dans vos applications pour les consommateurs. Et vous pouvez le faire sans écrire une seule ligne de code. Prend en charge un appel et le texte des messages vérification. Si vous déjà créé les stratégies d’inscription et de connexion, vous pouvez tout de même activer authentification à plusieurs facteurs.

> [AZURE.NOTE]
Plusieurs facteurs d’authentification peut également être activée lorsque vous créez des stratégies d’inscription et de connexion, non seulement par la modification des stratégies existantes.

Cette fonctionnalité permet aux applications de gérer des scénarios tels que les éléments suivants :

- Vous ne nécessitent pas une authentification multifactorielle pour accéder à une application, mais vous en avez besoin pour accéder à un autre. Par exemple, le consommateur peut signer dans une application d’assurance automatique avec un compte local ou social, mais il doit vérifier le numéro de téléphone avant d’accéder à l’application d’assurance domestique enregistré dans le même répertoire.
- Vous ne nécessitent pas une authentification multifactorielle pour accéder à une application en général, mais vous en avez besoin pour accéder aux parties sensibles au sein de celui-ci. Par exemple, le consommateur peut se connecter à une application bancaire avec un compte local ou social et le solde du compte à cocher, mais il doit vérifier le numéro de téléphone avant d’essayer un virement bancaire.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modifier votre stratégie d’inscription pour activer l’authentification selon plusieurs facteurs

1. [Procédez comme suit pour accéder à la lame de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies d’inscription**.
3. Cliquez sur votre stratégie d’inscription (par exemple, « B2C_1_SiUp ») pour l’ouvrir.
4. Cliquez sur **authentification à facteurs multiples** et activer l' **état** **on**. Cliquez sur **OK**.
5. Cliquez sur **Enregistrer** en haut de la lame.

Vous pouvez utiliser la fonctionnalité « Exécuter maintenant » de la stratégie pour vérifier l’expérience du consommateur. Vérifiez les points suivants :

Un compte client est créé dans votre répertoire avant l’étape d’authentification à plusieurs facteurs. Lors de l’étape, le consommateur est invité à fournir son numéro de téléphone et la vérifier. Si la vérification réussit, le numéro de téléphone est lié au compte client pour une utilisation ultérieure. Même si le client annule ou disparaît, il ou elle peut invitée à vérifier un numéro de téléphone à nouveau pendant le prochain signe-(avec une authentification multifacteur activé).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modifier votre stratégie de connexion pour activer l’authentification selon plusieurs facteurs

1. [Procédez comme suit pour accéder à la lame de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies de connexion**.
3. Cliquez sur votre stratégie ouverture de session (par exemple, « B2C_1_SiIn ») pour l’ouvrir. Cliquez sur **Modifier** dans la partie supérieure de la lame.
4. Cliquez sur **authentification à facteurs multiples** et activer l' **état** **on**. Cliquez sur **OK**.
5. Cliquez sur **Enregistrer** en haut de la lame.

Vous pouvez utiliser la fonctionnalité « Exécuter maintenant » de la stratégie pour vérifier l’expérience du consommateur. Vérifiez les points suivants :

Lorsque le client se connecte (à l’aide d’un compte local ou social), si un numéro de téléphone vérifié est connecté sur le compte du consommateur, il est invité à vérifier. Si aucun numéro de téléphone n’est connecté, le consommateur est invité à les fournir une et vérifier. Une vérification réussie, le numéro de téléphone est lié au compte client pour une utilisation ultérieure.

## <a name="multi-factor-authentication-on-other-policies"></a>Authentification à plusieurs facteurs sur les autres stratégies

Comme décrit pour les stratégies d’inscription et connexion ci-dessus, il est également possible d’activer une authentification multifactorielle sur abonnement ou stratégies de réinitialisation de mot de passe et stratégies de connexion. Il sera disponible bientôt sur les stratégies de modification du profil.
