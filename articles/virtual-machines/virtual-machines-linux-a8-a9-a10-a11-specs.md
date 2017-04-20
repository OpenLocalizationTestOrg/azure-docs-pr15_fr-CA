<properties
 pageTitle="Sur les ordinateurs virtuels de calcul intensif avec Linux | Microsoft Azure"
 description="Obtenir des informations générales et des considérations sur l’utilisation des H-series et A8, A9, A10, A11 intensives tailles et pour les machines virtuelles de Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>À propos de la série H et calcul intensif VM d’une série 

Voici des informations et des considérations relatives à l’aide la plus récente série H Azure et les tailles de A8, A9, A10, A11 et de version antérieures, également connu sous le nom des instances de *calcul intensif* à. Cet article traite de l’utilisation de ces tailles pour les machines virtuelles de Linux. Cet article est également disponible pour les [Ordinateurs virtuels de Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).




[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accès au réseau RDMA

Vous pouvez créer des clusters de VMs de Linux compatibles RDMA qu’une exécution des opérations suivantes et pris en charge les distributions Linux HPC une implémentation prise en charge de MPI pour tirer parti du réseau Azure RDMA. Pour les options de déploiement et les étapes de configuration des exemples, consultez [configurer un cluster Linux RDMA pour exécuter des applications de MPI](virtual-machines-linux-classic-rdma-cluster.md) .

* **Distributions** - vous devez déployer des ordinateurs virtuels à partir d’images compatibles RDMA SUSE Linux Enterprise Server (SLES) ou basée sur les OpenLogic CentOS HPC sur le marché d’Azure. Uniquement les images de marché suivantes prennent en charge les pilotes Linux RDMA nécessaires :

    * SLES 12 SP1 pour HPC, SLES 12 SP1 pour HPC (Premium)
    
    * SLES 12 pour HPC, SLES 12 pour HPC (Premium)
    
    * HPC de 7.1 basée sur centOS
    
    * HPC de 6.5 basée sur centOS
    
    >[AZURE.NOTE]Pour les machines virtuelles de la série H, nous vous recommandons de 12 SLES SP1 pour image HPC ou images HPC 7.1 CentOS.
    >
    >Sur les images de base CentOS HPC, les mises à jour du noyau sont désactivées dans le fichier de configuration de **yum** . C’est parce que les pilotes Linux RDMA sont distribuées sous la forme d’un package RPM et mises à jour du pilote peut ne pas fonctionneraient si le noyau est mis à jour.

* **MPI** - Bibliothèque MPI Intel 5.x

    En fonction de l’image de l’outil statistique choisie, installation de licence, distincte, ou la configuration de Intel MPI peuvent s’avérer nécessaires, comme suit : 
    
    * **SLES 12 SP1 pour image HPC** - installer les packages Intel MPI distribués sur l’ordinateur virtuel en exécutant la commande suivante :
    
            sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

    * **Image HPC SLES 12** : vous devez vous inscrire séparément pour télécharger et installer les MPI Intel. Consultez le [guide d’installation de bibliothèque des MPI Intel](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf).
    
    * **Les images basées sur centOS HPC** - Intel MPI 5.1 est déjà installé.  

    Une configuration système supplémentaire est nécessaire pour exécuter des travaux MPI sur des ordinateurs virtuels en cluster. Par exemple, sur un cluster d’ordinateurs virtuels, vous devez établir une approbation entre les nœuds de calcul. Pour connaître les paramètres par défaut, reportez-vous à [configurer un cluster Linux RDMA pour exécuter des applications de MPI](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="considerations-for-hpc-pack-and-linux"></a>Considérations pour le HPC Pack et Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), cluster à haute performance gratuit de Microsoft et la solution de gestion de projet, propose une option vous permettant d’utiliser les instances de calcul intensif avec Linux. Les dernières versions de HPC Pack 2012 R2 prise en charge de plusieurs distributions de Linux à exécuter sur les nœuds de calcul déployés dans Azure VM, géré par un nœud de tête de serveur de Windows. Avec les nœuds de calcul Linux compatibles RDMA exécutant Intel MPI, HPC Pack puisse planifier et exécuter des applications qui accèdent au réseau RDMA Linux MPI. Pour commencer, consultez [mise en route avec les nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="network-topology-considerations"></a>Considérations relatives à la topologie réseau

* Sur VMs de Linux RDMA-activé dans Azure, Eth1 est réservée pour le trafic de réseau RDMA. Ne modifiez pas les paramètres Eth1 ou toutes les informations dans le fichier de configuration se rapportant à ce réseau. Eth0 est réservée pour le trafic réseau Azure normal.

* Dans Azure, IP sur InfiniBand (IB) n’est pas pris en charge. Uniquement les RDMA sur i b est pris en charge.

## <a name="rdma-driver-updates-for-sles-12"></a>Mises à jour de pilote RDMA pour SLES 12

Après avoir créé une machine virtuelle basée sur une image SLES 12 HPC, vous devrez peut-être mettre à jour les pilotes RDMA sur les ordinateurs virtuels pour la connectivité de réseau RDMA. 

>[AZURE.IMPORTANT]Cette étape est **obligatoire** pour SLES 12 pour les déploiements de HPC VM dans toutes les régions Azure. 
>Cette étape n’est pas nécessaire si vous déployez SP1 12 SLES HPC, HPC de 7.1 basée sur CentOS ou basée sur CentOS HPC 6.5 VM. 

Avant de vous mettre à jour les pilotes, arrêtez tous les processus de **zypper** ou de tous les processus qui verrouillent les bases de données SUSE mis en pension sur la machine virtuelle. Dans le cas contraire les pilotes ne peut-être pas à jour correctement.  

Pour mettre à jour les pilotes Linux RDMA sur chaque ordinateur virtuel, exécutez un des ensembles de commandes CLI d’Azure suivants à partir de votre ordinateur client.

**12 SLES pour HPC VM mis en service dans le modèle de déploiement classique**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**12 SLES pour HPC VM mis en service dans le modèle de déploiement du Gestionnaire de ressources**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]Il peut prendre un certain temps pour installer les pilotes, et la commande renvoie sans sortie. Après la mise à jour, votre ordinateur virtuel redémarre et doit être prêt à l’emploi dans quelques minutes.

### <a name="sample-script-for-driver-updates"></a>Exemple de script pour les mises à jour de pilote

Si vous disposez d’un cluster de SLES 12 pour les machines virtuelles de HPC, vous pouvez créer un script la mise à jour du pilote sur tous les nœuds de votre cluster. Par exemple, le script suivant met à jour les pilotes dans un cluster à 8 nœuds.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la disponibilité et le prix des tailles de calcul intensif, consultez [tarification des Machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Pour les capacités de stockage et les détails d’un disque, reportez-vous à la section [tailles pour les machines virtuelles](virtual-machines-linux-sizes.md).

* Pour commencer à déployer et d’utiliser des tailles de calcul intensif avec RDMA sous Linux, voir [configurer un cluster Linux RDMA pour exécuter des applications de MPI](virtual-machines-linux-classic-rdma-cluster.md).


