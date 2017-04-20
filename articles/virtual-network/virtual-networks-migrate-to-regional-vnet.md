<properties 
   pageTitle="Comment migrer à partir de groupes d’affinité pour un réseau virtuel (VNet)"
   description="Apprenez à migrer à partir de groupes d’affinité pour vnets régionaux"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-migrate-from-affinity-groups-to-a-regional-virtual-network-vnet"></a>Comment migrer à partir de groupes d’affinité pour un réseau virtuel (VNet)

Vous pouvez utiliser un groupe d’affinité pour s’assurer que les ressources créées dans le même groupe d’affinité sont physiquement hébergés par des serveurs qui sont proches, l’activation de ces ressources communiquer plus rapidement. Dans le passé, les affinités ont été une condition requise pour la création de réseaux virtuels (VNets). À ce stade, le service de gestionnaire de réseau gérés VNets pourrait ne fonctionnent que dans un ensemble de serveurs physiques ou unité d’échelle. Améliorations architecturales ont augmenté de l’étendue de gestion de réseau à une région.

À la suite de ces améliorations architecturales, groupes d’affinité ne sont plus recommandés, ou requis pour les réseaux virtuels. L’utilisation de groupes d’affinité pour le VNets est remplacée par les régions. VNets qui sont associées à des régions sont appelées VNets régional.

En outre, nous vous recommandons de n’utiliser des affinités. Outre l’exigence VNet, groupes d’affinité ont été également importants à utiliser pour garantir des ressources, telles que le calcul et le stockage, ont été placés près de l’autre. Toutefois, avec l’architecture de réseau Azure actuelle, ces exigences de placement ne sont plus nécessaires. Voir [l’affinité de groupes et ordinateurs virtuels](#Affinity-groups-and-VMs) pour restants rares cas spécifiques dans lequel vous souhaitez utiliser un groupe d’affinité.

## <a name="creating-and-migrating-to-regional-vnets"></a>Création et migration vers VNets régionaux

À l’avenir, lors de la création de nouvelles VNets, utilisez la *région*. Vous verrez ceci en tant qu’option dans le portail de gestion. Notez que dans le fichier de configuration réseau, cela montre comme *emplacement*.

>[AZURE.IMPORTANT] Bien qu’il soit techniquement possible de créer un réseau virtuel est associé à un groupe d’affinité, il n’existe aucune bonne raison de le faire. De nombreuses nouvelles fonctionnalités, telles que les groupes de sécurité du réseau, ne sont disponibles que lors de l’utilisation d’un VNet régionaux et ne sont pas disponibles pour les réseaux virtuels associés à des groupes d’affinité.

### <a name="about-vnets-currently-associated-with-affinity-groups"></a>À propos de VNets actuellement associés aux groupes de l’affinité

VNets qui sont actuellement associés à des groupes d’affinité sont activées pour la migration vers VNets régional. Pour migrer vers un VNet régionaux, procédez comme suit :

1. Exporter le fichier de configuration réseau. Vous pouvez utiliser PowerShell ou le portail de gestion. Pour obtenir des instructions d’utilisation du portail de gestion, consultez [configurer votre VNet à l’aide d’un fichier de Configuration réseau](virtual-networks-using-network-configuration-file.md).

1. Modifier votre fichier de configuration réseau, remplaçant les anciennes valeurs avec les nouvelles valeurs. 

    > [AZURE.NOTE] L' **emplacement** est la région que vous avez spécifié pour le groupe d’affinité qui est associé à votre VNet. Par exemple, si votre VNet est associé à un groupe d’affinité qui se trouve dans les États-Unis Ouest, lors de la migration, l’emplacement doit pointer à l’ouest des États-Unis. 
    
    Modifiez les lignes suivantes dans votre fichier de configuration réseau, en remplaçant les valeurs avec votre propre : 

    **Ancienne valeur :** \<VirtualNetworkSitename = « VNetUSWest » AffinityGroup = « VNetDemoAG »\> 

    **Nouvelle valeur :** \<VirtualNetworkSitename = « VNetUSWest » Location = « US-ouest »\>

1. Enregistrez les modifications et les [Importer](virtual-networks-using-network-configuration-file.md) la configuration du réseau pour Azure.

>[AZURE.NOTE] Cette migration n’entraîne pas le temps d’indisponibilité à vos services.

## <a name="affinity-groups-and-vms"></a>Affinité de groupes et ordinateurs virtuels

Comme mentionné précédemment, les affinités sont n’est généralement plus recommandées pour les machines virtuelles. Vous devez utiliser un groupe d’affinité uniquement lorsqu’un ensemble de machines virtuelles doit avoir la latence plus bas absolu du réseau entre les ordinateurs virtuels. En plaçant des ordinateurs virtuels dans un groupe d’affinité, les ordinateurs virtuels sont tous effectués dans la même unité d’échelle ou de cluster de calcul.

Il est important de noter qu’à l’aide d’un groupe d’affinité peut disposer de deux, éventuellement négatif, conséquences :

- L’ensemble des tailles de machine virtuelle sera limitée à l’ensemble des tailles de machine virtuelle proposées par l’unité d’échelle de calcul.

- Il existe une probabilité de ne pas pouvoir allouer une nouvelle machine virtuelle. Cela se produit lorsque l’unité d’échelle spécifique pour le groupe d’affinité est capacité.

### <a name="what-to-do-if-you-have-a-vm-in-an-affinity-group"></a>Que faire si vous avez un ordinateur virtuel dans un groupe d’affinité

Ordinateurs virtuels qui sont actuellement dans un groupe d’affinité n’avez pas besoin d’être supprimé de l’affinité du groupe.

Une fois qu’un ordinateur virtuel est déployé, il est déployé dans une unité d’échelle unique. Affinités peut limiter le jeu de tailles de machine virtuelle disponibles pour un nouveau déploiement de machine virtuelle, mais tout ordinateur virtuel existant qui est déployé est déjà limité au jeu de tailles de mémoire virtuelle disponibles dans l’unité d’échelle dans lequel l’ordinateur virtuel est déployé. C’est pourquoi, la suppression d’un ordinateur virtuel à partir du groupe d’affinité n’ont aucun effet.
 
