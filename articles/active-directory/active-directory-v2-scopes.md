<properties
    pageTitle="Azure AD v2.0 étendues, autorisations et consentement | Microsoft Azure"
    description="Description de l’autorisation dans le point de terminaison de v2.0 AD Azure, y compris les étendues, les autorisations et consentement."
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

# <a name="scopes-permissions--consent-in-the-v20-endpoint"></a>Étendues, autorisations et consentement dans le point de terminaison v2.0

Les applications qui s’intègrent à AD Azure suivent un modèle d’autorisation qui permet aux utilisateurs de contrôler comment une application peut accéder à leurs données.  L’implémentation de la version 2.0 de ce modèle d’autorisation a été mis à jour, comment une application doit interagir avec Azure annonce la modification.  Cette rubrique décrit les concepts de base de ce modèle d’autorisation, y compris les étendues, les autorisations et consentement.

> [AZURE.NOTE]
    Pas toutes les fonctionnalités et les scénarios d’Azure Active Directory sont pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, obtenir des informations sur les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="scopes--permissions"></a>Étendues et des autorisations

Annonce Azure implémente le protocole d’autorisation [OAuth 2.0](active-directory-v2-protocols.md) , qui est une méthode permettant à une application de partie 3 accéder aux ressources de site web hébergé au nom d’un utilisateur.  Toutes les ressources hébergées par le web qui s’intègre à Active Directory Azure a un identificateur de ressource ou **URI ID d’application**.  Par exemple, certaines des ressources d’un hébergement web de Microsoft :

- L’Office 365 unifiée API de messagerie :`https://outlook.office.com`
- L’API Azure AD graphique :`https://graph.windows.net`
- Le graphique de Microsoft :`https://graph.microsoft.com`

Il en est de même pour les ressources 3ème partie qui a intégré à AD Azure.  Une de ces ressources peut également définir un jeu d’autorisations qui peut être utilisé pour diviser les fonctionnalités de la ressource en segments plus petits.  Par exemple, le graphique Microsoft a défini un nombre limité d’autorisations :

