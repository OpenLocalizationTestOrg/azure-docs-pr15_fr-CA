<properties 
   pageTitle="Échec des machines virtuelles VMware arrière et des serveurs physiques vers le site local | Microsoft Azure"
   description="Obtenir des informations sur l’échec vers le site local après basculement sur incident des ordinateurs virtuels VMware et des serveurs physiques vers Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/22/2016"
   ms.author="ruturajd"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Échec des machines virtuelles VMware arrière et des serveurs physiques vers le site local

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-failback-azure-to-vmware.md)
- [Azure Portal classique](site-recovery-failback-azure-to-vmware-classic.md)
- [Portail classique Azure (hérité)](site-recovery-failback-azure-to-vmware-classic-legacy.md)



Cet article décrit l’échec précédent Azure des machines virtuelles à partir d’Azure sur le site local. Suivez les instructions de cet article lorsque vous êtes prêt à restaurer vos machines virtuelles de VMware ou des serveurs physiques Windows/Linux après vous avez basculé dans les locaux du site vers Azure à l’aide de ce [didacticiel](site-recovery-vmware-to-azure-classic.md).



## <a name="overview"></a>Vue d’ensemble

Ce diagramme illustre l’architecture de restauration automatique pour ce scénario.

Utilisez cette architecture lorsque le serveur est local, et vous utilisez un ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Utilisez cette architecture lorsque le serveur de traitement sur Azure et que vous avez un réseau privé virtuel ou une connexion ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Pour voir la liste complète des ports et le retour arrière Architect diagramme font référence à l’image ci-dessous

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Voici comment la restauration automatique fonctionne :

- Une fois que vous avez basculé vers Azure, vous ne parvenez pas à votre site local en quelques étapes :
    - **Étape 1**: vous restaurez la protection ordinateurs virtuels Azure afin qu’ils commencent la réplication sur des machines virtuelles de VMware en cours d’exécution dans votre site local. L’activation de reprotection, la machine virtuelle déplace dans un groupe de protection de restauration qui a été créé automatiquement lorsque vous avez créé le groupe de protection avec basculement. Si vous avez ajouté à que votre groupe de protection avec basculement à un plan de récupération puis le groupe de protection de basculement a été ajoutée automatiquement au plan.  Au cours de la restaurez protection, vous spécifiez la planification pour le restaurer.
    - **Étape 2**: une fois que vos ordinateurs virtuels d’Azure répliquez vers votre site local, vous exécutez un échec échec d’Azure.
    - **Étape 3**: une fois que vos données a échoué en arrière, vous restaurez protection les ordinateurs virtuels sur les sites que vous n’avez pas à, afin qu’ils commencent à répliquer vers Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### <a name="failback-to-the-original-or-alternate-location"></a>Restauration à l’emplacement d’origine ou de remplacement

Si vous n’avez pas sur une VM VMware pouvez vous ne parvenez pas à la même source de machine virtuelle si elle existe toujours sur site. Dans ce scénario, uniquement les modifications delta va échouer précédent. Notez que :

- Si vous n’avez pas sur des serveurs physiques retour arrière est toujours à une nouvelle VM de VMware.
    - Avant la remise en place d’une machine physique Notez que
    - Machine physique protégé reviendra comme un ordinateur virtuel après un basculement de Azure pour VMware
    - Assurez-vous que vous découvriez au moins une cible de Master Server ainsi que les hôtes ESX/ESXi nécessaires à laquelle vous devez effectuer un retour arrière.
- Si vous ne parvenez pas à la machine virtuelle d’origine suivantes est requise :
    - Si la machine virtuelle est gérée par un serveur vCenter hôte ESX de la forme de base cible doit avoir accès à la banque de données des machines virtuelles.
    - Si la machine virtuelle sur un hôte ESX mais n’est pas gérée par vCenter le disque dur de la machine virtuelle doit être dans un magasin de données accessible par les hôtes de la MT.
    - Si votre ordinateur virtuel se trouve sur un hôte ESX et n’utilise pas vCenter puis vous exécutez la découverte de l’hôte ESX de la MT avant de vous restaurez la protection. Cela s’applique si vous êtes défaillance des serveurs physiques en trop.
    - Une autre option (si la machine virtuelle sur site existe) est de le supprimer avant d’effectuer un retour arrière. Puis restauration crée alors un nouvel ordinateur virtuel sur le même hôte que l’hôte ESX cible principale.
    
