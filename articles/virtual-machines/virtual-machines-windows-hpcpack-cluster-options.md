<properties
 pageTitle="Options de cluster de Windows HPC Pack dans le nuage | Microsoft Azure"
 description="En savoir plus sur les options avec Microsoft HPC Pack pour créer et gérer un Windows haute performance cluster (HPC) dans le nuage Azure"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-a-windows-hpc-cluster-in-azure"></a>Options avec le HPC Pack pour créer et gérer un cluster de Windows HPC dans Azure

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Cet article traite des options pour créer des clusters HPC Pack pour exécuter des charges de travail de Windows. Il existe également des options pour la création de clusters pour exécuter des [charges de travail Linux HPC avec le HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).


## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Exécuter un cluster HPC Pack dans Azure VM

### <a name="azure-templates"></a>Modèles Azure

* (Marché) [Cluster HPC Pack pour les charges de travail Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marché) [Cluster HPC Pack pour les charges de travail Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Démarrage rapide) [Créer un cluster à haute performance](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)

* (Démarrage rapide) [Créer un cluster à haute performance avec une image de nœud de calcul personnalisé](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Images VM Azure

* [Pack HPC sur Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nœud de calcul HPC Pack sur Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Nœud avec Excel sur Windows Server 2012 R2 de calcul HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### <a name="powershell-deployment-script"></a>Script de déploiement PowerShell

* [Créer un cluster à haute performance avec le script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Didacticiels

* [Didacticiel : Mise en route avec un cluster HPC Pack dans Azure pour exécuter des charges de travail Excel et SOA](virtual-machines-windows-excel-cluster-hpcpack.md)



### <a name="manual-deployment-with-the-azure-portal"></a>Déploiement manuel avec le portail Azure

* [Configurer le nœud principal d’un cluster HPC Pack dans un ordinateur virtuel d’Azure](virtual-machines-windows-hpcpack-cluster-headnode.md)

### <a name="cluster-management"></a>Gestion de clusters

* [Gérer les nœuds de calcul dans un cluster HPC Pack dans Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)

* [Agrandir et réduire les ressources de calcul Azure dans un cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Soumettre des tâches à un cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Gestion des tâches dans le HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="add-worker-role-nodes-to-an-hpc-pack-cluster"></a>Ajouter des nœuds de rôle de travail à un cluster HPC Pack


* [En mode rafale à des instances de travailleur Azure avec le HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Didacticiel : Configurer un cluster hybride avec le HPC Pack dans Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Ajouter des nœuds de la « Burst » Azure à un nœud de tête HPC Pack dans Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)


## <a name="integrate-with-azure-batch"></a>Intégration avec le traitement par lots Azure 

* [En mode rafale à Azure lot avec le HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>Créer des clusters RDMA pour les charges de travail MPI

* [Configurer un cluster Windows RDMA avec le HPC Pack à exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)
