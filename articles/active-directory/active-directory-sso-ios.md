<properties
    pageTitle="Comment faire pour activer l’authentification unique entre-app sur iOS à l’aide de ADAL | Microsoft Azure"
    description="Comment utiliser les fonctionnalités du SDK ADAL pour activer l’authentification unique au sein de vos applications. "
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Comment faire pour activer l’authentification unique entre-app sur iOS à l’aide de ADAL


Fournissant Single Sign-On (SSO) afin que les utilisateurs devront uniquement entrer leurs informations d’identification une fois et que ces informations d’identification automatiquement traverser les applications est maintenant attendu par les clients. La difficulté à entrer leur nom d’utilisateur et le mot de passe sur un petit écran, souvent fois combinés avec un facteur supplémentaire (2FA) comme un appel téléphonique ou d’un code texted, résultats de mécontentement rapide si un utilisateur a pour cela plus d’une fois pour votre produit.

En outre, si vous utilisez une plate-forme d’identité autres applications susceptibles d’utiliser Microsoft Accounts ou d’un compte à partir d’Office 365, les clients attendent que les informations d’identification pour pouvoir l’utiliser sur toutes leurs applications, quel que soit le fournisseur.

La plate-forme Microsoft Identity, ainsi que nos kits de développement d’identité de Microsoft, fait partie de ce travail pour vous et vous donne la possibilité de satisfaire pleinement à vos clients avec l’authentification unique dans votre propre suite d’applications ou, comme notre capacité de service broker et des applications de l’authentificateur, sur l’ensemble de l’unité.

Cette procédure pas à pas vous indique comment configurer notre kit de développement logiciel au sein de votre application pour fournir cet avantage pour vos clients.

Cette procédure s’applique à :

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Accès conditionnel d’Azure Active Directory


Notez que le document ci-dessous suppose que vous savez à [disposition des applications dans le portail hérité pour Azure Active Directory](./develop/active-directory-how-to-integrate.md) ainsi qu’avez intégré de votre application avec l' [identité de Microsoft iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Concepts de l’authentification unique dans la plate-forme d’identité de Microsoft

### <a name="microsoft-identity-brokers"></a>Courtiers d’identité de Microsoft

Microsoft fournit des applications pour toutes les plateformes mobiles permettant le pontage d’informations d’identification entre les applications provenant de différents fournisseurs en permettant à des fonctionnalités améliorées spéciale qui nécessitent un seul emplacement sécurisé d'où valider les informations d’identification. Nous appelons ces **courtiers**. Sur iOS et Android, ceux-ci sont fournis par le biais des applications téléchargeables que clients installer indépendamment ou peuvent être envoyés au périphérique par une société qui gère tout ou partie de l’équipement pour leurs employés. Ces courtiers prennent en charge la gestion de la sécurité pour certaines applications ou de l’ensemble de l’unité en fonction de ce que les administrateurs souhaitent. Dans Windows, cette fonctionnalité est fournie par un sélecteur de compte intégré dans le système d’exploitation, appelé point de vue technique comme le courtier d’authentification Web.

Pour comprendre comment nous utilisons ces courtiers et comment vos clients peuvent les voir dans leur flux de connexion pour la plate-forme Microsoft Identity continuez la lecture pour plus d’informations.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Modèles pour la connexion sur des périphériques mobiles

Accès aux informations d’identification sur les périphériques suivez les deux modèles de base pour la plate-forme Microsoft Identity :

* Non-Service Broker pour les connexions d’accès assistées
* Assisté de connexions de Broker

#### <a name="non-broker-assisted-logins"></a>Non-Service Broker pour les connexions d’accès assistées

Non-Service Broker pour les connexions d’accès assistées sont les expériences de connexion qui se produisent en ligne avec l’application et utilisent le stockage local sur le périphérique pour cette application. Ce système de stockage peut être partagé entre les applications, mais les informations d’identification sont étroitement liées à l’application ou d’une suite d’applications à l’aide de ces informations d’identification. C’est l’expérience que vous avez probablement rencontré dans de nombreuses applications mobiles, qui vous permet de saisir un nom d’utilisateur et le mot de passe dans l’application elle-même.

Ces connexions ont les avantages suivants :

-  Expérience utilisateur existe entièrement dans l’application.
-  Informations d’identification peuvent être partagées entre les applications qui sont signées par le même certificat, en fournissant un environnement d’authentification unique à votre suite d’applications.
-  Contrôle autour de l’expérience de connexion est fourni à l’application avant et après la connexion.

