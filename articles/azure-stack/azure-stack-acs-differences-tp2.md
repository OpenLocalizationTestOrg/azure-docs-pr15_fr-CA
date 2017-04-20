
<properties
    pageTitle="Stockage Azure cohérents : différences et considérations | Microsoft Azure"
    description="Comprendre les différences de stockage Azure et d’autres considérations de déploiement de stockage Azure cohérents."
    services="azure-stack"
    documentationCenter=""
    authors="MChadalapaka"
    manager="siroy"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="mchad"/>

# <a name="azure-consistent-storage-differences-and-considerations"></a>Stockage Azure cohérents : différences et considérations

Stockage Azure-cohérente est un ensemble de services de cloud de Microsoft Azure pile stockage. Le stockage Azure cohérentes fournit des blob, table, file d’attente et des fonctionnalités de gestion de compte avec une sémantique cohérente-Azure. Cet article résume les différences de stockage Azure cohérent connus avec le stockage Azure. Il présente également les autres considérations à garder à l’esprit lorsque vous déployez la version actuellement accessibles d’aperçu de Microsoft Azure pile.

<span id="Concepts" class="anchor"><span id="_Toc386544169" class="anchor"><span id="_Toc389466742" class="anchor"><span id="_Ref428966996" class="anchor"><span id="_Toc433223853" class="anchor"></span></span></span></span></span>
## <a name="known-differences"></a>Différences connues

Cette version d’aperçu technique de stockage Azure cohérent n’a pas de parité des fonctionnalités 100 % avec le stockage Azure pour les versions de l’API qui sont pris en charge. Connus des différences de fonctionnalité incluent les éléments suivants :

-   Certains types de compte de stockage ne sont pas encore disponibles, par exemple, la norme\_RAGRS et la norme\_GRS.

-   Prime\_des comptes de stockage LRS peuvent être mis en service. Toutefois, il n’existe actuellement aucune des limites de performances ou de garanties.

-   La fonctionnalité fichiers Azure n’est pas encore disponible.

-   L’API de plages obtenir de Page ne gère pas l’extraction de pages qui diffèrent entre deux instantanés du BLOB de la page.

-   L’API de plages obtenir de Page renvoie des pages de 4 Ko de granularité.

-   Clé de partition et la clé de ligne dans la mise en oeuvre du tableau de stockage Azure cohérentes sont limités à 400 caractères (c'est-à-dire, 800 octets).

-   Noms de BLOB dans l’implémentation du service stockage Blob Azure cohérentes sont limités à 880 caractères (c'est-à-dire, 1760 octets).

-   L’implémentation de stockage Azure cohérentes de clients stockage l’utilisation des rapports de données fournit des compteurs de l’utilisation de stockage qui sont identiques à ceux dans Azure, avec les mêmes sémantique et unités. Actuellement, cependant, la jauge d’utilisation de Transactions de stockage n’inclut pas les transactions IaaS, et la jauge d’utilisation de transfert de données ne différencie pas la bande passante utilisée par le trafic réseau interne ou externe à une région de la pile d’Azure.

-   Il existe certaines différences dans la portée des fonctionnalités pour la gestion du stockage. Par exemple, vous ne peut pas modifier le type de compte ou posséder des domaines personnalisés. En outre, seulement la cohérence du niveau API est proposée pour la prime\_type de compte de stockage LRS.

## <a name="deployment-considerations"></a>Considérations relatives au déploiement

-   **Test uniquement.** Ne déployez pas le stockage Azure-cohérente dans les environnements de production qui utilisent la version bêta de Microsoft Azure pile actuelle. Cette version est destinée uniquement à des fins d’évaluation dans un environnement de test.

-   **Performances**. La version bêta de Microsoft Azure pile de stockage Azure cohérentes n’est pas totalement à performances optimisées.

-   **Évolutivité.** La version bêta de Microsoft Azure pile de stockage Azure cohérentes n’est pas optimisée pour l’évolutivité.

## <a name="version-support-considerations"></a>Considérations relatives à la version prise en charge

Les versions suivantes sont prises en charge avec cette version d’évaluation du stockage Azure cohérents :

> Bibliothèque Client de stockage Azure : [Kit de développement .NET Microsoft Azure stockage 6.x](http://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)
>
> Services de données de stockage Azure : [version des API REST 2015-04-05](https://msdn.microsoft.com/library/azure/mt705637.aspx)
>
> Services de gestion du stockage Azure : [2015-05-01-aperçu](https://msdn.microsoft.com/library/azure/mt163683.aspx)
> [2015-06-15](https://msdn.microsoft.com/library/azure/mt163683.aspx)
## <a name="next-steps"></a>Étapes suivantes

-   [Introduction au stockage Azure-cohérent](azure-stack-storage-overview.md)
