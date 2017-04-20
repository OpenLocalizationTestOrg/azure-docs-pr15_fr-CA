<properties
    pageTitle="Règles de dénomination des infrastructures | Microsoft Azure"
    description="Obtenir des informations sur les directives de conception et d’implémentation clés d’affectation de noms dans les services d’infrastructure Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="infrastructure-naming-guidelines"></a>Instructions d’affectation de noms infrastructure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur la compréhension de l’approche des conventions d’affectation de noms pour toutes vos ressources Azure différentes générer un ensemble de ressources logique et facilement identifiable au sein de votre environnement.

## <a name="implementation-guidelines-for-naming-conventions"></a>Instructions d’implémentation pour les conventions d’affectation de noms

Décisions :

- Quels sont vos conventions d’affectation de noms pour les ressources Azure ?

Tâches :

- Définir les affixes de permet de maintenir la cohérence entre vos ressources.
- Définir des noms de compte de stockage étant donnés la nécessité pour les rendre globalement unique.
- Documenter la convention d’affectation de noms à utiliser et à distribuer à toutes les parties concernées garantir la cohérence entre les déploiements.

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Vous devez avoir une bonne convention d’affectation de noms en place avant de créer quoi que ce soit dans Azure. Une convention d’attribution de noms garantit que toutes les ressources ont un nom prévisible, qui contribue à réduire la charge administrative associée à la gestion de ces ressources.

Vous pouvez choisir de suivre un ensemble spécifique de conventions de dénomination définies pour votre organisation ou pour un compte ou un abonnement Azure spécifique. Bien qu’il soit facile pour les personnes au sein des organisations d’établir des règles implicites lorsque vous travaillez avec des ressources Azure, vous devez être capable de s’adapter aux équipes de travailler ensemble dans Azure.

Convenir d’un ensemble de conventions d’affectation de noms en amont. Il existe certaines considérations en ce qui concerne les conventions d’appellation couper les qui définit des règles.

## <a name="affixes"></a>Affixes

Coup de œil pour définir une convention d’affectation de noms, une décision est ou non l’apposer :

- Le début du nom (préfixe)
- La fin du nom (suffixe)

Par exemple, voici deux noms possibles pour un groupe de ressources à l’aide de la `rg` apposer :

- RG-WebApp (préfixe)
- WebApp-Rg (suffixe)

Affixes peuvent faire référence aux différents aspects qui décrivent les ressources. Le tableau suivant montre quelques exemples utilisés en général.

| Aspect                               | Exemples                                                               | Notes                                                                                                      |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Environnement                          | développement, stg, prod                                                         | En fonction de l’objectif et le nom de chaque environnement.                                                     |
| Emplacement                             | usw (Ouest américaine), utilisez (Extrême-Orient États-Unis 2)                                         | En fonction de la région du centre de données ou de la région de l’organisation.                               |
| Produit, service ou composant Azure | RG pour le groupe de ressources, VNet de réseau virtuel                        | Selon le produit pour lequel la ressource fournit la prise en charge.                                          |
| Rôle                                 | base de données, applications, web                                                           | Selon le rôle de l’ordinateur virtuel.                                                              |
| Instance                             | 01, 02, 03, etc..                                                       | Pour les ressources qui ont plus d’une instance. Par exemple, charge équilibrée de serveurs web dans un service cloud. |


Lors de l’établissement des conventions d’affectation de noms, assurez-vous qu’ils clairement les affixes à utiliser pour chaque type de ressource et dans quelle position (suffixe et de préfixe).

## <a name="dates"></a>Dates

Il est souvent important de déterminer la date de création à partir du nom d’une ressource. Nous vous recommandons du format de date AAAAMMJJ. Ce format permet de garantir que non seulement l’intégralité de la date est enregistrée, mais également que deux ressources dont les noms diffèrent uniquement sur la date sont triés par ordre alphabétique et par ordre chronologique.

## <a name="naming-resources"></a>Noms des ressources

Définissez chaque type de ressource dans la convention d’affectation de noms, qui doit avoir des règles qui définissent comment attribuer des noms à chaque ressource qui est créé. Ces règles doivent s’appliquer à tous les types de ressources, par exemple :

- Abonnements
- Comptes
- Comptes de stockage
- Réseaux virtuels
- Sous-réseaux
- Jeux de disponibilité
- Groupes de ressources
- Ordinateurs virtuels
- Points de terminaison
- Groupes de sécurité de réseau
- Rôles

Pour vous assurer que le nom fournit suffisamment d’informations pour déterminer à quelle ressource il fait référence, vous devez utiliser des noms descriptifs.

## <a name="computer-names"></a>Noms d’ordinateur

Lorsque vous créez une machine virtuelle (VM), Azure requiert un nom de machine virtuelle de 64 caractères au maximum qui est utilisé pour le nom de la ressource. Azure utilise le même nom pour le système d’exploitation installé sur la machine virtuelle. Cependant, ces noms ne peuvent pas toujours être le même.

Si un ordinateur virtuel est créé à partir d’un fichier d’image .vhd qui contient déjà un système d’exploitation, le nom de la machine virtuelle dans Azure peut différer de nom d’ordinateur de système d’exploitation de l’ordinateur. Cette situation peut ajouter un degré de difficulté pour la gestion des machines virtuelles, nous ne recommandons pas par conséquent. Affecter la ressource Azure VM le même nom que le nom de l’ordinateur que vous assignez au système d’exploitation de cet ordinateur virtuel.

Il est recommandé que le nom de la machine virtuelle d’Azure est le même que le nom d’ordinateur de système d’exploitation sous-jacent.

## <a name="storage-account-names"></a>Noms de compte de stockage

Comptes de stockage possèdent des règles spéciales régissant leurs noms. Vous ne pouvez utiliser que des minuscules et chiffres. Pour plus d’informations, voir [Création d’un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) . En outre, le nom de compte de stockage, avec core.windows.net, doit être un nom DNS valide globalement unique. Par exemple, si le compte de stockage est appelé mystorageaccount, les noms DNS suivants qui en résulte doivent être uniques :

- mystorageaccount.BLOB.Core.Windows.NET
- mystorageaccount.table.Core.Windows.NET
- mystorageaccount.Queue.Core.Windows.NET


## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 