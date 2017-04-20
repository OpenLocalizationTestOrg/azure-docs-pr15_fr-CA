<properties
    pageTitle="Azure B2C de répertoire actif : Limitations et restrictions | Microsoft Azure"
    description="Une liste des limitations et restrictions avec Azure Active Directory B2C"
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

# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure B2C de répertoire actif : Limitations et restrictions

Il existe plusieurs fonctions et fonctionnalités de B2C d’Azure Active Directory (AD Azure) qui ne sont pas pris en charge. La plupart de ces problèmes et les limitations seront adressées à l’avenir, mais vous devez être conscient si vous développez des applications pour les consommateurs à l’aide d’Azure AD B2C.

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Problèmes lors de la création de clients d’Azure AD B2C

Si vous rencontrez des problèmes lors de la [Création d’un locataire Azure AD B2C](active-directory-b2c-get-started.md), consultez [créer un locataire Azure AD ou un locataire Azure AD B2C--problèmes et résolutions](active-directory-b2c-support-create-directory.md) pour obtenir des instructions.

Notez qu’il existe des connus des problèmes lorsque vous supprimez un locataire B2C existant et créez de nouveau avec le même nom de domaine. Vous devez créer un locataire B2C avec un nom de domaine différent.

## <a name="note-about-b2c-tenant-quotas"></a>Remarque sur les quotas du locataire B2C

Par défaut, le nombre d’utilisateurs dans un locataire B2C est limité à 50 000 utilisateurs. Si vous avez besoin augmenter le quota de vos clients B2C, vous devez contacter la prise en charge.

## <a name="branding-issues-on-verification-email"></a>Problèmes de courrier électronique de vérification de la personnalisation

Le courrier électronique de vérification par défaut contient des marques de Microsoft. Nous le supprimerons dans le futur. Pour le moment, vous pouvez le supprimer à l’aide de la [fonctionnalité de personnalisation de société](../active-directory/active-directory-add-company-branding.md).

## <a name="restrictions-on-applications"></a>Restrictions sur les applications

Les types d’applications suivants ne sont actuellement pas pris en charge dans Azure AD B2C. Pour obtenir une description des types d’applications pris en charge, reportez-vous à [Azure AD B2C : Types d’applications](active-directory-b2c-apps.md).

### <a name="single-page-applications-javascript"></a>Applications de Page unique (JavaScript)

De nombreuses applications modernes ont une seule Page Application (SPA) frontal qui est écrit principalement en JavaScript et souvent utilise un framework SPA (AngularJS, Ember.js, Durandal, etc.). Ce flux n’est pas encore disponible dans Azure AD B2C.

### <a name="daemons--server-side-applications"></a>Processus / côté serveur, applications

