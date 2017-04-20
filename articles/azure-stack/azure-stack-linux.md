<properties
    pageTitle="Invités Linux sur pile Azure | Microsoft Azure"
    description="Découvrez comment créer des machines virtuelles basées sur Linux sur la pile d’Azure."
    services="azure-stack"
    documentationCenter=""
    authors="anjayajodha"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anajod"/>
    
# <a name="deploy-linux-virtual-machines-on-azure-stack"></a>Déployer des machines virtuelles de Linux sur la pile d’Azure

Vous pouvez déployer des machines virtuelles de Linux sur le contrôle du concept pile Azure en ajoutant une image basée sur Linux dans la pile d’Azure Marketplace. Plusieurs fournisseurs Linux ont fourni des images qui peuvent être ajoutées dans un test de pile d’Azure, ou vous pouvez créer votre propre.

## <a name="download-an-image"></a>Télécharger une image

 1. Téléchargez et extraire une image compatible avec pile Azure à partir des liens suivants ou préparer votre propre :
  - [Bitnami](https://bitnami.com/azure-stack)
  - [CentOS](http://olstacks.cloudapp.net/latest/)
  - [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
  - [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
  - [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
  
 2. Extraire l’image disque dur virtuel si nécessaire et [Ajouter l’image sur le marché](azure-stack-add-vm-image.md). Assurez-vous que le `OSType` paramètre est défini sur `Linux`.
 
 3. Après que vous avez ajouté l’image sur le marché, un élément de marché est créé et vous pouvez déployer une machine virtuelle Linux.
  
## <a name="prepare-your-own-image"></a>Préparer votre propre image

1. Préparer votre propre image de Linux à l’aide d’une des instructions suivantes :
 - [Distributions de base centOS](../virtual-machines/virtual-machines-linux-create-upload-centos.md)
 - [Debian Linux](../virtual-machines/virtual-machines-linux-debian-create-upload-vhd.md)
 - [Oracle Linux](../virtual-machines/virtual-machines-linux-oracle-create-upload-vhd.md)
 - [Red Hat Enterprise Linux](../virtual-machines/virtual-machines-linux-redhat-create-upload-vhd.md)
 - [SLES & openSUSE](../virtual-machines/virtual-machines-linux-suse-create-upload-vhd.md)
 - [Ubuntu](../virtual-machines/virtual-machines-linux-create-upload-ubuntu.md)

2. Télécharger et installer l' [Agent de Linux Azure](https://github.com/Azure/WALinuxAgent/)

    L’Agent de Linux version 2.1.3 Azure ou version ultérieure est requis pour mettre en service votre VM Linux sur la pile d’Azure. La plupart des distributions ci-dessus déjà incluent cette version de l’agent ou plus sous la forme d’un package dans leurs référentiels (généralement appelé `WALinuxAgent` ou `walinuxagent`). Toutefois, si la version du package de l’agent Azure est moins 2.1.3 (2.0.18 ou inférieur), puis vous devez installer l’agent manuellement. La version installée peut être déterminée à partir du nom de package ou en exécutant `/usr/sbin/waagent -version` sur l’ordinateur virtuel.

    Suivez les instructions ci-dessous pour installer l’agent Azure Linux manuellement :

 - Tout d’abord, téléchargez l’agent d’Azure Linux plus récente à partir de [Github](https://github.com/Azure/WALinuxAgent/releases), exemple :

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.0.tar.gz

 - Décompresser l’agent Azure :

            # tar -vzxf v2.2.0.tar.gz

 - Installer les python-setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools

 - Installer l’agent Azure :

            # cd WALinuxAgent-2.2.0
            # sudo python setup.py install --register-service

    Systèmes avec les Python 2.x et 3.x est installé Python côte à côte peuvent avoir besoin d’exécuter la commande suivante :

        # sudo python3 setup.py install --register-service

    Pour plus d’informations, consultez l’Agent de Linux Azure [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).

3. [Ajouter l’image sur le marché](azure-stack-add-vm-image.md). Assurez-vous que le `OSType` paramètre est défini sur `Linux`.

4. Après que vous avez ajouté l’image sur le marché, un élément de marché est créé et vous pouvez déployer une machine virtuelle Linux.

## <a name="next-steps"></a>Étapes suivantes

[Forum aux questions sur la pile d’Azure](azure-stack-faq.md)