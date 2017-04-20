<properties
    pageTitle="Inscription de l’application portail rubriques d’aide | Microsoft Azure"
    description="Une description des différentes fonctions dans le portail de l’enregistrement de Microsoft app."
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

# <a name="app-registration-reference"></a>Référence de l’inscription d’application
Ce document fournit le contexte et les descriptions des diverses fonctionnalités trouvées dans le portail de l’enregistrement de Microsoft App [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>Mes Applications
Cette liste contient toutes les applications enregistrées pour une utilisation avec le point de terminaison v2.0 Azure AD.  Ces applications ont la possibilité de connecter les utilisateurs avec les deux comptes personnels à partir de comptes de travail ou aux études d’Azure Active Directory et de compte Microsoft.  Pour en savoir plus sur le point de terminaison v2.0 AD Azure, consultez notre [vue d’ensemble de la version 2.0](active-directory-appmodel-v2-overview.md).  Ces applications peuvent également être utilisées pour s’intégrer à l’extrémité de l’authentification de compte Microsoft, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Applications Live SDK
Cette liste contient toutes vos applications enregistrées pour être utilisé uniquement avec un compte Microsoft.  Ils ne sont pas activés pour une utilisation avec Active Directory Azure quelque.  Il s’agit d’où vous trouverez toutes les applications qui avaient précédemment été enregistrées avec le portail des développeurs MSA au `https://account.live.com/developers/applications`.  Toutes les fonctions que vous avez effectué précédemment à `https://account.live.com/developers/applications` peuvent désormais être exécutées dans ce nouveau portail, `https://apps.dev.microsoft.com`.  Si vous avez d’autres questions concernant vos applications de compte Microsoft, veuillez nous contacter.

## <a name="application-secrets"></a>Secrets de l’application
Les secrets d’application sont des informations d’identification qui permettent à votre application effectuer une [authentification du client](http://tools.ietf.org/html/rfc6749#section-2.3) fiable avec Azure AD.  Dans OAuth & OpenID de se connecter, un secret de l’application est communément appelé un `client_secret`.  Dans le protocole de la version 2.0, n’importe quelle application qui reçoit un jeton de sécurité en offrant un emplacement web (utilisant un `https` schéma) doit utiliser le secret d’une application pour identifier lui-même pour Azure AD au remboursement de ce jeton de sécurité.  En outre, n’importe quel client natif qui reçoit les jetons sur un périphérique sera interdite à partir de l’utilisation d’un secret de l’application pour effectuer l’authentification du client, afin d’empêcher le stockage de secrets dans des environnements non sécurisés.

Chaque application peut contenir deux secrets d’application valide à un moment donné dans le temps.  En conservant les deux secrets, vous avez l’ablilty pour effectuer la substitution de la clé périodique sur la totalité de l’environnement de votre application.  Une fois que vous avez migré l’intégralité de votre application vers un nouveau secret, vous pouvez supprimer l’ancien secret et mettre en service un nouveau.

À ce stade, que deux types de secrets de l’application sont autorisées dans le portail de l’enregistrement d’app.  Choisissez de **Générer un nouveau mot de passe** pour générer et stocker un secret partagé dans le magasin de données respectifs, que vous pouvez utiliser dans votre application.  Choisir de **Générer une nouvelle paire de clés** crée une nouvelle paire de clés publique/privée qui peut être téléchargée et utilisée pour l’authentification de client AD Azure.

## <a name="profile"></a>Profil
La section de profil du portail de l’enregistrement d’application peut être utilisée pour personnaliser la page de votre application de connexion.  À ce stade, vous pouvez modifier le logo de la page application, les conditions de la déclaration de confidentialité et les URL du service de connexion.  Le logo doit être de 48 x 48 ou 50 x 50 pixels image transparente dans un fichier GIF, PNG ou JPEG 15 Ko ou plus petite.  Essayez de la modification des valeurs et l’affichage de la connexion qui en résulte dans la page !

## <a name="live-sdk-support"></a>Prise en charge du Kit de développement logiciel de Live
Lorsque vous activez le « Support de Live SDK », aucun secret d’application que vous créez sera déployé dans les deux l’annonce Azure et les magasins de données de Microsoft Account.  Cela permettra à votre application intégrer directement avec le service Microsoft Account (login.live.com).  Si vous souhaitez créer une application à l’aide de Microsoft Account directement (au lieu d’utiliser le point de terminaison v2.0 Azure AD), il se peut que vous devez vous assurer que la prise en charge du Kit de développement logiciel de Live est activé.

Désactivation de la prise en charge de Live SDK permet de garantir que le secret de l’application est uniquement écrit dans les données AD Azure stocker.  Les données AD Azure store intègre des réglementations à l’échelle de l’entreprise qui lui permettent de répondre à certaines normes, telles que de respect de la loi FISMA.  Si vous activez la prise en charge de Live SDK, votre application ne peut pas mettre en conformité avec certains de ces standards.

Si vous envisagez de seulement jamais utiliser le point de terminaison AD Azure v2.0, vous pouvez désactiver en toute sécurité la prise en charge de Live SDK.

