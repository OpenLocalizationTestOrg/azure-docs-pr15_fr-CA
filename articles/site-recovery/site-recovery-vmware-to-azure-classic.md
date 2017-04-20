<properties
    pageTitle="Répliquer les machines virtuelles VMware et des serveurs physiques vers Azure avec récupération de Site Azure | Microsoft Azure"
    description="Cet article décrit comment déployer la récupération de Site Azure pour orchestrer la réplication, de basculement et de récupération des locaux des machines virtuelles VMware et des serveurs physiques Linux/Windows Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Répliquer les machines virtuelles VMware et des serveurs physiques vers Azure avec récupération de Site Azure

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-vmware-to-azure.md)
- [Portail classique](site-recovery-vmware-to-azure-classic.md)
- [Portail classique (hérité)](site-recovery-vmware-to-azure-classic-legacy.md)


Le service de récupération de Site Azure contribue à votre stratégie de récupération (BCDR) de continuité d’activité et de reprise après sinistre de l’entreprise par l’orchestration de réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire sur site. Pour une vue d’ensemble rapide de lecture [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md).

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment :

- **Machines virtuelles de VMware de répliquer vers Azure**— déployer la récupération de Site afin de coordonner la réplication, le basculement et la récupération du local des machines virtuelles VMware pour le stockage Azure.
- **Répliquer les serveurs physiques vers Azure**: récupération de Site déployer Azure pour coordonner la réplication, le basculement et la restauration des serveurs sur site physiques Windows et Linux vers Azure.

>[AZURE.NOTE] Cet article décrit comment répliquer sur Azure. Si vous souhaitez répliquer des ordinateurs virtuels VMware ou les serveurs physiques Windows/Linux à un centre de données secondaire, suivez les instructions de [cet article](site-recovery-vmware-to-vmware.md).

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="enhanced-deployment"></a>Déploiement amélioré

Cet article inclut contient des instructions pour un déploiement amélioré dans le portail Azure classique. Nous vous recommandons de qu'utiliser cette version pour tous les nouveaux déploiements. Si vous avez déjà déployé à l’aide de l’ancienne version antérieure, nous vous recommandons de migrer vers la nouvelle version. Lire [plus](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment) à propos de la migration.

Le déploiement amélioré est une importante mise à jour. Voici un résumé des améliorations que nous avons :

- **Aucune infrastructure de machines virtuelles dans Azure**: répliquent les données directement dans un compte de stockage Azure. En outre pour la réplication et le basculement sur incident, il est inutile de configurer toute infrastructure de machines virtuelles (serveur de configuration, serveur cible principale) que nous avions besoin du déploiement hérité.  
- **Installation d’unifiée**: une seule installation offre simplicité d’installation et de l’évolutivité pour les composants locaux.
- **Déploiement sécurisé**: tout le trafic est crypté et communications de gestion de réplication sont envoyées via HTTPS 443.
- **Points de récupération**: points de prise en charge de l’incident et la récupération des applications pour les environnements Windows et Linux, et prend en charge les deux configurations de cohérence VM et multi-VM à un seul.
- **Tester le basculement**: prise en charge de basculement sur incident sans interruption de service de test pour Azure, sans impact sur la production ou la suspension de la réplication.
- **Basculement non planifié**: prise en charge de basculement non planifié pour Azure améliorée permet d’arrêter automatiquement les ordinateurs virtuels avant le basculement.
- **Restauration**: restauration intégrée qui réplique uniquement les modifications delta sur le site local.
- **vSphere 6.0**: prise en charge pour les déploiements de VMware Vsphere 6.0 limitée.


## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>Comment restauration du Site m’aide-t-il à protéger les ordinateurs virtuels et les serveurs physiques ?


- Les administrateurs de VMware peuvent configurer une protection hors site vers Azure de grosses charges de travail et les applications s’exécutant sur des machines virtuelles VMware. Gestionnaires de serveur peuvent répliquer des serveurs Windows et Linux sur site physique vers Azure.
- La console de récupération de Site Azure fournit un emplacement unique pour la simplicité d’installation et de gestion de la réplication, le basculement et les processus de récupération.
- Si vous répliquez des machines virtuelles VMware qui sont gérés par un serveur vCenter, récupération de Site peuvent découvrir automatiquement les ordinateurs virtuels. Si les ordinateurs sont sur un hôte ESXi récupération de Site découvre les ordinateurs virtuels sur l’hôte.
- Exécuter les basculements faciles à partir de votre infrastructure sur site Azure et d’Azure, restauration (restauration), aux serveurs VMware VM dans le site local.
- Configurer les plans de récupération qui regroupent des charges de travail applicatives qui sont montés en cascade sur plusieurs ordinateurs. Vous pouvez basculer sur ces plans et récupération de Site fournit une cohérence multi-VM afin que les ordinateurs qui exécutent les mêmes charges de travail peuvent être récupérées conjointement à un point de cohérence des données.


## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

### <a name="windows64-bit-only"></a>Windows (64 bits uniquement)
- Windows Server 2008 R2 SP1 +
- Windows Server 2012
- Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (64 bits uniquement)
- Red Hat Enterprise Linux 6.7, 7.1, 7.2
- CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2
- Oracle Enterprise Linux 6.4, 6.5 exécute le noyau compatible de Red Hat ou incassable entreprise noyau version 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Architecture de scénario

Composants du scénario :

- **Un serveur de gestion sur site**: le serveur d’administration exécute les composants de la récupération de Site :
    - **Serveur de configuration**: coordonne la communication et gère les processus de réplication et de récupération de données.
    - **Serveur de traitement**: sert de passerelle de réplication. Il reçoit des données à partir des machines d’origine protégée, il optimise avec la mise en cache, de compression et de cryptage et envoie des données de réplication au stockage Azure. Il gère l’installation par diffusion du service de mobilité pour les ordinateurs protégés et effectue la découverte automatique des ordinateurs virtuels VMware.
    - **Maître du serveur cible**: gère les données de réplication au cours de la restauration à partir d’Azure.
    Vous pouvez également déployer un serveur de gestion qui agit comme un serveur de traitement, afin de faire évoluer votre déploiement.
- **Service de la mobilité**: ce composant est déployé sur chaque ordinateur (VMware VM ou serveur physique) que vous souhaitez répliquer sur Azure. Il capture les écritures de données sur la machine et les transmet au serveur de traitement.
- **Azure**: vous n’avez pas besoin de créer les ordinateurs virtuels d’Azure pour gérer la réplication et le basculement. Le service de récupération de Site assure la gestion des données et répliquent les données directement au stockage Azure. Répliqué Azure VM est lancé automatiquement uniquement lors du basculement vers Azure. Toutefois, si vous souhaitez basculer d’Azure le site local, vous devrez configurer un Azure VM d’agir comme un serveur de traitement.


Le graphique illustre l’interaction de ces composants.

![architecture](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figure 1 : VMware/physiques vers Azure** (créé par Henry Robalino)


## <a name="capacity-planning"></a>Planification de la capacité

Lorsque vous envisagez de capacité, ici ce que vous devez penser à :

- **L’environnement source**: planification de la capacité ou les exigences de machine VMware infrastructure et source.
- **Le serveur d’administration**: planification pour les serveurs d’administration locaux qui exécutent des composants de récupération de Site.
- **La bande passante du réseau à partir de la source vers la cible**-planification de la bande passante réseau requise pour la réplication entre la source et Azure

### <a name="source-environment-considerations"></a>Considérations sur l’environnement source

- **Maximum quotidiennement modifier taux**— un ordinateur protégé peut utiliser uniquement un serveur de traitement et un serveur unique peut gérer jusqu'à 2 To de la modification de données par jour. 2 To est donc que le plus de données quotidiennes modifier taux qui est pris en charge pour un ordinateur protégé.
- **Le débit maximal**, un ordinateur répliqué peut appartenir à un compte de stockage dans Azure. Un compte de stockage standard peut gérer un maximum de 20 000 requêtes par seconde, et nous vous conseillons de conserver le nombre des Ops ES/s sur une machine source à 20 000. Pour l’exemple si vous disposez d’une machine source avec 5 disques et chaque disque génère des 120 IOPS (8 Ko la taille) la source il sera dans le Azure par la limite d’e/s de disque de 500. Le nombre de comptes de stockage requises = total source Ops ES/s/20000.


### <a name="management-server-considerations"></a>Considérations relatives au serveur de gestion

Le serveur d’administration exécute les composants de récupération de Site qui gèrent l’optimisation des données, de réplication et de gestion. Il doit être en mesure de gérer la capacité journalière de taux de change entre toutes les charges de travail en cours d’exécution sur les ordinateurs protégés et a une bande passante suffisante pour la réplication continue des données de stockage Azure. En particulier :

- Le serveur de traitement reçoit des données de réplication à partir d’ordinateurs protégés et l’optimise avec la mise en cache, de compression et de chiffrement avant de l’envoyer vers Azure. Le serveur d’administration doit avoir suffisamment de ressources pour effectuer ces tâches.
- Le serveur utilise le cache disque. Nous vous recommandons un disque cache distinct de 600 Go ou plus pour gérer les modifications apportées aux données stockées en cas de goulot d’étranglement du réseau ou de panne. Pendant le déploiement, vous pouvez configurer le cache sur un lecteur disposant d’au moins 5 Go de stockage disponible mais 600 Go est la recommandation minimale.
- Comme meilleure pratique, nous recommandons que le serveur de gestion se trouver sur le même réseau et un segment de réseau local que les ordinateurs que vous souhaitez protéger. Il peut se trouver sur un réseau différent, mais vous souhaitez protéger des machines doivent avoir L3 visibilité sur votre réseau pour qu’il.

Recommandations sur la taille du serveur d’administration sont résumées dans le tableau suivant.

**UC du serveur de gestion** | **Mémoire** | **Taille du cache disque** | **Taux de modification des données** | **Ordinateurs protégés**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 noyaux de @ 2.5 GHz) | 16 GO | 300 GO | 500 Go ou moins | Déployer un serveur de gestion avec ces paramètres pour répliquer de moins de 100 ordinateurs.
12 vCPUs (2 sockets * 6 cœurs @ 2.5 GHz) | 18 GO | 600 GO | 500 Go à 1 to | Déployer un serveur de gestion avec ces paramètres pour répliquer entre 100-150 ordinateurs.
16 vCPUs (2 sockets * 8 cœurs @ 2.5 GHz) | 32 GO | 1 TO | 1 To à 2 To | Déployer un serveur de gestion avec ces paramètres pour répliquer entre 150 et 200 ordinateurs.
Déployer un autre serveur de traitement | | | > 2 To | Déployer des serveurs de traitement supplémentaires si vous utilisez la réplication de plus de 200 ordinateurs, ou si les données quotidiennes modifier taux dépasse 2 To.

