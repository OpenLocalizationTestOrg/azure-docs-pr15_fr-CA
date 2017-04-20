<properties
   pageTitle="Créer un réseau virtuel à l’aide d’un modèle ARM | Microsoft Azure"
   description="Apprenez à créer un réseau virtuel à l’aide d’un modèle ARM | Gestionnaire de ressources."
   services="virtual-network"
   documentationCenter=""
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial"/>

# <a name="create-a-virtual-network-by-using-an-arm-template"></a>Créer un réseau virtuel à l’aide d’un modèle ARM

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Ce document décrit la création d’un VNet en utilisant le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [créer un réseau virtuel dans le modèle de déploiement classique](virtual-networks-create-vnet-classic-pportal.md).

Vous apprendrez à télécharger et modifier et modèle ARM existant à partir de GitHub et déployer le modèle à partir de la CLI d’Azure, PowerShell et GitHub.

Si vous déployez simplement le modèle ARM directement à partir de GitHub, sans modifications, passez de [déployer un modèle à partir de github](#deploy-the-arm-template-by-using-click-to-deploy).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]