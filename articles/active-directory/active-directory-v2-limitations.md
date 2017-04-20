<properties
    pageTitle="Limitations du point de terminaison v2.0 & restrictions | Microsoft Azure"
    description="Une liste des limitations et restrictions avec le point de terminaison AD Azure v2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="should-i-use-the-v20-endpoint"></a>Dois-je utiliser le point de terminaison v2.0 ?

Lors de la création d’applications qui s’intègrent avec Azure Active Directory, vous devez décider si les protocoles d’authentification et de point de terminaison v2.0 répondent à vos besoins.  Le point de terminaison AD Azure d’origine est toujours entièrement pris en charge et à certains égards, est plus riche que v2.0.  Toutefois, la version 2.0 point de terminaison [présente des avantages significatifs](active-directory-v2-compare.md) pour les développeurs qui peuvent vous inciter à utiliser le nouveau modèle de programmation.

À ce stade, nos recommandations sur l’utilisation du point de terminaison v2.0 sont la suivante :

- Si vous souhaitez prendre en charge des comptes personnels Microsoft dans votre application, vous devez utiliser le point de terminaison v2.0.  Mais, assurez-vous de bien comprendre les limitations répertoriées dans cet article, en particulier celles se rapportant spécifiquement pour fonctionner et l’école de comptes.
- Si votre application requiert seulement la prise en charge de travail et les comptes de l’établissement, vous devez utiliser [les points de terminaison Azure annonce d’origine](active-directory-developers-guide.md).

Le point de terminaison v2.0 augmentera au fil du temps, afin d’éliminer les restrictions indiquées ici, afin que vous devez toujours utiliser le point de terminaison v2.0.  En attendant, cet article est destiné à vous aider à déterminer si le point de terminaison v2.0 est pour vous.  Nous allons continuer à mettre à jour cet article dans le temps pour refléter l’état actuel du point de terminaison v2.0, reportez-vous à précédent pour réévaluer vos besoins avec les fonctionnalités de la version 2.0.

Si vous possédez une application existante avec Azure AD qui n’utilise pas le point de terminaison v2.0, il n’est pas nécessaire de démarrer à partir de zéro.  À l’avenir, nous fournissant un moyen d’activer vos applications Azure annonce existantes pour une utilisation avec le point de terminaison v2.0.

## <a name="restrictions-on-apps"></a>Restrictions sur les applications
Les types d’applications suivants ne sont actuellement pas pris en charge par le point de terminaison v2.0.  Pour une description des types d’applications pris en charge, reportez-vous à [cet article](active-directory-v2-flows.md).

