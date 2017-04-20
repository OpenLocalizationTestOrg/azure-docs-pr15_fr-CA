<properties
    pageTitle="Migrer vers le Gestionnaire de ressources avec PowerShell | Microsoft Azure"
    description="Cet article explique la migration de plate-forme prise en charge des ressources IaaS de classique pour le Gestionnaire de ressources Azure à l’aide des commandes PowerShell d’Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migration des ressources IaaS de classique pour le Gestionnaire de ressources Azure à l’aide de PowerShell d’Azure

Ces étapes vous montrent comment utiliser des commandes PowerShell d’Azure pour migrer l’infrastructure comme une ressource de service (IaaS) à partir du modèle de déploiement classique pour le modèle de déploiement du Gestionnaire de ressources Azure. 

Si vous le souhaitez, vous pouvez également migrer des ressources à l’aide de l' [Interface de ligne de commande (CLI Azure) Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md).

- Pour informations sur les scénarios de migration pris en charge, consultez [migration de plate-forme prise en charge de ressources IaaS classique pour le Gestionnaire de ressources Azure](virtual-machines-windows-migration-classic-resource-manager.md). 
- Pour des instructions détaillées et les étapes d’une migration, reportez-vous à la section [techniques détaillées sur une plate-forme prise en charge de la migration de classique pour le Gestionnaire de ressources Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).

## <a name="step-1-plan-for-migration"></a>Étape 1 : Planification de la migration

Voici quelques-unes des meilleures pratiques recommandées lors de l’évaluation de la migration ressources IaaS de classique pour le Gestionnaire de ressources :

- Lire les [configurations et les fonctionnalités prises en charge et non pris en charge](virtual-machines-windows-migration-classic-resource-manager.md). Si vous avez des machines virtuelles qui utilisent des configurations non prises en charge ou fonctionnalités, nous vous recommandons de que vous attendez que la prise en charge de la fonctionnalité/configuration à annoncer. Si elle répond à vos besoins, supprimer cette fonctionnalité ou sortir de cette configuration permettant la migration.
-   Si vous avez automatisé scripts de déploiement de votre infrastructure et vos applications aujourd'hui, essayez de créer une configuration de test similaire à l’aide de ces scripts pour la migration. Également, vous pouvez définir des environnements de l’échantillon à l’aide du portail Azure.

>[AZURE.IMPORTANT]Passerelles de réseau virtuel (site à site, ExpressRoute d’Azure, passerelle d’application, site-à-point) ne sont actuellement pas pris en charge pour la migration de classique pour le Gestionnaire de ressources. Pour migrer d’un réseau virtuel classique avec une passerelle, supprimer la passerelle avant d’exécuter une opération de validation pour placer le réseau (vous pouvez exécuter l’étape de préparation sans supprimer la passerelle). Une fois la migration terminée, reconnectez la passerelle dans le Gestionnaire de ressources Azure.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Étape 2 : Installer la dernière version de PowerShell d’Azure

Il existe deux options principales pour installer PowerShell d’Azure : [Galerie de PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) ou de [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). WebPI reçoit des mises à jour mensuelles. Galerie de PowerShell reçoit des mises à jour sur une base continue. Cet article est basé sur Azure PowerShell version 2.1.0.

Pour les instructions d’installation, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

<br>
## <a name="step-3-set-your-subscription-and-sign-up-for-migration"></a>Étape 3 : Définissez votre abonnement et inscription pour la migration

Tout d’abord, démarrez une invite de commandes PowerShell. Pour la migration, vous devez configurer votre environnement pour les deux classique et Gestionnaire de ressources.

Connectez-vous à votre compte pour le modèle de gestionnaire de ressources.

```powershell
    Login-AzureRmAccount
```

Obtenir les abonnements disponibles à l’aide de la commande suivante :

```powershell
    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName
```

Définissez votre abonnement Azure pour la session en cours. Cet exemple définit le nom d’abonnement par défaut à **Mon abonnement d’Azure**. Remplacez le nom d’abonnement exemple avec votre propre. 

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

>[AZURE.NOTE] L’inscription est une étape unique, mais vous devez le faire qu’une seule fois avant d’essayer de migration. Sans l’enregistrer, vous voyez le message d’erreur suivant : 

>   *BadRequest : Abonnement n’est pas inscrit pour la migration.* 

Inscrire avec le fournisseur de ressources de migration à l’aide de la commande suivante :

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Veuillez patienter cinq minutes pour l’enregistrement à la fin. Vous pouvez vérifier le statut de l’approbation à l’aide de la commande suivante :

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Assurez-vous que RegistrationState est `Registered` avant de continuer. 

Désormais vous connecter à votre compte pour le modèle classique.

```powershell
    Add-AzureAccount
```

Obtenir les abonnements disponibles à l’aide de la commande suivante :

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Définissez votre abonnement Azure pour la session en cours. Cet exemple définit l’abonnement par défaut à **Mon abonnement d’Azure**. Remplacez le nom d’abonnement exemple avec votre propre. 

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-4-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Étape 4 : Vérifiez que vous avez suffisamment noyaux de Machine virtuelle de gestionnaire de ressources Azure dans la région Azure de votre déploiement actuel ou de VNET

Vous pouvez utiliser la commande PowerShell suivante pour vérifier le nombre de cœurs que dans le Gestionnaire de ressources Azure. Pour en savoir plus sur les quotas de base, consultez [le Gestionnaire de ressources Azure et les limites](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager). 

Cet exemple vérifie la disponibilité de la région **Ouest des États-Unis** . Remplacez le nom de la région exemple avec votre propre. 

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Étape 5 : Exécution de commandes à faire migrer vos ressources IaaS

