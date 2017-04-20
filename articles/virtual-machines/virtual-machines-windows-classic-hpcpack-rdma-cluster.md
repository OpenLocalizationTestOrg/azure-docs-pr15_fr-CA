<properties
 pageTitle="Configurer un cluster RDMA de Windows pour exécuter des applications MPI | Microsoft Azure"
 description="Apprenez à créer un cluster de Windows HPC Pack avec taille H16r, H16mr, A8 ou A9 VM à utiliser le réseau Azure RDMA pour exécuter les applications MPI."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/20/2016"
 ms.author="danlep"/>

# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configurer un cluster Windows RDMA avec le HPC Pack à exécuter des applications MPI

Configurer un cluster Windows RDMA dans Azure avec [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) et [instances de A-série H-series ou de calcul intensif](virtual-machines-windows-a8-a9-a10-a11-specs.md) pour exécuter des applications d’Interface MPI (Message Passing) parallèles. Lorsque vous définissez des nœuds prenant en charge les RDMA, basée sur Windows Server dans un cluster HPC Pack, applications MPI communiquent efficacement sur un faible temps de latence réseau haut débit dans Azure qui est basé sur la technologie direct à la mémoire à distance (RDMA) d’accès.

Si vous souhaitez exécuter des charges de travail MPI sur les ordinateurs virtuels Linux qui accèdent au réseau Azure RDMA, reportez-vous à [configurer un cluster Linux RDMA pour exécuter des applications de MPI](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="hpc-pack-cluster-deployment-options"></a>Options de déploiement du cluster HPC Pack
Microsoft HPC Pack est un outil fourni sans frais supplémentaires pour créer des clusters HPC sur site ou dans Azure pour exécuter des applications Windows ou Linux HPC. HPC Pack inclut un environnement d’exécution de l’implémentation Microsoft du Message passant Interface pour Windows (MS-MPI). Lorsqu’il est utilisé avec les instances compatibles RDMA exécutant un système d’exploitation de serveur Windows pris en charge, HPC Pack fournit une option efficace pour exécuter des applications Windows MPI qui accèdent au réseau RDMA d’Azure. 

Cet article présente deux scénarios et des liens vers des instructions détaillées pour configurer un cluster Winodws RDMA avec Microsoft HPC Pack. 

* Scénario 1. Déployer des instances de rôle de travail du calcul intensif (PaaS)

* Scénario 2. Déployer des nœuds de calcul dans les ordinateurs virtuels de calcul intensif (IaaS)

Pour obtenir des conditions requises générales à utiliser des instances de calcul intensif avec Windows, consultez [H-series et calcul intensif VM d’une série](virtual-machines-windows-a8-a9-a10-a11-specs.md) .



## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scénario 1. Déployer des instances de rôle de travail du calcul intensif (PaaS)

À partir d’un cluster HPC Pack existant, ajouter des ressources de calcul supplémentaire dans les instances de rôle de travail Azure (nœuds Azure) en cours d’exécution dans un service cloud (PaaS). Cette fonctionnalité, également appelée « burst pour Azure » de HPC Pack, prend en charge une plage de tailles pour les instances de rôle de travail. Lorsque vous ajoutez les nœuds Azure, spécifiez simplement un des formats compatibles RDMA.

Voici les considérations et les étapes de rafale à compatibles RDMA Azure instances existant (en général local) cluster. Pour ajouter des instances de rôle de travail à un nœud principal HPC Pack qui est déployé dans une machine virtuelle d’Azure, utilisez des procédures similaires.

>[AZURE.NOTE] Pour un didacticiel de rafale vers Azure avec le HPC Pack, reportez-vous à [configurer un cluster hybride avec le HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Notez les considérations dans les étapes suivantes qui s’appliquent spécifiquement aux compatibles RDMA nœuds Azure.

![En mode rafale vers Azure][burst]

### <a name="steps"></a>Étapes

4. **Déployer et configurer un nœud principal HPC Pack 2012 R2**

    Télécharger le package d’installation HPC Pack le plus récent à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Pour la configuration requise et les instructions pour préparer un déploiement d’Azure rafale, consultez [HPC Pack Guide de démarrage](https://technet.microsoft.com/library/jj884144.aspx) et de [transmission en rafale pour des Instances de travailleur Azure avec Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

5. **Configurer un certificat de gestion de l’abonnement Azure**

    Configurer un certificat pour sécuriser la connexion entre le nœud de tête et Azure. Pour les options et les procédures, voir [scénarios pour configurer le certificat de gestion Azure pour le HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Pour les déploiements de test, HPC Pack installe un par défaut Microsoft HPC Azure certificat de gestion que vous pouvez rapidement télécharger à votre abonnement Azure.

6. **Créer un nouveau service de cloud et un compte de stockage**

    Le portail classique Azure permet de créer un service cloud et un compte de stockage pour le déploiement dans une région où les instances RDMA compatibles sont disponibles.

7. **Créer un modèle de nœud Azure**

    Utilisez l’Assistant créer un nœud modèle de HPC Cluster Manager. Pour la procédure, voir [créer un modèle de nœud Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) dans « Étapes pour déployer Azure nœuds avec Microsoft HPC Pack ».

    Pour les tests préliminaires, nous vous suggérons de la configuration d’une stratégie de disponibilité manuel dans le modèle.

8. **Ajouter des nœuds au cluster**

    Utilisez le nœud Assistant d’ajout de dans le Gestionnaire de Cluster HPC. Pour plus d’informations, consultez [Ajouter des nœuds au Cluster HPC Windows Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Lorsque vous spécifiez la taille des noeuds, sélectionnez une des tailles d’instance compatibles RDMA.
    
    >[AZURE.NOTE]Dans chaque rafale pour un déploiement d’Azure avec les instances de calcul intensif, HPC Pack déploie automatiquement un minimum de 2 instances RDMA compatible (par exemple, A8) en tant que nœuds de proxy, outre les instances de rôle Azure travailleur que vous spécifiez. Les nœuds de proxy utilisent les brins qui sont affectées à l’abonnement et entraînent des frais avec les instances de rôle de travail Azure.

9. **(Disposition) de démarrer les nœuds et en ligne pour exécuter des tâches**

    Sélectionnez les nœuds et utiliser l’action de **démarrage** dans le Gestionnaire de Cluster HPC. Lorsque la configuration est terminée, sélectionnez les nœuds et utiliser l’action de **Mettre en ligne** dans le Gestionnaire de Cluster HPC. Les nœuds sont prêts à exécuter des tâches.

10. **Soumettre les tâches au cluster**

    Outils de soumission de projet HPC Pack permet d’exécuter des tâches de cluster. Consultez [Microsoft HPC Pack : gestion du travail](http://technet.microsoft.com/library/jj899585.aspx).

11. **Arrêter (annulation) les nœuds**

    Lorsque vous avez terminé les tâches en cours d’exécution, mettez les nœuds hors connexion et utiliser l’action **Arrêter** de HPC Cluster Manager.





## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scénario 2. Déployer des nœuds de calcul dans les ordinateurs virtuels de calcul intensif (IaaS)

Dans ce scénario, vous déployez le nœud principal HPC Pack et les nœuds de cluster sur des ordinateurs virtuels joints à un domaine Active Directory dans un réseau virtuel Azure. HPC Pack fournit un certain nombre d' [options de déploiement de machines virtuelles d’Azure](virtual-machines-linux-hpcpack-cluster-options.md), y compris les scripts de déploiement automatisé et les modèles quickstart Azure. Par exemple, les considérations et les étapes ci-dessous vous guident pour utiliser le [script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour automatiser la majeure partie de ce processus.

![Cluster de machines virtuelles Azure][iaas]



### <a name="steps"></a>Étapes

1. **Créer un nœud de tête du cluster et de calcul des ordinateurs virtuels de nœud en exécutant le script de déploiement des HPC Pack IaaS sur un ordinateur client**

    Télécharger le Script de déploiement IaaS HPC Pack à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).

    Pour préparer l’ordinateur client, créez le fichier de configuration de script et exécuter le script, reportez-vous à [Création d’un Cluster à haute performance avec le script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). 
    
    Pour déployer des nœuds de calcul compatibles RDMA, notez les considérations supplémentaires suivantes :
    
    * **Réseau virtuel** , spécifiez un nouveau réseau virtuel dans une région dans laquelle la taille d’instance RDMA compatible à utiliser est disponible.

    * **Système d’exploitation Windows** pour la prise en charge de la connectivité RDMA, spécifier le système d’exploitation Windows Server 2012 R2 ou de Windows Server 2012 pour l’ordinateurs virtuels du nœud de calcul.

    * **Les services en nuage** - nous vous recommandons de déployer votre nœud de tête dans le service d’un nuage et vos nœuds de calcul dans un service de nuage différent.

    * **Taille de nœud de tête** - dans ce scénario, considérez une taille d’au moins A4 (très grande police) pour le nœud de tête.

    * **Extension de HpcVmDrivers** - le script de déploiement installe l’extension HpcVmDrivers et l’Agent de la machine virtuelle Azure automatiquement lorsque vous déployez taille A8 ou A9 nœuds avec un système d’exploitation Windows. HpcVmDrivers installe des pilotes sur l’ordinateurs virtuels du nœud de calcul afin qu’ils peuvent se connecter au réseau RDMA.

    * **Configuration de réseau de cluster** - le script de déploiement configure automatiquement le cluster HPC Pack à la topologie 5 (tous les nœuds sur le réseau d’entreprise). Cette topologie est nécessaire pour tous les déploiements de clusters HPC Pack dans des machines virtuelles. Ne modifiez pas la topologie de réseau de cluster ultérieurement.

2. **Les nœuds de calcul en ligne permettent d’exécuter des travaux**

    Sélectionnez les nœuds et utiliser l’action de **Mettre en ligne** dans le Gestionnaire de Cluster HPC. Les nœuds sont prêts à exécuter des tâches.

3. **Soumettre les tâches au cluster**

    Se connecter au nœud principal à soumettre des tâches, ou configurer un ordinateur local pour effectuer cette opération. Pour plus d’informations, voir [Soumettre des travaux à un cluster HPC dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

4. **Déconnecter les nœuds et arrêter (libérer) les**

    Lorsque vous avez terminé les tâches en cours d’exécution, prendre les nœuds hors connexion dans le Gestionnaire de Cluster HPC. Utilisez ensuite les outils de gestion Azure les arrêter.



## <a name="run-mpi-applications-on-the-cluster"></a>Exécuter des applications MPI sur le cluster

### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Exemple : Exécuter mpipingpong sur un cluster HPC Pack

Pour vérifier un déploiement HPC Pack des instances prenant en charge les RDMA, exécutez la commande **mpipingpong** de HPC Pack sur le cluster. **MPIPingPong** envoie des paquets de données entre les nœuds associés à plusieurs reprises pour calculer les mesures de débit et de latence et de statistiques pour le réseau de l’application activée RDMA. Cet exemple montre un modèle typique pour l’exécution d’un travail MPI (dans ce cas, **mpipingpong**) à l’aide de la commande **mpiexec** de cluster.

Cet exemple suppose que vous avez ajouté les nœuds Azure dans une configuration « en rafale vers Azure » ([scénario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS) in this article). Si vous avez déployé le HPC Pack sur un cluster de machines virtuelles d’Azure, vous devez modifier la syntaxe de commande pour spécifier un autre nœud groupe et de définir des variables d’environnement supplémentaires pour diriger le trafic réseau vers le réseau RDMA.


Pour exécuter le mpipingpong sur le cluster :


1. Sur le nœud principal ou sur un ordinateur client correctement configuré, ouvrez une invite de commande.

2. Pour estimer le temps de latence entre les paires de nœuds dans un déploiement d’Azure rafale de 4 nœuds, tapez la commande suivante pour envoyer un travail pour exécuter mpipingpong avec une taille de paquet de petite taille et d’un grand nombre d’itérations :

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    La commande renvoie l’ID de la tâche qui vous est envoyée.

    Si vous avez déployé le cluster HPC Pack déployé sur Azure VM, spécifiez un groupe de nœuds qui contient des ordinateurs virtuels déployés dans un service cloud unique de nœud de calcul et de modifier la commande **mpiexec** comme suit :

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. Une fois la tâche terminée, pour afficher le résultat (dans ce cas, la sortie de la tâche 1 du travail), tapez la ligne suivante

    ```
    task view <JobID>.1
    ```

    où &lt; *JobID* &gt; est l’ID de la tâche qui a été soumise.

    La sortie inclut les résultats de latence semblables à la suivante.

    ![Latence de pong ping][pingpong1]

4. Pour estimer le débit entre les paires de nœuds de rafale Azure, tapez la commande suivante pour envoyer un travail pour exécuter **mpipingpong** avec une taille de paquet importante et un petit nombre d’itérations :

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    La commande renvoie l’ID de la tâche qui vous est envoyée.

    Sur un cluster HPC Pack déployé sur Azure VM, modifiez la commande comme indiqué à l’étape 2.

5. Une fois la tâche terminée, pour afficher le résultat (dans ce cas, la sortie de la tâche 1 du travail), tapez ce qui suit :

    ```
    task view <JobID>.1
    ```

  La sortie inclut les résultats de débit semblables à la suivante.

  ![Débit de pong ping][pingpong2]


### <a name="mpi-application-considerations"></a>Considérations sur l’application MPI


Voici des considérations relatives à des applications MPI avec le HPC Pack dans Azure. Certains s’appliquent uniquement aux déploiements des nœuds Azure (instances de rôle de travail ajoutées dans une configuration « en rafale vers Azure »).

* Instances de rôle de travail dans un service en nuage sont périodiquement configurés sans préavis Azure (par exemple, pour la maintenance du système, ou dans le cas de qu'une instance échoue). Si une instance est configurée pendant l’exécution d’un travail MPI, l’instance perd ses données et retourne à l’état lorsqu’il a été déployé, qui peut provoquer l’échec du travail MPI. Les nœuds de plus que vous utilisez pour un seul travail MPI et plus la tâche s’exécute, le plus probable qu’une des instances est sera configurée lors d’un travail est en cours d’exécution. Vous devez également envisager si vous désignez un seul nœud dans le déploiement comme un serveur de fichiers.


* Pour exécuter des travaux MPI dans Azure, vous n’êtes pas obligé d’utiliser les instances RDMA compatible. Vous pouvez utiliser n’importe quelle taille d’instance qui est pris en charge par le HPC Pack. Toutefois, les instances RDMA compatibles sont recommandés pour l’exécution des travaux MPI relativement à grande échelle qui sont sensibles à la latence et la bande passante du réseau qui connecte les nœuds. Si vous utilisez d’autres tailles pour exécuter les tâches MPI et de la bande passante-sensibles à la latence, nous vous recommandons de petits travaux, dans lequel une tâche s’exécute sur uniquement quelques nœuds en cours d’exécution.

* Les applications déployées aux instances Azure sont soumis aux termes de la licences associées à l’application. Contactez le fournisseur de n’importe quelle application commerciale pour les restrictions de licences ou autres pour l’exécution dans le nuage. Pas de tous les fournisseurs offrent de paiement à l’utilisation de la licence.


* Instances Azure ont besoin d’installation supplémentaire pour les nœuds d’accès locaux, les partages et les serveurs de licences. Par exemple, pour activer les nœuds Azure à accéder à un serveur de licences sur site, vous pouvez configurer un réseau virtuel de site à site Azure.


* Pour exécuter des applications MPI sur des instances d’Azure, enregistrez chaque application MPI avec le pare-feu Windows sur les instances en exécutant la commande **hpcfwutil** . Cela autorise les communications de MPI ait lieu sur un port qui est affecté dynamiquement par le pare-feu.

    >[AZURE.NOTE] Pour la transmission en rafale pour des déploiements d’Azure, vous pouvez également configurer une commande d’exception de pare-feu pour s’exécuter automatiquement sur tous les nouveaux nœuds Azure qui sont ajoutés à votre cluster. Une fois que vous exécutez la commande **hpcfwutil** et vérifiez que votre application fonctionne, ajoutez la commande à un script de démarrage pour vos nœuds Azure. Pour plus d’informations, consultez [utilisation d’un Script de démarrage pour les nœuds d’Azure](https://technet.microsoft.com/library/jj899632.aspx).



* HPC Pack utilise la variable d’environnement CCP_MPI_NETMASK cluster pour spécifier une plage d’adresses acceptables pour les communications de MPI. Partir de HPC Pack 2012 R2, la variable d’environnement CCP_MPI_NETMASK cluster affecte uniquement la communication MPI entre les nœuds de calcul à un domaine de cluster (sur site ou dans Azure VM). La variable est ignorée par les nœuds ajoutés dans une rafale de configuration Azure.


* Impossible d’exécuter des travaux MPI sur Azure instances qui sont déployés dans les services de nuage différent (par exemple, en la transmission en rafale pour des déploiements Azure avec des modèles de différents nœuds ou des nœuds de calcul Azure VM déployés dans plusieurs services en nuage). Si vous disposez de plusieurs déploiements de nœud Azure qui démarrent avec des modèles de l’autre nœud, les MPI doit s’exécuter sur un seul ensemble de nœuds Azure.


* Lorsque vous ajoutez des nœuds Azure à votre cluster et mettez en ligne, le Service Planificateur de tâches de HPC essaie immédiatement de démarrer les travaux sur les nœuds. Si seule une partie de votre charge de travail peut exécuter sur Azure, assurez-vous que vous mettez à jour ou créez des modèles de projet pour définir les types de tâches peuvent s’exécuter sur Azure. Par exemple, pour vous assurer que les travaux envoyés avec un modèle de tâche exécutent uniquement sur Azure nœuds, ajouter la propriété de nœud groupes au modèle de tâche et sélectionnez AzureNodes comme la valeur requise. Pour créer des groupes personnalisés pour vos nœuds Azure, utilisez l’applet de commande PowerShell de HPC Add-HpcGroup.


## <a name="next-steps"></a>Étapes suivantes

* Comme alternative à l’utilisation de HPC Pack, développer avec le service de traitement par lots d’Azure pour exécuter des applications MPI sur les pools managés de nœuds de calcul dans Azure. Voir [utilisation des tâches de plusieurs instances d’exécuter des applications d’Interface MPI (Message Passing) dans le traitement par lots d’Azure](../batch/batch-mpi.md).

* Si vous souhaitez exécuter des applications qui accèdent au réseau Azure RDMA Linux MPI, reportez-vous à [configurer un cluster Linux RDMA pour exécuter des applications de MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/burst.png
[iaas]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/iaas.png
[pingpong1]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong2.png