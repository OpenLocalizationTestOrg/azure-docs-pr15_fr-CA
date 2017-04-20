<properties
    pageTitle="Comment faire pour déboguer une application web de Node.js dans le Service d’application Azure"
    description="Découvrez comment déboguer une application web de Node.js dans le Service d’application Azure."
    tags="azure-portal"
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-debug-a-nodejs-web-app-in-azure-app-service"></a>Comment faire pour déboguer une application web de Node.js dans le Service d’application Azure

Azure fournit des diagnostics intégrés pour faciliter le débogage des applications de Node.js hébergées dans [Azure Application Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. Dans cet article, vous apprendrez comment activer la journalisation de stdout et stderr, d’afficher des informations sur l’erreur dans le navigateur et télécharger et afficher les fichiers journaux.

Tests de diagnostic pour les applications de Node.js hébergées sur Azure est fourni par [IISNode]. Bien que cet article décrit les paramètres les plus courants pour la collecte des informations de diagnostic, il ne fournit pas une référence complète pour l’utilisation de IISNode. Pour plus d’informations sur l’utilisation de IISNode, consultez le [Fichier Readme de IISNode] sur GitHub.

<a id="enablelogging"></a>
## <a name="enable-logging"></a>Activer la journalisation

Par défaut, une application web de Service d’application capture uniquement les informations de diagnostic concernant les déploiements, comme lorsque vous déployez une application web à l’aide de Git. Ces informations sont utiles si vous rencontrez des problèmes lors du déploiement, telle qu’un échec lors de l’installation d’un module référencé dans la **package.json**, ou si vous utilisez un script de déploiement personnalisé.

Pour activer la journalisation de flux stdout et stderr, vous devez créer un fichier **IISNode.yml** à la racine de votre application Node.js et ajoutez le code suivant :

    loggingEnabled: true

Cela permet l’enregistrement de stderr et stdout à partir de votre application Node.js.

Le fichier **IISNode.yml** peut également servir à contrôle si les erreurs simples ou les erreurs des développeurs sont renvoyées au navigateur lorsqu’une défaillance se produit. Pour activer les erreurs des développeurs, ajoutez la ligne suivante au fichier **IISNode.yml** :

    devErrorsEnabled: true

Une fois cette option activée, IISNode renvoie le dernier 64 Ko d’informations envoyées à stderr au lieu d’une erreur conviviale tel que « une erreur de serveur interne s’est produite ».

> [AZURE.NOTE] DevErrorsEnabled est utile lorsque vous diagnostiquez des problèmes pendant le développement, l’activer dans un environnement de production peut entraîner des erreurs de développement qui est envoyés aux utilisateurs finaux.

Si le fichier **IISNode.yml** n’existait pas déjà dans votre application, vous devez redémarrer votre application web après la publication de l’application mis à jour. Si vous changez simplement les paramètres dans un fichier **IISNode.yml** existant qui a déjà été publié, aucun redémarrage n’est nécessaire.

> [AZURE.NOTE] Si votre application web a été créée en utilisant les outils de ligne de commande de Azure ou applets de commande PowerShell Azure, un fichier de **IISNode.yml** par défaut est automatiquement créé.

Pour redémarrer l’application web, sélectionnez l’application web du [Portail Azure](https://portal.azure.com)et puis cliquez sur le bouton **redémarrer** :

![Redémarrez le bouton][restart-button]

Si les outils de ligne de commande de Azure sont installés dans votre environnement de développement, vous pouvez utiliser la commande suivante pour redémarrer l’application web :

    azure site restart [sitename]

> [AZURE.NOTE] LoggingEnabled et devErrorsEnabled sont le plus couramment utilisé les options de configuration IISNode.yml pour la capture des informations de diagnostic, IISNode.yml permet de configurer diverses options pour votre environnement d’hébergement. Pour obtenir une liste complète des options de configuration, consultez le fichier [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml) .

<a id="viewlogs"></a>
## <a name="accessing-logs"></a>L’accès aux journaux

Journaux de diagnostic sont accessibles de trois manières ; En utilisant le protocole FTP (File Transfer), télécharger une archive Zip, ou sous la forme d’une ligne mise à jour de flux du journal (également connu sous le nom d’une queue). Téléchargez l’archive Zip des fichiers journaux ou afficher le flux en direct requièrent les outils de ligne de commande d’Azure. Elles peuvent être installées à l’aide de la commande suivante :

    npm install azure-cli -g

Une fois installées, les outils sont accessibles à l’aide de la commande « azure ». Les outils de ligne de commande doivent d’abord être configurés pour utiliser votre abonnement Azure. Pour plus d’informations sur la manière d’accomplir cette tâche, consultez le **comment télécharger et importer les paramètres de publication** section de l’article [comment utiliser l’Azure Command-line Tools](../xplat-cli-connect.md) .

###<a name="ftp"></a>FTP

Pour accéder aux informations de diagnostic par l’intermédiaire de FTP, visitez le [Portail Azure](https://portal.azure.com)votre application web et sélectionnez ensuite le **tableau de bord**. Dans la section **liens rapides** , les liens de **Journaux de DIAGNOSTIC FTP** et **FTPS** de DIAGNOSTIC fournissent l’accès aux journaux en utilisant le protocole FTP.

> [AZURE.NOTE] Si vous n’avez pas précédemment configuré nom d’utilisateur et le mot de passe FTP ou le déploiement, vous pouvez le faire à partir de la page de gestion des **Démarrages rapides** en sélectionnant **un jeu d’informations d’identification de déploiement**.

L’URL FTP retournés dans le tableau de bord est pour le répertoire de **fichiers journaux** , qui contient les sous-répertoires suivants :

* [Méthode de déploiement](web-sites-deploy.md) - si vous utilisez une méthode de déploiement par exemple Git, un répertoire du même nom sera créé et contiendra des informations relatives aux déploiements.

* nodejs - Stdout et stderr informations capturées à partir de toutes les instances de votre application (lorsque loggingEnabled a la valeur true.)

###<a name="zip-archive"></a>Archive ZIP

Pour télécharger une archive Zip des journaux de diagnostics, utilisez la commande suivante à partir des outils de ligne de commande Azure :

    azure site log download [sitename]

Un **diagnostics.zip** dans le répertoire en cours est alors téléchargée. Cette archive contient la structure de répertoire suivante :

* déploiements - un journal des informations sur le déploiement de votre application

* Fichiers journaux

    * [Méthode de déploiement](web-sites-deploy.md) - si vous utilisez une méthode de déploiement par exemple Git, un répertoire du même nom sera créé et contiendra des informations relatives aux déploiements.

    * nodejs - Stdout et stderr informations capturées à partir de toutes les instances de votre application (lorsque loggingEnabled a la valeur true.)

###<a name="live-stream-tail"></a>Flux en direct (fin)

Pour afficher un flux en direct des informations du journal de diagnostic, utilisez la commande suivante à partir des outils de ligne de commande Azure :

    azure site log tail [sitename]

Cette opération retourne un flux d’événements, qui sont mis à jour pour qu’ils se produisent sur le serveur. Ce flux de données retourne les informations de déploiement ainsi que des informations de stdout et stderr (lorsque loggingEnabled a la valeur true.)

<a id="nextsteps"></a>
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment activer et afficher les informations de diagnostic pour Azure. Cette information est utile dans les problèmes de compréhension qui se produisent avec votre application, il peut pointer vers un problème avec un module que vous utilisez ou que la version de Node.js utilisé par l’application de Service Web Apps est différente de celui utilisé dans votre environnement de déploiement.

Pour plus d’informations dans l’utilisation des modules sur Azure, consultez [à l’aide des Modules de Node.js avec Applications Azure](../nodejs-use-node-modules-azure-apps.md).

Pour plus d’informations sur la spécification d’une version Node.js pour votre application, consultez [spécification d’une version Node.js dans une application Azure].

Pour plus d’informations, consultez également le [Centre pour développeurs Node.js](/develop/nodejs/).

## <a name="whats-changed"></a>Ce qui a changé
* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

[IISNode]: https://github.com/tjanczuk/iisnode
[Fichier Lisezmoi de IISNode]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]: ../xplat-cli-install.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Spécification d’une version de Node.js dans une application Azure]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
 