>[AZURE.NOTE] Toutes les opérations décrites ici sont idempotents. Si vous avez un problème autre qu’une fonction non prise en charge ou une erreur de configuration, nous vous recommandons de relancer la préparation, l’annulation ou opération de validation. La plateforme essaie ensuite l’action à nouveau.

### <a name="migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Migrer des machines virtuelles dans un service cloud (et non dans un réseau virtuel)

Obtenir la liste des services en nuage à l’aide de la commande suivante, puis sélectionnez le service en nuage que vous souhaitez faire migrer. Si les ordinateurs virtuels dans le service en nuage sont dans un réseau virtuel, ou si elles ont des rôles web ou travailleur, la commande renvoie un message d’erreur.

```powershell
    Get-AzureService | ft Servicename
```

Obtenir le nom de déploiement pour le service en nuage. Dans cet exemple, le nom du service est **Mon Service**. Remplacez le nom du service exemple avec votre propre nom de service. 

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Préparez les ordinateurs virtuels dans le service en nuage pour la migration. Vous avez le choix entre deux options.

* **Option 1. Migrer les ordinateurs virtuels vers un réseau virtuel créé par plate-forme**

    Tout d’abord valider si vous pouvez migrer le service en nuage à l’aide des commandes suivantes :

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    La commande ci-dessus affiche les avertissements et les erreurs qui empêchent la migration. Si la validation est réussie, puis vous pouvez passer à l’étape de **préparation** :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```

* **Option 2. Migrer vers un réseau virtuel existant dans le modèle de déploiement du Gestionnaire de ressources**

    Cet exemple définit le nom de groupe de ressource pour **myResourceGroup**, le nom de réseau virtuel à **myVirtualNetwork** et le nom du sous-réseau à **mySubNet**. Remplacez les noms dans l’exemple par les noms de vos propres ressources.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Tout d’abord valider si vous pouvez migrer le service en nuage à l’aide de la commande suivante :

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    La commande ci-dessus affiche les avertissements et les erreurs qui empêchent la migration. Si la validation est réussie, vous pouvez procéder à l’étape suivante de la préparation :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```
    

Après le succès de l’opération de préparation avec une des options précédentes, interroger l’état de la migration des ordinateurs virtuels. Assurez-vous qu’ils sont dans le `Prepared` état.

Cet exemple définit le nom de la machine virtuelle à **myVM**. Remplacez le nom de l’exemple avec votre propre nom de machine virtuelle.

    ```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
    ```

Vérifiez la configuration pour les ressources préparés à l’aide de PowerShell ou sur le portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante :

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Si la configuration préparée est bonne, vous pouvez les déplacer vers l’avant et valider les ressources à l’aide de la commande suivante :

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="migrate-virtual-machines-in-a-virtual-network"></a>Migrer des machines virtuelles dans un réseau virtuel

Pour migrer des machines virtuelles dans un réseau virtuel, vous migrez du réseau. Les ordinateurs virtuels migrer automatiquement avec le réseau. Sélectionnez le réseau virtuel que vous souhaitez faire migrer. 

Cet exemple définit le nom de réseau virtuel pour **myVnet**. Remplacez l’exemple de nom de réseau virtuel par les vôtres. 

```powershell
    $vnetName = "myVnet"
```

>[AZURE.NOTE] Si le réseau virtuel contient web ou rôles worker ou VM avec des configurations non prises en charge, vous obtenez un message d’erreur de validation.

Tout d’abord valider si vous pouvez migrer le réseau virtuel à l’aide de la commande suivante :

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

La commande ci-dessus affiche les avertissements et les erreurs qui empêchent la migration. Si la validation est réussie, vous pouvez procéder à l’étape suivante de la préparation :

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Vérifiez la configuration pour les ordinateurs virtuels préparées en utilisant Azure PowerShell ou l’Azure portal. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante :

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Si la configuration préparée est bonne, vous pouvez les déplacer vers l’avant et valider les ressources à l’aide de la commande suivante :

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="migrate-a-storage-account"></a>Migrer un compte de stockage

Une fois que vous avez fait migrer les ordinateurs virtuels, nous vous recommandons de migrer les comptes de stockage.

Préparer chaque compte de stockage pour la migration à l’aide de la commande suivante. Dans cet exemple, le nom de compte de stockage est **myStorageAccount**. Remplacez l’exemple de nom avec le nom de votre compte de stockage. 

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Vérifiez la configuration du compte de stockage préparée en utilisant Azure PowerShell ou l’Azure portal. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante :

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Si la configuration préparée est bonne, vous pouvez les déplacer vers l’avant et valider les ressources à l’aide de la commande suivante :

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la migration, consultez [migration de plate-forme prise en charge de ressources IaaS classique pour le Gestionnaire de ressources Azure](virtual-machines-windows-migration-classic-resource-manager.md).
- Pour migrer des ressources réseau supplémentaires au Gestionnaire de ressources à l’aide de PowerShell d’Azure, utilisez une procédure similaire avec [Move-AzureNetworkSecurityGroup](https://msdn.microsoft.com/library/mt786729.aspx), [Move-AzureReservedIP](https://msdn.microsoft.com/library/mt786752.aspx)et [AzureRouteTable-déplacer](https://msdn.microsoft.com/library/mt786718.aspx).
- Pour les scripts open source que vous permet de migrer des ressources Azure de classique pour le Gestionnaire de ressources, consultez [Outils de communauté pour la migration vers le Gestionnaire de ressources Azure migration](virtual-machines-windows-migration-scripts.md)
