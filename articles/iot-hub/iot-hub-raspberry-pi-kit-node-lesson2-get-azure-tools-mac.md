<properties
 pageTitle="Obtenir des outils Azure (macOS 10.10) | Microsoft Azure"
 description="Installez les Python et Azure Interface de ligne de commande (CLI Azure) sur macOS."
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

# <a name="21-get-azure-tools-macos-1010"></a>2.1 obtenir des outils Azure (macOS 10.10)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 ce que vous ferez

Installer l’Azure l’Interface de ligne de commande (CLI Azure). Si vous répondez à tous les problèmes, rechercher des solutions dans la [page Dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="212-what-you-will-learn"></a>2.1.2 vous apprendrez

- Comment installer l’interface CLI d’Azure.
- Comment ajouter le sous-groupe IoT d’Azure CLI.

## <a name="213-what-you-need"></a>2.1.3 ce dont vous avez besoin

- Un Mac avec connexion Internet
- Un abonnement Azure. Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

## <a name="214-install-python"></a>2.1.4 installer les Python

Bien que macOS accompagne les Python 2.7 prêts à l’emploi, il est recommandé d’installer les Python via Homebrew. Reportez-vous à la section [Installation les Python sur macOS](http://docs.python-guide.org/en/latest/starting/install/osx/).

Installer les Python et pip en exécutant la commande suivante :

```bash
brew install python
```

## <a name="215-install-the-azure-cli"></a>2.1.5 installer l’interface CLI Azure

La CLI Azure fournit une expérience de ligne de commande multiplateforme pour Azure, qui vous permet de travailler directement à partir de la ligne de commande pour provisionner et gérer les ressources. 

Pour installer la dernière CLI d’Azure, procédez comme suit :

1. Exécutez les commandes suivantes dans une fenêtre de Terminal. Il peut prendre cinq minutes pour installer l’interface CLI d’Azure.

    ```bash
    pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```

2. Vérifier l’installation en exécutant la commande suivante :

    ```bash
    az iot -h
    ```
  
Vous devez voir la sortie suivante si l’installation est terminée.

![AZ iot -h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_osx.png)

## <a name="215-summary"></a>2.1.5 résumé

Vous avez installé le CLI d’Azure. Passez à la section suivante pour créer votre identité Azure IoT concentrateur et le périphérique à l’aide de la CLI d’Azure.

## <a name="next-steps"></a>Étapes suivantes

[2.2 créer votre concentrateur IoT et enregistrer votre framboises Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
