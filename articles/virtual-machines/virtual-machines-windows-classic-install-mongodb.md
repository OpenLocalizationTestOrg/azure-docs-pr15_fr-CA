<properties
    pageTitle="Installer MongoDB sur une machine virtuelle de Windows | Microsoft Azure"
    description="Découvrez comment installer MongoDB sur un Azure VM créée avec le modèle classique de déploiement Windows Server en cours d’exécution."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

#<a name="install-mongodb-on-a-windows-vm"></a>Installer MongoDB sur une machine virtuelle de Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour installer et configurer MongoDB en utilisant le modèle de déploiement du Gestionnaire de ressources, consultez [cet article](virtual-machines-windows-classic-install-mongodb.md).

[MongoDB] [ MongoDB] est une base de NoSQL open source, de hautes performances les plus courants. Cet article vous guide dans la création d’un serveur de Windows de machine virtuelle (VM) à l’aide d' [Azure portal classique][AzurePortal]. Ensuite, vous créez et que vous connectez un disque de données à la machine virtuelle avant l’installation et la configuration de MongoDB. Si vous avez un ordinateur virtuel existant dans Azure que vous souhaitez utiliser, vous pouvez accéder directement à [l’installation et la configuration de MongoDB](#install-and-run-mongodb-on-the-virtual-machine).


## <a name="create-a-virtual-machine-running-windows-server"></a>Créer un ordinateur virtuel exécutant Windows Server

Suivez ces instructions pour créer un ordinateur virtuel.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Vous pouvez ajouter un point de terminaison pour MongoDB lors de la création de la machine virtuelle et configurez-le comme suit : le nom en tant que **Mongo**et utiliser **TCP** comme protocole de la valeur **27017**à la fois les ports publics et privés.

## <a name="attach-a-data-disk"></a>Connectez un disque de données
Pour fournir le stockage pour la machine virtuelle, connectez un disque de données et de l’initialiser puis afin que Windows puisse l’utiliser. Si vous disposez déjà d’un disque de données, vous pouvez joindre ce disque existant, ou vous pouvez attacher un disque vide.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Pour obtenir des instructions sur l’initialisation de la disquette, consultez « Comment : initialiser un nouveau disque de données dans Windows Server « comment [attacher un disque de données pour une machine virtuelle Windows](virtual-machines-windows-classic-attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Installer et exécuter MongoDB sur l’ordinateur virtuel

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Résumé
Dans ce didacticiel, vous avez appris comment créer un ordinateur virtuel exécutant Windows Server, s’y connecter à distance et attacher un disque de données.  Vous avez également appris comment installer et configurer MongoDB sur la machine virtuelle Windows. Vous pouvez maintenant accéder à MongoDB sur la machine virtuelle Windows, en suivant les rubriques avancées de la [documentation MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com
