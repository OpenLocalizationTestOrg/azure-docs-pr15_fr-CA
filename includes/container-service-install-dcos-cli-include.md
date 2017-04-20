<properties
   pageTitle="Installer le contrôleur de domaine/système d’exploitation CLI | Microsoft Azure"
   description="Installez le contrôleur de domaine/système d’exploitation CLI."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Conteneurs, Micro-services, le contrôleur de domaine/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Il s’agit pour l’utilisation avec des clusters de basé sur le système d’exploitation DC l’ACS. Il n’est pas nécessaire de le faire pour les clusters basés sur l’essaim l’ACS.

Tout d’abord, [vous connecter à votre cluster basé sur le système d’exploitation DC l’ACS](../articles/container-service/container-service-connect.md). Une fois que vous l’avez fait, vous pouvez installer l’interface CLI de contrôleur de domaine/système d’exploitation sur votre ordinateur client, les commandes ci-dessous :

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Si vous utilisez une ancienne version de Python, vous pouvez remarquer des « InsecurePlatformWarnings ». Vous pouvez ignorer ces.

Pour mettre en route sans redémarrer votre shell, exécutez :

```bash
source ~/.bashrc
```

Cette étape ne sera pas nécessaire lorsque vous démarrez les nouvelles coques.

Maintenant, vous pouvez vérifier que l’infrastructure CLI est installé :

```bash
dcos --help
```