Les applications qui contiennent des processus longs ou qui fonctionnent sans la présence d’un utilisateur doivent également un moyen d’accéder aux ressources sécurisées, telles que les API Web. Ces applications peuvent s’authentifier et obtenir des jetons en utilisant l’identité de l’application (et non identité de délégué du consommateur) dans le [flux des informations d’identification des clients OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Ce flux n’est pas encore disponible dans Azure B2C de publicité, donc pour le moment, les applications peuvent obtenir des jetons qu’après qu’un flux d’ouverture de session interactive consommateur s’est produite.

### <a name="standalone-web-apis"></a>API Web d’autonome

Dans le AD B2C Azure, vous avez la possibilité de [créer une API Web qui est sécurisé à l’aide des jetons d’OAuth 2.0](active-directory-b2c-apps.md#web-apis). Toutefois, cette API Web sera uniquement en mesure de recevoir des jetons à partir d’un client qui partage le même ID d’Application. Génération d’une API Web qui est accessible à partir de plusieurs clients différents n’est pas pris en charge.

### <a name="web-api-chains-on-behalf-of"></a>Chaînes d’API de Web (de la part de)

De nombreuses architectures incluent une API Web qui doit appeler une autre API Web en aval, à la fois sécurisé par Azure AD B2C. Ce scénario est courant dans les clients natifs qui ont un API Web back-end, qui à son tour appelle un service en ligne de Microsoft comme l’API Azure AD graphique.

Ce scénario Web API chaîné peut être pris en charge à l’aide de l’octroi d’informations d’identification de OAuth 2.0 Jwt porteur, également appelé le flux de la part de. Toutefois, le flux de la part de n’est pas actuellement implémenté dans le AD B2C Azure.

## <a name="restriction-on-libraries-and-sdks"></a>Restriction sur les bibliothèques et les kits de développement logiciel

Le jeu de bibliothèques de prise en charge de Microsoft Azure AD B2C de travail est très limité en ce moment. Nous avons prise en charge pour les applications web .NET Framework en fonction et services, ainsi que les applications web NodeJS et services.  Nous avons également une aperçu la bibliothèque cliente .NET appelée MSAL qui peut être utilisé avec B2C de publicité d’Azure dans Windows et les autres applications .NET.

Nous n’avons pas actuellement prise en charge d’autres langues ou plates-formes, y compris iOS et Android de bibliothèque.  Si vous souhaitez construire sur une plate-forme autre que ceux mentionnés ci-dessus, nous vous recommandons d’à l’aide d’un kit de développement open source, en se référant à notre [Guide de référence du protocole de connexion OpenID et OAuth 2.0](active-directory-b2c-reference-protocols.md) si nécessaire.  Azure AD B2C implémente OAuth & OpenID se connecter, ce qui permet d’utiliser une bibliothèque OAuth ou OpenID connecter générique pour l’intégration.

Notre iOS et les didacticiels de Android démarrage rapide utilisent les bibliothèques open source, nous avons testé pour assurer la compatibilité avec Azure AD B2C.  Tous nos didacticiels de démarrage rapide sont disponibles dans la section consacrée à la [mise en route](active-directory-b2c-overview.md#getting-started) .

## <a name="restriction-on-protocols"></a>Restriction sur les protocoles

Azure AD B2C prend en charge les OpenID connecter et OAuth 2.0. Toutefois, certaines des fonctionnalités et des capacités de chaque protocole ont été implémentées. Pour mieux comprendre l’étendue de la fonctionnalité de protocole pris en charge dans B2C d’AD Azure, lisez notre [OpenID de se connecter et la référence au protocole OAuth 2.0](active-directory-b2c-reference-protocols.md). Prise en charge du protocole SAML et WS-Fed n’est pas disponible.

## <a name="restriction-on-tokens"></a>Restriction sur les jetons

La plupart des jetons émis par Azure AD B2C sont implémentées comme des jetons de Web JSON ou JWTs. Toutefois, toutes les informations contenues dans JWTs (appelées « réclamations ») sont tout à fait comme il convient ou est manquant. Certains exemples incluent le « sub » et les revendications de « preferred_username ».  Comme les valeurs, le format ou le sens de modification des demandes au fil du temps, des jetons pour vos stratégies existantes ne sont pas affectés, vous pouvez vous appuyer sur leurs valeurs dans les applications de production.  Lorsque les valeurs changent, nous vous donnera la possibilité de configurer ces modifications pour chacun de vos stratégies.  Pour mieux comprendre les jetons émis actuellement par le service Azure AD B2C, lisez notre [référence de jeton](active-directory-b2c-reference-tokens.md).

## <a name="restriction-on-nested-groups"></a>Restriction sur les groupes imbriqués

Appartenances aux groupes imbriqués ne sont pas pris en charge dans les locataires Azure AD B2C. Nous ne prévoyez pas d’ajouter cette fonctionnalité.

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Restriction sur la fonctionnalité de requête différentielle sur Azure AD graphique API

La [fonctionnalité de requête différentielle sur Azure AD graphique API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) n’est pas prise en charge par les locataires Azure AD B2C. Il s’agit de notre plan d’évolution à long terme.

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Problèmes liés à la gestion des utilisateurs sur le portail classique Azure

B2C fonctionnalités sont accessibles sur le portail Azure. Toutefois, vous pouvez utiliser le portail classique Azure pour accéder aux autres fonctionnalités de clients, y compris la gestion de l’utilisateur. Il existe actuellement des quelques problèmes connus liés à la gestion des utilisateurs (onglet **utilisateurs** ) sur le portail classique Azure :

- Pour un utilisateur de compte local (c'est-à-dire un consommateur qui s’inscrit avec une adresse de messagerie et mot de passe ou un nom d’utilisateur et le mot de passe), le champ **Nom d’utilisateur** ne correspond pas à l’ouverture de session identificateur (adresse de messagerie ou nom d’utilisateur) qui a été utilisée pendant l’inscription. C’est parce que le champ affiché dans Azure portal classique est en fait l’utilisateur nom Principal (UPN), qui n’est pas utilisé dans les scénarios de B2C. Pour afficher l’identificateur d’utilisateur du compte local, trouver l’objet utilisateur dans [l’Explorateur de graphique](https://graphexplorer.cloudapp.net/). Vous trouverez le même problème avec un utilisateur de compte social (c'est-à-dire, un consommateur qui s’inscrit avec Facebook, Google +, etc.), mais dans ce cas, il n’y a aucun identificateur de parler de connexion.

    ![Compte local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Pour un compte local d’utilisateur, vous ne pourrez pas modifier les champs et enregistrer les modifications apportées dans l’onglet **profil** .

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Problèmes de mot de passe initiées par l’administrateur, réinitialiser sur Azure portal classique

Si vous réinitialisez le mot de passe pour un consommateur basé sur un compte local sur le portail classique Azure (la commande **Réinitialiser le mot de passe** sous l’onglet **utilisateurs** ), que le consommateur ne sera pas en mesure de modifier son mot de passe à la prochaine ouverture de session, si vous utilisez un signe ou signez dans la stratégie et sera verrouillé hors de vos applications. Pour résoudre ce problème, utilisez l' [API d’Azure AD graphique](active-directory-b2c-devquickstarts-graph-dotnet.md) de mot de passe du consommateur (sans expiration de mot de passe) ou d’utiliser un signe dans la stratégie, au lieu d’un signe ou de signer dans la stratégie.

## <a name="issues-with-creating-a-custom-attribute"></a>Problèmes liés à la création d’un attribut personnalisé

Un [attribut personnalisé ajouté sur le portail Azure](active-directory-b2c-reference-custom-attr.md) n’est pas immédiatement créé dans vos clients B2C. Vous devrez utiliser l’attribut personnalisé dans au moins une de vos stratégies pour lui sont créés dans votre client B2C et sont disponibles via l’API de graphique.

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Problèmes avec la vérification d’un domaine sur Azure portal classique

Actuellement, vous ne peut pas vérifier un domaine avec succès sur [Azure portal classique](https://manage.windowsazure.com/).

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Problèmes de connexion avec la stratégie de l’AMF sur les navigateurs Safari

Demandes de l’échec des stratégies de connexion (avec AMF activée) par intermittence sur navigateurs Safari avec les erreurs HTTP 400 (demande incorrecte). Cela est dû les limites de taille de faible de cookies de Safari. Il existe deux solutions de contournement pour ce problème :

- Utilisez la « d’abonnement ou d’ouverture de session stratégie » au lieu de la stratégie « sign in ».
- Réduire le nombre de **demandes d’Application** demandée dans votre stratégie.
