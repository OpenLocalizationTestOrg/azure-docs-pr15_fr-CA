
<properties
   pageTitle="Scénarios d’authentification pour Azure annonce | Microsoft Azure"
   description="Vue d’ensemble des cinq scénarios plus courants d’authentification pour Azure Active Directory (DAS)"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="authentication-scenarios-for-azure-ad"></a>Scénarios d’authentification pour Azure AD

Azure Active Directory (AD Azure) simplifie l’authentification pour les développeurs à fournir l’identité en tant que service, avec prise en charge des protocoles standard tels que OAuth 2.0 et OpenID se connecter, ainsi qu’open source de bibliothèques pour les différentes plates-formes pour vous aider à commencer à coder rapidement. Ce document vous aidera à comprendre le prend en charge des scénarios AD Azure différents et vous montrera comment commencer. Elle est divisée dans les sections suivantes :

- [Notions de base de l’authentification dans AD Azure](#basics-of-authentication-in-azure-ad)

- [Revendications dans des jetons de sécurité AD Azure](#claims-in-azure-ad-security-tokens)

- [Notions de base de l’inscription d’une Application dans Azure AD](#basics-of-registering-an-application-in-azure-ad)

- [Scénarios et Types d’application](#application-types-and-scenarios)

  - [Navigateur Web pour l’Application Web](#web-browser-to-web-application)

  - [Application d’une Page unique (SPA)](#single-page-application-spa)

  - [Application native pour une API Web](#native-application-to-web-api)

  - [Application Web sur le Web API](#web-application-to-web-api)

  - [Démon ou une Application de serveur pour API Web](#daemon-or-server-application-to-web-api)



## <a name="basics-of-authentication-in-azure-ad"></a>Notions de base de l’authentification dans AD Azure

Si vous ne connaissez pas les concepts de base de l’authentification dans AD Azure, lisez cette section. Dans le cas contraire, il pouvez que vous souhaitez ignorer pour les [Types d’applications et de scénarios](#application-types-and-scenarios).

Prenons l’exemple du scénario plus simple où l’identité est requise : un utilisateur dans un navigateur web doit s’authentifier auprès d’une application web. Ce scénario est décrit en détail dans la section [Application Web le navigateur Web](#web-browser-to-web-application) , mais elle est un bon point de départ pour illustrer les fonctionnalités de publicité Azure et conceptualiser le fonctionne de scénario. Examinons le diagramme suivant de ce scénario :

![Vue d’ensemble de la session d’application web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Le diagramme ci-dessus à l’esprit, voici ce que vous devez savoir sur ses divers composants :

- Azure est le fournisseur d’identité, responsable de la vérification de l’identité des utilisateurs et des applications qui existent dans le répertoire de l’organisation et finalement qui délivre des jetons de sécurité lors de l’authentification d’utilisateurs et des applications.


- Une application qui souhaite externaliser l’authentification AD Azure doit être inscrit dans Active Directory Azure, qui inscrit et qui identifie de manière unique l’application dans le répertoire.


- Les développeurs peuvent utiliser les bibliothèques de l’authentification open source AD Azure pour faciliter l’authentification en gérant les détails du protocole pour vous. Pour plus d’informations, reportez-vous à la section [Azure des bibliothèques de l’authentification Active Directory](active-directory-authentication-libraries.md) .


• Une fois qu’un utilisateur a été authentifié, l’application doit valider le jeton de sécurité de l’utilisateur pour vous assurer que l’authentification a réussi pour les parties prévues. Les développeurs peuvent utiliser les bibliothèques d’authentification fournis pour gérer la validation de n’importe quel jeton d’Azure AD, y compris des jetons de Web JSON (JWT) ou SAML 2.0. Si vous souhaitez exécuter la validation manuellement, reportez-vous à la documentation du [Gestionnaire de jeton JWT](https://msdn.microsoft.com/library/dn205065.aspx) .


> [AZURE.IMPORTANT] Annonce Azure utilise la cryptographie à clé publique pour signer des jetons et vérifier qu’ils sont valides. Reportez-vous à la section [Important d’informations sur la signature de clé survol dans Azure AD](active-directory-signing-key-rollover.md) pour plus d’informations sur la logique nécessaire que vous devez disposer dans votre application pour vérifier qu’il est toujours mis à jour avec les dernières clés.


• Le flux des demandes et des réponses pour le processus d’authentification est déterminé par le protocole d’authentification qui a été utilisé, par exemple 2.0 OAuth, OpenID se connecter, WS-Federation et SAML 2.0. Ces protocoles sont décrits plus en détail dans la rubrique [Azure des protocoles de l’authentification Active Directory](active-directory-authentication-protocols.md) et dans les sections ci-dessous.

> [AZURE.NOTE] Annonce Azure prend en charge le 2.0 OAuth et normes OpenID connecter très souvent utilisent les jetons de support, y compris des jetons porteur représentés sous la forme JWTs. Un jeton de support est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Dans ce sens, le « porteur » est toute partie qui peut présenter le jeton. Si une partie doit tout d’abord authentifier avec AD Azure pour recevoir le jeton au porteur, si les mesures ne sont pas prises pour sécuriser le jeton dans la transmission et le stockage, peuvent être interceptée et utilisée par une partie involontaire. Des jetons de sécurité ont un mécanisme intégré pour empêchant des tiers de leur utilisation, les jetons de support n’ont pas ce mécanisme et doivent être transportés dans un canal sécurisé, tels que la sécurité de la couche transport (HTTPS). Si un jeton de support est transmis en clair, un homme de l’attaque peut servir par une partie malveillante pour obtenir le jeton et l’utiliser pour un accès non autorisé à une ressource protégée. Les mêmes principes de sécurité s’appliquent lors du stockage ou de la mise en cache des jetons de support pour une utilisation ultérieure. Assurez-vous toujours que votre application transmet et stocke les jetons des porteur de manière sécurisée. Plus en matière de sécurité sur les jetons de support, consultez la [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).


Maintenant que vous avez une vue d’ensemble des principes fondamentaux, lisez les sections ci-dessous pour comprendre comment fonctionne la mise en service dans AD Azure et les scénarios courants AD Azure prend en charge.


## <a name="claims-in-azure-ad-security-tokens"></a>Revendications dans des jetons de sécurité AD Azure

Les jetons de sécurité émis par AD Azure contiennent les demandes ou les assertions d’informations sur le sujet qui a été authentifié. Ces revendications utilisable par l’application pour différentes tâches. Par exemple, ils peuvent servir à valider le jeton, identifier les clients d’annuaire du sujet, afficher les informations utilisateur, déterminer les autorisations de l’objet et ainsi de suite. Les revendications présentes dans un jeton de sécurité varient selon le type de jeton, le type d’informations d’identification utilisées pour authentifier l’utilisateur et la configuration de l’application. Une brève description de chaque type de demande émise par Azure AD est fournie dans le tableau ci-dessous. Pour plus d’informations, consultez [prise en charge de jeton et les Types de revendications](active-directory-token-and-claims.md).


| Demande d’indemnisation | Description |
|-------|-------------|
| ID de l’application | Identifie l’application qui utilise le jeton.
| Public | Identifie la ressource destinataire que le jeton est destiné. |
| Référence de classe d’application d’authentification contexte | Indique comment le client a été authentifié (public client et client confidentielles). |
| Authentification instantanée | Enregistre la date et l’heure de l’authentification. |
| Méthode d’authentification | Indique comment l’objet du jeton a été authentifié (mot de passe, certificat, etc.). |
| Prénom | Fournit le prénom de l’utilisateur tel que défini dans AD Azure. |
| Groupes | Contient des groupes d’objets ID d’Azure AD de de que l’utilisateur est un membre. |
| Fournisseur d’identité | Enregistre le fournisseur d’identité qui a authentifié le sujet du jeton. |
| Délivrée à | Enregistre l’heure à laquelle le jeton a été délivré, souvent utilisée pour la fraîcheur de jeton. |
| Émetteur | Identifie le STS qui a émis le jeton ainsi que le locataire AD Azure. |
| Nom de famille | Fournit le nom de famille de l’utilisateur tel que défini dans AD Azure. |
| Nom | Fournit une valeur lisible humaine qui identifie le sujet du jeton. |
| Id de l’objet | Contient un identificateur unique et immuable de l’objet dans Active Directory Azure. |
| Rôles | Contient les noms conviviaux Azure AD de rôles d’Application que l’utilisateur a été accordée. |
| Champ d’application | Indique les autorisations accordées à l’application cliente. |
| Objet | Indique l’entité sur laquelle le jeton déclare les informations. |
| Id de clients | Contient un identificateur unique et immuable du locataire du répertoire qui a émis le jeton. |
| Durée de vie de jeton | Définit l’intervalle de temps pendant lequel un jeton est valide. |
| Nom d’utilisateur Principal | Contient le nom d’utilisateur principal de l’objet. |
| Version | Contient le numéro de version du jeton. |


## <a name="basics-of-registering-an-application-in-azure-ad"></a>Notions de base de l’inscription d’une Application dans Azure AD

Toute application qui externalise l’authentification AD Azure doit être enregistrée dans un répertoire. Cette étape implique de parler Azure AD de votre application, y compris l’URL où il a trouvé, l’URL pour envoyer une réponse après l’authentification, l’URI pour identifier votre application et bien plus encore. Cette information est nécessaire pour plusieurs raisons principales :

- Annonce Azure doit coordonnées pour communiquer avec l’application lors de la gestion des jetons de session ou l’échange. Ce sont les suivants :

  - ID application : L’identificateur URI D’une application. Cette valeur est envoyée pour Azure AD lors de l’authentification pour indiquer quelle application l’appelant veut un jeton. En outre, cette valeur est incluse dans le jeton afin que l’application ne sait qu’il a été la cible prévue.


  - Réponse d’URL et les URI de redirection : dans le cas d’une API de web ou une application web, l’URL de la réponse est l’emplacement auquel AD Azure envoie la réponse d’authentification, y compris un jeton si l’authentification a réussi. Dans le cas d’une application native, l’URI de redirection est un identificateur unique à laquelle AD Azure redirige user-agent dans une requête OAuth 2.0.


  - N° client : ID d’une application, qui est générée par AD Azure lorsque l’application est enregistrée. Lorsqu’elle demande un jeton ou un code d’autorisation, le code de client et la clé sont envoyés à AD Azure pendant l’authentification.


  - Clé : La clé est envoyée avec un ID client lors de l’authentification dans AD Azure pour appeler une API web.


- Annonce Azure doit s’assurer que l’application dispose des autorisations requises pour accéder à vos données de répertoire, et autres applications de votre organisation et ainsi de suite

Mise en service devient plus clair lorsque vous comprenez qu’il existe deux catégories d’applications qui peuvent être développées et intégrées avec AD Azure :

- Demande de client unique : une application mono-utilisateur est conçue pour une utilisation dans une organisation. Il s’agit généralement de métier applications métier écrites par un développeur d’entreprise. Une application mono-utilisateur ne doit être accessible par les utilisateurs dans un seul répertoire, et par conséquent, il doit uniquement être mis en service dans un répertoire. Ces applications sont généralement enregistrées par un développeur dans l’organisation.


- Applications : une application partagée est destinée dans de nombreuses organisations, pas simplement d’une organisation. Ce sont les applications généralement logiciel-as-a-service (SaaS) écrites par un fournisseur de logiciels indépendant (ISV). Les applications mutualisées doivent être mis en service dans chaque répertoire où ils seront utilisés, ce qui nécessite le consentement d’utilisateur ou un administrateur pour les enregistrer. Ce processus de consentement démarre lorsqu’une application a été enregistrée dans le répertoire et l’accès est accordée à l’API graphique ou peut-être un autre web API. Lorsqu’un utilisateur ou un administrateur à partir d’une autre organisation s’inscrit utiliser l’application, ils sont présentés avec une boîte de dialogue qui affiche les autorisations que requises par l’application. L’utilisateur ou l’administrateur peut ensuite consentir à l’application, ce qui permet l’application d’accéder aux données indiquées et enfin enregistre l’application dans son propre répertoire. Pour plus d’informations, consultez [vue d’ensemble de l’infrastructure de consentement](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Certaines considérations supplémentaires surviennent lors du développement d’une application partagée au lieu d’une application mono-utilisateur. Par exemple, si vous effectuez votre application disponible aux utilisateurs dans plusieurs répertoires, vous avez besoin d’un mécanisme permettant de déterminer quels clients ils se trouvent dans. Une application mono-utilisateur doit uniquement rechercher dans son propre répertoire d’un utilisateur, pendant une application mutualisée doit identifier un utilisateur spécifique à partir de tous les répertoires dans Azure AD. Pour accomplir cette tâche, AD Azure fournit un point de terminaison d’authentification commun où n’importe quelle application mutualisée peut diriger les demandes de connexion, au lieu d’un point de terminaison de client spécifiques. Ce point de terminaison est https://login.microsoftonline.com/common pour tous les répertoires dans Azure AD, alors que les clients spécifiques d’un point de terminaison peut être https://login.microsoftonline.com/contoso.onmicrosoft.com. Le point de terminaison commun est particulièrement important à prendre en compte lorsque vous développez votre application, car vous en aurez besoin la logique nécessaire pour gérer plusieurs locataires au cours de la connexion, déconnexion et validation de jeton.

Si vous souhaitez la rendre disponible pour de nombreuses organisations sont en train de développer une application mono-utilisateur, vous pouvez facilement apporter des modifications à l’application et sa configuration dans Active Directory Azure mutualisée capable. En outre, AD Azure utilise la même clé de signature pour tous les jetons dans tous les répertoires, si vous fournissez l’authentification dans un mono-utilisateur ou les applications.

Chaque scénario cité dans ce document comprend une rubrique qui décrit ses exigences de mise en service. Pour plus d’informations sur l’activation d’une application dans Azure AD et les différences entre les applications unique et mutualisées, voir [Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md) pour plus d’informations. Continuer la lecture pour comprendre les scénarios d’application courants dans Azure AD.

## <a name="application-types-and-scenarios"></a>Scénarios et Types d’application

Chacun des scénarios décrits dans ce document peut être développée à l’aide de différents langages et plates-formes. Ils sont tous les soutenues par des exemples de code complets qui sont disponibles dans notre [guide d’exemples de Code](active-directory-code-samples.md), ou directement à partir des [référentiels exemple de Github](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory)correspondant. En outre, si votre application a besoin d’une pièce spécifique ou un segment d’un scénario de bout en bout, dans la plupart des cas cette fonctionnalité peut être ajoutée séparément. Par exemple, si vous avez une application native qui appelle une API web, vous pouvez facilement ajouter une application web qui appelle également l’API web. Le diagramme suivant illustre ces scénarios et les types d’application, et la manière dont les différents composants peuvent être ajoutés :

![Types d’applications et de scénarios](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Voici les cinq scénarios d’application principal pris en charge par Active Directory Azure :

- [Navigateur Web pour l’Application Web](#web-browser-to-web-application): un utilisateur a besoin pour se connecter à une application web sécurisée par AD Azure.

- [Application de Page unique (SPA)](#single-page-application-spa): l’utilisateur doit se connecter à une application de page unique qui est sécurisée par AD Azure.

- [Application native pour une API Web](#native-application-to-web-api): une application native qui s’exécute sur un PC, un Tablet PC ou un téléphone doit authentifier un utilisateur pour obtenir des ressources à partir d’un site web API sécurisée par AD Azure.

- [Application Web de l’API Web](#web-application-to-web-api): une application web a besoin d’obtenir des ressources à partir d’un site web sécurisé par AD Azure des API.

- [Démon ou une Application de serveur pour API Web](#daemon-or-server-application-to-web-api): une application de démon ou une application serveur sans interface utilisateur de web a besoin d’obtenir des ressources à partir d’un site web sécurisé par AD Azure des API.

### <a name="web-browser-to-web-application"></a>Navigateur Web pour l’Application Web

Cette section décrit une application qui authentifie un utilisateur dans un navigateur web à une application web. Dans ce scénario, l’application web indique au navigateur de l’utilisateur pour ouvrir une session les annonces Azure. Annonce Azure renvoie une réponse de connexion via le navigateur de l’utilisateur, qui contient des déclarations sur l’utilisateur dans un jeton de sécurité. Ce scénario prend en charge de session à l’aide des protocoles WS-Federation, SAML 2.0 et OpenID se connecter.


#### <a name="diagram"></a>Diagramme
![Flux d’authentification du navigateur pour une application web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### <a name="description-of-protocol-flow"></a>Description du flux de protocole


1. Lorsqu’un utilisateur accède à l’application et les besoins pour vous connecter, il est redirigé via une demande de connexion pour le point de terminaison de l’authentification dans AD Azure.


2. L’utilisateur se connecte sur la page de connexion.


3. Si l’authentification réussit, Azure AD crée un jeton d’authentification et renvoie une réponse de connexion à l’URL de réponse de l’application qui a été configuré dans le portail de gestion Azure. Pour une application de production, cette URL de réponse doit être HTTPS. Le jeton retourné inclut des créances sur les annonces Azure qui sont requis par l’application de valider le jeton.


4. L’application valide le jeton à l’aide d’une clé de signature publique et des informations d’émetteur disponibles dans le document de métadonnées de fédération pour Azure AD. Une fois que l’application valide le jeton, AD Azure démarre une nouvelle session avec l’utilisateur. Cette session permet à l’utilisateur d’accéder à l’application jusqu'à son expiration.


#### <a name="code-samples"></a>Exemples de code


Consultez les exemples de code de navigateur Web aux scénarios d’Application Web. Et bien, à consulter régulièrement, que nous ajoutons de nouveaux exemples tout le temps. [Navigateur web pour l’Application Web](active-directory-code-samples.md#web-browser-to-web-application).


#### <a name="registering"></a>Inscription


- Mono-utilisateur : Si vous générez une application juste pour votre organisation, il doit être inscrit dans l’annuaire de votre société en utilisant le portail de gestion Azure.


- Mutualisée : Si vous créez une application qui peut être utilisée par des utilisateurs externes à votre organisation, il doit être enregistré dans le répertoire de votre entreprise, mais également qu’il doit être inscrit dans l’annuaire de chaque organisation qui utiliseront l’application. Pour rendre votre application disponible dans son propre répertoire, vous pouvez inclure un processus d’inscription pour vos clients qui leur permet de ne consentir à votre application. Lors de leur inscription pour votre application, elles seront présentées avec une boîte de dialogue qui indique les autorisations que requises par l’application, puis acceptez l’option. Selon les autorisations requises, un administrateur de l’autre organisation peut être nécessaire de donner leur consentement. Lorsque l’administrateur ou l’utilisateur accepte, l’application est enregistrée dans son propre répertoire. Pour plus d’informations, voir [Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiration du jeton

Expiration de la session de l’utilisateur lors de l’expiration de la durée de vie du jeton émis par AD Azure. Votre application peut réduire cette durée si vous le souhaitez, comme la signature des utilisateurs basées sur une période d’inactivité. Lorsque la session expire, l’utilisateur sera invité à vous connecter à nouveau.





### <a name="single-page-application-spa"></a>Application d’une Page unique (SPA)

Cette section décrit l’authentification pour une Application à une Page, qui utilise AD Azure et l’autorisation implicite OAuth 2.0 accordent pour sécuriser son site web API back end. Applications à Page unique sont généralement structurées comme une couche de présentation (couche frontale) JavaScript qui s’exécute dans le navigateur et un API Web back-end qui s’exécute sur un serveur et implémente la logique métier de l’application. Pour en savoir plus sur l’autorisation implicite grant et vous aidera à définir si il est adapté à votre scénario d’application, reportez-vous à la section [Présentation du flux de subvention implicite OAuth2 dans Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

Dans ce scénario, lorsque l’utilisateur se connecte, le code JavaScript front end utilise [de la bibliothèque d’authentification Active Directory pour JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) et l’octroi de l’autorisation implicite pour obtenir un jeton d’ID (id_token) à partir d’AD Azure. Le jeton est mis en cache et le client joint à la demande que le jeton de support lors d’appels à son API Web back-end, ce qui est sécurisé à l’aide de logiciels intermédiaires OWIN. 
#### <a name="diagram"></a>Diagramme

![Seul diagramme d’Application de la Page](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Description du flux de protocole

1. L’utilisateur accède à l’application web.


2. L’application retourne le JavaScript front-end (couche présentation) au navigateur.


3. L’utilisateur lance la connexion, par exemple en cliquant sur une lien de connexion. Le navigateur envoie une commande GET au point de terminaison AD Azure d’autorisation à demander un ID de jeton. Cette demande inclut l’URL de réponse et les ID de client dans les paramètres de requête.


4. Annonce Azure valide l’URL de la réponse par rapport à l’URL de réponse enregistré qui a été configuré dans le portail de gestion Azure.


5. L’utilisateur se connecte sur la page de connexion.


6. Si l’authentification réussit, Azure AD crée un jeton d’identification et le retourne sous la forme d’un fragment d’URL (#) à l’URL de réponse de l’application. Pour une application de production, cette URL de réponse doit être HTTPS. Le jeton retourné inclut des créances sur les annonces Azure qui sont requis par l’application de valider le jeton.


7. Le code de client JavaScript dans le navigateur extrait le jeton de la réponse à utiliser dans la sécurisation des appels pour le web de l’application QU'API back end.


8. Le navigateur appelle le web de l’application API back end avec le jeton d’accès dans l’en-tête d’autorisation.

#### <a name="code-samples"></a>Exemples de code


Consultez les exemples de code pour les scénarios d’Application de Page unique (SPA). N’oubliez pas de vérifier fréquemment--nous ajoutons de nouveaux exemples tout le temps. [Application d’une Page unique (SPA)](active-directory-code-samples.md#single-page-application-spa).


#### <a name="registering"></a>Inscription


- Mono-utilisateur : Si vous générez une application juste pour votre organisation, il doit être inscrit dans l’annuaire de votre société en utilisant le portail de gestion Azure.


- Mutualisée : Si vous créez une application qui peut être utilisée par des utilisateurs externes à votre organisation, il doit être enregistré dans le répertoire de votre entreprise, mais également qu’il doit être inscrit dans l’annuaire de chaque organisation qui utiliseront l’application. Pour rendre votre application disponible dans son propre répertoire, vous pouvez inclure un processus d’inscription pour vos clients qui leur permet de ne consentir à votre application. Lors de leur inscription pour votre application, elles seront présentées avec une boîte de dialogue qui indique les autorisations que requises par l’application, puis acceptez l’option. Selon les autorisations requises, un administrateur de l’autre organisation peut être nécessaire de donner leur consentement. Lorsque l’administrateur ou l’utilisateur accepte, l’application est enregistrée dans son propre répertoire. Pour plus d’informations, voir [Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md).

Après avoir inscrit l’application, il doit être configuré pour utiliser le protocole d’octroi implicite de OAuth 2.0. Par défaut, ce protocole est désactivé pour les applications. Pour activer le protocole octroi implicite de OAuth2 pour votre application, télécharger son manifeste d’application à partir du portail de gestion Azure, définissez la valeur de « oauth2AllowImplicitFlow » sur true et puis téléchargez l’arrière manifeste sur le portail. Pour obtenir des instructions détaillées, reportez-vous à la section [Activation OAuth 2.0 implicite Grant pour Applications à Page unique](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiration du jeton

Lorsque vous utilisez ADAL.js pour gérer l’authentification avec AD Azure, vous bénéficiez de plusieurs fonctionnalités qui facilitent l’actualisation d’un jeton a expiré, ainsi que la mise en route de jetons pour les ressources API web supplémentaire qui peuvent être appelées par l’application. Lorsque l’utilisateur s’authentifie avec succès avec l’annonce d’Azure, une session sécurisée par un cookie est établie pour l’utilisateur entre le navigateur et l’annonce Azure. Il est important de noter que la session existe entre l’utilisateur et l’annonce Azure et non entre l’utilisateur et l’application web en cours d’exécution sur le serveur. Lorsqu’un jeton arrive à expiration, ADAL.js utilise cette session silencieusement en obtenir un autre jeton. Pour cela, à l’aide d’un iFrame masqué pour l’envoi et la réception de la demande en utilisant le protocole OAuth les autorisations implicites. ADAL.js pouvez également utiliser ce même mécanisme pour obtenir silencieusement les jetons d’accès à partir d’AD Azure pour d’autre web ressources API que les appels de l’application tant que la prise en charge de ces ressources (CORS), le partage des ressources entre origine sont enregistrés dans le répertoire de l’utilisateur, et de toute autorisation nécessaire a été fournie par l’utilisateur lors de l’ouverture de session.


### <a name="native-application-to-web-api"></a>Application native pour une API Web


Cette section décrit une application native qui appelle une API web part d’un utilisateur. Ce scénario repose sur le type de subvention de code d’autorisation OAuth 2.0 avec un client public, comme indiqué au point 4.1 de la [spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749). L’application native Obtient un jeton d’accès de l’utilisateur en utilisant le protocole OAuth 2.0. Ce jeton d’accès est ensuite transmis dans la requête sur le web, API, ce qui autorise l’utilisateur et retourne la ressource souhaitée.

#### <a name="diagram"></a>Diagramme

![Application native pour le diagramme de l’API de Web](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Flux d’authentification pour une application native pour une API

#### <a name="description-of-protocol-flow"></a>Description du flux de protocole


Si vous utilisez les bibliothèques de l’authentification Active Directory, la plupart des détails du protocole décrites ci-dessous est gérée, tels que les fenêtres publicitaires intempestives du navigateur, jeton de la mise en cache et traitement des jetons d’actualisation.

1. À l’aide d’un navigateur contextuel, que l’application native effectue une demande pour le point de terminaison d’autorisation dans Active Directory Azure. Cette demande inclut l’ID de client et de l’URI de l’application native de redirection comme indiqué dans le portail de gestion et de l’identificateur URI d’application pour le web API. Si l’utilisateur n’a pas déjà connecté, ils sont invités à vous connecter à nouveau


2. Annonce Azure authentifie l’utilisateur. Si c’est une application mutualisée et consentement est requis pour utiliser l’application, l’utilisateur devra consentir s’ils n’ont pas déjà fait. Après l’octroi d’accord et si l’authentification aboutit, AD Azure émet une réponse de code d’autorisation à l’URI de redirection de l’application client.


3. Lorsque AD Azure émet une réponse de code d’autorisation à l’URI de redirection, l’application client cesse d’interaction du navigateur et extrait le code d’autorisation de la réponse. À l’aide de ce code d’autorisation, l’application client envoie une demande à l’extrémité de jeton d’Azure AD qui inclut le code d’autorisation, les détails concernant l’application de client (ID de client et l’URI de redirection) et la ressource de votre choix (application ID URI pour l’API web).


4. Le code d’autorisation et des informations sur l’API de client web et d’application sont validés par AD Azure. Si la validation réussit, AD Azure renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT. En outre, AD Azure renvoie des informations de base sur l’utilisateur, notamment leur nom et clients un code complet.


5. Via HTTPS, l’application cliente utilise le jeton d’accès JWT retourné pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête de la demande d’autorisation de l’API web. L’API de web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.


6. Lorsque le jeton d’accès arrive à expiration, l’application client recevra une erreur indiquant que l’utilisateur doit s’authentifier à nouveau. Si l’application possède un jeton d’actualisation valide, il peut servir d’acquérir un nouveau jeton d’accès sans inviter l’utilisateur à le faire. Si le jeton d’actualisation expire, l’application devra interactive de nouveau authentifier l’utilisateur.


> [AZURE.NOTE] Le jeton d’actualisation émis par AD Azure peut être utilisé pour accéder à plusieurs ressources. Par exemple, si vous disposez d’une application cliente qui a l’autorisation d’appeler deux web API, le jeton d’actualisation utilisable pour obtenir un accès à un jeton pour l’autre API web ainsi.


#### <a name="code-samples"></a>Exemples de code


Consultez les exemples de code pour une Application Native pour les scénarios de l’API Web. Et bien, à consulter régulièrement, que nous ajoutons de nouveaux exemples tout le temps. [Application native pour une API Web](active-directory-code-samples.md#native-application-to-web-api).


#### <a name="registering"></a>Inscription


- Mono-utilisateur : Natives l’application et l’API web doivent être enregistrés dans le même répertoire dans Azure AD. L’API de web peut être configuré pour exposer un ensemble d’autorisations, qui sont utilisées pour limiter l’accès de l’application native à ses ressources. L’application cliente puis sélectionne les autorisations souhaitées à partir de la liste déroulante « Autorisations pour les autres Applications » dans le portail de gestion Azure.


- Mutualisée : Tout d’abord, l’application native seulement jamais enregistré dans le développeur ou le répertoire du serveur de publication. Ensuite, l’application native est configurée pour indiquer les autorisations que requises pour être fonctionnelle. Cette liste des autorisations requises s’affiche dans une boîte de dialogue lorsqu’un utilisateur ou un administrateur dans le répertoire de destination donne le consentement à l’application, ce qui le rend disponible pour leur organisation. Certaines applications requièrent des autorisations de niveau utilisateur uniquement, lequel n’importe quel utilisateur de l’organisation peut consentir à. Autres applications nécessitent des autorisations de niveau administrateur, un utilisateur de l’organisation ne peut pas consentir à. Seul un administrateur de l’annuaire peut donner son consentement pour les applications qui requièrent ce niveau d’autorisation. Lorsque l’administrateur ou l’utilisateur consent, uniquement l’API web est enregistré dans son répertoire. Pour plus d’informations, voir [Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiration du jeton


Lorsque l’application native utilise son code d’autorisation pour obtenir un accès JWT à jeton, il reçoit également un jeton d’actualisation JWT. Lorsque le jeton d’accès arrive à expiration, le jeton d’actualisation peut être utilisé pour authentifier l’utilisateur sans avoir à vous connecter à nouveau. Ce jeton d’actualisation est ensuite utilisé pour authentifier l’utilisateur, ce qui entraîne un nouveau jeton d’accès et un jeton d’actualisation.





### <a name="web-application-to-web-api"></a>Application Web sur le Web API


Cette section décrit une application web qui a besoin d’obtenir des ressources à partir d’un site web API. Dans ce scénario, il existe deux types d’identité que l’application web peut utiliser pour authentifier et appeler l’API web : une identité d’application ou une identité de l’utilisateur délégué.

*Identité de l’application :* Ce scénario utilise un octroi d’informations d’identification OAuth 2.0 client pour s’authentifier en tant que l’application et d’accéder à l’API web. Lors de l’utilisation d’une identité de l’application, le web API ne peut détecter que l’application web appelle, comme le web API ne reçoit pas toutes les informations relatives à l’utilisateur. Si l’application reçoit des informations sur l’utilisateur, il va être envoyé via le protocole d’application, et il n’est pas signé par AD Azure. L’API de web approuve que l’utilisateur est authentifié par l’application web. Pour cette raison, ce modèle est appelé un sous-système approuvé.

*Délégué l’identité de l’utilisateur :* Ce scénario peut être réalisé de deux manières : OpenID se connecter et OAuth 2.0 d’octroi de code avec un client confidentiel. L’application web Obtient un jeton d’accès de l’utilisateur, ce qui s’avère le web API que l’utilisateur a été authentifié avec succès à l’application web et que l’application web n’a pu obtenir une identité de l’utilisateur délégué pour appeler l’API web. Ce jeton d’accès est envoyé dans la requête sur le web, API, ce qui autorise l’utilisateur et retourne la ressource souhaitée.

#### <a name="diagram"></a>Diagramme

![Application Web sur le diagramme de l’API Web](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### <a name="description-of-protocol-flow"></a>Description du flux de protocole

L’identité de l’application et les types d’identité utilisateur délégué sont décrits dans le flux ci-dessous. La principale différence entre les deux est que l’identité de l’utilisateur délégué doit acquérir tout d’abord un code d’autorisation avant que l’utilisateur ouverture de session et obtenir l’accès à l’API web.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identité de l’application avec OAuth 2.0 Client d’informations d’identification de la subvention

1. Un utilisateur est connecté à une annonce Azure dans l’application web (voir le [Navigateur Web à une Application Web](#web-browser-to-web-application) ci-dessus).


2. L’application web doit acquérir un jeton d’accès afin qu’il peut s’authentifier auprès de l’API web et récupérer la ressource souhaitée. Il fait une demande au point de terminaison jeton Azure annonce, fournissant les informations d’identification, l’ID de client et ID URI d’application que l’API de web.


3. Annonce Azure authentifie l’application et retourne un jeton d’accès JWT qui est utilisé pour appeler l’API web.


4. Via HTTPS, l’application web utilise le jeton d’accès JWT retourné pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête de la demande d’autorisation de l’API web. L’API de web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.

##### <a name="delegated-user-identity-with-openid-connect"></a>Connexion de l’identité de l’utilisateur délégué avec OpenID

1. Un utilisateur est connecté à une application web à l’aide d’Azure AD (voir la section [Application Web le navigateur Web](#web-browser-to-web-application) ci-dessus). Si l’utilisateur de l’application web n’a pas encore consenti à l’application web appeler l’API web en son nom, l’utilisateur devra consentir. L’application affiche les autorisations que requises et s’il une de ces autorisations de niveau administrateur, un utilisateur normal dans le répertoire ne sera pas en mesure de consentement. Ce processus d’autorisation s’applique uniquement aux applications mutualisées, applications de pas mono-utilisateur, que l’application aura déjà les autorisations nécessaires. Lorsque l’utilisateur connecté, l’application web a reçu un jeton ID avec des informations sur l’utilisateur, ainsi que d’un code d’autorisation.


2. À l’aide du code d’autorisation délivré par AD Azure, l’application web envoie une demande à l’extrémité de jeton d’Azure AD qui inclut le code d’autorisation, les détails concernant l’application de client (ID de client et l’URI de redirection) et la ressource de votre choix (application ID URI pour l’API web).


3. Le code d’autorisation et des informations sur l’application web et une API web sont validés par AD Azure. Si la validation réussit, AD Azure renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT.


4. Via HTTPS, l’application web utilise le jeton d’accès JWT retourné pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête de la demande d’autorisation de l’API web. L’API de web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identité de l’utilisateur délégué avec OAuth 2.0 d’octroi de Code

1. Un utilisateur est déjà connecté à une application web, dont mécanisme d’authentification est indépendant de la publicité Azure.


2. L’application web nécessite un code d’autorisation pour acquérir un jeton d’accès afin qu’il émet une demande via le navigateur vers le point de terminaison Azure annonce d’autorisation, fournissant l’ID de client et de rediriger les URI pour l’application web après une authentification réussie. L’utilisateur se connecte à Active Directory Azure.


3. Si l’utilisateur de l’application web n’a pas encore consenti à l’application web appeler l’API web en son nom, l’utilisateur devra consentir. L’application affiche les autorisations que requises et s’il une de ces autorisations de niveau administrateur, un utilisateur normal dans le répertoire ne sera pas en mesure de consentement. Ce processus d’autorisation s’applique uniquement aux applications mutualisées, applications de pas mono-utilisateur, que l’application aura déjà les autorisations nécessaires.


4. Une fois que l’utilisateur a accepté, l’application web reçoit le code d’autorisation dont il a besoin d’acquérir un jeton d’accès.


5. À l’aide du code d’autorisation délivré par AD Azure, l’application web envoie une demande à l’extrémité de jeton d’Azure AD qui inclut le code d’autorisation, les détails concernant l’application de client (ID de client et l’URI de redirection) et la ressource de votre choix (application ID URI pour l’API web).


6. Le code d’autorisation et des informations sur l’application web et une API web sont validés par AD Azure. Si la validation réussit, AD Azure renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT.


7. Via HTTPS, l’application web utilise le jeton d’accès JWT retourné pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête de la demande d’autorisation de l’API web. L’API de web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.

#### <a name="code-samples"></a>Exemples de code

Consultez les exemples de code pour une Application Web pour des scénarios de l’API Web. Et bien, à consulter régulièrement, que nous ajoutons de nouveaux exemples tout le temps. Web [Application Web API](active-directory-code-samples.md#web-application-to-web-api).


#### <a name="registering"></a>Inscription

- Mono-utilisateur : Pour l’identité de l’application et cas d’identité utilisateur délégué, l’application web et l’API web doivent être enregistrés dans le même répertoire dans Azure AD. L’API de web peut être configuré pour exposer un ensemble d’autorisations, qui sont utilisées pour limiter l’accès de l’application web à ses ressources. Si un type d’identité utilisateur délégué est utilisé, l’application web doit sélectionner les autorisations souhaitées à partir de la liste déroulante « Autorisations pour les autres Applications » dans le portail de gestion Azure. Cette étape n’est pas requise si le type d’identité de l’application est utilisé.


- Mutualisée : Tout d’abord, l’application web est configurée pour indiquer les autorisations que requises pour être fonctionnelle. Cette liste des autorisations requises s’affiche dans une boîte de dialogue lorsqu’un utilisateur ou un administrateur dans le répertoire de destination donne le consentement à l’application, ce qui le rend disponible pour leur organisation. Certaines applications requièrent des autorisations de niveau utilisateur uniquement, lequel n’importe quel utilisateur de l’organisation peut consentir à. Autres applications nécessitent des autorisations de niveau administrateur, un utilisateur de l’organisation ne peut pas consentir à. Seul un administrateur de l’annuaire peut donner son consentement pour les applications qui requièrent ce niveau d’autorisation. Lorsque l’administrateur ou l’utilisateur accepte, l’application web et l’API web sont tous deux enregistrés dans leur répertoire.

#### <a name="token-expiration"></a>Expiration du jeton

Lorsque l’application web utilise son code d’autorisation pour obtenir un accès JWT à jeton, il reçoit également un jeton d’actualisation JWT. Lorsque le jeton d’accès arrive à expiration, le jeton d’actualisation peut être utilisé pour authentifier l’utilisateur sans avoir à vous connecter à nouveau. Ce jeton d’actualisation est ensuite utilisé pour authentifier l’utilisateur, ce qui entraîne un nouveau jeton d’accès et un jeton d’actualisation.


### <a name="daemon-or-server-application-to-web-api"></a>Démon ou une Application de serveur pour API Web


Cette section décrit une processus ou une application serveur qui a besoin d’obtenir des ressources à partir d’un site web API. Il existe deux scénarios secondaire qui s’appliquent à cette section : un démon qui doit appeler un web API, basée sur le type de subvention de d’informations d’identification de client de OAuth 2.0 ; et une application serveur (par exemple, une API web) doit appeler un API, basée sur une spécification d’avant-projet OAuth 2.0 On-Behalf-Of de site web.

Pour le scénario lorsqu’une application de démon doit appeler un API, site web, il est important de comprendre les choses. Tout d’abord, l’interaction de l’utilisateur n’est pas possible avec une application de démon, qui nécessite l’application dispose de sa propre identité. Un exemple d’une application de démon est un traitement par lots, ou un service de système d’exploitation en cours d’exécution en arrière-plan. Ce type d’application demande un jeton d’accès à l’aide de son identité de l’application et de présenter son client ID, les informations d’identification (mot de passe ou certificat) et application URI ID pour Azure AD. Après une authentification réussie, le démon reçoit un jeton d’accès dans Active Directory Azure, qui est ensuite utilisé pour appeler l’API web.

Pour le scénario lorsqu’une application serveur doit appeler un web API, il est utile d’utiliser un exemple. Imaginez qu’un utilisateur authentifié dans une application native, et que cette application native doit appeler une API web. Annonce Azure émet un jeton d’accès JWT pour appeler l’API web. Si l’API web doit appeler une autre API de web en aval, il peut utiliser le flux de la part de déléguer une identité de l’utilisateur et s’authentifier sur le site web de deuxième niveau API.

#### <a name="diagram"></a>Diagramme

![Démon ou une Application serveur au diagramme de l’API Web](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Description du flux de protocole

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identité de l’application avec OAuth 2.0 Client d’informations d’identification de la subvention

1. Tout d’abord, l’application serveur doit authentifier avec AD Azure comme de lui-même, sans intervention humaine, comme une boîte de dialogue Ouverture de session interactive. Il fait une demande au point de terminaison jeton Azure annonce, fournir d’informations d’identification, ID de client et ID URI d’application.


2. Annonce Azure authentifie l’application et retourne un jeton d’accès JWT qui est utilisé pour appeler l’API web.


3. Via HTTPS, l’application web utilise le jeton d’accès JWT retourné pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête de la demande d’autorisation de l’API web. L’API de web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.


##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identité de l’utilisateur délégué avec OAuth 2.0 sur la part de spécifications

Le flux indiqué ci-dessous suppose qu’un utilisateur a été authentifié sur une autre application (comme une application native) et leur identité d’utilisateur a été utilisée pour obtenir un jeton d’accès à l’API de web de premier niveau.

1. L’application native envoie le jeton d’accès à l’API de web de premier niveau.


2. L’API web de premier niveau envoie une requête au point de terminaison Azure annonce jeton, en fournissant son client ID et les informations d’identification, ainsi que le jeton d’accès utilisateur. En outre, la demande est envoyée avec un on_behalf_of paramètre indiquant le web API demande nouveaux jetons pour appeler une API web en aval, pour le compte de l’utilisateur d’origine.


3. Annonce Azure vérifie que le site web de premier niveau API dispose d’autorisations pour accéder au site web de deuxième niveau API et valide la demande en retournant un jeton d’accès JWT et un JWT actualiser jeton sur le web de premier niveau API.


4. Via HTTPS, l’API web de premier niveau appelle ensuite l’API de web de deuxième niveau en ajoutant la chaîne de jeton dans l’en-tête d’autorisation dans la demande. L’API de web de premier niveau peut continuer à appeler l’API de second niveau web tant que le jeton d’accès et les jetons d’actualisation sont valides.

#### <a name="code-samples"></a>Exemples de code

Consultez les exemples de code pour le démon ou une Application serveur pour les scénarios de l’API Web. Et bien, à consulter régulièrement, que nous ajoutons de nouveaux exemples tout le temps. [Serveur ou une Application de démon sur Web API](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>Inscription

- Mono-utilisateur : Pour l’identité de l’application et cas d’identité utilisateur délégué, la démon ou une application serveur doit être inscrit dans le même répertoire dans Azure AD. L’API de web peut être configuré pour exposer un ensemble d’autorisations, qui sont utilisés pour limiter le démon ou l’accès à ses ressources du serveur. Si un type d’identité utilisateur délégué est utilisé, l’application serveur doit sélectionner les autorisations souhaitées à partir de la liste déroulante « Autorisations pour les autres Applications » dans le portail de gestion Azure. Cette étape n’est pas requise si le type d’identité de l’application est utilisé.


- Mutualisée : Tout d’abord, la démon ou une application serveur est configurée pour indiquer les autorisations que requises pour être fonctionnelle. Cette liste des autorisations requises s’affiche dans une boîte de dialogue lorsqu’un utilisateur ou un administrateur dans le répertoire de destination donne le consentement à l’application, ce qui le rend disponible pour leur organisation. Certaines applications requièrent des autorisations de niveau utilisateur uniquement, lequel n’importe quel utilisateur de l’organisation peut consentir à. Autres applications nécessitent des autorisations de niveau administrateur, un utilisateur de l’organisation ne peut pas consentir à. Seul un administrateur de l’annuaire peut donner son consentement pour les applications qui requièrent ce niveau d’autorisation. Lorsque l’administrateur ou l’utilisateur accepte, à la fois de l’API de web enregistrement dans leur répertoire.

#### <a name="token-expiration"></a>Expiration du jeton

Lors de la première application utilise son code d’autorisation pour obtenir un accès JWT à jeton, il reçoit également un jeton d’actualisation JWT. Lorsque le jeton d’accès arrive à expiration, le jeton d’actualisation peut être utilisé pour authentifier l’utilisateur sans afficher d’invite d’informations d’identification. Ce jeton d’actualisation est ensuite utilisé pour authentifier l’utilisateur, ce qui entraîne un nouveau jeton d’accès et un jeton d’actualisation.

## <a name="see-also"></a>Voir aussi

[Guide du développeur d’Azure Active Directory](active-directory-developers-guide.md)

[Exemples de Code Azure Active Directory](active-directory-code-samples.md)

[Informations importantes à propos de la substitution de la clé de signature dans Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)
