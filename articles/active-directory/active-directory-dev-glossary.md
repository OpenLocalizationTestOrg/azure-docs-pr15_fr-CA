<properties
   pageTitle="Glossaire de développeur Azure Active Directory | Microsoft Azure"
   description="Une liste de termes pour les concepts de développeur Azure Active Directory et les fonctionnalités couramment utilisées."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="bryanla"/>

# <a name="azure-active-directory-developer-glossary"></a>Glossaire de développeur Azure Active Directory
Cet article contient des définitions pour certains concepts de développement principaux Azure Active Directory (AD), qui sont utiles lors de la formation sur le développement d’applications pour Azure AD.

## <a name="access-token"></a>jeton d’accès
Type de [jeton de sécurité](#security-token) émis par un [serveur d’autorisation](#authorization-server)et utilisé par une [application cliente](#client-application) pour accéder à un [protégé du serveur de ressources](#resource-server). En règle générale sous la forme d’un [Jeton JSON Web (JWT)][JWT], le jeton représente l’autorisation accordée au client par le [propriétaire de la ressource](#resource-owner), un niveau demandé d’accès. Le jeton contient toutes les [demandes](#claim) applicables sur le sujet, l’activation de l’application cliente à l’utiliser en tant que forme d’information d’identification lorsque vous accédez à une ressource donnée. Ceci élimine la nécessité pour le propriétaire de la ressource exposer des informations d’identification pour le client.

Les jetons d’accès sont parfois appelés « Utilisateur + application » ou « Application seule », selon les informations d’identification qui est représentées. Par exemple, lorsqu’une application cliente utilise le :

- [Accorder l’autorisation de « Code d’autorisation »](#authorization-grant), l’utilisateur s’authentifie tout d’abord en tant que propriétaire de la ressource, la délégation d’autorisation au client d’accéder à la ressource. Le client s’authentifie ensuite lors de l’obtention du jeton d’accès. Le jeton peut être parfois plus spécifiquement sous la forme d’un jeton « Utilisateur + application », car elle représente à la fois l’utilisateur qui a autorisé l’application cliente et l’application.
- [Accorder l’autorisation de « Informations d’identification du client »](#authorization-grant), le client fournit l’authentification unique, fonctionne sans authentification/autorisation de-propriétaire de la ressource, afin que le jeton peut être parfois sous la forme d’un jeton « App-Only ».

Voir la [référence de jeton de Azure AD] [ AAD-Tokens-Claims] pour plus de détails.

## <a name="application-manifest"></a>manifeste d’application  
Une fonctionnalité fournie par [l’Azure portal classique]du[AZURE-classic-portal], qui produit une représentation sous forme de JSON de configuration d’identité de l’application, utilisée comme un mécanisme de mise à jour du son associé [Application] [ AAD-Graph-App-Entity] et [ServicePrincipal] [ AAD-Graph-Sp-Entity] les entités. Voir la rubrique [Présentation du manifeste d’application Azure Active Directory] [ AAD-App-Manifest] pour plus de détails.

## <a name="application-object"></a>objet d’application  
Lorsque vous livre/mettre à jour une application dans [Azure portal classique][AZURE-classic-portal], le portail crée ou mises à jour à la fois un objet application et un correspondant [objet principal de service](#service-principal-object) pour ce client. L’objet d’application *définit* l’application de configuration de l’identité globalement (sur tous les locataires d’auquel il a accès), fournissant un modèle à partir duquel ses objets principal service correspondantes sont *dérivées* pour utiliser localement au moment de l’exécution (dans un client spécifique).

Voir [Application et les objets d’entité de Service] [ AAD-App-SP-Objects] pour plus d’informations.

## <a name="application-registration"></a>inscription de l’application  
Pour permettre à une application d’intégrer et de déléguer des fonctions de gestion des identités et l’accès à Active Directory Azure, il doit être enregistré avec une annonce Azure [locataire](#tenant). Lorsque vous enregistrez votre application avec AD Azure, vous fournissez une configuration de l’identité de votre application, ce qui lui permet d’intégrer AD Azure et d’utiliser des fonctionnalités telles que :

- Gestion fiable de session unique à l’aide de la gestion des identités AD Azure et [OpenID connecter] [ OpenIDConnect] implémentation de protocole
- Accès de courtage à [des ressources protégées](#resource-server) par les [applications clientes](#client-application), via la mise en oeuvre de l’annonce Azure OAuth 2.0 [server d’autorisation](#authorization-server)
- [Consentement de framework](#consent) de gestion de l’accès client à des ressources protégées, en fonction de l’autorisation de propriétaire de ressource.

Consultez [l’intégration des applications avec Azure Active Directory] [ AAD-Integrating-Apps] pour plus de détails.

## <a name="authentication"></a>authentification
L’acte d’attaquer une partie des informations d’identification légitimes, offrant la base pour la création d’une entité de sécurité à utiliser pour le contrôle des identités et des accès. Par exemple, au cours d’un [octroi d’autorisation de OAuth2](#authorization-grant) la partie authentification remplit le rôle de l' [application cliente](#client-application), en fonction de la subvention utilisée ou [propriétaire de la ressource](#resource-owner) .

## <a name="authorization"></a>autorisation
L’acte de l’octroi d’une autorisation de principal de sécurité authentifié à faire quelque chose. Il existe deux principaux exemples d’utilisation dans le modèle de programmation AD Azure :

- Au cours d’un flux [d’accorder l’autorisation de OAuth2](#authorization-grant) : lorsque le [propriétaire de la ressource](#resource-owner) accorde l’autorisation de l' [application cliente](#client-application), permettant au client d’accéder aux ressources du propriétaire de la ressource.
- Lors de l’accès aux ressources par le client : comme elle est implémentée par le [serveur de ressources](#resource-server), à l’aide de la [revendication](#claim) de valeurs présentent dans le [jeton d’accès](#access-token) pour prendre des décisions de contrôle d’accès basées sur les.

## <a name="authorization-code"></a>code d’autorisation
Une courte durée de vie « jeton » fourni à une [application cliente](#client-application) par [point de terminaison de l’autorisation](#authorization-endpoint), dans le cadre du flux « code d’autorisation », une des de OAuth2 quatre [accorde l’autorisation](#authorization-grant). Le code est renvoyé à l’application client en réponse à l’authentification d’un [propriétaire de la ressource](#resource-owner), indiquant le que propriétaire de la ressource a délégué l’autorisation d’accéder aux ressources demandées. Dans le cadre du flux, le code est ensuite remboursé pour un [jeton d’accès](#access-token).

## <a name="authorization-endpoint"></a>point de terminaison d’autorisation
Un points de terminaison implémentés par le [serveur d’autorisation](#authorization-server), utilisé pour interagir avec le [propriétaire de la ressource](#resource-owner) afin de fournir une [autorisation accorder](#authorization-grant) au cours d’une autorisation OAuth2 accorder des flux. En fonction du flux de licence d’autorisation utilisé le don effectivement fourni peut varier, y compris un [code d’autorisation](#authorization-code) ou un [jeton de sécurité](#security-token).

Reportez-vous à la section de la spécification OAuth2 [autorisation d’accorder des types] [ OAuth2-AuthZ-Grant-Types] et [autorisation de point de terminaison] [ OAuth2-AuthZ-Endpoint] sections et la [spécification de OpenIDConnect] [ OpenIDConnect-AuthZ-Endpoint] pour plus de détails.

## <a name="authorization-grant"></a>octroi d’autorisation
Une information d’identification qui représente [de propriétaire de la ressource](#resource-owner) [autorisation](#authorization) pour accéder à ses ressources protégées, accordées à une [application cliente](#client-application). Une application cliente peut utiliser un des [quatre types de subventions définies par la structure d’autorisation OAuth2] [ OAuth2-AuthZ-Grant-Types] pour obtenir une subvention, en fonction des exigences de type/client : « octroi de code », « accordent des informations d’identification du client », « grant implicite » et « accordent des informations d’identification de mot de passe de propriétaire ressource ». Les informations d’identification retournées au client sont un [jeton d’accès](#access-token)ou un [code d’autorisation](#authorization-code) (échangés ultérieurement pour un jeton d’accès), en fonction du type de l’octroi d’autorisation utilisé.

## <a name="authorization-server"></a>serveur d’autorisation
Telle que définie par la [Structure d’autorisation de OAuth2][OAuth2-Role-Def], le serveur chargé de la délivrance des accès jetons au [client](#client-application) une fois le [propriétaire de la ressource](#resource-owner) d’authentification et son autorisation réussie. Une [application cliente](#client-application) interagit avec le serveur d’autorisation lors de l’exécution via l' [autorisation](#authorization-endpoint) et des points de terminaison [jeton](#token-endpoint) , conformément à la OAuth2 définissent [accorde l’autorisation](#authorization-grant).

Dans le cas d’intégration d’applications Azure AD, AD Azure implémente le rôle de serveur d’autorisation pour les applications Azure AD et Microsoft API de service, par exemple [Microsoft Graph API][Microsoft-Graph].

## <a name="claim"></a>demande d’indemnisation
Un [jeton de sécurité](#security-token) contient les revendications, qui fournissent des assertions sur une entité (par exemple, une [application cliente](#client-application) ou le [propriétaire de la ressource](#resource-owner)) à une autre entité (par exemple, le [serveur de ressources](#resource-server)). Les revendications sont des paires nom/valeur qui relaient des faits sur le sujet de jeton (par exemple, l’entité de sécurité qui a été authentifié par le [serveur d’autorisation](#authorization-server)). Les revendications présentes dans un jeton donné dépendent de plusieurs variables, y compris le type de jeton, le type d’informations d’identification utilisées pour authentifier le sujet, de la configuration de l’application, etc..

Reportez-vous à la section [référence de jeton Azure AD] [ AAD-Tokens-Claims] pour plus de détails.

## <a name="client-application"></a>application cliente  
Telle que définie par la [Structure d’autorisation de OAuth2][OAuth2-Role-Def], une application qui rend protégé par des demandes de ressources pour le compte du [propriétaire de la ressource](#resource-owner). Le terme « client » n’implique pas des caractéristiques de mise en œuvre de matériel particulier (par exemple, si l’application s’exécute sur un serveur, un ordinateur de bureau ou d’autres périphériques).  

Une application cliente demande [l’autorisation](#authorization) propriétaire d’une ressource à participer à un flux [d’accorder l’autorisation de OAuth2](#authorization-grant) et peut accéder aux API/données pour les compte du propriétaire de la ressource. La structure de OAuth2 d’autorisation [définit deux types de clients][OAuth2-Client-Types], « confidentiel » et « public », en fonction de la capacité du client à respecter la confidentialité de ses informations d’identification. Les applications peuvent implémenter un [client web (confidentiel)](#web-client) qui s’exécute sur un serveur web, un [client natif (public)](#native-client) est installé sur un périphérique ou un [utilisateur-agent - clients (public)](#user-agent-based-client) qui s’exécute dans le navigateur d’un appareil.

## <a name="consent"></a>consentement
Le processus d’un [propriétaire de la ressource](#resource-owner) , l’octroi de l’autorisation d’une [application cliente](#client-application), des [autorisations](#permissions) spécifiques pour accéder aux ressources protégées, le compte du propriétaire de la ressource. En fonction des autorisations demandées par le client, un administrateur ou un utilisateur demandera à donner son consentement pour permettre l’accès à leurs données d’entreprise/individu respectivement. Notez que dans un scénario de [plusieurs utilisateurs](#multi-tenant-application) , de l’application [service principal](#service-principal-object) est également enregistré dans le client de l’utilisateur en terme autorisation.

## <a name="id-token"></a>Jeton d’ID
Une [Connexion de OpenID] [ OpenIDConnect-ID-Token] de [jeton de sécurité](#security-token) fourni par un [point de terminaison d’autorisation](#authorization-endpoint), qui contient des [déclarations](#claim) relatives à l’authentification de l' utilisateur [propriétaire de la ressource [serveur d’autorisation](#authorization-server) ](#resource-owner). Comme un jeton d’accès, les jetons de code sont également représentés sous la forme d’une signature numérique [JSON Web jeton (JWT)][JWT]. Contrairement à un jeton d’accès, les revendications du jeton d’un ID ne sont pas utilisées à des fins liées à l’accès aux ressources et contrôle d’accès spécifiquement.

Reportez-vous à la section [référence de jeton Azure AD] [ AAD-Tokens-Claims] pour plus de détails.

## <a name="multi-tenant-application"></a>application de plusieurs utilisateurs
Une classe de l' [application cliente](#client-application) qui permet de se connecter et [de consentement](#consent) par les utilisateurs mis en service dans n’importe quel AD Azure [locataire](#tenant), y compris les locataires autre que celui où le client est enregistré. En revanche, une application enregistrée comme mono-utilisateur, permet uniquement ouvertures de comptes d’utilisateurs mis en service dans le même client que celui où l’application est enregistrée. Les applications [client natif](#native-client) sont mutualisées par défaut, alors que les applications de [client web](#web-client) ont la possibilité de choisir entre unique et à plusieurs utilisateurs.

Voir [comment signer dans n’importe quel utilisateur AD Azure en utilisant le modèle d’application de mutualisée] [ AAD-Multi-Tenant-Overview] pour plus de détails.

## <a name="native-client"></a>client natif
Un type d' [application client](#client-application) qui est installé en mode natif sur un périphérique. Dans la mesure où tout le code est exécuté sur un périphérique, il est considéré comme un client « public » en raison de son incapacité à stocker des informations d’identification à titre privé/confidentielle. Reportez-vous à la section [profils et types de client OAuth2] [ OAuth2-Client-Types] pour plus de détails.

## <a name="permissions"></a>autorisations
Une [application cliente](#client-application) peut accéder à un [serveur de ressources](#resource-server) en déclarant les demandes d’autorisation. Deux types sont disponibles :

- Les autorisations « Déléguées » [étendue](#scopes) d’accès sous la demande déléguée d’autorisation du [propriétaire de la ressource](#resource-owner)connecté, sont présentés à la ressource au moment de l’exécution en tant que [« CSP » créances](#claim) dans le du client [jeton d’accès](#access-token).
- Les autorisations « Application », qui demande l’accès [par rôle](#roles) sous identités ou informations d’identification de l’application client, sont présentées à la ressource au moment de l’exécution comme [« rôles » revendications](#claim) dans le jeton d’accès du client.

Il est également de surface au cours du processus [de consentement](#consent) , offrant à l’administrateur ou le propriétaire de la ressource permet d’accorder/refuser de l’accès client aux ressources de leurs clients.

Demandes d’autorisation sont configurés sur « Applications » / « « onglet Configurer dans [Azure portal classique][AZURE-classic-portal], sous « Autorisations à d’autres applications », par sélection les souhaité « déléguée autorisations » et « Application » (cette dernière nécessite l’appartenance au rôle de l’administrateur Global). Dans la mesure où un [client public](#client-application) ne peut pas mettre à jour les informations d’identification, il peut demander uniquement les autorisations déléguées, alors qu’un [client confidentielles](#client-application) a la possibilité de demande déléguée et les autorisations de l’application. L' du client [objet application](#application-object) stocke les autorisations déclarées dans sa [propriété de requiredResourceAccess][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>propriétaire de la ressource
Telle que définie par la [Structure d’autorisation de OAuth2][OAuth2-Role-Def], une entité capable d’octroyer l’accès à une ressource protégée. Lorsque le propriétaire de la ressource est une personne, il est appelé à un utilisateur final. Par exemple, lorsqu’une [application cliente](#client-application) souhaite d’accéder aux boîtes aux lettres de l’utilisateur par le biais de l' [API de Microsoft Graph][Microsoft-Graph], il requiert une autorisation de propriétaire de la ressource de la boîte aux lettres.

## <a name="resource-server"></a>serveur de ressources
Telle que définie par la [Structure d’autorisation de OAuth2][OAuth2-Role-Def], un serveur que les hôtes des ressources protégées, capables d’accepter et de répondre aux protégé par des demandes de ressources par les [applications clientes](#client-application) qui présente un [jeton d’accès](#access-token). Également appelé serveur de la ressource protégée, ou application de ressource.

Un serveur de ressources expose les API et applique l’accès à ses ressources protégés via les [étendues](#scopes) et les [rôles](#roles), à l’aide de la structure de d’autorisation OAuth 2.0. L’API d’Azure AD graphique qui permet d’accéder aux données des clients AD Azure et Office 365 API qui fournissent l’accès aux données de messagerie et de calendrier sont des exemples. Ces deux éléments sont également accessibles via l' [API de Microsoft Graph][Microsoft-Graph].  

À l’instar d’une application cliente, configuration d’identité de l’application de la ressource est établie via [l’inscription](#application-registration) dans un locataire AD Azure, fournissant à la fois l’application et l’objet principal du service. Certaines API fournie par Microsoft, comme l’API Azure AD graphique, ont préinscrites entités de service mises à la disposition de tous les locataires au cours de la mise en service.

## <a name="roles"></a>rôles
Comme [étendues](#scopes), rôles fournissent un moyen pour un [serveur de ressources](#resource-server) régir l’accès à ses ressources protégées. Il existe deux types : un rôle « utilisateur » implémente le contrôle d’accès basé sur les rôles pour les utilisateurs/groupes qui nécessitent un accès à la ressource, bien qu’un rôle « application » implémente le même pour les [applications clientes](#client-application) qui ont besoin d’accéder.

Les rôles sont des chaînes de ressource définie (par exemple « approbateur de la dépense, » « Lecture seule », « Directory.ReadWrite.All »), géré dans [Azure portal classique] [ AZURE-classic-portal] par l’intermédiaire de [manifeste d’application](#application-manifest)de la ressource et stocké dans [appRoles propriété la ressource][AAD-Graph-Sp-Entity]. Le portail classique Azure est également utilisé pour affecter des utilisateurs aux rôles des « utilisateurs » et configurer les [autorisations de l’application](#permissions) client pour accéder à un rôle de « application ».

Pour une description détaillée des rôles d’application exposées par les API de graphique Azure annonce, consultez [Graphique API autorisation étendues][AAD-Graph-Perm-Scopes]. Pour obtenir un exemple d’implémentation pas à pas, consultez [contrôle d’accès dans les applications en nuage à l’aide d’Active Directory Azure basée sur les rôles][Duyshant-Role-Blog].

## <a name="scopes"></a>étendues
Tout comme les [rôles](#roles), les étendues fournissent un moyen pour un [serveur de ressources](#resource-server) régir l’accès à ses ressources protégées. Étendues sont utilisées pour implémenter [l’étendue] [ OAuth2-Access-Token-Scopes] contrôle d’accès, d’une [application cliente](#client-application) qui a octroyé l’accès délégué à la ressource par son propriétaire.

Les étendues sont défini par ressource des chaînes (par exemple « Mail.Read », « Directory.ReadWrite.All »), géré dans [Azure portal classique] [ AZURE-classic-portal] par l’intermédiaire de [manifeste d’application](#application-manifest)de la ressource et stocké dans [oauth2Permissions propriété la ressource][AAD-Graph-Sp-Entity]. Le portail classique Azure sert également à configurer le client application [délégué des autorisations](#permissions) pour accéder à une portée.

Une convention de nommage best practice, est d’utiliser un format « resource.operation.constraint ». Pour une présentation détaillée des zones exposées par les API de graphique Azure annonce, consultez [Graphique API autorisation étendues][AAD-Graph-Perm-Scopes]. Pour les zones exposées par les services d’Office 365, [Office 365 API référence d’autorisations], consultez[O365-Perm-Ref].

## <a name="security-token"></a>jeton de sécurité
Un document signé contenant les revendications, tel qu’un jeton de OAuth2 ou d’une assertion de SAML 2.0. Pour un OAuth2, [accorder l’autorisation](#authorization-grant), un [jeton d’accès](#access-token) (OAuth2) et un [ID de jeton](OpenID Connect) sont des types de jetons de sécurité, qui sont implémentés sous la forme d’un [Jeton JSON Web (JWT)][JWT].

## <a name="service-principal-object"></a>objet principal de service
Lorsque vous livre/mettre à jour une application dans [Azure portal classique][AZURE-classic-portal], le portail crée/mises à jour à la fois un [objet application](#application-object) et un objet d’entité de service correspondantes pour ce client. L’objet d’application *définit* la configuration l’application identité globalement (sur tous les locataires d’où l’application associée a accès), et est le modèle à partir duquel ses objets principal service correspondantes sont *dérivées* pour utiliser localement au moment de l’exécution (dans un client spécifique).

Voir [Application et les objets d’entité de Service] [ AAD-App-SP-Objects] pour plus d’informations.

## <a name="sign-in"></a>ouverture de session
Le processus d’une [application cliente](#client-application) lancement d’authentification de l’utilisateur final et la capture d’état connexe, en vue d’acquérir un [jeton de sécurité](#security-token) et de la portée de la session d’application à cet état. L’état peut inclure des artefacts tels que les informations de profil utilisateur et des informations dérivées de revendications de jeton.

La fonction d’ouverture de session d’une application est généralement utilisée pour implémenter single-sign-on (SSO). Il peut également être précédé d’une fonction « inscription », comme point d’entrée pour un utilisateur final d’accéder à une application (lors de la première connexion à). La fonction d’inscription est utilisée pour rassembler et conserver l’état supplémentaire spécifique à l’utilisateur et peut exiger le [consentement de l’utilisateur](#consent).

## <a name="sign-out"></a>déconnexion
Le processus d’authentification non un utilisateur final, détachement de l’état de l’utilisateur associé à la session de [l’application cliente](#client-application) lors de [l’ouverture de session](#sign-in)

## <a name="tenant"></a>clients
Une instance d’un annuaire AD Azure est appelée un locataire AD Azure. Il fournit une variété de fonctionnalités, notamment :

- un service de Registre d’applications intégrées
- authentification des comptes d’utilisateurs et des applications
- Les points de terminaison reste nécessaires pour prendre en charge des différents protocoles, y compris OAuth2 et SAML, y compris [point de terminaison de l’autorisation](#authorization-endpoint), [point de terminaison de jeton](#token-endpoint) et le point de terminaison « commune » utilisé par les [applications de plusieurs utilisateurs](#multi-tenant-application).

Un client est également associé à une annonce Azure ou abonnement à Office 365 au cours de la mise en service de l’abonnement, en fournissant des fonctionnalités d’identité et de gestion de l’accès de l’abonnement. Découvrez [comment obtenir un locataire Azure Active Directory] [ AAD-How-To-Tenant] pour plus d’informations sur les différentes manières vous pouvez accéder à un client. Voir [comment Azure abonnements sont associés à Active Directory de Azure] [ AAD-How-Subscriptions-Assoc] pour plus d’informations sur la relation entre un locataire Azure AD et les abonnements.

## <a name="token-endpoint"></a>point de terminaison de jeton
Un points de terminaison implémentés par le [serveur d’autorisation](#authorization-server) pour prendre en charge les OAuth2 [accorde l’autorisation](#authorization-grant). En fonction de la subvention, il peut être utilisé pour acquérir un [jeton d’accès](#access-token) (et connexes de jeton « Actualiser ») à un [client](#client-application), ou un [jeton d’ID](#ID-token) lorsqu’il est utilisé avec la [Connexion de OpenID] [ OpenIDConnect] protocole.

## <a name="user-agent-based-client"></a>Agent-client par utilisateur
Un type d' [application cliente](#client-application) qui télécharge du code à partir d’un serveur web et s’exécute au sein d’un agent utilisateur (par exemple, un navigateur web), par exemple une Application de Page unique (SPA). Dans la mesure où tout le code est exécuté sur un périphérique, il est considéré comme un client « public » en raison de son incapacité à stocker des informations d’identification à titre privé/confidentielle. Reportez-vous à la section [profils et types de client OAuth2] [ OAuth2-Client-Types] pour plus de détails.

## <a name="user-principal"></a>utilisateur principal
Similaire à la façon dont un objet principal de service est utilisé pour représenter une instance de l’application, un objet principal d’utilisateur est un autre type d’entité de sécurité, qui représente un utilisateur. graphique de publicité Azure [entité utilisateur] [ AAD-Graph-User-Entity] définit le schéma d’un objet utilisateur, y compris les propriétés utilisateur telles que les nom et prénom, nom d’utilisateur principal, l’appartenance au rôle de répertoire, etc.. Ainsi, la configuration d’identités utilisateur pour Azure AD établir une identité utilisateur au moment de l’exécution. L’utilisateur principal est utilisé pour représenter un utilisateur authentifié pour Single Sign-On, enregistrement de délégation de [consentement](#consent) , rendre des décisions de contrôle d’accès, etc..

## <a name="web-client"></a>client Web
Un type d' [application cliente](#client-application) qui exécute tout le code sur un serveur web et est capable de fonctionner comme un client « confidentiel » en stockant en toute sécurité de ses informations d’identification sur le serveur. Reportez-vous à la section [profils et types de client OAuth2] [ OAuth2-Client-Types] pour plus de détails.

## <a name="next-steps"></a>Étapes suivantes
Le [Guide du développeur d’Azure AD] [ AAD-Dev-Guide] est le portail à utiliser pour le développement de publicité Azure tous les autres thèmes, notamment une vue d’ensemble de [l’intégration de l’application] de[ AAD-How-To-Integrate] et les bases de [l’authentification Active Directory Azure et scénarios d’authentification pris en charge][AAD-Auth-Scenarios].

Utilisez la section commentaires de Disqus à l’adresse suivante pour envoyer vos commentaires et nous aider à affiner et à notre contenu de forme.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ./active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
