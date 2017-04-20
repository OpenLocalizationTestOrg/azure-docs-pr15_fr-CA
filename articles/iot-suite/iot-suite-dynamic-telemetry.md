<properties
    pageTitle="Utiliser la télémétrie dynamique | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à utiliser de télémétrie dynamique avec la solution préconfigurée d’analyse à distance."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="dobett"/>

# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Utilisation de télémétrie dynamique avec l’analyse à distance de solution préconfigurée

## <a name="introduction"></a>Introduction

Télémétrie dynamique vous permet de visualiser toute télémétrie envoyé à la solution de surveillance préconfigurée à distance. Les simulations de périphériques qui déploient la solution préconfigurée envoient de télémétrie de température et d’humidité, qui vous pouvez d’afficher sur le tableau de bord. Si vous personnalisez les simulations de périphériques existants, créez de nouveaux périphériques simulés ou périphériques physiques de connectez à la solution préconfigurée vous pouvez envoyer d’autres valeurs de télémétrie tels que la température externe, tr/min ou vitesse du vent. Vous pouvez ensuite visualiser cette télémétrie supplémentaire sur le tableau de bord.

Ce didacticiel utilise un périphérique simulé Node.js simple que vous pouvez facilement modifier pour expérimenter la télémétrie dynamique.

Pour terminer ce didacticiel, vous avez besoin :

- Un abonnement Azure. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure][lnk_free_trial].
- [Node.js] [ lnk-node] version 0.12.x ou une version ultérieure.

Vous pouvez effectuer ce didacticiel sur n’importe quel système d’exploitation, comme Windows ou Linux, dans lequel vous pouvez installer Node.js.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="configure-the-nodejs-simulated-device"></a>Configurer le périphérique simulé Node.js

1. Sur la console de surveillance à distance, cliquez sur **+ Ajouter un périphérique** et ajouter un périphérique personnalisé. Prenez note du concentrateur IoT hostname, id de périphérique et clé de périphérique. Vous en avez besoin plus loin dans ce didacticiel lors de la préparation de l’application de client de périphérique remote_monitoring.js.

2. Assurez-vous que la version Node.js 0.12.x ou version ultérieure est installé sur votre ordinateur de développement. Exécution de `node --version` à l’invite de commande ou dans un shell pour vérifier la version. Pour plus d’informations sur l’utilisation d’un gestionnaire de package pour installer Node.js sous Linux, voir [Installation de Node.js via le Gestionnaire de package][node-linux].

3. Lorsque vous avez installé Node.js, cloner la dernière version de l' [azure-iot-SDK] [ lnk-github-repo] référentiel à votre ordinateur de développement. Toujours utiliser la branche **principale** pour la version la plus récente des bibliothèques et des exemples.

4. À partir de votre copie locale de la [azure-iot-SDK] [ lnk-github-repo] du référentiel, copie les deux fichiers à partir du dossier du nœud / / exemples de périphériques dans un dossier vide sur votre ordinateur de développement :

  - packages.JSON
  - remote_monitoring.js

5. Ouvrez le fichier remote_monitoring.js et recherchez la définition de la variable suivante :

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. Remplacez **[chaîne de connexion d’un périphérique de concentrateur de IoT]** par votre chaîne de connexion de périphérique. Utilisez les valeurs pour votre IoT concentrateur hostname, id de périphérique et clé de périphérique vous notés à l’étape 1. Une chaîne de connexion du périphérique a le format suivant :

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Si votre nom d’hôte IoT concentrateur est **contoso** et que votre id de périphérique est **mydevice**, votre chaîne de connexion ressemble à ceci :

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. Enregistrez le fichier. Exécutez les commandes suivantes dans une coque ou d’une invite de commandes dans le dossier qui contient ces fichiers pour installer les packages nécessaires et exécutez l’exemple d’application :

    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Observez la télémétrie dynamique en action

Le tableau de bord affiche la télémétrie de température et d’humidité dans les simulations de périphériques existants :

![Le tableau de bord par défaut][image1]

Si vous sélectionnez le périphérique simulé Node.js que vous avez exécuté dans la section précédente, vous voyez de température, d’humidité et de télémétrie de température externe :

![Ajouter des températures externes au tableau de bord][image2]

La solution de surveillance à distance détecte le type de télémétrie supplémentaires de température externe automatiquement et l’ajoute au graphique dans le tableau de bord.

## <a name="add-a-telemetry-type"></a>Ajouter un type de télémétrie

L’étape suivante consiste à remplacer la télémétrie généré par périphérique simulé Node.js avec un nouveau jeu de valeurs :

