<properties 
   pageTitle="Automatiser la reprise après sinistre pour les partages de fichiers sur StorSimple à l’aide de la récupération de Site Azure | Microsoft Azure"
   description="Décrit les étapes et les meilleures pratiques pour la création d’une solution de reprise après sinistre pour les partages de fichiers hébergés sur le stockage de StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="vidarmsft"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/16/2016"
   ms.author="vidarmsft" />

# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solution de reprise après sinistre automatisée à l’aide de la récupération de Site Azure pour les partages de fichiers hébergés sur StorSimple

## <a name="overview"></a>Vue d’ensemble

Microsoft Azure StorSimple est une solution de stockage cloud hybride qui gère les complexités de données non structurées couramment associées aux partages de fichiers. StorSimple utilise le stockage en nuage comme une extension de la solution de locaux et automatiquement les données de niveaux sur le stockage de locaux et de stockage en nuage. Intégrée de protection des données, locale et instantanés en nuage, élimine la nécessité d’une infrastructure de stockage immense.

[Récupération de Site Azure](../site-recovery/site-recovery-overview.md) est un service qui fournit des capacités de reprise après sinistre par l’orchestration de réplication, le basculement et la restauration des machines virtuelles basées sur Azure. Récupération de Site Azure prend en charge un certain nombre de technologies de réplication pour répliquer de manière cohérente, de protéger et de basculer en toute transparence des machines virtuelles et les applications pour les nuages publics/privés ou hébergés.

À l’aide de récupération de Site Azure, la réplication de la machine virtuelle et capacités de snapshot de nuage StorSimple, vous pouvez protéger l’environnement de serveur de fichier complet. En cas d’interruption, vous pouvez utiliser un simple clic pour mettre vos partages de fichiers en ligne dans Azure dans quelques minutes.

Ce document explique en détail comment vous pouvez créer une solution de reprise après sinistre pour vos partages de fichiers hébergés sur le stockage de StorSimple et exécuter planifié, planifié et test de basculement à l’aide d’un plan de récupération d’un seul clic. En gros, il montre comment vous pouvez modifier le Plan de récupération dans votre coffre-fort de récupération de Site Azure pour activer les basculements de StorSimple au cours de scénarios de reprise après sinistre. En outre, il décrit les configurations prises en charge et les conditions préalables. Ce document suppose que vous êtes familiarisé avec les concepts de base des architectures de restauration de Site Azure et StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Options de déploiement de récupération de Site Azure pris en charge

Clients peuvent déployer des serveurs de fichiers en tant que serveurs physiques ou virtuels (VM) s’exécutant sur Hyper-V ou VMware et puis créer des partages de fichiers à partir de volumes aménagées à partir de StorSimple stockage. Récupération de Site Azure peuvent protéger les déploiements physiques et virtuels, soit un site secondaire et Azure. Ce document décrit les détails d’une solution de reprise après sinistre avec Azure que le site de récupération pour un ordinateur virtuel hébergé sur Hyper-V de serveur de fichiers et partages de fichiers de stockage de StorSimple. Autres scénarios dans lesquels la machine virtuelle du serveur de fichiers est sur une VM VMware ou sur une machine physique peuvent être implémentés de la même façon.

## <a name="prerequisites"></a>Conditions préalables

Implémentation d’une solution de récupération après sinistre d’un seul clic qui utilise la récupération de Site Azure pour les partages de fichiers hébergés sur le stockage de StorSimple possède les conditions préalables suivantes :

-   Serveur de fichier de Windows Server 2012 R2 que machine virtuelle hébergée sur Hyper-V ou VMware ou sur une machine physique sur site

-   StorSimple stockage périphérique local inscrit avec le Gestionnaire de StorSimple d’Azure

-   Équipement de Cloud StorSimple créé dans le Gestionnaire de StorSimple d’Azure (Cela peut être conservé dans un état d’arrêt)

