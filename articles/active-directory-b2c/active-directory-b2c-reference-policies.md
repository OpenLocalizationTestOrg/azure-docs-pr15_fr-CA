<properties
    pageTitle="Azure B2C de répertoire actif : Infrastructure de stratégie Extensible | Microsoft Azure"
    description="Une rubrique dans le cadre de la stratégie extensible d’Azure Active Directory B2C et comment créer différents types de stratégie"
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

# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure B2C de répertoire actif : Infrastructure de stratégie Extensible

## <a name="the-basics"></a>Les notions de base

Le cadre de la stratégie extensible de B2C d’Azure Active Directory (AD Azure) est le niveau principal du service. Stratégies de décrivent complètement les expériences d’identité consommateur comme d’abonnement, la connexion ou profil de modification. Par exemple, une stratégie d’inscription vous permet de contrôler les comportements en configurant les paramètres suivants :

- Types de comptes (comptes sociaux tels que Facebook, ou comptes locaux comme adresse e-mail) que les consommateurs peuvent utiliser pour s’inscrire pour l’application.
- Attributs (par exemple, nom, code postal et pointure) à collecter à partir de la consommation pendant l’inscription.
- Utilisation de l’authentification à plusieurs facteurs.
- L’aspect et l’apparence de toutes les pages d’inscription.
- Informations (qui se manifeste en tant que revendications dans un jeton) que l’application reçoit lorsque la stratégie d’exécution se termine.

Vous pouvez créer plusieurs stratégies de types différents de vos clients et les utiliser dans vos applications, selon vos besoins. Les stratégies peuvent être réutilisées entre les applications. Cela permet aux développeurs de définir et de modifier les expériences d’identité consommateur avec très peu ou aucune modification de leur code.

