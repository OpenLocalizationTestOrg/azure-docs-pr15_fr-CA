<properties
 pageTitle="Exécution des applications MPI du cluster Linux RDMA | Microsoft Azure"
 description="Créer un cluster Linux de taille H16r, H16mr, A8 ou A9 VM à utiliser le réseau Azure RDMA pour exécuter les applications MPI"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Configurer un cluster Linux RDMA pour exécuter des applications MPI


Apprenez à configurer un cluster Linux RDMA dans Azure avec [VMs de série une série H ou de calcul intensif](virtual-machines-linux-a8-a9-a10-a11-specs.md) pour exécuter des applications d’Interface MPI (Message Passing) parallèles. Cet article fournit des étapes pour préparer une image Linux HPC pour exécuter Intel MPI sur un cluster. Ensuite, vous déployez un cluster d’ordinateurs virtuels à l’aide de cette image et un des formats compatibles RDMA Azure VM (actuellement H16r, H16mr, A8 ou A9). Le cluster permet d’exécuter des applications MPI communiquent efficacement via une latence faible, haut débit basée sur le réseau distant direct memory Access la technologie (RDMA).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="cluster-deployment-options"></a>Options de déploiement de cluster

Voici les méthodes que vous pouvez utiliser pour créer un cluster Linux RDMA, avec ou sans un planificateur de tâches.


* **Scripts CLI d’azure** - comme indiqué plus loin dans cet article, utilisent l' [Interface de ligne de commande de Azure](../xplat-cli-install.md) (CLI) pour le déploiement d’un cluster d’ordinateurs virtuels compatibles RDMA de script. L’infrastructure CLI en mode de gestion de Service crée en série les nœuds de cluster dans le modèle de déploiement classique, afin de déployer plusieurs nœuds de calcul peut prendre plusieurs minutes. Pour activer la connexion réseau RDMA lorsque vous utilisez le modèle de déploiement classique, déployer les ordinateurs virtuels dans le même service de cloud.