- Lorsque la restauration automatique vers un autre emplacement de données sera restaurée dans le même magasin de données et le même hôte ESX que celui utilisé par le serveur cible principal sur site. 


## <a name="prerequisites"></a>Conditions préalables

- Vous aurez besoin un environnement VMware afin de ne pas sauvegarder des ordinateurs virtuels VMware et des serveurs physiques. Échec sur un serveur physique n’est pas pris en charge.
- Pour être restauré vous aurez créé un réseau Azure lorsque vous définissez initialement la protection. Retour arrière a besoin d’une connexion VPN ou ExpressRoute à partir du réseau d’Azure dans lequel les ordinateurs virtuels d’Azure se trouvent sur le site local.
- Si les ordinateurs virtuels que vous souhaitez basculer sur sont gérés par un serveur vCenter vous devrez vous assurer que vous disposez des autorisations nécessaires pour la découverte des ordinateurs virtuels sur les serveurs vCenter. [Pour en savoir plus](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Si les captures instantanées sont présentes sur un ordinateur virtuel reprotection échoue. Vous pouvez supprimer les captures instantanées ou les disques. 
- Avant que vous restaurez, vous devrez créer un certain nombre de composants :
    - **Créer un serveur de traitement dans Azure**. Il s’agit d’une machine virtuelle Azure que vous aurez besoin pour créer et maintenir en cours d’exécution lors du retour arrière. Vous pouvez supprimer l’ordinateur une fois la restauration terminée.
    - **Créer un serveur cible principal**: le serveur cible maître envoie et reçoit les données de restauration. Le serveur d’administration que vous avez créé sur site a un serveur cible maître installé par défaut. Toutefois, selon le volume du trafic d’arrière-plan ayant échoué, vous devrez peut-être créer un serveur cible principal distinct de la restauration.
    - Si vous souhaitez créer un serveur cible principale supplémentaire en cours d’exécution sous Linux, vous devrez configurer la VM Linux avant d’installer le serveur cible principale, comme décrit ci-dessous.
- Serveur de configuration est requis sur site lorsque vous faites un retour arrière. Lors du retour arrière, l’ordinateur virtuel doit exister dans la base de données serveur Configuration, Échec de la restauration automatique ne soit réussie. Par conséquent, assurez-vous que vous prenez une sauvegarde planifiée régulière de votre serveur. En cas de sinistre, vous devez le restaurer avec la même adresse IP afin que la restauration automatique fonctionne.

## <a name="set-up-the-process-server-in-azure"></a>Configurer le serveur de traitement dans Azure

Vous devez installer un serveur de traitement dans Azure afin que les ordinateurs virtuels d’Azure peut envoyer les données au serveur de cible principal sur site. 

1.  Dans le portail de récupération de Site > **Configuration de serveurs** , sélectionnez Ajouter un nouveau serveur de processus.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Spécifiez un nom de serveur de traitement et entrez un nom et un mot de passe que vous allez utiliser pour vous connecter à la machine virtuelle d’Azure en tant qu’administrateur. Dans une **Configuration serveur** sélectionnez le serveur de gestion sur site et spécifier le réseau Azure dans lequel le serveur de traitement doit être déployé. Ce doit être le réseau dans lequel se trouvent les ordinateurs virtuels d’Azure. Spécifier une adresse IP unique à partir du sous-réseau de sélectionner et de commencer le déploiement.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

    Un travail pour déployer le serveur de traitement sera déclenché

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

    Après le déploiement du serveur de traitement dans Azure, vous pouvez vous connecter à en utilisant les informations d’identification que vous avez spécifié. La première fois que vous ouvrez une session dans la boîte de dialogue processus serveur s’exécute. Tapez l’adresse IP du serveur d’administration sur site et son mot de passe. Laissez le paramètre de port 443 par défaut. Vous pouvez également laisser de la valeur par défaut port 9443 pour la réplication de données sauf si, en particulier, vous avez modifié ce paramètre lorsque vous configurez le serveur d’administration sur site. 

    >[AZURE.NOTE] Le serveur n’est pas visible dans les **Propriétés de la machine virtuelle**. Elle n’est visible sous l’onglet **serveurs** dans le serveur d’administration dans lequel elle est inscrite. Il peut prendre environ 10-15 min pour le serveur de processus s’affiche.


## <a name="set-up-the-master-target-server-on-premises"></a>Configurer la cible principal serveur local

Le serveur maître cible reçoit les données de restauration. Un serveur cible principal est automatiquement installé sur le serveur d’administration sur site, mais si vous êtes échouent beaucoup de données, vous devrez configurer un serveur cible principale supplémentaire. Procédez comme suit :

>[AZURE.NOTE] Si vous souhaitez installer un serveur maître cible sous Linux, suivez les instructions dans la procédure suivante.

1. Si vous installez le serveur cible principal sous Windows, ouvrez la page de démarrage rapide à partir de l’ordinateur virtuel sur lequel vous installez le serveur cible maître et téléchargez le fichier d’installation pour l’Assistant Azure récupération unifiée installation d’un Site.
2. Exécutez le programme d’installation et dans **avant de commencer** , sélectionnez **Ajouter des serveurs supplémentaires de processus à l’échelle du déploiement**.
3. Terminez l’Assistant de la même manière que vous l’avez fait lorsque vous [Configurez le serveur d’administration](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Sur la page de **Détails de Configuration de serveur** , spécifiez l’adresse IP de ce serveur maître cible et un mot de passe pour accéder à la machine virtuelle.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurer une VM Linux en tant que serveur maître cible
Pour configurer le serveur d’administration en cours d’exécution le serveur maître cible comme une VM Linux vous devrez installer le Cent) 6.6 de S minimale système d’exploitation, récupérer les ID SCSI pour chaque disque SCSI, installer certains des packages supplémentaires et appliquer des modifications personnalisées.

#### <a name="install-centos-66"></a>Installer CentOS 6.6

1.  Installez le système d’exploitation minimal de CentOS 6.6 sur le serveur de gestion des machines virtuelles. Conserver l’ISO dans un lecteur de DVD et démarrez le système. Ignorer le test de media, sélectionnez anglais (États-Unis), à la langue, sélectionnez les **Périphériques de stockage de base**, vérifiez que le disque dur n’ont toutes les données importantes et cliquez sur **Oui**, ignorer les données. Entrez le nom d’hôte du serveur d’administration et sélectionnez la carte réseau du serveur.  Dans la boîte de dialogue **Système de montage** , sélectionnez** se connecter automatiquement** et ajouter une adresse IP statique, réseau et paramètres DNS. Spécifier un fuseau horaire et un mot de passe racine pour accéder au serveur de gestion. 
2.  Lorsque le système vous demande le type d’installation, vous souhaitez sélectionner **Créer une mise en page personnalisée** en tant que la partition. Après avoir cliqué sur **suivant** sélectionnez **libre** et cliquez sur Créer. Créer des **/**, **/var/crash** **et//Home partitions** avec **FS Type :** **ext4**. Créer la partition swap comme **FS Type : permutation**.
3.  Si les périphériques existants se trouvent qu'un message d’avertissement s’affiche. Cliquez sur **Format** pour formater le lecteur avec les paramètres de partition. Cliquez sur **écrire les modifications sur le disque** pour appliquer les modifications de la partition.
4.  Sélectionnez le **chargeur de démarrage d’installation** > **suivante** pour installer le chargeur de démarrage sur la partition racine.
5.  Lorsque l’installation est terminée cliquez sur **redémarrer**.


#### <a name="retrieve-the-scsi-ids"></a>Récupérer les ID SCSI

1. Après l’installation de récupérer l’ID SCSI pour chaque disque SCSI de l’ordinateur virtuel. Pour faire cette arrêter le serveur d’administration VM, dans les propriétés de la machine virtuelle dans VMware avec le bouton droit l’entrée d’ordinateur virtuel > **Modifier les paramètres de** > **Options**.
2. Sélectionnez **Avancé** > **Général de l’article** , puis cliquez sur **Paramètres de Configuration**. Lorsque l’ordinateur est en cours d’exécution, cette option sera de-active. L’ordinateur doit être arrêté pour la rendre active.
3. Si la ligne **disque. EnableUUID** existe Assurez-vous que la valeur est définie sur **True** (sensible à la casse). S’il est déjà, vous pouvez annuler et tester la commande SCSI à l’intérieur d’un système d’exploitation invité après que ce dernier est démarré. 
4.  Si la ligne n’est pas existant Cliquez sur **Ajouter une ligne** – et l’ajouter à la valeur **True** . N’utilisez pas de guillemets doubles.

#### <a name="install-additional-packages"></a>Installer des packages supplémentaires

Vous devez télécharger et installer des packages supplémentaires. 

1.  Assurez-vous que le serveur cible principal est connecté à internet.
2.  Exécutez la commande suivante pour télécharger et installer des packages de 15 à partir du référentiel CentOS : **# yum installer – y xfsprogs perl lsscsi rsync wget kexec-tools**.
3.  Si les ordinateurs source protection Linux wit Reiser sont en cours d’exécution ou XFS de fichier système pour le périphérique racine ou de démarrage, puis vous devez télécharger et installer des packages supplémentaires comme suit :

    - # <a name="cd-usrlocal"></a>CD/usr/local
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
    - # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>reiserfs dans le kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm de-ivh RPM-utils-3.6.21-1.el6.elrepo.x86_64.rpm
    - # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
    - # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rpm-ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Appliquer des modifications personnalisées

Les éléments suivants pour appliquer des modifications personnalisées une fois que vous avez effectuez les étapes consécutives à l’installation et installé les packages :

1.  Copiez le binaire RHEL 6-64 unifiée Agent de la machine virtuelle. Exécutez cette commande pour décompressez le fichier binaire : **tar-zxvf <file name> **
2.  Exécutez la commande suivante pour accorder les autorisations : **# chmod 755./ApplyCustomChanges.sh**
3.  Exécuter le script : **#./ApplyCustomChanges.sh**. Vous ne devez exécuter le script une seule fois. Redémarrez le serveur une fois que le script s’exécute correctement.


## <a name="run-the-failback"></a>Exécuter la restauration automatique

### <a name="reprotect-the-azure-vms"></a>Restaurez protection les ordinateurs virtuels Azure

1.  Dans le portail du Site récupération > onglet de **Machines** , sélectionnez l’ordinateur virtuel est basculé, puis cliquez sur **protéger de nouveau**.
2.  Dans le **Serveur cible de maître** et **Serveur de traitement** , sélectionnez le serveur cible principal sur site et le serveur de traitement Azure VM.
3.  Sélectionnez le compte que vous avez configuré pour la connexion à la machine virtuelle.
4.  Sélectionnez la version de restauration automatique du groupe de protection. Pour exemple, si la machine virtuelle est protégée dans PG1, vous devez sélectionner PG1_Failback.
5.  Si vous souhaitez récupérer vers un autre emplacement, sélectionnez le lecteur de la rétention et le magasin de données configuré pour le serveur cible principale. Lorsque vous ne parvenez pas sur le site local les machines virtuelles VMware dans le plan de protection de restauration automatique utilise le même magasin de données en tant que serveur maître cible. Si vous souhaitez récupérer le réplica Azure VM au même local machine virtuelle, la machine virtuelle sur site doit être déjà dans le magasin de données de même que le serveur maître de cible. S’il n’existe aucune machine virtuelle sur site un nouveau sera créé au cours de la reprotection.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.  Une fois que vous cliquez sur **OK** pour commencer le reprotection une tâche commence à répliquer de la machine virtuelle d’Azure sur le site local. Vous pouvez suivre la progression de l’onglet **tâches** .

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Exécuter un basculement vers le site local

Après reprotection l’ordinateur virtuel est déplacé vers la version de restauration de son groupe de protection et est automatiquement ajouté pour le plan de récupération que vous avez utilisé pour le basculement vers Azure s’il en existe un.

1.  Dans la page de **Plans de** sélectionner le plan de récupération contenant le groupe de restauration automatique et cliquez sur **Basculer** > **Basculement non planifié**.
2.  Dans **Confirmer le basculement** , vérifiez le sens de basculement (vers Azure), puis sélectionnez le point de récupération que vous souhaitez utiliser pour le basculement (plus récente). Si vous avez activé le **Multi-VM** lors de la configuration des propriétés de réplication, vous pouvez récupérer à la dernière application ou d’un point de récupération cohérent après sinistre. Cliquez sur la case à cocher pour démarrer le basculement.
3.  Récupération de Site va arrêter les ordinateurs virtuels d’Azure au cours du basculement. Après avoir vérifié que la restauration est terminée normalement vous pouvez vous pouvez vérifier que les ordinateurs virtuels d’Azure a été arrêtés correctement.

### <a name="reprotect-the--on-premises-site"></a>Restaurez protection du site local

Une fois la restauration terminée vos données seront sur le site local, mais ne pas être protégées. Pour lancer la réplication vers Azure à nouveau effectuer les opérations suivantes :

1.  Dans le portail du Site récupération > onglet **ordinateurs** , sélectionnez les ordinateurs virtuels qui n’ont pas pu sauvegarder, puis cliquez sur **protéger de nouveau**. 
2.  Après avoir vérifié que la réplication vers Azure fonctionne comme prévu, dans Azure que vous pouvez supprimer des ordinateurs virtuels Azure (actuellement ne pas en cours d’exécution) et qui n’ont pas pu revenir.


### <a name="common-issues-in-failback"></a>Problèmes courants lors de la restauration automatique

1. Si vous effectuez la découverte de vCenter en lecture seule utilisateur et protégez les machines virtuelles, il réussit et basculement fonctionne. Il échouera au moment de restaurez protection, étant donné que les magasins de données ne peut pas être découvert. Comme un symptôme, vous ne verrez pas les magasins de données répertorié tout en protégeant de nouveau. Pour résoudre ce problème, vous pouvez mettre à jour les informations d’identification de vCenter avec un compte qui dispose des autorisations appropriées et renouvelez l’opération. [Pour en savoir plus](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Lors de l’opération de retour arrière une VM Linux et exécutez-le sur prem, vous verrez que le package du Gestionnaire de réseau est désinstallé de l’ordinateur. C’est parce que lorsque la machine virtuelle est restaurée dans Azure, le package du Gestionnaire de réseau est supprimé.
3. Lorsqu’un ordinateur virtuel est configuré avec une adresse IP statique et est basculé sur Azure, l’adresse IP est acquis via DHCP. Lorsque vous faites basculer arrière sur prem, la machine virtuelle continue à utiliser DHCP pour obtenir l’adresse IP. Vous devez connecter manuellement dans la machine et le jeu de sauvegarde de l’adresse IP à adresse statique si nécessaire.
4. Si vous utilisez l’édition gratuite de ESXi 5.5 ou d’édition gratuite de 6 de vSphere Hypervisor, basculement devrait aboutir, mais la restauration automatique ne réussira pas. Vous allez ned mise à niveau vers une licence d’évaluation pour activer la restauration automatique.

## <a name="failing-back-with-expressroute"></a>Défaillant arrière avec ExpressRoute

Vous pouvez basculer en une connexion VPN ou d’un ExpressRoute d’Azure. Si vous souhaitez utiliser ExpressRoute, notez les éléments suivants :

- ExpressRoute doit être configuré sur le réseau virtuel Azure à la source d’échec de machines sur et dans les ordinateurs virtuels d’Azure se trouvent après le basculement.
- Sont répliquées vers un compte de stockage Azure sur un point de terminaison public. Vous devez définir peering public dans ExpressRoute avec le centre de données cible pour la réplication de récupération de Site à utiliser ExpressRoute.



