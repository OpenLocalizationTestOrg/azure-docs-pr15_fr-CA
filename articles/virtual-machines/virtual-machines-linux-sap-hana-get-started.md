<properties
   pageTitle="Guide de démarrage rapide pour l’installation manuelle de SAP HANA sur Azure VM | Microsoft Azure"
   description="Guide de démarrage rapide pour l’installation manuelle de SAP HANA sur Azure VM"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="quickstart-guide-for-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guide de démarrage rapide pour l’installation manuelle de HANA de SAP uniques sur Azure VM

## <a name="introduction"></a>Introduction

Ce guide de démarrage rapide qui permet de définir un seule instance SAP HANA prototype et de démonstration système sur Azure VM par une installation manuelle de SAP NetWeaver 7.5 et SAP HANA SP12.
Ce guide suppose que le lecteur est familiarisé avec les notions de base sur Azure IaaS comme le déploiement d’ordinateurs virtuels ou les réseaux virtuels via le portail Azure ou Powershell/CLI, y compris la possibilité d’utiliser des modèles de json. En outre, il est probable que le lecteur est familiarisé avec SAP HANA, SAP NetWeaver et comment l’installer sur site.

Il est probable que le lecteur connaît la documentation SAP-Azure générale mentionnée dans la section informations générales, à la fin de l’article.

En raison de la restriction à des systèmes hors production ce guide couvre pas sujets tels que de la haute disponibilité, sauvegarde, de reprise après sinistre, des performances élevées ou considérations de sécurité particulières.

L’exemple de configuration a été effectuée à l’aide de deux ordinateurs virtuels pour réaliser une installation SAP NetWeaver distribuée via le modèle de gestionnaire de ressources Azure comme SAP-Linux-Azure est uniquement pris en charge sur Azure le Gestionnaire de ressources et non pas au modèle classique. Vous trouverez des liens vers plus d’informations sur le Gestionnaire de ressources Azure dans la section informations générales, à la fin de cet article.

Il s’agissait de test de deux ordinateurs virtuels utilisés pour l’installation de l’exemple :

* Hana-appsrv (type DS3) pour héberger l’instance de NW 7.5 ASC + PAS
* Hana-dbsrv (type GS4) à l’hôte HANA SP12
* les deux ordinateurs virtuels appartenaient à un seul réseau virtuel Azure (azure-hana-test-vnet)
* Système d’exploitation dans les deux cas a été SLES SP1 de 12


Soyez conscient du fait cependant qu’à partir de juillet 2016 SAP HANA est uniquement entièrement pris en charge pour les systèmes de production OLAP (BW) sur le type d’Azure VM GS5. Pour des tests où une n’attend pas de prise en charge SAP officiel soit vendue à utiliser quelque chose de plus petits, comme par exemple GS4.
Doit toujours être utilisée pour stockage Azure premium pour les fichiers de données et de journaux HANA - HANA SAP sur Azure consultez disque » configuration « section plus bas. En ce qui concerne les détails supplémentaires à propos de SAP des produits sont pris en charge sur Azure, veuillez consulter la section informations générales, à la fin de cet article.


Le guide décrit deux méthodes différentes pour installer manuellement la HANA de SAP sur Azure VM :

* installation de SAP HANA via SAP logiciels Configuration Manager (SWPM) dans le cadre d’une installation NetWeaver distribuée dans l’étape « instance de base de données »
* Installez HANA SAP à l’aide de la hdblcm d’outil Gestionnaire du Cycle de vie HANA, puis NetWeaver par la suite

Il est également possible d’utiliser des SWPM et d’installer tous les composants (SAP HANA, serveur d’applications SAP, instance ASC, SAP GUI) dans un ordinateur virtuel d’unique. Cette option n’est pas décrite dans cet article, mais les éléments qui doivent être pris en compte sont les mêmes.

Avant de commencer une installation, la section après les listes de contrôle ci-dessous sur la configuration des ordinateurs virtuels test Azure doit être lu pour éviter des erreurs de base plusieurs qui se produit lors de l’utilisation d’une configuration d’Azure VM par défaut uniquement.


