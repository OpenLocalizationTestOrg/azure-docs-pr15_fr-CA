<properties
    pageTitle="Télécharger le Kit de développement Azure pour PHP"
    description="Découvrez comment télécharger et installer le Kit de développement Azure pour PHP."
    documentationCenter="php"
    services="app-service\web"
    authors="allclark"
    manager="douge"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="06/01/2016"
    ms.author="allclark;yaqiyang"/>

#<a name="download-the-azure-sdk-for-php"></a>Télécharger le Kit de développement Azure pour PHP

## <a name="overview"></a>Vue d’ensemble

Le Kit de développement Azure pour PHP inclut des composants qui vous permettent de développer, déployer et gérer des applications PHP pour Azure. Plus précisément, le Kit de développement Azure pour PHP inclut les éléments suivants :

* **Bibliothèques de client le PHP pour Azure**. Ces bibliothèques de classes fournissent une interface pour l’accès aux fonctionnalités d’Azure, tels que les services de gestion de données et les services en nuage.  
* **L’Interface de ligne de commande Azure pour Mac, Linux et Windows (CLI Azure)**. Il s’agit d’un ensemble de commandes pour le déploiement et la gestion des services Azure, tels que les sites Web d’Azure et d’Azure Virtual Machines. Le travail d’Azure CLI sur toutes les plates-formes, y compris Mac, Linux et Windows.
* **PowerShell azure (Windows uniquement)**. Il s’agit d’un jeu d’applets de commande PowerShell pour le déploiement et la gestion des Services Azure, tels que les Services en nuage et les Machines virtuelles.
* **Les émulateurs Azure (Windows uniquement)**. Les émulateurs de calcul et de stockage sont des émulateurs locales de services en nuage et les services de gestion des données qui vous permettent de tester une application localement. Les émulateurs de Azure s’exécutent uniquement sous Windows.

Les sections ci-dessous décrivent comment télécharger et installer les composants décrits ci-dessus.

Les instructions de cette rubrique supposent que vous avez [PHP] [ install-php] installé.

> [AZURE.NOTE] Vous devez avoir PHP version 5.5 ou ultérieure pour utiliser les bibliothèques de client PHP pour Azure.

##<a name="php-client-libraries-for-azure"></a>Bibliothèques de client PHP pour Azure

Les bibliothèques clientes de PHP pour Azure fournissent une interface pour l’accès aux fonctionnalités d’Azure, tels que les services de gestion de données et services, à partir de n’importe quel système d’exploitation en nuage. Ces bibliothèques peuvent être installés via le compositeur.

Pour plus d’informations sur la façon d’utiliser les bibliothèques de Client PHP pour Azure, voir [comment utiliser le Service d’objet Blob][blob-service], [Comment faire pour utiliser le Service de la Table] [ table-service] et [comment utiliser le Service de file d’attente][queue-service].

###<a name="install-via-composer"></a>Installer via Composer

1. [Installer Git][install-git].


    > [AZURE.NOTE] Sous Windows, vous devez également ajouter le Git exécutable à votre variable d’environnement PATH.

2. Créez un fichier nommé **composer.json** à la racine de votre projet et ajoutez le code suivant :

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Télécharger ** [composer.phar] [ composer-phar] ** dans la racine de votre projet.

4. Ouvrez une invite de commande et l’exécuter dans la racine de votre projet

        php composer.phar install

##<a name="azure-powershell-and-azure-emulators"></a>PowerShell Azure et Azure émulateurs

PowerShell Azure est un jeu d’applets de commande PowerShell pour le déploiement et la gestion des Services Azure (par exemple, les Services en nuage et les Machines virtuelles). Les émulateurs Azure sont les émulateurs de services en nuage et les services de gestion des données qui vous permettent de tester une application localement. Ces composants sont pris en charge Windows uniquement.

Il est recommandé d’installer PowerShell d’Azure et les émulateurs Azure pour utiliser [Microsoft Web Platform Installer][download-wpi]. Notez que vous pouvez également choisir d’installer d’autres composants de développement, telles que PHP, SQL Server, le Microsoft Drivers pour SQL Server pour PHP et WebMatrix.

Pour plus d’informations sur l’utilisation de PowerShell d’Azure, voir [comment utiliser le PowerShell Azure][powershell-tools].

##<a name="azure-cli"></a>CLI Azure

L’infrastructure du langage commun Azure est un ensemble de commandes pour le déploiement et la gestion des services Azure, tels que les sites Web d’Azure et d’Azure Virtual Machines. Pour plus d’informations sur l’installation du CLI d’Azure, consultez [installer l’interface CLI d’Azure](xplat-cli-install.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
