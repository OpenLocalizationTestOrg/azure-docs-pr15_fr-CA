<properties
    pageTitle="Le point de terminaison v2.0 AD Azure | Microsoft Azure"
    description="Une comparaison entre l’annonce d’origine de Azure et les points de terminaison v2.0."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="whats-different-about-the-v20-endpoint"></a>Quelle est la différence sur le point de terminaison v2.0 ?

Si vous êtes familiarisé avec Active Directory de Azure ou avez intégré les applications Azure AD dans le passé, il est peut-être quelques différences dans le point de terminaison v2.0 qui vous n’attendez pas.  Ce document s’appelle ces différences pour votre présentation.

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).


## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Les comptes Microsoft et Azure AD
le point de terminaison v2.0 permettent aux développeurs d’écrire des applications qui acceptent le signe à l’aide de Microsoft Accounts et AD Azure comptes, à l’aide d’un point de terminaison unique auth.  Cela vous donne la possibilité d’écrire votre application complètement indépendant du compte ; Il peut être ignorant du type de compte de l’utilisateur se connecte à l’aide.  Bien sûr, vous *pouvez* rendre votre application prenant en charge le type de compte utilisé dans une session particulière, mais que vous n’êtes pas obligé.

Par exemple, si votre application appelle [Microsoft Graph](https://graph.microsoft.io), certaines fonctionnalités supplémentaires et des données est disponibles pour les utilisateurs de l’entreprise, telles que leurs sites SharePoint ou les données de l’annuaire.  Mais, pour de nombreuses actions, telles que la [lecture du courrier d’un utilisateur](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), le code peut être écrit l’exactement la même pour les comptes Microsoft Accounts et AD Azure.  

Intégration de votre application avec Microsoft Accounts et Azure AD est maintenant un processus simple.  Vous pouvez utiliser un seul jeu de points de terminaison, une seule bibliothèque et un enregistrement d’une application unique pour accéder à des mondes à la fois le consommateur et l’entreprise.  Pour en savoir plus sur le point de terminaison v2.0, consultez [la vue d’ensemble](active-directory-appmodel-v2-overview.md).


## <a name="new-app-registration-portal"></a>Nouveau portail de l’enregistrement d’application
le point de terminaison v2.0 ne peut être inscrite que dans un nouvel emplacement : [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Il s’agit du portail où vous pouvez obtenir l’Id d’Application, personnaliser l’apparence de la page de connexion de votre application et bien plus encore.  Tout ce dont vous avez besoin pour accéder au portail est un compte Microsoft sous tension - compte personnel ou de travail ou aux études.  

Nous continuerons d’ajouter des fonctionnalités de plus à ce portail de l’enregistrement d’application au fil du temps.  L’objectif est que ce portail sera le nouvel emplacement dans lequel vous pouvez accéder pour gérer tous les éléments et tout ce qui est en rapport avec vos applications Microsoft.


## <a name="one-app-id-for-all-platforms"></a>Une Id d’application pour toutes les plates-formes
Dans le service Active Directory de Azure d’origine, vous êtes peut-être déjà inscrit plusieurs applications différentes pour un seul projet.  Vous avez dû utiliser les enregistrements d’application distinct pour vos clients natifs et les applications web :

![Ancienne immatriculation Application interface utilisateur](../media/active-directory-v2-flows/old_app_registration.PNG)

Par exemple, si vous avez généré à la fois un site Web et une application iOS, vous deviez inscrire séparément, à l’aide de deux ID d’Application différents.  Si vous avez un site Web et un back-end web api, vous peut-être avez enregistré chacun sous la forme d’une application distincte dans Azure AD.  Si vous avez une application iOS et une application d’Android, vous également pourriez avoir enregistré deux applications différentes.  

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

Maintenant, tout ce dont vous avez besoin est d’un enregistrement d’une application unique et un Id d’Application unique pour chacun de vos projets.  Vous pouvez ajouter plusieurs « plates-formes » pour chaque projet et fournir les données appropriées pour chaque plate-forme que vous ajoutez.  Bien sûr, vous pouvez créer autant d’applications que vous souhaitez en fonction de vos besoins, mais pour la majorité des cas, un seul Id de l’Application doit être nécessaire.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

Notre objectif est que cela entraîner une gestion app plus simplifiée et une expérience de développement et créer une vue plus globale de que vous travaillez sur un projet unique.


## <a name="scopes-not-resources"></a>Étendues, pas de ressources
Dans le service Active Directory Azure d’origine, une application peut se comporter comme une **ressource**ou d’un destinataire de jetons.  Une ressource peut définir un nombre **d’étendues** ou **oAuth2Permissions** qu’il comprend, permettant des applications demander des jetons à cette ressource pour un certain nombre de zones de client.  Prenez en compte l’API Azure AD graphique sous la forme d’un exemple d’une ressource :

- Identificateur de la ressource, ou `AppID URI`:`https://graph.windows.net/`
- Étendues, ou `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, etc..  

Tout cela est vrai pour le point de terminaison v2.0.  Une application continue à se comporter en tant que ressource, définir des portées et être identifiée par un URI.  Les applications client peuvent toujours demander l’accès à ces zones.  Toutefois, la manière dont un client demande des autorisations a changé.  Dans le passé, autoriser une 2.0 OAuth demande à Active Directory Azure peut présenté comme :

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

dans le cas où le paramètre de **ressource** indiqué à quelle ressource l’application cliente demande l’autorisation pour.  Annonce Azure calculées les autorisations requises par l’application en fonction de la configuration statique dans le portail d’Azure et en conséquence des jetons émis.  Désormais, le même 2.0 OAuth autoriser demande ressemble :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

où le paramètre de **portée** indique quelle ressource et autorisations de l’application demande l’autorisation pour. La ressource souhaitée est encore très présente dans la demande, il est simplement englobé dans chacune des valeurs du paramètre d’étendue.  À l’aide du paramètre de l’étendue de cette manière permet le point de terminaison v2.0 soit plus conforme à la spécification OAuth 2.0 et mieux adaptée aux pratiques courantes du secteur.  Il permet également des applications effectuer le [consentement incrémentielle](#incremental-and-dynamic-consent), qui est décrit dans la section suivante.

## <a name="incremental-and-dynamic-consent"></a>Consentement incrémentielle et dynamique
Applications inscrites dans la publicité Azure disponible nécessaire pour spécifier leurs autorisations OAuth 2.0 requises dans le portail d’Azure, au moment de la création d’application de service :

![Autorisations inscription l’interface utilisateur](../media/active-directory-v2-flows/app_reg_permissions.PNG)

Les autorisations d’une application requise ont été configurés de **manière statique**.  Bien que cette configuration de l’application existe dans le portail Azure autorisés et conservé le code simple et agréable, elle présente quelques problèmes pour les développeurs :

- Une application devait connaître toutes les autorisations qu’il aurait un jour besoin au moment de la création d’application.  Ajout d’autorisations au fil du temps d’était un processus difficile.
- Une application devait connaître toutes les ressources qu’il serait jamais accéder à l’avance.  Il était difficile de créer des applications qui pourraient accéder à un nombre arbitraire de ressources.
- Une application a dû demander toutes les autorisations dont elle aurait un jour besoin lors de son premier signe.  Dans certains cas, cela conduit à une très longue liste d’autorisations de décourager les utilisateurs finaux d’approuver l’accès de l’application dans l’authentification initiale.

Avec le point de terminaison version 2.0, vous pouvez spécifier les autorisations requises par votre application **dynamiquement**, lors de l’exécution, lors d’une utilisation régulière de votre application.  Pour ce faire, vous pouvez spécifier les zones de votre application a besoin à un moment donné dans le temps en les incluant dans le `scope` paramètre d’une demande d’autorisation :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

L’autorisation de demandes ci-dessus pour lire les données de l’annuaire d’un utilisateur AD Azure, ainsi que d’écrire des données dans son propre répertoire de l’application.  Si l’utilisateur a consenti à ces autorisations dans le passé pour cette application particulière, ils vont suffit d’entrer leurs informations d’identification et être connectés à l’application.  Si l’utilisateur n’a pas consenti à une de ces autorisations, le point de terminaison v2.0 demande à l’utilisateur de consentement à ces autorisations.  Pour en savoir plus, vous pouvez lire sur les [autorisations, le consentement et les étendues](active-directory-v2-scopes.md).

Autoriser une application à demander des autorisations dynamiquement via la `scope` paramètre vous donne un contrôle total sur l’expérience utilisateur.  Si vous le souhaitez, vous pouvez choisir de frontload votre consentement expérience et demandez pour toutes les autorisations dans une demande d’autorisation initiale.  Ou, si votre application requiert un grand nombre d’autorisations, vous pouvez choisir de rassembler ces autorisations de l’utilisateur de façon incrémentielle, alors qu’ils tentent d’utiliser certaines fonctionnalités de votre application dans le temps.

## <a name="well-known-scopes"></a>Étendues connus

#### <a name="offline-access"></a>Accès hors connexion
le point de terminaison v2.0 peut-être nécessiter l’utilisation d’une nouvelle autorisation bien connue pour les applications - le `offline_access` étendue.  Vous devez demander cette autorisation s’ils ont besoin d’accéder aux ressources de la part d’un utilisateur pour une période prolongée, même si l’utilisateur ne peut pas les utiliser activement l’application toutes les applications.  Le `offline_access` étendue s’affiche à l’utilisateur dans les boîtes de dialogue de consentement en tant que « Accéder à vos données en mode hors connexion », dont l’utilisateur doit accepter.  Demander le `offline_access` autorisation permettra à votre application web recevoir des refresh_tokens de OAuth 2.0 à partir de la version 2.0.  Refresh_tokens sont durables et peuvent être échangées de nouveau OAuth 2.0 access_tokens pendant de longues périodes d’accès.  

Si votre application ne demande pas la `offline_access` étendue, il ne recevra pas refresh_tokens.  Cela signifie que lorsque vous rembourser un authorization_code dans le [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), vous ne recevrez que sauvegarder un access_token à partir de la `/token` point de terminaison.  Ce access_token reste valide pendant une courte période de temps (en général une heure), mais ils arriveront à expiration.  À ce point dans le temps, votre application devra rediriger l’utilisateur précédent pour le `/authorize` point de terminaison pour récupérer un nouveau authorization_code.  Au cours de cette redirection, l’utilisateur peut ou non besoin entrer de nouveau leurs informations d’identification ou de nouveau leur accord aux autorisations, selon le type d’application.

Pour plus d’informations sur OAuth 2.0, refresh_tokens et access_tokens, consultez la [référence au protocole v2.0](active-directory-v2-protocols.md).

#### <a name="openid-profile--email"></a>Messagerie, profil & OpenID

Dans le service Active Directory de Azure d’origine, le plus simple OpenID se connecter connexion flux fournirait une mine d’informations sur l’utilisateur dans l’id_token qui en résulte.  Les revendications dans un id_token peuvent inclure l’utilisateur nom nom d’utilisateur par défaut, adresse de messagerie, ID d’objet et plus.

Nous sommes maintenant restreindre les informations qui le `openid` étendue permet l’accès de l’application à.  La portée de 'openid' uniquement signer l’utilisateur dans votre application et de recevoir un identificateur spécifique à l’application de l’utilisateur.  Si vous souhaitez obtenir des informations d’identification personnelle (PII) sur l’utilisateur dans votre application, votre application devez demander des autorisations supplémentaires à partir de l’utilisateur.  Nous vous présentons aujourd'hui deux nouvelles étendues – la `email` et `profile` étendues – ce qui vous permet de le faire.

Le `email` étendue est très simple : il permet l’accès de votre application à l’adresse de messagerie principale de l’utilisateur via la `email` de revendication dans l’id_token.  Le `profile` étendue permet l’accès de l’application à toutes les autres informations de base sur l’utilisateur, le nom, le nom d’utilisateur par défaut, ID d’objet et ainsi de suite.

Cela vous permet de coder votre application de façon minimale-divulgation – vous pouvez uniquement demander à l’utilisateur pour l’ensemble des informations dont votre application a besoin pour effectuer son travail.  Pour plus d’informations sur ces portées, reportez-vous à [la référence de portée v2.0](active-directory-v2-scopes.md). 

## <a name="token-claims"></a>Revendications de jeton

Les revendications dans des jetons émis par le point de terminaison version 2.0 ne sera pas identiques à jetons émis par le disponible points de terminaison Azure AD - apps migration vers le nouveau service ne doivent pas supposer une demande de remboursement particulier existe dans id_tokens ou access_tokens.   Les jetons émis par le point de terminaison v2.0 sont conformes aux spécifications OAuth 2.0 et OpenID se connecter, mais il peut suivre sémantique différente généralement disponibles service AD Azure.

Pour obtenir des informations sur les demandes spécifiques émis dans les jetons de la version 2.0, consultez la [référence de jeton de version 2.0](active-directory-v2-tokens.md).

## <a name="limitations"></a>Limitations
Il existe quelques restrictions à connaître lors de l’utilisation du point v2.0.  Veuillez consulter le [document de limitations v2.0](active-directory-v2-limitations.md) pour voir si ces restrictions s’appliquent à votre scénario particulier.
