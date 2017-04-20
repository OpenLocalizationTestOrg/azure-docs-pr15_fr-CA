<properties
 pageTitle="Options de cluster HPC Pack de Linux dans le nuage | Microsoft Azure"
 description="En savoir plus sur les options avec Microsoft HPC Pack pour créer et gérer un Linux haute performance computing clusters (HPC) dans le nuage Azure"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>Options avec le HPC Pack pour créer et gérer un cluster HPC dans Azure pour les charges de travail Linux

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Cet article se concentre sur les options à utiliser le HPC Pack à exécuter les charges de travail Linux. Il existe également des options pour l’exécution de [Windows HPC de charges de travail avec le HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Exécuter un cluster HPC Pack dans Azure VM

### <a name="azure-templates"></a>Modèles Azure


* (Marché) [Cluster HPC Pack pour les charges de travail Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Démarrage rapide) [Créer un cluster à haute performance avec Linux des nœuds de calcul](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)


### <a name="powershell-deployment-script"></a>Script de déploiement PowerShell

* [Créer un cluster HPC de Linux avec le script de déploiement des HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Didacticiels

* [Didacticiel : Mise en route avec les nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Didacticiel : Exécuter NAMD avec Microsoft HPC Pack sous Linux nœuds de calcul dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Didacticiel : OpenFOAM d’exécution avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Didacticiel : Exécution STAR-CCM + avec Microsoft HPC Pack sur un RDMA Linux cluster dans Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### <a name="cluster-management"></a>Gestion de clusters

* [Soumettre des tâches à un cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Gestion des tâches dans le HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="create-rdma-clusters-for-mpi-workloads"></a>Créer des clusters RDMA pour les charges de travail MPI

* [Didacticiel : OpenFOAM d’exécution avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configurer un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md)

