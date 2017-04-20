<properties
    pageTitle="Azure B2C de répertoire actif : Jeton de session et configuration d’ouverture de session unique | Microsoft Azure"
    description="Jeton, de session et de connexion de configuration unique dans Azure Active Directory B2C"
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

# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure B2C de répertoire actif : Jeton, session et de configuration d’ouverture de session unique

Cette fonctionnalité vous donne un contrôle précis sur une [stratégie de base](active-directory-b2c-reference-policies.md), de :
 
1. Durées de vie des jetons de sécurité émis par Azure Active Directory (AD Azure) B2C.
2. Durées de vie des sessions d’applications web gérées par Azure AD B2C.
3. Comportement de session unique (SSO) dans plusieurs applications et stratégies dans vos clients B2C.

Vous pouvez utiliser cette fonctionnalité dans vos clients B2C comme suit :

1. Procédez comme suit pour [accéder à la lame de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Cliquez sur **stratégies de connexion**. *Remarque : vous pouvez utiliser cette fonctionnalité sur n’importe quel type de stratégie, pas seulement de* *Stratégies de connexion***.
3. Ouvrez une stratégie en cliquant dessus. Par exemple, cliquez sur **B2C_1_SiIn**.
4. Cliquez sur **Modifier** dans la partie supérieure de la lame.
5. Cliquez sur **jeton, de session et de configuration d’ouverture de session unique**.
6. Apportez les modifications voulues. Obtenir des informations sur les propriétés disponibles dans les sections suivantes.
7. Cliquez sur **OK**.
8. Cliquez sur **Enregistrer** en haut de la lame.

![Capture d’écran de jeton, de session et de configuration d’ouverture de session unique](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## <a name="token-lifetimes-configuration"></a>Configuration du jeton de durée de vie

Azure AD B2C prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-b2c-reference-protocols.md) pour activer l’accès sécurisé à des ressources protégées. Pour implémenter cette prise en charge, Azure AD B2C émet plusieurs [jetons de sécurité](active-directory-b2c-reference-tokens.md). Voici les propriétés que vous pouvez utiliser pour gérer les durées de vie des jetons de sécurité émis par Azure AD B2C :

- **Accès & ID de jetons des durées de vie (minutes)**: la durée de vie du jeton PORTEUR OAuth 2.0 permet d’accéder à une ressource protégée. Azure AD B2C émet uniquement des jetons ID en ce moment. Cette valeur s’appliquera aux jetons d’accès, lorsque nous ajoutons leur prise en charge.
   - Par défaut = 60 minutes.
   - Minimum (inclus) = 5 minutes.
   - Maximum (inclus) = 1 440 minutes.
- **Durée de vie de jeton actualisation (jours)**: la période maximale avant laquelle un jeton d’actualisation peut servir à acquérir un nouvel accès ou le jeton d’ID (et éventuellement, un nouvelle actualisation jeton, si votre application a été accordée le `offline_access` étendue).
   - Par défaut = 14 jours.
   - Minimum (inclus) = 1 jour.
   - Maximum (inclus) = 90 jours.
- **Jeton d’actualisation coulissant le long de la fenêtre (jours)**: une fois cette période de temps écoulé de l’utilisateur est obligé de s’authentifier de nouveau, quelle que soit la période de validité de la dernière actualisation jeton rachetée par l’application. Il peut être fourni uniquement si le commutateur est défini sur **DELIMITEE**. Elle doit être supérieure ou égale à la **Actualiser le jeton durée de vie (jours)** valeur. Si le commutateur a la valeur **Unbounded**, vous ne peut pas fournir une valeur spécifique.
   - Par défaut = 90 jours.
   - Minimum (inclus) = 1 jour.
   - Maximum (inclus) = 365 jours.

Voici quelques exemples de cas d’usage que vous pouvez activer à l’aide de ces propriétés :

- Permettre à un utilisateur rester connecté à une application mobile indéfiniment, tant qu’il ou elle est active en permanence dans l’application. Vous pouvez le faire en définissant le **actualisation jeton coulissante fenêtre durée de vie (jours)** passer à **Unbounded** dans votre stratégie de connexion.
- Répondre aux exigences de conformité et de sécurisation de votre secteur, en définissant la durée de vie jeton d’accès approprié.

## <a name="session-configuration"></a>Configuration de la session

Azure AD B2C prend en charge le [protocole d’authentification de connexion de OpenID](active-directory-b2c-reference-oidc.md) permettant la connexion sécurisé dans des applications web. Voici les propriétés que vous pouvez utiliser pour gérer les sessions de l’application web :

- **L’application Web durée de vie de session (minutes)**: la durée de vie du cookie de la session de B2C Azure AD stocké sur le navigateur de l’utilisateur après une authentification réussie.
   - Par défaut = 1 440 minutes.
   - Minimum (inclus) = 15 minutes.
   - Maximum (inclus) = 1 440 minutes.
- **Expiration de la session Web app**: Si ce commutateur est la valeur **absolue**, l’utilisateur est obligé de s’authentifier de nouveau après la période de temps spécifiée par au terme de la **durée de vie de session (minutes) de Web app** . Si ce commutateur est défini pour le **matériel** (le paramètre par défaut), l’utilisateur demeure signé en tant que l’utilisateur est active en permanence dans votre application web.

Voici quelques exemples de cas d’usage que vous pouvez activer à l’aide de ces propriétés :

- Répondre aux exigences de sécurité et de conformité de votre secteur en définissant la session d’application web appropriée des durées de vie.
- Forcer la ré-authentification après une période au cours de l’interaction d’un utilisateur avec une partie haute sécurité de votre application web. 

## <a name="single-sign-on-sso-configuration"></a>Configuration de session unique (SSO)

Si vous avez plusieurs applications et les stratégies dans votre client B2C, vous pouvez gérer des interactions utilisateur entre eux à l’aide de la propriété de **configuration d’ouverture de session unique** . Vous pouvez définir la propriété à l’un des paramètres suivants :

- **Clients**: il s’agit du paramètre par défaut. À l’aide de ce paramètre permet à plusieurs applications et les stratégies dans vos clients B2C de partager la même session de l’utilisateur. Par exemple, une fois qu’un utilisateur se connecte à une application, achats de Contoso, il ou elle peut également en toute transparence vous connecter à un autre une pharmacie Contoso, à y accéder.
- **Application**: vous pouvez ainsi maintenir une session utilisateur exclusivement pour une application, indépendamment des autres applications. Par exemple, si vous souhaitez que l’utilisateur de se connecter à Contoso pharmacie (avec les mêmes informations d’identification), même si il ou elle est déjà signée en achats de Contoso, une autre application sur le même B2C client. 
- **Stratégie**: vous pouvez ainsi maintenir une session utilisateur exclusivement pour une stratégie, indépendamment des applications l’utilisent. Par exemple, si l’utilisateur a déjà connecté et terminé une étape de l’authentification (AMF) facteur multiples, il ou elle peut obtenir l’accès aux parties de sécurité plus élevée de plusieurs applications tant que la session liée à la stratégie n’expire jamais.
- **Désactivé**: cela oblige l’utilisateur à exécuter via le trajet de l’ensemble des utilisateurs à chaque exécution de la stratégie. Par exemple, cela permettra à plusieurs utilisateurs de s’inscrire à votre application (dans un scénario de bureau partagé), même lorsqu’un seul utilisateur reste connecté pendant toute la durée.