Les stratégies sont disponibles pour utilisation via une interface simple développeur. Votre application déclenche une stratégie en utilisant une demande d’authentification HTTP standard (passage d’un paramètre de stratégie dans la demande) et reçoit un jeton personnalisé en tant que réponse. Par exemple, la seule différence entre les demandes de l’appel à une stratégie d’inscription et celles de l’appel à une stratégie d’authentification sont le nom de stratégie utilisé dans le paramètre de chaîne de requête « p » :

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Pour plus d’informations sur l’infrastructure de stratégie, consultez ce [blog valider](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Créer une stratégie d’inscription

Pour activer l’inscription de votre application, vous devez créer une stratégie d’inscription. Cette stratégie décrit les expériences qui passe par les consommateurs pendant l’inscription et le contenu de jetons qui l’application recevra sur inscriptions réussies.

1. [Procédez comme suit pour accéder à la lame de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies d’inscription**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Le **nom** détermine le nom de la stratégie d’inscription utilisé par votre application. Par exemple, entrez « SiUp ».
5. Cliquez sur **fournisseurs d’identité** et sélectionnez « Courrier électronique d’abonnement ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d’identité sociale, si déjà configuré. Cliquez sur **OK**.
6. Cliquez sur **attributs d’abonnement**. Ici, vous choisissez les attributs que vous souhaitez collecter à partir de la consommation pendant l’inscription. Par exemple, sélectionnez « Pays », « Nom complet » et « Code Postal ». Cliquez sur **OK**.
7. Cliquez sur **demandes de l’Application**. Ici, vous choisissez les demandes que vous souhaitez dans les jetons envoyés à votre application après une expérience d’inscription réussie. Par exemple, sélectionnez « Nom complet », « Fournisseur d’identité », « Code Postal », « Utilisateur est nouvelle » et « ID d’objet de l’utilisateur ».
8. Cliquez sur **créer**. Notez que la stratégie juste créée apparaît comme «**B2C_1_SiUp**» (le **B2C\_1\_ ** fragment est automatiquement ajouté) dans la lame de **stratégies d’inscription** .
9. Ouvrez la stratégie en cliquant sur «**B2C_1_SiUp**».
10. Sélectionnez « Contoso B2C application » dans la liste déroulante des **Applications** et `https://localhost:44321/` dans le **URL de réponse / URI de redirection** liste déroulante.
11. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter par l’intermédiaire de l’expérience du consommateur d’inscription pour votre application.

    > [AZURE.NOTE]
    Il occupe une minute pour la création de stratégies et mises à jour prennent effet.

## <a name="create-a-sign-in-policy"></a>Créer une stratégie d’authentification

Pour activer la connexion de votre application, vous devez créer une stratégie d’authentification. Cette stratégie décrit les expériences que les consommateurs devront suivre lors de l’ouverture de session et le contenu de l’application recevra les jetons sur les connexions réussies.

1. [Procédez comme suit pour accéder à la lame de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies de connexion**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Le **nom** détermine le nom de la stratégie de connexion utilisé par votre application. Par exemple, entrez « SiIn ».
5. Cliquez sur **fournisseurs d’identité** et sélectionnez « Connexion de compte Local ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d’identité sociale, si déjà configuré. Cliquez sur **OK**.
6. Cliquez sur **demandes de l’Application**. Ici, vous choisissez les demandes que vous souhaitez dans les jetons envoyés à votre application après une expérience de connexion réussie. Par exemple, sélectionnez « Nom complet », « Fournisseur de l’identité », « Code Postal » et « ID d’objet de l’utilisateur ». Cliquez sur **OK**.
7. Cliquez sur **créer**. Notez que la stratégie juste créée apparaît comme «**B2C_1_SiIn**» (le **B2C\_1\_ ** fragment est automatiquement ajouté) dans la lame de **stratégies d’authentification** .
8. Ouvrez la stratégie en cliquant sur «**B2C_1_SiIn**».
9. Sélectionnez « Contoso B2C application » dans la liste déroulante des **Applications** et `https://localhost:44321/` dans le **URL de réponse / URI de redirection** liste déroulante.
10. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter par l’intermédiaire de l’expérience du consommateur de la signature dans votre application.

    > [AZURE.NOTE]
    Il occupe une minute pour la création de stratégies et mises à jour prennent effet.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Créer une stratégie d’inscription ou d’ouverture de session

Cette stratégie gère à la fois consommateur d’inscription et de connexion des expériences avec une configuration unique. Les consommateurs sont dirigées vers le bas le chemin d’accès (inscription ou ouverture de session) en fonction du contexte. Il décrit également le contenu de jetons qui l’application recevra les inscriptions réussies ou des connexions.  Un exemple de code pour la stratégie d’inscription ou d’ouverture de session est [disponible ici](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

1. [Procédez comme suit pour accéder à la lame de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies d’abonnement ou d’ouverture de session**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Le **nom** détermine le nom de la stratégie d’inscription utilisé par votre application. Par exemple, entrez « SiUpIn ».
5. Cliquez sur **fournisseurs d’identité** et sélectionnez « Courrier électronique d’abonnement ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d’identité sociale, si déjà configuré. Cliquez sur **OK**.
6. Cliquez sur **attributs d’abonnement**. Ici, vous choisissez les attributs que vous souhaitez collecter à partir de la consommation pendant l’inscription. Par exemple, sélectionnez « Pays », « Nom complet » et « Code Postal ». Cliquez sur **OK**.
7. Cliquez sur **demandes de l’Application**. Ici, vous choisissez les demandes que vous souhaitez dans les jetons envoyés à votre application après une expérience d’abonnement ou d’ouverture de session réussie. Par exemple, sélectionnez « Nom complet », « Fournisseur d’identité », « Code Postal », « Utilisateur est nouvelle » et « ID d’objet de l’utilisateur ».
8. Cliquez sur **créer**. Notez que la stratégie juste créée apparaît comme «**B2C_1_SiUpIn**» (le **B2C\_1\_ ** fragment est automatiquement ajouté) dans la lame de **stratégies d’inscription ou de connexion** .
9. Ouvrez la stratégie en cliquant sur «**B2C_1_SiUpIn**».
10. Sélectionnez « Contoso B2C application » dans la liste déroulante des **Applications** et `https://localhost:44321/` dans le **URL de réponse / URI de redirection** liste déroulante.
11. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter grâce à l’expérience d’inscription ou de connexion de consommateur, tel que configuré.

    > [AZURE.NOTE]
    Il occupe une minute pour la création de stratégies et mises à jour prennent effet.

## <a name="create-a-profile-editing-policy"></a>Créer un stratégie de modification du profil

Pour permettre à votre application de modification du profil, vous devez créer un stratégie de modification du profil. Cette stratégie décrit les expériences qui passe par les consommateurs lors de la modification de profils et le contenu de jetons qui l’application recevra de réussite.

1. [Procédez comme suit pour accéder à la lame de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies de modification du profil**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Le **nom** détermine le nom de la stratégie utilisée par votre application de modification du profil. Par exemple, entrez « LAMELLE ».
5. Cliquez sur **fournisseurs d’identité** et sélectionnez « Adresse E-mail ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d’identité sociale, si déjà configuré. Cliquez sur **OK**.
6. Cliquez sur **attributs de profil**. Ici, vous choisissez les attributs que le consommateur peut afficher et modifier. Par exemple, sélectionnez « Pays », « Nom complet » et « Code Postal ». Cliquez sur **OK**.
7. Cliquez sur **demandes de l’Application**. Ici, vous choisissez les demandes que vous souhaitez dans les jetons envoyés à votre application après une expérience de modification du profil réussie. Par exemple, sélectionnez « Nom complet » et « Code Postal ».
8. Cliquez sur **créer**. Notez que la stratégie juste créée apparaît comme «**B2C_1_SiPe**» (le **B2C\_1\_ ** fragment est automatiquement ajouté) dans la lame de **stratégies de modification de profils** .
9. Ouvrez la stratégie en cliquant sur «**B2C_1_SiPe**».
10. Sélectionnez « Contoso B2C application » dans la liste déroulante des **Applications** et `https://localhost:44321/` dans le **URL de réponse / URI de redirection** liste déroulante.
11. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter par l’intermédiaire de l’expérience du consommateur dans votre application de modification du profil.

    > [AZURE.NOTE]
    Il occupe une minute pour la création de stratégies et mises à jour prennent effet.
    
## <a name="create-a-password-reset-policy"></a>Créer une stratégie de réinitialisation de mot de passe

Pour activer le mot de passe affiné réinitialiser sur votre application, vous devez créer une stratégie de réinitialisation de mot de passe. Notez que le mot de passe à l’échelle du locataire réinitialiser option spécifiée [ici](active-directory-b2c-reference-sspr.md) est toujours applicable pour les stratégies de connexion. Cette stratégie décrit les expériences qui passe par les consommateurs lors de la réinitialisation de mot de passe et le contenu de jetons qui l’application recevra de réussite.

1. [Procédez comme suit pour accéder à la lame de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies de réinitialisation de mot de passe**.
3. Cliquez sur **+ Ajouter** en haut de la lame.
4. Le **nom** détermine que le mot de passe réinitialiser le nom de la stratégie utilisée par votre application. Par exemple, entrez « SSPR ».
5. Cliquez sur **fournisseurs d’identité** et sélectionnez « Réinitialiser le mot de passe à l’aide d’adresse de messagerie ». Cliquez sur **OK**.
6. Cliquez sur **demandes de l’Application**. Ici, vous choisissez les demandes que vous souhaitez dans les jetons envoyés à votre application après la réinitialisation d’un mot de passe réussie expérience. Par exemple, sélectionnez « ID d’objet de l’utilisateur ».
7. Cliquez sur **créer**. Notez que la stratégie juste créée apparaît comme «**B2C_1_SSPR**» (le **B2C\_1\_ ** fragment est automatiquement ajouté) dans la lame de **stratégies de réinitialisation de mot de passe** .
8. Ouvrez la stratégie en cliquant sur «**B2C_1_SSPR**».
9. Sélectionnez « Contoso B2C application » dans la liste déroulante des **Applications** et `https://localhost:44321/` dans le **URL de réponse / URI de redirection** liste déroulante.
10. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter par l’intermédiaire de l’expérience de consommateur de réinitialisation de mot de passe dans votre application.

    > [AZURE.NOTE]
    Il occupe une minute pour la création de stratégies et mises à jour prennent effet.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Jeton, de session et de configuration d’ouverture de session unique](active-directory-b2c-token-session-sso.md).
