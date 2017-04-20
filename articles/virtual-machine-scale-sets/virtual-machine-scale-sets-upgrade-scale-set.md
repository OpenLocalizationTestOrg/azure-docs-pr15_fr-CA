<properties
    pageTitle="Déployer une application sur les jeux d’ordinateur virtuel échelle | Microsoft Azure"
    description="Déployer une application sur les jeux d’échelle de machine virtuelle"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>


# <a name="upgrade-a-virtual-machine-scale-set"></a>Mise à niveau d’un ensemble d’échelle de machine virtuelle

Cet article décrit comment vous pouvez déployer une mise à jour du système d’exploitation à une échelle Azure VM définir sans interruption de service. Dans ce contexte, une mise à jour du système d’exploitation implique la modification de la version ou la version du système d’exploitation ou la modification de l’URI d’une image personnalisée. Mise à jour sans moyens d’interruptions de service mise à jour virtual machines un à la fois ou dans des groupes (par exemple, un domaine d’erreur à la fois) plutôt que toutes à la fois. De cette manière, toutes les machines virtuelles qui ne sont pas être mis à niveau peut conserver en cours d’exécution.

Pour éviter toute ambiguïté, nous allons faire la distinction entre trois types de mise à jour du système d’exploitation que vous souhaiterez peut-être effectuer :

- Modification de la version ou la référence d’une image de la plate-forme. Par exemple, la modification Ubuntu version 14.04.2-LTS de 14.04.201506100 à 14.04.201507060, ou la modification de la référence SKU Ubuntu 15.10/dernière à 16.04.0-LTS/latest. Ce scénario est décrit dans cet article.

- Modifiant l’URI qui pointe vers une nouvelle version d’une image personnalisée que vous créé (**Propriétés** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **image** > **uri**). Ce scénario est décrit dans cet article.

- Application des correctifs du système d’exploitation à partir d’un ordinateur virtuel (des exemples l’installation d’un correctif de sécurité et de mise à jour de Windows en cours d’exécution). Ce scénario est pris en charge mais pas abordée dans cet article.

Les deux premières options sont prises en charge des exigences visées au présent article. Vous devez créer une nouvelle échelle pour exécuter la troisième option définie.

Jeux d’échelle machine virtuelle qui sont déployés dans le cadre d’un cluster [d’Azure Fabric de Service](https://azure.microsoft.com/services/service-fabric/) ne sont pas abordés ici.

La séquence de base permettant de modifier la version du système d’exploitation/le SKU d’une image de la plate-forme ou l’URI d’une image personnalisée se présente comme suit :

1. Obtenir le modèle de jeu d’échelle de machine virtuelle.

2. Modification de la version, un point de stock ou un URI dans le modèle de valeur.

3. Mettre à jour le modèle.

4. Effectuez un appel de *manualUpgrade* sur les ordinateurs virtuels dans l’ensemble de l’échelle. Cette étape est pertinente uniquement si *upgradePolicy* est défini sur **manuel** dans l’ensemble de l’échelle. Si elle est définie sur **automatique**, tous les ordinateurs virtuels sont mis à niveau à la fois, ce qui provoque les interruptions de service.


Grâce à ces informations en arrière-plan à l’esprit, nous allons voir comment vous a pu mettre à jour la version d’une échelle définie dans PowerShell et à l’aide de l’API REST. Ces exemples couvrent le cas d’une image de la plate-forme, mais il fournit suffisamment d’informations pour vous permettre d’adapter ce processus vers une image personnalisée.

## <a name="powershell"></a>PowerShell##

Cet exemple met à jour une échelle de machine virtuelle Windows définie sur la nouvelle version 4.0.20160229. Après la mise à jour du modèle, il effectue une mise à jour une instance de machine virtuelle à la fois.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Si vous mettez à jour l’URI pour une image personnalisée au lieu de changer d’une version d’image de plate-forme, remplacez la ligne « définir la nouvelle version » comme suit :

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## <a name="the-rest-api"></a>L’API REST

Voici quelques exemples de Python qui utilisent l’API REST Azure pour déployer une mise à jour de la version du système d’exploitation. Les deux utilisent la bibliothèque légers [azurerm](https://pypi.python.org/pypi/azurerm) des fonctions wrapper de API REST de Azure pour effectuer une opération GET sur le modèle de jeu d’échelle, suivi d’une commande PUT avec un modèle mis à jour. Elles consultent aussi vues d’instances de machine virtuelle pour identifier les ordinateurs virtuels par domaine de mise à jour.

### <a name="vmssupgrade"></a>Vmssupgrade

 [Vmssupgrade](https://github.com/gbowerman/vmsstools) est un script Python qui est utilisé pour déployer une mise à niveau du système d’exploitation sur une échelle de machine virtuelle en cours d’exécution définie un domaine de mise à jour à la fois.

![Script de Vmssupgrade pour le choix d’ordinateurs virtuels ou un domaine de mise à jour](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Ce script vous permet de choisir des machines virtuelles spécifiques à mettre à jour ou de spécifier un domaine de mise à jour. Il prend en charge le changement de version d’une image de plate-forme ou de modifier l’URI d’une image personnalisée.

### <a name="vmsseditor"></a>Vmsseditor

[Vmsseditor](https://github.com/gbowerman/vmssdashboard) est un éditeur à usage général pour les jeux d’échelle de machine virtuelle qui affiche le statut de machine virtuelle sous la forme d’un heatmap où une ligne représente un domaine de mise à jour. Entre autres choses, vous pouvez mettre à jour le modèle pour un ensemble d’échelle avec une nouvelle version, le point de stock ou l’URI de l’image personnalisée et choisissez mise à niveau de domaines d’erreur. Lorsque vous procédez ainsi, tous les ordinateurs virtuels dans ce domaine de mise à jour sont mis à niveau vers le nouveau modèle. Sinon, vous pouvez effectuer une mise à niveau propagée, en fonction de la taille de lot de votre choix.  

La capture d’écran suivante montre un modèle d’une échelle définie pour Ubuntu 14.04-2LTS version 14.04.201507060. Beaucoup plus d’options ont été ajoutées à cet outil, dans la mesure où cette capture d’écran a été prise.

![Modèle de Vmsseditor d’une échelle définie pour Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Une fois que vous cliquez sur **mettre à jour** et **Obtenir des détails**, les machines virtuelles dans UD 0 démarrer mettre à jour.

![Mise à jour de Vmsseditor affichage en cours](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)