1. Arrêter le périphérique simulé Node.js en tapant **Ctrl + C** dans votre invite de commande ou du shell.

2. Dans le fichier remote_monitoring.js, vous pouvez voir les valeurs de données de base pour l’existant de température, d’humidité et télémétrie de température externe. Ajoutez une valeur de données de base de **t/min** comme suit :

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Le périphérique simulé Node.js utilise la fonction **generateRandomIncrement** dans le fichier remote_monitoring.js pour ajouter un incrément aléatoire pour les valeurs de base de données. Randomiser la valeur **tr/min** en ajoutant une ligne de code après les randomizations existantes, comme suit :

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Ajouter la nouvelle valeur de t/min pour la charge utile JSON que le périphérique envoie à IoT concentrateur :

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Exécutez le périphérique simulé Node.js à l’aide de la commande suivante :

    ```
    node remote_monitoring.js
    ```

6. Observez le nouveau type de télémétrie RPM qui s’affiche sur le graphique dans le tableau de bord :

![Ajouter t/min au tableau de bord][image3]

> [AZURE.NOTE] Il se pouvez que vous deviez désactivez, puis activez le périphérique Node.js sur la page **de périphériques** dans le panneau de commandes pour voir les modifications immédiatement.

## <a name="customize-the-dashboard-display"></a>Personnaliser l’affichage du tableau de bord

Le message **d’Informations du périphérique** peut inclure des métadonnées sur la télémétrie, que le périphérique peut envoyer à IoT concentrateur. Ces métadonnées peuvent spécifier les types de télémétrie que le périphérique envoie. Modifiez la valeur de **deviceMetaData** dans le fichier remote_monitoring.js d’inclure une définition de **télémétrie** la définition de **commandes** . L’extrait de code suivant illustre la définition de **commandes** (n’oubliez pas d’ajouter un `,` après la définition de **commandes** ) :

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] La solution de surveillance à distance utilise une casse à comparer la définition des métadonnées avec des données dans le flux de télémétrie.

Ajout d’une définition de **télémétrie** comme indiqué dans l’extrait de code précédent ne modifie pas le comportement du tableau de bord. Toutefois, les métadonnées peuvent également inclure un attribut **DisplayName** pour personnaliser l’affichage du tableau de bord. Mettre à jour la définition de métadonnées de **télémétrie** comme indiqué dans l’extrait suivant :

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

La capture d’écran suivante montre comment ce changement modifie la légende du graphique du tableau de bord :

![Personnaliser la légende du graphique][image4]

> [AZURE.NOTE] Il se pouvez que vous deviez désactivez, puis activez le périphérique Node.js sur la page **de périphériques** dans le panneau de commandes pour voir les modifications immédiatement.

## <a name="filter-the-telemetry-types"></a>Filtrer les types de télémétrie

Par défaut, le graphique du tableau de bord affiche chaque série de données dans le flux de télémétrie. Vous pouvez utiliser les métadonnées de **Périphérique-Info** pour supprimer l’affichage de types de télémétrie spécifique sur le graphique. 

Pour rendre le graphique à afficher uniquement les télémétrie de température et d’humidité, omettez **ExternalTemperature** à partir des métadonnées de **télémétrie** **d’Informations du périphérique** comme suit :

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

La **Température extérieure** n’affiche plus sur le graphique :

![Filtrer la télémétrie sur le tableau de bord][image5]

Cette modification n’affecte que l’affichage du graphique. Les valeurs de données **ExternalTemperature** sont toujours stockées et accessibles pour tout traitement back-end.

> [AZURE.NOTE] Il se pouvez que vous deviez désactivez, puis activez le périphérique Node.js sur la page **de périphériques** dans le panneau de commandes pour voir les modifications immédiatement.

## <a name="handle-errors"></a>Gérer les erreurs

Pour un flux de données à afficher sur le graphique, son **Type** dans les métadonnées de **Périphérique-Info** doit correspondre le type de données des valeurs de télémétrie. Par exemple, si les métadonnées spécifie que le **Type** de données d’humidité est de **type int** et **double** est trouvé dans le flux de télémétrie puis la télémétrie d’humidité n’affiche pas sur le graphique. Toutefois, les valeurs **d’humidité** sont toujours stockés et disponibles pour tout traitement back-end.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez vu comment utiliser télémétrie dynamique, vous pouvez en savoir plus sur l’utilisation des informations relatives au périphérique par les solutions préconfigurées : [solution préconfigurée de surveillance de métadonnées d’informations de périphérique dans la base distante][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks
