<properties
    pageTitle="Migrer les ressources IaaS de classique pour le Gestionnaire de ressources Azure en utilisant l’infrastructure du langage commun Azure | Microsoft Azure"
    description="Cet article explique la migration de plate-forme prise en charge des ressources de classique pour le Gestionnaire de ressources Azure en utilisant l’infrastructure du langage commun Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrer les ressources IaaS de classique pour le Gestionnaire de ressources Azure en utilisant l’infrastructure du langage commun Azure

Ces étapes vous montrent comment utiliser des commandes Azure interface de ligne de commande (CLI) pour migrer l’infrastructure comme une ressource de service (IaaS) à partir du modèle de déploiement classique pour le modèle de déploiement du Gestionnaire de ressources Azure. L’article nécessite la [CLI d’Azure](../xplat-cli-install.md).

>[AZURE.NOTE] Toutes les opérations décrites ici sont idempotents. Si vous avez un problème autre qu’une fonction non prise en charge ou une erreur de configuration, nous vous recommandons de relancer la préparation, l’annulation ou opération de validation. La plate-forme sera puis réessayez.

## <a name="step-1-prepare-for-migration"></a>Étape 1 : Préparer la migration

Voici quelques-unes des meilleures pratiques recommandées lors de l’évaluation de la migration ressources IaaS de classique pour le Gestionnaire de ressources :

- Parcourez la [liste des fonctionnalités ou des configurations non prises en charge](virtual-machines-windows-migration-classic-resource-manager.md). Si vous avez des machines virtuelles qui utilisent des configurations non prises en charge ou fonctionnalités, nous vous recommandons d’attendre la prise en charge de la fonction/configuration à annoncer. Également, vous pouvez supprimer cette fonctionnalité ou sortir de cette configuration permettant la migration si elle répond à vos besoins.
-   Si vous avez automatisé scripts de déploiement de votre infrastructure et vos applications aujourd'hui, essayez de créer une configuration de test similaire à l’aide de ces scripts pour la migration. Également, vous pouvez définir des environnements de l’échantillon à l’aide du portail Azure.

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Étape 2 : Définir votre abonnement et inscrire le fournisseur

Pour les scénarios de migration, vous devez configurer votre environnement pour les deux classique et Gestionnaire de ressources. [Installer Azure CLI](../xplat-cli-install.md) et [Sélectionnez votre abonnement](../xplat-cli-connect.md).

Ouvrez une session dans votre compte.
    
    azure login

Sélectionnez l’abonnement Azure à l’aide de la commande suivante.

    azure account set "<azure-subscription-name>"

>[AZURE.NOTE] L’inscription est une étape de temps, mais il doit être effectué qu’une seule fois avant d’essayer de migration. Sans l’enregistrer, vous verrez le message d’erreur suivant 

>   *BadRequest : Abonnement n’est pas inscrit pour la migration.* 

Inscrire avec le fournisseur de ressources de migration à l’aide de la commande suivante. Notez que dans certains cas, cette commande arrive à expiration. Toutefois, l’enregistrement sera réussie.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Veuillez patienter cinq minutes pour l’enregistrement à la fin. Vous pouvez vérifier l’état de l’approbation à l’aide de la commande suivante. Assurez-vous que RegistrationState est `Registered` avant de continuer.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Passez maintenant en CLI pour le `asm` mode.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Étape 3 : Vérifiez que vous avez suffisamment noyaux de Machine virtuelle de gestionnaire de ressources Azure dans la région Azure de votre déploiement actuel ou de VNET

Pour cette étape, vous devrez basculer vers `arm` mode. Pour ce faire, utilisez la commande suivante.

```
azure config mode arm
```

