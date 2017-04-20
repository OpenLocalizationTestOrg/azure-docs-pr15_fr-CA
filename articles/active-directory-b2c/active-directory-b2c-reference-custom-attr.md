<properties
    pageTitle="Azure B2C de répertoire actif : Attributs personnalisés | Microsoft Azure"
    description="Comment utiliser des attributs personnalisés dans Azure Active Directory B2C pour collecter des informations sur vos clients"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

#  <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure B2C de répertoire actif : Utiliser des attributs personnalisés pour collecter des informations sur vos clients

Votre répertoire B2C d’Azure Active Directory (AD Azure) est fourni avec un ensemble intégré d’informations (attributs) : prénom, nom, ville, Code Postal et autres attributs. Toutefois, toutes les applications pour les consommateurs a des exigences uniques sur les attributs à collecter auprès des consommateurs. Avec Azure B2C d’Active Directory, vous pouvez étendre l’ensemble d’attributs stockés sur chaque compte du consommateur. Vous pouvez créer des attributs personnalisés dans [Azure portal](https://portal.azure.com/) et l’utiliser dans vos stratégies d’inscription, comme illustré ci-dessous. Vous pouvez également lire et écrire ces attributs à l’aide de l' [API d’Azure AD graphique](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]
Attributs personnalisés utilisent des [Extensions de schéma du répertoire Azure AD graphique API](https://msdn.microsoft.com/library/azure/dn720459.aspx).

## <a name="create-a-custom-attribute"></a>Créer un attribut personnalisé

1. [Procédez comme suit pour accéder à la lame de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **attributs de l’utilisateur**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Fournir un **nom** pour l’attribut personnalisé (par exemple, « ShoeSize ») et, éventuellement, une **Description**. Cliquez sur **créer**.

    > [AZURE.NOTE]
    Uniquement le « Chaîne » **Type de données** n’est actuellement disponible.

L’attribut personnalisé est maintenant disponible dans la liste des **attributs de l’utilisateur**et pour une utilisation dans vos stratégies d’inscription.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Utilisez un attribut personnalisé dans votre stratégie d’inscription

1. [Procédez comme suit pour accéder à la lame de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies d’inscription**.
3. Cliquez sur votre stratégie d’inscription (par exemple, « B2C_1_SiUp ») pour l’ouvrir. Cliquez sur **Modifier** dans la partie supérieure de la lame.
4. Cliquez sur **attributs d’abonnement** et sélectionnez l’attribut personnalisé (par exemple, « ShoeSize »). Cliquez sur **OK**.
5. Cliquez sur les **demandes de l’Application** et sélectionnez l’attribut personnalisé. Cliquez sur **OK**.
6. Cliquez sur **Enregistrer** en haut de la lame.

Vous pouvez utiliser la fonctionnalité « Exécuter maintenant » de la stratégie pour vérifier l’expérience du consommateur. Vous devez maintenant voir « ShoeSize » dans la liste des attributs collectés au cours de l’inscription du consommateur et apparaît dans le jeton envoyé à votre application.

## <a name="notes"></a>Notes

- Ainsi que les stratégies d’inscription, les attributs personnalisés peuvent également être utilisés dans les stratégies d’inscription ou d’ouverture de session et les stratégies de modification du profil.
- Il existe une limitation connue des attributs personnalisés. Il est uniquement créé la première fois qu’il est utilisé dans toutes les stratégies, et pas lorsque vous l’ajoutez à la liste des **attributs de l’utilisateur**.
