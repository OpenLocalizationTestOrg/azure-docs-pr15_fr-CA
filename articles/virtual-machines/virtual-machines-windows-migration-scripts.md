<properties
    pageTitle="Outils de communauté pour la gestion des services Azure à la migration du Gestionnaire de ressources Azure"
    description="Cet article répertorie les outils qui ont été fournies par la Communauté pour aider avec migration des ressources IaaS de gestion des services Azure à la pile du Gestionnaire de ressources Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="singhkay"/>

# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Outils de communauté pour la gestion des services Azure à la migration du Gestionnaire de ressources Azure

Cet article répertorie les outils qui ont été fournies par la Communauté pour aider avec migration des ressources IaaS de gestion des services Azure à la pile du Gestionnaire de ressources Azure.

>[AZURE.NOTE]Ces outils ne sont pas officiellement pris en charge par le Support Microsoft. Par conséquent, ils sont open source sur Github et nous sommes acceptons PRs de correctifs ou d’autres scénarios. Pour signaler un problème, utilisez la fonctionnalité de problèmes Github.
>
> Migration à l’aide de ces outils entraîne temps d’arrêt de votre Machine virtuelle classique. Si vous recherchez la migration des plates-formes prises en charge, visitez le site 
>
>- [Migration de la plate-forme prise en charge de ressources IaaS classique à une pile d’Azure le Gestionnaire de ressources](./virtual-machines-windows-migration-classic-resource-manager.md)
>- [Techniques détaillées sur une plate-forme prise en charge de migration de classique pour le Gestionnaire de ressources Azure](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
>- [Migration des ressources IaaS de classique pour Azure le Gestionnaire de ressources à l’aide de PowerShell d’Azure](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## <a name="asm2arm"></a>ASM2ARM

Il s’agit d’un module de script PowerShell pour migrer votre **seule** Machine virtuelle (VM) à partir de la pile de la gestion des services Azure pour pile d’Azure le Gestionnaire de ressources. 

[Créer un lien vers la documentation de l’outil](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz

migAz est une option supplémentaire pour faire migrer un ensemble complet de ressources IaaS de gestion de Service Azure aux ressources d’Azure le Gestionnaire de ressources IaaS. La migration peut se produire dans l’abonnement même ou entre différents abonnements et types d’abonnements (ex : abonnement du fournisseur de services cryptographiques).

[Créer un lien vers la documentation de l’outil](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)