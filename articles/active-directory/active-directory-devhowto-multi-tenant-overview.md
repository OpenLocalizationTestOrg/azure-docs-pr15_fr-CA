<properties
   pageTitle="Comment générer une application qui peut signer dans n’importe quel utilisateur d’Azure Active Directory | Microsoft Azure"
   description="Étape par étape les instructions pour la création d’une application qui peuvent se connecter un utilisateur depuis n’importe quel client Azure Active Directory, également connu sous le nom d’une application partagée."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="skwan;bryanla"/>

# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Comment signer dans n’importe quel utilisateur d’Azure Active Directory (AD) en utilisant le modèle d’application de plusieurs utilisateurs
Si vous proposez un logiciel comme une application de Service à de nombreuses organisations, vous pouvez configurer votre application pour accepter des connexions à partir de n’importe quel client AD Azure.  Dans Active Directory Azure, il s’agit de rendre votre mutualisée de l’application.  Les utilisateurs de n’importe quel client AD Azure sera en mesure de vous connecter à votre application après l’autorisez à utiliser leur compte avec votre application.  

Si vous avez une application existante qui possède son propre système de compte, ou prend en charge d’autres types de connexion à partir d’autres fournisseurs de nuage, l’ajout de connexion AD Azure à partir de n’importe quel client est aussi simple que l’inscription de votre application, l’ajout de signe dans le code via OAuth2, OpenID de se connecter ou SAML et placer un signe dans avec bouton Microsoft sur votre application. Cliquez sur le bouton ci-dessous pour en savoir plus sur la personnalisation de votre application.

[! [Connexion bouton] [DAS-Sign-In]][AAD-App-Branding]


Cet article suppose que vous êtes familiarisé avec la création d’une application mono-utilisateur pour Azure AD.  Si vous n’êtes pas, tête de sauvegarder sur la [page d’accueil du guide de développeur] [ AAD-Dev-Guide] et essayez l’une de nos Démarrages rapides !

Il existe quatre étapes simples pour convertir votre application en une application de mutualisée AD Azure :

1.  Mettre à jour votre inscription d’application pour être mutualisée
2.  Mettre à jour votre code pour envoyer des requêtes à la /common point de terminaison 
3.  Mettre à jour votre code afin de gérer plusieurs valeurs de l’émetteur
4.  Consentement d’administration et de l’utilisateur de comprendre et d’apporter des modifications de code appropriées

