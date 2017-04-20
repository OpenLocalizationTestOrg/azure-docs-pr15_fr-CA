<properties
 pageTitle="Calculer les scores de banc d’essai pour les machines virtuelles de Windows | Microsoft Azure"
 description="Comparer les scores des tests de performances SPECint compute pour les machines virtuelles d’Azure exécutant Windows Server"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="cynthn"/>

# <a name="compute-benchmark-scores-for-windows-vms"></a>Calculer les scores de banc d’essai pour les machines virtuelles de Windows

Les scores de banc d’essai de performances SPECInt suivants indiquent les performances de calcul pour la gamme de machine virtuelle hautes performances d’Azure exécutant Windows Server. Calculer les scores de banc d’essai sont également disponibles pour les [Machines virtuelles de Linux](virtual-machines-linux-compute-benchmark-scores.md).



## <a name="a-series---compute-intensive"></a>A-series-calcul intensif


Taille | vCPUs | Nœuds NUMA | UNITÉ CENTRALE | S’exécute | Taux de base moy. | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_A8 | 8 | 1 | Intel Xeon processeur E5-2670 0 @ 2,6 GHz | 10 | 236.1 | 1.1
Standard_A9 | 16 | 2 | Intel Xeon processeur E5-2670 0 @ 2,6 GHz | 10 | 450.3 | 7.0
Standard_A10 | 8 | 1 | Intel Xeon processeur E5-2670 0 @ 2,6 GHz | 5 | 235.6 | 0,9
Standard_A11 | 16 | 2 | Intel Xeon processeur E5-2670 0 @ 2,6 GHz |7 | 454.7 | 4.8.

## <a name="dv2-series"></a>Série Dv2


Taille | vCPUs | Nœuds NUMA | UNITÉ CENTRALE | S’exécute | Taux de base moy. | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_D1_v2 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 83 | 36,6 | 2.6
Standard_D2_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 27 | 70.0 | 3.7
Standard_D3_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 19 | 130.5 | 4.4
Standard_D4_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 19 | 238.1 | 5.2
Standard_D5_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 14 | 460.9 | 15,4 pouces
Standard_D11_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 19 | 70,1 | 3.7
Standard_D12_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 2 | 132.0 | 1.4
Standard_D13_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 17 | 235.8 | 3.8
Standard_D14_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 15 | 460.8 | 6.5


## <a name="g-series-gs-series"></a>G-series, série GS


Taille | vCPUs | Nœuds NUMA | UNITÉ CENTRALE | S’exécute | Taux de base moy. | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_G1, Standard_GS1  | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 31 | 71.8 | 6.5
Standard_G2, Standard_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 5 | 133.4 | 13.0
Standard_G3, Standard_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 6 | 242.3 | 6.0
Standard_G4, Standard_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 15 | 398.9 | 6.0
Standard_G5, Standard_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 22 | 762.8 | 3.7

## <a name="h-series"></a>H-series

Taille | vCPUs | Nœuds NUMA | UNITÉ CENTRALE | S’exécute | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_H8 | 8 | 1 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 5 | 297.4 | 0,9
Standard_H16 | 16 | 2 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 5 | 575.8 | 6.8
Standard_H8m | 8 | 1 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 5 | 297.0 | 1.2
Standard_H16m | 16 | 2 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 5 | 572.2 | 3.9
Standard_H16r | 16 | 2 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 5 | 573.2 | 2.9
Standard_H16mr | 16 | 2 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 7 | 569.6 | 2.8

## <a name="about-specint"></a>À propos de performances SPECint

Numéros de Windows ont été calculés en exécutant [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) sur Windows Server. SPECint a été exécuté à l’aide de l’option de taux de base (SPECint_rate2006), avec une seule copie par cœur. SPECint se compose de 12 tests séparés, chacun exécuté trois fois, la valeur médiane de chaque test et pondération pour former un score composite. Ces tests ont été exécutées sur plusieurs ordinateurs virtuels pour fournir les notes moyennes indiqués.


## <a name="next-steps"></a>Étapes suivantes

* Pour les capacités de stockage, les détails de disque et d’autres considérations pour choisir parmi les tailles de la machine virtuelle, voir [tailles pour les machines virtuelles](virtual-machines-windows-sizes.md).