- Lire le calendrier d’un utilisateur
- Écrire dans le calendrier d’un utilisateur
- Envoyer du courrier en tant qu’utilisateur
- [+ autres](https://graph.microsoft.io)

En définissant ces autorisations, la ressource peut avoir un contrôle précis sur ses données et comment il est exposé au monde extérieur.  Une application de partie 3 peut alors demander ces autorisations d’un utilisateur final - et que l’utilisateur final doit approuver les autorisations avant que l’application puisse agir en leur nom.  Par segmentation des fonctionnalités de la ressource dans les jeux d’autorisations plus petits, 3ème partie applications peuvent être générées pour demander uniquement les autorisations spécifiques dont ils ont besoin pour effectuer leur obligation.  Il permet également aux utilisateurs de savoir exactement comment une application utilise des données, afin qu’ils soient plus il est probable que l’application ne semble pas fonctionne dans un but malveillant.

Dans Active Directory Azure et OAuth, ces autorisations sont appelées des **étendues**.  Vous pouvez également voir les dénommé **oAuth2Permissions**.  Une étendue est représentée dans Active Directory Azure sous la forme d’une valeur de chaîne.  Poursuivre avec l’exemple de Microsoft Graph, la valeur de portée pour chaque autorisation est la suivante :

- Lire le calendrier d’un utilisateur :`Calendar.Read`
- Écrire dans le calendrier d’un utilisateur :`Mail.ReadWrite`
- Envoyer du courrier en tant qu’utilisateur :`Mail.Send`

Une application peut demander ces autorisations en spécifiant les étendues dans les demandes au point de terminaison v2.0, comme décrit ci-dessous.

## <a name="openid-connect-scopes"></a>OpenId connecter des étendues

La mise en oeuvre de la version 2.0 du OpenID se connecter a quelques zones bien définies qui ne s’appliquent pas à une ressource particulière - `openid`, `email`, `profile`, et `offline_access`.

#### <a name="openid"></a>OpenId

Si une application effectue la connexion à l’aide de [OpenID de se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow), il doit demander le `openid` étendue.  Le `openid` étendue s’affiche dans l’écran de consentement travail compte que l’autorisation « Connexion vous » et dans l’écran de consentement Microsoft compte personnel que l’autorisation « Afficher votre profil et se connecter à des applications et des services à l’aide de votre compte Microsoft ».  Cette autorisation permet à une application de recevoir un identificateur unique pour l’utilisateur sous la forme de la `sub` de revendication.  Il permet également l’accès de l’application au point de terminaison des informations utilisateur.  Le `openid` étendue peut également servir à l’extrémité de jeton v2.0 d’acquérir id_tokens, qui peut être utilisé pour sécuriser les appels HTTP entre les différents composants d’une application.

#### <a name="email"></a>Messagerie

Le `email` étendue peut être incluse avec le `openid` étendue et tous les autres.  Il offre l’accès de l’application à l’adresse de messagerie principale de l’utilisateur sous la forme de la `email` de revendication.  Le `email` revendication n’est incluse dans les jetons si une adresse de messagerie est associée au compte d’utilisateur, qui n’est pas toujours le cas.  Si vous utilisez la `email` étendue, votre application doit être préparée à gérer le cas où la `email` revendication n’existe pas dans le jeton.

#### <a name="profile"></a>Profil

Le `profile` étendue peut être incluse avec le `openid` étendue et tous les autres.  Il offre l’accès de l’application à une mine d’informations sur l’utilisateur.  Ceci inclut, mais n’est pas limitée à, l’utilisateur prénom, nom de famille, nom d’utilisateur par défaut, ID d’objet et ainsi de suite.  Pour une liste complète des créances profil disponibles dans id_tokens pour un utilisateur donné, consultez la [référence de jeton v2.0](active-directory-v2-tokens.md).

#### <a name="offlineaccess"></a>Offline_access

La [ `offline_access` étendue](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permet à votre application d’accéder aux ressources pour le compte de l’utilisateur pendant une longue période de temps.  Dans l’écran accord de compte de travail, cette zone apparaîtra en tant que l’autorisation « Accéder à vos données à tout moment ».  Dans l’écran accord de compte Microsoft personal, il apparaît que l’autorisation « Accéder à vos informations à tout moment ».  Lorsqu’un utilisateur a approuvé le `offline_access` étendue, votre application sera activée pour recevoir des jetons d’actualisation à partir du point de terminaison de jeton de version 2.0.  Actualiser les jetons sont durables et permettre à votre application d’acquérir de nouveaux jetons d’accès comme date d’expiration les plus anciens.

Si votre application ne demande pas la `offline_access` étendue, il ne recevra pas refresh_tokens.  Cela signifie que lorsque vous rembourser un authorization_code dans le [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), vous ne recevrez que sauvegarder un access_token à partir de la `/token` point de terminaison.  Ce access_token reste valide pendant une courte période de temps (en général une heure), mais ils arriveront à expiration.  À ce point dans le temps, votre application devra rediriger l’utilisateur précédent pour le `/authorize` point de terminaison pour récupérer un nouveau authorization_code.  Au cours de cette redirection, l’utilisateur peut ou non besoin entrer de nouveau leurs informations d’identification ou de nouveau leur accord aux autorisations, selon le type d’application.

Pour plus d’informations sur la façon d’obtenir et d’utiliser actualisation de jetons, reportez-vous à la [référence au protocole v2.0](active-directory-v2-protocols.md).


## <a name="requesting-individual-user-consent"></a>Solliciter le consentement de l’utilisateur individuel

Dans une demande d’autorisation [OpenID connecter ou OAuth 2.0](active-directory-v2-protocols.md) , une application peut demander les autorisations dont il a besoin à l’aide de la `scope` paramètre de requête.  Par exemple, lorsqu’un utilisateur se connecte à une application, l’application envoie une requête semblable à la suivante (avec les sauts de ligne pour une meilleure lisibilité) :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Le `scope` le paramètre est une liste séparée par des espaces des étendues qui demande l’application.  Chaque étendue individuelles est indiqué en ajoutant la valeur de portée à l’identificateur de ressource (URI ID d’application).  La requête ci-dessus indique que l’application a besoin d’autorisation pour le calendrier de l’utilisateur de lire et d’envoyer du courrier en tant que l’utilisateur.

Une fois que l’utilisateur entre ses informations d’identification, le point de terminaison v2.0 recherchera un enregistrement correspondant du **consentement de l’utilisateur**.  Si l’utilisateur n’a pas consenti à une des autorisations demandées par le passé, le point de terminaison v2.0 demande à l’utilisateur d’accorder les autorisations demandées.  

![Capture d’écran de travail compte consentement](../media/active-directory-v2-flows/work_account_consent.png)

Si l’utilisateur approuve l’autorisation, l’autorisation sera enregistrée afin que l’utilisateur n’ait pas à nouveau de donner son consentement sur les connexions ultérieures.

## <a name="requesting-consent-for-an-entire-tenant"></a>Demande de consentement pour un locataire entière

Souvent, lorsqu’une entreprise achète une licence ou un abonnement à une application, qu’elles souhaitent mettre entièrement en service pour les employés.  Dans le cadre de ce processus, un administrateur de société peut accorder consentement de cette application peut agir au nom d’un employé.  En accordant le consentement pour un locataire ensemble, les employés de cette organisation ne rencontreront l’écran accord de cette application.

Pour demander l’autorisation pour tous les utilisateurs d’un client, votre application peut utiliser l' **administrateur de consentement de point de terminaison**, décrites ci-dessous.

## <a name="admin-restricted-scopes"></a>Restreint aux administrateurs des étendues

Certaines autorisations haute-privilège dans l’écosystème de Microsoft peuvent être marquées comme **restreint aux administrateurs**.  Voici quelques exemples de ces étendues :

- Lecture des données de l’annuaire d’un l :`Directory.Read`
- Écriture de données dans le répertoire de l’organisation :`Directory.ReadWrite`
- Lecture des groupes de sécurité dans le répertoire de l’organisation :`Groups.Read.All`

Lorsqu’un utilisateur grand public peut accorder l’accès à ces données d’une application, d’organisation utilisateurs sont limités à partir de l’octroi de l’accès au même jeu de données d’entreprise sensibles.  Si votre application demande l’accès à une de ces autorisations à un utilisateur d’organisation, l’utilisateur recevra un message d’erreur indiquant qu’ils sont non autorisés à donner son consentement pour les autorisations de votre application.

Si votre application requiert l’accès à ces étendues restreint aux administrateurs pour les organisations, vous devez leur demander directement à partir de l’administrateur d’une société également à l’aide de l' **administrateur de consentement de point de terminaison**, décrites ci-dessous.

Lorsqu’un administrateur accorde ces autorisations via le point de terminaison du consentement admin, autorisation est accordée pour tous les utilisateurs du client, comme décrit ci-dessus.

## <a name="using-the-admin-consent-endpoint"></a>En utilisant le point de terminaison du consentement admin

En suivant ces étapes, votre application sera en mesure de rassembler les autorisations pour tous les utilisateurs d’un client donné, y compris les étendues restreint aux administrateurs.  Pour voir un exemple de code qui implémente les utiliser étapes pour les inventaires ci-dessous, reportez-vous à l' [exemple de zones d’accès restreint admin](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Demander les autorisations dans le portail de l’enregistrement d’application

- Si vous ne l’avez pas déjà, accédez à votre application dans [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou [créer une application](active-directory-v2-app-registration.md) .
- Recherchez la section **Autorisations de Microsoft Graph** et ajouter les autorisations requises par votre application.
- Assurez-vous **d’Enregistrer** l’inscription de l’application

#### <a name="recommended-sign-the-user-into-your-app"></a>Recommandé : connexion de l’utilisateur dans votre application

Lors de la création d’une application qui utilise l’administrateur de consentement à point de terminaison, l’application devrez généralement une page/vue permet à l’administrateur d’approuver les autorisations de l’application.  Cette page peut être la partie de flux d’inscription de l’application, dans les paramètres de l’application, ou un flux de « se connecter » dédiée.  Dans de nombreux cas, il convient pour l’application à afficher ce « se connecter » afficher uniquement après que l’utilisateur s’est inscrit avec un travail ou à l’école compte Microsoft.

La signature de l’utilisateur dans l’application vous permet d’identifier le visant à laquelle l’administrateur appartient avant leur demandant d’approuver les autorisations nécessaires.  Si elle est absolument nécessaire, il peut vous aider à créer une expérience plus intuitive pour les utilisateurs de votre organisation.  Pour signer l’utilisateur dans, suivez nos [didacticiels de protocole v2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Demander les autorisations d’un administrateur de l’annuaire

Lorsque vous êtes prêt à demander des autorisations de l’administrateur de la société, vous pouvez rediriger l’utilisateur vers version 2.0 **admin consentement de point de terminaison**.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| clients | Obligatoire | Le locataire du répertoire que vous souhaitez demander l’autorisation à partir de.  Peut être fourni au format guid ou nom convivial. |
| identifiant_client | Obligatoire | L’Id d’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| redirect_uri | Obligatoire | Le redirect_uri où vous souhaitez que la réponse à envoyer pour votre application à gérer.  Il doit exactement correspondre à l’une de le redirect_uris que vous avez enregistré dans le portail. |
| état | recommandé | Valeur incluse dans la demande est également retournée dans la réponse de jeton.  Il peut être une chaîne de tout contenu que vous souhaitez.  L’état est utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page ou l’affichage qu’ils étaient sur. |

À ce stade, AD Azure appliquera que seul un administrateur peut se connecter terminer la demande.  L’administrateur sera invité à approuver toutes les autorisations que vous avez demandés pour votre application dans le portail d’inscription. 

##### <a name="successful-response"></a>Réponse correcte
Si l’administrateur approuve les autorisations pour votre application, la réponse correcte est :

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paramètre | Description |
| ----------------------- | ------------------------------- | --------------- |
| clients | Le locataire du répertoire qui a les autorisations accordées à votre application il a demandé, au format guid. |
| état | Valeur incluse dans la demande est également retournée dans la réponse de jeton.  Il peut être une chaîne de tout contenu que vous souhaitez.  L’état est utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telle que la page ou l’affichage qu’ils étaient sur. |
| admin_consent | Est définie sur `True`. |



##### <a name="error-response"></a>Réponse d’erreur
Si l’administrateur n’approuve pas les autorisations pour votre application, la réponse d’échec sera :

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paramètre | Description |
| ----------------------- | ------------------------------- | --------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur.  |

Une fois que vous avez reçu une réponse correcte du point de terminaison du consentement admin, votre application a acquis les autorisations qu’il a demandé.  Vous pouvez désormais déplacer sur demande un jeton pour la ressource de votre choix, comme décrit ci-dessous.

## <a name="using-permissions"></a>À l’aide d’autorisations

Une fois que l’utilisateur accepte d’autorisations pour votre application, votre application peut acquérir des jetons d’accès qui représentent les autorisations de votre application d’accéder à une ressource dans une capacité.  Un jeton d’accès donné peut uniquement être utilisé pour un ressources unique, mais codée qu’elle contient seront toutes les autorisations que votre application a été accordée pour cette ressource.  Pour acquérir un jeton d’accès, votre application peut effectuer une requête pour le point de terminaison de jeton v2.0 :

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Le jeton d’accès qui en résulte peut ensuite être utilisé dans les demandes HTTP vers la ressource - fiable indiquera à la ressource que votre application possède les autorisations appropriées pour effectuer une tâche donnée.  

Pour plus de détails sur le protocole OAuth 2.0 et comment acquérir les jetons d’accès, consultez la [référence de protocole de point de terminaison v2.0](active-directory-v2-protocols.md).
