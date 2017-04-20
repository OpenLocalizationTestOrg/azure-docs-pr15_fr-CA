<properties
 pageTitle="Exécutez OpenFOAM avec le HPC Pack sur les ordinateurs virtuels de Linux | Microsoft Azure"
 description="Déployer un cluster Microsoft HPC Pack sur Azure et d’exécuter une tâche OpenFOAM sur plusieurs nœuds de calcul Linux sur un réseau RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Exécutez OpenFoam avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure

Cet article vous montre une façon d’exécuter le OpenFoam sur des machines virtuelles Azure. Ici, vous déployez un cluster Microsoft HPC Pack avec les nœuds de calcul Linux sur Azure et d’exécuter un [OpenFoam](http://openfoam.com/) de travail avec Intel MPI. Vous pouvez utiliser compatibles RDMA Azure VM pour les nœuds de calcul, afin que les nœuds de calcul communiquent sur le réseau d’Azure RDMA. D’autres options pour exécuter OpenFoam dans Azure incluent entièrement configurés images commerciales disponibles sur le marché, par exemple [2.3 OpenFoam sur CentOS 6 du UberCloud](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/)et en exécutant sur [Azure lot](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (pour ouvrir une opération de champ et Manipulation) est un logiciel open source calcul dynamique des fluides (CFD) qui est utilisé largement dans l’ingénierie et la science, dans les organisations commerciales et universitaires. Il inclut des outils pour le maillage, notamment snappyHexMesh, un mailleur parallélisée pour les géométries complexes de CAO et de pré et post-traitement. Presque tous les processus s’exécutent en parallèle, ce qui permet aux utilisateurs de profiter pleinement du matériel informatique à leur disposition.  

Microsoft HPC Pack fournit les fonctionnalités pour exécuter des applications parallèles, y compris les applications de MPI, sur les clusters d’ordinateurs virtuels Microsoft Azure et HPC à grande échelle. HPC Pack prend également en charge exécutant Linux HPC applications sous Linux calculent le nœud ordinateurs virtuels déployés dans un cluster HPC Pack. Pour une introduction à l’utilisation de nœuds de calcul Linux avec le HPC Pack, consultez [mise en route avec les nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) .

>[AZURE.NOTE] Cet article explique comment exécuter une charge de travail Linux MPI avec le HPC Pack. Il suppose que vous être familiarisé avec l’administration du système Linux et l’exécution des charges de travail MPI dans les clusters Linux. Si vous utilisez des versions de MPI et OpenFOAM différente de celles indiquées dans cet article, vous devrez peut-être modifier quelques étapes d’installation et de configuration. 

## <a name="prerequisites"></a>Conditions préalables

*   **Nœuds de cluster HPC Pack avec Linux compatibles RDMA** - déployer un cluster HPC Pack avec taille A8, A9, H16r ou H16rm Linux nœuds de calcul à l’aide d’un [modèle de gestionnaire de ressources Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou un [script PowerShell d’Azure](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Pour les composants requis et les étapes à suivre pour chaque option, consultez [mise en route avec les nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) . Si vous choisissez l’option de déploiement de script PowerShell, consultez l’exemple de fichier de configuration dans les fichiers d’exemple à la fin de cet article. Cette configuration permet de déployer un cluster basé sur Azure HPC Pack consistant en un nœud de tête de taille A8 Windows Server 2012 R2 et les nœuds de calcul 2 taille A8 SUSE Linux Enterprise Server 12. Substituez les valeurs appropriées pour les noms de service et d’abonnement. 

    **Autres choses à savoir**

    *   Pour les conditions de mise en réseau Linux RDMA dans Azure, consultez [H-series et calcul intensif VM d’une série](virtual-machines-windows-a8-a9-a10-a11-specs.md).

    *   Si vous utilisez l’option de déploiement de script Powershell, déployer tous les nœuds de calcul Linux dans le service d’un nuage à utiliser la connexion de réseau RDMA.

    *   Après avoir déployé les nœuds Linux, connectez-vous en SSH pour effectuer d’autres tâches administratives. Rechercher les détails de connexion SSH pour chaque VM de Linux dans le portail Azure.  
        
*   **Les MPI Intel** - pour exécuter OpenFOAM sur SLES 12 HPC nœuds de calcul dans Azure, vous devez installer le runtime de 5 de bibliothèque MPI Intel à partir du [site de Intel.com](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 est préinstallé sur les images de base CentOS HPC).  Dans une étape ultérieure, si nécessaire, installez les MPI Intel sur les nœuds de calcul Linux. Pour préparer cette étape, une fois que vous enregistrez avec Intel, suivez le lien dans le courriel de confirmation à la page web associée. Ensuite, copiez le lien de téléchargement du fichier .tgz pour la version appropriée d’Intel. Cet article est basé sur Intel MPI version 5.0.3.048.

*   **Pack de Source OpenFOAM** - télécharger le logiciel Pack de OpenFOAM Source pour Linux à partir du [site OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). Cet article est basé sur la version du Pack Source 2.3.1, disponible en téléchargement en tant que OpenFOAM-2.3.1.tgz. Suivez les instructions fournies plus loin dans cet article pour décompresser et compiler OpenFOAM sur les nœuds de calcul Linux.

*   **EnSight** (facultatif) - pour voir les résultats de votre simulation de OpenFOAM, de télécharger et d’installer le programme de visualisation et d’analyse [EnSight](https://www.ceisoftware.com/download/) . Les informations de licence et de téléchargement sont sur le site EnSight.


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Définir une approbation mutuelle entre les nœuds de calcul

Exécution d’un travail de cross-nœud sur plusieurs nœuds Linux requiert les nœuds s’approuvent mutuellement (de **rsh** ou **ssh**). Lorsque vous créez le cluster HPC Pack avec le script de déploiement de Microsoft HPC Pack IaaS, le script définit automatiquement une confiance réciproque permanente pour le compte d’administrateur que vous spécifiez. Pour les utilisateurs non administrateurs que vous créez dans les domaine du cluster, vous devez configurer temporaire confiance mutuelle entre les nœuds lorsqu’une tâche leur est allouée et détruisez la relation une fois la tâche terminée. Pour établir l’approbation pour chaque utilisateur, fournir une paire de clés RSA vers le cluster HPC Pack utilise pour la relation d’approbation.

### <a name="generate-an-rsa-key-pair"></a>Générer une paire de clés RSA

Il est facile de générer une paire de clés RSA, qui contient une clé publique et une clé privée, en exécutant la commande **ssh keygen** de Linux.

1.  Ouvrez une session sur un ordinateur Linux.

2.  Exécutez la commande suivante :

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Appuyez sur **entrée** pour utiliser les paramètres par défaut jusqu'à ce que la commande est exécutée. N’entrez pas un mot de passe ici ; Lorsque vous y êtes invité pour un mot de passe, appuyez simplement sur **entrée**.

    ![Générer une paire de clés RSA][keygen]

3.  Changez de répertoire vers le répertoire ~/.ssh. La clé privée est stockée dans id_rsa et la clé publique dans id_rsa.pub.

    ![Clés publiques et privées][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Ajouter la paire de clés pour le cluster HPC Pack
1.  Établir une connexion Bureau à distance pour votre nœud de tête avec votre compte d’administrateur de HPC Pack (le compte administrateur que vous configurez lorsque vous avez exécuté le script de déploiement).

2. Utilisez les procédures standard de Windows Server pour créer un compte d’utilisateur de domaine dans le domaine du répertoire actif du cluster. Par exemple, utiliser l’outil Active Directory utilisateurs et ordinateurs sur le nœud principal. Les exemples de cet article supposent que vous créez un utilisateur de domaine nommé hpclab\hpcuser.

3.  Créez un fichier nommé C:\cred.xml et copiez les données de clé RSA. Un exemple de fichier cred.xml se trouve à la fin de cet article.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.  Ouvrez une invite de commandes et entrez la commande suivante pour définir les données d’informations d’identification pour le compte hpclab\hpcuser. Le paramètre **extendeddata** vous permet de passer le nom de fichier C:\cred.xml que vous avez créé pour les données de clé.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Cette commande a été exécutée sans sortie. Une fois les informations d’identification pour les comptes d’utilisateur que vous avez besoin pour exécuter les travaux, stocker le fichier cred.xml dans un endroit sûr, ou supprimez-le.

5.  Si vous avez généré la paire de clés RSA sur l’un de vos nœuds de Linux, pensez à supprimer les clés lorsque vous avez terminé de les utiliser. Si le HPC Pack détecte un fichier id_rsa ou le fichier de id_rsa.pub, il ne définit pas une approbation mutuelle.

>[AZURE.IMPORTANT] Nous ne recommande pas l’exécution d’un travail de Linux en tant qu’un administrateur de cluster sur un cluster partagé, car un projet soumis par un administrateur s’exécute sous le compte racine sur les nœuds de Linux. Toutefois, un projet soumis par un utilisateur non administrateur s’exécute sous un compte d’utilisateur Linux local avec le même nom que l’utilisateur de la tâche. Dans ce cas, HPC Pack définit une confiance mutuelle pour cet utilisateur Linux sur les nœuds affectés à la tâche. Vous pouvez définir l’utilisateur Linux manuellement sur les nœuds de Linux avant d’exécuter la tâche ou HPC Pack crée automatiquement l’utilisateur lors de l’envoi du travail. Si l’utilisateur crée des HPC Pack, HPC Pack supprime une fois la tâche terminée. Pour réduire les menaces de sécurité, HPC Pack supprime les clés après l’achèvement de la tâche.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurez un partage de fichier pour les nœuds Linux

Définir un partage SMB standard sur un dossier dans le nœud de tête. Pour autoriser les nœuds Linux d’accéder aux fichiers d’application avec un chemin d’accès commun, montez le dossier partagé sur les nœuds de Linux. Si vous le souhaitez, vous pouvez utiliser un autre fichier partage option, comme un partage de fichiers d’Azure - recommandé pour nombreux scénarios - ou un partage NFS. Consultez le fichier à partager des informations et des instructions détaillées dans [mise en route avec les nœuds de calcul Linux dans des clusters HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Créez un dossier sur le nœud de tête et le partager à tout le monde en définissant des privilèges de lecture/écriture. Par exemple, partager des C:\OpenFOAM sur le nœud principal en tant que \\ \\SUSE12RDMA-HN\OpenFOAM. Ici, *HN-SUSE12RDMA* est le nom d’hôte du nœud principal.

2.  Ouvrez une fenêtre de Windows PowerShell et exécutez les commandes suivantes :

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

La première commande crée un dossier nommé /openfoam sur tous les nœuds dans le groupe LinuxNodes. La deuxième commande monte le dossier partagé //SUSE12RDMA-HN/OpenFOAM sur les nœuds de Linux avec dir_mode et file_mode bits la valeur 777. Le *nom d’utilisateur* et le *mot de passe* dans la commande doivent être les informations d’identification d’un utilisateur sur le nœud principal.

>[AZURE.NOTE]La "\`" symbole dans la deuxième commande est un symbole d’échappement pour PowerShell. «\`, » signifie le «, » (virgule) fait partie de la commande.

## <a name="install-mpi-and-openfoam"></a>Installer des MPI et OpenFOAM

Pour exécuter OpenFOAM comme une tâche MPI sur le réseau RDMA, vous devez compiler OpenFOAM avec les bibliothèques Intel MPI. 

Commencez par exécuter plusieurs commandes **clusrun** pour installer les bibliothèques Intel MPI (le cas échéant) et OpenFOAM sur les nœuds de Linux. Utiliser le partage de nœud principal configuré précédemment pour partager les fichiers d’installation sur les nœuds de Linux.

>[AZURE.IMPORTANT]Ces étapes de compilation et d’installation sont des exemples. Vous avez besoin des connaissances de l’administration du système Linux pour s’assurer que les bibliothèques et compilateurs dépendants sont correctement installés. Vous devrez peut-être modifier certaines variables d’environnement ou d’autres paramètres de vos versions de MPI d’Intel et OpenFOAM. Pour plus d’informations, consultez [Bibliothèque de MPI Intel® pour Linux Installation Guide](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) et [Installation du Pack Source OpenFOAM](http://openfoam.org/download/2-3-1-source/) pour votre environnement.


### <a name="install-intel-mpi"></a>Installer Intel MPI

Enregistrer le package d’installation téléchargé pour Intel MPI (l_mpi_p_5.0.3.048.tgz dans cet exemple) dans C:\OpenFoam sur le nœud principal afin que les nœuds Linux peuvent accéder à ce fichier à partir de /openfoam. Puis exécutez **clusrun** pour installer la bibliothèque Intel MPI sur tous les nœuds de Linux.

1.  Les commandes suivantes, copiez le package d’installation et extraire vers /opt/intel sur chaque nœud.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Pour installer la bibliothèque de MPI Intel en mode silencieux, utilisez un fichier silent.cfg. Vous trouverez un exemple dans les fichiers d’exemple à la fin de cet article. Placez ce fichier dans le dossier partagé /openfoam. Pour plus d’informations sur le fichier silent.cfg, reportez-vous à la section [Bibliothèque de MPI Intel® pour Linux Installation Guide - Installation en mode silencieux](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).

    >[AZURE.TIP]Assurez-vous que vous enregistrez votre fichier silent.cfg sous la forme d’un fichier texte avec Linux (LF uniquement, pas de CR LF) de fins de ligne. Cette étape permet de s’assurer qu’il s’exécute correctement sur les nœuds de Linux.

3.  Installez la bibliothèque de MPI Intel en mode silencieux.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### <a name="configure-mpi"></a>Configurer les MPI

Pour le test, vous devez ajouter les lignes suivantes à la /etc/security/limits.conf sur chacun des nœuds Linux :


    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Redémarrez les nœuds Linux une fois que vous mettez à jour le fichier limits.conf. Par exemple, utilisez la commande **clusrun** suivante :

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Après le redémarrage, assurez-vous que le dossier partagé est monté en tant que /openfoam.

### <a name="compile-and-install-openfoam"></a>Compiler et installer OpenFOAM

Enregistrer le package d’installation téléchargé pour le Pack de Source OpenFOAM (OpenFOAM-2.3.1.tgz dans cet exemple) à C:\OpenFoam sur le nœud principal afin que les nœuds Linux peuvent accéder à ce fichier à partir de /openfoam. Puis exécutez les commandes **clusrun** pour compiler OpenFOAM sur tous les nœuds de Linux.


1.  Créer un dossier /opt/OpenFOAM sur chaque nœud de Linux, copiez le package source dans ce dossier et il l’extraire.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
    
    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Pour compiler OpenFOAM avec la Bibliothèque MPI Intel, d’abord définir certaines variables d’environnement pour les MPI Intel et OpenFOAM. Permet de définir les variables d’un script bash appelé settings.sh. Vous trouverez un exemple dans les fichiers d’exemple à la fin de cet article. Placez ce fichier (à des fins de ligne Linux) dans le dossier partagé /openfoam. Ce fichier contient également des paramètres pour les runtimes MPI et OpenFOAM que vous utilisez une version ultérieure pour exécuter une tâche OpenFOAM.

3. Installez les packages dépendants nécessaires à la compilation OpenFOAM. Selon votre distribution Linux, vous devrez tout d’abord ajouter un référentiel. Exécutez les commandes **clusrun** semblables à la suivante :

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    Si nécessaire, SSH à chaque nœud de Linux pour exécuter les commandes pour confirmer qu’ils s’exécutent correctement.

4.  Exécutez la commande suivante pour compiler OpenFOAM. Le processus de compilation prend du temps et génère une grande quantité d’informations de journal pour la sortie standard, donc d’utiliser l’option **/ l’interleaved** pour afficher la sortie entrelacée.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]La "\`" symbole dans la commande est un symbole d’échappement pour PowerShell. «\`& » signifie le « & » fait partie de la commande.

## <a name="prepare-to-run-an-openfoam-job"></a>Préparation à l’exécution d’une tâche OpenFOAM

Préparez-vous maintenant exécuter une tâche MPI appelée sloshingTank3D, qui est un des exemples de OpenFoam, sur les deux nœuds de Linux. 

### <a name="set-up-the-runtime-environment"></a>Configurer l’environnement d’exécution

Pour configurer les environnements runtime de MPI et OpenFOAM sur les nœuds de Linux, exécutez la commande suivante dans une fenêtre Windows PowerShell sur le nœud principal. (Cette commande est valide pour SUSE Linux uniquement.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Préparer des exemples de données

Utiliser le partage de nœud principal que vous avez configurés précédemment pour partager des fichiers entre les nœuds de Linux (montés en tant que /openfoam).

1.  Nœuds de calcul SSH à l’un de vos Linux.

2.  Exécutez la commande suivante pour configurer l’environnement d’exécution de OpenFOAM, si vous ne l’avez pas déjà fait.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Copiez l’exemple sloshingTank3D dans le dossier partagé et naviguer jusqu'à lui.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Lorsque vous utilisez les paramètres par défaut de cet exemple, il peut prendre des dizaines de minutes pour s’exécuter, vous pouvez donc choisir de modifier certains paramètres pour accélérer son exécution. Un choix simple est de modifier le temps étape variables deltaT et writeInterval dans le fichier système/controlDict. Ce fichier stocke toutes les données d’entrée relatives au contrôle du temps et de lire et d’écrire des données de la solution. Par exemple, vous pouvez modifier la valeur de deltaT à partir de 0,05 à 0,5 et la valeur de writeInterval à partir de 0,05 à 0,5.

    ![Modifier les variables de l’étape][step_variables]

5.  Spécifiez les valeurs souhaitées pour les variables dans le fichier système/decomposeParDict. Cet exemple utilise deux nœuds Linux chaque avec 8 noyaux, donc la valeur numberOfSubdomains 16 et n de hierarchicalCoeffs à (1 1 16), qui signifie exécuter OpenFOAM en parallèle avec les 16 processus. Pour plus d’informations, consultez [OpenFOAM Guide de l’utilisateur : 3.4 applications en cours d’exécution en parallèle](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Décomposer les processus][decompose]

6.  Exécutez les commandes suivantes à partir du répertoire sloshingTank3D pour préparer les données d’exemple.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  Sur le nœud principal, vous devez voir que les exemples de fichiers de données sont copiés dans C:\OpenFoam\sloshingTank3D. (C:\OpenFoam est le dossier partagé sur le nœud principal.)

    ![Fichiers de données sur le nœud de tête][data_files]

### <a name="host-file-for-mpirun"></a>Fichier hôte de mpirun

Dans cette étape, vous créez un fichier d’hôte (il s’agit d’une liste de nœuds de calcul) qui utilise de la commande **mpirun** .

1.  Sur l’un des nœuds Linux, créez un fichier nommé fichier_hôte sous /openfoam, afin que ce fichier peut être contacté à /openfoam/hostfile sur tous les nœuds de Linux.

2.  Écrire vos noms de nœud de Linux dans ce fichier. Dans cet exemple, le fichier contient les noms suivants :
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]Vous pouvez également créer ce fichier à C:\OpenFoam\hostfile sur le nœud principal. Si vous choisissez cette option, enregistrez-le dans un fichier texte avec Linux fins de ligne (LF uniquement, pas de CR LF). Cela garantit qu’il s’exécute correctement sur les nœuds de Linux.

    **Bash script wrapper**

    Si vous avez de nombreux nœuds de Linux et que vous voulez que votre travail s’exécuter que sur certaines d'entre elles, il n’est pas une bonne idée d’utiliser un fichier hôte fixe, car vous ne connaissez pas les nœuds qui sont affectés à votre travail. Dans ce cas, écrire un wrapper de script bash pour **mpirun** créer automatiquement le fichier hôte. Vous pouvez trouver un wrapper de script exemple bash appelé hpcimpirun.sh à la fin de cet article et enregistrez-le sous /openfoam/hpcimpirun.sh. Cet exemple de script effectue les opérations suivantes :

    1.  Définit les variables d’environnement pour **mpirun**et certains paramètres de la commande Ajout pour exécuter la tâche MPI à travers le réseau RDMA. Dans ce cas, il définit les variables suivantes :

        *   I_MPI_FABRICS = shm:dapl
        *   I_MPI_DAPL_PROVIDER = ofa-ib0-v2
        *   I_MPI_DYNAMIC_CONNECTION = 0

    2.  Crée un fichier d’hôte en fonction de l’environnement variable $CCP_NODES_CORES, qui est définie par le nœud principal HPC lorsque le travail est activé.

        Le format de $CCP_NODES_CORES suit ce modèle :

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        où

        * `<Number of nodes>`-le nombre de nœuds affectés à cette tâche.  
        
        * `<Name of node_n_...>`-le nom de chaque nœud affecté à cette tâche.
        
        * `<Cores of node_n_...>`-le nombre de cœurs sur le nœud affecté à cette tâche.

        Par exemple, si le projet a besoin de deux nœuds pour lesquels exécuter, $CCP_NODES_CORES est similaire à
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.  Appelle la commande **mpirun** et ajoute deux paramètres à la ligne de commande.

        * `--hostfile <hostfilepath>: <hostfilepath>`-le chemin d’accès du fichier hôte crée par le script

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`-une variable d’environnement définie par le nœud principal HPC Pack, qui stocke le nombre de cœurs total alloué à cette tâche. Dans ce cas, il indique le nombre de processus de **mpirun**.


## <a name="submit-an-openfoam-job"></a>Soumettre une tâche OpenFOAM

Maintenant, vous pouvez soumettre une tâche de HPC Cluster Manager. Vous devez passer le script hpcimpirun.sh dans les lignes de commande pour certaines tâches.

1. Connectez-vous à votre nœud de tête du cluster et démarrer HPC Cluster Manager.

2. **Dans la gestion des ressources**, assurez-vous que les nœuds de calcul Linux sont dans l’état **en ligne** . S’ils ne le sont pas, sélectionnez-les et cliquez sur **Mettre en ligne**.

3.  **Gestion des tâches**, cliquez sur **Nouvelle tâche**.

4.  Entrez un nom de travail par exemple, _sloshingTank3D_.

    ![Détails de la tâche][job_details]

5.  Dans **les ressources de travail**, choisissez le type de ressource en tant que « Nœud » et la valeur 2 au Minimum. Cette configuration s’exécute la tâche sur les deux nœuds de Linux, chacun d’eux ayant huit cœurs dans cet exemple.

    ![Ressources de travail][job_resources]

6. Cliquez sur **Modifier les tâches** dans la navigation de gauche, puis cliquez sur **Ajouter** pour ajouter une tâche à la tâche. Ajoutez quatre tâches à la tâche avec les lignes de commande et les paramètres suivants.

    >[AZURE.NOTE]Exécution de `source /openfoam/settings.sh` configure les environnements runtime OpenFOAM et MPI, pour chacune des tâches suivants l’appelle avant la commande OpenFOAM.

    *   **La tâche 1**. Exécutez **decomposePar** pour générer des fichiers de données de **interDyMFoam** en cours d’exécution en parallèle.
    
        *   Affecter un seul nœud à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Répertoire de travail** - / openfoam/sloshingTank3D
        
        Voir la figure suivante. Vous configurez les tâches restantes de la même façon.

        ![Détails de la tâche 1][task_details1]

    *   **La tâche 2**. Exécutez **interDyMFoam** en parallèle pour calculer l’échantillon.

        *   Affecter des deux nœuds à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Répertoire de travail** - / openfoam/sloshingTank3D

    *   **Tâche 3**. Exécutez **reconstructPar** pour fusionner les jeux de répertoires de temps à partir de chaque répertoire de processor_N_ en un seul jeu.

        *   Affecter un seul nœud à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Répertoire de travail** - / openfoam/sloshingTank3D

    *   **La tâche 4**. Exécutez **foamToEnsight** en parallèle pour convertir les fichiers de résultats de OpenFOAM de format d’EnSight et de placer les fichiers de EnSight dans un répertoire nommé Ensight dans le répertoire cas.

        *   Affecter des deux nœuds à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Répertoire de travail** - / openfoam/sloshingTank3D

6.  Ajouter des dépendances à ces tâches dans l’ordre des tâches dans l’ordre croissant.

    ![Interdépendances des tâches][task_dependencies]

7.  Cliquez sur **Soumettre** pour exécuter cette tâche.

    Par défaut, HPC Pack soumet le travail que votre compte d’utilisateur connecté actuel. Une fois que vous cliquez sur **Envoyer**, vous pouvez voir une boîte de dialogue vous invitant à entrer le nom d’utilisateur et le mot de passe.

    ![Informations d’identification de la tâche][creds]

    Sous certaines conditions, le HPC Pack garde en mémoire les informations utilisateur d’entrée avant de ne plus afficher cette boîte de dialogue. Pour afficher à nouveau HPC Pack, entrez la commande suivante à une invite de commande et puis envoyer la tâche.

    ```
    hpccred delcreds
    ```

8.  Le travail est de quelques dizaines de minutes à plusieurs heures en fonction des paramètres que vous avez défini pour l’exemple. Dans la carte thermique, vous consultez la tâche en cours d’exécution sur les nœuds de Linux. 

    ![Carte thermique][heat_map]

    Sur chaque nœud, huit processus démarrés.

    ![Processus Linux][linux_processes]

9.  Lorsque la tâche se termine, rechercher les résultats des travaux dans les dossiers C:\OpenFoam\sloshingTank3D et les fichiers journaux à C:\OpenFoam.


## <a name="view-results-in-ensight"></a>Afficher les résultats dans EnSight

Utiliser [EnSight](https://www.ceisoftware.com/) pour visualiser et analyser les résultats de la tâche OpenFOAM. Pour plus d’informations sur la visualisation et animation dans EnSight, consultez ce [guide vidéo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Après l’installation de EnSight sur le nœud de tête, démarrez-le.

2.  Ouvrez C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.

    Vous consultez une cuve dans la visionneuse.

    ![Réservoir de EnSight][tank]

3.  Créer une **Isosurface** à partir de **internalMesh**et choisissez variable **alpha_water**.

    ![Créer une isosurface][isosurface]

4.  Définir la couleur pour **Isosurface_part** créé à l’étape précédente. Par exemple, définir l’eau bleu.

    ![Modifier la couleur de l’isosurface][isosurface_color]

5.  Créer un **volume-Iso** à partir de **murs** en sélectionnant des **murs** dans le panneau **composants** et cliquez sur le bouton **Isosurfaces** dans la barre d’outils.

6.  Dans la boîte de dialogue, sélectionnez **Type** **d’Isovolume** et minimum de la **plage de Isovolume** la valeur 0,5. Pour créer l’isovolume, cliquez sur **créer avec les pièces sélectionnées**.

7.  Définir la couleur pour **Iso_volume_part** créé à l’étape précédente. Par exemple, définissez-la pour l’eau profonde bleu.

8.  Définir la couleur pour que les **murs**. Par exemple, défini sur blanc transparent.

9. Cliquez sur **lire** pour voir les résultats de la simulation.

    ![Résultat de réservoir][tank_result]

## <a name="sample-files"></a>Fichiers d’exemple

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Exemple de fichier de configuration XML pour le déploiement du cluster par script PowerShell

 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>Exemple de fichier cred.xml

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>Exemple de fichier silent.cfg pour installer MPI

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Exemple de script settings.sh

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###<a name="sample-hpcimpirunsh-script"></a>Exemple de script hpcimpirun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/linux_processes.png
