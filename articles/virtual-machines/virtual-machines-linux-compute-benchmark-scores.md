<properties
 pageTitle="Calculer les scores de banc d’essai pour les machines virtuelles de Linux | Microsoft Azure"
 description="Comparer les scores de banc d’essai de calcul CoreMark pour les machines virtuelles d’Azure sous Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="cynthn"/>

# <a name="compute-benchmark-scores-for-linux-vms"></a>Calculer les scores de banc d’essai pour les machines virtuelles de Linux

Les scores de banc d’essai suivants CoreMark affichent les performances de calcul pour la gamme de machine virtuelle hautes performances d’Azure exécutant Ubuntu. Calculer les scores de banc d’essai sont également disponibles pour les [Machines virtuelles de Windows](virtual-machines-windows-compute-benchmark-scores.md).




## <a name="a-series---compute-intensive"></a>A-series-calcul intensif


Taille | vCPUs | Nœuds NUMA | UNITÉ CENTRALE | S’exécute | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_A8 | 8 | 1 | Intel Xeon processeur E5-2670 0 @ 2,6 GHz| 179 | 110,294 | 554
Standard_A9 | 16 | 2 | Intel Xeon processeur E5-2670 0 @ 2,6 GHz| 189 | 210,816| 2,126
Standard_A10 | 8 | 1 | Intel Xeon processeur E5-2670 0 @ 2,6 GHz| 188 | 110,025 | 1,045
Standard_A11 | 16 | 2 | Intel Xeon processeur E5-2670 0 @ 2,6 GHz| 188 | 210,727| 2,073

## <a name="dv2-series"></a>Série Dv2


Taille | vCPUs | Nœuds NUMA | UNITÉ CENTRALE | S’exécute | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_D1_v2 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 140 | 14,852 | 780
Standard_D2_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 133 | 29,467 | 1,863
Standard_D3_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 139 | 56,205 | 1,167
Standard_D4_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 126 | 108,543 | 3,446
Standard_D5_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 126 | 205,332 | 9,998
Standard_D11_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 125 | 28,598 | 1,510
Standard_D12_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 131 | 55,673 | 1,418
Standard_D13_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 140 | 107,986 | 3,089
Standard_D14_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 140 | 208,186 | 8,839
Standard_D15_v2 | 20 | 2 | Intel Xeon E5-2673 v3 @ 2,4 GHz |28 | 268,560 | 4,667

## <a name="f-series"></a>F-series

Taille | vCPUs | Nœuds NUMA | UNITÉ CENTRALE | S’exécute | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_F1 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 154 | 15,602 | 787
Standard_F2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 126 | 29,519 | 1,233
Standard_F4 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 147 | 58,709 | 1,227
Standard_F8 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 224 | 112,772 | 3,006
Standard_F16 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2,4 GHz | 42 | 218,571 | 5,113



## <a name="g-series"></a>G-series


Taille | vCPUs | Nœuds NUMA | UNITÉ CENTRALE | S’exécute | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_G1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 31,310 | 2,891
Standard_G2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 60,112 | 3,537
Standard_G3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 107,522 | 4,537
Standard_G4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 195,116 | 5,024
Standard_G5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 360,329 | 14,212

## <a name="gs-series"></a>GS-series


Taille | vCPUs | Nœuds NUMA | UNITÉ CENTRALE | S’exécute | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 28,613 | 1,884
Standard_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 54,348 | 3,474
Standard_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 104,564 | 1,834
Standard_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 194,111 | 4,735
Standard_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 357,396 | 16,228


## <a name="h-series"></a>H-series

Taille | vCPUs | Nœuds NUMA | UNITÉ CENTRALE | S’exécute | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_H8 | 8 | 1 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 28 | 140,782 | 2,512
Standard_H16 | 16 | 2 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 35 | 275,289 | 7,110 
Standard_H18m | 8 | 1 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 28 | 139,071 | 3,988 
Standard_H16m | 16 | 2 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 28 | 275,988 | 6,963 
Standard_H16r | 16 | 2 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 28 | 273,982 | 6,069 
Standard_H16mr | 16 | 2 | Intel Xeon E5-2667 v3 @ 3,2 GHz | 28 | 274,523 | 5 698 



## <a name="about-coremark"></a>À propos de CoreMark

Numéros de Linux ont été calculés en exécutant [CoreMark](http://www.eembc.org/coremark/faq.php) sur Ubuntu. CoreMark a été configuré avec le nombre de threads par le nombre de processeurs virtuels et concurrence d’accès définie sur PThreads. Le nombre de cibles d’itérations a été ajusté en fonction des performances attendues de fournir un runtime de moins de 20 secondes (généralement beaucoup plus de temps). Le score final représente le nombre d’itérations terminées, divisé par le nombre de secondes que nécessaire pour exécuter le test. Chaque test a été exécuté au moins sept fois sur chaque ordinateur virtuel. Tests (sauf pour H-series_ ont été exécutés en octobre 2015 sur plusieurs ordinateurs virtuels dans chaque région publique Azure la machine virtuelle a été pris en charge dans la date d’exécution.

## <a name="next-steps"></a>Étapes suivantes



* Pour les capacités de stockage, les détails de disque et d’autres considérations pour choisir parmi les tailles de la machine virtuelle, voir [tailles pour les machines virtuelles](virtual-machines-linux-sizes.md).

* Pour exécuter les scripts de CoreMark sur les machines virtuelles de Linux, téléchargez le [pack de script CoreMark](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip).