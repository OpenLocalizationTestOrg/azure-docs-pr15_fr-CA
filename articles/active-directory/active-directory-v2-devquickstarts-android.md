<properties
    pageTitle="Application de Android Azure Active Directory v2.0 | Microsoft Azure"
    description="Comment générer une application d’Android qui signe les utilisateurs avec personnel compte Microsoft et travail ou les comptes de l’établissement et appels de l’API de graphique à l’aide de bibliothèques de tiers."
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

#  <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Ajoutez connexion à une application d’Android à l’aide d’une bibliothèque tierce avec les API de graphique à l’aide de l’extrémité de la version 2.0

La plate-forme d’identité de Microsoft utilisant des normes ouvertes telles que OAuth2 et OpenID se connecter. Les développeurs peuvent utiliser n’importe quelle bibliothèque qu’ils souhaitent intégrer grâce à nos services. Pour aider les développeurs à utiliser notre plate-forme avec d’autres bibliothèques, nous avons écrit quelques procédures pas à pas comme celui-ci pour montrer comment configurer des bibliothèques tierces pour se connecter à la plate-forme d’identité de Microsoft. La plupart des bibliothèques qui implémentent [la spécification RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) pouvez vous connecter à la plate-forme d’identité de Microsoft.

Avec l’application qui crée de cette procédure pas à pas, les utilisateurs peuvent se connecter à leur organisation et puis rechercher eux-mêmes dans leur organisation à l’aide de l’API du graphique.

Si vous êtes novice dans OAuth2 ou OpenID de se connecter, de cet exemple de configuration ne peut pas de sens pour vous. Nous vous conseillons de lire [les 2.0 protocoles - flux de Code OAuth 2.0 d’autorisation](active-directory-v2-protocols-oauth-code.md) pour l’arrière-plan.

> [AZURE.NOTE] Certaines fonctionnalités de notre plate-forme qui n’ont pas une expression dans les normes OAuth2 ou OpenID de se connecter, par exemple l’accès conditionnel et de gestion des stratégies de Intune vous obligent à utiliser nos bibliothèques d’identité Microsoft Azure ouvrir la source.

Le point de terminaison v2.0 ne supporte pas toutes les fonctionnalités et scénarios d’Azure Active Directory.

> [AZURE.NOTE] Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).