## <a name="checklist-sap-hana-installation-via-sap-swpm"></a>Liste de vérification des installation SAP HANA via SAP SWPM

Il s’agit d’une simple liste de vérification de la clé d’installation des éléments liés à une installation manuelle de SAP HANA uniques de démonstration ou prototypage pursposes via SWPM SAP effectuant un 7.5 de NW SAP distribués. Les éléments sont expliqués et affichées sous forme de captures d’écran plus en détail tout au long de l’article :

* créer un réseau virtuel Azure qui comprend les ordinateurs virtuels de deux test 
* déployer deux Azure VM avec OS SLES 12 SP1 via le modèle de gestionnaire de ressources Azure 
* joindre deux disques de stockage standard sur le serveur de l’application virtuelle (par exemple, 75 Go et 500 Go)
* quatre disques se rattacher au serveur HANA DB VM - disques 2 stockage standard comme pour le serveur d’application VM + 2 disques de stockage de prime (par exemple, 2x512GB)
* selon les spécifications de taille et/ou débit joindre plusieurs disques et de créer des volumes agrégés par bande soit à l’aide du lvm ou mdadm au niveau du système d’exploitation à l’intérieur de la machine virtuelle
* créer des systèmes de fichiers XFS sur les disques connectés / logique des volumes
* Montez les systèmes de fichiers XFS nouveau niveau de système d’exploitation. Un système de fichiers permet de conserver tous les logiciels SAP et l’autre par exemple pour le répertoire de sapmnt et peut-être les sauvegardes. Sur la base de données SAP HANA montage du serveur le XFS des systèmes de fichiers sur les disques de stockage premium en tant que /hana et /usr/sap tous les nécessaire éviter que le système de fichiers racine qui n’est pas trop important sur Linux Azure VM remplit
* Entrez les adresses ip locales du test des ordinateurs virtuels dans/etc/hosts
* Entrez le paramètre nofail dans/etc/fstab
* définir les paramètres du noyau la note SAP de HANA-SLES-12 (voir détails plus bas dans la section Paramètres de noyau)
* ajouter de l’espace de swap
* Si vous souhaitez - installer un bureau graphique sur l’ordinateurs virtuels de test. Sinon, utilisez une installation à distance sapinst
* Téléchargez le logiciel SAP SAP service marketplace
* installation de l’instance SAP ASC sur le serveur d’application virtuelle
* partager le répertoire sapmnt de via NFS entre l’ordinateurs virtuels de test (serveur d’applications VM est le serveur NFS)
* installation de l’instance de base de données, y compris la HANA via SWPM sur le serveur de base de données virtuelle
* installer le PAS sur le serveur d’application virtuelle
* Démarrez SAP MC et connectez-vous par exemple via l’interface graphique de SAP / HANA Studio 



## <a name="checklist-sap-hana-installation-via-hdblcm"></a>Liste de vérification des installation SAP HANA via hdblcm

Il s’agit d’une simple liste de vérification de la clé d’installation des éléments liés à une installation manuelle de SAP HANA uniques de démonstration ou prototypage pursposes via SWPM SAP effectuant un 7.5 de NW SAP distribués. Les éléments sont expliqués et affichées sous forme de captures d’écran plus en détail tout au long de l’article :