Ces connexions ont les inconvénients suivants :

- Utilisateur ne peut pas rencontrer de connexion unique entre toutes les applications qui utilisent un Microsoft Identity, uniquement à travers ces Identities Microsoft possède de votre application et avez configuré.
- Votre application ne peut pas être utilisé avec des fonctionnalités plus avancées telles que l’accès conditionnel business ou utiliser les produits de la suite InTune.
- Votre application ne peut pas prendre en charge authentification par certificat pour les utilisateurs professionnels.

Voici une représentation du fonctionnement des kits de développement logiciel Microsoft Identity avec le stockage partagé de vos applications pour activer l’authentification unique :

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Assisté de connexions de Broker

Assistée par service Broker pour les connexions d’accès sont les expériences d’ouverture de session qui se produisent au sein de l’application de service broker et utilisent le stockage et la sécurité du service broker pour partager des informations d’identification pour toutes les applications qui tirent parti de la plate-forme Microsoft Identity sur le périphérique. Cela signifie que vos applications reposera sur le service broker pour vous connecter aux utilisateurs. Sur iOS et Android, ceux-ci sont fournis par le biais des applications téléchargeables que clients installer indépendamment ou peuvent être envoyés au périphérique par une société qui gère le périphérique pour leurs utilisateurs. Un exemple de ce type d’application est l’application Azure authentificateur sur iOS. Dans Windows, cette fonctionnalité est fournie par un sélecteur de compte intégré dans le système d’exploitation, appelé point de vue technique comme le courtier d’authentification Web.
L’expérience varie selon la plate-forme et peut parfois être dangereuses pour les utilisateurs si elle n’est pas géré correctement. Vous connaissez probablement plus ce modèle si vous avez l’application Facebook installée et que vous utilisez les fonctionnalités de connexion de Facebook dans une autre application. La plate-forme Microsoft Identity s’appuie sur le même modèle.

Pour iOS, que cela conduit à une « transition » animation où votre application est envoyée à l’arrière-plan pendant que les applications Azure authentificateur est au premier plan pour l’utilisateur de sélectionner le compte sur lequel il souhaite se connecter à l’aide.  

Pour Android et Windows, le sélecteur de compte s’affiche sur votre application qui est moins dangereuses pour l’utilisateur.

#### <a name="how-the-broker-gets-invoked"></a>Comment service broker est appelé

Si un agent compatible est installé sur le périphérique, telles que l’application Azure authentificateur, kits de développement logiciel Microsoft Identity automatiquement effectuera le travail de l’appel du service broker pour vous lorsqu’un utilisateur indique qu’ils souhaitent se connecter à l’aide d’un compte à partir de la plate-forme Microsoft Identity. Il peut s’agir d’un Account Microsoft, un travail ou le compte de l’établissement, ou le compte que vous fournissez et hôte dans Azure à l’aide de nos produits B2C et B2B. À l’aide d’algorithmes de chiffrement et extrêmement sécurisés, nous assurer que les informations d’identification sont demandées et remies à votre application de manière sécurisée. Le détail technique exact de ces mécanismes n’est pas publié mais ont été développés en collaboration par Apple et Google.

**Le développeur a le choix entre si le Kit de développement Microsoft Identity appelle le courtier ou utilise le flux assisté non-broker.** Cependant si le développeur choisit de ne pas utiliser le flux assistés par service broker ils perdent le bénéfice de l’utilisation des informations d’identification de l’authentification unique que l’utilisateur a peut-être déjà ajouté sur le périphérique ainsi qu’empêche leur application d’être utilisés avec les fonctionnalités d’entreprise que Microsoft offre à ses clients, tels que l’accès conditionnel, les fonctions de gestion de Intune, et l’authentification basée sur des certificats.

Ces connexions ont les avantages suivants :

-  Utilisateur rencontre SSO sur toutes leurs applications, quel que soit le fournisseur.
-  Votre application peut tirer parti des fonctionnalités d’entreprise plus avancées telles que l’accès conditionnel ou utilisez la suite InTune de produits.
-  Votre application peut prendre en charge authentification par certificat pour les utilisateurs professionnels.
- Expérience de connexion plus sécurisée que l’identité de l’application et l’utilisateur sont vérifiées par l’application de service broker avec chiffrement et les algorithmes de sécurité supplémentaires.

Ces connexions ont les inconvénients suivants :

