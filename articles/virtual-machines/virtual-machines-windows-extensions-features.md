<properties
 pageTitle="Extensions de la machine virtuelle et de fonctionnalités | Microsoft Azure"
 description="Découvrez les extensions disponibles pour les ordinateurs virtuels Azure, regroupées par ce qu’ils fournissent ou améliorent."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/30/2016"
 ms.author="nepeters"/>

# <a name="about-virtual-machine-extensions-and-features"></a>À propos des fonctionnalités et extensions de machine virtuelle

## <a name="azure-vm-extensions"></a>Extensions d’Azure VM

Les extensions de Machine virtuelle Azure sont de petites applications qui fournissent une tâche de configuration et de l’automatisation du déploiement post Azure machines virtuelles en fonctionnement. Par exemple, si un ordinateur virtuel requiert un logiciel de protection antiviruse installée, ou configuration de Docker, une extension de la machine virtuelle peut être utilisée pour effectuer ces tâches. Les extensions Azure VM peuvent être exécutées à l’aide de la CLI d’Azure, PowerShell, gérer les ressources de modèles et le portail Azure. Extensions peuvent être fournies avec un nouveau déploiement de machines virtuelles ou exécuter sur n’importe quel système existant.

Ce document fournit des conditions préalables à l’extension d’Azure Virtual Machine et des conseils sur la détection des extensions de mémoire virtuelle disponibles. 

## <a name="azure-vm-agent"></a>Agent d’Azure VM

L’Agent de la machine virtuelle Azure gère l’interaction entre une Machine virtuelle de Azure et le contrôleur de tissu Azure. L’agent de la machine virtuelle est chargée de nombreux aspects fonctionnels de déploiement et de gestion des ordinateurs virtuels de Azure, y compris les Extensions de la machine virtuelle en cours d’exécution. L’Agent de la machine virtuelle Azure est préinstallé sur les Images de la galerie Azure et peut être installé sur les systèmes d’exploitation pris en charge. 

Pour plus d’informations sur les systèmes d’exploitation pris en charge et les instructions d’installation, voir [Agent de Machine virtuelle d’Azure](./virtual-machines-windows-classic-agents-and-extensions.md).

## <a name="discover-vm-extensions"></a>Découvrez les Extensions de la machine virtuelle

De nombreuses extensions de machine virtuelle différentes sont disponibles pour une utilisation avec Azure Virtual Machines. Pour obtenir une liste complète, exécutez la commande suivante avec la CLI d’Azure, remplaçant l’emplacement avec l’emplacement de son choix.

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>Extensions communes de machine virtuelle

|Nom de l’extension   |Description   |Plus d’informations   |
|---|---|---|
|Extension de Script personnalisé pour Windows  | Exécuter des scripts sur un ordinateur virtuel de Azure  |[Extension de Script personnalisé pour Windows](./virtual-machines-windows-extensions-customscript.md)   |
|Extension de DSC pour Windows | Extension de PowerShell DSC (Configuration de l’état souhaitée).  | [Extension de machine virtuelle docker](./virtual-machines-windows-extensions-dsc-overview.md)  |
|Extension de Diagnostics de Windows Azure | Gérer les Diagnostics de Windows Azure |[Extension de Diagnostics de Windows Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
