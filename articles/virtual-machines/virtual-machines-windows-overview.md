<properties
    pageTitle="Vue d’ensemble des Machines virtuelles Windows | Microsoft Azure"
    description="Obtenir des informations sur la création et la gestion des ordinateurs virtuels Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="davidmu"/>

# <a name="overview-of-windows-virtual-machines-in-azure"></a>Vue d’ensemble des ordinateurs virtuels Windows Azure

Azure Machines virtuelles (VM) est un des nombreux types de [à la demande, des ressources informatiques et évolutives](../app-service-web/choose-web-site-cloud-service-vm.md) qui offre d’Azure. En général, vous choisissez un ordinateur virtuel lorsque vous avez besoin de davantage de contrôle sur l’environnement informatique de propose d’autres choix. Cet article vous donne des informations que vous devez prendre en compte avant de créer un ordinateur virtuel, comment vous le créez et la gestion.

Un Azure VM vous donne la flexibilité de la virtualisation sans avoir à acheter et à gérer le matériel physique qui l’exécute. Toutefois, vous devez mettre à jour la machine virtuelle en effectuant les tâches, telles que la configuration, application de correctifs et l’installation du logiciel qui s’exécute sur celle-ci.

Les machines virtuelles Azure peut être utilisés de différentes manières. Quelques exemples sont :

- **Test et développement** – offre Azure VM rapide et facile pour créer un ordinateur avec des configurations spécifiques requis pour le code et tester une application.
- **Applications dans le cloud** , car la demande de votre application peut fluctuer, il vaut peut-être mieux économique de l’exécuter sur une machine virtuelle dans Azure. Vous payez pour les machines virtuelles supplémentaires lorsque vous en avez besoin et les arrêtez lorsque vous n’avez pas.
- **Centre de données étendu** – les machines virtuelles dans un réseau virtuel Azure peut facilement être connecté au réseau de votre organisation.

Le nombre de machines virtuelles que votre application utilise peut évoluer d’entrée et de sortie, tout ce qui est nécessaire pour répondre à vos besoins.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Que dois-je réfléchir avant de créer une machine virtuelle ?

Il existe toujours une multitude [d’éléments de conception](virtual-machines-windows-infrastructure-virtual-machine-guidelines.md) lorsque vous générez une infrastructure d’application dans Azure. Ces aspects d’un ordinateur virtuel sont importants réfléchir avant de commencer :

- Les noms de vos ressources d’application
- L’emplacement dans lequel les ressources sont stockées.
- La taille de la machine virtuelle
- Le nombre maximum de machines virtuelles qui peuvent être créés.
- Le système d’exploitation qui s’exécute de la machine virtuelle
- La configuration de la machine virtuelle après son démarrage
- Les ressources associées qui a besoin de la machine virtuelle

### <a name="naming"></a>D’attribution de noms

Un ordinateur virtuel a un [nom](virtual-machines-windows-infrastructure-naming-guidelines.md) qui lui est assignée et qu’il a un nom d’ordinateur configuré en tant que partie du système d’exploitation. Le nom d’un ordinateur virtuel peut être jusqu'à 15 caractères.

Si vous utilisez Azure pour créer le disque du système d’exploitation, le nom de l’ordinateur et le nom de la machine virtuelle sont les mêmes. Si vous [Téléchargez et utiliser votre propre image](virtual-machines-windows-upload-image.md) qui contient un système d’exploitation configuré précédemment et l’utiliser pour créer un ordinateur virtuel, les noms peuvent être différents. Nous recommandons de lorsque vous téléchargez votre propre fichier de l’image, vous le nom d’ordinateur dans le système d’exploitation et le nom de la machine virtuelle identique.

### <a name="locations"></a>Emplacements

