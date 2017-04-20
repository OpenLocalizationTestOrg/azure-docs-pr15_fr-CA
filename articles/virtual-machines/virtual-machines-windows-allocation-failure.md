<properties
    pageTitle="Dépannage des échecs d’allocation de mémoire virtuelle de Windows | Microsoft Azure"
    description="Résoudre les échecs d’allocation lorsque vous créez, redémarrez ou redimensionnez un ordinateur virtuel Windows Azure"
    services="virtual-machines-windows, azure-resource-manager"
    documentationCenter=""
    authors="JiangChen79"
    manager="felixwu"
    editor=""
    tags="top-support-issue,azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="cjiang"/>

# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Résoudre les échecs d’allocation lorsque vous créez, redémarrez ou redimensionnez des ordinateurs virtuels de Windows dans Azure

Lorsque vous créez une machine virtuelle, redémarrez arrêtés VMs (libérés) ou redimensionnez une machine virtuelle, Microsoft Azure alloue les ressources de calcul à votre abonnement. Vous pouvez parfois recevoir des erreurs lors de l’exécution de ces opérations, avant même que vous atteignez les limites de l’abonnement Azure. Cet article explique les causes des échecs d’allocation de la commune et suggère des mesures correctives possibles. Les informations peuvent également être utiles lorsque vous planifiez le déploiement de vos services. Vous pouvez également [résoudre les échecs d’allocation lorsque vous créez, redémarrerez ou redimensionnez des machines virtuelles de Linux dans Azure](virtual-machines-linux-allocation-failure.md).

[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]
