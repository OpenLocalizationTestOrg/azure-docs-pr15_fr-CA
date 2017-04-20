<properties
   pageTitle="Présentation de l’OAuth2 implicite accorder des flux dans Azure Active Directory | Microsoft Azure"
   description="En savoir plus sur la mise en oeuvre de Azure d’Active Directory de la OAuth2 implicite accordent des flux, et si elle est appropriée pour votre application."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="vittorib;bryanla"/>

# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>La compréhension du flux d’octroi implicite OAuth2 dans Azure Active Directory (AD)

L’octroi implicite OAuth2 est tristement célèbre pour devenir la subvention avec la liste la plus longue des problèmes de sécurité dans la spécification OAuth2. Et pourtant, c’est l’approche mise en œuvre par ADAL JS et celui que nous vous recommandons de lors de l’écriture d’applications de SPA. Ce que cela signifie ? Il s’agit d’une question de compromis : et en fait, l’octroi implicite est la meilleure approche, vous pouvez rechercher pour les applications qui utilisent une API Web via JavaScript à partir d’un navigateur.

## <a name="what-is-the-oauth2-implicit-grant"></a>Quel est l’octroi implicite OAuth2 ?

Les résulats [accorder de code d’autorisation de OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) est l’octroi d’autorisation qui utilise deux points de terminaison séparés. Le point de terminaison de l’autorisation est utilisée pour la phase d’interaction utilisateur, qui se traduit par un code d’autorisation. Le point de terminaison de jeton est ensuite utilisé par le client pour échanger le code d’un jeton d’accès et souvent un jeton d’actualisation. Les applications Web sont tenues de présenter leurs propres informations d’identification de l’application au point de terminaison jeton, afin que le serveur d’autorisation peut authentifier le client.