Où :

- Chaque ordinateur source est configuré avec 3 disques de 100 Go.
- Nous avons utilisé les évaluation des performances de stockage de 8 disques durs SAS 10 k tr/min avec RAID 10 pour les mesures de disque cache.

### <a name="network-bandwidth-from-source-to-target"></a>Bande passante du réseau à partir de la source vers la cible
Assurez-vous que vous calculez la bande passante requise pour la réplication initiale et de la réplication delta à l’aide de l' [outil capacity planner](site-recovery-capacity-planner.md)

#### <a name="throttling-bandwidth-used-for-replication"></a>La limitation de bande passante utilisée pour la réplication

Le trafic de VMware répliqué vers Azure passe par un certain serveur de traitement. Vous pouvez limiter la bande passante disponible pour la réplication de récupération de Site sur ce serveur comme suit :

1. Ouvrir le composant logiciel enfichable MMC de Microsoft Azure sauvegarde sur le serveur d’administration principal ou sur un serveur d’administration en cours d’exécution supplémentaires mis en service des serveurs de traitement. Par défaut un raccourci pour Microsoft Azure Backup est créé sur le bureau, ou vous pouvez le trouver dans : C:\Program Files\Microsoft Azure récupération Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.

    ![Limiter la bande passante](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. Sur l’onglet **régulation** spécifier la bande passante qui peut être utilisée pour la réplication de la récupération de Site et la planification applicable.

    ![Limiter la bande passante](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Éventuellement vous pouvez également définir la limitation de l’utilisation de PowerShell. Voici un exemple :

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>Optimisation de l’utilisation de la bande passante
Vous devez modifier une clé de Registre pour augmenter la bande passante utilisée pour la réplication lors de la récupération de Site Azure.

La clé suivante contrôle le nombre de threads par réplication de disque qui sont utilisés lors de la réplication

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 Dans un réseau de « overprovisioned », cette clé de Registre doit être modifié à partir des valeurs par défaut de son. Nous prenons en charge un maximum de 32.  


[En savoir plus](site-recovery-capacity-planner.md) sur la planification détaillée de la capacité.

### <a name="additional-process-servers"></a>Serveurs de traitement supplémentaires

Si vous avez besoin protéger plus de 200 ordinateurs ou taux de modification quotidien est supérieur que 2 To que vous pouvez ajouter des serveurs supplémentaires pour gérer la charge. Pour mettre à l’échelle les vous pouvez :

- Augmenter le nombre de serveurs d’administration. Par exemple, vous pouvez protéger jusqu'à 400 machines avec deux serveurs d’administration.
- Ajouter des serveurs de traitement supplémentaires et les utiliser pour gérer le trafic au lieu de (ou en plus) le serveur d’administration.

Ce tableau décrit un scénario dans lequel :

- Permet de paramétrer le serveur d’administration d’origine pour l’utiliser comme un seul serveur de configuration.
- Permet de paramétrer un serveur de processus supplémentaires.
- Vous configurez des ordinateurs virtuels protégés pour utiliser le serveur de traitement supplémentaires.
- Chaque ordinateur protégé source est configuré avec trois disques de 100 Go.

**Serveur de gestion d’origine**<br/><br/>(serveur de configuration) | **Serveur de traitement supplémentaires**| **Taille du cache disque** | **Taux de modification des données** | **Ordinateurs protégés**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 noyaux de @ 2.5 GHz), 16 Go de mémoire | 4 vCPUs (2 sockets * 2 noyaux @ 2.5 GHz), 8 Go de mémoire | 300 GO | 250 Go ou moins | Vous pouvez répliquer les machines inférieur ou égal à 85.
8 vCPUs (2 sockets * 4 noyaux de @ 2.5 GHz), 16 Go de mémoire | 8 vCPUs (2 sockets * 4 noyaux de @ 2.5 GHz), 12 Go de mémoire | 600 GO | De 250 Go à 1 to | Vous pouvez répliquer entre 85-150 ordinateurs.
12 vCPUs (2 sockets * 6 cœurs @ 2.5 GHz), 18 Go de mémoire | 12 vCPUs (2 sockets * 6 cœurs @ 2.5 GHz) 24 Go de mémoire | 1 TO | 1 To à 2 To | Vous pouvez répliquer entre 150 et 225 machines.


La façon dont vous mettre à l’échelle vos serveurs est dépend de vos préférences pour une échelle des ou évoluer de modèle.  Vous évoluez en déployant un nombre restreint de gestion haut de gamme et de serveurs de traitement, ou évoluer par le déploiement de plusieurs serveurs avec moins de ressources. Par exemple : Si vous avez besoin protéger les 220 machines peut procéder à une des opérations suivantes :

- Configurer le serveur de gestion d’origine avec 12vCPU, 18 Go de mémoire, un serveur de traitement supplémentaires 12vCPU, 24 Go de mémoire et configurer des ordinateurs protégés pour utiliser uniquement le serveur de processus supplémentaires.
- Ou vous pouvez également configurer deux serveurs d’administration (2 x 8vCPU, 16 Go de RAM) et deux serveurs de processus supplémentaires (1 x 8vCPU) et 4vCPU x 1 pour gérer les ordinateurs (220) 135 + 85 et configurer des ordinateurs protégés pour utiliser uniquement les serveurs de processus supplémentaires.


[Suivez les instructions](#deploy-additional-process-servers) pour configurer un serveur de processus supplémentaires.

## <a name="before-you-start-deployment"></a>Avant de commencer le déploiement

Les tableaux résument les conditions préalables pour le déploiement de ce scénario.


### <a name="azure-prerequisites"></a>Conditions préalables Azure

**Condition préalable** | **Détails**
--- | ---
**Compte Azure**| Vous devez disposer d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification de la récupération de Site.
**Stockage Azure** | Vous devez posséder un compte de stockage Azure pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et d’Azure VM sont lancé lors du basculement. <br/><br/>Vous avez besoin d’un [compte de stockage redondant geo standard](../storage/storage-redundancy.md#geo-redundant-storage). Le compte doit être dans la même région que le service de récupération de Site et être associé à l’abonnement de même. Notez que la réplication vers les comptes de stockage premium n’est pas actuellement pris en charge et ne doit pas être utilisée.<br/><br/>Nous ne supportent pas le déplacement des comptes de stockage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) entre des groupes de ressources. [En savoir plus sur](../storage/storage-introduction.md) Stockage Azure.<br/><br/>
**Réseau Azure** | Vous aurez besoin d’un réseau virtuel Azure qui se connectera à Azure VM lors du basculement. Le réseau virtuel Azure doit être dans la même région, comme le coffre-fort de la récupération de Site.<br/><br/>Notez que pour restauré après le basculement vers Azure vous aurez besoin d’un réseau privé virtuel connexion (ou Azure ExpressRoute) configuré à partir du réseau Azure sur le site local.


### <a name="on-premises-prerequisites"></a>Conditions préalables du local

**Condition préalable** | **Détails**
--- | ---
**Serveur d’administration** | Vous avez besoin d’un serveur de Windows 2012 R2 sur site s’exécutant sur un ordinateur virtuel ou d’un serveur physique. Tous les composants de la récupération de Site local sont installés sur ce serveur d’administration<br/><br/> Nous vous recommandons de que déployer le serveur comme un VM VMware hautement disponible. Retour arrière vers le site local d’Azure est toujours être aux machines virtuelles de VMware que vous n’avez pas sur les ordinateurs virtuels ou des serveurs physiques. Si vous ne configurez pas le serveur d’administration comme un VM VMware, vous devez configurer un serveur cible principal distinct comme une VM VMware à recevoir du trafic de retour arrière.<br/><br/>Le serveur ne doit pas être un contrôleur de domaine.<br/><br/>Le serveur doit disposer d’une adresse IP statique.<br/><br/>Le nom d’hôte du serveur doit être de 15 caractères ou moins.<br/><br/>Les paramètres régionaux du système d’exploitation doivent être uniquement en anglais.<br/><br/>Le serveur d’administration requiert un accès à internet.<br/><br/>Vous devez accéder à partir du serveur comme suit : accès temporaire sur HTTP 80 lors de l’installation des composants de récupération de Site (à télécharger MySQL) ; Accès sortant continue sur HTTPS 443 pour la gestion de la réplication ; Accès sortant continue sur HTTPS 9443 le trafic de réplication (ce port peut être modifié)<br/><br/> Assurez-vous que ces URL est accessibles à partir du serveur de gestion : <br/>- \*. hypervrecoverymanager.windowsazure.com<br/>- \*. accesscontrol.windows.net<br/>- \*. backup.windowsazure.com<br/>- \*. blob.core.windows.net<br/>- \*. store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi]( https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Si vous avez des règles de pare-feu basé sur l’adresse IP sur le serveur, vérifiez que les règles autorisent les communications vers Azure. Vous devez autoriser les [Plages d’Azure centre de données IP](https://www.microsoft.com/download/details.aspx?id=41653) et le port HTTPS (443). Vous devrez également liste d’autorisation des plages d’adresses IP pour la région Azure de votre abonnement et ouest des États-Unis. L' URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") est pour le téléchargement de MySQL.
**VMware vCenter/ESXi hôte**: | Vous devez au moins vMware vSphere ESX/ESXi les hyperviseurs gérer vos ordinateurs virtuels VMware, exécutant ESX/ESXi version 6.0, 5.5 ou 5.1 avec les dernières mises à jour.<br/><br/> Nous vous recommandons de que déployer un serveur de vCenter VMware pour gérer vos hôtes ESXi. Il doit être exécuté vCenter version 6.0 ou 5.5 avec les dernières mises à jour.<br/><br/>Notez que la récupération de Site ne prend pas en charge le nouveau vCenter et vSphere 6.0 fonctionnalités croisent telle que vCenter vMotion et DRS de stockage volumes virtuels. Prise en charge de récupération de site est limité aux fonctionnalités qui étaient également disponibles dans la version 5.5.
**Les machines protégé**: | **AZURE**<br/><br/>Ordinateurs à protéger doivent être conformes avec [conditions préalables Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) pour la création d’ordinateurs virtuels d’Azure.<br><br/>Si vous souhaitez vous connecter aux ordinateurs virtuels Azure après basculement vous devrez activer les connexions Bureau à distance sur le pare-feu local.<br/><br/>Capacité de chaque disque sur les ordinateurs protégés ne doit pas être supérieure à 1 023 go. Une machine virtuelle peut avoir jusqu'à 64 disques (donc jusqu'à 64 To). Si vous avez plus de 1 To de disques envisager à l’aide de la réplication de base de données comme SQL Server Always On ou Oracle Data Guard.<br/><br/>Minimum de 2 Go d’espace disponible sur le lecteur d’installation pour l’installation des composants.<br/><br/>Partagé invité disque clusters ne sont pas pris en charge. Si vous avez un déploiement en clusters envisager d’utiliser la réplication de base de données comme SQL Server Always On ou Oracle Data Guard.<br/><br/>Unified Extensible Firmware Interface UEFI () Extensible Firmware Interface(EFI) démarrage n’est pas pris en charge.<br/><br/>Noms de machine doivent contenir entre 1 et 63 des caractères (lettres, chiffres et traits d’union). Le nom doit commencer par une lettre ou un nombre et se terminer par une lettre ou un chiffre. Une fois une machine est protégée, vous pouvez modifier le nom Azure.<br/><br/>**Ordinateurs virtuels VMware**<br/><br>Vous devez installer VMware vSphere PowerCLI 6.0. sur le serveur de gestion (serveur de configuration).<br/><br/>Machines virtuelles de VMware à protéger doit avoir les outils VMware installé et en cours d’exécution.<br/><br/>Si la source de machine virtuelle a l’association de cartes réseau, il est converti à une seule carte réseau après le basculement vers Azure.<br/><br/>Si les ordinateurs virtuels protégés ont un disque iSCSI récupération de Site convertit ensuite le disque iSCSI VM protégé dans un fichier de disque dur virtuel lorsque l’ordinateur virtuel bascule vers Azure. Si la cible iSCSI peut être atteint par la machine virtuelle d’Azure puis il se connecter à la cible iSCSI et essentiellement voir deux disques – le disque dur sur l’ordinateur virtuel d’Azure et disque iSCSI source. Dans ce cas, vous devez vous déconnecter de la cible iSCSI qui apparaît sur la panne sur Azure VM.<br/><br/>[En savoir plus](#vmware-permissions-for-vcenter-access) sur les autorisations d’utilisateur VMware qui sont nécessaires à la récupération de Site.<br/><br/> **MACHINES de serveur WINDOWS (sur VMware VM ou serveur physique)**<br/><br/>Le serveur doit exécuter un système d’exploitation 64 bits pris en charge : Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2 avec au moins SP1.<br/><br/>Le système d’exploitation doit être installé sur le lecteur C:\ et le disque du système d’exploitation doit être un disque de base Windows (système d’exploitation ne doit pas être installé sur un disque dynamique Windows.)<br/><br/>Pour les ordinateurs Windows Server 2008 R2, vous devez disposer de.NET Framework 3.5.1 installé.<br/><br/>Vous devez fournir un compte d’administrateur (doit être d’un administrateur local sur l’ordinateur Windows) pour l’installation par diffusion du Service de la mobilité sur des serveurs Windows. Si le compte fourni est un compte de domaine, vous devez désactiver le contrôle de l’accès utilisateur à distance sur l’ordinateur local. [En savoir plus](#install-the-mobility-service-with-push-installation).<br/><br/>Récupération de site prend en charge les ordinateurs virtuels avec un disque RDM.  Lors du retour arrière récupération de Site va réutiliser le disque RDM si le disque de machine virtuelle et RDM source d’origine est disponible. Si elles ne sont pas disponibles, lors du retour arrière récupération de Site crée un nouveau fichier VMDK pour chaque disque.<br/><br/>**ORDINATEURS LINUX**<br/><br/>Vous aurez besoin d’un système d’exploitation de prise en charge 64 bits : Red Hat Enterprise Linux 6.7 ; CentOS 6.5, 6.6,6.7 ; Oracle Enterprise Linux 6.4, 6.5 exécute le noyau compatible de Red Hat ou incassable entreprise noyau version 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>les fichiers/etc/hosts sur les ordinateurs protégés doivent contenir des entrées qui mappent le nom d’hôte local pour les adresses IP associées à toutes les cartes réseau. <br/><br/>Si vous souhaitez vous connecter à un ordinateur virtuel Azure exécutant Linux après le basculement à l’aide d’un SSH client (ssh), assurez-vous que le service de Shell sécurisé sur l’ordinateur protégé est défini pour démarrer automatiquement au démarrage du système, et que les règles de pare-feu autorisent un ssh connexion à ce dernier.<br/><br/>Protection ne peut être activée que pour les machines Linux avec le stockage suivant : fichier système (EXT3, ETX4, ReiserFS, XFS) ; Multipath-périphérique logiciel Mappeur (multipath)) ; Le Gestionnaire de volumes : (LVM2). Serveurs physiques avec le stockage HP CCISS contrôleur ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur le Service Pack 3 de SUSE Linux Enterprise Server 11.<br/><br/>Récupération de site prend en charge les ordinateurs virtuels avec un disque RDM.  Lors du retour arrière pour Linux, récupération de Site ne réutiliser le disque RDM. Au lieu de cela, il crée un nouveau fichier VMDK pour chaque disque RDM correspondant.

Uniquement pour les VM Linux - vous assurer que le paramètre disk.enableUUID=true dans les paramètres de Configuration de la machine virtuelle dans VMware. Si cette ligne n’existe pas, ajoutez-la. Ceci est nécessaire pour fournir un UUID cohérent pour le VMDK afin qu’il monte correctement. Notez également que sans ce paramètre, le retour arrière entraîne un téléchargement complet même si l’ordinateur virtuel est disponible sur prém. Ajout de ce paramètre permet de garantir que seules les modifications delta sont transférées pendant la restauration automatique.

## <a name="step-1-create-a-vault"></a>Étape 1 : Création d’un coffre-fort

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).
2. Développez **Services de données** > **Services de récupération** et cliquez sur le **Coffre de récupération de Site**.
3. Cliquez sur **créer une nouvelle** > **Création rapide**.
4. Dans la zone **nom**, entrez un nom convivial pour identifier le coffre-fort.
5. Dans la **zone**, sélectionnez la zone géographique pour la chambre forte. Pour vérifier les régions pris en charge consultez disponibilité géographique dans les [Détails de la tarification de récupération de Site de Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Cliquez sur **créer coffre-fort**.
    ![Nouveau coffre-fort](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Vérifiez la barre d’état pour confirmer que le coffre-fort a été créé avec succès. Le coffre-fort apparaît comme **actif** sur la page principale **Des Services de récupération** .

## <a name="step-2-set-up-an-azure-network"></a>Étape 2 : Configuration d’un réseau Azure

Configurer un réseau Azure afin que les ordinateurs virtuels d’Azure sera connecté à un réseau après le basculement, et pour que le retour arrière vers le site local peut fonctionner comme prévu.

1. Dans le portail Azure > **créer des réseaux virtuels** spécifier le nom du réseau. Nom sous-réseau et de la plage d’adresse IP.
2. Vous devez ajouter VPN/ExpressRoute sur le réseau si vous avez besoin effectuer la restauration. VPN/ExpressRoute peuvent être ajouté au réseau, même après le basculement.

[Pour en savoir plus](../virtual-network/virtual-networks-overview.md) sur les réseaux Azure.

> [AZURE.NOTE] [Migration de réseaux](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les réseaux utilisés pour le déploiement de récupération de Site.

## <a name="step-3-install-the-vmware-components"></a>Étape 3 : Installer les composants de VMware

Si vous souhaitez répliquer VMware virtual machines installer les composants de VMware suivants sur le serveur d’administration :

1. [Télécharger](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) et installer VMware vSphere PowerCLI 6.0.
2. Redémarrez le serveur.


## <a name="step-4-download-a-vault-registration-key"></a>Étape 4 : Télécharger une clé d’enregistrement de coffre-fort

1. À partir de la gestion serveur ouvrir la console de récupération de Site dans Azure. Dans les **Services de récupération** de la page, cliquez sur le coffre pour ouvrir la page de démarrage rapide. Démarrage rapide peut également être ouvert à tout moment à l’aide de l’icône.

    ![Icône de démarrage rapide](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. Sur la page de **Démarrage rapide** , cliquez sur **Préparer les ressources cible** > **télécharger une clé d’enregistrement**. Le fichier d’enregistrement est généré automatiquement. Il est valable 5 jours après sa création.


## <a name="step-5-install-the-management-server"></a>Étape 5 : Installation du serveur de gestion
> [AZURE.TIP] Assurez-vous que ces URL est accessibles à partir du serveur de gestion :
>
- *. hypervrecoverymanager.windowsazure.com
- *. accesscontrol.windows.net
- *. backup.windowsazure.com
- *. blob.core.windows.net
- *. store.core.windows.net
- https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. Sur la page de **Démarrage rapide de** télécharger le fichier d’installation unifiée sur le serveur.

2. Exécutez le fichier d’installation pour démarrer le programme d’installation de l’Assistant Installation d’unifiée de récupération de Site.

3.  Dans **avant de commencer** , sélectionnez **installer le serveur de configuration et d’un serveur de traitement**.

    ![Avant de commencer](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. Dans la **Licence de logiciel de tierce partie** , cliquez sur **J’accepte** pour télécharger et installer MySQL. 

    ![Troisième = logiciels de tiers](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)

5. Dans **l’enregistrement** Parcourir et sélectionnez la clé d’inscription que vous avez téléchargé à partir de la chambre forte.

    ![Inscription](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

6. Dans **Les paramètres Internet** , spécifiez comment le fournisseur en cours d’exécution sur le serveur de configuration se connectera à la récupération de Site Azure via internet.

    - Si vous souhaitez vous connecter avec le proxy qui est actuellement configuré sur l’ordinateur, sélectionnez **se connecter avec les paramètres de serveur proxy existants**.
    - Si vous souhaitez que le fournisseur pour vous connecter directement, sélectionnez **se connecter directement sans proxy**.
    - Si le proxy existant requiert une authentification, ou que vous souhaitez utiliser un proxy personnalisé pour la connexion du fournisseur, sélectionnez **se connecter avec les paramètres de proxy personnalisés**.
        - Si vous utilisez un proxy personnalisé, que vous devez spécifier les informations d’identification, le port et l’adresse
        - Si vous utilisez un proxy que vous avez déjà il convient des URL suivantes :
            - *. hypervrecoverymanager.windowsazure.com ;    
            - *. accesscontrol.windows.net ; 
            - *. backup.windowsazure.com ; 
            - *. blob.core.windows.net ; 
            - *. store.core.windows.net
            

    ![Pare-feu](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

7. Dans **Prerequisites Check** , le programme d’installation s’exécute une vérification pour s’assurer que l’installation peut exécuter. 

    
    ![Conditions préalables](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     Si un message d’avertissement apparaît à propos de la **vérification de la synchronisation de temps globale** Vérifiez que l’heure de l’horloge système (paramètres de**Date et heure** ) est le même que le fuseau horaire.

    ![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. **Configuration de MySQL** de créer les informations d’identification pour vous connecter à l’instance de serveur MySQL qui est installé.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

9. Dans Sélectionner des **Détails d’environnement** si vous allez répliquer des ordinateurs virtuels VMware. Dans ce cas, le programme d’installation vérifie que PowerCLI 6.0 est installé.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

10. Dans, sélectionnez **L’emplacement d’installation** où vous souhaitez installer les fichiers binaires et de stocker le cache. Vous pouvez sélectionner un lecteur disposant d’au moins 5 Go de stockage disponible, mais nous vous recommandons d’un lecteur de cache au moins 600 Go d’espace libre.

    ![Emplacement d’installation](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

11. **Sélection du réseau** permet de spécifier le port d’écoute (carte réseau et le port SSL) sur lequel le serveur de configuration envoient et reçoivent des données de réplication. Vous pouvez modifier la valeur par défaut du port (9443). En plus de ce port, le port 443 sera utilisé par un serveur web qui orchestre les opérations de réplication. 443 ne doit pas être utilisée pour recevoir le trafic de réplication.


    ![Sélection du réseau](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



12.  En **Résumé** , examinez les informations et cliquez sur **installer**. Lorsque l’installation est terminée, un mot de passe est généré. Vous en aurez besoin lorsque vous activez la réplication donc copier et conservez-le dans un endroit sûr.

    ![Résumé](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)



13.  En **Résumé** , examinez les informations.

    ![Résumé](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

>[AZURE.WARNING]Proxy de Microsoft Azure Service l’Agent de récupération doit être le programme d’installation.
>Une fois l’installation terminée, lancez une application nommée « Microsoft Azure récupération Services Shell » dans le menu Démarrer de Windows. Dans la fenêtre de commande qui ouvre le, exécutez l’ensemble de commandes vous permet de configurer les paramètres du serveur proxy suivant.
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine



### <a name="run-setup-from-the-command-line"></a>Exécutez le programme d’installation à partir de la ligne de commande

Vous pouvez également exécuter l’Assistant unifié comme suit à partir de la ligne de commande :

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Où :

- / ServerMode : obligatoire. Spécifie si l’installation doit installer les serveurs de configuration et de processus ou seulement le serveur de traitement (utilisé pour installer des serveurs de traitement supplémentaires). Valeurs d’entrée : CS, PS.
- InstallDrive : obligatoire. Spécifie le dossier dans lequel les composants sont installés.
- / MySQLCredFilePath. Obligatoire. Spécifie le chemin d’accès à un fichier dans lequel les informations d’identification du serveur MySQL sont histoire. Obtenir le modèle pour créer le fichier.
- / VaultCredFilePath. Obligatoire. Emplacement du fichier d’informations d’identification de coffre-fort
- / EnvType. Obligatoire. Type d’installation. Valeurs : VMware, NonVMware
- / PSIP et /CSIP. Obligatoire. Adresse IP du serveur et du serveur de configuration.
- / PassphraseFilePath. Obligatoire. Emplacement du fichier de mot de passe.
- / ByPassProxy. Facultatif. Spécifie que le serveur de gestion se connecte sur Azure sans proxy.
- / ProxySettingsFilePath. Facultatif. Spécifie les paramètres de serveur proxy personnalisé (proxy par défaut sur le serveur qui requiert l’authentification), ou proxy personnalisé




## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>Étape 6 : Configurer des informations d’identification pour le serveur vCenter

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

Le serveur de traitement peut découvrir automatiquement les ordinateurs virtuels VMware qui sont gérés par un serveur vCenter. Pour la découverte automatique de récupération de Site a besoin d’un compte et les informations d’identification qui peuvent accéder au serveur de vCenter. Ce n’est pas pertinent si vous utilisez la réplication des serveurs physiques.

Procédez comme suit :

1. Sur le vCenter server créer un rôle (**Azure_Site_Recovery**) au niveau vCenter avec les [autorisations requises](#vmware-permissions-for-vcenter-access).
2. Affecter le rôle de **Azure_Site_Recovery** à un utilisateur de vCenter.

    >[AZURE.NOTE] Un compte d’utilisateur vCenter dont le rôle en lecture seule peut exécuter le basculement sans arrêt des machines d’origine protégée. Si vous souhaitez arrêter ces machines, vous devez le rôle Azure_Site_Recovery. Notez que si vous êtes uniquement migration des ordinateurs virtuels VMware vers Azure et n’est pas nécessaire à la restauration automatique le rôle en lecture seule est suffisant.

3. Pour ajouter le compte d’ouvrir **cspsconfigtool**. Il est disponible sous la forme d’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
2. dans l’onglet **Gérer les comptes** , cliquez sur **Ajouter un compte**.

    ![Ajouter compte](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. Ajouter des informations d’identification qui peuvent être utilisées pour accéder au serveur vCenter dans **Détail du compte** . Notez qu’il peut prendre plus de 15 minutes pour le nom du compte apparaît dans le portail. Pour mettre à jour immédiatement, cliquez sur Actualiser sous l’onglet **Serveurs de Configuration** .

    ![Détails](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>Étape 7 : Ajouter des serveurs vCenter et ESXi hôtes

Si vous utilisez la réplication ordinateurs virtuels VMware, vous devez ajouter un serveur de vCenter (ou ESXi hôte).

1. Sur les **serveurs** > **Serveurs de Configuration** , sélectionnez le serveur de configuration > **Ajouter un serveur vCenter**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Ajoutez le serveur vCenter ou ESXi détails de l’hôte, le nom du compte que vous avez spécifié pour accéder au serveur de vCenter à l’étape précédente et le serveur de traitement qui sera utilisé pour découvrir des ordinateurs virtuels VMware qui sont gérés par le serveur vCenter. Notez que le serveur de vCenter ou ESXi hôte doit se trouver dans le même réseau que le serveur sur lequel le serveur de traitement est installé.

    >[AZURE.NOTE] Si vous ajoutez le serveur de vCenter ou l’hôte de ESXi avec un compte qui ne dispose pas des privilèges d’administrateur sur le serveur hôte ou vCenter, puis assurez-vous que le vCenter ou ESXi comptes ces portent activé : Datacenter, magasin de données, dossier, Jost, réseau, ressources, Virtual machine, vSphere distribués commutateur. En outre, le serveur vCenter nécessite le privilège de vues de stockage.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Une fois la découverte terminée le serveur vCenter sera répertorié dans l’onglet **Serveurs de Configuration** .

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## <a name="step-8-create-a-protection-group"></a>Étape 8 : Création d’un groupe de protection

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


Les groupes de protection sont des groupements logiques d’ordinateurs virtuels ou des serveurs physiques que vous souhaitez protéger à l’aide des mêmes paramètres de protection. Vous appliquez des paramètres de protection pour un groupe de protection, et ces paramètres sont appliqués à tous les ordinateurs de machines virtuelles/physiques que vous ajoutez au groupe.

1. Ouvrir les **Éléments de protection** > **Groupe de Protection** , puis cliquez sur Ajouter un groupe de protection.

    ![Créer le groupe de protection](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. Dans la page **Spécifier les paramètres du groupe de Protection** spécifier un nom pour le groupe et dans la **zone de** , sélectionnez le serveur de configuration sur lequel vous souhaitez créer le groupe. **La cible** est Azure.

    ![Paramètres de groupe de protection](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. Dans la page **Spécifier les paramètres de réplication** , configurez les paramètres de réplication qui seront utilisées pour tous les ordinateurs dans le groupe.

    ![Réplication de groupe de protection](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

    - **La cohérence de plusieurs VM**: Si vous activez cette option elle crée des points de partagé récupération des applications sur les ordinateurs dans le groupe de protection. Ce paramètre est plus approprié lorsque tous les ordinateurs du groupe de protection sont en cours d’exécution la même charge de travail. Tous les ordinateurs seront récupérés au même point de données. Cette option est disponible si vous utilisez la réplication ordinateurs virtuels VMware ou les serveurs physiques Windows/Linux.
    - **Seuil RPO**: définit le RPO. Alertes seront générées lors de la réplication de la protection continue des données est supérieure à la valeur de seuil RPO configurée.
    - **Rétention de point de récupération**: Spécifie la fenêtre de la rétention. Ordinateurs protégés peuvent être récupérées à tout moment dans cette fenêtre.
    - **Fréquence des snapshots cohérents au niveau application**: Spécifie la fréquence des points de récupération contenant des snapshots cohérents avec l’application doit être créées.

Lorsque vous cliquez sur la coche un groupe de protection sera créé avec le nom spécifié. In addition un second groupe de protection est créé avec le nom < protection-groupe-nom-retour arrière). Ce groupe de protection est utilisé si vous restauré vers le site local après le basculement vers Azure. Vous pouvez contrôler les groupes de protection qu’elles sont créées sur la page **Les éléments protégés** .

## <a name="step-9-install-the-mobility-service"></a>Étape 9 : Installer le service de la mobilité

La première étape de l’activation de la protection de machines virtuelles et des serveurs physiques est d’installer le service de la mobilité. Vous pouvez pour cela de deux façons :

- Distribuer et installer le service sur chaque ordinateur du serveur de traitement automatiquement. Notez que lorsque vous ajoutez des ordinateurs à un groupe de protection et ils sont déjà une version appropriée de l’installation par diffusion de service de mobilité en cours d’exécution ne se produit pas.
- Installer automatiquement le service en utilisant votre méthode de transmission entreprise telle que WSUS ou System Center Configuration Manager. Assurez-vous que vous avez configuré le serveur d’administration avant de le faire.
- Installer manuellement sur chaque ordinateur que vous souhaitez protéger. aire que vous avez configuré le serveur d’administration avant de le faire.


### <a name="install-the-mobility-service-with-push-installation"></a>Installer le service de la mobilité avec l’installation par diffusion

Lorsque vous ajoutez des ordinateurs à un groupe de protection du service de la mobilité est automatiquement poussé et installé sur chaque ordinateur par le serveur de traitement.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Préparer pour la distribution automatique sur les machines Windows

Voici comment préparer des machines Windows afin que le service de la mobilité peut être installé automatiquement par le serveur de traitement.

1.  Créer un compte qui peut être utilisé par le serveur de traitement pour accéder à l’ordinateur. Le compte doit disposer des privilèges d’administrateur (local ou domaine). Notez que ces informations d’identification sont uniquement utilisées pour l’installation par diffusion du service de la mobilité.

    >[AZURE.NOTE] Si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle de l’accès utilisateur à distance sur l’ordinateur local. Pour ce faire, ajoutez l’entrée DWORD LocalAccountTokenFilterPolicy avec une valeur de 1 dans le Registre sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System sous. Pour ajouter le Registre entrée à partir d’une interface CLI ouvrir la commande ou à l’aide de PowerShell entrez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  Le pare-feu Windows de l’ordinateur que vous souhaitez protéger, sélectionnez **Autoriser une application ou une fonctionnalité via le pare-feu** et permettre **de fichiers et de partage d’imprimante** et **Windows Management Instrumentation**. Pour les ordinateurs qui appartiennent à un domaine, vous pouvez configurer la stratégie de pare-feu avec un objet de stratégie de groupe.

    ![Paramètres de pare-feu](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Ajoutez le compte que vous avez créé :

    - Ouvrez **cspsconfigtool**. Il est disponible sous la forme d’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
    - Dans l’onglet **Gérer les comptes** , cliquez sur **Ajouter un compte**.
    - Ajoutez le compte que vous avez créé. Après avoir ajouté le compte, vous devrez fournir les informations d’identification lorsque vous ajoutez un ordinateur à un groupe de protection.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Préparer pour la distribution automatique sur des serveurs Linux

1.  Assurez-vous que l’ordinateur Linux que vous souhaitez protéger est pris en charge comme décrit dans [les conditions locales](#on-premises-prerequisites). Vérifiez qu’il existe la connectivité réseau entre l’ordinateur que vous souhaitez protéger et le serveur d’administration qui s’exécute sur le serveur de traitement.

2.  Créer un compte qui peut être utilisé par le serveur de traitement pour accéder à l’ordinateur. Le compte doit être un utilisateur root sur le serveur source Linux. Notez que ces informations d’identification sont uniquement utilisées pour l’installation par diffusion du service de la mobilité.

    - Ouvrez **cspsconfigtool**. Il est disponible sous la forme d’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
    - Dans l’onglet **Gérer les comptes** , cliquez sur **Ajouter un compte**.
    - Ajoutez le compte que vous avez créé. Après avoir ajouté le compte, vous devrez fournir les informations d’identification lorsque vous ajoutez un ordinateur à un groupe de protection.

3.  Vérifiez que le fichier/etc/hosts sur le serveur Linux source contienne des entrées qui mappent le nom d’hôte local pour les adresses IP associées à toutes les cartes réseau.
4.  Installation des dernière openssh, openssh-server, packages d’openssl sur l’ordinateur que vous souhaitez protéger.
5.  Assurez-vous que SSH est activé et en cours d’exécution sur le port 22.
6.  Activer l’authentification de mot de passe et le sous-système SFTP dans le fichier sshd_config comme suit :

    - Ouvrez une session en tant que racine.
    - Dans le fichier /etc/ssh/sshd_config de fichier, recherchez la ligne qui commence par PasswordAuthentication.
    - Décommentez la ligne et remplacez la valeur **Aucun** à **Oui**.
    - Recherchez la ligne qui commence par le **sous-système** et ne commentez pas la ligne.

        ![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Installer le service de la mobilité manuellement

Les programmes d’installation sont disponibles dans les fichiers de C:\Program (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

Système d’exploitation source | Fichier d’installation de service de mobilité
--- | ---
Windows Server (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-manually-on-a-windows-server"></a>Installer manuellement sur un serveur Windows


1. Téléchargez et exécutez le programme d’installation approprié.
2. Dans **avant de commencer **, sélectionnez **service de la mobilité**.

    ![Service de la mobilité](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. Dans les **Détails de Configuration de serveur** , spécifiez l’adresse IP du serveur d’administration et le mot de passe qui a été généré lorsque vous avez installé les composants de serveur de gestion. Vous pouvez récupérer le mot de passe en exécutant : ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** sur le serveur d’administration.

    ![Service de la mobilité](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. Dans **L’emplacement d’installation** , conservez l’emplacement par défaut et cliquez sur **suivant** pour commencer l’installation.
5. En **Cours de l’Installation de** surveiller l’installation et redémarrez l’ordinateur si vous y êtes invité.

Vous pouvez également installer à partir de la ligne de commande :

UnifiedAgent.exe [/ rôle < Agent/MasterTarget >] [/ InstallLocation <Installation Directory>] [/ CSIP <IP address of CS to be registered with>] [/ PassphraseFilePath <Passphrase file path>] [/ LogFilePath <Log File Path>]

Où :

- / Rôle : obligatoire. Spécifie si le service de la mobilité doit être installé.
- / InstallLocation : obligatoire. Spécifie l’emplacement d’installation du service.
- / PassphraseFilePath : obligatoire. Spécifie le mot de passe de serveur de configuration.
- / LogFilePath : obligatoire. Spécifie l’emplacement des fichiers journaux d’installation

#### <a name="uninstall-mobility-service-manually"></a>Désinstaller manuellement des services de mobilité

Service de mobilité peut être désinstallé à l’aide de l’ajouter supprimer un programme à partir du Panneau de configuration ou à l’aide de la ligne de commande.

La commande pour désinstaller le Service de la mobilité à l’aide de la ligne de commande est

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="modify-the-ip-address-of-the-management-server"></a>Modifier l’adresse IP du serveur d’administration

Après l’exécution de l’Assistant vous pouvez modifier l’adresse IP du serveur d’administration comme suit :

1. Ouvrez la hostconfig.exe fichier (situé sur le bureau).
2. Sous l’onglet **Global** , vous pouvez modifier l’adresse IP du serveur d’administration.

    >[AZURE.NOTE] Vous ne devez modifier l’adresse IP du serveur d’administration. Le numéro de port pour les communications de serveur de gestion doit être 443 et utiliser HTTPS doit être activée. Le mot de passe ne doit pas être modifié.

    ![Adresse IP du serveur Management](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-manually-on-a-linux-server"></a>Installer manuellement sur un serveur Linux :

1. Copiez l’archive tar approprié en fonction du tableau ci-dessus sur l’ordinateur Linux que vous souhaitez protéger.
2. Ouvrez un programme de shell et extraire l’archive tar compressé à un chemin d’accès local en exécutant :`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Créer un fichier passphrase.txt dans le répertoire local dans lequel vous avez extrait le contenu de l’archive tar. Pour effectuer cette permet de copier le mot de passe de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase sur le serveur d’administration et enregistrez-le dans passphrase.txt en exécutant *`echo <passphrase> >passphrase.txt`* dans le shell.
4. Installer le service de la mobilité en entrant *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Spécifiez l’adresse IP interne du serveur d’administration et vérifiez que le port 443 est sélectionné.

**Vous pouvez également installer à partir de la ligne de commande**:

1. Copier le mot de passe à partir de C:\Program Files (x86) les \InMage Systems\private\connection sur le serveur d’administration et l’enregistrer en tant que « passphrase.txt » sur le serveur d’administration. Puis exécutez ces commandes. Dans notre exemple, l’adresse IP du serveur management est 104.40.75.37 et le port HTTPS doit être 443 :

Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Pour installer sur le serveur cible principale :


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>Étape 10 : Activer la protection d’un ordinateur

Pour activer la protection vous ajoutez des machines virtuelles et des serveurs physiques à un groupe de protection. Avant de commencer, notez les points suivants si vous vous protégez des machines virtuelles VMware :

- Ordinateurs virtuels VMware sont découverts toutes les 15 minutes et elle peut prendre plus de 15 minutes pour apparaître dans le portail de reprise de Site après la découverte.
- Modifications de l’environnement sur l’ordinateur virtuel (par exemple, l’installation des outils VMware) peuvent également prendre plus de 15 minutes pour être mis à jour dans récupération du Site.
- Vous pouvez vérifier la dernière découverte pour les machines virtuelles de VMware dans le champ **Dernier Contact à** l’hôte de serveur/ESXi vCenter sous l’onglet **Serveurs de Configuration** .
- Si vous avez déjà créé un groupe de protection et d’ajouter un hôte de serveur ou ESXi vCenter après cela, il peut prendre plus de 15 minutes pour le portail de récupération de Site Azure actualiser et pour les machines virtuelles figurer dans la boîte de dialogue **Ajouter des ordinateurs à un groupe de protection** .
- Si vous souhaitez procéder sans délai à l’ajout d’ordinateurs au groupe de protection sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (ne cliquez pas dessus) et cliquez sur le bouton **Actualiser** .

En outre, notez que :

- Nous vous recommandons de concevoir de vos groupes de protection afin qu’ils reflètent vos charges de travail. Par exemple, ajouter une application spécifique au même groupe d’ordinateurs.
- Lorsque vous ajoutez des ordinateurs à un groupe de protection, le serveur de traitement pousse automatiquement et installe le service de la mobilité s’il n’est pas déjà installé. Notez que vous devrez avoir le mécanisme de diffusion préparer comme décrit à l’étape précédente.


Ajouter des ordinateurs à un groupe de protection :

1. Cliquez sur les **éléments protégés** > **Groupe de Protection** > **Machines** > ajouter des ordinateurs. \As recommandé
2. Dans **Sélectionner les ordinateurs virtuels** si vous vous protégez des machines virtuelles VMware, sélectionnez un serveur vCenter qui gère vos machines virtuelles (ou l’hôte EXSi sur lequel ils sont en cours d’exécution) et puis sélectionnez les ordinateurs.

    ![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  Dans **Sélectionner les ordinateurs virtuels** si vous vous protégez des serveurs physiques, dans l’Assistant **Ajouter des Machines physiques** fournir l’adresse IP et un nom convivial. Sélectionnez la famille de systèmes d’exploitation.

    ![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. De **Spécifier les ressources cible** , sélectionnez le compte de stockage, vous utilisez pour la réplication et permet d’indiquer si les paramètres doivent être utilisés pour toutes les charges de travail. Notez que les comptes de stockage de prime ne sont pas actuellement pris en charge.

    >[AZURE.NOTE] 1.les nous ne supportent pas le déplacement des comptes de stockage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) entre des groupes de ressources.                           2.[la migration des comptes de stockage](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

    ![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. **Spécifiez** les comptes, sélectionnez le compte [configuré](#install-the-mobility-service-with-push-installation) à utiliser pour l’installation automatique du service de la mobilité.

    ![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Cliquez sur la case à cocher pour terminer d’ajouter des ordinateurs au groupe de protection et pour lancer la réplication initiale de chaque machine.

    >[AZURE.NOTE] Si le service de la mobilité a été préparée pour l’installation par diffusion est automatiquement installée sur les ordinateurs que vous ne l’avez pas lorsqu’ils sont ajoutés au groupe de protection. Une fois le service installation une tâche de protection démarre et échoue. Après la défaillance, vous devez redémarrer manuellement chaque ordinateur qui a été installé le service de mobilité. Après le redémarrage la tâche de protection commence à nouveau et la réplication initiale se produit.

Vous pouvez surveiller l’état sur la page **tâches** .

![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

En outre, l’état de protection peut être surveillé dans **Les éléments protégés** > <protection group name> > **Machines virtuelles**. Après la réplication initiale est terminée et que les données sont synchronisées, machine devient** protégé**.

![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## <a name="step-11-set-protected-machine-properties"></a>Étape 11 : Ensemble protégé des propriétés de l’ordinateur

1. Une fois un ordinateur a un état de **protection** , vous pouvez configurer ses propriétés de basculement. Dans les détails de groupe de protection, sélectionnez l’ordinateur et ouvrez l’onglet **configurer** .
2. Récupération de site propose des propriétés de la machine virtuelle d’Azure automatiquement et détecte que les paramètres réseau sur site.

    ![Définir les propriétés de la machine virtuelle](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. Vous pouvez modifier ces paramètres :

    -  **Nom de la machine virtuelle d’Azure**: c’est le nom qui sera donné à la machine dans Azure après le basculement. Le nom doit être conforme aux exigences d’Azure.

    -  **Taille d’Azure VM**: le nombre de cartes réseau dépend de la taille que vous spécifiez pour l’ordinateur virtuel cible. [Pour en savoir plus](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables) sur les tailles et les cartes. Notez que :

        - Lorsque vous modifiez la taille d’une machine virtuelle et enregistrez les paramètres, le nombre de carte réseau changera lors de la prochaine ouverture de l’onglet **configurer** . Le nombre de cartes réseau des ordinateurs virtuels de cible est au minimum le nombre de cartes réseau sur l’ordinateur virtuel source et le nombre maximal de cartes réseau pris en charge par la taille de l’ordinateur virtuel choisi.
            - Si le nombre de cartes réseau sur l’ordinateur source est inférieur ou égal au nombre de cartes autorisé pour la taille de l’ordinateur cible, la cible ont le même nombre de cartes comme source.
            - Si le nombre de cartes sur l’ordinateur virtuel source dépasse le nombre autorisé de la taille cible puis le maximum de la taille cible sera utilisé.
            - Par exemple, si une machine source possède deux cartes réseau et la taille de la machine cible prend en charge quatre, l’ordinateur cible aura deux cartes. Si l’ordinateur source dispose de deux cartes, mais la taille cible prise en charge prend uniquement en charge un l’ordinateur cible aura qu’une seule carte.
        - Si l’ordinateur virtuel possède plusieurs cartes réseau toutes les cartes doivent connecté au même réseau Azure.
    - **Réseau Azure**: vous devez spécifier un réseau Azure qui seront connectés à Azure VM après le basculement. Si vous ne spécifiez pas un l’Azure VM n’est pas connectés à un réseau. En outre, vous devez spécifier un réseau Azure si vous souhaitez effectuer un retour arrière dans Azure le site local. Restauration automatique nécessite une connexion VPN entre un réseau d’Azure et d’un réseau local.
    - **Azure IP/sous-réseau**: pour chaque carte réseau, vous sélectionnez le sous-réseau auquel l’Azure VM doit se connecter. Notez que :
        - Si la carte réseau de l’ordinateur source est configurée pour utiliser une adresse IP statique vous pouvez spécifier une adresse IP statique pour la machine virtuelle d’Azure. Si vous ne fournissez pas une adresse IP statique, puis n’importe quelle adresse IP disponible sera allouée. Si l’adresse IP cible est spécifié, mais il est déjà en cours d’utilisation par une autre VM dans Azure basculement échouera. Si la carte réseau de l’ordinateur source est configurée pour utiliser le protocole DHCP vous aurez DHCP comme paramètre pour Azure.

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>Étape 12 : Créer un plan de récupération et exécuter un basculement sur incident

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

Vous pouvez exécuter un basculement sur incident sur un seul ordinateur ou basculer sur plusieurs machines virtuelles qui effectuent la même tâche ou à exécuter la même charge de travail. Basculer sur plusieurs ordinateurs en même temps que vous les ajoutez à un plan de récupération.

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Sur la page des **Plans de reprise** , cliquez sur **Ajouter un Plan de récupération** et ajouter un plan de récupération. Spécifiez les détails du plan et sélectionnez **Azure** comme cible.

    ![Configurer le plan de récupération](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. Dans **Sélectionner l’ordinateur virtuel** sélectionnez un groupe de protection et sélectionnez ordinateurs dans le groupe à ajouter au plan de récupération.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Vous pouvez personnaliser le plan pour créer des groupes et la séquence de l’ordre dans lequel les ordinateurs dans le plan de récupération sont basculées. Vous pouvez également ajouter des scripts et des invites pour les actions manuelles. Les scripts peuvent être créés manuellement ou à l’aide par les [Procédures opérationnelles de Automation Azure](site-recovery-runbook-automation.md). [En savoir plus](site-recovery-create-recovery-plans.md) sur la personnalisation des plans de reprise.

## <a name="run-a-failover"></a>Exécuter un basculement sur incident

Avant d’exécuter un basculement Notez que :


- Assurez-vous que le serveur d’administration est en cours d’exécution et disponible - sinon basculement échouera.
- Si vous exécutez une note de basculement non planifié qui :

    - Si possible vous devez arrêter les ordinateurs principales avant d’exécuter un basculement non planifié. Cela garantit que vous n’avez pas les ordinateurs source et de réplica en cours d’exécution en même temps. Si vous utilisez la réplication VMware VM lorsque vous exécutez un basculement non planifié vous pouvez ensuite spécifier que récupération de Site doit faire mieux pour arrêter les ordinateurs source. Selon l’état du site principal, cela peut ou peut ne pas fonctionne. Récupération de Site n’offre pas cette option si vous utilisez la réplication des serveurs physiques.
    - Lorsque vous effectuez un basculement non planifié il cesse de réplication des données à partir d’ordinateurs principales afin que n’importe quel delta de données ne seront pas transférée après qu’un basculement non planifié commence.

- Si vous souhaitez vous connecter à la machine virtuelle de réplica dans Azure après le basculement, activer la connexion Bureau à distance sur l’ordinateur source avant d’exécuter le basculement et autoriser la connexion RDP via le pare-feu. Vous devez également permettre de RDP sur le point de terminaison public de la machine virtuelle Azure après le basculement. Suivez ces [recommandations](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) pour garantir le fonctionne RDP après un basculement.

>[AZURE.NOTE] Pour obtenir les meilleures performances lorsque vous effectuez un basculement vers Azure, assurez-vous que vous avez installé l’Agent d’Azure dans l’ordinateur protégé. Cela permet de démarrer plus rapidement et que vous permet également de diagnostic en cas de problèmes. Agent de Linux peut être trouvés [ici](https://github.com/Azure/WALinuxAgent) - et Windows l’agent se trouve [ici](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="run-a-test-failover"></a>Exécutez un test de basculement

Exécuter un basculement de test afin de simuler vos processus de basculement et de récupération dans un réseau isolé sans incidence sur votre environnement de production et de réplication régulières continue normalement. Basculement de test démarre sur la source et vous pouvez l’exécuter de deux façons :

- **Ne spécifiez pas un réseau Azure**: Si vous exécutez un test de basculement sans réseau test vérifie simplement que les ordinateurs virtuels démarrent et s’affichent correctement dans Azure. Ordinateurs virtuels n’est pas connectés à un réseau Azure après le basculement.
- **Spécifier un réseau Azure**: ce type de basculement vérifie que l’environnement de réplication complet est fourni comme prévu et que les ordinateurs virtuels Azure sont connectés au réseau spécifié.


1. Dans la page des **Plans de reprise** , sélectionnez le plan, cliquez sur **Test de basculement**.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. Dans **Confirmer le basculement Test** sélectionnez **None** pour indiquer que vous ne souhaitez pas utiliser un réseau Azure pour le basculement de l’essai, ou sélectionnez le réseau auquel l’ordinateurs virtuels de test sera connecté après le basculement. Cliquez sur la case à cocher pour démarrer le basculement.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Surveiller la progression de basculement dans l’onglet **tâches** .

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Une fois le basculement terminé vous devez également être en mesure de voir le réplica Azure machine apparaissent dans Azure portal > **Machines virtuelles**. Si vous souhaitez établir une connexion RDP de la machine virtuelle d’Azure, vous devez ouvrir le port 3389 sur le point de terminaison de machine virtuelle.

5. Une fois que vous avez terminé, lorsque le basculement atteint la terminer test de phase, cliquez sur Test complet pour terminer. Dans Notes, enregistrement d’observations éventuelles associées à la reprise du test.

6. Cliquez sur **le test de basculement est achevé** nettoie automatiquement l’environnement de test. Après cela, le basculement de test affiche un état **terminé** . Tous les éléments ou ordinateurs virtuels créés automatiquement lors du basculement de test sont supprimés. Notez que si un basculement sur incident test continue de plus de deux semaines, il est terminée en vigueur.



### <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié

Basculement non planifié est initiée à partir d’Azure et peut être effectuée même si le site principal n’est pas disponible.


1. Dans la page des **Plans de reprise** , sélectionnez le plan, cliquez sur **Basculer** > **Basculement non planifié**.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Si vous utilisez la réplication des machines virtuelles VMware, vous pouvez sélectionner pour essayer d’arrêter les ordinateurs virtuels de locaux. Il s’agit de mieux et basculement sur incident se poursuit si l’effort réussisse ou non. Si elle n’aboutit pas les détails de l’erreur seront affiche dans l’onglet **tâches **> **Travaux de basculement non planifié**.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

    >[AZURE.NOTE] Cette option n’est pas disponible si vous utilisez la réplication des serveurs physiques. Vous aurez besoin essayer d’arrêter les manuellement si possible.

3. Dans **Confirmer le basculement** , vérifiez le sens de basculement (vers Azure), puis sélectionnez le point de récupération que vous souhaitez utiliser pour le basculement. Si vous avez activé le multi-VM lors de la configuration des propriétés de réplication, vous pouvez récupérer pour le dernier point de récupération cohérent après sinistre ou des applications. Vous pouvez également sélectionner **des points de récupération de personnalisé** à récupérer à un point antérieur dans le temps. Cliquez sur la case à cocher pour démarrer le basculement.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Attendez la fin du travail de basculement non planifié. Vous pouvez surveiller la progression de basculement dans l’onglet **tâches** . Notez que même si des erreurs se produisent pendant le basculement non planifié le plan de récupération s’exécute jusqu'à ce qu’elle soit terminée. Vous serez également en mesure de voir le réplica Azure machine apparaissent dans des Machines virtuelles dans le portail Azure.

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>Se connecter à répliquer les machines virtuelles Azure après basculement

Pour vous connecter à répliquer les machines virtuelles dans Azure après que basculement ici est ce dont vous aurez besoin :

1. Une connexion Bureau à distance doit être activée sur l’ordinateur principal.
2. Le pare-feu Windows sur l’ordinateur principal pour autoriser le protocole RDP.
3. Après le basculement, vous devrez ajouter RDP au point de terminaison pour les machines virtuelles Azure public.

[Pour en savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) sur cette configuration.


## <a name="deploy-additional-process-servers"></a>Déployer des serveurs de traitement supplémentaires

Si vous avez à faire évoluer votre déploiement au-delà de 200 machines d’origine ou de votre taux d’évolution du quotidien total dépasse la sortie 2 To, vous aurez besoin des serveurs de traitement supplémentaires pour gérer le volume de trafic. Pour configurer un serveur de processus supplémentaires Vérifiez la configuration requise dans les [serveurs de traitement supplémentaires](#additional-process-servers) et puis suivez les instructions pour configurer le serveur de traitement. Après avoir configuré le serveur, vous pouvez configurer des machines d’origine pour l’utiliser.

### <a name="set-up-an-additional-process-server"></a>Configurer un serveur de processus supplémentaires

Permet de paramétrer un serveur de processus supplémentaires comme suit :

- Exécutez l’Assistant unifié pour configurer un serveur d’administration en tant que processus serveur uniquement.
- Si vous souhaitez gérer la réplication de données avec le nouveau serveur de processus, vous devez effectuer la migration de vos machines protégées pour cela.

### <a name="install-the-process-server"></a>Installer le serveur de traitement

1. Sur la page de démarrage rapide de télécharger le fichier d’installation unifié pour l’installation du composant restauration du Site. Exécutez le programme d’installation.
2. Dans **avant de commencer** , sélectionnez **Ajouter des serveurs supplémentaires de processus à l’échelle du déploiement**.

    ![Ajoutez le serveur de traitement](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Terminez l’Assistant de la même manière que vous l’avez fait lorsque vous [Configurez](#step-5-install-the-management-server) le premier serveur d’administration.

4. Dans les **Détails de Configuration de serveur** , spécifiez l’adresse IP du serveur d’administration d’origine sur lequel vous avez installé le serveur de configuration et le mot de passe. Sur le serveur de gestion d’origine exécuter ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** pour obtenir le mot de passe.

    ![Ajoutez le serveur de traitement](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migration des ordinateurs pour utiliser le nouveau serveur de processus

1. Ouvrir la **Configuration des serveurs** > **Server** > nom du serveur d’administration d’origine > **Détails du serveur**.

    ![Mise à jour du serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. Dans la liste des **Serveurs de traitement** cliquez sur **Changer de processus de serveur** correspondante au serveur que vous souhaitez modifier.

    ![Mise à jour du serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. Dans les **Processus de changer de serveur** > sélectionner le nouveau serveur de gestion**Serveur du processus cible** et sélectionnez les ordinateurs virtuels qui gère le nouveau serveur de processus. Cliquez sur l’icône d’informations pour obtenir des informations sur le serveur. L’espace moyen nécessaire à la réplication de chaque ordinateur virtuel sélectionné vers le nouveau serveur de processus s’affiche pour vous aider à prendre des décisions de charger. Cliquez sur la case à cocher pour démarrer la réplication vers le nouveau serveur de processus.

    ![Mise à jour du serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## <a name="vmware-permissions-for-vcenter-access"></a>Autorisations de VMware pour les accès vCenter

Le serveur de traitement peut découvrir automatiquement les ordinateurs virtuels sur un serveur vCenter. Pour exécuter une découverte automatique, vous devrez définir un rôle (Azure_Site_Recovery) au niveau vCenter pour permettre la récupération de Site accéder au serveur de vCenter. Notez que si vous devez uniquement migrer des machines VMware vers Azure et n’est nécessaire à la restauration automatique d’Azure, vous pouvez définir un rôle en lecture seule est suffisant. Vous définissez les autorisations comme décrit dans [étape 6 : définir des informations d’identification pour le serveur vCenter](#step-6-set-up-credentials-for-the-vcenter-server) les autorisations de rôle sont résumées dans le tableau suivant.

**Rôle** | **Détails** | **Autorisations**
--- | --- | ---
Rôle de Azure_Site_Recovery | Découverte de VMware VM |Attribuer ces privilèges pour le serveur du centre-v :<br/><br/>Magasin de données -> Allocate espace, parcourir le magasin de données, les opérations de fichier de niveau faible., suppression du fichier, mise à jour des fichiers de machine virtuelle<br/><br/>Réseau -> attribuer de réseau<br/><br/>Ressources -> machine virtuelle d’affecter au pool de ressources, migration de machine virtuelle hors tension, la migration de machines virtuelles sous tension<br/><br/>Tâches -> créer une tâche, la tâche de mise à jour<br/><br/>Machine virtuelle -> Configuration<br/><br/>Machine virtuelle -> interagir -> répondre à la question, périphérique connexion., configurer un CD, disquettes configurer, alimentation hors tension sur, installer les outils VMware<br/><br/>Machine virtuelle -> stock -> Annuler l’inscription de la créer, Registre,<br/><br/>Machine virtuelle -> Provisioning -> Autoriser télécharger la machine virtuelle, de téléchargement des fichiers de machine virtuelle d’autoriser<br/><br/>Machine virtuelle -> Snapshots -> Supprimer les snapshots
rôle d’utilisateur vCenter | Découverte de VMware VM/basculement sans arrêt de la machine virtuelle de source | Attribuer ces privilèges pour le serveur du centre-v :<br/><br/>Objet de centre de données -> propager vers l’objet enfant, rôle = lecture seule <br/><br/>L’utilisateur est affecté au niveau du centre de données et a donc accès à tous les objets dans le centre de données.  Si vous souhaitez restreindre l’accès, affecter le rôle **Aucun accès** à l’objet de **propager aux enfants** pour les objets enfants (ESX hôtes, magasins de données, ordinateurs virtuels et réseaux).
rôle d’utilisateur vCenter | Basculement et restauration automatique | Attribuer ces privilèges pour le serveur du centre-v :<br/><br/>Objet de centre de données – les propager vers l’objet enfant, rôle = Azure_Site_Recovery<br/><br/>L’utilisateur est affecté au niveau du centre de données et a donc accès à tous les objets dans le centre de données.  Si vous souhaitez restreindre l’accès, attribuer le rôle de **pas d’accès **avec la **propager à un objet enfant** à l’objet enfant (ESX hôtes, magasins de données, ordinateurs virtuels et réseaux).  



## <a name="third-party-software-notices-and-information"></a>Les informations et les avis de logiciels tiers

Ne pas traduire ou localiser

Le logiciel et le microprogramme utilisé dans le produit ou service Microsoft repose sur ou incorpore des matières à partir des projets répertoriés ci-dessous (collectivement, « Code tiers »).  Microsoft est l’auteur de pas d’origine du Code tiers.  La notice de copyright d’origine et de la licence, dans lesquelles Microsoft a reçu un tel Code de tierce partie, sont définis ci-dessous.

Les informations contenues dans la Section A sont en ce qui concerne les composants tiers Code des projets répertoriés ci-dessous. Ces licences et ces informations sont fournies à titre d’information uniquement.  Ce Code de tierce partie est en cours relicensed pour vous par Microsoft sous les termes de licence de logiciel Microsoft pour le produit ou service Microsoft.  

Les informations dans la Section B sont concernant des composants de Code de tiers qui sont en cours mis à votre disposition par Microsoft sous les termes de la licence d’origine.

Vous trouverez le fichier complet sur le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft réserve tous les droits non expressément concédés par les présentes, que ce soit par implication, estoppel ou autrement.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur la restauration automatique](site-recovery-failback-azure-to-vmware-classic.md) pour mettre votre a échoué sur des ordinateurs exécutant dans Azure à votre environnement sur site.