-   Partages de fichiers hébergés sur les volumes configurés sur le périphérique de stockage de StorSimple

-   [Chambre forte de services de récupération de Site azure](../site-recovery/site-recovery-vmm-to-vmm.md) créé un abonnement Microsoft Azure

En outre, si Azure est votre site de récupération, exécutez l' [outil Readiness Assessment de la Machine virtuelle Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) sur des ordinateurs virtuels pour s’assurer qu’ils sont compatibles avec les services Azure VM et de la récupération de Site Azure.

Pour éviter des problèmes (ce qui peuvent entraîner une hausse des coûts), assurez-vous que vous créez votre solution de Cloud StorSimple, compte d’automation et de stockage de latence compte (s) dans la même région.

## <a name="enable-dr-for-storsimple-file-shares"></a>Activer la reprise après sinistre pour les partages de fichiers StorSimple  

Chaque composant de l’environnement local doit être protégé pour activer la restauration et la réplication terminée. Cette section décrit comment :

-   Définissez la réplication Active Directory et DNS (facultatif)

-   Récupération de Site Azure permet d’activer la protection de la mémoire virtuelle du serveur de fichiers

-   Activer la protection des volumes de StorSimple

-   Configurer le réseau

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Définissez la réplication Active Directory et DNS (facultatif)

Si vous souhaitez protéger les ordinateurs qui exécutent Active Directory et DNS afin qu’ils soient disponibles sur le site de reprise après sinistre, vous devez explicitement les protéger (de sorte que les serveurs de fichiers sont accessibles après basculement avec une authentification). Il existe deux options recommandées en fonction de la complexité de l’environnement du client local.

#### <a name="option-1"></a>Option 1

Si le client dispose d’un petit nombre d’applications, un seul contrôleur de domaine pour l’ensemble du site local et défectueux sur l’ensemble du site, puis nous recommandons l’utilisation de la réplication de la récupération de Site Azure pour répliquer l’ordinateur contrôleur de domaine sur un site secondaire (c’est applicable pour les site à site et site-à-Azure).

#### <a name="option-2"></a>Option 2

Si le client possède un grand nombre d’applications, une forêt Active Directory est en cours d’exécution et basculement quelques applications à la fois, puis nous vous recommandons de configurer le contrôleur de domaine supplémentaire sur le site de reprise après sinistre (soit un site secondaire ou dans Azure).

Reportez-vous à la [solution Automated DR pour Active Directory et DNS à l’aide de la récupération de Site Azure](../site-recovery/site-recovery-active-directory.md) pour obtenir des instructions pour un contrôleur de domaine disponible sur le site de reprise après sinistre. Pour le reste de ce document, nous supposons qu'un contrôleur de domaine est disponible sur le site de reprise après sinistre.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Récupération de Site Azure permet d’activer la protection de la mémoire virtuelle du serveur de fichiers

Cette étape nécessite que vous préparez l’environnement de serveur de fichiers local, créez et préparez un coffre-fort de récupération de Site Azure et activez la protection de fichier de la machine virtuelle.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Pour préparer l’environnement de serveur de fichiers local

1.  Définir le **contrôle de compte d’utilisateur** pour **ne jamais m’avertir**. Ceci est nécessaire afin que vous puissiez utiliser des scripts d’automatisation Azure pour connecter les cibles iSCSI après échec en récupération de Site Azure.

    1.  Appuyez sur la touche Windows + Q et recherche de **compte d’utilisateur**.

    2.  Sélectionnez **paramètres de modifier le contrôle de compte d’utilisateur**.

    3.  Faites glisser la barre vers le bas vers **Ne jamais m’avertir**.

    4.  Cliquez sur **OK** , puis sélectionnez **Oui** lorsque vous y êtes invité.

        ![](./media/storsimple-dr-using-asr/image1.png)

