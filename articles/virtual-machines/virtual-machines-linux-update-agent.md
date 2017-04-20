<properties
    pageTitle="Mise à jour de l’Agent pour Linux Azure GitHub | Microsoft Azure"
    description="Découvrez comment la mise à jour de l’Agent de Linux Azure pour votre VM Linux dans Azure vers la version lateset de Github"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/14/2015"
    ms.author="mingzhan"/>


# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Comment mettre à jour l’Agent de Linux Azure sur un ordinateur virtuel à la version la plus récente à partir de GitHub

Pour mettre à jour votre [Agent de Linux Azure](https://github.com/Azure/WALinuxAgent) sur une VM Linux dans Azure, vous devez avoir :

1. Une VM Linux en cours d’exécution dans Azure.
2. Une connexion à cette VM Linux à l’aide de SSH.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [AZURE.NOTE] Si vous exécutez cette tâche depuis un ordinateur Windows, vous pouvez utiliser PuTTY pour SSH dans l’ordinateur Linux. Pour plus d’informations, consultez [comment ouvrir une session sur un Linux exécutant d’ordinateur virtuel](virtual-machines-linux-mac-create-ssh-keys.md).

Distros de Linux standard Azure ont mis le package de l’Agent de Linux Azure dans leurs référentiels, veuillez vérifier et installer la version la plus récente à partir de ce référentiel Distro tout d’abord si possible.  

Pour Ubuntu, tapez :

    #sudo apt-get install walinuxagent

Et sur CentOS, tapez :

    #sudo yum install waagent


Pour Oracle, Linux, assurez-vous que le `Addons` référentiel est activé. Cliquez modifier le fichier `/etc/yum.repos.d/public-yum-ol6.repo`(6 de Linux Oracle) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) et modifiez la ligne `enabled=0` à `enabled=1` **[ol6_addons]** ou **[ol7_addons]** dans ce fichier.

Ensuite, pour installer la dernière version de l’Agent de Linux Azure, tapez :


    #sudo yum install WALinuxAgent

Si vous ne trouvez pas le référentiel de modules complémentaires vous pouvez simplement ajouter ces lignes à la fin de votre fichier .repo en fonction de votre version de Linux d’Oracle :

Pour les machines virtuelles de Oracle Linux 6 :

    [ol6_addons]
    name=Add-Ons for Oracle Linux $releasever ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
    gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
    gpgcheck=1
    enabled=1

Pour les machines virtuelles de Oracle Linux 7 :

    [ol7_addons]
    name=Oracle Linux $releasever Add ons ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=1
    enabled=0

Tapez ensuite :

    #sudo yum update WALinuxAgent

C’est en général tout que vous avez besoin, mais si pour une raison quelconque vous devez l’installer à partir de https://github.com directement, procédez comme suit.


## <a name="install-wget"></a>Installation de wget

Connectez-vous à votre ordinateur virtuel à l’aide de SSH.

Installez wget (il y a certaines distros qui ne l’installez pas par défaut comme Redhat et Oracle Linux CentOS les versions 6.4 et 6.5) en tapant `#sudo yum install wget` sur la ligne de commande.


## <a name="download-the-latest-version"></a>Télécharger la version la plus récente

Ouvrir [la version de l’Agent de Linux Azure dans GitHub](https://github.com/Azure/WALinuxAgent/releases) dans une page web et trouver le numéro de version plus récent. (Vous pouvez rechercher la version actuelle en tapant `#waagent --version`.)

### <a name="for-version-20x-type"></a>Pour la version 2.0.x, tapez :

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   La ligne suivante utilise la version 2.0.14 par exemple :

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### <a name="for-version-21x-or-later-type"></a>Pour la version 2.1.x ou version ultérieure, tapez :

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   La ligne suivante utilise la version 2.1.0 à titre d’exemple :

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## <a name="install-the-azure-linux-agent"></a>Installation de l’Agent de Linux Azure

### <a name="for-version-20x-use"></a>Pour la version 2.0.x, utilisez :

 Rendez waagent exécutable :

    #chmod +x waagent

 Copier de nouveau l’exécutable dans/usr/sbin /.

  Pour la plupart des Linux, utilisez :

    #sudo cp waagent /usr/sbin

  Pour CoreOS, utilisez :

    #sudo cp waagent /usr/share/oem/bin/

  S’il s’agit d’une nouvelle installation de l’Agent de Linux Azure, exécutez :
 
    #sudo /usr/sbin/waagent -install -verbose

### <a name="for-version-21x-use"></a>Pour la version 2.1.x, utilisez :

Vous devrez peut-être installer le package `setuptools` tout d’abord, voyez [ici](https://pypi.python.org/pypi/setuptools). Ensuite, exécutez :

    #sudo python setup.py install

## <a name="restart-the-waagent-service"></a>Redémarrez le service de waagent

Pour la plupart des linux Distros :

    #sudo service waagent restart

Pour Ubuntu, utilisez :

    #sudo service walinuxagent restart

Pour CoreOS, utilisez :

    #sudo systemctl restart waagent

## <a name="confirm-the-azure-linux-agent-version"></a>Confirmation de la version de l’Agent de Linux Azure

    #waagent -version

Pour CoreOS, la commande ci-dessus peut ne pas fonctionne.

Vous verrez que la version de l’Agent de Linux Azure a été mis à jour vers la nouvelle version.

Pour plus d’informations sur l’Agent de Linux Azure, consultez le [Fichier README Agent de Linux de Azure](https://github.com/Azure/WALinuxAgent).
