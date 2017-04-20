<properties
 pageTitle="Sur les ordinateurs virtuels de calcul intensif avec Windows | Microsoft Azure"
 description="Obtenir des informations générales et des considérations sur l’utilisation des Azure H-series et A8, A9, A10, A11 intensives tailles et pour les services de cloud et les ordinateurs virtuels de Windows"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>À propos de la série H et calcul intensif VM d’une série

Voici des informations générales et certaines considérations sur l’utilisation de la nouvelle série H Azure et les instances de A8, A9, A10, A11 et d’antérieures, également connu sous le nom des instances de *calcul intensif* . Cet article traite de l’utilisation de ces instances pour les machines virtuelles de Windows. Cet article est également disponible pour les [Machines virtuelles de Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accès au réseau RDMA

Vous pouvez créer des clusters des instances de serveur de Windows prenant en charge les RDMA et déployer une des implémentations de MPI pris en charge pour tirer parti du réseau RDMA d’Azure. Ce réseau de faible latence, à haute capacité est réservé pour le trafic MPI uniquement.

* **Système d'exploitation**
    * **Machines virtuelles** - Windows Server 2012 R2, Windows Server 2012
    * **Les services en nuage** - Windows Server 2012 R2, Windows Server 2012, famille de système d’exploitation de Windows Server 2008 R2 invité

* **MPI** - Microsoft MPI (MS-MPI) 2012 R2 ou version ultérieure, bibliothèque de MPI Intel 5.x

Prise en charge des implémentations de MPI utilisent l’interface directe de réseau Microsoft pour communiquer entre les instances. Pour les options de déploiement et les étapes de configuration d’exemple, reportez-vous à la section [configurer un cluster Windows RDMA avec le HPC Pack à exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) et [Utilisez les tâches de plusieurs instances pour exécuter des applications d’Interface MPI (Message Passing) dans le traitement par lots d’Azure](../batch/batch-mpi.md) .


>[AZURE.NOTE]Sur les ordinateurs virtuels de calcul intensif compatibles RDMA, l’extension de HpcVmDrivers doit être ajoutée aux ordinateurs virtuels pour installer des pilotes de périphériques réseau Windows qui sont nécessaires pour une connectivité RDMA. Dans la plupart des déploiements, l’extension HpcVmDrivers est ajoutée automatiquement. Si vous devez ajouter l’extension vous-même, consultez [extensions de gérer la machine virtuelle](virtual-machines-windows-classic-manage-extensions.md).

## <a name="considerations-for-hpc-pack-and-windows"></a>Considérations pour le HPC Pack et Windows

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), cluster à haute performance gratuit de Microsoft et la solution de gestion de projet, n’est pas nécessaire d’utiliser les instances de calcul intensif avec Windows Server. Toutefois, il s’agit d’une option pour créer un cluster de calcul dans Azure pour exécuter des applications basées sur Windows MPI et autres charges de travail HPC. HPC Pack 2012 R2 et les versions ultérieures comprennent un environnement d’exécution de MS-MPI qui utilisent le réseau Azure RDMA lors du déploiement sur les ordinateurs virtuels compatibles RDMA.

Pour plus d’informations et des listes de contrôle d’utiliser les instances de calcul intensif avec le HPC Pack sur Windows Server, reportez-vous à [configurer un cluster Windows RDMA avec le HPC Pack pour exécuter des applications de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la disponibilité et le prix des tailles de calcul intensif, consultez [tarification des Machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) et la [tarification des Services en nuage](https://azure.microsoft.com/pricing/details/cloud-services/).

* Pour les capacités de stockage et les détails d’un disque, reportez-vous à la section [tailles pour les machines virtuelles](virtual-machines-linux-sizes.md).

* Pour commencer à déployer et d’utiliser des instances de calcul intensif avec le HPC Pack sous Windows, reportez-vous à [configurer un cluster Windows RDMA avec le HPC Pack pour exécuter des applications de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Pour plus d’informations sur l’utilisation des instances A8 et A9 pour exécuter des applications de MPI dans des lots d’Azure, consultez [Utilisez les tâches de plusieurs instances pour exécuter des applications d’Interface MPI (Message Passing) dans le traitement par lots d’Azure](../batch/batch-mpi.md).
