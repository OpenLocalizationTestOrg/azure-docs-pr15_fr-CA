<properties
 pageTitle="Créer une application de fonction Azure et le compte de stockage Azure | Microsoft Azure"
 description="L’application de la fonction Azure écoute des événements de concentrateur Azure IoT, traite les messages entrants et les écrit dans le stockage par table Azure."
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

# <a name="31-create-an-azure-function-app-and-azure-storage-account"></a>3.1 créer une application de fonction Azure et d’un compte de stockage Azure

[Fonctions d’Azure](../../articles/azure-functions/functions-overview.md) est une solution pour exécuter facilement les petits morceaux de code, appelée « fonctions » dans le nuage. Une application Azure fonction héberge l’exécution de vos fonctions dans Azure.

## <a name="311-what-will-you-do"></a>3.1.1. que souhaitez-vous faire

Utiliser un modèle de gestionnaire de ressources Azure pour créer une application de fonction Azure et un compte de stockage Azure. L’application de la fonction Azure écoute des événements de concentrateur Azure IoT, traite les messages entrants et les écrit dans le stockage par table Azure. Si vous répondez à tous les problèmes, rechercher des solutions dans la [page Dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="312-what-will-you-learn"></a>3.1.2 vous apprendrez

- Comment utiliser [Le Gestionnaire de ressources Azure](../../articles/azure-resource-manager/resource-group-overview.md) pour déployer des ressources Azure.
- Comment faire pour utiliser une application Azure fonction IoT concentrateur de traiter les messages et les écrire dans une table dans le stockage par table Azure.

## <a name="313-what-do-you-need"></a>3.1.3 que vous faut-il

- Vous devez avoir terminé avec succès les leçons précédentes : [mise en route de votre framboises Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md) et [créer votre concentrateur IoT d’Azure](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="314-open-the-sample-app"></a>3.1.4 ouvrir l’exemple d’application

Ouvrez l’exemple de projet de Code de Visual Studio en exécutant les commandes suivantes :

```bash
cd Lesson3
code .
```

![Structure de mis en pension](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure.png)

- Le `app.js` de fichiers dans le `app` sous-dossier est le fichier source de clé. Ce fichier source contient le code pour envoyer un message à 20 fois à votre concentrateur IoT et clignoter le voyant de chaque message qu’il envoie.
- Le `arm-template.json` fichier est le modèle de gestionnaire de ressources Azure qui contient une application Azure fonction et un compte de stockage Azure.
- Le `arm-template-param.json` fichier est le fichier de configuration utilisé par le modèle du Gestionnaire de ressources Azure.
- Le `ReceiveDeviceMessages` sous-dossier contient le code Node.js pour la fonction Azure.

## <a name="315-configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>3.1.5 configurer les modèles Azure Gestionnaire de ressources et de créer des ressources dans Azure

Mise à jour de la `arm-template-param.json` fichier de Code de Visual Studio.

![Paramètres de modèle de gestionnaire de ressources Azure](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para.png)

- Remplacez **[votre nom de concentrateur de IoT]** **{Mon nom hub}** que vous avez spécifié dans la [leçon 2](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).
- Remplacez **[chaîne de préfixe pour les nouvelles ressources]** par n’importe quel préfixe que vous souhaitez. Le préfixe garantit que le nom de ressource est globalement unique afin d’éviter le conflit. N’utilisez pas de tiret ou numéro initial dans le préfixe.

> [AZURE.NOTE] Vous n’avez pas besoin `azure_storage_connection_string` de cette section. Conserver tel quel.

Une fois que vous mettez à jour la `arm-template-param.json` de fichiers, de déployer les ressources sur Azure en exécutant la commande suivante :

```bash
az resource group deployment create --template-file-path arm-template.json --parameters-file-path arm-template-param.json -g iot-sample -n mydeployment
```

Elle dure environ cinq minutes pour créer ces ressources. Lors de la création de la ressource est en cours, vous pouvez passer à la section suivante.

## <a name="316-summary"></a>3.1.6 résumé

Vous avez créé votre application Azure fonction pour traiter les messages de concentrateur IoT et un compte de stockage Azure pour stocker ces messages. Vous pouvez vous déplacer à la section suivante pour déployer et exécuter l’exemple pour envoyer des messages de périphérique-nuage sur votre Pi.

## <a name="next-steps"></a>Étapes suivantes

[3.2 exécuter l’exemple d’application pour envoyer des messages de périphérique-nuage sur votre framboises Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