* **Modèles du Gestionnaire de Ressources azure** - vous pouvez également utiliser le modèle de déploiement du Gestionnaire de ressources pour déployer un cluster d’ordinateurs virtuels compatibles RDMA qui se connecte au réseau RDMA. Vous pouvez [créer votre propre modèle](../resource-group-authoring-templates.md), ou de vérifier les [modèles de quickstart Azure](https://azure.microsoft.com/documentation/templates/) pour les modèles fournis par Microsoft ou de la Communauté pour déployer la solution que vous souhaitez. Modèles du Gestionnaire de ressources peuvent fournir un moyen fiable et rapide à déployer un cluster Linux. Pour activer la connexion réseau RDMA lorsque vous utilisez le modèle de déploiement du Gestionnaire de ressources, déployer les ordinateurs virtuels dans le même jeu de disponibilité.

* Le **HPC Pack** - créer un cluster Microsoft HPC Pack dans Azure et ajouter des nœuds de calcul compatibles RDMA qui exécutent une distribution Linux pris en charge pour accéder au réseau RDMA. Reportez-vous à la section [mise en route avec les nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-classic-model"></a>Exemples de procédures de déploiement dans le modèle classique

Les étapes suivantes montrent comment utiliser la CLI de Azure pour déployer un ordinateur virtuel de SUSE Linux Enterprise Server (SLES) 12 SP1 HPC depuis le site Marketplace d’Azure, personnaliser et créer une image personnalisée de la machine virtuelle. Ensuite, utilisez l’image pour le déploiement d’un cluster d’ordinateurs virtuels compatibles RDMA de script. 

>[AZURE.TIP]Utiliser une procédure similaire pour déployer un cluster de VMs prenant en charge les RDMA basé sur d’autres images HPC prises en charge sur le marché d’Azure. Certaines étapes peuvent différer légèrement, comme indiqué. Par exemple, Intel MPI est inclus et configuré uniquement dans quelques-uns de ces images. Et si vous déployez une VM de HPC SLES 12 au lieu d’une VM de HPC SLES 12 SP1, vous devez mettre à jour les pilotes RDMA. Pour plus d’informations, voir [instances de calcul intensif sur l’A8 et A9, A10, A11](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12).


### <a name="prerequisites"></a>Conditions préalables

*   **Ordinateur client** , vous avez besoin d’un Mac, Linux ou Windows client ordinateur de communiquer avec Azure. Ces étapes supposent que vous utilisez un client Linux.

*   **Abonnement azure** - si vous n’êtes pas abonné, vous pouvez créer un [compte de libérer](https://azure.microsoft.com/free/) dans quelques minutes. Pour les clusters de grande taille, envisagez un abonnement de paiement ou d’autres options d’achat. 

*   **Disponibilité de taille de mémoire virtuelle** - actuellement les tailles d’instance suivantes sont capables de RDMA : H16r, H16mr, A8 et A9. Vérifier disponibilité dans les régions Azure [produits disponibles par région](https://azure.microsoft.com/regions/services/) . 

*   **Quota de cœurs** - vous devrez peut-être augmenter le quota de cœurs pour déployer un cluster d’ordinateurs virtuels de calcul intensif. Par exemple, vous avez besoin d’au moins 128 cœurs si vous souhaitez déployer 8 A9 VM, comme indiqué dans cet article. Votre abonnement peut également limiter le nombre de cœurs, que vous pouvez déployer de certaines familles de taille de machine virtuelle, y compris la série-H. Pour demander un quota augmenter, [Ouvrez une demande de support client en ligne](../azure-supportability/how-to-create-azure-support-request.md) sans frais. 

*   **CLI Azure** - [d’installer](../xplat-cli-install.md) l’interface CLI d’Azure et [se connecter à votre abonnement Azure](../xplat-cli-connect.md) à partir de l’ordinateur client.


### <a name="step-1-provision-a-sles-12-sp1-hpc-vm"></a>Étape 1. Mettre en service un ordinateur virtuel de SLES SP1 12 HPC

Après l’ouverture de session Azure avec la CLI d’Azure, exécutez `azure config list` pour confirmer que la sortie indique le mode de gestion de Service. S’il n’est pas le cas, définissez le mode en exécutant cette commande :


    azure config mode asm


Tapez la commande suivante pour répertorier tous les abonnements que vous êtes autorisé à utiliser :


    azure account list

L’abonnement en cours est identifié avec `Current` la valeur `true`. Si cet abonnement n’est pas celui que vous souhaitez utiliser pour créer le cluster, définissez l’Id de l’abonnement approprié comme l’abonnement actif :

    azure account set <subscription-Id>

Pour visualiser les images SLES 12 SP1 HPC publiquement disponibles dans Azure, exécutez une commande semblable à la suivante, en supposant que de votre de prise en charge de l’environnement shell **grep**:


    azure vm image list | grep "suse.*hpc"

Maintenant provisionner une VM RDMA compatible avec une image SLES 12 SP1 HPC en exécutant une commande semblable à la suivante :

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

où

* La taille (A9 dans cet exemple) est un des formats compatibles RDMA VM.

* Le numéro de port externe SSH (22 dans cet exemple, qui est la valeur par défaut SSH) est un numéro de port valide. Le numéro de port interne SSH est défini à 22.

* Un nouveau service en nuage est créé dans la région Azure spécifiée par l’emplacement. Spécifiez un emplacement dans lequel la taille de mémoire virtuelle que vous choisissez est disponible.

* Le nom d’image SLES 12 SP1 peut actuellement être `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824` ou `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824` pour la prise en charge de la priorité SUSE (des frais supplémentaires s’appliquent).

### <a name="step-2-customize-the-vm"></a>Étape 2. Personnaliser l’ordinateur virtuel

Une fois que la machine virtuelle complète de mise en service, SSH à la machine virtuelle en utilisant l’adresse IP externe de la machine virtuelle (ou le nom DNS) et le port externe numéro vous configuré et le personnaliser. Détails de connexion, consultez [connexion à un Linux exécutant d’ordinateur virtuel](virtual-machines-linux-mac-create-ssh-keys.md). Exécuter les commandes que l’utilisateur que vous avez configuré sur la machine virtuelle, à moins que l’accès à la racine est nécessaire pour terminer une étape.

>[AZURE.IMPORTANT]Microsoft Azure ne fournit pas d’accès à la racine aux machines virtuelles de Linux. Pour obtenir l’accès d’administration lorsque vous êtes connecté en tant qu’utilisateur de la machine virtuelle, exécuter des commandes à l’aide de `sudo`.

* **Mises à jour** - mises à jour de l’installation à l’aide de **zypper**. Vous pourriez également installer les utilitaires NFS. 

    >[AZURE.IMPORTANT]Dans une VM de HPC SLES 12 SP1, nous vous recommandons que vous n’appliquez pas les mises à jour du noyau, ce qui peuvent provoquer des problèmes avec le RDMA Linux pilotes.

* **Les MPI Intel** - terminer l’installation de MPI Intel sur la machine virtuelle de SLES 12 SP1 HPC en exécutant la commande suivante :

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

* **Verrouillage de mémoire** - codes de MPI pour verrouiller la mémoire disponible pour RDMA, d’ajouter ou de modifier les paramètres suivants dans le fichier /etc/security/limits.conf. (Vous devez accès racine pour modifier ce fichier.) 

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]À des fins de test, vous pouvez également définir des memlock illimité. Par exemple : `<User or group name>    hard    memlock unlimited`. Pour plus d’informations, consultez [Méthodes de mieux connu pour la taille de la mémoire le paramètre verrouillé](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).

* **Clés SSH pour les machines virtuelles de SLES** - générer SSH clés pour établir l’approbation de votre compte d’utilisateur entre les nœuds de calcul du cluster SLES lors de l’exécution de travaux MPI. (Si vous avez déployé une VM HPC basée sur CentOS, ne suivez pas cette étape. Consultez les instructions ultérieurement dans cet article pour définir passwordless confiance SSH entre les nœuds du cluster une fois que vous capturez l’image et déployez du cluster). 

    Exécutez la commande suivante pour créer des clés SSH. Lorsque vous y êtes invité pour l’entrée, appuyez sur ENTRÉE pour générer les clés dans l’emplacement par défaut sans définir un mot de passe.

        ssh-keygen

    Ajouter la clé publique dans le fichier authorized_keys pour les clés publiques connues.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Dans le répertoire ~/.ssh, modifier ou créer le fichier « config ». Fournir la plage d’adresses IP du réseau privé que vous prévoyez d’utiliser dans Azure (10.32.0.0/16 dans cet exemple) :

        host 10.32.0.*
        StrictHostKeyChecking no

    Vous pouvez également la liste l’adresse IP de réseau privé de chaque machine virtuelle dans votre cluster comme suit :

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]Configuration de `StrictHostKeyChecking no` peut créer un risque de sécurité potentiel lorsqu’une adresse IP spécifique ou une plage n’est pas spécifié.

* **Applications** , installer des applications, vous avez besoin sur cet ordinateur virtuel ou effectuez d’autres personnalisations avant de capturer l’image.

### <a name="step-3-capture-the-image"></a>Étape 3. Capturer l’image

Pour capturer l’image, tout d’abord d’exécuter la commande suivante dans la VM de Linux. Cette commande arrête la machine virtuelle, mais conserve les comptes d’utilisateurs et clés SSH que vous définissez.

```
sudo waagent -deprovision
```

Ensuite, à partir de votre ordinateur client, exécutez les commandes CLI d’Azure suivantes pour capturer l’image. Pour plus d’informations, voir [la capture d’une machine de virtuelle Linux classique sous forme d’image](virtual-machines-linux-classic-capture-image.md) .  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Après avoir exécuté ces commandes, l’image de la machine virtuelle est capturée pour l’utilisation et la machine virtuelle est supprimée. Vous avez maintenant votre image personnalisée prêt à déployer un cluster.

### <a name="step-4-deploy-a-cluster-with-the-image"></a>Étape 4. Déployer un cluster avec l’image

Modifiez le script Bash suivant avec les valeurs appropriées pour votre environnement et exécutez-le à partir de votre ordinateur client. Car Azure déploie les ordinateurs virtuels en série dans le modèle de déploiement classique, il prend quelques minutes pour déployer le VMs A9 8 suggérées dans ce script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Considérations sur un cluster HPC de CentOS

Si vous souhaitez configurer un cluster basé sur l’une des images sur le marché d’Azure au lieu de SLES 12 basée sur CentOS HPC pour HPC, suivez les étapes générales de la section précédente. Notez les différences suivantes lorsque vous mettez en service et configurez la machine virtuelle :

1. Intel MPI est déjà installé sur un ordinateur virtuel configuré à partir d’une image basée sur CentOS HPC. 

2. Verrouiller les paramètres de mémoire ont déjà été ajoutés dans le fichier /etc/security/limits.conf de l’ordinateur.

2. Ne générez pas de clés SSH sur la machine virtuelle que vous mettez en service pour la capture. Au lieu de cela, nous vous recommandons de configurer l’authentification utilisateur après le déploiement de la cluser. Consultez la section suivante.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Définir passwordless approbation SSH sur le cluster

Sur un cluster HPC de base CentOS, il existe deux méthodes pour établir une approbation entre les nœuds de calcul : l’authentification basée sur l’hôte et l’authentification basée sur les utilisateurs. L’authentification basée sur l’hôte est en dehors de la portée de cet article et doit généralement être effectuée par un script d’extension pendant le déploiement. L’authentification utilisateur est pratique pour établir une approbation après le déploiement et requiert la génération et le partage de clés SSH entre les nœuds de calcul dans le cluster. Cette méthode est communément appelée connexion SSH passwordless et est nécessaire lors de l’exécution de travaux MPI. 

Un exemple de script a contribué à partir de la Communauté est disponible sur [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) pour activer l’authentification utilisateur simple sur un cluster HPC de base CentOS. Téléchargez et utilisez ce script à l’aide de la procédure suivante. Vous pouvez également modifier ce script ou toute autre méthode permet d’établir l’authentification SSH passwordless entre les nœuds de cluster de calcul.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Pour exécuter le script, vous devez connaître le préfixe pour les adresses IP de votre sous-réseau. Obtenir le préfixe en exécutant la commande suivante sur l’un des nœuds du cluster. Votre sortie doit ressembler à 10.1.3.5, et le préfixe est le 10.1.3 partie.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

À présent exécuter le script à l’aide de trois paramètres : le nom d’utilisateur commun sur les nœuds de calcul, le mot de passe commun pour cet utilisateur sur les nœuds de calcul et le préfixe de sous-réseau qui a été retourné à partir de la commande précédente.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Ce script effectue les opérations suivantes :

* Crée un répertoire sur le nœud hôte nommé .ssh, qui est requis pour la connexion passwordless. 
* Crée un fichier de configuration dans le répertoire .ssh qui indique la connexion passwordless pour permettre la connexion à partir de n’importe quel nœud dans le cluster. 
* Crée des fichiers contenant les noms de nœuds et les adresses IP de nœud pour tous les nœuds du cluster. Ces fichiers sont conservés après l’exécution du script pour référence ultérieure. 
* Crée une paire de clés publique et privée pour chaque nœud de cluster, notamment le nœud hôte et crée des écritures dans le fichier authorized_keys.

>[AZURE.WARNING]L’exécution de ce script peut de créer un risque de sécurité potentiel. Vérifiez que les informations de clé publique dans ~/.ssh ne sont pas distribuées.


## <a name="configure-intel-mpi"></a>Configurer Intel MPI

Pour exécuter des applications MPI sur Azure Linux RDMA, vous devez configurer certaines variables d’environnement spécifiques à Intel MPI. Voici un exemple de script Bash pour configurer les variables nécessaires à l’exécution d’une application. Modifiez le chemin d’accès à mpivars.sh pour l’installation d’Intel.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Le format du fichier hôte est comme suit. Ajoutez une ligne pour chaque nœud de votre cluster. Spécifier les adresses IP privées à partir de la VNet définie plus haut, pas les noms DNS. Par exemple, pour que deux hôtes avec les adresses IP 10.32.0.1 et 10.32.0.2, le fichier contient les éléments suivants :

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Exécuter des MPI sur un cluster de deux nœuds de base

Si vous ne l’avez pas déjà fait, tout d’abord configurer l’environnement pour les MPI Intel. 

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-a-simple-mpi-command"></a>Exécuter une commande MPI simple

Exécuter une commande MPI simple sur l’un des nœuds de calcul pour montrer que les MPI est installé correctement et peut communiquer au moins que deux nœuds de calcul. La commande **mpirun** suivante exécute la commande **hostname** sur deux nœuds.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Votre sortie doit répertorier les noms de tous les nœuds que vous avez passées en tant qu’entrée pour `-hosts`. Par exemple, une commande **mpirun** avec deux nœuds renvoie sortie semblable à la suivante :

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Exécuter un banc d’essai MPI

La commande Intel MPI suivante exécute un banc d’essai pingpong pour vérifier la configuration du cluster et la connexion au réseau RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Sur un cluster opérationnel avec deux nœuds, vous devez voir une sortie similaire à ce qui suit. Sur le réseau Azure RDMA, attendez jusqu'à 512 octets de tailles de latence au niveau ou en dessous de 3 microsecondes pour le message.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Étapes suivantes

* Essayez de déployer et exécuter des applications de votre MPI Linux sur votre cluster Linux.

* Consultez la [documentation de la bibliothèque de MPI Intel](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) pour obtenir des conseils sur les MPI Intel.

* Essayez un [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) pour créer un cluster Lustre d’Intel à l’aide d’une image basée sur CentOS HPC. Pour plus d’informations, consultez ce [blog valider](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