- Dans iOS l’utilisateur passe de l’expérience de votre application alors que les informations d’identification sont choisies.
- Perte de la capacité de gérer l’expérience de connexion pour vos clients dans votre application.



Voici une représentation du fonctionnement des kits de développement logiciel Microsoft Identity avec les applications service broker pour activer l’authentification unique :

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Grâce à ces informations de fond que vous devez être en mesure de mieux comprendre et implémenter l’authentification unique au sein de votre application à l’aide de la plate-forme de Microsoft Identity et les kits de développement logiciel.


## <a name="enabling-cross-app-sso-using-adal"></a>L’activation de l’authentification unique entre-app à l’aide de ADAL

Ici, nous allons utiliser le Kit de développement logiciel d’iOS ADAL à :

- Activer le service Broker pour les non assistée par l’authentification unique pour votre suite d’applications
- Activer la prise en charge assistée par l’intermédiaire de l’authentification unique

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Activation de l’authentification unique pour le service Broker pour les non assistée par l’authentification unique

Pour l’authentification unique assisté de non-broker entre applications kits de développement logiciel Microsoft Identity gèrent la complexité de la technologie SSO pour vous. Cela inclut la recherche de l’utilisateur dans le cache et la mise à jour une liste des utilisateurs connectés pour interroger.

Pour activer l’authentification unique entre les applications que vous possédez que vous devez effectuer les opérations suivantes :

1. Assurez-vous que tous les utilisateurs de vos applications le même ID de Client ou code d’Application.
* S’assurer que toutes les applications partagent le même certificat de signature à partir d’Apple afin que vous puissiez partager les trousseaux
* Demander le droit de trousseau même pour chacune de vos applications.
* Précisez les kits de développement logiciel de Microsoft Identity sur la chaîne de clé partagée à utiliser.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>En utilisant le même ID de Client / ID d’Application pour toutes les applications dans votre suite d’applications

Dans l’ordre pour la plate-forme Microsoft Identity de savoir qu’elle a autorisés à partager des jetons dans vos applications, chacune de vos applications devrez partagent le même ID de Client ou code d’Application. Il s’agit de l’identificateur unique qui vous a été fourni lorsque vous avez enregistré votre première application dans le portail.

Vous vous demandez peut-être comment vous allez identifier les différentes applications au service Microsoft Identity s’il utilise le même ID d’Application. La réponse est l' **URI de redirection**. Chaque application peut avoir plusieurs URI rediriger inscrit sur le portail d’intégration. Chaque application de votre suite a un autre URI de redirection. Un exemple de comment cela est inférieur à :

L’URI de redirection App1 :`x-msauth-mytestiosapp://com.myapp.mytestapp`

L’URI de redirection App2 :`x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 l’URI de redirection :`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Celles-ci sont imbriquées sous le même ID de client / ID d’application et recherchée en fonction de l’URI vous revenez à nous dans votre configuration du Kit de développement logiciel de redirection.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Notez que le format de ces URI rediriger sont décrites ci-dessous. Vous pouvez utiliser n’importe quel URI rediriger, sauf si vous souhaitez prendre en charge de l’intermédiaire financier, auquel cas il doivent ressembler à ce qui précède*



#### <a name="create-keychain-sharing-between-applications"></a>Créer le trousseau partage entre applications

L’activation du partage de trousseau dépasse le cadre de ce document et couverts par Apple en leur [Ajoutant des fonctionnalités](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)de document. Ce qui est important est que vous décidez que votre trousseau à appeler et ajouter cette fonctionnalité dans toutes vos applications.

Lorsque vous avez droits correctement, vous devraient voir un fichier dans votre répertoire de projet intitulé `entitlements.plist` qui contient quelque chose qui ressemble à ce qui suit :

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Une fois que vous avez le droit de trousseau activé dans chacune de vos applications, et vous êtes prêt à utiliser l’authentification unique, informer le Kit de développement Microsoft identité votre trousseau à l’aide du paramètre suivant dans votre `ADAuthenticationSettings` avec le paramètre suivant :

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING]
Lorsque vous partagez un trousseau dans vos applications n’importe quelle application peut supprimer des utilisateurs, ou pire encore supprimer tous les jetons dans votre application. Cela est particulièrement désastreux si vous avez des applications qui s’appuient sur les jetons de travail en arrière-plan. Partage un trousseau signifie que vous devez être très prudent dans tous les supprime des opérations par l’intermédiaire de kits de développement logiciel Microsoft Identity.