* créer un réseau virtuel Azure qui comprend les ordinateurs virtuels de deux test 
* déployer deux Azure VM avec OS SLES 12 SP1 via le modèle de gestionnaire de ressources Azure 
* joindre deux disques de stockage standard sur le serveur de l’application virtuelle (par exemple, 75 Go et 500 Go)
* quatre disques se rattacher au serveur HANA DB VM - 2 stockage standard comme pour le serveur d’applications VM + 2 disques de stockage de prime (par exemple, 2x512GB)
* selon les spécifications de taille et/ou débit joindre plusieurs disques et de créer des volumes agrégés par bande soit à l’aide du lvm ou mdadm au niveau du système d’exploitation à l’intérieur de la machine virtuelle
* créer des systèmes de fichiers XFS sur les disques connectés / logique des volumes
* Montez les systèmes de fichiers XFS nouveau niveau de système d’exploitation. Un système de fichiers permet de conserver tous les logiciels SAP et l’autre par exemple pour le répertoire de sapmnt et peut-être les sauvegardes. Sur la base de données SAP HANA montage du serveur le XFS des systèmes de fichiers sur les disques de stockage premium en tant que /hana et /usr/sap tous les nécessaire éviter que le système de fichiers racine qui n’est pas trop important sur Linux Azure VM remplit
* Entrez les adresses ip locales du test des ordinateurs virtuels dans/etc/hosts
* Entrez le paramètre nofail dans/etc/fstab
* définir les paramètres du noyau la note SAP de HANA-SLES-12 (voir détails plus bas dans la section Paramètres de noyau)
* ajouter de l’espace de swap
* Si vous souhaitez - installer un bureau graphique sur l’ordinateurs virtuels de test. Sinon, utilisez une installation à distance sapinst
* Téléchargez le logiciel SAP SAP service marketplace
* créer un groupe « sapsys » avec l’id de groupe 1001 sur l’ordinateur de serveur virtuel HANA DB
* installation de SAP HANA sur le serveur de base de données machine virtuelle à l’aide de hdblcm
* installation de l’instance SAP ASC sur le serveur d’application virtuelle
* partager le répertoire sapmnt de via NFS entre l’ordinateurs virtuels de test (serveur d’applications VM est le serveur NFS)
* installation de l’instance de base de données, y compris la HANA via SWPM sur le serveur de base de données virtuelle
* installer le PAS sur le serveur d’application virtuelle
* Démarrez SAP MC et connectez-vous par exemple via l’interface graphique de SAP / HANA Studio 




## <a name="prepare-azure-vms-for-manual-installation-of-sap-hana"></a>Préparer l’installation manuelle de SAP HANA Azure VM

Ce chapitre sur la préparation d’ordinateurs virtuels Azure pour l’installation manuelle de SAP HANA se compose de cinq sections qui couvrent les sujets suivants :

* Programme d’installation de disque
* Paramètres de noyau
* Systèmes de fichiers
* / etc/hosts
* / etc/fstab


### <a name="disk-setup"></a>Programme d’installation de disque

Le système de fichiers racine dans une VM Linux sur Azure est de taille limitée. Par conséquent, il est nécessaire de joindre un espace disque supplémentaire à une machine virtuelle pour l’exécution de SAP. Dans le cas d’un serveur d’applications SAP que machine virtuelle utilisée dans un environnement pur prototype et de démonstration il convient d’utiliser des disques de stockage standard Azure. Considérant que, pour les fichiers journaux et de données base de données SAP HANA - disques de stockage Azure prime doivent être utilisées même dans un environnement hors production.

Reportez-vous à la section pour plus d’informations sur l’association de disques à une VM Linux [ici](virtual-machines-linux-add-disk.md)

Lorsqu’il s’agit de la mise en cache du disque Azure - il doit utiliser « None » pour les disques qui seront utilisés pour stocker les journaux de transactions HANA. Pour HANA des fichiers de données qu'il est OK pour utiliser le cache de lecture. HANA est une base de données en mémoire que dépend du modèle d’utilisation globale combien le cache de lecture au niveau du disque Azure améliore les performances (par exemple démarrage HANA et lire les données à partir du disque dans la mémoire).

Reportez-vous à la section plus d’informations sur le stockage Azure Premium [ici](../storage/storage-premium-storage.md)

