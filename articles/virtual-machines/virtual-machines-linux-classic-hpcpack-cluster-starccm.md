<properties
 pageTitle="Exécutez STAR-CCM + Pack HPC sur Linux VM | Microsoft Azure"
 description="Déployer un cluster Microsoft HPC Pack sur Azure et d’exécuter une STAR-CCM + travail sur Linux plusieurs nœuds de calcul via un réseau RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/13/2016"
 ms.author="xpillons"/>

# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Exécutez STAR-CCM + avec Microsoft HPC Pack sur un RDMA Linux cluster dans Azure
Cet article vous explique comment déployer un cluster Microsoft HPC Pack sur Azure et d’exécuter un [CD-adapco STAR-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) travail sur plusieurs nœuds de calcul Linux qui sont interconnectés avec InfiniBand.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack fournit les fonctionnalités pour exécuter une variété de HPC à grande échelle et des applications parallèles, y compris les applications de MPI, sur les clusters d’ordinateurs virtuels Microsoft Azure. HPC Pack prend également en charge l’exécution d’applications Linux HPC sur Linux-nœud de calcul VMs qui est déployés dans un cluster HPC Pack. Pour une introduction à l’utilisation de Linux nœuds avec le HPC Pack, reportez-vous à la section [mise en route avec les nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Configurer un cluster HPC Pack
Télécharger les scripts de déploiement HPC Pack IaaS à partir du [Centre de téléchargement](https://www.microsoft.com/en-us/download/details.aspx?id=44949) et les extraire localement.

PowerShell Azure est une condition préalable. Si PowerShell n’est pas configuré sur votre ordinateur local, consultez l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Au moment de la rédaction de ce document, les images de Linux à partir du marché Azure (qui contient les pilotes InfiniBand pour Azure) sont pour SLES 12, CentOS 6.5 et CentOS 7.1. Cet article est basé sur l’utilisation de SLES 12. Pour récupérer le nom de toutes les images de Linux qui prend en charge les HPC sur le marché, vous pouvez exécuter la commande PowerShell suivante :

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

Le résultat indique l’emplacement dans lequel ces images sont disponibles et le nom de l’image (**ImageName**) doit être utilisée ultérieurement dans le modèle de déploiement.

Avant de déployer le cluster, vous devez créer un fichier de modèle de déploiement de HPC Pack. Étant donné que nous ciblons un petit cluster, le nœud principal sera le contrôleur de domaine et héberger une base de données SQL locale.

Le modèle suivant déploient un tel nœud de tête, créer un fichier XML nommé **MyCluster.xml**et remplacez les valeurs du **SubscriptionId**, **StorageAccount**, **emplacement**, **VMName**et **ServiceName** avec le vôtre.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Démarrer la création du nœud de tête en exécutant la commande PowerShell dans une invite de commandes avec élévation de privilèges :

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Après 20 à 30 minutes, le nœud de tête doit être prêt. Vous pouvez vous connecter à celui-ci à partir du portail Azure en cliquant sur l’icône de **connexion** de l’ordinateur virtuel.

Vous devrez éventuellement corriger le redirecteur DNS. Pour ce faire, démarrez le Gestionnaire DNS.

1.  Droit sur le nom du serveur dans le Gestionnaire DNS, sélectionnez **Propriétés**, puis cliquez sur l’onglet **redirecteurs** .

2.  Cliquez sur le bouton **Modifier** pour supprimer les redirecteurs, puis cliquez sur **OK**.

3.  Assurez-vous que la case à cocher **utiliser les indications de racine si aucun redirecteur n’est disponibles** est sélectionnée, puis cliquez sur **OK**.

## <a name="set-up-linux-compute-nodes"></a>Configurer des nœuds de calcul Linux
Vous déployez les nœuds de calcul Linux en utilisant le même modèle de déploiement que vous avez utilisé pour créer le nœud de tête.

Copiez le fichier **MyCluster.xml** à partir de votre ordinateur local pour le nœud de tête et mettre à jour la balise **NodeCount** avec le nombre de nœuds que vous souhaitez déployer (< = 20). Veillez à avoir suffisamment cœurs disponibles dans votre quota d’Azure, car chaque instance A9 consomme 16 noyaux dans votre abonnement. Vous pouvez utiliser des instances de A8 (8 cœurs) au lieu de A9 si vous souhaitez utiliser davantage d’ordinateurs virtuels dans le même budget.

Sur le nœud de tête, copier les scripts de déploiement HPC Pack IaaS.

Exécutez les commandes PowerShell d’Azure suivantes dans une invite de commandes avec élévation de privilèges :

1.  Exécutez **Add-AzureAccount** pour vous connecter à votre abonnement Azure.

2.  Si vous disposez de plusieurs abonnements, exécutez **Get-AzureSubscription** pour afficher la liste.

3.  Définir un abonnement par défaut en exécutant le **SubscriptionName sélectionnez-AzureSubscription - xxxx-par défaut** commande.

4.  Exécutez **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** pour commencer à déployer des nœuds de calcul Linux.

    ![Déploiement du nœud de tête en action][hndeploy]

Ouvrez l’outil Gestion du Cluster HPC Pack. Après quelques minutes, des nœuds de calcul Linux régulièrement apparaîtra dans la liste des nœuds de calcul de cluster. Avec le mode de déploiement classique, IaaS VMs sont créés de façon séquentielle. Ainsi, si le nombre de noeuds est important, incitant tous déployé peuvent prendre un temps considérable.

![Nœuds de Linux dans le Gestionnaire de Cluster HPC Pack][clustermanager]

Maintenant que tous les nœuds sont en cours d’exécution dans le cluster, il existe des paramètres d’infrastructure supplémentaire pour vous.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Définissez un partage de fichier d’Azure pour les nœuds de Windows et Linux
Vous pouvez utiliser le service de fichiers d’Azure pour stocker les scripts, les packages d’application et les fichiers de données. Fichier Azure fournit les fonctions CIFS sur le stockage des objets Blob Azure sous la forme d’un magasin persistant. N’oubliez pas que ce n’est pas la solution la plus évolutive, mais il est plus simple et ne nécessite pas de machines virtuelles dédiés.

Créer un partage de fichiers d’Azure en suivant les instructions dans l’article [mise en route de stockage Azure sous Windows](..\storage\storage-dotnet-how-to-use-files.md).

Conservez le nom de votre compte de stockage **saname**, le nom de partage de fichier en tant que **nom de partage**et la clé de compte de stockage en tant que **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Montez le partage de fichiers d’Azure sur le nœud principal
Ouvrez une invite de commandes avec élévation de privilèges et exécutez la commande suivante pour stocker les informations d’identification dans le coffre-fort de la machine locale :

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Ensuite, pour monter le partage de fichiers d’Azure, exécutez :

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Montez le partage de fichiers d’Azure sur les nœuds de calcul Linux
Un outil utile qui est fourni avec le HPC Pack est l’outil clusrun. Vous pouvez utiliser cet outil de ligne de commande pour exécuter la même commande de simultanément sur un ensemble de nœuds de calcul. Dans notre cas, il est utilisé pour le partage de fichiers d’Azure de monter et de rendre persistant à survivre aux redémarrages.
Dans une invite de commandes avec élévation de privilèges sur le nœud de tête, exécutez les commandes suivantes.

Pour créer le répertoire de montage :

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Pour monter le partage de fichiers d’Azure :

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Pour conserver le partage de montage :

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Installation de STAR-CCM +
Azure VM instances A8 et A9 assurent InfiniBand et capacités RDMA. Les pilotes de noyau qui permettent ces fonctionnalités sont disponibles pour Windows Server 2012 R2, SUSE 12, CentOS 6.5 et images CentOS 7.1 sur le marché d’Azure. Microsoft MPI et Intel MPI (version 5.x) sont les deux bibliothèques MPI qui prennent en charge ces pilotes dans Azure.

CD-adapco STAR-CCM + version 11.x et ultérieurement est fourni avec la version de Intel MPI 5.x, prise en charge de InfiniBand pour Azure est inclus.

Obtenir le Linux64 STAR-CCM + package à partir du [CD-adapco portal](https://steve.cd-adapco.com). Dans notre cas, nous avons utilisé la version 11.02.010 de précision mixte.

Sur le nœud de tête, dans le partage de fichier d’Azure **/hpcdata** , créez un script shell intitulé **setupstarccm.sh** avec le contenu suivant. Ce script sera exécuté sur chaque nœud de calcul configuration STAR-CCM + localement.

#### <a name="sample-setupstarcmsh-script"></a>Exemple de script setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Maintenant, pour configurer la STAR-CCM + sur tous vos Linux nœuds de calcul, ouvrez une invite de commandes avec élévation de privilèges et exécutez la commande suivante :

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Pendant l’exécution de la commande, vous pouvez surveiller l’utilisation du processeur à l’aide de la carte thermique du Gestionnaire de Cluster. Après quelques minutes, tous les nœuds doivent être correctement définies.

## <a name="run-star-ccm-jobs"></a>Exécutez STAR-CCM + travaux
HPC Pack est utilisé pour ses fonctionnalités de planificateur de tâches pour exécuter STAR-CCM + travaux. Pour ce faire, nous avons besoin de la prise en charge de quelques scripts qui sont utilisés pour démarrer la tâche et exécutez STAR-CCM +. Les données d’entrée sont conservées sur le partage de fichiers d’Azure premier pour plus de simplicité.

Le script PowerShell suivant est utilisé pour la file d’attente d’une étoile-CCM + travail. Il prend trois arguments :

*  Le nom du modèle

*  Le nombre de nœuds à utiliser

*  Le nombre de cœurs sur chaque nœud à utiliser

Étant donné que STAR-CCM + peut remplir la bande passante de mémoire, il est généralement préférable d’utiliser moins de cœurs par les nœuds de calcul et d’ajouter de nouveaux nœuds. Le nombre exact de noyaux par nœud dépend de la famille de processeurs et de la vitesse de l’interconnexion.

Les nœuds sont affectés exclusivement pour le travail et ne peut pas être partagés avec d’autres tâches. La tâche n’est pas démarrée directement comme un travail MPI. Le script du shell **runstarccm.sh** démarrera le Lanceur MPI.

Le modèle d’entrée et le script **runstarccm.sh** sont stockées dans le partage de **/hpcdata** qui a été monté auparavant.

Les fichiers journaux sont nommés avec l’ID de travail et sont stockés dans le **partage de /hpcdata**, ainsi que de l’étoile-CCM + fichiers de sortie.


#### <a name="sample-submitstarccmjobps1-script"></a>Exemple de script SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job    
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Remplacer **runner.java** avec votre étoile préféré-Lanceur de modèle CCM + Java et code de journalisation.

#### <a name="sample-runstarccmsh-script"></a>Exemple de script runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

Dans notre test, nous avons utilisé un jeton de licence d’alimentation-à la demande. Pour ce jeton, vous devez définir la variable d’environnement **$CDLMD_LICENSE_FILE** **1999@flex.cd-adapco.com** et la clé de l’option **- podkey** de la ligne de commande.

Après une initialisation, le script extrait--les **$CCP_NODES_CORES** variables d’environnement HPC Pack définie : la liste de nœuds pour générer un fichier hôte que le Lanceur MPI utilise. Ce fichier hôte contient la liste des noms de nœuds de calcul sont utilisées pour le travail, un nom par ligne.

Le format de **$CCP_NODES_CORES** suit ce modèle :

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Où :

* `<Number of nodes>`est le nombre de nœuds affectés à cette tâche.

* `<Name of node_n_...>`est le nom de chaque nœud affecté à cette tâche.

* `<Cores of node_n_...>`est le nombre de cœurs sur le nœud affecté à cette tâche.

Le nombre de noyaux (**$NBCORES**) est également calculé selon le nombre de nœuds (**$NBNODES**) et le nombre de noyaux par nœud (fourni comme paramètre **$NBCORESPERNODE**).

Pour les options de MPI, ceux qui est utilisés avec les MPI Intel sur Azure sont :

*   `-mpi intel`Pour spécifier les MPI Intel.

*   `-fabric UDAPL`Pour utiliser les verbes Azure InfiniBand.

*   `-cpubind bandwidth,v`Pour optimiser la bande passante de MPI avec STAR-CCM +.

*   `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"`faire travailler avec Azure InfiniBand Intel MPI et pour définir le nombre de noyaux par nœud.

*   `-batch`Pour démarrer la STAR-CCM + en mode batch sans interface utilisateur.


Enfin, pour démarrer une tâche, assurez-vous que vos nœuds sont en cours d’exécution et en ligne dans le Gestionnaire de Cluster. Puis à partir d’une invite de commande PowerShell, exécutez ceci :

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Arrêter les nœuds
Ensuite lorsque vous avez terminé avec vos tests, vous pouvez utiliser les commandes de HPC Pack PowerShell suivantes pour arrêter et démarrer des nœuds :

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Étapes suivantes
Essayez d’exécuter les autres charges de travail Linux. Par exemple, voir :

* [Exécutez NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Exécutez OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png
