<properties
    pageTitle="Déployer le feu sur une machine virtuelle Linux | Microsoft Azure"
    description="Pour savoir comment installer la pile de la lampe sur une VM Linux"
    services="virtual-machines-linux"
    documentationCenter="virtual-machines"
    authors="jluk"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="juluk"/>

# <a name="deploy-lamp-stack-on-azure"></a>Déployer la pile de la lampe sur Azure
Cet article vous guidera à travers le déploiement d’un serveur web Apache, MySQL et PHP (la pile de la lampe) sur Azure. Vous aurez besoin d’un compte Azure ([obtenir une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/)) et l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) qui est [connecté à votre compte Azure](../xplat-cli-connect.md).

Il existe deux méthodes d’installation de lampe abordé dans cet article :

## <a name="quick-command-summary"></a>Récapitulatif des commandes rapide

1) Déployer la lampe sur le nouvel ordinateur virtuel

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Déployer le feu sur une machine virtuelle existante

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Déployer la lampe sur le nouvel ordinateur virtuel procédure pas à pas

Vous pouvez démarrer en créant un nouveau [groupe de ressources](../azure-resource-manager/resource-group-overview.md) qui contiendra la machine virtuelle :

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Pour créer la machine virtuelle elle-même, vous pouvez utiliser un modèle de gestionnaire de ressources Azure déjà écrit trouvé [ici sur GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Vous devez voir une réponse de demande de certaines entrées de plus :

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

Vous avez maintenant créé une VM Linux avec lampe déjà installé dessus. Si vous le souhaitez, vous pouvez vérifier l’installation en sautant à [Vérifiez feu correctement installé].

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Déployer la lampe de la procédure pas à pas de machine virtuelle existante

Si vous avez besoin d’aide pour créer une VM Linux vous pouvez tête [ici pour apprendre à créer une VM Linux] (. / virtual-machines-linux-quick-create-cli.md). Ensuite, vous devrez SSH dans la VM Linux. Si vous avez besoin d’aide sur la création d’un code SSH vous pouvez tête [ici pour apprendre à créer un code SSH sur Linux/Mac] (. / virtual-machines-linux-mac-create-ssh-keys.md).
Si vous disposez déjà d’un code SSH, aller plus loin et SSH dans votre VM Linux avec `ssh username@uniqueDNS`.

Maintenant que vous utilisez dans votre VM Linux, nous guidera dans l’installation de la pile de la lampe sur base de Debian de distributions. Les commandes exactes peuvent différer pour d’autres distros de Linux.

#### <a name="installing-on-debianubuntu"></a>L’installation de Debian/Ubuntu

Vous devez les packages suivants installés : `apache2`, `mysql-server`, `php5`, et `php5-mysql`. Vous pouvez installer directement ces packages de saisie ou à l’aide de Tasksel. Vous trouverez ci-dessous des instructions pour les deux options.
Avant d’installer, vous devez télécharger et mettre à jour les listes de package.

    user@ubuntu$ sudo apt-get update
    
##### <a name="individual-packages"></a>Packages individuels
À l’aide d’apt-get :

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>À l’aide de Tasksel
Vous pouvez également télécharger Tasksel, un outil de Debian/Ubuntu qui installe plusieurs packages associés comme une « tâche » coordonnée sur votre système.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Après l’exécution de l’une des options ci-dessus vous sera invité à installer ces packages et un certain nombre d’autres dépendances. Appuyez sur 'y' et 'Entrée' pour continuer, suivez les autres invites pour définir un mot de passe d’administration de MySQL. Ceci va installer les extensions PHP requises minimales nécessaires pour utiliser PHP avec MySQL. 

![][1]

Exécutez la commande suivante pour afficher d’autres extensions PHP qui sont disponibles sous forme de packages :

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Créer info.php dans le document

Vous devez maintenant pouvoir vérifier quelle version d’Apache, MySQL et PHP vous avez via la ligne de commande en tapant `apache2 -v`, `mysql -v`, ou `php -v`.

Si vous souhaitez poursuivre l’analyse, vous pouvez créer une page d’informations sur PHP rapide à afficher dans un navigateur. Créer un nouveau fichier avec Nano-éditeur de texte avec cette commande :

    user@ubuntu$ sudo nano /var/www/html/info.php

Dans l’éditeur de texte GNU Nano, ajoutez les lignes suivantes :

    <?php
    phpinfo();
    ?>

Enregistrez et quittez l’éditeur de texte.

Redémarrez Apache avec cette commande afin que toutes les nouvelles installations seront prises en compte.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Vérifiez le feu installé avec succès

Maintenant vous pouvez vérifier la page d’informations sur PHP que vous venez de créer dans votre navigateur en cliquant sur les http://youruniqueDNS/info.php, il doit ressembler à ceci.

![][2]

Vous pouvez vérifier votre installation Apache en affichant la Page par défaut d’Ubuntu Apache2 en accédant à http://youruniqueDNS/ vous. Vous devriez voir quelque chose comme ceci.

![][3]

Félicitations, vous avez simplement le programme d’installation une pile de lampe sur votre Azure VM.

## <a name="next-steps"></a>Étapes suivantes

Consultez la documentation d’Ubuntu sur la pile de lampe :

- [https://help.Ubuntu.com/Community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png