[OAuth2 implicite accorder](https://tools.ietf.org/html/rfc6749#section-1.3.2) d’est une variante permettant à un client d’obtenir un jeton d’accès (et id_token, dans le cas de [OpenId connecter](http://openid.net/specs/openid-connect-core-1_0.html)) directement à partir de l’extrémité de l’autorisation, sans contacter le point de terminaison jeton ni l’authentification de l’application cliente. Cette variante a été spécialement conçue pour les applications qui s’exécutent dans un navigateur Web à base de JavaScript : dans la spécification de OAuth2 d’origine, les jetons sont retournés dans un fragment d’URI. Qui rend les jetons bits disponibles pour le code JavaScript dans le client, mais elle garantit qu’ils ne seront pas incluses dans les redirige vers le serveur. Renvoi des jetons via navigateur redirige directement à partir de l’extrémité de l’autorisation. Il a également l’avantage de l’élimination des exigences pour les appels d’origine, qui sont nécessaires si l’application JavaScript est requis pour contacter le point de terminaison de jeton.

Une caractéristique importante de la subvention implicite OAuth2 est le fait que ces flux de jetons d’actualisation jamais retour au client. Comme nous le verrons dans la section suivante, qui n’est pas vraiment nécessaire et en fait serait un problème de sécurité.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Scénarios pour l’octroi implicite OAuth2

Comme la spécification OAuth2 lui-même déclare, l’octroi implicite a été conçu pour permettre aux applications de l’agent utilisateur – c'est-à-dire, applications JavaScript exécuté dans un navigateur. La caractéristique essentielle de ces applications est que le code JavaScript est utilisé pour accéder aux ressources du serveur (généralement une API Web) et de mise à jour de l’application UX en conséquence. Pensez à des applications telles que Gmail ou Outlook Web Access : lorsque vous sélectionnez un message dans votre boîte de réception, seul le panneau de visualisation du message change pour afficher la nouvelle sélection, tandis que le reste de la page reste inchangé. Il s’agit de Contrairement aux traditionnels redirection Web applications, où chaque interaction utilisateur provoque une publication (postback) de page entière et un rendu de la page entière de la réponse du serveur.

Applications qui utilisent l’approche JavaScript basé à son extrême sont appelées Applications à Page unique, ou SPAs : l’idée est que ces applications servent uniquement d’une première page HTML et le JavaScript associé, avec toutes les interactions résultantes piloté par les appels de l’API Web effectuées via JavaScript. Toutefois, les approches hybride, où l’application est principalement axé sur la publication, mais effectue des appels JS occasionnels, ne sont pas rares – la discussion sur l’utilisation du flux implicite pertinente pour ceux ainsi.

Applications basées sur les redirection sécurisent généralement leurs demandes via des cookies, toutefois, que l’approche ne fonctionne pas aussi bien pour les applications de JavaScript. Les cookies ne fonctionnent que sur le domaine qu’ils ont été générés, tandis que les appels JavaScript peuvent être dirigées vers d’autres domaines. En fait, qui sera souvent le cas : réflexion des demandes d’appel de l’API du Microsoft Graph API, API Office, Azure – tous les de résidant en dehors du domaine d’où l’application est pris en charge. Une tendance croissante pour les applications de JavaScript n’est aucun back-end, 100 % 3e partie API Web pour implémenter leur fonction d’entreprise de confiance.

Actuellement, la méthode préférée de protection des appels à une API Web est d’utiliser l’approche de jeton de support OAuth2, où chaque appel est accompagné d’un jeton d’accès OAuth2. L’API Web examine le jeton d’accès entrant et, s’il détecte qu’il les étendues nécessaires, elle accorde l’accès à l’opération demandée. Le flux implicite offre un mécanisme pratique pour les applications de JavaScript obtenir les jetons d’accès pour une API Web, offre de nombreux avantages en ce qui concerne les cookies :

- Jetons peuvent être obtenus fiable sans avoir aux entre des appels d’origine – inscription obligatoire de la redirection URI vers lequel les jetons sont retour garantit que les jetons ne sont pas déplacées
- Les applications JavaScript peuvent obtenir des jetons d’accès autant que nécessaire, pour autant API Web qu’ils ciblent – sans aucune restriction sur les domaines
- Fonctionnalités HTML5 comme la session ou de stockage local accorder un contrôle total sur le jeton de la mise en cache et la gestion de la durée de vie, alors que la gestion des cookies est opaque à l’application
- Les jetons d’accès ne sont pas exposés à des attaques de falsification (CSRF) de demande entre sites

Le flux d’octroi implicite n’émet pas de jetons de l’actualisation, principalement pour des raisons de sécurité. Un jeton d’actualisation n’est pas une portée aussi étroitement les jetons d’accès, accordant beaucoup plus d’énergie donc causer beaucoup plus de dommages dans le cas où il est l’objet de fuites. Dans le flux implicite, les jetons sont remis dans l’URL, donc les risques d’interception sont supérieur à l’octroi du code d’autorisation.

Toutefois, notez qu’une application JavaScript a un autre mécanisme à sa disposition pour le renouvellement de jetons d’accès sans afficher d’invite à plusieurs reprises l’utilisateur des informations d’identification. L’application peut utiliser un iframe masqué pour effectuer de nouvelles demandes de jetons sur le point de terminaison de l’autorisation de publicité Azure : tant que le navigateur a toujours une session active (lire : a un cookie de session) par rapport au domaine AD Azure, la demande d’authentification peut se produire avec succès sans aucune intervention de l’utilisateur. 

Ce modèle permet l’application JavaScript à renouveler indépendamment des jetons d’accès et même Attirez-en de nouveau pour une nouvelle API (à condition que l’utilisateur est déjà consenti pour eux. Cela permet d’éviter la charge supplémentaire d’acquisition, de maintenance et de protection un artefact de valeur élevée, tel qu’un jeton d’actualisation. L’artefact qui permet le renouvellement en mode silencieux, le cookie de session AD Azure, est gérée en dehors de l’application. Un autre avantage de cette approche est qu'un utilisateur peut se déconnecter à partir d’AD Azure, à l’aide de toutes les applications signées dans Azure annonce, en cours d’exécution dans tous les onglets du navigateur. Cela entraîne la suppression du cookie session AD Azure, et l’application JavaScript automatiquement perdrez la possibilité de renouvellement de jetons pour l’utilisateur signé par.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>L’octroi implicite n’est adapté à mon application ?

L’octroi implicite présente plus de risques que les autres aides versées. Les zones que vous devez prêter attention à sont bien documentées (voir, par exemple [Une mauvaise utilisation du jeton d’accès pour propriétaire de la ressource emprunter l’identité de flux implicite] [ OAuth2-Spec-Implicit-Misuse] et le [modèle de menace OAuth 2.0 et les considérations de sécurité][OAuth2-Threat-Model-And-Security-Implications]). Toutefois, le profil de risque plus élevé est en grande partie due au fait qu’il est destiné à permettre aux applications d’exécuter du code actif, pris en charge par une ressource distante à un navigateur. Si vous envisagez une architecture SPA, aucun composants back-end ou souhaitez appeler un API Web via JavaScript, recommandé d’utiliser le flux implicite pour l’acquisition de jeton.

Si votre application est un client natif, le flux implicite n’est pas parfait. L’absence du cookie de session AD Azure dans le contexte d’un client natif PRIVE des moyens de maintenir une session longue durée de vie de votre application. Qui signifie que votre application vous invite à plusieurs reprises l’utilisateur lors de l’obtention des jetons d’accès pour les nouvelles ressources.

Si vous développez une application Web qui comprend un back-end et l’utilisation d’une API à partir de son code de back-end, le flux implicite n’est également pas parfaitement. Autres subventions vous donnent beaucoup plus d’énergie. Par exemple, l’octroi d’informations d’identification de client OAuth2 fournit la possibilité d’obtenir des jetons qui reflètent les autorisations attribuées à l’application elle-même, par opposition aux délégations de l’utilisateur. Cela signifie que le client a la possibilité de mettre à jour l’accès par programme aux ressources même lorsqu’un utilisateur n’est pas activement engagé dans une session et ainsi de suite. Non seulement cela, mais de telles subventions donnent des garanties de sécurité plus élevés. Par exemple, les jetons d’accès jamais de transit via le navigateur de l’utilisateur, ils ne risque d’être enregistrées dans l’historique du navigateur et ainsi de suite. L’application cliente peut également effectuer une authentification renforcée lors de la demande d’un jeton.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une liste complète des ressources de développement, y compris des informations de référence pour les protocoles et l’autorisation de OAuth2 accorder la prise en charge de flux par AD Azure, consultez [Azure AD du développeur Guide][AAD-Developers-Guide]
- Comment [intégrer une application avec Azure AD]  [ ACOM-How-To-Integrate] pour la profondeur supplémentaire sur le processus d’intégration de l’application.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16 
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