1.  Installer l’Agent de l’ordinateur virtuel sur chaque serveur de fichiers VMs. Ceci est nécessaire afin que vous pouvez exécuter des scripts d’automatisation Azure sur l’échec de la sur des machines virtuelles.

    1.  [Téléchargez l’agent](http://aka.ms/vmagentwin) à `C:\\Users\\<username>\\Downloads`.

    2.  Ouvrir Windows PowerShell dans le mode administrateur (exécuter en tant qu’administrateur) et entrez la commande suivante pour accéder à l’emplacement de téléchargement :

        `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

        > [AZURE.NOTE] Le nom de fichier peut-être changer en fonction de la version.

1.  Cliquez sur **suivant**.

2.  Accepter les **Termes du contrat** , puis sur **suivant**.

3.  Cliquez sur **Terminer**.


1.  Créer des partages de fichiers à l’aide de volumes aménagées à partir de StorSimple stockage. Pour plus d’informations, voir [utiliser le service de gestionnaire de StorSimple pour gérer les volumes](storsimple-manage-volumes.md).

    1.  Sur vos ordinateurs virtuels locaux, appuyez sur la touche Windows + Q et recherche pour **iSCSI**.

    2.  Sélectionnez **l’initiateur iSCSI**.

    3.  Sélectionnez l’onglet **Configuration** et copier le nom de l’initiateur.

    4.  Ouvrez une session [Azure portal classique](https://manage.windowsazure.com/).

    5.  Sélectionnez l’onglet **StorSimple** , puis sélectionnez le Service de gestionnaire de StorSimple qui contient le périphérique physique.

    6.  Créez le conteneur (s) de volume, puis créez ou les volumes. (Ces volumes sont pour les partages de fichiers sur l’ordinateurs virtuels du serveur de fichiers). Copier le nom de l’initiateur et donner un nom approprié pour les enregistrements de contrôle d’accès lorsque vous créez des volumes.

    7.  Sélectionnez l’onglet **configurer** et notez l’adresse IP du périphérique vers le bas.

    8.  Sur vos ordinateurs virtuels locaux, accédez à l' **initiateur iSCSI** à nouveau et entrez l’adresse IP dans la section connexion rapide. Cliquez sur **Connexion rapide** (le périphérique doit désormais être connecté).

    9.  Ouvrir le portail de gestion Azure et sélectionnez l’onglet **Volumes et des périphériques** . Cliquez sur **configuration automatique**. Le volume que vous venez de créer doit apparaître.

    10. Dans le portail, cliquez sur l’onglet **périphériques** , puis sélectionnez **créer un périphérique virtuel.** (Ce périphérique virtuel sera utilisé si un basculement se produit). Ce nouveau périphérique virtuel peut être conservé dans un état hors connexion afin d’éviter des coûts supplémentaires. Pour déconnecter le périphérique virtuel, passez à la section de **Machines virtuelles** sur le portail et arrêtez-le.

    11. Retour aux ordinateurs virtuels locaux et ouvrez Gestion des disques (appuyez sur la touche Windows + X et sélectionnez **Gestion des disques**).

    12. Vous remarquerez certains disques supplémentaires (en fonction du nombre de volumes que vous avez créé). Avec le bouton droit à **l’Initialiser le disque**et sélectionnez **OK**. Avec le bouton droit de la section **non alloué** , sélectionnez **Nouveau Volume Simple**, lui attribuer une lettre de lecteur et terminer l’Assistant.

    13. Répétez l’étape l pour tous les disques. Vous pouvez maintenant voir tous les disques sur **Ce PC** dans l’Explorateur Windows.

    14. Le rôle Services de fichiers et stockage permet de créer des partages de fichiers sur ces volumes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Créer et préparer un coffre-fort de récupération de Site Azure

Reportez-vous à la [documentation de récupération de Site Azure](../site-recovery/site-recovery-hyper-v-site-to-azure.md) mise en route avec la récupération de Site Azure avant de protéger le serveur de fichiers VM.

#### <a name="to-enable-protection"></a>Pour activer la protection

1.  Déconnecter l’ou les cibles iSCSI à partir d’ordinateurs virtuels sur les sites que vous souhaitez protéger par la récupération de Site Azure :

    1.  Appuyez sur Windows + Q et recherche pour **iSCSI**.

    2.  Sélectionnez le **jeu de configuration de l’initiateur iSCSI**.

    3.  Déconnectez le périphérique de StorSimple que vous avez connecté auparavant. Également, vous pouvez le désactiver le serveur de fichiers pendant quelques minutes lors de l’activation de la protection.

    > [AZURE.NOTE] Cela entraînera les partages de fichiers temporairement indisponible

1.  [Activer la protection de la machine virtuelle](../site-recovery/site-recovery-hyper-v-site-to-azure.md##step-6-enable-replication) de la machine virtuelle du serveur de fichiers à partir du portail de la récupération de Site Azure.

2.  Lors de la synchronisation initiale commence, vous pouvez reconnecter la cible à nouveau. Accédez à l’initiateur iSCSI, sélectionnez le périphérique StorSimple et cliquez sur **se connecter**.

3.  Lorsque la synchronisation est terminée et que l’état de la machine virtuelle est **protégé**, sélectionnez l’ordinateur virtuel, cliquez sur l’onglet **configurer** et mettre à jour le réseau de la machine virtuelle en conséquence (c’est le réseau qui l’a échoué sur VM(s) fera partie de). Si le réseau ne s’affiche pas, cela signifie que la synchronisation continue.

### <a name="enable-protection-of-storsimple-volumes"></a>Activer la protection des volumes de StorSimple

Si vous n’avez pas sélectionné l’option **activer une sauvegarde par défaut pour ce volume** pour les volumes StorSimple, accédez à **Stratégies de sauvegarde** dans le service Gestionnaire de StorSimple et de créer une stratégie de sauvegarde appropriée pour tous les volumes. Nous vous conseillons de définir la fréquence des sauvegardes à l’objectif de point de récupération (RPO) que vous souhaitez voir pour l’application.

### <a name="configure-the-network"></a>Configurer le réseau

Pour le serveur de fichiers VM, configurer les paramètres réseau dans Azure récupération de Site afin que les réseaux de la machine virtuelle sont attachés au réseau DR approprié après le basculement.

Vous pouvez sélectionner la machine virtuelle dans le **Nuage VMM** ou le **Groupe de Protection** pour configurer les paramètres réseau, comme indiqué dans l’illustration suivante.

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>Créer un plan de récupération

Vous pouvez créer un plan de récupération dans l’ASR pour automatiser le processus de basculement de partages de fichiers. Si une interruption se produit, vous pouvez mettre les partages de fichiers en quelques minutes avec un seul clic. Pour activer cette automatisation, vous devez un compte Azure automation.

#### <a name="to-create-the-account"></a>Pour créer le compte

1.  Accédez au portail Azure classique et passez à la section **automatisation** .

1.  Créer un nouveau compte d’automation. Conserver dans la même zone géographique/région dans lequel l’Appliance de nuage de StorSimple et les comptes de stockage ont été créés.

2.  Cliquez sur **Nouveau** &gt; **Application Services** &gt; **Automation** &gt; **procédure opérationnelle** &gt; **De la galerie** pour importer toutes les procédures opérationnelles requises dans le compte de l’automation.

    ![](./media/storsimple-dr-using-asr/image3.png)

1.  Dans le volet de **Récupération d’urgence** dans la galerie, ajoutez les procédures opérationnelles suivantes :

    -   Basculer vers les conteneurs de volume StorSimple

    -   Nettoyage des StorSimple volumes après le basculement sur incident Test (TFO)

    -   Montage de volumes sur le périphérique StorSimple après basculement

    -   Démarrer l’Appliance virtuelle de StorSimple

    -   Désinstaller l’extension de script personnalisé dans Azure VM

        ![](./media/storsimple-dr-using-asr/image4.png)


1.  Publier tous les scripts en sélectionnant la procédure opérationnelle dans le compte de l’automation et en accédant à l’onglet de **l’auteur** . Après cette étape, l’onglet **procédures opérationnelles** apparaîtra comme suit :

     ![](./media/storsimple-dr-using-asr/image5.png)

1.  Dans le compte de l’automatisation, cliquez sur l’onglet **ressources** , cliquez sur **Ajouter un paramètre** &gt; **Ajouter les informations d’identification**et ajouter vos informations d’identification Azure – nom de la ressource AzureCredential.

    Utilisez les informations d’identification de Windows PowerShell. Il s’agit d’une information d’identification qui contient un nom d’utilisateur ID d’organisation et d’un mot de passe avec accès à cet abonnement Azure et avec une authentification multifacteur désactivé. Ceci est nécessaire pour authentifier l’utilisateur pendant les basculements et pour afficher les volumes du serveur de fichiers sur le site de reprise après sinistre.

1.  Dans le compte de l’automatisation, cliquez sur l’onglet **ressources** et puis cliquez sur **Ajouter un paramètre** &gt; **Ajouter** et ajoutez les variables suivantes. Vous pouvez choisir de crypter ces actifs. Ces variables sont spécifiques au plan de récupération. Si vous prévoyez de votre restauration (que vous allez créer à l’étape suivante) nom est le plan de test, puis vos variables doivent être plan de test-StorSimRegKey, AzureSubscriptionName-plan de test et ainsi de suite.

    -   *RecoveryPlanName* **-StorSimRegKey**: la clé d’enregistrement pour le service Gestionnaire de StorSimple.

    -   *RecoveryPlanName* **-AzureSubscriptionName**: le nom de l’abonnement Azure.

    -   *RecoveryPlanName* **-ResourceName**: le nom de la ressource StorSimple dont le périphérique StorSimple.

    -   *RecoveryPlanName* **-DeviceName**: le périphérique qui doit être basculé.

    -   *RecoveryPlanName* **-TargetDeviceName**: l’Appliance de nuage StorSimple sur lesquels les conteneurs doivent être basculé.

    -   *RecoveryPlanName* **-VolumeContainers**: une chaîne séparée par des virgules des conteneurs de volumes présents sur le périphérique qui doivent être basculés ; par exemple, volcon1, volcon2, volcon3.

    -   RecoveryPlanName**-TargetDeviceDnsName**: le nom du service de l’équipement cible (Ceci se trouve dans la section de la **Machine virtuelle** : le nom du service est le même que le nom DNS).

    -   *RecoveryPlanName* **-StorageAccountName**: le nom du compte de stockage dans lequel est stocké le script (qui doit s’exécuter sur l’échec de sur une machine virtuelle). Cela peut être n’importe quel compte de stockage disposant d’espace pour stocker le script temporairement.

    -   *RecoveryPlanName* **-StorageAccountKey**: la clé d’accès pour le compte de stockage ci-dessus.

    -   *RecoveryPlanName* **-ScriptContainer**: le nom du conteneur dans lequel le script est stocké dans le nuage. Si le conteneur n’existe pas, il sera créé.

    -   *RecoveryPlanName* **-VMGUIDS**: lors de la protection d’un ordinateur virtuel, récupération de Site Azure affecte chaque machine virtuelle un ID unique qui donne les détails de l’échec de la machine virtuelle. Pour obtenir le VMGUID, cliquez sur l’onglet **Services de récupération** et puis cliquez sur **L’élément protégé** &gt; **Groupes de Protection** &gt; **Machines** &gt; **Propriétés**. Si vous avez plusieurs ordinateurs virtuels, vous devez ensuite ajouter les GUID sous forme de chaîne séparée par des virgules.

    -   *RecoveryPlanName* **-AutomationAccountName** – le nom du compte dans lequel vous avez ajouté les procédures opérationnelles et les actifs d’automation.

    Par exemple, si le nom de la planification de récupération est fileServerpredayRP, puis l’onglet **immobilisations** doit apparaître comme suit après avoir ajouté tous les actifs.

    ![](./media/storsimple-dr-using-asr/image6.png)


1.  Reportez-vous à la section **Services de récupération** et sélectionnez le coffre-fort de la récupération de Site Azure que vous avez créé précédemment.

2.  Sélectionnez l’onglet **Plans de reprise** et de créer un nouveau plan de récupération comme suit :

    une barre d’outils.  Spécifiez un nom et sélectionnez le **Groupe de Protection**.

    b.  Sélectionnez les ordinateurs virtuels du groupe de protection que vous souhaitez inclure dans le plan de récupération.

    c.  Après la restauration le plan est créé, sélectionnez-le pour ouvrir le mode de personnalisation de plan de récupération.

    d.  Sélectionnez **Arrêter le système tous les groupes**, cliquez sur **Script**et cliquez sur **Ajouter un script côté primaire avant l’arrêt du groupe tous les**.

    e.  Sélectionnez le compte d’automation (dans laquelle vous avez ajouté les procédures opérationnelles), puis sélectionnez la procédure opérationnelle de **conteneurs over-StorSimple-Volume d’échouer** .

    f.  Cliquez sur **groupe 1 : Démarrer**, de choisir des **Machines virtuelles**, et ajouter les ordinateurs virtuels qui doivent être protégées dans le plan de récupération.

    g.  Cliquez sur **groupe 1 : Démarrer**, sélectionnez **le Script**et d’ajouter tous les scripts suivants dans l’ordre les étapes **après le groupe 1** .

    - Procédure opérationnelle de début-StorSimple--Appliance virtuelle
    - Échec de procédure opérationnelle de conteneurs over-StorSimple-volume
    - Procédure opérationnelle de montage des volumes après basculement
    - Procédure de désinstallation personnalisée-script-extension opérationnelle

1.  Ajouter une action manuelle après les 4 scripts ci-dessus dans la même **groupe 1 : POST-étapes** section. Cette action est le point auquel vous pouvez vérifier que tout fonctionne correctement. Cette action doit être ajoutée que comme une partie du test de basculement (donc uniquement sélectionner le **Test de basculement** checkbox).

2.  Après l’action manuelle, ajoutez le script de nettoyage à l’aide de la même procédure que pour les autres procédures opérationnelles. Enregistrez le plan de récupération.

    > [AZURE.NOTE] Lorsque vous exécutez un test de basculement, vous devez vérifier tout à l’étape d’action manuelle car les volumes StorSimple qui avaient été clonés sur le périphérique cible seront supprimés dans le cadre du nettoyage de la fin de l’action manuelle.

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>Effectuez un test de basculement

Lors du basculement de test, consultez le guide compagnon de [Solution de reprise après sinistre de Active Directory](../site-recovery/site-recovery-active-directory.md) pour des raisons spécifiques à Active Directory. Le programme d’installation sur site n’est pas perturbé du tout lorsque le test de basculement se produit. Les volumes StorSimple qui étaient associées à la machine virtuelle sur site sont clonées à l’Appliance de nuage StorSimple sur Azure. Un ordinateur virtuel à des fins de test s’affiche dans Azure et les volumes clonés sont attachés à la machine virtuelle.

#### <a name="to-perform-the-test-failover"></a>Pour effectuer le basculement de test

1.  Dans le portail Azure classique, sélectionnez votre coffre-fort de récupération de site.

1.  Cliquez sur le plan de récupération créé pour la machine virtuelle du serveur de fichiers.

2.  Cliquez sur **Test basculement**.

3.  Sélectionnez le réseau virtuel pour démarrer le processus de basculement test.

    ![](./media/storsimple-dr-using-asr/image8.png)

1.  Lorsque l’environnement secondaire est opérationnel, vous pouvez effectuer vos validations.

2.  Lorsque les validations sont terminées, cliquez sur **Validation complète**. L’environnement de test de basculement est supprimé, et l’opération de TFO sera terminée.

## <a name="perform-an-unplanned-failover"></a>Effectuer un basculement non planifié

Au cours d’un basculement non planifié, les volumes StorSimple sont basculées sur le périphérique virtuel, un réplica VM est porté sur Azure et les volumes associés à la machine virtuelle.

#### <a name="to-perform-an-unplanned-failover"></a>Pour effectuer un basculement non planifié

1.  Dans le portail Azure classique, sélectionnez votre coffre-fort de récupération de site.

1.  Cliquez sur le plan de récupération créé pour le serveur de fichiers VM.

2.  Cliquez sur **Basculer** , puis sélectionnez **Basculement non planifié**.

    ![](./media/storsimple-dr-using-asr/image9.png)

1.  Sélectionnez le réseau cible, puis sur l’icône de vérification ✓ pour démarrer le processus de basculement.

## <a name="perform-a-planned-failover"></a>Effectuer un basculement planifié

Au cours d’un basculement planifié, les locaux sur serveur de fichiers que VM est arrêté normalement et un nuage sauvegarde instantané des volumes sur le dispositif de StorSimple. Les volumes StorSimple sont basculées sur le périphérique virtuel, un réplica VM est amenée sur Azure et les volumes associés à la machine virtuelle.

#### <a name="to-perform-a-planned-failover"></a>Pour effectuer un basculement planifié

1.  Dans le portail Azure classique, sélectionnez votre coffre-fort de récupération de site.

1.  Cliquez sur le plan de récupération créé pour la machine virtuelle du serveur de fichiers.

2.  Cliquez sur **Basculer** , puis sélectionnez **Planifié de basculement**.

3.  Sélectionnez le réseau cible, puis sur l’icône de vérification ✓ pour démarrer le processus de basculement.

## <a name="perform-a-failback"></a>Effectuer un retour arrière

Pendant un retour arrière, conteneurs de volume StorSimple sont basculées vers le périphérique physique après qu’une sauvegarde est effectuée.

#### <a name="to-perform-a-failback"></a>Pour effectuer un retour arrière

1.  Dans le portail Azure classique, sélectionnez votre coffre-fort de récupération de site.

1.  Cliquez sur le plan de récupération créé pour la machine virtuelle du serveur de fichiers.

2.  Cliquez sur **Basculer** et sélectionnez **basculement de planifié** ou un **basculement non planifié**.

3.  Cliquez sur **Modifier l’orientation**.

4.  Sélectionnez la synchronisation de données appropriées et les options de création de machines virtuelles.

5.  Cliquez sur l’icône de vérification ✓ pour démarrer le processus de restauration.

    ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>Meilleures pratiques

### <a name="capacity-planning-and-readiness-assessment"></a>Évaluation des capacités de planification et de préparation


#### <a name="hyper-v-site"></a>Site de Hyper-V

Pour concevoir une infrastructure réseau pour votre environnement de réplica de Hyper-V, de stockage et le serveur, utilisez l' [outil de planification de capacité de l’utilisateur](http://www.microsoft.com/download/details.aspx?id=39057) .

#### <a name="azure"></a>Azure

Vous pouvez exécuter l' [outil Readiness Assessment de la Machine virtuelle Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) sur des ordinateurs virtuels pour s’assurer qu’ils sont compatibles avec Azure VM et les Services de récupération de Site Azure. L’outil Readiness Assessment Tool vérifie la configuration de la machine virtuelle et vous avertit lorsque les configurations sont incompatibles avec Azure. Par exemple, il émet un avertissement si un lecteur C: est supérieur à 127 Go.


Planification de la capacité est constituée par au moins deux processus importants :

-   Mappage sur site ordinateurs virtuels Hyper-V pour Azure VM tailles (A6, A7, A8 et A9).

-   Déterminer les besoins en bande passante Internet.

## <a name="limitations"></a>Limitations

- Actuellement, 1 seul dispositif de StorSimple peut être basculé (à une seule Appliance de nuage StorSimple). Le scénario d’un serveur de fichiers qui couvre plusieurs périphériques StorSimple n’est pas encore pris en charge.

- Si vous obtenez une erreur lors de l’activation de la protection d’un ordinateur virtuel, assurez-vous que vous avez déconnecté les cibles iSCSI.

- Tous les conteneurs de volume qui ont été regroupés en raison des stratégies de sauvegarde couvrant entre les conteneurs de volume seront alors basculés entre eux.

- Tous les volumes dans les conteneurs de volume que vous avez choisi seront alors basculés.

- Volumes qui ajoutent à plus de 64 To ne peut pas être basculées car la capacité maximale d’une seule Appliance de nuage StorSimple est 64 To.

- Si le basculement planifiés échoue et que les ordinateurs virtuels sont créés dans Azure, puis ne pas nettoyer les ordinateurs virtuels. Au lieu de cela, faites un retour arrière. Si vous supprimez les ordinateurs virtuels puis ordinateurs virtuels locaux ne peuvent pas être réactivés.

- Après un basculement, si vous n’êtes pas en mesure de voir les volumes, accéder aux ordinateurs virtuels, ouvrez Gestion des disques, réanalyser les disques et de mettre en ligne.

- Dans certains cas, les lettres de lecteur dans le site de reprise après sinistre peuvent être différentes de celle des lettres sur site. Si cela se produit, vous devez corriger le problème manuellement une fois le basculement terminé.

- Plusieurs facteurs d’authentification doit être désactivé pour les informations d’identification Azure sont entrée dans le compte de l’automatisation en tant qu’actif. Si cette authentification n’est pas désactivée, les scripts pas pourra s’exécuter automatiquement, et le plan de récupération échouera.

- Délai d’attente de basculement sur incident : StorSimple le script expire si le basculement de conteneurs de volume prend plus de temps que la limite de récupération de Site Azure par script (actuellement 120 minutes).

- Délai d’expiration de l’opération de sauvegarde : StorSimple le script expire si la sauvegarde de volumes prend plus de temps que la limite de récupération de Site Azure par script (actuellement 120 minutes).
 
    > [AZURE.IMPORTANT] Exécution de la sauvegarde manuellement à partir du portail Azure et puis exécutez à nouveau le plan de récupération.

- Cloner le délai d’attente de travail : script de StorSimple de l’expiration du délai si le clonage des volumes prend plus de temps que la limite de récupération de Site Azure par script (actuellement 120 minutes).

- Erreur de synchronisation du temps : la StorSimple scripts erreurs en indiquant que les sauvegardes ont échoué, même si la sauvegarde a réussi dans le portail. Une cause possible de ce peut-être que le temps de l’appliance StorSimple peut être synchronisée avec l’heure actuelle dans le fuseau horaire.
 
    > [AZURE.IMPORTANT] Synchroniser l’heure de l’appliance à l’heure actuelle dans le fuseau horaire.

- Erreur de basculement matériel : StorSimple le script peut échouer s’il existe un basculement de la solution matérielle-logicielle lorsque le plan de récupération est en cours d’exécution.
    
    > [AZURE.IMPORTANT] Exécutez à nouveau le plan de récupération après que le basculement de la solution matérielle-logicielle est terminé.

## <a name="summary"></a>Résumé

À l’aide de la récupération de Site d’Azure, vous pouvez créer un plan de récupération d’urgence automatisée complète pour un machine virtuelle du serveur de fichiers ayant des partages de fichiers hébergés sur le stockage de StorSimple. Vous pouvez lancer le basculement en secondes depuis n’importe où dans le cas d’une interruption de service et les obtenir l’application opérationnel en quelques minutes.