Vous pouvez utiliser la commande CLI suivante pour vérifier la quantité de cœurs que dans le Gestionnaire de ressources Azure. Pour en savoir plus sur les quotas de base, reportez-vous à la section [limites et le Gestionnaire de ressources Azure](../articles/azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Une fois que vous avez terminé à la vérification de cette étape, vous pouvez basculer vers `asm` mode.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Étape 4 : Option 1 - migration de machines virtuelles dans un service cloud 

Obtenir la liste des services en nuage à l’aide de la commande suivante, puis sélectionnez le service en nuage que vous souhaitez faire migrer. Notez que si les ordinateurs virtuels dans le service en nuage sont dans un réseau virtuel, ou si elles ont des rôles web/worker, vous obtiendrez un message d’erreur.

    azure service list

Exécutez la commande suivante pour obtenir le nom de déploiement pour le service en nuage à partir de la sortie des commentaires. Dans la plupart des cas, le nom de déploiement est le même que le nom de service cloud.

    azure service show <serviceName> -vv

Préparez les ordinateurs virtuels dans le service en nuage pour la migration. Vous avez le choix entre deux options.

Si vous souhaitez migrer les ordinateurs virtuels vers un réseau virtuel créé par plate-forme, utilisez la commande suivante.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Si vous souhaitez migrer vers un réseau virtuel existant dans le modèle de déploiement du Gestionnaire de ressources, utilisez la commande suivante.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

Une fois l’opération de préparation réussie, vous pouvez consulter la sortie détaillée pour obtenir l’état de la migration des ordinateurs virtuels et assurez-vous qu’ils sont dans le `Prepared` état.

    azure vm show <vmName> -vv

Vérifiez la configuration pour les ressources préparés à l’aide de l’interface CLI ou l’Azure portal. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

    azure service deployment abort-migration <serviceName> <deploymentName>

Si la configuration préparée est bonne, vous pouvez les déplacer vers l’avant et valider les ressources à l’aide de la commande suivante.

    azure service deployment commit-migration <serviceName> <deploymentName>


    
## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Étape 4 : Option 2 - migration de machines virtuelles dans un réseau virtuel

Sélectionnez le réseau virtuel que vous souhaitez faire migrer. Notez que si le réseau virtuel contient les rôles web/worker ou VM avec des configurations non prises en charge, vous obtiendrez un message d’erreur de validation.

Obtenir tous les réseaux virtuels dans l’abonnement à l’aide de la commande suivante.

    azure network vnet list
    
La sortie doit ressembler à ceci :

![Capture d’écran de la ligne de commande avec le nom de l’ensemble du réseau virtuel mis en surbrillance.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Dans l’exemple ci-dessus, le **virtualNetworkName** est le nom complet **« Classicubuntu16 de classicubuntu16 de groupe »**.

Préparer le réseau virtuel de votre choix pour la migration à l’aide de la commande suivante.

    azure network vnet prepare-migration <virtualNetworkName>

Vérifiez la configuration pour les ordinateurs virtuels préparées à l’aide de l’interface CLI ou l’Azure portal. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

    azure network vnet abort-migration <virtualNetworkName>

Si la configuration préparée est bonne, vous pouvez les déplacer vers l’avant et valider les ressources à l’aide de la commande suivante.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Étape 5 : Migrer un compte de stockage

Une fois que vous avez fait migrer les ordinateurs virtuels, nous vous recommandons de migrer le compte de stockage.

Préparer la migration du compte de stockage à l’aide de la commande suivante

    azure storage account prepare-migration <storageAccountName>

Vérifiez la configuration du compte de stockage préparé à l’aide de l’interface CLI ou l’Azure portal. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

    azure storage account abort-migration <storageAccountName>

Si la configuration préparée est bonne, vous pouvez les déplacer vers l’avant et valider les ressources à l’aide de la commande suivante.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Étapes suivantes

- [Migration de plate-forme prise en charge de IaaS ressources classique pour le Gestionnaire de ressources](virtual-machines-windows-migration-classic-resource-manager.md)
- [Technique approfondie sur la migration de plate-forme prise en charge de classique pour le Gestionnaire de ressources](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
