<properties
    pageTitle="Comment faire pour utiliser le plug-in de Apache Cordova pour les applications mobiles Azure"
    description="Comment faire pour utiliser le plug-in de Apache Cordova pour les applications mobiles Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Comment faire pour utiliser la bibliothèque Apache Cordova Client pour les applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide vous apprend à effectuer des scénarios courants utilisant la plus récente [Du plug-in de Apache Cordova pour les applications mobiles Azure]. Si vous ne connaissez pas les applications Azure Mobile, première complète [Azure Mobile applications Quick Start] pour créer un serveur principal, créez une table et télécharger un projet Apache Cordova prédéfini. Dans ce guide, nous concentrer sur le plug-in de Cordova de Apache côté client.

## <a name="supported-platforms"></a>Plates-formes prises en charge

Ce kit prend en charge Apache Cordova v6.0.0 et ultérieurement sur iOS, Android et Windows périphériques.  La prise en charge de la plate-forme est la suivante :

* API Android 19-24 (KitKat via la commande)
* iOS 8.0 et versions ultérieures.
* Windows Phone 8.0
* Windows Phone 8.1
* Plate-forme Windows universel

##<a name="Setup"></a>Le programme d’installation et conditions requises

Ce guide suppose que vous avez créé un back-end avec une table. Ce guide suppose que la table a le même schéma que les tables dans ces didacticiels. Ce guide suppose également que vous avez ajouté le plug-in de Cordova Apache à votre code.  Si vous n'avez pas fait, vous pouvez ajouter le plug-in Cordova de Apache à votre projet sur la ligne de commande :

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Pour plus d’informations sur la création de [votre première application Apache Cordova], consultez leur documentation.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]


##<a name="auth"></a>Comment : authentifier les utilisateurs

Service d’application Azure prend en charge l’authentification et l’autorisation des utilisateurs de l’application à l’aide de différents fournisseurs d’identité externes : Twitter, Microsoft Account, Facebook et Google. Vous pouvez définir des autorisations sur les tables pour restreindre l’accès à des opérations spécifiques aux utilisateurs authentifiés uniquement. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour mettre en œuvre des règles d’autorisation dans les scripts de serveur. Pour plus d’informations, consultez le didacticiel [mise en route de l’authentification] .

Lors de l’utilisation de l’authentification dans une application Cordova de Apache, les plug-ins Cordova suivants doivent être disponibles :

* [Dispositif de plug-in de cordova]
* [plug-in-cordova-inappbrowser]

Deux flux d’authentification sont pris en charge : un flux du serveur et un flux de client.  Le flux de serveur fournit l’expérience d’authentification la plus simple, car il repose sur l’interface du fournisseur web d’authentification. Le flux client permet une intégration plus poussée avec des fonctionnalités spécifiques au périphérique tels que single-sign-on car il repose sur les kits de développement logiciel spécifique au fournisseur spécifique à l’appareil.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Comment : configurer votre Service d’application Mobile pour les URL de redirection externe.

Plusieurs types d’applications d’Apache Cordova permet de gérer les flux OAuth UI une capacité de bouclage.  Flux OAuth UI sur localhost causer des problèmes dans la mesure où le service d’authentification seulement sait utiliser votre service par défaut.  Exemples de flux de OAuth UI problématiques :

- L’émulateur d’ondulation.
- Recharger avec IONIQUES en direct.
- Le serveur principal mobile s’exécute
- Exécuter le principal mobile dans un autre Service d’application Azure que l’authentification d’un.

Suivez ces instructions pour ajouter vos paramètres locaux pour la configuration :

1. Connectez-vous au [portail Azure]
2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre application Mobile.
3. Cliquez sur **Outils**
4. Cliquez sur **Explorateur de ressources** dans le menu de l’observer, puis cliquez sur **OK**.  Une nouvelle fenêtre ou onglet s’ouvre.
5. Développez la **configuration**, des nœuds de **authsettings** pour votre site dans le cadre de gauche.
6. Cliquez sur **Modifier**
7. Recherchez l’élément « allowedExternalRedirectUrls ».  Elle peut être définie à null ou un tableau de valeurs.  Modifiez la valeur pour la valeur suivante :

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Remplacer les URL avec l’URL de votre service.  Exemples : « http://localhost:3000 » (pour l’exemple de service Node.js) ou « http://localhost:4400 » (pour le service de l’ondulation).  Toutefois, ces URL est des exemples - votre situation, y compris pour les services mentionnés dans les exemples, peuvent être différents.
8. Cliquez sur le bouton **Lecture** dans le coin supérieur droit de l’écran.
9. Cliquez sur le vert **placer** le bouton.

Les paramètres sont enregistrés à ce stade.  Ne fermez pas la fenêtre du navigateur jusqu'à ce que les paramètres ont terminé l’enregistrement.
Également ajouter ces URL loopback aux paramètres CORS pour votre application de Service :

1. Connectez-vous au [portail Azure]
2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre application Mobile.
3. La lame de paramètres s’ouvre automatiquement.  Si elle n’est pas le cas, cliquez sur **Tous les paramètres**.
4. Cliquez sur **CORS** sous le menu de l’API.
5. Entrez l’URL que vous souhaitez ajouter dans la zone fournie, puis appuyez sur ENTRÉE.
6. Entrez l’URL supplémentaires si nécessaire.
7. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

Il faut environ 10 à 15 secondes pour que les nouveaux paramètres prennent effet.

##<a name="register-for-push"></a>Comment : inscrire pour les Notifications de type Pousser

Installer le [plug-in phonegap-push] pour gérer les notifications de transmission.  Ce plug-in peut être facilement ajouté à l’aide de la `cordova plugin add` commande sur la ligne de commande ou via le programme d’installation du plug-in Git dans Visual Studio.  Le code suivant dans votre application d’Apache Cordova enregistre votre périphérique pour les notifications de transmission :

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Le Kit de développement de concentrateurs de Notification permet d’envoyer des notifications de type pousser à partir du serveur.  Ne jamais envoyer de notifications de type Pousser directement à partir de clients. Cela pourrait être utilisé pour déclencher une attaque de déni de service contre les concentrateurs de Notification ou de la solution.  La solution peut interdire le trafic à la suite de ces attaques.

<!-- URLs. -->
[Azure portal]: https://portal.azure.com
[Guide de démarrage rapide de Apps Mobile Azure]: app-service-mobile-cordova-get-started.md
[Mise en route de l’authentification]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Plug-in d’Apache Cordova pour les applications mobiles Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[votre première application Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[plug-in PhoneGap-push]: https://www.npmjs.com/package/phonegap-plugin-push
[Dispositif de plug-in de cordova]: https://www.npmjs.com/package/cordova-plugin-device
[plug-in-cordova-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