## <a name="download-the-code-from-github"></a>Télécharger le code à partir de GitHub
Le code de ce didacticiel est maintenue [sur GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Pour suivre l’exemple, vous pouvez [Télécharger le squelette de l’application sous la forme d’un fichier zip](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) ou cloner le squelette :

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Vous pouvez également téléchargez l’exemple et commencez dès maintenant :

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application sur le [portail de l’enregistrement d’Application](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez les étapes détaillées à [l’enregistrement d’une application avec le point de terminaison v2.0](active-directory-v2-app-registration.md).  Veillez à :

- Copiez l' **Id de l’Application** qui est assigné à votre application, car vous en aurez besoin plus rapidement.
- Ajouter la plate-forme **Mobile** pour votre application.

> Remarque : Le portail de l’enregistrement d’Application fournit une valeur **d’URI de redirection** . Toutefois, dans cet exemple, vous devez utiliser la valeur par défaut de `https://login.microsoftonline.com/common/oauth2/nativeclient`.


## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Télécharger la bibliothèque tierce NXOAuth2 et créer un espace de travail

Pour cette procédure pas à pas, vous allez utiliser le OIDCAndroidLib de GitHub, qui est une bibliothèque de OAuth2 basée sur le code OpenID connecter de Google. Il implémente le profil de l’application d’origine et le point de terminaison de l’autorisation de l’utilisateur. Il s’agit de tout ce dont vous aurez besoin d’intégrer à la plate-forme d’identité de Microsoft.

Cloner le mis en pension OIDCAndroidLib sur votre ordinateur.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Configurer votre environnement de Studio Android

1. Créez un nouveau projet de Studio Android et acceptez les valeurs par défaut dans l’Assistant.

    ![Créez un nouveau projet dans Studio Android](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

    ![Cible des appareils Android](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

    ![Ajouter une activité à mobile](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

2. Pour configurer les modules de votre projet, déplacez le mis en pension cloné à l’emplacement du projet. Vous pouvez également créer le projet et puis clonez-le directement à l’emplacement du projet.

    ![Modules projet](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

3. Ouvrez les paramètres de modules du projet à l’aide du menu contextuel, ou en utilisant le raccourci Ctrl + Alt + Maj + S.

    ![Paramètres des modules projet](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

4. Supprimer le module d’application par défaut car vous souhaitez uniquement que les paramètres du projet conteneur.

    ![Le module d’application par défaut](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

5. Importer les modules dans le mis en pension cloné au projet en cours.

    ![Importer le projet gradle](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG)
    ![créer une nouvelle page de module](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

6. Répétez ces étapes pour le `oidlib-sample` module.

7. Vérifiez les dépendances oidclib de le `oidlib-sample` module.

    ![dépendances d’oidclib sur l’oidlib-exemple de module](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

8. Cliquez sur **OK** et attendre la synchronisation gradle.

    Votre settings.gradle doit ressembler à :

    ![Capture d’écran de settings.gradle](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

9. Générer l’exemple d’application pour vous assurer que l’exemple fonctionne correctement.

    Vous ne pourrez pas utiliser encore avec Azure Active Directory. Nous avons besoin configurer des points de terminaison en premier. C’est pour que vous n’ayez un problèmes Android Studio avant de commencer la personnalisation de l’exemple d’application.

10. Générer et exécuter `oidlib-sample` comme cible dans Studio Android.

    ![Progression de la génération de l’exemple de l’oidlib](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

11. Supprimer le `app ` répertoire qui a été laissé lorsque vous retirez le module du projet car Android Studio ne le supprime pas de la sécurité.

    ![Structure de fichier qui inclut le répertoire de l’application](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

12. Ouvrir le menu **Modifier les Configurations** pour supprimer la configuration d’exécution qui était également lorsque vous retirez le module du projet.

    ![Modifier le menu configurations](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![exécuter la configuration de l’application](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Configurer les points de terminaison de l’échantillon

Maintenant que vous avez les `oidlib-sample` fonctionne correctement, nous allons modifier les quelques points de terminaison pour que cela fonctionne correctement avec Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Configurer votre client en modifiant le fichier oidc_clientconf.xml

1. Car vous utilisez OAuth2 flux uniquement pour obtenir un jeton et appeler l’API graphique, définir le client pour faire OAuth2 uniquement. OIDC entreront dans un exemple d’une version ultérieure.

    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```

2. Configurer votre ID client que vous avez reçu à partir du portail de l’enregistrement.

    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```

3. Configurer votre URI de redirection avec celui ci-dessous.

    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```

4. Configurer vos étendues dont vous avez besoin pour accéder à l’API de graphique.

    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

Le `User.Read` valeur dans `oidc_scopes` vous permet de lire le profil de base signées de l’utilisateur.
Pour plus d’informations sur toutes les étendues disponibles dans des [portées d’autorisation de Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Si vous souhaitez que des explications sur les `openid` ou `offline_access` comme étendues dans OpenID se connecter, voir [2.0 protocoles - flux du Code d’autorisation OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Configurer des points de terminaison de votre client en modifiant le fichier oidc_endpoints.xml

- Ouvrir le `oidc_endpoints.xml` de fichiers et apporter les modifications suivantes :

    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

Ces points de terminaison ne doivent jamais changer si vous utilisez OAuth2 comme protocole.

> [AZURE.NOTE]
Les points de terminaison de `userInfoEndpoint` et `revocationEndpoint` ne sont actuellement pas pris en charge par Active Directory de Azure. Si vous laissez ces zones de la valeur par défaut exemple.com, vous rappellera qu’ils ne sont pas disponibles dans l’échantillon  :-)


## <a name="configure-a-graph-api-call"></a>Configurer un appel API de graphique

- Ouvrir le `HomeActivity.java` de fichiers et apporter les modifications suivantes :

    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

Dans ce cas, un simple appel d’API du graphique retourne nos informations.

Ce sont toutes les modifications que vous devez faire. Exécuter le `oidlib-sample` application, cliquez sur **connexion**.

Une fois que vous avez été authentifié avec succès, cliquez sur le bouton de **Demande de ressource protégée** pour tester votre appel à l’API de graphique.

## <a name="get-security-updates-for-our-product"></a>Obtenir des mises à jour de sécurité pour notre produit

Nous vous encourageons à obtenir des notifications sur les incidents de sécurité en visitant le [Centre de sécurité TechNet](https://technet.microsoft.com/security/dd252948) et s’abonner à l’alerte de sécurité.
