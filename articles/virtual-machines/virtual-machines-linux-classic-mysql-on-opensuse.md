<properties
    pageTitle="Installation de MySQL sur une machine virtuelle de OpenSUSE | Microsoft Azure"
    description="Apprenez à installer MySQL sur une machine VMirtual de Linux OpenSUSE dans Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installation de MySQL sur une machine virtuelle en cours d’exécution OpenSUSE Linux dans Azure

[MySQL] [ MySQL] est une base de données SQL les plus courants, open source. Ce didacticiel vous montre comment créer un ordinateur virtuel exécutant OpenSUSE Linux, puis installation de MySQL.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


<br>


## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Créer un ordinateur virtuel exécutant les OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Installer et exécuter MySQL sur l’ordinateur virtuel

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur MySQL, reportez-vous à la [Documentation MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

