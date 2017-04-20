<properties
 pageTitle="Obtenez les outils (macOS 10.10) | Microsoft Azure"
 description="Téléchargez et installez les outils nécessaires et le logiciel pour le premier exemple d’application pour votre Pi sur macOS."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="12-get-the-tools-macos-1010"></a>1.2 obtenir les outils (macOS 10.10)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
- [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="121-what-you-will-do"></a>1.2.1 ce que vous ferez

Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour votre framboises Pi 3. Si vous répondez à tous les problèmes, rechercher des solutions dans la [page Dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="122-what-you-will-learn"></a>1.2.2 vous apprendrez
Dans cette section, vous apprendrez à :

- Comment faire pour installer le Git et Node.js
    - [GIT](https://git-scm.com) est un système de contrôle de version open source distribué. L’exemple d’application de cette leçon est stocké dans le Git.
    - [Node.js](https://nodejs.org/en/) est un runtime de JavaScript avec un écosystème package complet.
- Comment utiliser NPM pour installer les outils de développement supplémentaires Node.js.
  - La version minimale requise de Node.js est 4.5 LTS.
  - [NPM](https://www.npmjs.com) est un des gestionnaires de package pour Node.js.

## <a name="123-what-you-need"></a>1.2.3 ce dont vous avez besoin

- Une connexion Internet pour télécharger les outils de développement et le logiciel
- Un Mac fonctionnant sous macOS Yosemite (10.10) ou version ultérieure

## <a name="124-install-git-and-nodejs"></a>1.2.4 installer Git et Node.js

Pour installer le Git et Node.js, utilisez l’utilitaire de gestion de package [Homebrew](http://brew.sh) en suivant ces étapes :

1. Installation de Homebrew. Si vous avez déjà installé Homebrew, passez à l’étape 2.
  1. Appuyez sur la touche `Cmd + Space` et `Terminal` pour ouvrir une fenêtre de terminal.
  2. Exécutez la commande suivante :

    ```bash
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
2. Installer le Git et Node.js en exécutant la commande suivante :

    ```bash
    brew install node git
    ```

## <a name="125-install-additional-nodejs-development-tools"></a>1.2.5 installer d’autres outils de développement de Node.js

[Gulp.js](http://gulpjs.com) vous permet d’automatiser le déploiement de l’exemple d’application à votre Pi. Le [périphérique-découverte-cli](https://github.com/Azure/device-discovery-cli) permet également de récupérer des informations du réseau sur vos périphériques IoT.

Installation de `gulp` et `device-discovery-cli` en exécutant la commande suivante dans la fenêtre de Terminal :

```bash
sudo npm install -g device-discovery-cli gulp
```

Si vous rencontrez des problèmes d’installation Node.js et ces outils de développement supplémentaires sur macOS, consultez le [guide de dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md) des solutions aux problèmes courants.

## <a name="126-install-visual-studio-code"></a>1.2.6 installer du Code de Visual Studio

[Téléchargement](https://code.visualstudio.com/docs/setup/osx) et installation de Code de Visual Studio. Le Code de Visual Studio est un éditeur de code source léger mais puissant pour macOS, Linux et Windows. Vous utilisez cet éditeur pour modifier le code de l’exemple plus loin dans le didacticiel.

## <a name="127-summary"></a>1.2.7). résumé de

Vous avez installé les outils de développement requis et le logiciel pour le premier exemple d’application. Dans la section suivante, créer, déployer et exécuter l’exemple d’application sur votre Pi.

## <a name="next-steps"></a>Étapes suivantes

[1.3 créer et déployer l’exemple d’application de clignotement](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)
