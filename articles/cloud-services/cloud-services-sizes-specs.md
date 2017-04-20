<properties
 pageTitle="Tailles des services en nuage | Microsoft Azure"
 description="Répertorie les tailles de machine virtuelle différente (et) pour les rôles web et worker du service cloud Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/27/2016"
 ms.author="adegeo"/>

# <a name="sizes-for-cloud-services"></a>Tailles des Services en nuage

Cette rubrique décrit les options pour les instances de rôle de Service Cloud (rôles web et rôles worker) et tailles disponibles. Il fournit également des considérations relatives au déploiement pour être informé lors de la planification à utiliser ces ressources. Chaque taille a un ID que vous allez placer dans votre [fichier de définition de service](cloud-services-model-and-package.md#csdef).

Les Services en nuage est un des nombreux types de ressources de calcul offertes par Azure. Cliquez [ici](cloud-services-choose-me.md) pour plus d’informations sur les Services en nuage.

> [AZURE.NOTE]Pour voir les limites d’Azure connexes, consultez [abonnement Azure et les limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md)

## <a name="sizes-for-web-and-worker-role-instances"></a>Tailles des instances de rôles web et worker

Il existe plusieurs formats standard pour choisir à partir de dans Azure. Considérations pour certains de ces tailles sont les suivantes :

* Machines virtuelles de D-series sont conçues pour exécuter des applications qui nécessitent une puissance de calcul supérieure et des performances de disque temporaire. Machines virtuelles de la série D offrent des processeurs plus rapides, un taux plus élevé de la mémoire vers le centre et un lecteur à l’état solide (SSD) pour le disque temporaire. Pour plus d’informations, consultez l’annonce sur le blog d’Azure, [Nouvelles tailles de Machine virtuelle de série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

* Dv2-series, un successeur de la série D d’origine, doté d’un processeur plus puissant. Le processeur de la série Dv2 est d’environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell), processeur et avec la technologie 2.0 Intel Turbo Boost, peut aller jusqu'à 3.1 GHz. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

*   Machines virtuelles de G-series offrent le plus de mémoire et s’exécutent sur des hôtes qui sont dotés de processeurs Intel Xeon E5 V3 de la gamme.

*   Les ordinateurs virtuels d’une série peuvent être déployés sur différents types de matériels et de processeurs. La taille est limitée, en fonction du matériel, pour offrir des performances du processeur cohérente pour l’instance en cours d’exécution, quel que soit le matériel qu’il est déployé sur. Pour déterminer le matériel physique sur lequel cette taille est déployée, interrogez le matériel virtuel de la machine virtuelle.

*   La taille de A0 est trop sollicitée sur le matériel physique. Cette taille spécifiques, autres déploiements de client peuvent avoir un impact sur les performances de votre charge de travail en cours d’exécution. La performance relative est décrite ci-dessous comme référence attendue, sous réserve d’une variabilité approximative de 15 pour cent.


La taille de la machine virtuelle a une incidence sur la tarification. La taille affecte également la capacité de traitement, de mémoire et de stockage de l’ordinateur virtuel. Les coûts de stockage sont calculés séparément en fonction des pages utilisées dans le compte de stockage. Pour plus d’informations, consultez les [Détails de la tarification des Machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/) et les [Tarifs de stockage Azure](https://azure.microsoft.com/pricing/details/storage/). 


Les considérations suivantes peuvent vous aider à vous décider sur une taille :


* Les tailles A8-A11 et H de la série sont également appelés *instances de calcul intensif*. Le matériel qui exécute ces tailles est conçu et optimisé pour calcul intensif et applications gourmandes en réseau, notamment l’informatique hautes performances (HPC) cluster des applications, la modélisation et simulations. La série A8-A11 utilise Intel Xeon E5-2670 @ 2,6 GHZ et la série H utilise Intel Xeon E5-2667 v3 @ 3,2 GHz. Pour obtenir des informations détaillées et des considérations sur l’utilisation de ces tailles, voir à [propos des ordinateurs virtuels A-série H-series et de calcul intensif](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md). 

* Dv2-series, série D, G-series, sont idéales pour les applications qui requièrent des processeurs plus rapides, local de meilleur performances de disque ou ont des exigences de mémoire supérieures.  Ils offrent une combinaison puissante pour de nombreuses applications d’entreprise.

*   Certains hôtes physiques des centres de données Azure ne peuvent pas prennent en charge ordinateur virtuel plus volumineux, tels que A5 – A11. Par conséquent, vous pouvez voir le message d’erreur **Impossible de configurer l’ordinateur virtuel {nom de l’ordinateur}** ou **Création d’ordinateur virtuel {nom de machine} a échoué** lors du redimensionnement d’un ordinateur virtuel existant à une nouvelle taille ; Création d’un nouvel ordinateur virtuel dans un réseau virtuel est créé avant le 16 avril 2013 ; ou l’ajout d’un nouvel ordinateur virtuel à un service cloud existant. Consultez [erreur : « Impossible de configurer l’ordinateur virtuel »](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) sur le forum de support pour les solutions de contournement pour chaque scénario de déploiement.  

* Votre abonnement peut également limiter le nombre de cœurs, que vous pouvez déployer de certaines familles de taille. Pour augmenter le quota, contactez le support technique d’Azure.


## <a name="performance-considerations"></a>Considérations sur les performances

Nous avons créé le concept d’Azure Compute unité (ACU) pour fournir une possibilité de comparer les performances de calcul (CPU) sur Azure SKU. Ceci vous aidera à identifier facilement SKU est susceptible de répondre à vos besoins de performances.  ACU est actuellement normalisé sur un petit (Standard_A1) VM en cours puis de 100 et tous les autres points de stock représente environ comment beaucoup plus rapidement que SKU peut exécuter un banc d’essai standard. 

>[AZURE.IMPORTANT] L’ACU n'est qu’une indication.  Les résultats de votre charge de travail peuvent varier. 

<br>

|Famille de références SKU |ACU/cœur |
|---|---|
|[Standard_A0](#a-series)   |50 |
|[Standard_A1-4](#a-series) |100 |
|[Standard_A5-7](#a-series) |100 |
|[A8-A11](#a-series)    |225 *|
|[D1-14](#d-series) |160 |
|[D1-15v2](#dv2-series) |210 - 250 *|
|[G1-5](#g-series)  |180 - 240 *|
|[H](#h-series) |290 - 300 *|

ACUs marqués avec une * la technologie Intel® Turbo permet d’augmenter la fréquence du processeur et de fournir une amélioration des performances.  Le montant de l’augmentation peut varier en fonction de la taille de la mémoire virtuelle, la charge de travail et autres charges de travail en cours d’exécution sur le même hôte.

## <a name="size-tables"></a>Dimensionner les tables

Les tableaux suivants indiquent les tailles et les capacités qu’ils fournissent.

* Capacité de stockage est indiquée en unités de talon ou 1024 ^ 3 octets. Lorsque la comparaison des disques exprimée en Go (1000 ^ 3 octets) vers des disques mesurées dans le talon (1024 ^ 3) Gardez à l’esprit que les chiffres de capacité dans le talon peut apparaissent plus petits. Par exemple, le talon de 1023 = 1098.4 Go

* Débit de disque est mesuré dans les opérations d’entrées/sorties par seconde (IOPS) et Mbits/s où Mbits/s = 10 ^ 6 octets/s.

* Disques de données peuvent fonctionner en mode mis en cache ou non mis en cache. Pour l’opération de disque de données mises en cache, le mode de cache hôte a la valeur **ReadOnly** ou **ReadWrite**.  Pour disque de données non mis en cache, le mode de cache hôte est défini sur **Aucun**.

* Bande passante réseau maximale est la synthèse la bande passante maximale allouée et attribuée par type de machine virtuelle. La bande passante maximale fournit des indications pour sélectionner le bon type de machine virtuelle pour assurer une capacité réseau adéquate sont disponible. Lors d’un déplacement entre faible, moyenne, haute et très haute, le débit augmente en conséquence. Les performances réseau dépend de plusieurs facteurs, notamment le réseau et les charges d’application et les paramètres réseau de l’application.


## <a name="a-series"></a>A-series

| Taille        | Coeurs de processeur | Mémoire : le talon | Disque dur local : le talon | Disques de données max | Débit de disque de données max : Ops ES/s | Cartes d’interface réseau max / la bande passante réseau |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A0 | 1         | 0.768        | 20                    | 1              | 1 x 500              | 1 / faible                   |
| Standard_A1 | 1         | 1,75         | 70                    | 2              | 2 x 500              | 1 / modéré              |
| Standard_A2 | 2         | 3,5 GO       | 135                   | 4              | 4 x 500              | 1 / modéré              |
| Standard_A3 | 4         | 7            | 285                   | 8              | 8 x 500              | 2 / haute                  |
| Standard_A4 | 8         | 14           | 605                   | 16             | 16 x 500             | 4 / haute                  |
| Standard_A5 | 2         | 14           | 135                   | 4              | 4 X 500              | 1 / modéré              |
| Standard_A6 | 4         | 28           | 285                   | 8              | 8 x 500              | 2 / haute                  |
| Standard_A7 | 8         | 56           | 605                   | 16             | 16 x 500             | 4 / haute                  |

## <a name="a-series---compute-intensive-instances"></a>A-series - instances de calcul intensif

Pour des informations et des considérations sur l’utilisation de ces tailles, voir à [propos des ordinateurs virtuels A-série H-series et de calcul intensif](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


| Taille         | Coeurs de processeur | Mémoire : le talon | Disque dur local : le talon | Disques de données max | Débit de disque de données max : Ops ES/s | Cartes d’interface réseau max / la bande passante réseau |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A8 * | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / haute                  |
| Standard_A9 * | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / très haute             |
| Standard_A10 | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / haute                  |
| Standard_A11 | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / très haute             |

* RDMA capable

## <a name="d-series"></a>Série D


| Taille         | Coeurs de processeur | Mémoire : le talon | SSD local : le talon | Disques de données max | Débit de disque de données max : Ops ES/s | Cartes d’interface réseau max / la bande passante réseau |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / modéré              |
| Standard_D2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / haute                  |
| Standard_D3  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / haute                  |
| Standard_D4  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / haute                  |
| Standard_D11 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / haute                  |
| Standard_D12 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / haute                  |
| Standard_D13 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / haute                  |
| Standard_D14 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / très haute             |

## <a name="dv2-series"></a>Série Dv2

| Taille            | Coeurs de processeur | Mémoire : le talon | SSD local : le talon | Disques de données max | Débit de disque de données max : Ops ES/s | Cartes d’interface réseau max / la bande passante réseau |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1_v2  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / modéré              |
| Standard_D2_v2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / haute                  |
| Standard_D3_v2  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / haute                  |
| Standard_D4_v2  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / haute                  |
| Standard_D5_v2  | 16        | 56           | 800                  | 32             | 32 x 500             | 8 / extrêmement élevée        |
| Standard_D11_v2 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / haute                  |
| Standard_D12_v2 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / haute                  |
| Standard_D13_v2 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / haute                  |
| Standard_D14_v2 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / extrêmement élevée        |
| Standard_D15_v2 | 20        | 140          | 1 000                | 40             | 40 x 500             | 8 / extrêmement élevée        |

## <a name="g-series"></a>G-series

| Taille        | Coeurs de processeur | Mémoire : le talon  | SSD local : le talon  | Disques de données max | Débit de disque maximal : Ops ES/s | Cartes d’interface réseau max / la bande passante réseau |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_G1 | 2         | 28           | 384                  | 4              | 4 x 500            | 1 / haute                  |
| Standard_G2 | 4         | 56           | 768                  | 8              | 8 x 500            | 2 / haute                  |
| Standard_G3 | 8         | 112          | 1,536                | 16             | 16 x 500           | 4 / très haute             |
| Standard_G4 | 16        | 224          | 3 072                | 32             | 32 x 500           | 8 / extrêmement élevée        |
| Standard_G5 | 32        | 448          | 6,144                | 64             | 64 x 500           | 8 / extrêmement élevée        |


## <a name="h-series"></a>H-series

Azure machines virtuelles de la série H sont la prochaine génération informatique hautes performances que VMS visant à des besoins informatiques de haut de gamme, telles que la modélisation moléculaire et fluides informatisée. Ces 8 et 16 core VMs sont basées sur la technologie de processeur Intel Haswell E5-2667 V3 DDR4 de mémoire et de stockage local des SSD en fonction. 

En plus de la puissance du processeur substantielle, la série H propose diverses options pour le réseau de RDMA faible temps de latence à l’aide de FDR InfiniBand et plusieurs configurations de mémoire pour prendre en charge les exigences de calcul gourmandes en mémoire.


| Taille           | Coeurs de processeur | Mémoire : le talon | SSD local : le talon | Disques de données max | Débit de disque maximal : Ops ES/s | Cartes d’interface réseau max / la bande passante réseau |
|----------------|-----------|-------------|--------------------------|----------------|---------------------------|------------------------------|
| Standard_H8    | 8         | 56          | 1000                     | 16             | 16 x 500                    | 8 / haute                      |
| Standard_H16   | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / très haute                  |
| Standard_H8m   | 8         | 112         | 1000                     | 16             | 16 x 500                    | 8 / haute                      |
| Standard_H16m  | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / très haute                 |
| Standard_H16r * | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / très haute                  |
| Standard_H16mr * | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / très haute                  |


* RDMA capable

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Notes : Standard A0 - A4 à l’aide de l’interface CLI et PowerShell 

Dans le modèle de déploiement classique, certains noms de taille de mémoire virtuelle sont légèrement différentes dans l’infrastructure du langage commun et PowerShell :

* Standard_A0 est ExtraSmall 
* Standard_A1 est le petit
* Standard_A2 est défini sur moyen
* Standard_A3 est de grande taille
* Standard_A4 est ExtraLarge

## <a name="configure-sizes-for-cloud-services"></a>Configurer les tailles des Services en nuage

Vous pouvez spécifier la taille de la Machine virtuelle d’une instance de rôle dans le cadre du modèle de service décrit par le [fichier de définition de service](cloud-services-model-and-package.md#csdef). La taille du rôle détermine le nombre de cœurs du processeur, la capacité de la mémoire et la taille du système de fichiers local qui est affectée à une instance en cours d’exécution. Choisissez la taille de rôle en fonction de la spécification de ressources de votre application.

Voici un exemple de définition de la taille de rôle pour une instance de rôle Web [Standard_D2](#general-purpose-d) :

```xml
<WorkerRole name="Worker1" vmsize="<mark>Standard_D2</mark>">
...
</WorkerRole>
```

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur [l’abonnement azure et les limites de service, les quotas et les contraintes](../azure-subscription-service-limits.md).
- En savoir plus [sur les ordinateurs virtuels une série de H-series et de calcul intensif](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) pour les charges de travail à haute performance Computing (HPC).

