<properties
    pageTitle="Opération de maintenance planifiée pour les machines virtuelles de Windows | Microsoft Azure"
    description="Comprendre quel Azure maintenance planifiée est et comment il affecte votre machines virtuelles Windows Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="drewm"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="drewm"/>

# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>Maintenance planifiée pour les machines virtuelles dans Azure


Comprendre quel Azure maintenance planifiée est et comment il peut affecter la disponibilité de vos machines virtuelles de Windows. Cet article est également disponible pour les [ordinateurs virtuels Linux](virtual-machines-linux-planned-maintenance.md). 

Cet article fournit un arrière-plan pour le processus d’Azure de maintenance planifiée. Si vous souhaitez résoudre les problèmes liés à la raison pour laquelle votre ordinateur virtuel redémarré, vous pouvez [lire ce blog post détaillant l’affichage de machine virtuelle redémarre les journaux](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="why-azure-performs-planned-maintenance"></a>Pourquoi Azure effectue la maintenance planifiée

Microsoft Azure exécute régulièrement des mises à jour dans le monde entier afin d’améliorer la fiabilité, performances et sécurité de l’infrastructure de l’hôte qui se trouve sous les ordinateurs virtuels. La plupart de ces mises à jour sont effectuées sans aucune incidence sur vos ordinateurs virtuels ou les Services en nuage, y compris les conserve en mémoire les mises à jour.

Toutefois, certaines mises à jour nécessitent un redémarrage pour vos ordinateurs virtuels pour appliquer les mises à jour nécessaires à l’infrastructure. Les ordinateurs virtuels sont arrêtés alors que nous l’infrastructure des correctifs, et ensuite les ordinateurs virtuels sont redémarrés.

Veuillez noter qu’il existe deux types de maintenance qui peuvent avoir un impact sur la disponibilité de vos machines virtuelles : planifiées et non planifiées. Cette page décrit comment Microsoft Azure effectue la maintenance planifiée. Pour plus d’informations sur la maintenance planifiée, voir [comprendre planifiée par rapport à une maintenance planifiée](virtual-machines-windows-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]