[Ici](https://github.com/Azure/azure-quickstart-templates) vous trouvez des exemples de modèles de json à créer des ordinateurs virtuels.
Le « 101-vm-simple-linux » montre comment un modèle de base, y compris de la section de stockage qui ajoute un disque de données de 100 Go.

[Cet article](virtual-machines-linux-sap-on-suse-quickstart.md) contient des informations sur comment trouver une image de SUSE via Powershell ou l’interface CLI et l’importance pour attacher un disque via un UUID.


Selon la taille de la configuration système et de débit, il peut être nécessaire de joindre plusieurs disques au lieu d’un et ensuite créer un agrégat par bandes sur ces disques au niveau du système d’exploitation. Voici les deux aspects pourquoi un serait de créer un agrégat par bandes sur plusieurs disques Azure :

* augmenter le débit
* besoin d’un seul système de fichiers > 1 to comme la limite de taille disque Azure est de 1 To (à partir de juillet 2016)


Plus d’informations sur les deux principaux outils de configuration de l’entrelacement, vous pouvez trouver ici :

[L’article sur l’utilisation de mdadm pour configurer le raid logiciel de Linux sur une machine virtuelle d’Azure](virtual-machines-linux-configure-raid.md)

[L’article sur la configuration du Gestionnaire de volumes logiques sur un ordinateur Linux Azure VM](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

Dans le test de deux disques de stockage standard Azure environnement étaient reliés au serveur d’application SAP VM. Un a été utilisé pour stocker tous les logiciels SAP pour l’installation (par exemple, NetWeaver 7.5, interface graphique de SAP, SAP HANA... ) et l’autre pour disposer de suffisamment d’espace pour tout ce qui peut s’avérer nécessaire (par exemple, la sauvegarde, les données de test) ainsi que le répertoire sapmnt (par exemple, les profils SAP) à partager entre tous les ordinateurs virtuels qui appartiennent à la même paysage SAP.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

Contrairement au serveur d’applications disques VM quatre ont été connectés au serveur SAP HANA VM. Comme avant les deux disques ont été utilisées pour maintenir le logiciel SAP (un peut également partager le disque du logiciel SAP via NFS) et avoir suffisamment d’espace, par exemple pour la sauvegarde. Les deux disques supplémentaires ont été des disques de stockage Azure Premium pour conserver les fichiers journaux et de données SAP HANA ainsi que le répertoire /usr/sap.


### <a name="kernel-parameters"></a>Paramètres de noyau


SAP HANA nécessite des paramètres de noyau Linux spécifiques qui ne font pas partie des images de la galerie Azure standard et ne doit être définie manuellement. Il existe une note SAP spécifique qui décrit les paramètres. 


Remarque SAP SAP HANA DB : Système d’exploitation paramètres recommandés pour SLES 12 / SLES pour 12 des Applications SAP : [SAP Remarque 2205917](https://launchpad.support.sap.com/#/notes/2205917)

Une rubrique supplémentaire reagrding-cache de page lié à l’exécution de SAP HANA SLES se trouve [ici](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache) dans le chapitre 6.1 noyau : limite du Cache de la Page

Il existe également une Note SAP en ce qui concerne la limite de cache de page [SAP Remarque 1557506](https://service.sap.com/sap/support/notes/1557506)

SLES 12 a un nouvel outil qui remplace l’ancien utilitaire de sapconf. Il est « adm réglé » et un profil de SAP HANA spécial est disponible. Un peut trouver plus d’informations sur cet outil en suivant les deux liens ci-dessous.

La documentation sur les adm réglé profil sap-hana SLES est disponible [ici](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

Documentation de SLES sur adm réglé de profil sap-hana - chapitre 6.2 réglage des systèmes pour les charges de travail SAP avec adm réglé - vous pouvez trouver [ici](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Ici, un peut voir comment « adm réglé » changé la transparent_hugepage ainsi que les valeurs de numa_balancing en fonction des paramètres SAP HANA requis.


Pour conserver les paramètres de noyau SAP HANA une doit utiliser grub2 sur SLES 12. Vous pouvez trouver des informations complémentaires sur le grub2 [ici](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Cette capture d’écran montre comment les paramètres de noyau ont été modifiées dans le fichier de configuration et puis « compiled » via grub2-mkconfig


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

Une autre option consisterait à modifier les paramètres via Yast et les paramètres de configuration de chargeur de démarrage du noyau.


### <a name="filesystems"></a>Systèmes de fichiers 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Un peut visualiser ici les deux systèmes de fichiers qui ont été créés sur le serveur d’applications SAP sur les deux disques de stockage standard Azure VM. Les deux systèmes de fichiers sont de type XFS et monté sur /sapdata et /sapsoftware.

Il n’est pas obligatoire de procéder de cette manière. Il existe différentes options de la structure de l’espace disque.
L’aspect le plus important est d’éviter que le système de fichiers racine manque d’espace. 


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

En ce qui concerne la machine virtuelle de SAP HANA DB, il est important de savoir que lors d’une installation de base de données via sapinst (swpm) et uniquement à l’aide de l’option d’installation « typique » simple il va installer les sélections par défaut sous /hana et /usr/sap. Le paramètre par défaut pour la sauvegarde de journal SAP HANA est, par exemple sous /usr/sap.
Type avant qu’il soit la clé afin d’éviter que le système de fichiers racine manque d’espace. Par conséquent une Assurez-vous qu’il existe suffisamment d’espace libre sous /hana et /usr/sap avant d’installer SAP HANA via swpm.

[Cet article](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) à partir de SAP décrit la disposition du système de fichiers standard de SAP HANA 


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

Lors de l’installation de SAP NetWeaver sur une photo de la galerie SLES 12 Azure standard il y aura un message qu’il n’existe aucun espace de swap. Pour vous débarrasser de ce message une pourrait par exemple ajouter manuellement un fichier d’échange comme décrit dans ce document via jj, mkswap et swapon. Suffit de rechercher « Ajout d’un échange fichier manuelle » dans [cet article](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)

Une autre option consiste à configurer l’espace d’échange via l’agent Linux VM. Vous trouverez plus d’informations [ici](virtual-machines-linux-agent-user-guide.md)


### <a name="etchosts"></a>/ etc/hosts

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

Un autre aspect important avant le début de l’installation SAP est d’inclure des noms d’hôtes et les adresses IP des ordinateurs virtuels SAP dans le fichier/etc/hosts. Un doit déployer tous les ordinateurs virtuels SAP au sein d’un réseau virtuel Azure et utilisez les adresses IP internes.

### <a name="etcfstab"></a>/ etc/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

Au cours de la phase de test il s’est avéré être une bonne idée d’ajouter le paramètre nofail fstab. Si un problème survient avec les disques de la machine virtuelle serait toujours trouver et ne se bloque pas dans le processus de démarrage. Mais à Méfiez-vous comme dans ce cas, l’espace disque supplémentaire n’est peut-être pas disponible et le processus peuvent se remplir le système de fichiers racine. En cas de /hana serait manquant SAP HANA ne démarre que tout.


## <a name="install-graphical-gnome-desktop-on-sles-12"></a>Installer Bureau de Gnome graphique sur SLES 12

Ce chapitre se compose de deux secitions qui couvrent les sujets suivants :

* Installation du bureau de Gnome et xrdp sur SLES 12
* MC de SAP basée sur Java à l’aide de Firefox sur SLES 12 en cours d’exécution

Vous pouvez également utiliser alternatives telles que Xterminal, VNC, mais à sept 2016 cet article décrit uniquement les xrdp.

### <a name="installation-of-gnome-desktop-and-xrdp-on-sles-12"></a>Installation du bureau de Gnome et xrdp sur SLES 12

En particulier pour ceux qui ont l’arrière-plan de Microsoft Windows et souhaitez utiliser un graphique de bureau directement dans les ordinateurs virtuels Linux de SAP pour exécuter Firefox, Sapinst, interface graphique de SAP, SAP MC ou HANA Studio et peut-être vous connecter à la machine virtuelle via RDP à partir d’un ordinateur Microsoft Windows, il existe un moyen simple pour y parvenir. Alors que ce n’est peut-être pas approprié, par exemple pour un serveur de base de données de production, il est OK pour un environnement pur prototype et de démonstration. Voici les étapes de l’installation de Gnome desktop sur un ordinateur virtuel de Azure SLES 12 :

installation de gnome desktop par la commande suivante (par exemple, dans une fenêtre de mastic) :

   zypper dans le modèle -t gnome-basic

puis installez xrdp pour autoriser la connexion à la machine virtuelle via RDP :

   zypper dans xrdp

modifier /etc/sysconfig/windowmanager et définir le Gestionnaire de windows par défaut pour Gnome :

   DEFAULT_WM = « gnome »

Exécutez la commande chkconfig afin de vous assurer que xrdp commence automatiquement après un redémarrage : 

  commande chkconfig-xrdp de niveau 3 sur

dans le cas où il doit y avoir un problème avec la connexion RDP essayer de redémarrer (peut-être hors de la fenêtre mastic) :

  redémarrage de /etc/xrdp/xrdp.sh

dans le cas où le redémarrage de xrdp comme indiqué ci-dessus ne fonctionnent à cocher s’il existe un fichier .pid et le supprimer :

  Vérifiez les /var/run et recherchez les xrdp.pid   
  Supprimez-le et réessayez le redémarrage



### <a name="sap-mc"></a>SAP MC


Pour démarrer le graphique MC SAP basée sur Java de Firefox fonctionnant dans un ordinateur virtuel de Azure SLES 12 après avoir installé le Gnome dekstop comme décrit dans la section précédente, un obtiendra une erreur car le plug-in de navigateur Java sont manquantes.

L’URL à démarrer la MC SAP est <server>: 5 < numéro_instance > 13

Vous trouverez plus de détails [ici](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

Sur la capture d’écran ci-dessus, une pouvez voir comment le message d’erreur pourrait ressembler lorsque le plug-in de navigateur Java est manquant. 

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Une option pour résoudre le problème consiste à installer le plug-in manquant via Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

Répétition de l’URL de SAP MC fait apparaître un peu de temps la première de dialogue vous demandant d’activer le plug-in.


Un problème supplémentaire qui peut apparaître est un message d’erreur concernant un fichier manquant : il s’agit très probablement liés à l’installation de Java 1.8 qui est requis pour SAP GUI 7.4 de javafx.properties

La version de Java d’IBM visible via Yast n’inclut pas ce fichier. La solution est un téléchargement de Java à partir d’Oracle.
Un article qui parle à ce problème spécifique, vous pouvez trouver [ici](https://scn.sap.com/thread/3908306)



## <a name="manual-sap-hana-installation-via-swpm-as-part-of-a-netweaver-75-installation"></a>Manuel d’installation de SAP HANA via SWPM dans le cadre d’une installation NetWeaver 7.5


La liste des captures d’écran ci-dessous illustre les principales étapes d’installation de SAP NetWeaver 7.5 et SP12 de HANA SAP via SWPM (sapinst). Dans le cadre d’un 7.5 NW installation SWPM possède les capacités d’installer également la base de données HANA sous la forme d’une seule instance.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

Pour le test seul serveur d’application ABAP environnement a été installé. L’option « Distributed System » a été utilisée pour installer l’instance ASC et l’instance de serveur d’application principal dans une machine virtuelle d’Azure et SAP HANA comme système de base de données dans un autre Azure VM.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

Une fois l’instance ASC est installé sur le serveur de l’application virtuelle et a la valeur « vert » dans SAP MC le répertoire sapmnt, qui inclut le répertoire du profil SAP par exemple, doit être partagé avec le serveur de base de données SAP HANA VM.
L’étape d’installation de base de données a besoin d’accéder à ces informations. Le meilleur moyen consiste à utiliser des NFS qui peut être configuré à l’aide de Yast.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

Sur l’application serveur VM, le répertoire sapmnt doit être partagé via NFS à l’aide des options « rw » ainsi que « no_root_squash ». Valeur par défaut est « ro » et « root_squash » qui pourraient conduire à des problèmes lors de l’installation de l’instance de base de données.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

Sur le serveur de base de données SAP HANA VM le sapmnt partager à partir de la machine virtuelle doit être configurée via « client NFS » (par exemple, avec l’aide de Yast) le serveur d’applications


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

Un doit ensuite ouvrir une session sur le serveur de base de données SAP HANA VM et démarrer SWPM pour atteindre l’étape suivante d’une installation NW 7.5 distribuée - « Instance de base de données ».


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Liés à l’installation de SAP HANA n’est pas en fait trop entrer après avoir sélectionné une installation « classique ». En outre, le chemin d’accès au média installatiom une a permet d’entrer un identificateur de sécurité de base de données, le nom d’hôte, le numéro d’instance et le mot de passe de base de données Sys Admin.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Étape suivante consiste à entrer le mot de passe pour le schéma DBACOCKPIT.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Puis vient la question pour le mot de passe du schéma SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

À la fin, il doit être uniquement les coches vertes devant chaque phase du processus d’installation de la base de données et de la zone de petit message qui s’affiche doit indiquer « exécution de... Instance de base de données est terminée ».

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

Une fois l’installation réussie du MC SAP doit également afficher l’instance de base de données en tant que « vert » et la liste complète des processus SAP HANA (par exemple, hdbindexserver, hdbcompileserver)


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Cette capture d’écran illustre les parties de la structure de fichier sous /hana/shared qui SWPM créé pendant l’installation de HANA. Il n’a pas d’option pour spécifier un autre chemin d’accès. Par conséquent, il est donc important de monter de l’espace disque supplémentaire sous /hana avant l’installation de SAP HANA via SWPM afin d’éviter que le système de fichiers racine espace libre est insuffisant.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

Un pouvez visualiser ici la même chose que décrit précédemment pour le répertoire /usr/sap.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

La dernière étape de l’installation distribuée ABAP est le « Application Server Instance principale »


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Une fois le PAS ainsi que SAP GUI a installé un peut vérifier à l’aide de la transaction « dbacockpit », tout semble droite avec l’installation SAP HANA.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

En dernier étape 1 peut installer SAP HANA Studio dans le serveur d’applications SAP VM et connectez-vous à l’instance SAP HANA en cours d’exécution sur le serveur de base de données virtuelle.




## <a name="manual-sap-hana-installation-via-hana-life-cycle-manager-tool-hdblcm"></a>Installation manuelle de SAP HANA via hdblcm d’outil de gestionnaire de cycle de vie HANA


Outre l’installation SAP HANA dans le cadre d’une installation distribuée via SWPM, il est également réaliste pour tout d’abord installer HANA en mode autonome à l’aide de hdblcm et installer, par exemple SAP NetWeaver 7.5 par la suite. La liste des captures d’écran ci-dessous montre comment cela fonctionne.

Voici trois sources d’informations sur l’outil de hdblcm HANA :

[Choix de la HDBLCM HANA SAP Correct pour votre tâche](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[Outils de gestion du cycle de vie SAP HANA](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA serveur Guide d’Installation et mise à jour](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

Pour éviter de rencontrer des problèmes plus tard avec un paramètre d’id de groupe par défaut pour le \<HANA SID\>utilisateur adm (créé par l’outil hdblcm) il doit définir un nouveau groupe appelé « sapsys » avec l’id de groupe 1001 avant d’installer la HANA SAP à l’aide de hdblcm.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

Heure de hdblcm le premier Il y aura un menu Démarrer simple où un a pour sélectionner l’élément de début 1 « installer nouveau système »



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

Sur cette capture d’écran une peut voir toutes les options de clé qui ont été entrées avant. Important - répertoires qui ont été nommés pour les volumes de données et de journal HANA ainsi que le chemin d’installation (/ hana/partagé dans cet exemple) et/usr/sap ne doit pas faire partie du système de fichiers racine mais qui appartiennent à des disques de données Azure qui étaient associées à la machine virtuelle, comme décrit dans la section de configuration Azure VM. Cela permet d’éviter le risque que le système de fichiers racine peut manquer d’espace.
Un peut également voir que l’utilisateur admin HANA a pseudo 1005 et fait partie du groupe sapsys (id 1001) qui a été défini avant l’installation.



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

Un peut vérifier la HANA \<HANA SID\>détails dans/etc/passwd adm (azdadm dans cet exemple) de l’utilisateur



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Après avoir installé la HANA SAP à l’aide de hdblcm, il peut être vu dans SAP HANA Studio. Le schéma SAPABAP1, qui contient, par exemple toutes les SAP NetWeaver tables n’est encore disponible.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Après l’installation de SAP HANA d’installer SAP NetWeaver dessus. Dans cet exemple, qu'il a été fait à l’aide de « installation distribuée » via SWPM comme décrit dans la section correspondante ci-dessus.
Lorsque l’installation de l’instance de base de données via SWPM une juste doit entrer les mêmes données qu’avant avec hdblcm (par exemple, nom d’hôte, HANA SID, numéro d’instance). SWPM ensuite utiliser l’installation existante de la HANA et ajouter des schémas supplémentaires.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

C’est l’image de l’étape d’installation SWPM, où un doit entrer des données concernant le schéma DBACOCKPIT.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Puis SWPM s’attend à entrer des informations sur le schéma SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Une fois la fin de l’installation d’instance de base de données SWPM un peut voir le schéma SAPABAP1 dans HANA Studio.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

Et enfin après l’installation du serveur d’applications SAP et SAP GUI un doit être en mesure de vérifier l’instance HANA DB avec transaction « dbacockpit ».




## <a name="general-information-related-to-sap-azure-certifications-running-sap-hana-on-azure-and-sap-software-download"></a>Informations générales relatives aux certifications SAP Azure, SAP HANA en cours d’exécution sur le téléchargement du logiciel Azure et SAP

* des docu SAP Azure général sur l’exécution de SAP sur Azure avec le système d’exploitation Windows en mode classique : [À l’aide de SAP sur les ordinateurs virtuels Windows Azure](virtual-machines-windows-classic-sap-get-started.md)

* informations sur les modèles existants de SAP pour l’utilisation par les clients : [Modèles de démarrage rapide d’Azure pour SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)

* des docu SAP Azure général sur l’exécution de SAP sur Azure avec le système d’exploitation Linux dans le modèle du Gestionnaire de ressources Azure : [À l’aide de SAP sur Linux les machines virtuelles (VM)](virtual-machines-linux-sap-get-started.md)

* répertoire de matériel SAP HANA certifié qui répertorie les types d’Azure VM sont pris en charge pour la production : [Le matériel certifié le HANA SAP® Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)

* informations sur les tailles de machine virtuelle en particulier pour les charges de travail Linux : [tailles pour les machines virtuelles dans Azure](virtual-machines-linux-sizes.md)

* Note SAP qui répertorie tous les pris en charge par les produits SAP Azure et prise en charge des types d’Azure VM pour SAP : [SAP Remarque 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)

* Note SAP SAP « Amélioration de la surveillance » avec les ordinateurs virtuels de Linux sur Azure : [SAP Remarque 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)

* HANA SAP offre sur Azure « Instances de grande taille ». Il est important de comprendre que ce n’est pas sur l’exécution de SAP HANA sur Azure VM, mais dans un hybride environnement où les serveurs d’applications SAP exécutent dans Azure VM mais SAP HANA s’exécute sur des serveurs sans système d’exploitation : [SAP Remarque 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)

* Note SAP avec les informations de SAPOSCOL sous Linux : [SAP Remarque 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)

* Clé de surveillance des métriques de SAP sur Microsoft Azure : [Note SAP n° 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Informations sur le Gestionnaire de ressources Azure : [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)

* Informations sur le déploiement des ordinateurs virtuels de Linux via des modèles : [déployer et gérer des ordinateurs virtuels à l’aide des modèles du Gestionnaire de ressources Azure et Azure CLI](virtual-machines-linux-cli-deploy-templates.md)

* Comparaison du déploiement de modèles entre le Gestionnaire de ressources Azure et classique : [Azure le Gestionnaire de ressources et de déploiement classique : comprendre les modèles de déploiement et de l’état de vos ressources de](../resource-manager-deployment-model.md)

* Télécharger NetWeaver 7.5 pour Linux/HANA à partir de SAP Service Marketplace :![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* Télécharger HANA SP12 Platform Edition à partir de SAP Service Marketplace :![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

