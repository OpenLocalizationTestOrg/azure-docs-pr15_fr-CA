<properties
   pageTitle="Dépannage des échecs d’extension Linux VM | Microsoft Azure"
   description="Obtenir des informations sur le dépannage des échecs d’extension Azure VM de Linux"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>Dépannage des échecs d’extension Azure VM de Linux

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Affichage de l’état de l’extension
Azure modèles du Gestionnaire de ressources peuvent être exécutés à partir de la CLI d’Azure. Une fois que le modèle est exécuté, l’état d’extension peut être affiché dans l’Explorateur de ressources Azure ou les outils de ligne de commande.

Voici un exemple :

CLI Azure :

      azure vm get-instance-view


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

## <a name="troubleshooting-extenson-failures"></a>Dépannage des échecs de Extenson :

### <a name="re-running-the-extension-on-the-vm"></a>Ré-exécutez l’extension sur l’ordinateur virtuel

Si vous exécutez les scripts sur l’ordinateur virtuel à l’aide d’Extension de Script personnalisé, vous pouvez parfois exécuter une erreur où ordinateur virtuel a été créé avec succès, mais le script a échoué. Dans ces conditions, la méthode recommandée pour récupérer cette erreur est pour supprimer l’extension et exécutez à nouveau le modèle à nouveau.
Remarque : À l’avenir, cette fonctionnalité serait renforcée pour supprimer le besoin de désinstaller l’extension.

#### <a name="remove-the-extension-from-azure-cli"></a>Supprimer l’extension de l’infrastructure du langage commun Azure

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Où « objet Publisher-name » correspond au type d’extension à partir de la sortie de « get-instance-vue azure vm » et est le nom de la ressource d’extension à partir du modèle

Une fois que l’extension a été supprimée, le modèle peut être exécuté de nouveau pour exécuter les scripts sur l’ordinateur virtuel.
