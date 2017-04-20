<properties
   pageTitle="Dépannage des échecs d’extension de mémoire virtuelle de Windows | Microsoft Azure"
   description="Obtenir des informations sur le dépannage des échecs d’extension de mémoire virtuelle de Windows Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Dépannage des échecs d’extension de mémoire virtuelle de Windows Azure

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Affichage de l’état de l’extension
Azure modèles du Gestionnaire de ressources peuvent être exécutés à partir de PowerShell d’Azure. Une fois que le modèle est exécuté, l’état d’extension peut être affiché dans l’Explorateur de ressources Azure ou les outils de ligne de commande.

Voici un exemple :

PowerShell Azure :

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

Voici l’exemple de sortie :

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Dépannage des échecs d’extension

### <a name="re-running-the-extension-on-the-vm"></a>Ré-exécutez l’extension sur l’ordinateur virtuel

Si vous exécutez les scripts sur l’ordinateur virtuel à l’aide d’Extension de Script personnalisé, vous pouvez parfois exécuter une erreur où ordinateur virtuel a été créé avec succès, mais le script a échoué. Dans ces conditions, la méthode recommandée pour récupérer cette erreur est pour supprimer l’extension et exécutez à nouveau le modèle à nouveau.
Remarque : À l’avenir, cette fonctionnalité serait renforcée pour supprimer le besoin de désinstaller l’extension.


#### <a name="remove-the-extension-from-azure-powershell"></a>Supprimer l’extension de PowerShell d’Azure

    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Une fois que l’extension a été supprimée, le modèle peut être exécuté de nouveau pour exécuter les scripts sur l’ordinateur virtuel.