##### <a name="standalone-web-apis"></a>API Web d’autonome
Avec le point de terminaison v2.0, vous avez la possibilité de [créer une API Web qui est sécurisé à l’aide de OAuth 2.0](active-directory-v2-flows.md#web-apis).  Toutefois, cette API Web sera uniquement en mesure de recevoir des jetons à partir d’une application qui partage le même ID d’Application.  Création d’un site web qui est accessible à partir d’un client avec un Id différent de l’Application API n’est pas pris en charge.  Ce client ne sera pas en mesure de demander ou d’obtenir les autorisations nécessaires à votre site web API.

Pour voir comment générer une API Web qui accepte des jetons à partir d’un client avec le même App Id, consultez les exemples d’API Web de point de terminaison v2.0 dans [Mise en route](active-directory-appmodel-v2-overview.md#getting-started).

##### <a name="web-api-on-behalf-of-flow"></a>API de Web sur la part de flux
De nombreuses architectures incluent une API Web qui doit appeler une autre API Web en aval, à la fois sécurisé par le point de terminaison v2.0.  Ce scénario est courant dans les clients natifs qui disposent d’une API Web principale, qui à son tour appelle un service en ligne Microsoft ou un autre site de web personnalisées API prenant en charge AD Azure.

Ce scénario peut être pris en charge à l’aide de l’octroi d’informations d’identification de OAuth 2.0 Jwt porteur, également appelé le flux On-Behalf-Of.  Toutefois, le flux de la part de n'est pas actuellement pris en charge pour le point de terminaison v2.0.  Pour voir comment ce flux fonctionne dans la publicité Azure disponible de service, consultez l' [exemple de la part de code sur GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restrictions sur les enregistrements d’application
À ce stade, toutes les applications qui veulent intégrer avec le point de terminaison v2.0 doivent créer une nouvelle inscription de l’application à [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Toute annonce Azure existante ou les applications Microsoft Account ne sera pas compatibles avec le point de terminaison v2.0, ni sera enregistrées dans n’importe quel portail outre le nouveau portail de l’enregistrement d’application des applications.  Nous prévoyons de fournir un moyen d’activer les applications existantes pour une utilisation comme une application version 2.0. À ce stade, il n’existe aucun chemin de migration pour une application au point de terminaison v2.0.

De la même façon, les applications enregistrées dans le nouveau portail de l’enregistrement d’application ne fonctionnent pas sur le point de terminaison de l’authentification AD Azure d’origine.  Vous pouvez cependant, utiliser applications créées dans le portail de l’enregistrement d’application à intégrer avec succès à l’extrémité de l’authentification de compte Microsoft, `https://login.live.com`.

En outre, les enregistrements d’application créés au [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ont les restrictions suivantes :

- La propriété de la **page d’accueil** , également connu sous le nom de l' **ouverture de session d’URL** n’est pas pris en charge.  Sans une page d’accueil, ces applications affichera pas dans le panneau Office MyApps.
- Seulement deux secrets d’application sont autorisés par l’Id d’application pour l’instant.
- Une inscription de l’application peut uniquement être affichée et gérée par un seul compte.  Il ne peut pas être partagé entre plusieurs développeurs.
- Il existe plusieurs restrictions sur le format de redirect_uri autorisé.  Consultez la section suivante pour plus de détails.

## <a name="restrictions-on-redirect-uris"></a>Restrictions sur l’URI de redirection
Les applications qui sont enregistrées dans le nouveau portail de l’enregistrement d’Application sont actuellement limitées à un ensemble limité de valeurs de redirect_uri.  Le redirect_uri pour les applications web et des services doit commencer par le jeu de `https`, et toutes les valeurs de redirect_uri doivent partager un seul domaine DNS.  Par exemple, il n’est pas possible d’inscrire une application web qui a redirect_uris :

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Le système d’enregistrement compare le nom DNS complet de le redirect_uri existante avec le nom DNS de la redirect_uri que vous ajoutez. La demande d’ajout du nom DNS échoue si une des conditions suivantes sont remplie :  

- Si le nom DNS complet de la nouvelle redirect_uri ne correspond pas le nom DNS de la redirect_uri existant
- Si le nom DNS complet de la nouvelle redirect_uri n’est pas un sous-domaine de la redirect_uri existant

Par exemple, si l’application a redirect_uri :

`https://login.contoso.com`

Il est alors possible d’ajouter :

`https://login.contoso.com/new`

qui exactement le nom DNS, ou :

`https://new.login.contoso.com`

qui est un sous-domaine DNS de login.contoso.com.  Si vous souhaitez avoir une application qui a des east.contoso.com de l’ouverture de session et connexion-west.contoso.com en tant que redirect_uris, puis vous devez ajouter le redirect_uris suivant dans l’ordre :

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Les deux derniers peuvent être ajoutés, car ils sont des sous-domaines de la première redirect_uri, contoso.com. Cette limitation sera supprimée dans une prochaine version.

Pour savoir comment enregistrer une application dans le nouveau portail de l’enregistrement d’Application, reportez-vous à [cet article](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services--apis"></a>Restrictions sur les API et les services
Le point de terminaison v2.0 actuellement prise en charge-connexion à n’importe quelle application enregistrée dans le nouveau portail de l’enregistrement de Application, sous réserve qu’elle fait partie de la liste des [flux d’authentification pris en charge](active-directory-v2-flows.md).  Toutefois, ces applications seulement pourrez acquérir des jetons d’accès OAuth 2.0 pour un ensemble très limité de ressources.  Le point de terminaison v2.0 n’émettra qu’access_tokens pour :

- L’application qui a demandé le jeton.  Une application peut acquérir un access_token pour elle-même, si l’application logique se compose de plusieurs composants différents ou des couches.  Pour découvrir ce scénario en action, consultez nos didacticiels [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) .
- La messagerie Outlook, un calendrier et une API d’autres Contacts, qui se trouvent dans https://outlook.office.com.  Pour savoir comment écrire une application qui accède à ces API, reportez-vous à ces didacticiels [Office mise en route](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) .
- Les API de Microsoft Graph.  Pour en savoir plus sur le graphique Microsoft et toutes les données qui sont disponibles, visitez le site [https://graph.microsoft.io](https://graph.microsoft.io).

Aucun autre service n’est pris en charge pour l’instant.  Plus de Microsoft Online services seront ajoutés à l’avenir, ainsi que de la prise en charge de vos propres API de Web intégré personnalisé et les services.

## <a name="restrictions-on-libraries--sdks"></a>Restrictions sur les bibliothèques et les kits de développement logiciel
Prise en charge de bibliothèque pour le point de terminaison v2.0 est relativement limité en ce moment.  Si vous souhaitez utiliser le point de terminaison v2.0 dans une application de production, vous disposez des options suivantes :

- Si vous générez une application web, vous pouvez en toute sécurité utiliser notre middleware côté serveur disponible pour effectuer l’ouverture de session et le jeton de validation.  Celles-ci incluent le middleware se connecter ID OWIN ouvert pour ASP.NET et notre plug-in NodeJS Passport.  Les exemples de code à l’aide de nos logiciels intermédiaires sont disponibles dans notre section [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) .
- Pour les autres plates-formes et pour les applications natives et mobiles, vous pouvez également intégrer avec le point de terminaison v2.0 en directement envoyer et recevoir des messages de protocole dans le code de votre application.  La version 2.0 OpenID connecter et OAuth protocoles [ont été explicitement documentés](active-directory-v2-protocols.md) pour vous aider à effectuer une telle intégration.
- Enfin, vous pouvez utiliser les bibliothèques open source ouvrir ID se connecter et OAuth pour s’intégrer à l’extrémité de la version 2.0.  Le protocole de la version 2.0 doit être compatible avec de nombreuses bibliothèques de protocole open source sans modifications majeures.  Varie en fonction de la disponibilité de ces bibliothèques par la plate-forme et du langage, et les [Connecter de code ouvert](http://openid.net/connect/) et [OAuth 2.0](http://oauth.net/2/) sites Web mettre à jour une liste des implémentations courantes. Pour plus de détails et la liste des bibliothèques du client open source et des exemples qui ont été testés avec le point de terminaison v2.0, reportez-vous à la section [bibliothèques v2.0 et l’authentification d’Azure Active Directory (AD)](active-directory-v2-libraries.md) .

Nous avons également publié un aperçu initial de la [Bibliothèque de l’authentification de Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) uniquement pour .NET.  N’hésitez pas à essayer cette bibliothèque dans les applications client et serveur de .NET, mais comme une bibliothèque d’aperçu qu'il ne sera pas accompagné GA-qualité prend en charge.

## <a name="restrictions-on-protocols"></a>Restrictions sur les protocoles
Le point de terminaison v2.0 prend uniquement en charge la connexion de code ouvert et OAuth 2.0.  Toutefois, certaines des fonctionnalités et des capacités de chaque protocole ont été incorporées dans le point de terminaison v2.0, y compris :

- La connexion de OpenID `end_session_endpoint`, qui permet à une application à la fin de cette session avec le point de terminaison v2.0.
- id_tokens délivré par le point de terminaison v2.0 contiennent uniquement un identificateur par paire pour l’utilisateur.  Cela signifie que deux applications différentes recevra des ID différents pour le même utilisateur.  Notez que, en interrogeant le Microsoft Graph `/me` point de terminaison, vous serez en mesure d’obtenir un ID de correlatable pour l’utilisateur qui peut être utilisé entre les applications.
- id_tokens délivré par le point de terminaison v2.0 ne contiennent pas une `email` pour l’utilisateur de revendication à ce stade, même si vous acquérez l’autorisation de l’utilisateur pour consulter leur messagerie.
- Le point de terminaison OpenID un utilisateur de se connecter. Le point de terminaison d’infos de l’utilisateur n’est pas implémentée sur le point de terminaison v2.0 en ce moment.  Toutefois, toutes les données de profil utilisateur vous recevriez potentiellement à ce point de terminaison est disponible à partir du graphique de Microsoft `/me` point de terminaison.
- Rôle & les revendications de groupe.  À ce stade, le point de terminaison v2.0 ne gère pas les revendications émettrices de rôle ou un groupe dans id_tokens.

Pour mieux comprendre la portée de fonctionnalité du protocole pris en charge par le point de terminaison v2.0, lisez notre [OpenID se connecter & référence au protocole OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work--school-accounts"></a>Restrictions pour les comptes de l’école & travailler
Il existe quelques fonctionnalités spécifiques aux utilisateurs métier / d’entreprise de Microsoft qui ne sont pas encore pris en charge par le point de terminaison v2.0.

##### <a name="device-based-conditional-access-native-and-mobile-apps-and-the-microsoft-graph"></a>Le graphique Microsoft basé sur un dispositif d’accès conditionnel et les applications natives et mobiles
Le point de terminaison v2.0 ne pas prend en charge l’authentification de périphérique pour les applications mobiles et natifs, tels que les applications natives en cours d’exécution sur iOS ou Android.  Cela peut empêcher votre application native de l’appel de la Microsoft Graph pour certaines organisations.  Authentification des périphériques est requise lorsqu’un administrateur définit une stratégie basée sur un dispositif d’accès conditionnel dans une application.  Pour le point de terminaison v2.0, le scénario le plus probable pour des périphériques d’accès conditionnel est un administrateur de définir une stratégie sur une ressource dans Microsoft Graph, telles que l’API de Outlook.  Si un administrateur définit cette stratégie et que votre application native demande un jeton dans le Microsoft Graph, la demande échoue en fin de compte, car l’authentification de périphérique n’est pas encore pris en charge.  Les applications Web qui demandent des jetons à la Microsoft Graph, cependant, sont pris en charge lorsque des stratégies basées sur l’appareil sont configurés.  Du site web en app scénario périphérique d’authentifications via le navigateur web de l’utilisateur.

En tant que développeur, vous avez pas le contrôle lorsque vous définissez des stratégies sur les ressources de Microsoft Graph, ou même de savoir quand il se produit.  Si vous créez une application pour les utilisateurs de l’école et de travail, vous devez utiliser [le point de terminaison AD Azure d’origine](active-directory-developers-guide.md) jusqu'à ce que le point de terminaison version 2.0 prend en charge l’authentification des périphériques.  Pour plus d’informations sur des périphériques d’accès conditionnel, consultez [cet article](active-directory-conditional-access.md#device-based-conditional-access).

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>Authentification intégrée de Windows pour les locataires fédérés
Si vous avez utilisé ADAL (et par conséquent le point de terminaison Azure annonces d’origine) dans les applications Windows, vous avez peut-être commencé parti de ce que l'on appelle l’octroi d’assertion SAML.  Cette autorisation permet aux utilisateurs de fédérées AD Azure locataires pour l’authentification en mode silencieux avec leur instance d’Active Directory sur site, sans avoir à entrer leurs informations d’identification.  La subvention d’assertion SAML n’est actuellement pas pris en charge sur le point de terminaison v2.0.
