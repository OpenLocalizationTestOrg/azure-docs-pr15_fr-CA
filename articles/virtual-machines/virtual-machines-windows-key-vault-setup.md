<properties
    pageTitle="Configurer la clé de coffre-fort pour les machines virtuelles dans le Gestionnaire de ressources Azure | Microsoft Azure"
    description="La configuration en chambre forte de la clé pour une utilisation avec une machine virtuelle d’Azure le Gestionnaire de ressources."
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
    ms.date="05/31/2016"
    ms.author="singhkay"/>

# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurer la clé de coffre-fort pour les machines virtuelles dans le Gestionnaire de ressources Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique

Dans le Gestionnaire de ressources Azure pile, secrets/certificats sont modelées en tant que ressources fournies par le fournisseur de ressources de stockage en chambre forte de clé. Pour en savoir plus sur le stockage en chambre forte de clé, consultez [Quel est le coffre-fort de clé Azure ?](../key-vault/key-vault-whatis.md)

>[AZURE.NOTE] 
>
>1. Dans l’ordre pour chambre forte de clé à utiliser avec les ordinateurs virtuels de gestionnaire de ressources Azure, la propriété de **EnabledForDeployment** sur le coffre-fort de la clé doit être définie sur true. Pour cela, dans les différents clients.
>
>2. Le coffre-fort de la clé doit être créée dans le même abonnement et le même emplacement que la Machine virtuelle.

## <a name="use-powershell-to-set-up-key-vault"></a>Utilisation de PowerShell pour configurer le stockage en chambre forte de clé
Pour créer un coffre-fort de clé à l’aide de PowerShell, consultez [mise en route de la chambre forte de clé Azure](../key-vault/key-vault-get-started.md#vault).

Pour les coffres-forts nouvelles clés, vous pouvez utiliser cette applet de commande PowerShell :

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Pour les coffres-forts principaux existants, vous pouvez utiliser cette applet de commande PowerShell :

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Nous CLI pour configurer le stockage en chambre forte de clé
Pour créer un coffre-fort de clé à l’aide de l’interface de ligne de commande (CLI), consultez [gestion de coffre-fort de clé à l’aide de CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Pour l’infrastructure du langage commun, vous devez créer le coffre-fort de clés avant d’attribuer la stratégie de déploiement. Pour cela, à l’aide de la commande suivante :

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Utilisation de modèles pour paramétrer le coffre-fort de la clé
Lorsque vous utilisez un modèle, vous devez définir le `enabledForDeployment` propriété `true` pour la ressource de stockage en chambre forte de clé.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Pour les autres options que vous pouvez configurer lorsque vous créez un coffre-fort de clé à l’aide de modèles, voir [Création d’un coffre-fort de clé](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