Voilà ! Le Kit de développement Microsoft Identity sera maintenant partager des informations d’identification dans toutes vos applications. La liste des utilisateurs est également partagée entre les instances de l’application.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Activation de l’authentification unique pour broker assistée par l’authentification unique

La possibilité pour une application peut utiliser n’importe quel courtier qui est installé sur le périphérique est **désactivé par défaut**. Pour utiliser votre application avec le broker, vous devez effectuer une configuration supplémentaire et ajouter du code à votre application.

Les étapes à suivre sont :

1. Activer le mode de service broker dans l’appel de code de votre application au SDK de Microsoft.
2. Établir une nouvelle redirection URI et qui fournissent à l’application et de l’inscription de l’application.
3. Enregistrement d’un modèle d’URL.
4. prise en charge de l’iOS9 : ajouter une autorisation à votre fichier info.plist.


#### <a name="step-1-enable-broker-mode-in-your-application"></a>Étape 1 : Activer le mode de service broker dans votre application
La capacité de votre application pour utiliser le service broker est activée lorsque vous créez le « contexte » ou une installation initiale de votre objet d’authentification. Pour cela, en définissant le type d’informations d’identification dans votre code :

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Le `AD_CREDENTIALS_AUTO` paramètre permet d’identité Microsoft SDK tenter de faire appel à service broker, `AD_CREDENTIALS_EMBEDDED` empêchera le Kit de développement Microsoft Identity appel à service broker.

#### <a name="step-2-registering-a-url-scheme"></a>Étape 2 : Enregistrement d’un modèle d’URL
La plate-forme Microsoft Identity utilise des URL pour appeler le service broker et rendre ensuite le contrôle à votre application. Pour terminer cet aller-retour, vous avez besoin d’un modèle d’URL enregistré pour votre application qui saura de la plate-forme Microsoft Identity. Il peut s’agir en plus des autres régimes d’application que vous pouvez avoir précédemment enregistré avec votre application.

> [AZURE.WARNING]
Nous recommandons de définir le modèle d’URL unique afin de minimiser les risques d’une autre application utilisant le même modèle d’URL. Apple n’assure pas l’unicité des modèles d’URL qui sont enregistrées dans le magasin de l’application.

Voici un exemple de comment cela apparaît dans la configuration de votre projet. Vous pouvez également le faire dans XCode ainsi :

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Étape 3 : Établir une nouvelle redirection URI avec votre modèle d’URL

Afin de garantir que nous avons toujours retourner les jetons d’informations d’identification à l’application correcte, nous avons besoin pour vous assurer que nous rappeler à votre application de sorte que le système d’exploitation iOS peut vérifier. Le système d’exploitation iOS signale aux applications Microsoft Service Broker pour l’ID de lot de l’application d’appel. Il ne peut pas être usurpée par une application non fiables. Par conséquent, nous Exploitez ceci avec l’URI de notre application service broker à assurer que les jetons sont retournés à l’application correcte. Nous vous demandons d’établir cette redirection unique URI à la fois dans votre application et le définir comme un URI rediriger dans notre portail destiné aux développeurs.

Votre URI de redirection doit être de la forme correcte de :

`<app-scheme>://<your.bundle.id>`

ex : *x-msauth-mytestiosapp://com.myapp.mytestapp*

Cet URI rediriger doit être spécifié dans votre inscription d’application à l’aide d' [Azure portal classique](https://manage.windowsazure.com/). Pour plus d’informations sur l’inscription de app AD Azure, consultez [Integrating avec Azure Active Directory](./develop/active-directory-how-to-integrate.md).


##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Étape 3 : ajouter une redirection URI dans votre portail d’application et de développement pour prendre en charge d’authentification par certificat

Pour prendre en charge des certificats en fonction d’authentification qu'une seconde « msauth » doit être enregistré dans votre application et le [Azure portal classique](https://manage.windowsazure.com/) pour gérer l’authentification par certificat, si vous souhaitez ajouter cette prise en charge dans votre application.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex : *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Étape 4 : iOS9 : ajouter un paramètre de configuration pour votre application

ADAL utilise – canOpenURL : pour vérifier si le service broker est installé sur le périphérique. Dans iOS Apple 9 verrouillés les régimes d’une application peuvent interroger. Vous devez ajouter « msauth » à la section LSApplicationQueriesSchemes de vos `info.plist file`.

<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>Vous avez configuré l’authentification unique !

Désormais le Kit de développement Microsoft Identity automatiquement à la fois partager des informations d’identification entre vos applications et appeler le broker, si elle est présente sur leur périphérique.
