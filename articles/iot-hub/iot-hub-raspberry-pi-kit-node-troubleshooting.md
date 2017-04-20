<properties
 pageTitle="Dépannage | Microsoft Azure"
 description="Page de dépannage pour les framboises Pi Node.js expérience"
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

# <a name="troubleshooting"></a>Résolution des problèmes

## <a name="hardware-issues"></a>Problèmes liés au matériel

### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>L’application s’exécute correctement, mais le voyant ne clignote pas

Ce problème est toujours lié à la connectivité de circuit de matériel. Utilisez les étapes suivantes pour identifier les problèmes.

1. Vérifiez si vous choisissez la bonne **GPIO** sur votre carte mère. Les deux ports dans cette leçon doivent être **GPIO GND (6 broches)** et **GPIO 04 (broche 7)**.
2. Vérifiez si la polarité de votre voyant est correcte. La jambe plue doit indiquer le **positif**, broche d’anode.
3. Utilisez la **broche de 3,3 v** et un **code confidentiel de terre** sur votre Raspberry Pi 3. Traiter votre Pi comme l’alimentation. Vérifiez si le voyant fonctionne correctement.

![Spécifications du voyant](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>Autres problèmes matériels

Pour plus d’informations sur la résolution des problèmes courants de la Pi 3 de framboises, reportez-vous à la [page de dépannage officiel](http://elinux.org/R-Pi_Troubleshooting).

## <a name="nodejs-package-issues"></a>Problèmes de package Node.js

### <a name="no-response-during-gulp-tasks"></a>Aucune réponse au cours des tâches de choses

Si vous rencontrez des problèmes pour exécuter des tâches de choses, vous pouvez ajouter la `--verbose` option pour le débogage. Tenter d’arrêter les tâches en cours de choses avec `Ctrl + C` puis exécutez la commande suivante dans la fenêtre de console pour afficher les messages de débogage. Vous pouvez voir les messages d’erreur détaillés imprimés dans la sortie de la console. 

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problèmes de découverte de périphériques

De l’aide pour résoudre les problèmes courants liés à la `devdisco` de commande, consultez le [fichier readme](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### <a name="other-npm-issues"></a>Autres problèmes NPM

Essayez de mettre à jour votre package NPM avec la commande suivante :

```bash
npm install -g npm
```

Si le problème persiste, laissez vos commentaires à la fin de cet article ou créer un problème de Github dans notre [Exemple de référentiel](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started)

## <a name="remote-debugging"></a>Le débogage distant

### <a name="run-the-sample-application-in-debug-mode"></a>Exécutez l’exemple d’application en mode débogage

```bash
gulp run --debug
```

Lorsque le moteur de débogage est prêt, vous devez voir ```Debugger listening on port 5858``` dans la sortie de la console.

### <a name="configure-vs-code-to-connect-to-the-remote-device"></a>Configurer Code VS pour se connecter au périphérique distant

Ouvrez le panneau **débogage** sur le côté gauche.

Cliquez sur le bouton vert **Démarrer le débogage** (F5). Code de VS ouvre un fichier de **launch.json** , vous devez mettre à jour.

Mettre à jour le fichier **launch.json** avec le contenu suivant. Remplacer `[device hostname or IP address]` avec l’adresse IP du périphérique réel ou nom d’hôte.   

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![Configuration du débogage distant](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Attacher à l’application distante

Cliquez sur le bouton vert **Démarrer le débogage** (F5) pour démarrer le débogage. 

Lire le [JavaScript dans le Code de VS](https://code.visualstudio.com/docs/languages/javascript#_debugging) pour plus d’informations sur le débogueur.

![Débogage interactif à distance](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Problèmes CLI Azure

CLI Azure est une génération de l’aperçu. Vous pouvez faire référence au [Guide d’installation aperçu](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md) de rechercher des solutions.

Si vous rencontrez des bogues à l’aide de l’outil, un [problème](https://github.com/Azure/azure-cli/issues) de fichier dans la section **problèmes** de la mis en pension GitHub.

Pour obtenir la résolution de problèmes courants, vérifiez le [fichier Lisezmoi](https://github.com/Azure/azure-cli/blob/master/README.rst).

## <a name="python-installation-issues"></a>Problèmes d’installation de Python

### <a name="legacy-installation-issues-macos"></a>Problèmes d’installation héritée (macOS)

Lorsque vous installez le **pip**, une erreur d’autorisation est levée lorsqu’il y a des packages existants qui sont installés avec les autorisations de **su** . Cette situation se produit car l’installation précédente de Python à l’aide d’infusion (macOS) n’est pas complètement désinstallée. Certains packages **pip** à partir d’une installation précédente ont été créés à la racine, ce qui provoque l’erreur d’autorisation. La solution consiste à supprimer les packages installés par la racine. Pour effectuer cette tâche, utilisez les étapes suivantes :

1. Accédez à /usr/local/lib/python2.7/site-packages
2. Créer des packages de la liste par racine :`ls -l | grep root`
3. Désinstallation des packages à partir de l’étape 2 :`sudo rm -rf {package name}`
4. Réinstallez les Python.

## <a name="azure-iot-hub-issues"></a>Problèmes de concentrateur IoT Azure

Si vous avez déployé concentrateur Azure IoT avec `azure-cli`, et vous avez besoin d’un outil permettant de gérer les périphériques qui se connectent à votre concentrateur IoT, essayez les outils suivants :

### <a name="device-explorer"></a>Explorateur de périphérique

[Explorateur de périphérique](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) s’exécute sur votre ordinateur local et se connecte à votre concentrateur IoT dans Azure. Il communique avec [IoT concentrateur de points de terminaison](iot-hub-devguide.md)suivants :

- *Gestion des identités* pour provisionner et gérer les périphériques enregistré avec votre concentrateur IoT.
- *Périphérique-nuage réception* vous permettent de contrôler les messages envoyés à partir de votre appareil à votre concentrateur IoT.
- *Envoyer le nuage-DISPOSITIF* pour vous permettre d’envoyer des messages à vos périphériques à partir de votre concentrateur IoT.

Configurer votre `IoT hub connection string` au sein de cet outil vous permet d’utiliser toutes ses fonctionnalités.

### <a name="iot-hub-explorer"></a>Concentrateur IoT Explorer

[IoT concentrateur Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/readme.md) est un outil CLI de multi-plateforme exemple pour gérer des périphériques clients. L’outil vous permet de gérer les périphériques dans le Registre de l’identité, de contrôler les messages de périphérique-nuage et envoyer des commandes de nuage vers le périphérique.

Pour installer la dernière version (version préliminaire) de l’outil explorer-iothub, exécutez la commande suivante dans votre environnement de ligne de commande :

```
npm install -g iothub-explorer@latest
```

Vous pouvez utiliser la commande suivante pour obtenir une aide supplémentaire sur toutes les commandes de l’Explorateur de l’iothub et de leurs paramètres :

```bash
iothub-explorer help
```

### <a name="use-azure-portal-to-manage-your-resources"></a>Azure portal permet de gérer vos ressources

Dans ces leçons, une expérience complète de CLI est fournie pour créer et gérer toutes les ressources d’Azure. Vous pourriez également utiliser le [portail Azure](../azure-portal-overview.md) à disposition, gérer et déboguer vos ressources Azure.

## <a name="azure-storage-issues"></a>Problèmes de stockage Azure

[Explorateur de stockage Microsoft Azure (aperçu)](http://storageexplorer.com) est une application autonome de Microsoft qui vous permet de travailler avec des données de stockage Azure sur Windows, macOS et Linux. Cet outil vous permet de vous connecter à votre table et voir les données qu’il contient. Vous pouvez utiliser cet outil pour résoudre vos problèmes de stockage Azure.
