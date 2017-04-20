<properties
    pageTitle="Différentes façons de créer un ordinateur virtuel de Windows | Microsoft Azure"
    description="Répertorie les différentes façons de créer une machine virtuelle Windows avec le Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>Différentes façons de créer une machine virtuelle Windows avec le Gestionnaire de ressources

Azure offre différentes manières de créer un ordinateur virtuel, car les machines virtuelles sont adaptés à des fins et à différents utilisateurs. Cela signifie que vous devez faire certains choix sur l’ordinateur virtuel et comment le créer. Cet article vous donne un résumé de ces différentes possibilités et les liens pour obtenir des instructions.

## <a name="azure-portal"></a>Azure portal

L’utilisation du portail Azure d’est un moyen simple de découvrir un ordinateur virtuel, surtout si vous démarrez avec Azure. 

[Créer un ordinateur virtuel exécutant Windows à l’aide du portail](virtual-machines-windows-hero-tutorial.md)

## <a name="template"></a>Modèle

Ordinateurs virtuels nécessitent une combinaison de ressources (comme une disponibilité des jeux et des comptes de stockage). Plutôt que de déployer et gérer séparément de chaque ressource, vous pouvez créer un modèle de gestionnaire de ressources Azure qui déploie et toutes les ressources dans une opération unique et coordonnée des dispositions.

- [Créer une machine virtuelle Windows avec un modèle de gestionnaire de ressources](virtual-machines-windows-ps-template.md)


## <a name="azure-powershell"></a>PowerShell Azure

Si vous préférez travailler dans une invite de commandes, vous pouvez utiliser PowerShell d’Azure.

- [Créer un ordinateur virtuel de Windows à l’aide de PowerShell](virtual-machines-windows-ps-create.md)


## <a name="visual-studio"></a>Visual Studio

Visual Studio permet de créer, de gérer et de déployer des machines virtuelles avec les outils Azure pour Visual Studio et le Kit de développement Azure.

[Azure Tools pour Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)

