<properties
   pageTitle="Création de modèles avec des extensions Windows VM | Microsoft Azure"
   description="En savoir plus sur la création de modèles d’Azure le Gestionnaire de ressources avec des extensions pour les machines virtuelles de Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>Création de modèles d’Azure le Gestionnaire de ressources avec des extensions de mémoire virtuelle de Windows

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

À partir de PowerShell d’Azure, exécutez l’applet de commande PowerShell de Azure suivante :

      Get-AzureVMAvailableExtension


Cette applet de commande renvoie le nom de l’éditeur, le nom de l’extension et la version comme suit :

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Ces trois propriétés correspondent à « publisher », « type » et « typeHandlerVersion » respectivement dans l’extrait de modèle ci-dessus.

>[AZURE.NOTE]Il est toujours recommandé d’utiliser la dernière version de l’extension pour obtenir la fonctionnalité la plus à jour.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identification du schéma pour les paramètres de configuration d’extension

L’étape suivante avec la création d’un modèle d’extension consiste à identifier le format pour fournir les paramètres de configuration. Chaque extension prend en charge son propre jeu de paramètres.

Pour consulter des exemples pour les extensions Windows, consultez [exemples d’extensions Windows](virtual-machines-windows-extensions-configuration-samples.md).


Reportez-vous à pour obtenir un modèle totalement terminé avec les extensions de la machine virtuelle.

[Extension de Script personnalisé sur un ordinateur virtuel de Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


Après la création du modèle, vous pouvez déployer à l’aide de PowerShell d’Azure.
