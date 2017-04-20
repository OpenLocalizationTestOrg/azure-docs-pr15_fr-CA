<properties
    pageTitle="Linux et Open-Source informatique sur Azure | Microsoft Azure"
    description="Répertorie les articles Linux et Open-Source informatique sur Azure, y compris l’utilisation de base Linux, certains concepts fondamentaux à l’exécution ou le téléchargement d’images de Linux sur Azure et d’autres contenus sur les technologies spécifiques et les optimisations."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="06/27/2016"
    ms.author="rasquill"/>



# <a name="linux-and-open-source-computing-on-azure"></a>Linux et open-source informatique sur Azure

Rechercher toutes les informations dont vous avez besoin pour créer et gérer des machines virtuelles Linux dans le modèle de déploiement classique.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="get-started"></a>Mise en route
- [Introduction pour Linux sur Azure](virtual-machines-linux-intro-on-azure.md)
- [Forum aux questions sur Azure une machine virtuelle créée avec le modèle classique de déploiement](virtual-machines-linux-classic-faq.md)
- [À propos des images des machines virtuelles](virtual-machines-linux-classic-about-images.md)
- [Télécharger votre propre Image de Distro](virtual-machines-linux-classic-create-upload-vhd.md) (et également des instructions en utilisant une [Distribution de Azure-Endorsed](virtual-machines-linux-endorsed-distros.md))
- [Ouvrez une session sur une machine virtuelle Linux à l’aide du portail classique Azure](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="set-up"></a>Configurer

- [Installer Azure l’Interface de ligne de commande (CLI Azure)](../xplat-cli-install.md)


## <a name="tutorials"></a>Didacticiels

- [Installer la pile de la lampe sur une machine virtuelle de Linux dans Azure](virtual-machines-linux-create-lamp-stack.md)
- [Ruby sur l’application Web de Rails sur une machine virtuelle d’Azure](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
- [Comment : installer Apache Qpid PROTONS-c pour AMQP et Bus des services](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>Bases de données
- [Optimiser les performances de MySQL sur Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Clusters de MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [En cours d’exécution Cassandra avec Linux sur Azure et y accéder à partir de Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
- [Créer un cluster à plusieurs maîtres de MariaDbs](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### <a name="hpc"></a>INFORMATIQUE HAUTES PERFORMANCES
- [Mise en route avec les nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Exécutez NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [Configurer un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md)

### <a name="docker"></a>Docker
- [À l’aide de l’Extension de la machine virtuelle Docker à partir de la ligne de commande d’Azure Interface (CLI Azure)](virtual-machines-linux-classic-cli-use-docker.md)
- [À l’aide de l’Extension de la machine virtuelle Docker à partir du portail Azure](virtual-machines-linux-classic-portal-use-docker.md)
- [L’utilisation de docker-machine sur Azure](virtual-machines-linux-docker-machine.md)

### <a name="ubuntu"></a>Ubuntu
- [Comment : Clusters de MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Comment : Node.js et Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### <a name="opensuse"></a>OpenSUSE
- [Comment : installer et exécuter MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### <a name="coreos"></a>CoreOS
- [Comment : utiliser un CoreOS sur Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="planning"></a>Planification
- [Consignes de mise en œuvre de services infrastructure Azure](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
- [Sélection de noms d’utilisateurs de Linux](virtual-machines-linux-usernames.md)
- [Comment faire pour configurer une disponibilité pour les machines virtuelles dans le modèle de déploiement classique](virtual-machines-linux-classic-configure-availability.md)
- [Comment planifier la Maintenance planifiée sur Azure VM](virtual-machines-linux-planned-maintenance-schedule.md)
- [Gérer la disponibilité des machines virtuelles](virtual-machines-linux-manage-availability.md)
- [Maintenance planifiée pour les machines virtuelles de Linux dans Azure](virtual-machines-linux-planned-maintenance.md)


## <a name="deployment"></a>Déploiement
- [Créer un ordinateur virtuel personnalisé fonctionnant sous Linux](virtual-machines-linux-classic-createportal.md)
- [Les notions de base : capture une VM Linux pour créer un modèle](virtual-machines-linux-classic-capture-image.md)
- [Pour les Distributions de Non approuvée](virtual-machines-linux-create-upload-generic.md)


## <a name="management"></a>Gestion des

- [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
- [Comment faire pour réinitialiser un mot de passe ou de propriétés SSH pour Linux](virtual-machines-linux-classic-reset-access.md)
- [À l’aide de la racine](virtual-machines-linux-use-root-privileges.md)


## <a name="azure-resources"></a>Ressources Azure

- [L’Agent de Linux Azure](virtual-machines-linux-agent-user-guide.md)
- [Fonctionnalités et Extensions de azure VM](virtual-machines-windows-extensions-features.md)
- [Injection de données personnalisées dans un ordinateur virtuel à utiliser avec le nuage-init.](virtual-machines-windows-classic-inject-custom-data.md)


## <a name="storage"></a>Stockage

- [Attachement d’un disque de données à un ordinateur virtuel de Linux](virtual-machines-linux-classic-attach-disk.md)
- [Détachement d’un disque de données à partir d’un ordinateur virtuel de Linux](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## <a name="networking"></a>Mise en réseau
- [Comment configurer des points de terminaison sur une machine virtuelle classique dans Azure](virtual-machines-linux-classic-setup-endpoints.md)


## <a name="troubleshooting"></a>Résolution des problèmes
- [Dépannage des connexions SSH (Secure Shell) à une machine virtuelle Azure Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [Résoudre les problèmes de déploiement classique avec création d’une machine virtuelle Linux dans Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)  
- [Résoudre les problèmes de déploiement classique avec redémarrage ou le redimensionnement d’un ordinateur virtuel de Linux existant dans Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md) 


## <a name="reference"></a>Référence

- [Commandes CLI Azure en mode de gestion des services Azure (asm)](../virtual-machines-command-line-tools.md)
- [Gestion des services Azure API REST](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## <a name="general-links"></a>Liens généraux
Les liens suivants sont pour les blogs Microsoft, Technet, pages et des sites externes plutôt que documentation Azure.com comme indiqué ci-dessus. Comme Azure et sont tous deux le monde informatique open source se déplacent rapidement cibles, il est presque certain que les liens suivants sont obsolètes, *malgré* le fait que nous allons faisons de notre mieux pour constamment ajouter de nouvelles rubriques et supprimer ceux qui sont obsolètes. Si nous avons manqué une, veuillez nous le faire savoir dans les commentaires ou soumettre une demande d’extraction pour notre [mis en pension de GitHub](https://github.com/Azure/azure-content/).

- [5 d’ASP.NET en cours d’exécution sous Linux à l’aide de conteneurs de Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [Comment déployer une Image de machine virtuelle CentOS à partir de OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [Infrastructure de mise à jour SUSE](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SUSE Linux Enterprise Server pour le Cloud de SAP bibliothèque SAP solution matérielle-logicielle](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [Construction Linux hautement disponible sur Azure dans 12 étapes](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [Automatiser le provisionnement de Linux sur Azure avec la CLI d’Azure, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [GlusterFS sur Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
- [FreeBSD en cours d’exécution dans Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [Déploiement facile de FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [Déploiement d’une Image personnalisée de FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky antivirus pour serveur de fichiers de Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL

- [8 bases de données NoSql d’open source pour Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [Slideshare (MSOpenTech) : Expériences de CouchDb sur Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [CouchDB-as-a-Service avec node.js CORS et Grunt en cours d’exécution](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [Redis sur Windows dans le Service de Cache Redis Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Annonce de fournisseur d’état de Session ASP.NET pour la version d’évaluation de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [Blog : RavenHQ est désormais disponible sur le marché Azure](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>Données volumineuses
- [L’installation d’Hadoop sur Linux Azure VM](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [HDInsight Azure](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>Base de données relationnelle
- [Architecture de disponibilité élevée de MySQL dans Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [L’installation de Postgres avec corosync, pg_bouncer à l’aide de ILB](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux à haute performance (HPC)

- [Modèle de Quickstart : faire tourner un cluster SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (et [le billet de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
- [Modèle de démarrage rapide : créer un cluster à haute performance avec des nœuds de calcul Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>DevOps, la gestion et l’optimisation

Le monde de devops, la gestion et l’optimisation est très vaste et change très rapidement, vous devez envisager la liste en dessous d’un point de départ.

- [Vidéo : Les Machines virtuelles Azure : à l’aide de Chef, Marionnette et Docker pour la gestion des ordinateurs virtuels de Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [Guide complet de déploiement de cluster Kubernetes automatisé avec CoreOS et tissage](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Visualiseur de Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [Jenkins esclave de plug-in pour Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [Mis en pension de GitHub : stockage de Jenkins plug-in pour Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [Tiers : Esclave d’Hudson plug-in pour Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Tiers : Stockage d’Hudson plug-in pour Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [Vidéo : Nouveautés Chef et comment fonctionne-t-il ?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [Vidéo : Comment utiliser l’Automation Azure avec Linux VMs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [Blog : Procédure Powershell DSC pour Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub : Docker Client DSC](https://github.com/anweiss/DockerClientDSC)

- [Plug-in de garniture d’étanchéité pour Azure](https://github.com/msopentech/packer-azure)
