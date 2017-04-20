<properties
    pageTitle="Utilisez l’Extension CustomScript sur un ordinateur virtuel de Linux | Microsoft Azure"
    description="Apprenez à utiliser l’extension CustomScript pour déployer des applications Linux machines virtuelles en fonctionnement dans Azure créé en utilisant le modèle de déploiement classique."
    editor="tysonn"
    manager="timlt"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="gbowerman"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="multiple"
    ms.tgt_pltfrm="linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

#<a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Déployer une application de feu à l’aide de l’Extension de CustomScript Azure pour Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


L’Extension de CustomScript Microsoft Azure pour Linux permet de personnaliser vos machines virtuelles (VM) par l’exécution de code arbitraire écrit dans tout langage de script pris en charge par la machine virtuelle (par exemple, Python et Bash). Cela offre un moyen très souple pour automatiser le déploiement des applications sur plusieurs ordinateurs.

Vous pouvez déployer le CustomScript Extension à l’aide de l’Interface de ligne de commande (CLI Azure) Azure, Windows PowerShell ou l’Azure portal classique.

Dans cet article, nous allons utiliser la CLI Azure pour déployer une application simple de lampe à une VM Ubuntu créés à l’aide du modèle de déploiement classique.

## <a name="prerequisites"></a>Conditions préalables

Pour cet exemple, commencez par créer deux Azure VM exécutant Ubuntu 14.04 ou une version ultérieure. Les ordinateurs virtuels sont appelés *script-vm* et *lampe-vm*. Utilisez des noms uniques lorsque vous créez des ordinateurs virtuels. Une est utilisée pour exécuter les commandes CLI, et une est utilisée pour déployer l’application de la lampe.

Vous devez également un compte de stockage Azure et d’une clé d’accès (vous pouvez obtenir cette information à partir du portail classique Azure).

Si vous avez besoin d’aide pour créer des ordinateurs virtuels de Linux sur Azure consultez [créer un Linux exécutant d’ordinateur virtuel](virtual-machines-linux-classic-createportal.md).

Les commandes d’installation supposent Ubuntu, mais vous pouvez adapter l’installation pour n’importe quel distro Linux pris en charge.

La machine virtuelle de script-vm doit CLI Azure installé, avec une connexion active à Azure. Pour l’aide à ce sujet, reportez-vous à [installer et à configurer l’Interface de ligne de commande d’Azure](../xplat-cli-install.md).

## <a name="upload-a-script"></a>Télécharger un script

Nous allons utiliser le CustomScript Extension pour exécuter un script sur un ordinateur distant virtuel pour installer la pile de lampe et de créer une page PHP. Pour accéder au script de n’importe où nous allons le télécharger sous la forme d’un blob Azure.

### <a name="script-overview"></a>Vue d’ensemble du script

L’exemple de script installe une pile de lampe à Ubuntu (y compris la configuration d’une installation silencieuse de MySQL) écrit un simple fichier PHP et Apache de démarre.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Télécharger le script

Enregistrez le script sous la forme d’un fichier texte, par exemple, *install_lamp.sh*et ensuite de le télécharger vers le stockage Azure. Vous pouvez le faire facilement avec l’interface CLI d’Azure. L’exemple suivant télécharge le fichier à un conteneur de stockage nommé « scripts ». Si le conteneur n’existe pas, vous devrez d’abord la créer.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Également créer un fichier JSON qui décrit la procédure de téléchargement du script de stockage Azure. Enregistrer en tant que *public_config.json* (en remplaçant « mystorage » par le nom de votre compte de stockage) :

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Déploiement de l’extension

Maintenant, vous pouvez utiliser la commande suivante pour déployer le CustomScript Extension de Linux pour la VM à distance à l’aide de la CLI d’Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

La commande précédente, télécharge et exécute le script de *install_lamp.sh* sur la machine virtuelle appelée *lampe-vm*.

Dans la mesure où l’application comprend un serveur web, n’oubliez pas d’ouvrir un port d’écoute HTTP sur l’ordinateur virtuel à distance avec la commande suivante.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Surveillance et dépannage

Vous pouvez vérifier comment le script personnalisé s’exécute en consultant le fichier journal sur l’ordinateur virtuel distant. SSH pour *lampe-vm* et de la fin du fichier journal avec la commande suivante.

    cd /var/log/azure/customscript
    tail -f handler.log

Après l’exécution de le CustomScript Extension, vous pouvez accéder à la page PHP que vous avez créé pour plus d’informations. La page PHP dans l’exemple de cet article est *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Ressources supplémentaires

Vous pouvez utiliser les mêmes étapes pour déployer des applications plus complexes. Dans cet exemple, le script d’installation a été enregistré sous la forme d’un blob publique dans le stockage Azure. Pour stocker le script d’installation sous la forme d’un blob sécurisé avec une [Signature d’accès sécurisée](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS) est une option plus sûre.

Ressources supplémentaires pour la CLI d’Azure, de Linux et de le CustomScript Extension sont décrits ci-dessous.

[Automatiser les tâches de personnalisation de Virtual Media Linux à l’aide d’Extension de CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensions d’Azure Linux (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux et Open-Source informatique sur Azure](virtual-machines-linux-opensource-links.md)