Examinons chaque étape en détail. Vous pouvez également accéder directement à [cette liste d’exemples de mutualisée][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Mettre à jour l’inscription pour être mutualisée
Par défaut, les enregistrements d’application/API web dans Azure AD sont mono-utilisateur.  Vous pouvez rendre votre inscription mutualisée en recherchant le commutateur « Application est mutualisée » sur la page de configuration de l’enregistrement de l’application dans [Azure portal classique] [ AZURE-classic-portal] et en le définissant sur « Oui ».

Remarque : Avant d’effectuer une application mutualisée, AD Azure nécessite l’URI ID d’application de l’application soit globalement unique. L’URI d’ID App est l’une des façons qu'une application est identifiée dans les messages de protocole.  Pour une application mono-utilisateur, il est suffisant pour l’URI d’ID App être unique au sein de ce client.  Pour une application partagée, il doit être globalement unique pour Azure AD puisse trouver l’application sur tous les locataires.  Unicité globale est appliquée en demandant l’URI d’ID App avoir un nom d’hôte qui correspond à un domaine vérifié le locataire AD Azure.  Par exemple, si le nom de votre client est contoso.onmicrosoft.com puis valide URI ID d’application est `https://contoso.onmicrosoft.com/myapp`.  Si votre client a un domaine vérifié de `contoso.com`, puis un URI d’ID App valide serait également `https://contoso.com/myapp`.  Configuration d’une application comme mutualisée échouera si l’URI d’ID App ne suit pas ce modèle.

Les enregistrements de client natif sont mutualisées par défaut.  Vous n’êtes pas obligé d’intervenir pour rendre un natif mutualisée client application d’enregistrement.

## <a name="update-your-code-to-send-requests-to-common"></a>Mettre à jour votre code pour envoyer des requêtes à /common
Dans une application mono-utilisateur, demandes de connexion est envoyées au point de terminaison de connexion du locataire.   Par exemple, pour contoso.onmicrosoft.com le point de terminaison est :

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Demandes envoyées au point de terminaison d’un client peuvent signer dans utilisateurs (ou invités) dans cette cliente pour les applications de ce client.  Avec une application partagée, l’application ne sait pas dès le départ les clients provient de l’utilisateur, afin que vous ne pouvez pas envoyer des demandes au point de terminaison d’un client.  En revanche, les demandes sont envoyées à un point de terminaison MULTIPLEXE dans AD Azure tous les locataires :

    https://login.microsoftonline.com/common

Lorsque AD Azure reçoit une demande sur la /common de point de terminaison, il se connecte l’utilisateur et par conséquent découvre quels clients provient de l’utilisateur.  La/le point de terminaison commun fonctionne avec tous les protocoles d’authentification pris en charge par Active Directory Azure : OpenID se connecter, OAuth 2.0, SAML 2.0 et WS-Federation.

La réponse à l’application de connexion contient un jeton de l’utilisateur.  La valeur de l’émetteur du jeton indique une application quels clients provient de l’utilisateur.  Lorsqu’une réponse est retournée provenant point de terminaison, la valeur de l’émetteur du jeton correspond à des clients de l’utilisateur.  Il est important de noter la /common point de terminaison n’est pas un client et n’est pas un émetteur, c’est juste un multiplexeur.  Lorsque vous utilisez /common, la logique dans votre application, pour valider les jetons doit être mis à jour pour prendre en compte. 

Comme mentionné précédemment, les applications mutualisées doivent également fournir une expérience utilisateur cohérente pour les utilisateurs, suivant l’application Azure AD instructions de personnalisation. Cliquez sur le bouton ci-dessous pour en savoir plus sur la personnalisation de votre application.

[! [Connexion bouton] [DAS-Sign-In]][AAD-App-Branding]

Jetons un œil à l’utilisation de la /common point de terminaison et l’implémentation de votre code plus en détail.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Mettre à jour votre code afin de gérer plusieurs valeurs de l’émetteur
Les applications Web et web API recevoir et valider des jetons de publicité Azure.  

> [AZURE.NOTE] Alors que les applications client natif demandent et de recevoir des jetons d’Azure AD, ils le faire pour les envoyer aux API, où ils sont validés.  Les applications natives ne valident pas les jetons et doivent les traiter comme opaque.

Jetons un œil à la manière dont une application valide les jetons qu’il reçoit à partir d’AD Azure.  Une application mono-utilisateur normalement prend une valeur de point de terminaison comme :

    https://login.microsoftonline.com/contoso.onmicrosoft.com

et l’utiliser pour construire une URL de métadonnées (dans ce cas, OpenID connecter) comme :

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Pour télécharger les deux informations essentielles qui sont utilisés pour valider des jetons : le locataire de signature de clés et la valeur de l’émetteur.  Chaque client AD Azure a une valeur unique de l’émetteur du formulaire :

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

où la valeur GUID est la version de changement de nom-safe de l’ID de client du locataire.  Si vous cliquez sur le lien de métadonnées ci-dessus pour `contoso.onmicrosoft.com`, vous pouvez voir cette valeur émetteur dans le document.

Lorsqu’une application mono-utilisateur valide un jeton, il vérifie la signature du jeton contre les clés de signature à partir du document de métadonnées et permet de s’assurer que la valeur de l’émetteur du jeton correspond à celui qui a été trouvé dans le document de métadonnées.

Depuis la /common point de terminaison ne correspond pas à un client et n’est pas un émetteur, lorsque vous examinez la valeur de l’émetteur dans les métadonnées pour courants qu’il possède une URL basé sur un modèle au lieu d’une valeur réelle :

    https://sts.windows.net/{tenantid}/

Par conséquent, une application partagée ne peut pas valider les jetons en correspondant à la valeur de l’émetteur dans les métadonnées avec la `issuer` valeur du jeton.  Une application partagée doit être logique pour décider des valeurs de l’émetteur sont valides et qui ne sont ne pas, selon le locataire de partie de l’ID de la valeur de l’émetteur.  

Par exemple, si une application mutualisée permet uniquement de connexion à partir des locataires spécifiques qui ont souscrit pour leur service, il doit vérifier la valeur de l’émetteur ou de la `tid` valeur du jeton pour vous assurer que le client est dans leur liste d’abonnés de la demande.  Si une application mutualisée traite de personnes uniquement et ne rend pas les décisions d’accès basées sur les locataires, elle peut ignorer la valeur d’émetteur complètement.

Dans les échantillons de mutualisée que vous trouverez dans la section [Contenu associé](#related-content) à la fin de cet article, la validation de l’émetteur est désactivée pour activer les clients AD Azure pour vous connecter.

Maintenant examinons l’expérience utilisateur pour les utilisateurs qui sont la connexion aux applications de plusieurs utilisateurs.

## <a name="understanding-user-and-admin-consent"></a>Utilisateur de compréhension et de consentement de l’administration
Pour un utilisateur de se connecter à une application dans Azure AD, l’application doit être représentée dans les clients de l’utilisateur.  Cela permet à l’organisation faire appliquer les stratégies uniques lorsque les utilisateurs de leurs clients se connecter à l’application.  Pour une application mono-utilisateur, cet enregistrement est simple ; Il est celui qui se produit lorsque vous enregistrez l’application dans [Azure portal classique][AZURE-classic-portal].

Pour une application partagée, l’enregistrement initial de l’application réside dans le locataire AD Azure utilisé par le développeur.  Lorsqu’un utilisateur à partir d’un autre client se connecte à l’application pour la première fois, Azure annonce les invite à donner son consentement pour les autorisations demandées par l’application.  Si leur consentement, puis une représentation de l’application appelée *service principal* est créée dans le client de l’utilisateur et ouverture de session puisse continuer. Une délégation est également créée dans le répertoire qui enregistre le consentement de l’utilisateur à l’application. Voir [les objets Application et Service Principal] [ AAD-App-SP-Objects] pour plus d’informations sur l’application des objets Application et ServicePrincipal, et comment ils sont associés entre eux.

![Pour les applications monocouches de consentement][Consent-Single-Tier] 

Cette expérience de consentement est affectée par les autorisations demandées par l’application.  Annonce Azure prend en charge deux types d’autorisations, l’application et déléguées :

- Une autorisation de délégué accorde à une application la capacité d’agir comme un utilisateur connecté pour un sous-ensemble des choses que l’utilisateur peut faire.  Par exemple, vous pouvez accorder à une application l’autorisation déléguée à lire le calendrier de l’utilisateur connecté.
- Une application seule autorisation directement à l’identité de l’application.  Par exemple, vous pouvez autoriser une application l’application seule pour lire la liste des utilisateurs d’un client, et il sera en mesure d’effectuer cette opération que qui est connecté à l’application.

Certaines autorisations peuvent être consenties un utilisateur normal, tandis que d’autres nécessitent le consentement d’un administrateur. 

### <a name="admin-consent"></a>Autorisation de l’administrateur
Autorisations d’application seule requièrent toujours consentement d’un administrateur.  Si votre application demande une autorisation d’application uniquement, et un utilisateur normal essaie de se connecter à l’application, votre application obtiendra un message d’erreur indiquant que l’utilisateur n’est pas en mesure de consentement.

Certaines autorisations déléguées nécessitent également l’autorisation d’un administrateur.  Par exemple, la possibilité de réécrire dans Azure annonce que l’utilisateur connecté requiert le consentement d’un administrateur.  Comme autorisations d’application uniquement, si un utilisateur ordinaire essaie de se connecter à une application qui demande une autorisation de délégué qui requiert le consentement de l’administrateur, votre application reçoit une erreur.  Si une autorisation requiert ou non le consentement admin est déterminé par le développeur qui a publié la ressource et se trouve dans la documentation de la ressource.  Liens vers les rubriques décrivant les autorisations disponibles pour l’API de Azure AD graphique et Microsoft Graph sont dans la section [Contenu associé](#related-content) de cet article.

Si votre application utilise les autorisations qui nécessitent le consentement de l’administration, vous devez disposer d’un mouvement dans votre application, tel qu’un bouton ou un lien, où l’administrateur peut initier l’action.  La demande de votre application envoie pour cette action est une demande d’autorisation habituelle OAuth2/OpenID de se connecter, mais qui inclut également la `prompt=admin_consent` paramètre de chaîne de requête.  Une fois que l’administrateur a consenti et l’entité du service est créée dans les clients du client, signe les demandes n’ont pas besoin du `prompt=admin_consent` paramètre.   Étant donné que l’administrateur a décidé des qu'autorisations demandées sont acceptables, aucun autre utilisateur dans le client ne demandera de consentement à partir de ce point.

Le `prompt=admin_consent` paramètre peut être également utilisé par les applications qui requièrent des autorisations qui ne nécessitent pas d’autorisation d’administrateur, mais souhaitez offrir une expérience où l’administration des clients « inscrit » de l’application une fois, et aucun autre utilisateur ne demande de consentement à partir de ce point.

Si une application requiert le consentement de l’administrateur, et que l’administrateur se connecte à l’application, mais la `prompt=admin_consent` paramètre n’est pas envoyé, l’administrateur pourra consentement avec succès à l’application, mais ils seront uniquement consentement pour leur compte d’utilisateur.  Les utilisateurs standard pas pourront se connecter et à l’application de consentement.  Cela est utile si vous souhaitez donner à l’administrateur la possibilité d’Explorer votre application avant d’autoriser l’accès aux autres utilisateurs.

Un administrateur peut désactiver la possibilité pour les utilisateurs normaux à donner son consentement pour les applications.  Si cette fonctionnalité est désactivée, admin accord est toujours requis pour l’application à être défini dans le client.  Si vous souhaitez tester votre application avec le consentement de l’utilisateur normal désactivé, vous pouvez trouver le commutateur de configuration dans le locataire AD Azure section de configuration du [portail de classique Azure][AZURE-classic-portal].

> [AZURE.NOTE] Certaines applications souhaitez une expérience où les utilisateurs standard peuvent consentement initialement, et ultérieurement l’application peut impliquer des autorisations administrateur et demande qui nécessitent le consentement de l’administration.  Il n’y a aucun moyen de faire cela avec un enregistrement unique d’application dans Azure annonce aujourd'hui.  Le prochain point de terminaison AD Azure v2 permettra d’applications pour demander des autorisations lors de l’exécution, plutôt qu’au moment de l’enregistrement, qui permettre à ce scénario.  Pour plus d’informations, consultez le [Guide du développeur Azure AD App modèle v2][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Applications n-tier et de consentement
Votre application peut avoir plusieurs niveaux, chacun représenté par sa propre inscription dans Active Directory Azure.  Par exemple, une application native qui appelle une API web ou une application web qui appelle une API web.  Dans ces deux cas, le client (application native ou web app) demande des autorisations pour appeler la ressource (web API).  Pour le client être consenti avec succès dans les clients d’un client, toutes les ressources auxquelles il demande des autorisations doivent déjà exister dans les clients du client.  Si cette condition n’est pas remplie, AD Azure renvoie une erreur, que la ressource doit être d’abord ajoutée.

Cela peut poser un problème si votre application logique se compose de deux ou plusieurs enregistrements d’application, par exemple un client distinct et une ressource.  Comment obtenir la ressource dans le locataire client premier ?  Annonce Azure couvre ce cas en activant le client et les ressources être consenti en une seule étape, où l’utilisateur voit la somme totale des autorisations demandées par le client et les ressources sur la page consentement.  Pour activer ce comportement, l’enregistrement de la ressource application doit inclure des ID de l’application du client sous la forme d’une `knownClientApplications` dans son manifeste d’application.  Par exemple :

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Cette propriété peut être mis à jour par l’intermédiaire de la ressource [manifeste de l’application][AAD-App-Manifest]et est présentée dans un client natif n-tier appel web API, exemple dans la section [Contenu associé](#related-content) à la fin de cet article. Le diagramme ci-dessous fournit une vue d’ensemble de l’accord pour une application à plusieurs niveaux :

![Consentement clients connus de n-tier application][Consent-Multi-Tier-Known-Client] 

Un cas similaire qui se produit si les différents niveaux d’une application sont enregistrés dans les locataires différents.  Par exemple, considérez le cas de la création d’une application client natif qui appelle l’API d’en ligne Exchange Office 365.  Pour développer l’application et les versions ultérieur de l’application native pour s’exécuter dans les clients d’un client en natif, l’entité du service Exchange Online doit être présente.  Dans ce cas, le client doit acheter en ligne Exchange pour le service principal devant être créé dans leurs clients.  Dans le cas d’une API générée par une société autre que Microsoft, le développeur de l’API doit fournir un moyen pour leurs clients à donner son consentement de leur application dans un client de client, par exemple une page web que les lecteurs de consentement en utilisant les mécanismes décrits dans cet article.  Une fois l’entité du service est créée dans le client, l’application native peut obtenir des jetons pour l’API.

Le diagramme ci-dessous fournit une vue d’ensemble de l’accord pour une application n-tier enregistrée dans les locataires différents :

![Consentement à l’application de plusieurs à plusieurs niveaux][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Révocation du consentement
Les utilisateurs et les administrateurs peuvent révoquer l’autorisation à votre application à tout moment :

- Les utilisateurs révoquer l’accès à des applications individuelles en les supprimant de leurs [Applications de panneau d’accès] [ AAD-Access-Panel] liste.
- Administrateurs de révoquer l’accès aux applications en les supprimant de Azure AD à l’aide de la section de gestion AD Azure d' [Azure portal classique][AZURE-classic-portal].

Si un administrateur consent à une application pour tous les utilisateurs d’un client, les utilisateurs ne peuvent pas révoquer l’accès individuellement.  Seul l’administrateur peut révoquer l’accès et uniquement pour l’ensemble de l’application.

### <a name="consent-and-protocol-support"></a>Prise en charge de protocole et de consentement
Consentement est pris en charge dans Azure AD via le OAuth, OpenID se connecter, WS-Federation et les protocoles SAML.  Les protocoles SAML et WS-Federation ne gèrent pas la `prompt=admin_consent` paramètre, afin que le consentement de l’administration est uniquement possible via OAuth et OpenID se connecter.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Les Applications de plusieurs utilisateurs et la mise en cache des jetons d’accès
Les applications mutualisées peuvent également obtenir des jetons d’accès pour appeler des API qui est protégés par AD Azure.  Une erreur courante lors de l’utilisation de la bibliothèque de l’authentification Active Directory (ADAL) avec une application partagée est initialement demander un jeton pour un utilisateur à l’aide de /common, recevoir une réponse et ensuite demander un jeton suivant pour ce même utilisateur, également à l’aide de /common.  Étant donné que la réponse à partir d’AD Azure provient d’un client, pas/commun, ADAL met en cache le jeton comme provenant du client. L’entrée du cache d’absences dans le prochain appel à /common pour obtenir un jeton d’accès de l’utilisateur et l’utilisateur est invité à vous connecter à nouveau.  Pour éviter de manquer de cache, veillez à ce que les appels suivants à un utilisateur déjà connecté sont effectuées au point de terminaison du locataire.

## <a name="related-content"></a>Contenu associé

- [Exemples d’applications][AAD-Samples-MT]
- [Instructions de personnalisation pour les Applications][AAD-App-Branding]
- [Guide du développeur d’Azure AD][AAD-Dev-Guide]
- [Objets d’application et les objets d’entité de Service][AAD-App-SP-Objects]
- [Intégration des Applications avec Azure Active Directory][AAD-Integrating-Apps]
- [Vue d’ensemble de l’infrastructure de consentement][AAD-Consent-Overview]
- [Microsoft Graph API autorisations étendues][MSFT-Graph-AAD]
- [Graphique de publicité Azure API autorisations étendues][AAD-Graph-Perm-Scopes]

Utilisez la section de commentaires Disqus ci-dessous pour envoyer vos commentaires et nous aider à affiner et à notre contenu de forme.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














