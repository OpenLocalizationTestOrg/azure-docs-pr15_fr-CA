<properties
   pageTitle="Automatisation du déploiement d’applications avec les Extensions de la Machine virtuelle | Microsoft Azure"
   description="Didacticiel de DotNet Core Azure VM"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Déploiement d’applications avec modèles du Gestionnaire de ressources Azure

Une fois que toutes les exigences d’infrastructures Azure ont été identifiés et convertis en un modèle de déploiement, le déploiement d’une application réelle doit être résolu. Déploiement d’application fait référence à réinstaller les binaires de l’application réelle sur les ressources d’Azure. Pour l’exemple de magasin de musique, .net Core, NGINX et contrôleur doivent être installés et configurés sur chaque machine virtuelle. Les fichiers binaires du magasin de musique doivent être installés sur l’ordinateur virtuel et la base de données de magasin de musique créés à l’avance.

Ce document décrit en détail comment les extensions de la Machine virtuelle peuvent automatiser le déploiement d’applications et la configuration des machines virtuelles Azure. Toutes les dépendances et les configurations uniques sont mis en surbrillance. Pour optimiser les performances, avant de déployer une instance de la solution à votre abonnement Azure et le travail avec le modèle de gestionnaire de ressources Azure. Le modèle terminé se trouve ici – [Déploiement du magasin de musique sur Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="configuration-script"></a>Script de configuration

Extensions de la Machine virtuelle sont des programmes spécialisés qui s’exécutent sur des ordinateurs virtuels pour fournir l’automatisation de la configuration. Les extensions sont disponibles dans des buts spécifiques tels que les antivirus, la configuration de la journalisation et configuration de Docker. Une extension de script personnalisé peut être utilisée pour exécuter un script sur une machine virtuelle. Dans l’exemple de magasin de musique, il revient à l’extension de script personnalisé pour configurer les ordinateurs virtuels de Ubuntu et installer l’application magasin de musique.

Avant d’en détaillant comment les extensions de la machine virtuelle sont déclarées dans un modèle de gestionnaire de ressources Azure, examinez le script qui est exécuté. Ce script configure l’ordinateur virtuel Ubuntu pour héberger l’application magasin de musique. Lors de l’exécution, le script installe tous les nécessaires logiciel, installer l’application magasin de musique à partir du contrôle de code source et de préparer la base de données. 

Pour plus d’informations sur l’hébergement .net application sous Linux de base, reportez-vous à la section [publier sur un environnement de production Linux](https://docs.asp.net/en/latest/publishing/linuxproduction.html). 

> Cet exemple est destiné à des fins de démonstration.

```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>Extension de Script de machine virtuelle

Extensions de la machine virtuelle peut être exécutées sur une machine virtuelle au moment de la génération en incluant la ressource d’extension dans le modèle de gestionnaire de ressources Azure. L’extension peut être ajoutée avec l’Assistant Visual Studio ajouter une ressource, ou en insérant un JSON valide dans le modèle. La ressource de Script Extension est imbriquée à l’intérieur de la ressource d’ordinateur virtuel ; Ceci peut être observé dans l’exemple suivant.

Suivez ce lien pour voir l’exemple JSON dans le modèle de gestionnaire de ressources – [Extension de Script de machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359). 

Notez que, dans le sous JSON que le script est stocké dans GitHub. Ce script peut également être stocké dans le stockage des objets Blob Azure. En outre, les modèles de Azure le Gestionnaire de ressources permettent de la chaîne d’exécution du script construites telles que les valeurs des paramètres de modèle peuvent être utilisés comme paramètres pour l’exécution du script. Dans ce cas les données sont fournies lorsque vous déployez les modèles, et ces valeurs peuvent ensuite être utilisées lors de l’exécution du script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Pour plus d’informations sur l’utilisation de l’extension de script personnalisé, reportez-vous à la section [extensions de script personnalisé avec les modèles du Gestionnaire de ressources](./virtual-machines-linux-extensions-customscript.md).

## <a name="next-step"></a>Étape suivante

<hr>

[Explorez plus Azure modèles du Gestionnaire de ressources](https://github.com/Azure/azure-quickstart-templates)