Toutes les ressources créées dans Azure sont réparties sur plusieurs [régions géographiques](https://azure.microsoft.com/regions/) du monde. En règle générale, la région est appelée **emplacement** lorsque vous créez un ordinateur virtuel. Pour un ordinateur virtuel, l’emplacement spécifie où les disques durs virtuels sont stockés.

Le tableau suivant répertorie quelques-unes des méthodes que vous pouvez obtenir la liste des emplacements disponibles.

| Méthode | Description |
| ------ | ----------- |
| Azure portal | Sélectionnez un emplacement dans la liste lorsque vous créez un ordinateur virtuel. |
| PowerShell Azure | Utilisez la commande [Get-AzureRmLocation](https://msdn.microsoft.com/library/mt619449.aspx) . |
| API REST | Utilisez les [emplacements de la liste](https://msdn.microsoft.com/library/dn790540.aspx) . |

### <a name="vm-size"></a>Taille de mémoire virtuelle

La [taille](virtual-machines-windows-sizes.md) de la machine virtuelle que vous utilisez est déterminée par la charge de travail que vous souhaitez exécuter. La taille que vous choisissez puis détermine les facteurs tels que l’alimentation, la mémoire et le stockage, la capacité de traitement. Azure offre une grande variété de tailles pour prendre en charge de nombreux types d’utilisations.

Les frais d’Azure un [prix horaire](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) en fonction de la taille et le système d’exploitation de la machine virtuelle. Heures partielles, Azure frais que pour les temps en minutes. Le stockage est tarifé et facturé séparément.

### <a name="vm-limits"></a>Limites de mémoire virtuelle

Votre abonnement a des [limites de quota](../azure-subscription-service-limits.md) par défaut en place qui pourrait avoir un impact sur le déploiement de machines virtuelles pour votre projet. La limite sur une base par abonnement est de 20 VM par région. Limites peuvent être portées en déposant un ticket de support demandant une augmentation.

### <a name="operating-system-disks-and-images"></a>Les images et les disques du système d’exploitation

Ordinateurs virtuels utilisent des [disques durs virtuels (VHD)](virtual-machines-windows-about-disks-vhds.md) pour stocker leurs données et système d’exploitation (OS). Disques durs virtuels sont également utilisés pour les images que vous pouvez choisir d’installer un système d’exploitation. 

Azure fournit des [images de marketplace](https://azure.microsoft.com/marketplace/virtual-machines/) à utiliser avec différentes versions et types de systèmes d’exploitation Windows Server. Images du marché sont identifiés par l’éditeur d’image, offre, sku et version (en général version est spécifiée en tant que plus tard). 

Ce tableau illustre certaines manières que vous pouvez trouver les informations d’une image.

| Méthode | Description |
| ------ | ----------- |
| Azure portal | Les valeurs sont spécifiés automatiquement pour vous, lorsque vous sélectionnez une image à utiliser. |
| PowerShell Azure | [Get-AzureRMVMImagePublisher](https://msdn.microsoft.com/library/mt603484.aspx) -emplacement « emplacement »<BR>[Get-AzureRMVMImageOffer](https://msdn.microsoft.com/library/mt603824.aspx) -emplacement « emplacement »-éditeur « éditeur »<BR>[Get-AzureRMVMImageSku](https://msdn.microsoft.com/library/mt619458.aspx) -emplacement « emplacement »-éditeur « éditeur »-offre des « offerName » |
| AUTRES API | [Éditeurs d’image de liste](https://msdn.microsoft.com/library/mt743702.aspx)<BR>[Offre de l’image de la liste](https://msdn.microsoft.com/library/mt743700.aspx)<BR>[Références SKU de liste image](https://msdn.microsoft.com/library/mt743701.aspx) |

Vous pouvez choisir de [télécharger et d’utiliser votre propre image](virtual-machines-windows-upload-image.md) et lorsque vous le faites, le nom de l’éditeur, l’offre et la référence ne sont pas utilisés.

### <a name="extensions"></a>Extensions

Machine virtuelle [extensions](virtual-machines-windows-extensions-features.md) accroître les fonctionnalités votre machine virtuelle via la configuration de déploiement de post et de tâches automatisées.

Ces tâches peuvent être effectuées à l’aide d’extensions :

- **Exécuter des scripts personnalisés** : l' [Extension de Script personnalisée](virtual-machines-windows-extensions-customscript.md) vous permet de configurer des charges de travail sur l’ordinateur virtuel en exécutant le script lorsque l’ordinateur virtuel est configuré.
- **Déployer et gérer les configurations** – l' [Extension de Configuration d’état souhaité (PowerShell DSC)](virtual-machines-windows-extensions-dsc-overview.md) vous permet de configurer de DSC sur une machine virtuelle pour gérer des environnements et des configurations.
- **Collecter des données de diagnostic** – l' [Extension de Diagnostics Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) permet de configurer l’ordinateur virtuel pour collecter des données de diagnostics qui peuvent être utilisées pour surveiller la santé de votre application.

### <a name="related-resources"></a>Ressources connexes

Les ressources de cette table sont utilisées par la machine virtuelle et doivent exister ou être créée lors de la création de la machine virtuelle.

| Ressources | Obligatoire | Description |
| -------- | -------- | ----------- |
| [Groupe de ressources](../azure-resource-manager/resource-group-overview.md) | Oui | La machine virtuelle doit être contenue dans un groupe de ressources. |
| [Compte de stockage](../storage/storage-create-storage-account.md) | Oui | La machine virtuelle doit être le compte de stockage pour stocker ses disques durs virtuels. |
| [Réseau virtuel](../virtual-network/virtual-networks-overview.md) | Oui | La machine virtuelle doit être un membre d’un réseau virtuel. |
| [Adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) | N° | La machine virtuelle peut avoir une adresse IP publique pour y accéder à distance. |
| [Interface réseau](../virtual-network/virtual-network-network-interface-overview.md) | Oui | La machine virtuelle a besoin de l’interface réseau pour communiquer sur le réseau. |
| [Disques de données](virtual-machines-windows-attach-disk-portal.md) | N° | La machine virtuelle peut inclure des disques de données pour développer les capacités de stockage. |

## <a name="how-do-i-create-my-first-vm"></a>Comment procéder pour créer mon premier VM ?

Vous avez plusieurs choix pour la création de votre machine virtuelle. Le choix que vous effectuez dépend de l’environnement. 

Ce tableau fournit des informations pour vous aider à démarrer la création de votre machine virtuelle.

| Méthode | Article |
| ------ | ------- |
| Azure portal | [Créer un ordinateur virtuel exécutant Windows à l’aide du portail](virtual-machines-windows-hero-tutorial.md) |
| Modèles | [Créer une machine virtuelle Windows avec un modèle de gestionnaire de ressources](virtual-machines-windows-ps-template.md) |
| PowerShell Azure | [Créer un ordinateur virtuel de Windows à l’aide de PowerShell](virtual-machines-windows-ps-create.md) |
| Kits de développement logiciel client | [Déployer des ressources Azure à l’aide de C#](virtual-machines-windows-csharp.md) |
| AUTRES API | [Créer ou mettre à jour d’une machine virtuelle](https://msdn.microsoft.com/library/mt163591.aspx) |

Vous espérez qu’il ne se produit jamais, mais il peut arriver que quelque chose se passe mal. Si cela est le cas, examinez les informations dans le [Gestionnaire de ressources de résolution des problèmes de déploiement avec la création d’une machine virtuelle de Windows dans Azure](virtual-machines-windows-troubleshoot-deployment-new-vm.md).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Comment gérer la machine virtuelle créée ?

Ordinateurs virtuels peuvent être gérés à l’aide d’un portail basé sur navigateur, les outils de ligne de commande avec prise en charge pour le script, ou directement via des API. Certaines tâches de gestion classiques que vous pouvez effectuer sont lors de l’obtention d’informations sur une machine virtuelle, ouvrir une session sur un ordinateur virtuel, gestion de la disponibilité et les sauvegardes.

### <a name="get-information-about-a-vm"></a>Obtenir des informations sur une machine virtuelle

Ce tableau montre quelques-unes des méthodes que vous pouvez obtenir des informations sur une machine virtuelle.

| Méthode | Description |
| ------ | ----------- |
| Azure portal | Dans le menu de concentrateur, cliquez sur **ordinateurs virtuels** , puis sélectionnez l’ordinateur virtuel dans la liste. Sur la lame de la machine virtuelle, vous avez accès aux informations de la vue d’ensemble, définition des valeurs et les mesures de surveillance. |
| PowerShell Azure | Pour plus d’informations sur l’utilisation de PowerShell pour gérer des ordinateurs virtuels, voir [Gérer les Machines d’Azure virtuelles à l’aide de gestionnaire de ressources et de PowerShell](virtual-machines-windows-ps-manage.md). |
| API REST | Pour obtenir des informations sur un ordinateur virtuel, utilisez l’opération [VM d’obtenir des informations](https://msdn.microsoft.com/library/mt163682.aspx) . |
| Kits de développement logiciel client | Pour plus d’informations sur l’utilisation de C# pour gérer des ordinateurs virtuels, voir [Gérer les Machines d’Azure virtuelles à l’aide de gestionnaire de ressources Azure et C#](virtual-machines-windows-csharp-manage.md). |

### <a name="log-on-to-the-vm"></a>Ouvrez une session sur l’ordinateur virtuel

Vous utilisez le bouton se connecter sur le portail Azure pour [Démarrer une session RDP (Remote Desktop)](virtual-machines-windows-connect-logon.md). Choses peuvent parfois se produire lorsque vous essayez d’utiliser une connexion à distance. Si cela est le cas, vérifiez les informations de l’aide dans [les connexions de résoudre les problèmes de bureau à distance à un ordinateur virtuel Azure exécutant Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

### <a name="manage-availability"></a>Gérer la disponibilité

Il est important de comprendre comment [garantir une haute disponibilité](virtual-machines-windows-manage-availability.md) pour votre application. Cette configuration implique la création de plusieurs ordinateurs virtuels pour s’assurer qu’au moins un est en cours d’exécution.

Pour votre déploiement bénéficier de notre contrat de niveau de Service VM 99.95, vous devez déployer deux ou plusieurs ordinateurs virtuels sont votre charge de travail à l’intérieur d’un [jeu de disponibilité](virtual-machines-windows-infrastructure-availability-sets-guidelines.md)en cours d’exécution. Cette configuration garantit vos ordinateurs virtuels sont répartis sur plusieurs domaines d’erreur et sont déployés sur les hôtes avec les fenêtres de maintenance. Le total [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explique la garantie de la disponibilité d’Azure dans son ensemble.
 
### <a name="back-up-the-vm"></a>Sauvegarder la machine virtuelle
 
Un [coffre-fort de Services de récupération](../backup/backup-introduction-to-azure-backup.md) est utilisé pour protéger des données et des ressources dans les services Azure sauvegarde et de récupération de Site Azure. Vous pouvez utiliser un coffre-fort de Services de récupération pour [déployer et gérer des sauvegardes pour les machines virtuelles de déployés par le Gestionnaire de ressources à l’aide de PowerShell](../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Étapes suivantes

- Si votre intention est de travailler avec les ordinateurs virtuels de Linux, consultez [Azure et Linux](virtual-machines-linux-azure-overview.md).
- Pour en savoir plus sur les règles autour de la configuration de votre infrastructure dans la [procédure pas à pas d’exemple Azure infrastructure](virtual-machines-windows-infrastructure-example.md).
- Assurez-vous de que suivre les [Meilleures pratiques pour l’exécution d’une machine virtuelle Windows sur Azure](virtual-machines-windows-guidance-compute-single-vm.md).


