<properties
 pageTitle="NAMD avec Microsoft HPC Pack Linux VMs | Microsoft Azure"
 description="Déployer un cluster Microsoft HPC Pack sur Azure et d’exécuter une simulation de NAMD avec charmrun sur plusieurs nœuds de calcul Linux"
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
 ms.date="10/13/2016"
 ms.author="danlep"/>

# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Exécutez NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure

Cet article vous montre une façon d’exécuter une charge de travail informatique à hautes performances (HPC) Linux sur les machines virtuelles Azure. Ici, vous paramétrez un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) sur Azure avec les nœuds de calcul Linux et d’exécuter une simulation de [NAMD](http://www.ks.uiuc.edu/Research/namd/) permettant de calculer et de visualiser la structure d’un système de grande BIOMOLÉCULAIRE.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (pour le programme de Dynamics moléculaire d’échelle nanométrique) est un package de parallèle dynamics moléculaire conçu pour la simulation de hautes performances des systèmes de grande BIOMOLÉCULAIRE contenant jusqu'à des millions d’atomes. Virus, structures de cellules et protéines de grande taille sont des exemples de ces systèmes. NAMD peut évoluer vers des centaines de cœurs pour les simulations typiques et à plus de 500 000 cœurs pour les simulations plus grandes.

* **Microsoft HPC Pack** fournit les fonctionnalités pour exécuter des applications parallèles et HPC à grande échelle dans les clusters d’ordinateurs des locaux ou des machines virtuelles Azure. Développé à l’origine en tant que solution pour les charges de travail de Windows HPC, HPC Pack prend désormais en charge exécutant des applications Linux HPC sur Linux compute VMs nœud déployés dans un cluster HPC Pack. Pour plus d’informations, consultez [mise en route avec les nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) .

Pour d’autres options exécuter des charges de travail Linux HPC dans Azure, reportez-vous à la section [des ressources techniques pour le lot et hautes performances informatique](../batch/batch-hpc-solutions.md).




## <a name="prerequisites"></a>Conditions préalables

* **Nœuds de cluster HPC Pack avec Linux** - déployer un cluster HPC Pack avec les nœuds de calcul Linux sur Azure à l’aide d’un [modèle de gestionnaire de ressources Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou un [script PowerShell d’Azure](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Pour les composants requis et les étapes à suivre pour chaque option, consultez [mise en route avec les nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) . Si vous choisissez l’option de déploiement de script PowerShell, consultez l’exemple de fichier de configuration dans les fichiers d’exemple à la fin de cet article. Ce fichier configure un cluster basé sur Azure HPC Pack consistant en un nœud principal de Windows Server 2012 R2 et quatre nœuds de calcul de grande taille CentOS 6.6 taille. Personnaliser ce fichier, en fonction des besoins de votre environnement.


* **Les fichiers logiciels et didacticiel NAMD** - logiciel de NAMD de téléchargement pour Linux à partir du site [NAMD](http://www.ks.uiuc.edu/Research/namd/) (inscription obligatoire). Cet article est basé sur le NAMD version 2.10 et utilise le fichier d’archives [Linux-x86_64 64 bits Intel/AMD (avec Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) . Également télécharger les [fichiers du didacticiel de NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Les téléchargements sont les fichiers .tar, et vous avez besoin d’un outil de Windows pour extraire les fichiers sur le nœud principal du cluster. Pour extraire les fichiers, suivez les instructions fournies plus loin dans cet article. 

* **VDM** (facultatif) - pour afficher les résultats de votre travail NAMD, téléchargez et installez le programme de visualisation moléculaire [VDM](http://www.ks.uiuc.edu/Research/vmd/) sur un ordinateur de votre choix. La version actuelle est 1.9.2. Consultez le VDM télécharger le site pour commencer.  


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Définir une approbation mutuelle entre les nœuds de calcul
Exécution d’un travail de cross-nœud sur plusieurs nœuds Linux requiert les nœuds s’approuvent mutuellement (de **rsh** ou **ssh**). Lorsque vous créez le cluster HPC Pack avec le script de déploiement de Microsoft HPC Pack IaaS, le script définit automatiquement une confiance réciproque permanente pour le compte d’administrateur que vous spécifiez. Pour les utilisateurs non administrateurs que vous créez dans les domaine du cluster, vous devez configurer temporaire confiance mutuelle entre les nœuds lorsqu’une tâche leur est allouée. Puis, détruisez la relation une fois la tâche terminée. Pour ce faire, pour chaque utilisateur, fournir une paire de clés RSA pour le cluster HPC Pack utilise pour établir la relation d’approbation. Instructions ci-dessous.

### <a name="generate-an-rsa-key-pair"></a>Générer une paire de clés RSA
Il est facile de générer une paire de clés RSA, qui contient une clé publique et une clé privée, en exécutant la commande **ssh keygen** de Linux.

1.  Ouvrez une session sur un ordinateur Linux.

2.  Exécutez la commande suivante :

    ```bash
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Appuyez sur **entrée** pour utiliser les paramètres par défaut jusqu'à ce que la commande est exécutée. N’entrez pas un mot de passe ici ; Lorsque vous y êtes invité pour un mot de passe, appuyez simplement sur **entrée**.

    ![Générer une paire de clés RSA][keygen]

3.  Changez de répertoire vers le répertoire ~/.ssh. La clé privée est stockée dans id_rsa et la clé publique dans id_rsa.pub.

    ![Clés publiques et privées][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Ajouter la paire de clés pour le cluster HPC Pack
1.  [Connexion Bureau à distance](virtual-machines-windows-connect-logon.md) pour le nœud de tête VM le domaine informations d’identification fournies lorsque vous avez déployé le cluster (par exemple, hpc\clusteradmin). Vous gérez le cluster du nœud de tête.

2. Utilisez les procédures standard de Windows Server pour créer un compte d’utilisateur de domaine dans le domaine du répertoire actif du cluster. Par exemple, utiliser l’outil Active Directory utilisateurs et ordinateurs sur le nœud principal. Les exemples de cet article supposent que vous créez un utilisateur de domaine nommé hpcuser dans le domaine de la hpclab (hpclab\hpcuser).

3. Ajoutez l’utilisateur de domaine pour le cluster HPC Pack en tant qu’un utilisateur de cluster. Pour plus d’informations, consultez [Ajouter ou supprimer les utilisateurs du cluster](https://technet.microsoft.com/library/ff919330.aspx).

2.  Créez un fichier nommé C:\cred.xml et copiez les données de clé RSA. Vous trouverez un exemple dans les fichiers d’exemple à la fin de cet article.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  Ouvrez une invite de commandes et entrez la commande suivante pour définir les données d’informations d’identification pour le compte hpclab\hpcuser. Le paramètre **extendeddata** vous permet de passer le nom d’un fichier C:\cred.xml que vous avez créé pour les données de clé.

    ```command
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Cette commande a été exécutée sans sortie. Une fois les informations d’identification pour les comptes d’utilisateur que vous avez besoin pour exécuter les travaux, stocker le fichier cred.xml dans un endroit sûr, ou supprimez-le.

5.  Si vous avez généré la paire de clés RSA sur l’un de vos nœuds de Linux, pensez à supprimer les clés lorsque vous avez terminé de les utiliser. HPC Pack n’a pas défini une confiance mutuelle s’il trouve un fichier id_rsa ou le fichier de id_rsa.pub.

>[AZURE.IMPORTANT] Nous ne recommande pas l’exécution d’un travail de Linux en tant qu’un administrateur de cluster sur un cluster partagé, car un projet soumis par un administrateur s’exécute sous le compte racine sur les nœuds de Linux. Un projet soumis par un utilisateur non administrateur s’exécute sous un compte d’utilisateur Linux local avec le même nom que l’utilisateur de la tâche. Dans ce cas, HPC Pack définit une confiance mutuelle pour cet utilisateur Linux sur tous les nœuds affectés à la tâche. Vous pouvez définir l’utilisateur Linux manuellement sur les nœuds de Linux avant d’exécuter la tâche ou HPC Pack crée automatiquement l’utilisateur lors de l’envoi du travail. Si l’utilisateur crée des HPC Pack, HPC Pack supprime une fois la tâche terminée. Pour réduire les risques de sécurité, les clés sont supprimées après la fin du travail sur les nœuds.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurez un partage de fichier pour les nœuds Linux

Maintenant configurer un partage de fichiers SMB et le monter le dossier partagé sur tous les nœuds de Linux pour permettre les nœuds Linux d’accéder aux fichiers NAMD avec un chemin d’accès commun. Voici les étapes pour monter un dossier partagé sur le nœud principal. Un partage est recommandé pour les distributions comme CentOS 6.6 qui actuellement ne prennent en charge le service de fichiers d’Azure. Si vos nœuds Linux prend en charge un partage de fichiers d’Azure, voir [comment utiliser le stockage de fichier Azure avec Linux](../storage/storage-how-to-use-files-linux.md). Pour les options de partage avec le HPC Pack des fichiers supplémentaires, consultez [mise en route avec les nœuds de calcul Linux dans des clusters HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Créez un dossier sur le nœud de tête et le partager à tout le monde en définissant des privilèges de lecture/écriture. Dans cet exemple, \\ \\CentOS66HN\Namd est le nom du dossier, où CentOS66HN est le nom d’hôte du nœud principal.

2. Créez un sous-dossier nommé namd2 dans le dossier partagé. Dans namd2, créez un autre sous-dossier nommé namdsample.

3. Extrayez les fichiers dans le dossier NAMD à l’aide d’une version Windows de **tar** ou un autre utilitaire de Windows qui fonctionne sur les archives .tar. 
    * Décompactez l’archive tar NAMD à \\ \\CentOS66HN\Namd\namd2.
    
    * Extraire les fichiers du didacticiel sous \\ \\CentOS66HN\Namd\namd2\namdsample.

4. Ouvrez une fenêtre de Windows PowerShell et exécutez les commandes suivantes pour monter le dossier partagé sur les nœuds de Linux.

    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

La première commande crée un dossier nommé /namd2 sur tous les nœuds dans le groupe LinuxNodes. La deuxième commande monte le dossier partagé //CentOS66HN/Namd/namd2 sur le dossier avec dir_mode et file_mode bits la valeur 777. Le *nom d’utilisateur* et le *mot de passe* dans la commande doivent être les informations d’identification d’un utilisateur sur le nœud principal.

>[AZURE.NOTE]La "\`" symbole dans la deuxième commande est un symbole d’échappement pour PowerShell. «\`, » signifie le «, » (virgule) fait partie de la commande.


## <a name="create-a-bash-script-to-run-a-namd-job"></a>Créer un script Bash pour exécuter une tâche NAMD

Votre travail NAMD requiert un fichier de *nodelist* pour **charmrun** déterminer le nombre de nœuds à utiliser lors du démarrage du processus NAMD. Vous utilisez un script Bash qui génère le fichier de nodelist et exécute des **charmrun** avec ce fichier nodelist. Vous pouvez ensuite soumettre un travail NAMD de HPC Cluster Manager qui appelle ce script.

À l’aide d’un éditeur de texte de votre choix, créez un script Bash dans le dossier /namd2 qui contient les fichiers de programme NAMD et nommez-le hpccharmrun.sh. Pour un contrôle rapide du concept, copier l’exemple de script hpccharmrun.sh fourni à la fin de cet article et cliquez sur [soumettre un travail NAMD](#submit-a-namd-job).

>[AZURE.TIP] Enregistrer votre script dans un fichier texte avec Linux ligne fins (LF uniquement, pas de CR LF). Cela garantit qu’il s’exécute correctement sur les nœuds de Linux.

Voici plus de détails sur ce que fait ce script bash. 

1.  Définir des variables.

    ```bash
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  Obtenir des informations de nœud dans les variables d’environnement. $NODESCORES stocke une liste de séparation de mots à partir de $CCP_NODES_CORES. $COUNT est la taille de $NODESCORES.
    ```
    # Get node information from the environment variables
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```    
    
    Le format pour la variable CCP_NODES_CORES est la suivante :

    ```
    <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
    ```

    Cette variable donne le nombre total de nœuds, les noms de nœud et le nombre de cœurs sur chaque nœud qui sont affectées à la tâche. Par exemple, si la tâche doit 10 cœurs d’exécution, la valeur de $CCP_NODES_CORES est similaire à :

    ```
    3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
    ```
        
3.  Si la variable CCP_NODES_CORES n’est pas définie, démarrez **charmrun** directement. (Ceci doit uniquement se produire lorsque vous exécutez ce script directement sur vos nœuds Linux.)

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  Ou créez un fichier nodelist pour **charmrun**.

    ```
    else
        # Create the nodelist file
        NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

        # Write the head line
        echo "group main" > ${NODELIST_PATH}

        # Get every node name and number of cores and write into the nodelist file
        I=1
        while [ ${I} -lt ${COUNT} ]
        do
            echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
            let "I=${I}+2"
        done
```
5.  Exécutez **charmrun** avec le fichier nodelist, obtenir son état de retour et supprimer le fichier nodelist à la fin.

    ${CCP_NUMCPUS} est une autre variable d’environnement définie par le nœud principal HPC Pack. Il stocke le nombre de cœurs total alloué à cette tâche. Nous l’utiliser pour spécifier le nombre de processus de charmrun.

    ```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi

    ```
6.  Sortie avec le statut de retour de **charmrun** .

    ```
    exit ${RTNSTS}
    ```



Voici les informations contenues dans le fichier nodelist, le script génère :

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Par exemple :

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Soumettre une tâche NAMD

Vous êtes maintenant prêt à envoyer un travail de NAMD de HPC Cluster Manager.

1.  Connectez-vous à votre nœud de tête du cluster et démarrer HPC Cluster Manager.

2.  Dans la **Gestion des ressources**, assurez-vous que les nœuds de calcul Linux sont dans l’état **en ligne** . S’ils ne le sont pas, sélectionnez-les et cliquez sur **Mettre en ligne**.

2.  **Gestion des tâches**, cliquez sur **Nouvelle tâche**.

3.  Entrez un nom de travail par exemple, *hpccharmrun*.

    ![Nouveau travail HPC][namd_job]

4.  Sur la page de **Détails de la tâche** , sous **Ressources de travail**, sélectionnez le type de ressource en tant que **nœud** et la valeur 3 au **Minimum** . , nous exécutons le travail sur les trois nœuds de Linux et que chaque nœud dispose de quatre cœurs.

    ![Ressources de travail][job_resources]

5. Cliquez sur **Modifier les tâches** dans la navigation de gauche, puis cliquez sur **Ajouter** pour ajouter une tâche à la tâche.    


6. Sur la page **Détails de la tâche et la Redirection d’e/s** , définissez les valeurs suivantes :

    * **Ligne de commande** -
`/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

        >[AZURE.TIP] La ligne de commande précédente est une seule commande sans saut de ligne. Il encapsule pour apparaître sur plusieurs lignes sous la **ligne de commande**.

    * **Répertoire de travail** - /namd2

    * **Minimum** - 3

    ![Détails de la tâche][task_details]

    >[AZURE.NOTE] Vous définissez le répertoire de travail ici parce que **charmrun** essaie de naviguer dans le même répertoire de travail sur chaque nœud. Si le répertoire de travail n’est pas défini, le HPC Pack démarre la commande dans un dossier nommé de façon aléatoire créé sur l’un des nœuds Linux. Cela provoque l’erreur suivante sur les autres nœuds : `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` pour éviter ce problème, spécifiez un chemin d’accès du dossier qui est accessible par tous les nœuds en tant que le répertoire de travail.

5.  Cliquez sur **OK** , puis sur **Envoyer** pour exécuter cette tâche.

    Par défaut, HPC Pack soumet le travail que votre compte d’utilisateur connecté actuel. Une boîte de dialogue peut vous inviter à entrer le nom d’utilisateur et le mot de passe une fois que vous cliquez sur **Envoyer**.

    ![Informations d’identification de la tâche][creds]

    Sous certaines conditions, le HPC Pack garde en mémoire les informations utilisateur d’entrée avant de ne plus afficher cette boîte de dialogue. Pour afficher à nouveau HPC Pack, entrez la commande suivante à une invite de commande et puis envoyer la tâche.

    ```command
    hpccred delcreds
    ```

6.  La tâche prend plusieurs minutes.

7.  Trouver le journal des travaux en \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log et les fichiers de sortie dans \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.

8.  Vous pouvez également démarrer VDM pour afficher les résultats de votre travail. Les opérations de visualisation de la NAMD de sortie sont des fichiers (dans ce cas, une UBIQUITINE molecule de PROTEINE dans une sphère de l’eau) au-delà de la portée de cet article. Pour plus d’informations, consultez [Didacticiel sur NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) .

    ![Résultats des travaux][vmd_view]

## <a name="sample-files"></a>Fichiers d’exemple

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Exemple de fichier de configuration XML pour le déploiement du cluster par script PowerShell

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
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

### <a name="sample-hpccharmrunsh-script"></a>Exemple de script hpccharmrun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/creds.png
[task_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/vmd_view.png
