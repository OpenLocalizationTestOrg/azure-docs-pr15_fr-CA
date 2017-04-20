<properties 
   pageTitle="Échec des machines virtuelles VMware arrière et des serveurs physiques d’Azure à VMware (hérité) | Microsoft Azure" 
   description="Cet article décrit comment basculer d’une machine virtuelle VMware été répliquées vers Azure avec récupération de Site Azure." 
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
   ms.workload="storage-backup-recovery" 
   ms.date="10/05/2016"
   ms.author="ruturajd@microsoft.com"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-from-azure-to-vmware-with-azure-site-recovery-legacy"></a>Échec des machines virtuelles VMware arrière et des serveurs physiques d’Azure pour VMware avec récupération de Site Azure (hérité)

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-failback-azure-to-vmware.md)
- [Azure Portal classique](site-recovery-failback-azure-to-vmware-classic.md)
- [Portail classique Azure (hérité)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


Le service de récupération de Site Azure contribue à votre stratégie de récupération (BCDR) de continuité d’activité et de reprise après sinistre de l’entreprise par l’orchestration de réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire sur site. Pour une vue d’ensemble rapide de lecture [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md)

## <a name="overview"></a>Vue d’ensemble

Cet article décrit l’échec des machines virtuelles VMware arrière et des serveurs physiques Windows/Linux à partir d’Azure sur votre site local une fois que vous avez répliqué à partir de votre site local sur Azure.

>[AZURE.NOTE] Cet article décrit un scénario hérité. Vous devez uniquement utiliser les instructions dans cet article si la réplication vers Azure à l’aide de [ces instructions héritées](site-recovery-vmware-to-azure-classic-legacy.md). Si vous configurez la réplication à l’aide de [déploiement amélioré](site-recovery-vmware-to-azure-classic-legacy.md) , suivez les instructions fournies dans [cet article](site-recovery-failback-azure-to-vmware-classic.md) soit restauré. 


## <a name="architecture"></a>Architecture

Ce diagramme représente le scénario de basculement et de restauration. Les lignes bleues sont les connexions utilisées au cours du basculement. Les lignes rouges représentent les connexions utilisées lors du retour arrière. Les lignes avec des flèches aller sur internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## <a name="before-you-start"></a>Avant de commencer 

- Vous devez ont échoué sur vos ordinateurs virtuels VMware ou les serveurs physiques, et ils doivent être en cours d’exécution dans Azure.
- Notez que vous ne pouvez qu’échouer arrière machines virtuelles de VMware et des serveurs physiques Windows/Linux à partir d’Azure pour les machines virtuelles VMware du site principal sur site.  Si vous êtes remise en place d’une machine physique, basculement vers Azure convertira une Azure VM et restauration VMware, il sera converti à une VM de VMware.

Voici comment configurer la restauration automatique :

1. **Configurer les composants de la restauration**: vous aurez besoin configurer un serveur de vContinuum sur site et qu’il pointe vers le serveur de configuration VM dans Azure. Vous allez également configurer un serveur de traitement comme un Azure VM pour envoyer des données sur le serveur de la cible principale sur site. Vous enregistrez le serveur auprès du serveur de configuration gérée le basculement. Vous installez d’un serveur de la cible principale sur site Si vous avez besoin d’un serveur cible principal de Windows il est défini automatiquement lorsque vous installez vContinuum. Si vous avez besoin de Linux, vous devrez configurer manuellement sur un serveur distinct.
2. **Activer la protection et restauration**: une fois que vous avez configuré les composants, dans vContinuum vous devez activer la protection d’a échoué sur Azure VM. Vous allez exécuter une vérification de la disponibilité sur les ordinateurs virtuels et exécuter un basculement sur incident d’Azure sur votre site local. Une fois la restauration terminée vous restaurez protection machines des locaux afin qu’ils commencent à répliquer vers Azure.



## <a name="step-1-install-vcontinuum-on-premises"></a>Étape 1 : Installer vContinuum sur site

Vous devez installer un serveur de vContinuum sur les locaux et le pointer vers le serveur de configuration.

1.  [Télécharger vContinuum](http://go.microsoft.com/fwlink/?linkid=526305). 
2.  Téléchargez la version [mise à jour de vContinuum](http://go.microsoft.com/fwlink/?LinkID=533813) .
3. Installez la dernière version de vContinuum. Dans la page **Bienvenue** , cliquez sur **suivant**.
    ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  Sur la première page de l’Assistant, spécifiez l’adresse IP du serveur CX et le port du serveur CX. Sélectionnez **utiliser HTTPS**.

    ![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Trouver le serveur de configuration adresse IP sous l’onglet **tableau de bord** du serveur de configuration VM dans Azure.
    ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Rechercher le serveur de configuration de port HTTPS du public sur l’onglet **points de terminaison** du serveur de configuration VM dans Azure.

    ![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. Sur la page de **Détails de mot de passe de CS** , spécifiez le mot de passe que vous avez noté vers le bas lorsque vous avez enregistré le serveur de configuration. Si vous ne vous souvenez pas il archiver **C:\\Program Files (x86)\\InMage systèmes\\privé\\connection.passphrase** sur le serveur de configuration VM.

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  Dans la page **Sélectionner l’emplacement de Destination** , indiquez où vous souhaitez installer le serveur de vContinuum et cliquez sur **installer**.

    ![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Une fois l’installation terminée, vous pouvez lancer vContinuum.
    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## <a name="step-2-install-a-process-server-in-azure"></a>Étape 2 : Installer un serveur de traitement dans Azure 

Vous devez installer un serveur de traitement dans Azure afin que les ordinateurs virtuels dans Azure peuvent renvoyer les données à un serveur de la cible principale sur site. 

1.  Sur la page de **Configuration des serveurs** dans Azure, sélectionnez Ajouter un nouveau serveur.

    ![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Spécifier un serveur de nom et un nom et un mot de passe pour vous connecter à l’ordinateur virtuel en tant qu’administrateur. Sélectionnez le serveur de configuration auquel vous souhaitez enregistrer le serveur de traitement. Ce doit être le même serveur que vous utilisez pour protéger et échouer sur vos ordinateurs virtuels.
3.  Spécifiez le réseau Azure dans lequel le serveur de traitement doit être déployé. Il doit être le même réseau que le serveur de configuration. Spécifier un sous-réseau d’adresses sélectionné IP unique et commencer le déploiement.

    ![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Une tâche est déclenchée pour déployer le serveur de traitement.

    ![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Vous pouvez vous connecter au serveur en utilisant les informations d’identification que vous avez spécifié après le déploiement du serveur de traitement dans Azure. Inscrire le serveur de traitement de la même façon que vous avez enregistré le serveur de traitement sur place. 

    ![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] Les serveurs enregistrés au cours de la restauration automatique ne sont pas visibles dans les propriétés de machine virtuelle dans la récupération de Site. Ils sont uniquement visibles sous l’onglet **serveurs** du serveur de configuration auquel ils sont inscrits. Il peut prendre environ 10 à 15 minutes avant d’être le serveur de traitement s’affiche sous l’onglet.


## <a name="step-3-install-a-master-target-server-on-premises"></a>Étape 3 : Installer un serveur cible principal sur site

Selon votre système d’exploitation de machines virtuelles de source, vous devez installer une Linux ou locaux sur un serveur cible principale de Windows.

### <a name="deploy-a-windows-master-target-server"></a>Déploiement d’un serveur cible principal de Windows

Une cible principale de Windows est déjà fournie avec le programme d’installation vContinuum. Lorsque vous installez le vContinuum, un serveur principal est également déployé sur le même ordinateur et enregistré sur le serveur de configuration.

1.  Pour commencer le déploiement, créer un vide de l’ordinateur local sur l’hôte ESX sur lequel vous souhaitez restaurer les ordinateurs virtuels à partir d’Azure.

2.  Vérifiez qu’il existe au moins deux disques attachés à la machine virtuelle : un est utilisé pour le système d’exploitation et l’autre est utilisé pour le lecteur de rétention.

3.  Installez le système d’exploitation.

4.  Installer le vContinuum sur le serveur. Installation du serveur cible principal est également terminée.

### <a name="deploy-a-linux-master-target-server"></a>Déploiement d’un serveur cible principale de Linux

1.  Pour commencer le déploiement, créer un vide de l’ordinateur local sur l’hôte ESX sur lequel vous souhaitez restaurer les ordinateurs virtuels à partir d’Azure.

2.  Vérifiez qu’il existe au moins deux disques attachés à la machine virtuelle : un est utilisé pour le système d’exploitation et l’autre est utilisé pour le lecteur de rétention.

3.  Installez le système d’exploitation Linux. Le système cible principale de Linux ne devez pas utiliser de LVM pour la racine ou la conservation des espaces de stockage. Par défaut, un serveur maître cible de Linux est configuré pour éviter la découverte de partitions/disques LVM.
4.  Partitions que vous pouvez créer :

    ![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Effectuer la valider ci-dessous les étapes d’installation avant de commencer l’installation de la cible principale.


#### <a name="post-os-installation-steps"></a>Valider les étapes de l’Installation du système d’exploitation

Pour obtenir les ID SCSI pour chaque disque SCSI dans une machine virtuelle Linux, activez le paramètre « disque. EnableUUID = TRUE » comme suit :

1. Arrêter votre machine virtuelle.
2. Avec le bouton droit à l’entrée de la machine virtuelle dans le panneau de gauche > **Modifier les paramètres**.
3. Cliquez sur l’onglet **Options** . Sélectionnez **Avancé\>général de l’article** > **Les paramètres de Configuration**. L’option de **Paramètres de Configuration** n’est disponible que lorsque l’ordinateur est arrêté.

    ![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Vérifie si une ligne avec disque de **. EnableUUID** existe. Si la valeur est **False** et donnez-lui la valeur **True** (non sensible à la casse). Si existe et est définie à true, cliquez sur **Annuler** et tester la commande SCSI dans le système d’exploitation invité une fois qu’il est démarré. Si n’existe pas cliquez sur **Ajouter une ligne**.
5. Ajoutez le disque. EnableUUID dans la colonne **nom** . Affectez-lui la valeur True. Ne pas ajouter les valeurs ci-dessus avec des guillemets.

    ![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### <a name="download-and-install-the-additional-packages"></a>Téléchargez et installez les packages supplémentaires

Remarque : Vérifiez que le système possède une connexion internet avant de télécharger et d’installer des packages supplémentaires.

\#YUM install -y xfsprogs perl lsscsi rsync wget kexec-outils

Cette commande télécharge ces 15 packages CentOS 6.6 référentiel et les installe :

BC-1.06.95-1.el6.x86\_64. rpm

busybox-1.15.1-20.el6.x86\_64. rpm

elfutils-libs-0.158-3.2.el6.x86\_64. rpm

kexec-outils-2.0.0-280.el6.x86\_64. rpm

lsscsi-0,23-2.el6.x86\_64. rpm

2.03-lzo-3.1.el6\_5.1.x86\_64. rpm

Perl-5.10.1-136.el6\_6.1.x86\_64. rpm

Perl-Module-connectable-3.90-136.el6\_6.1.x86\_64. rpm

Perl-clavier-souris-échappement-1,04-136.el6\_6.1.x86\_64. rpm
    
Perl-clavier-souris-Simple-3.13-136.el6\_6.1.x86\_64. rpm

Perl-libs-5.10.1-136.el6\_6.1.x86\_64. rpm

Perl-version-0.77-136.el6\_6.1.x86\_64. rpm

rsync-3.0.6-12.el6.x86\_64. rpm

réactivité 1.1.0-1.el6.x86\_64. rpm

1.12-wget-5.el6\_6.1.x86\_64. rpm

Remarque : Si l’ordinateur source utilise le système de fichiers Reiser ou XFS pour le périphérique racine ou de démarrage, puis les packages suivants doivent être téléchargés et installés sur la cible principale de Linux avant de protection.

\#CD/usr/local

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\#rpm - ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64. rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64. rpm

\#wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\#rpm - ivh xfsprogs-3.1.1-16.el6.x86\_64. rpm

#### <a name="apply-custom-configuration-changes"></a>Appliquer les modifications de configuration personnalisée

Avant d’appliquer ces modifications vous assurer que vous avez terminé la section précédente, puis procédez comme suit :


1. Copiez le binaire RHEL 6-64 unifiée Agent pour le système d’exploitation nouvellement créé.

2. Exécutez cette commande pour décompressez le fichier binaire : **tar - zxvf \<nom de fichier\> **

3. Exécutez la commande suivante pour accorder les autorisations : \# **les./ApplyCustomChanges.sh 755 chmod**

4. Exécuter le script : ** \# ./ApplyCustomChanges.sh**. Exécutez le script une seule fois sur le serveur. Redémarrez le serveur après l’exécution du script.



### <a name="install-the-linux-server"></a>Installez le serveur Linux


1. [Téléchargez](http://go.microsoft.com/fwlink/?LinkID=529757) le fichier d’installation.
2. Copiez le fichier sur la machine virtuelle Linux Masque cible à l’aide d’un utilitaire de client sftp de votre choix. Vous pouvez également vous pouvez ouvrir une session sur l’ordinateur virtuel du maître cible Linux et utiliser wget pour télécharger le package d’installation à partir du lien fourni
3. Ouvrez une session sur le Linux cible master server machine virtuelle à l’aide un ssh client de votre choix.
4. Si vous êtes connecté au réseau sur lequel vous avez déployé votre serveur cible principale de Linux via une connexion VPN Azure ensuite utiliser l’adresse IP interne du serveur que vous trouverez dans l’onglet **tableau de bord** de machine virtuelle et le port 22 pour se connecter à la cible principale de Linux Server à l’aide de Secure Shell.
5. Si vous vous connectez au serveur Linux cible maître via une connexion internet publique utiliser adresse IP publique virtuelle du serveur Linux cible principale (à partir de l’onglet de **tableau de bord** de machines virtuelles) et le point de terminaison public créé pour ssh pour se connecter à la servder de Linux.
6. Extraire les fichiers à partir de l’archive tar compressés Linux cible master Server installer en exécutant : *« tar-xvzf Microsoft ASR\_UA\_8.2.0.0\_RHEL6-64\ »* à partir du répertoire qui contient le fichier de programme d’installation.

    ![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Si vous avez extrait le programme d’installation à modifier les fichiers dans un répertoire différent à l’annuaire afin que le contenu du paquetage archive ont été extraits. À partir de ce chemin d’accès du répertoire exécuter « sudo. / install.sh ».

    ![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Lorsque vous êtes invité à choisir un rôle principal Sélectionnez **2 (maître cible)**. Laissez les autres options d’installation interactive à leurs valeurs par défaut.
9. Attendez pour continuer l’installation et l’Interface de configuration d’hôte s’affiche. L’utilitaire de Configuration de l’hôte de le sarget de maître de Linux Server est un utilitaire de ligne de commande. Ne pas redimensionner le ssh fenêtre de l’utilitaire client. Utilisez les touches de direction pour sélectionner l’option **Global** et appuyez sur la touche entrée de votre clavier.

    ![](./media/site-recovery-failback-azure-to-vmware/image18.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. Dans champ **IP** , entrez l’adresse IP interne du serveur de configuration (vous pouvez le trouver dans l’onglet **tableau de bord** du serveur de configuration VM) et appuyez sur ENTRÉE. Dans la zone **Port** , tapez **22** puis appuyez sur ENTRÉE. 
11.  Laisser **Utiliser HTTPS** comme **Oui**et appuyez sur ENTRÉE.
12.  Entrez le mot de passe qui a été généré sur le serveur de configuration. Si vous utilisez un client de mastic à partir d’un ordinateur Windows à ssh à la machine virtuelle Linux, vous pouvez utiliser Maj + Inser pour coller le contenu du Presse-papiers. Copier le mot de passe vers le Presse-papiers local à l’aide de Ctrl + C et utiliser Maj + Inser pour la coller. Appuyez sur ENTRÉE.
13.  Utilisez la touche flèche droite pour naviguer pour quitter et appuyez sur ENTRÉE. Attendre pour terminer l’installation.

    ![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Si pour une raison quelconque vous n’a pas pu enregistrer votre serveur cible principale de Linux sur le serveur de configuration vous pouvez donc à nouveau en exécutant l’utilitaire de configuration à partir de /usr/local/ASR/Vx/bin/hostconfigcli (vous êtes d’abord nécessaire de définir des autorisations d’accès à ce répertoire en chmod en tant qu’utilisateur super).


#### <a name="validate-master-target-registration"></a>Valider l’inscription la cible principale

Vous pouvez valider que le serveur maître cible a été enregistré avec succès sur le serveur de configuration dans le coffre-fort de la récupération de Site Azure > **Serveur de Configuration** > **Détails du serveur**.

>[AZURE.NOTE] Après avoir inscrit le serveur cible principale, si vous recevez des erreurs de configuration que l’ordinateur virtuel a peut-être été supprimée de Azure ou points de terminaison ne sont pas configurés correctement, il s’agit, car bien que la configuration de la cible principale est détectée par le dndpoints Azure lorsque la cible principale est déployée dans Azure, ce n’est pas true pour un sur site cible principal serveur local. Cela n’affecte pas la restauration automatique et vous pouvez ignorer ces erreurs. 



## <a name="step-4-protect-the-virtual-machines-to-the-on-premises-site"></a>Étape 4 : Protéger les machines virtuelles sur le site local

Vous devez protéger les machines virtuelles sur le site local avant que vous restaurez.

### <a name="before-you-begin"></a>Avant de commencer

Lorsqu’un ordinateur virtuel est basculé vers Azure, il ajoute un lecteur supplémentaire temporaire pour le fichier de la page. Il s’agit d’un lecteur supplémentaire qui n’est généralement pas requis par votre a échoué sur une machine virtuelle dans la mesure où il est peut-être déjà un lecteur dédié pour le fichier d’échange. Avant de commencer une protection indirecte des ordinateurs virtuels, vous devez vous assurer que ce lecteur est mis hors connexion afin qu’elle ne pas être protégé. Procédez comme suit :

1.  Ouvrez Gestion de l’ordinateur, puis sélectionnez la gestion du stockage afin qu’elle répertorie les disques en ligne et connecté à l’ordinateur.
2.  Sélectionnez le disque temporaire est relié à l’ordinateur et choisir de mettre hors connexion. 

### <a name="protect-the-vms"></a>Protéger les ordinateurs virtuels

1. Dans le portail Azure, vérifier l’état de l’ordinateur virtuel afin qu’il vous a basculé.

    ![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Lancer vContinuum sur votre ordinateur.

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Cliquez sur **Nouvelle Protection** et sélectionnez le type de système d’opération, le 

4.  Dans la nouvelle fenêtre qu’Ouvrir permet de sélectionner le **type de système d’exploitation** > **Obtenir des détails** pour les ordinateurs virtuels que vous souhaitez restaurer. **Détails du serveur principal**, identifiez et sélectionnez les ordinateurs virtuels que vous souhaitez protéger. Ordinateurs virtuels sont répertoriées sous le nom d’hôte de vCenter qu’ils étaient avant le basculement.
5.  Lorsque vous sélectionnez un ordinateur virtuel à protéger (et il a déjà échoué vers Azure) une fenêtre pop-up propose deux entrées pour la machine virtuelle. C’est parce que le serveur de configuration détecte deux instances des machines virtuelles enregistrés à. Vous devez supprimer l’entrée pour la machine virtuelle sur site afin que vous pouvez protéger l’ordinateur virtuel approprié. Pour identifier l’entrée correcte Azure VM ici, vous pouvez vous connecter à la machine virtuelle d’Azure et accédez à C:\Program Files (x86) \Microsoft Azure Site Recovery\Application Data\etc. Dans le fichier drscout.conf, identifiez l’ID hôte. Dans la boîte de dialogue vContinuum, conserver l’entrée qui correspondent à l’ID d’hôte sur l’ordinateur virtuel. Supprimer toutes les autres entrées. Pour sélectionner l’ordinateur virtuel approprié, vous pouvez faire référence à son adresse IP. IP adresse plage sur site sera la machine virtuelle sur site.

    ![](./media/site-recovery-failback-azure-to-vmware/image22.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. Sur le serveur vCenter arrêter la machine virtuelle. Vous pouvez également supprimer les ordinateurs virtuels locaux.
7. Spécifiez ensuite le serveur MT local auquel vous souhaitez protéger les ordinateurs virtuels. Pour ce faire, connectez-vous au serveur vCenter à laquelle vous souhaitez basculer.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Sélectionnez le serveur cible maître basé sur l’hôte auquel vous souhaitez récupérer la machine virtuelle.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Fournir l’option de réplication pour chacun des ordinateurs virtuels. Pour ce faire, vous devez sélectionner le magasin de données de côté de récupération auquel les ordinateurs virtuels seront récupérés. Le tableau récapitule les différentes options que vous devez fournir pour chaque machine virtuelle.

    ![](./media/site-recovery-failback-azure-to-vmware/image25.png)

    **Option** | **Option recommandée de valeur**
    ---|---
    Adresse IP du serveur de traitement | Sélectionnez le serveur de processus déployé dans Azure
    Taille de rétention en Mo| 
    Valeur de rétention | 1
    Jours/heures | Jours
    Intervalle de cohérence | 1
    Sélectionnez le magasin de données cible | Le magasin de données disponible sur le site de récupération. Le magasin de données doit avoir suffisamment d’espace et être disponible pour l’hôte ESX sur lequel vous souhaitez restaurer l’ordinateur virtuel.


10. Configurer les propriétés de la machine virtuelle cherche après le basculement vers le site local. Les propriétés sont résumées dans le tableau suivant.

    ![](./media/site-recovery-failback-azure-to-vmware/image26.png)

    **Propriété** | **Détails**
    ---|---
    Configuration du réseau| Pour chaque carte réseau est détectée, sélectionnez-le et cliquez sur **Modifier** pour configurer l’adresse IP de basculement de la machine virtuelle. 
    Configuration matérielle| Spécifier le processeur et la mémoire de la machine virtuelle. Paramètres peuvent être appliqués à tous les ordinateurs virtuels que vous essayez de protéger. Pour identifier les valeurs correctes pour le processeur et la mémoire, vous pouvez faire référence à la taille du rôle VM de IAAS et afficher le nombre de cœurs et de la mémoire allouée.
    Nom complet | Après la restauration en local, vous pouvez renommer les ordinateurs virtuels tels qu’ils apparaîtront dans le stock de vCenter. Le nom par défaut est le nom d’hôte ordinateur machine virtuelle. Pour identifier le nom de la machine virtuelle, vous pouvez faire référence à la liste de la machine virtuelle dans le groupe de Protection.
    Configuration NAT | Décrit en détail ci-dessous

    ![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### <a name="configure-nat-settings"></a>Configurer les paramètres de NAT

1. Pour activer la protection des machines virtuelles, les deux canaux de communication doivent être établies. La première chaîne est entre l’ordinateur virtuel et le serveur de traitement. Ce canal collecte les données de la machine virtuelle et l’envoie au serveur de processus qui envoie les données vers le serveur cible principale. Si le serveur de traitement et de l’ordinateur virtuel pour être protégés sont sur le même réseau virtuel Azure vous n’avez pas besoin d’utiliser les paramètres de NAT. Dans le cas contraire, spécifiez les paramètres NAT. Permet d’afficher l’adresse IP publique du serveur de traitement dans Azure. 

    ![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. Le second canal est entre le serveur et le serveur cible principale. La possibilité d’utiliser NAT ou non dépend d’à l’aide d’une connexion VPN en fonction ou de la communication sur l’internet. Ne sélectionnez pas NAt si vous utilisez un VPN, mais uniquement si vous utilisez une connexion internet.

    ![](./media/site-recovery-failback-azure-to-vmware/image29.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Si vous n’avez pas supprimé les machines virtuelles sur site comme spécifié et si vous ne pouvez pas toujours dans le magasin de données contient l’ancien fichiers VMDK de, vous devrez vous assurer que la machine virtuelle est créée dans un nouvel emplacement de restauration. Pour cela, sélectionnez les paramètres **Avancé** et spécifiez un autre dossier pour restaurer dans les **Paramètres de nom de dossier**. Laissez les autres options à leurs paramètres par défaut. Appliquer les paramètres de nom de dossier à tous les serveurs.

    ![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Exécuter une vérification de la disponibilité pour garantir que les ordinateurs virtuels sont prêtes à être protégés en local. 

    ![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Attendez qu’elle se termine. S’il est correctement sur tous les ordinateurs virtuels, vous pouvez spécifier un nom pour le plan de protection. Puis, cliquez sur **protéger** pour commencer.

    ![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. Vous pouvez surveiller la progression dans vContinuum.

    ![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Après l’étape de **Qu'activation d’un Plan de Protection** se termine, vous pouvez surveiller protection de machine virtuelle dans le portail de récupération de Site.

    ![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Vous pouvez voir le statut exact en cliquant sur la machine virtuelle et la surveillance de sa progression.

    ![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## <a name="prepare-the-failback-plan"></a>Préparer le plan de restauration

Vous pouvez préparer un plan de restauration à l’aide de vContinuum afin que l’application peut être basculée sur le site local à tout moment. Ces plans de récupération sont très similaires aux plans de restauration de la récupération de Site.

1.  Lancement de vContinuum, puis sélectionnez **Gérer les plans de** > **restauration.** Vous pouvez voir de la liste de tous les plans qui ont été utilisés pour basculer sur les machines virtuelles. Vous pouvez utiliser les mêmes plans pour récupérer.

    ![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Sélectionnez le plan de protection et de tous les ordinateurs virtuels que vous souhaitez restaurer dans il. Lorsque vous sélectionnez chaque machine virtuelle, vous pouvez voir plus de détails, y compris le serveur ESX cible et le disque de la machine virtuelle source. Cliquez sur **suivant** pour lancer l’Assistant de récupération et de sélectionner les ordinateurs virtuels que vous souhaitez récupérer.

    ![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Vous pouvez récupérer en fonction de plusieurs options, mais nous vous recommandons de vous utilisez **Dernière balise** et sélectionnez **Appliquer pour tous les ordinateurs virtuels** pour s’assurer que les données les plus récentes à partir de l’ordinateur virtuel seront utilisées.
4. Exécuter le **vérification de la disponibilité.** Il vérifie si les paramètres de droit sont configurés pour activer la récupération de la machine virtuelle. Si toutes les vérifications sont réussies, cliquez sur **suivant** . Si ce n’est pas le cas, vérifiez le journal et résoudre les erreurs.

    ![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Dans **Configuration de l’ordinateur virtuel** , vérifiez que les paramètres de récupération sont définies correctement. Vous pouvez modifier les paramètres de la machine virtuelle si vous avez besoin.

    ![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Passez en revue la liste des ordinateurs virtuels qui seront récupérés et spécifiez un ordre de recouvrement. Notez que les ordinateurs virtuels sont répertoriés à l’aide du nom d’hôte de l’ordinateur. Il peut être difficile de mapper le nom de l’ordinateur hôte à l’ordinateur virtuel.
Pour mapper les noms, consultez le **tableau de bord** dans Azure des machines virtuelles et vérifiez le nom d’hôte de la machine virtuelle.

    ![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Spécifiez un nom de plan et sélectionnez **récupérer ultérieurement**. Nous vous recommandons de récupérer ultérieurement dans la mesure où la protection initiale n’est peut-être pas complète. 
11. Cliquez sur **récupérer** pour enregistrer le plan, ou de déclencher la récupération si vous avez choisi de récupérer maintenant et au plus tard. Vous pouvez vérifier l’état de la récupération pour voir si le plan de l’été enregistré.

    ![](./media/site-recovery-failback-azure-to-vmware/image42.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## <a name="recover-virtual-machines"></a>Restaurer des machines virtuelles

Après que le plan de l’avoir créé, vous pouvez récupérer les ordinateurs virtuels. Avant que vous l’activez que les ordinateurs virtuels ont terminé la synchronisation. Si l’état de la réplication indique OK puis la protection est terminée et le seuil RPO a été atteint. Vous pouvez vérifier l’état de santé dans les propriétés de la machine virtuelle.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Activer les ordinateurs virtuels Azure avant de lancer la restauration. Cela garantit qu’il n’y a aucun fractionnement et que les utilisateurs n’accèdent seulement une seule copie de l’application. 


1.  Démarrez le plan enregistré. Dans vContinuum, sélectionnez **Moniteur** les plans. Cette liste répertorie tous les plans qui ont été exécutés.

    ![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Sélectionnez le plan de **récupération** , cliquez sur **Démarrer**. Vous pouvez surveiller la récupération. Une fois que les ordinateurs virtuels ont été activées en vous y connecter dans vCenter.

    ![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## <a name="protect-to-azure-again-after-failback"></a>Protéger sur Azure après restauration

Une fois la restauration terminée, vous voudrez probablement protéger les ordinateurs virtuels à nouveau. Procédez comme suit :

1.  Vérifiez que les ordinateurs virtuels locaux fonctionnent et que les applications sont accessibles.
2.  Dans le portail de la récupération de Site d’Azure, sélectionnez les ordinateurs virtuels et les supprimer. Sélectionnez cette option pour désactiver la protection des machines virtuelles. Cela garantit que les ordinateurs virtuels ne sont pas plus protégés.
3.  Dans Azure supprimer l’échec des machines virtuelles Azure
4.  Supprimez l’ancienne machine virtuelle sur vSpehere. Il s’agit d’ordinateurs virtuels que vous avez basculé précédemment vers Azure.
5.  Dans la restauration du Site portail protéger les ordinateurs virtuels qui ont récemment basculé. Une fois qu’ils sont protégés, vous pouvez les ajouter à un plan de récupération.
 
## <a name="next-steps"></a>Étapes suivantes



- [En savoir plus sur](site-recovery-vmware-to-azure-classic.md) réplication des machines virtuelles VMware et des serveurs physiques vers Azure à l’aide de déploiement amélioré.

 
