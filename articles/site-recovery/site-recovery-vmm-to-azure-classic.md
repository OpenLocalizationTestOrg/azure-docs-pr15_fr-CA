<properties
    pageTitle="Répliquer les machines virtuelles de Hyper-V dans les nuages VMM à Azure | Microsoft Azure"
    description="Cet article décrit comment répliquer les machines virtuelles de Hyper-V sur les hôtes Hyper-V que qui se trouve dans des nuages de System Center VMM sur Azure."
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
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Répliquer les machines virtuelles de Hyper-V dans les nuages VMM vers Azure

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-vmm-to-azure.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portail classique](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - classique](site-recovery-deploy-with-powershell.md)



Le service de récupération de Site Azure contribue à votre stratégie de récupération (BCDR) de continuité d’activité et de reprise après sinistre de l’entreprise par l’orchestration de réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire sur site. Pour une vue d’ensemble rapide de lecture [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md).

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment déployer la récupération de Site pour répliquer des ordinateurs virtuels de Hyper-V sur les serveurs hôtes Hyper-V qui sont trouvent dans les nuages privés de VMM sur Azure.

L’article inclut les conditions préalables pour le scénario et vous montre comment configurer un coffre-fort de restauration de Site, obtenir le fournisseur de récupération de Site Azure installé sur le serveur VMM de source, inscrire le serveur dans le coffre-fort, ajoutez un compte de stockage Azure, installer l’agent de Services de récupération Azure sur les serveurs hôtes Hyper-V, configurer les paramètres de protection pour les nuages VMM qui seront appliquées à tous les ordinateurs virtuels protégés , puis activez la protection des machines virtuelles. Terminez en tester le basculement sur incident pour vous assurer que tout fonctionne comme prévu.

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Architecture

![Architecture](./media/site-recovery-vmm-to-azure-classic/topology.png)

- Le fournisseur de récupération de Site Azure est installé sur le moniteur pendant le déploiement de la récupération de Site et le serveur VMM est enregistré dans le coffre-fort de la récupération de Site. Le fournisseur communique avec récupération de Site pour gérer l’orchestration de la réplication.
- L’agent des Services de récupération Azure est installé sur les serveurs hôtes Hyper-V au cours du déploiement de la récupération de Site. Il gère la réplication des données de stockage Azure.


## <a name="azure-prerequisites"></a>Conditions préalables Azure

Voici ce que vous aurez besoin dans Azure.

**Condition préalable** | **Détails**
--- | ---
**Compte Azure**| Vous devez disposer d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification de la récupération de Site.
**Stockage Azure** | Vous devez posséder un compte de stockage Azure pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et d’Azure VM sont lancé lors du basculement. <br/><br/>Vous avez besoin d’un [compte de stockage redondant geo standard](../storage/storage-redundancy.md#geo-redundant-storage). Le compte doit dans la même région que le service de récupération de Site et être associé à l’abonnement de même. Notez que la réplication vers les comptes de stockage premium n’est pas actuellement pris en charge et ne doit pas être utilisée.<br/><br/>[En savoir plus sur](../storage/storage-introduction.md) Stockage Azure.
**Réseau Azure** | Vous aurez besoin d’un réseau virtuel Azure qui se connectera à Azure VM lors du basculement. Le réseau virtuel Azure doit être dans la même région, comme le coffre-fort de la récupération de Site.

## <a name="on-premises-prerequisites"></a>Conditions préalables du local

Voici ce dont vous aurez besoin sur site.

**Condition préalable** | **Détails**
--- | ---
**VMM** | Vous aurez besoin d’au moins un serveur VMM déployé comme un serveur autonome physiques ou virtuels, ou comme un cluster virtuel. <br/><br/>Le serveur VMM doit être en cours d’exécution System Center 2012 R2 avec les dernières mises à jour cumulatives.<br/><br/>Vous devez au moins un nuage configuré sur le serveur VMM.<br/><br/>Le nuage source que vous souhaitez protéger doit contenir un ou plusieurs groupes d’hôtes VMM.<br/><br/>Pour en savoir plus sur la configuration des nuages VMM dans [procédure pas à pas : création des nuages privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) sur le blog de Keith Mayer.
**Hyper-V** | Vous aurez besoin d’un ou plusieurs serveurs d’hôte Hyper-V ou des clusters dans le nuage VMM. Le serveur hôte doit disposer et les ordinateurs virtuels d’un ou plusieurs. <br/><br/>Le serveur Hyper-V doit être en cours d’exécution au moins **Windows Server 2012 R2** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012 R2** et installé les dernières mises à jour.<br/><br/>N’importe quel serveur Hyper-V qui contient les ordinateurs virtuels que vous souhaitez protéger doit se trouver dans un nuage VMM.<br/><br/>Si vous utilisez Hyper-V dans une note de cluster que broker de cluster n’est pas créé automatiquement si vous disposez d’un cluster de base d’adresses IP statique. Vous devez configurer manuellement le service broker de cluster. [En savoir plus](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) dans une entrée de blog de Marie Finn.
**Ordinateurs protégés** | Ordinateurs virtuels que vous souhaitez protéger doivent satisfaire aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).


## <a name="network-mapping-prerequisites"></a>Conditions préalables du mappage réseau
Lorsque vous protégez des ordinateurs virtuels dans les mappages du mappage réseau Azure entre les réseaux de machine virtuelle sur le serveur VMM de source et ciblez les réseaux Azure pour activer les éléments suivants :

- Tous les ordinateurs où le basculement sur le même réseau peut se connecter entre eux, quel que soit le plan de récupération, ils sont en cours.
- Si une passerelle réseau est configuré sur la cible de réseau Azure, machines virtuelles peuvent se connecter à d’autres ordinateurs virtuels de locaux.
- Si vous ne configurez pas réseau mappage uniquement d’ordinateurs virtuels qui basculent dans le même plan de restauration pourront se connecter à l’autre après le basculement vers Azure.

Si vous souhaitez déployer le mappage réseau, vous devez les éléments suivants :

- Les ordinateurs virtuels que vous souhaitez protéger sur le serveur VMM de source doit être connectés à un réseau de la machine virtuelle. Ce réseau doit être lié à un réseau logique qui est associé avec le nuage.
- Un réseau Azure auquel les machines virtuelles dupliquées peuvent se connecter après le basculement. Vous devez sélectionner ce réseau au moment du basculement. Le réseau doit être dans la même région en tant que votre abonnement Azure récupération de Site.

Préparer pour le mappage de réseau comme suit :

1. [Découvrez les](site-recovery-network-mapping.md) exigences de mappage réseau.
2. Préparer des réseaux d’ordinateur virtuel dans VMM :

    - [Configurer des réseaux logiques](https://technet.microsoft.com/library/jj721568.aspx).
    - [Configurer des réseaux de la machine virtuelle](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>Étape 1 : Créer un coffre-fort de récupération de Site

1. Connectez-vous au [Portail de gestion](https://portal.azure.com) à partir du serveur VMM, que vous souhaitez enregistrer.
2. Cliquez sur **Services de données** > **Services de récupération de** > **coffre-fort de récupération de Site**.
3. Cliquez sur **créer une nouvelle** > **Création rapide**.
4. Dans la zone **nom**, entrez un nom convivial pour identifier le coffre-fort.
5. Dans la **zone**, sélectionnez la zone géographique pour la chambre forte. Pour vérifier les régions pris en charge consultez disponibilité géographique dans les [Détails de la tarification de récupération de Site de Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Cliquez sur **créer coffre-fort**.

    ![Nouveau coffre-fort](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Vérifiez la barre d’état pour confirmer que le coffre-fort a été créé avec succès. Le coffre-fort apparaît comme **actif** sur la page principale des Services de récupération.

## <a name="step-2-generate-a-vault-registration-key"></a>Étape 2 : Générer une clé d’enregistrement de coffre-fort

Générer une clé d’enregistrement dans la chambre forte. Après le téléchargement du fournisseur de récupération de Site Azure et l’installer sur le serveur VMM, vous utiliserez cette clé pour inscrire le serveur VMM dans la chambre forte.

1. Dans la page **Services de restauration** , cliquez sur le coffre pour ouvrir la page de démarrage rapide. Démarrage rapide peut également être ouvert à tout moment à l’aide de l’icône.

    ![Icône de démarrage rapide](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)

2. Dans la liste déroulante, sélectionnez **entre un site VMM sur site et Microsoft Azure**.
3. **Préparer les serveurs VMM**, cliquez sur fichier de **Générer une clé d’enregistrement** . Le fichier de clé est généré automatiquement et est valable 5 jours après sa création. Si vous accédez pas le portail Azure à partir du serveur VMM, vous devrez copier ce fichier sur le serveur.

    ![Clé d’enregistrement](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Étape 3 : Installer le fournisseur de récupération de Site Azure

1. Dans **Démarrage rapide** > **serveurs de préparer de VMM**, cliquez sur **Télécharger Microsoft Azure récupération fournisseur de Site pour une installation sur les serveurs VMM** pour obtenir la dernière version du fichier d’installation de fournisseur.
2. Exécutez ce fichier sur le serveur VMM de source.

    >[AZURE.NOTE] Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois l’installer sur un nœud actif et terminer l’installation pour inscrire le serveur VMM dans la chambre forte. Puis, installez le fournisseur sur les autres nœuds. Notez que si vous mettez à niveau le fournisseur vous devrez mettre à niveau sur tous les nœuds, car ils doivent tous exécuter la même version du fournisseur.

3. Le programme d’installation effectue une vérification de prerequirements et demande l’autorisation d’arrêter le service VMM pour commencer l’installation du fournisseur. Le Service VMM sera redémarré automatiquement lorsque le programme d’installation se termine. Si vous installez sur un cluster VMM que vous serez invité à arrêter le rôle Cluster.

4. Mise à jour de **Microsoft** , vous pouvez choisir dans les mises à jour. Avec ce paramètre activé fournisseur mises à jour seront installées en fonction de votre stratégie de Microsoft Update.

    ![Mises à jour Microsoft](./media/site-recovery-vmm-to-azure-classic/updates.png)


5.  L’emplacement d’installation pour le fournisseur a la valeur ** <SystemDrive>\Program Files\Microsoft Manager\bin de System Center 2012 R2\Virtual Machine**. Cliquez sur **installer**.

    ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)

6. Après avoir installé le fournisseur cliquez sur **Enregistrer** pour enregistrer le serveur dans la chambre forte.

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)

9. Dans la zone **nom de coffre-fort**, vérifiez le nom du coffre-fort dans lequel le serveur est inscrit. Cliquez sur *suivant*.

    ![Inscription du serveur](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)

7. **Connexion à Internet** de spécifier comment le fournisseur en cours d’exécution sur le serveur VMM se connecte à Internet. Sélectionnez **se connecter avec les paramètres de serveur proxy existants** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

    ![Paramètres Internet](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

    - Si vous souhaitez utiliser un proxy personnalisé, que vous devez le configurer avant d’installer le fournisseur. Lorsque vous configurez les paramètres de proxy personnalisés, un test s’exécute pour vérifier la connexion du proxy.
    - Si vous utilisez un proxy personnalisé ou votre proxy par défaut requiert une authentification, que vous devez saisir les détails de proxy, y compris l’adresse proxy et le port.
    - URL suivantes doit être accessible depuis le serveur VMM et sur les hôtes Hyper-v
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Autoriser les adresses IP décrits dans des [Plages d’IP Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653) et HTTPS protocole (443). Vous seriez obligé de plages IP de liste blanche de la région Azure que vous prévoyez d’utiliser et de l’ouest des États-Unis.
    - Si vous utilisez un proxy personnalisé qu'un compte RunAs de VMM (DRAProxyAccount) est créé automatiquement avec les informations d’identification du proxy spécifié. Configurer le serveur proxy afin que ce compte peut s’authentifier avec succès. Les paramètres de compte de VMM RunAs peuvent être modifiés dans la console VMM. Pour ce faire, ouvrez l’espace de **paramètres** , développez **sécurité**, cliquez sur **Exécuter en tant que comptes**et puis modifiez le mot de passe pour DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.


8. Dans la **Clé d’enregistrement**, sélectionnez la clé que vous avez téléchargé à partir de la récupération de Site Azure copié sur le serveur VMM.


10.  Le paramètre de cryptage est uniquement utilisé lors de la réplication de machines virtuelles de Hyper-V dans les nuages VMM à Azure. Si vous utilisez la réplication sur un site secondaire n’est pas utilisée.

11.  Dans **nom du serveur**, spécifiez un nom convivial pour identifier le serveur VMM dans la chambre forte. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.
12.  Dans **les métadonnées de nuage synchroniser** , sélectionnez si vous souhaitez synchroniser les métadonnées pour tous les nuages sur le serveur VMM à la chambre forte. Cette action ne doit se produire qu’une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les nuages, vous pouvez laisser ce paramètre désactivé et synchroniser chaque nuage individuellement dans les propriétés du nuage dans la console VMM.

13.  Cliquez sur **suivant** pour terminer le processus. Après l’inscription, les métadonnées du serveur VMM sont récupérée lors de la récupération de Site Azure. Le serveur s’affiche dans l’onglet **Serveurs VMM** sur la page **serveurs** de la chambre forte.

    ![LastPage](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

Après l’inscription, les métadonnées du serveur VMM sont récupérée lors de la récupération de Site Azure. Le serveur s’affiche dans l’onglet **Serveurs VMM** sur la page **serveurs** de la chambre forte.

### <a name="command-line-installation"></a>Installation de ligne de commande

Le fournisseur de récupération de Site Azure peut également être installé à l’aide de la ligne de commande. Cette méthode peut être utilisée pour installer le fournisseur sur un Server Core de Windows Server 2012 R2.

1. Téléchargez la clé de l’enregistrement de fichiers et d’installation de fournisseur dans un dossier. Par exemple : C:\ASR.
2. Arrêtez le service System Center Virtual Machine Manager
3. À partir d’une invite de commandes avec élévation de privilèges, extraire le programme d’installation de fournisseur avec les commandes :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installez le fournisseur comme suit :

        C:\ASR> setupdr.exe /i

5. Enregistrer le fournisseur comme suit :

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Où les paramètres sont les suivants :

 - **/Credentials** : un paramètre obligatoire qui spécifie l’emplacement dans lequel se trouve le fichier de clé d’enregistrement  
 - **/FriendlyName** : un paramètre obligatoire pour le nom du serveur hôte Hyper-V qui s’affiche dans le portail de récupération de Site Azure.
 - **/EncryptionEnabled** : paramètre facultatif pour spécifier si vous souhaitez le cryptage vos machines virtuelles dans Azure (au cryptage de repos). Le nom de fichier doit avoir une extension **.pfx** .
 - **/ProxyAddress** : paramètre optionnel qui spécifie l’adresse du serveur proxy.
 - **/ProxyPort** : paramètre optionnel qui spécifie le port du serveur proxy.
 - **/proxyUsername** : paramètre optionnel qui spécifie le nom d’utilisateur proxy.
 - **/proxyPassword** : paramètre optionnel qui spécifie le mot de passe du proxy.  


## <a name="step-4-create-an-azure-storage-account"></a>Étape 4 : Créer un compte de stockage Azure

1. Si vous n’avez pas un compte de stockage Azure, cliquez sur **Ajouter un compte de stockage Azure** pour créer un compte.
2. Créer un compte avec géo-réplication est activée. Il doit dans la même région, comme le service de récupération de Site Azure et être associé à l’abonnement de même.

    ![Compte de stockage](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [AZURE.NOTE] [Migration des comptes de stockage](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

## <a name="step-5-install-the-azure-recovery-services-agent"></a>Étape 5 : Installation de l’Agent de Services de récupération Azure

Installez l’agent des Services de récupération Azure sur chaque serveur d’hôte Hyper-V dans le nuage VMM.

1. Cliquez sur **Démarrage rapide** > **Agent des Services de récupération télécharger Azure Site et installer sur les hôtes** pour obtenir la dernière version du fichier d’installation de l’agent.

    ![Installer l’Agent de récupération de Services](./media/site-recovery-vmm-to-azure-classic/install-agent.png)

2. Exécutez le fichier d’installation sur chaque serveur d’hôte Hyper-V.
3. Sur la page **Prerequisites Check** , cliquez sur **suivant**. Les composants requis manquants seront automatiquement installés.

    ![Agent de Services de récupération de conditions préalables](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)

4. Dans la page **Paramètres de l’Installation** , indiquez où vous souhaitez installer l’agent, puis sélectionnez l’emplacement du cache dans lequel les métadonnées de sauvegarde va être installée. Puis cliquez sur **installer**.
5. Une fois l’installation terminée, cliquez sur **Fermer** pour terminer l’Assistant.

    ![Inscrire l’Agent de MARS](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Installation de ligne de commande

Vous pouvez également installer l’Agent de Services de récupération de Microsoft Azure à partir de la ligne de commande à l’aide de cette commande :

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>Étape 6 : Configuration cloud paramètres de protection

Après avoir enregistré le serveur VMM, vous pouvez configurer les paramètres de protection de nuage. Vous avez activé l’option **synchroniser les données de nuage avec le coffre-fort** lorsque vous avez installé le fournisseur pour tous les nuages sur le serveur VMM seront affichent dans l’onglet <b>Éléments de protection</b> dans la chambre forte.

![Nuage publié](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. Sur la page de démarrage rapide, cliquez sur **configurer la protection pour les nuages VMM**.
2. Dans l’onglet **Protection** , cliquez sur le nuage que vous souhaitez configurer et cliquez sur l’onglet **Configuration** .
3. Dans la **cible** , sélectionnez **Azure**.
4. Dans le **Compte de stockage** , sélectionnez le compte de stockage Azure que vous utilisez pour la réplication.
5. Pour **désactiver**la valeur **crypter les données stockées** . Ce paramètre spécifie que les données doivent être chiffrées répliquées entre le site local et Azure.
6. Dans **fréquence de copie** , laissez le paramètre par défaut. Cette valeur spécifie la fréquence à laquelle les données doivent être synchronisées entre les emplacements source et cible.
7. **Conserver les points de récupération**, laissez le paramètre par défaut. Avec une valeur par défaut de zéro, seul le dernier point de récupération pour un ordinateur virtuel principal est stocké sur un serveur hôte de réplica.
8. La **fréquence des instantanés cohérents avec les applications**, laissez le paramètre par défaut. Cette valeur spécifie la fréquence de création des instantanés. Instantanés utilisent le Volume Shadow Copy Service (VSS) pour s’assurer que les applications sont dans un état cohérent lorsque l’instantané est pris.  Si vous ne définissez pas une valeur, assurez-vous qu’il est inférieur au nombre de points de récupération supplémentaires que vous configurez.
9. Dans la zone **heure de début de la réplication**, spécifiez lors de la réplication initiale des données sur Azure doit démarrer. Le fuseau horaire sur le serveur hôte Hyper-V est utilisé. Nous vous conseillons de prévoir la réplication initiale pendant les heures creuses.

    ![Paramètres de réplication de nuage](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

Après avoir enregistré les paramètres d’une tâche est créée et peut être surveillée dans l’onglet **tâches** . Tous les serveurs hôtes Hyper-V nuage VMM source seront configurés pour la réplication.

Après l’enregistrement, les paramètres de nuage peuvent être modifiés dans l’onglet **configurer** . Pour modifier l’emplacement de la cible ou d’un compte de stockage cible, vous devez supprimer la configuration cloud, et puis reconfigurer le nuage. Notez que si vous modifiez le compte de stockage la modification est appliquée uniquement pour les ordinateurs virtuels qui sont activés pour la protection après que le compte de stockage a été modifié. Ordinateurs virtuels existants ne sont pas migrés vers le nouveau compte de stockage.

## <a name="step-7-configure-network-mapping"></a>Étape 7 : Configuration de mappage réseau
Avant de commencer la mappage réseau Vérifiez que les ordinateurs virtuels sur le serveur VMM de source sont connectés à un réseau de la machine virtuelle. En outre, créer un ou plusieurs réseaux virtuels Azure. Notez que plusieurs réseaux de l’ordinateur virtuel peuvent être mappés à un seul réseau Azure.

1. Sur la page de démarrage rapide, cliquez sur **mapper les réseaux**.
2. Sous l’onglet **réseaux** , dans l' **emplacement Source**, sélectionnez le serveur VMM de source. Dans **l’emplacement cible** , sélectionnez Azure.
3. Dans les réseaux de la **Source** , une liste des réseaux d’ordinateur virtuel associé au serveur VMM s’affichent. Dans les réseaux de **cible** les réseaux Azure associés à l’abonnement sont affichés.
4. Sélectionnez le réseau de la machine virtuelle source et cliquez sur la **carte**.
5. Dans la page **Sélectionner un réseau de la cible** , sélectionnez la cible Azure réseau que vous souhaitez utiliser.
6. Cliquez sur la case à cocher pour terminer le processus de mappage.

    ![Paramètres de réplication de nuage](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

Après avoir enregistré les paramètres un travail commence suivre la progression de la mise en correspondance et il peut être analysé dans l’onglet tâches. N’importe quel réplica des machines virtuelles existantes qui correspondent au réseau de la machine virtuelle source sera connectés à la cible Azure réseaux. De nouvelles machines virtuelles qui sont connectés au réseau de la machine virtuelle source va être connectés au réseau Azure mappé après la réplication. Si vous modifiez un mappage avec un réseau existant, les machines virtuelles de réplica sera connectés avec les nouveaux paramètres.

Notez que si le réseau cible a plusieurs sous-réseaux et de ces sous-réseaux a le même nom que le sous-réseau sur lequel se trouve l’ordinateur virtuel source, puis l’ordinateur virtuel de réplica sera connecté au sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, l’ordinateur virtuel va être connecté pour le premier sous-réseau dans le réseau.

> [AZURE.NOTE] [Migration de réseaux](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les réseaux utilisés pour le déploiement de récupération de Site.

## <a name="step-8-enable-protection-for-virtual-machines"></a>Étape 8 : Activer la protection des machines virtuelles

Une fois que les serveurs, les réseaux et les nuages sont correctement configurés, vous pouvez activer la protection pour les ordinateurs virtuels dans le nuage. Notez les points suivants :

- Machines virtuelles doit répondre aux [exigences d’Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
- Pour activer la protection du système d’exploitation et le système d’exploitation, les propriétés de disque doivent être définies pour l’ordinateur virtuel. Lorsque vous créez un ordinateur virtuel dans VMM à l’aide d’un modèle d’ordinateur virtuel, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes sous les onglets **Général** et la **Configuration matérielle** des machine virtuelle de propriétés. Si vous ne définissez ces propriétés dans VMM, vous serez en mesure de les configurer dans le portail de récupération de Site Azure.

    ![Créer des machines virtuelles](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![Modifier les propriétés de la machine virtuelle](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)


1. Pour activer la protection, sur l’onglet **ordinateurs virtuels** dans le nuage dans lequel se trouve l’ordinateur virtuel, cliquez sur **Activer la protection** > **Ajouter des ordinateurs virtuels**.
2. Dans la liste des ordinateurs virtuels dans le nuage, sélectionnez celui que vous souhaitez protéger.

    ![Activer la protection de l’ordinateur virtuel](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    Suivre la progression de l’action **d’Activation de la Protection** dans l’onglet **tâches** , y compris la réplication initiale. Après l’exécution de la tâche de **Protection de finaliser** l’ordinateur virtuel est prêt pour le basculement. Une fois la protection est activée et les machines virtuelles sont répliqués, vous serez en mesure de les afficher dans Azure.


    ![Tâche de protection d’ordinateur virtuel](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

3. Vérifiez les propriétés de la machine virtuelle et modifiez-le si nécessaire.

    ![Vérifiez les machines virtuelles](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)


4. Dans l’onglet **configurer** les propriétés de la machine virtuelle des propriétés réseau suivantes peuvent être modifiées.





- **Nombre de cartes réseau sur l’ordinateur virtuel cible** - le nombre de cartes réseau dépend de la taille que vous spécifiez pour l’ordinateur virtuel cible. Vérifiez les [spécifications de taille d’ordinateur virtuel](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) pour le nombre de cartes prises en charge par la taille de la machine virtuelle. Lorsque vous modifiez la taille d’une machine virtuelle et enregistrez les paramètres, le nombre de carte réseau changera lors de la prochaine ouverture de la page de **configuration** . Le nombre de cartes réseau des ordinateurs virtuels de cible est le nombre de cartes réseau sur l’ordinateur virtuel source et le nombre maximal de cartes réseau pris en charge par la taille de la machine virtuelle sélectionnée, comme suit :

    - Si le nombre de cartes réseau sur l’ordinateur source est inférieur ou égal au nombre de cartes autorisé pour la taille de l’ordinateur cible, la cible ont le même nombre de cartes comme source.
    - Si le nombre de cartes sur l’ordinateur virtuel source dépasse le nombre autorisé de la taille cible puis le maximum de la taille cible sera utilisé.
    - Par exemple, si une machine source possède deux cartes réseau et la taille de la machine cible prend en charge quatre, l’ordinateur cible aura deux cartes. Si l’ordinateur source dispose de deux cartes, mais la taille cible prise en charge prend uniquement en charge un l’ordinateur cible aura qu’une seule carte.    

- **Réseau de l’ordinateur virtuel cible** - le réseau auquel l’ordinateur virtuel se connecte est déterminé par la correspondance de réseau du réseau de l’ordinateur virtuel source. Si l’ordinateur virtuel source a plusieurs cartes réseau et les réseaux de source sont mappés sur les différents réseaux sur la cible, vous devez choisir entre un des réseaux cible.
- **Sous-réseau de chaque carte réseau** - réseau pour chaque carte, vous pouvez sélectionner le sous-réseau auquel l’échec sur l’ordinateur virtuel doit se connecter à.
- **Adresse IP de la cible** - si la carte réseau de l’ordinateur virtuel source est configurée pour utiliser une adresse IP statique, vous pouvez fournir l’adresse IP pour l’ordinateur virtuel cible. Utilisez cette fonctionnalité conserve l’adresse IP d’un ordinateur virtuel de source après un basculement. Si aucune adresse IP n’est pas fourni n’importe quelle adresse IP disponible est attribué à la carte réseau au moment du basculement. Si l’adresse IP cible est spécifié mais qu’il est déjà utilisé par une autre machine virtuelle en cours d’exécution dans Azure basculement échouera.  

    ![Modifier les propriétés de réseau](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

>[AZURE.NOTE] Machines virtuelles de Linux avec adresse IP statique ne sont pas pris en charge.

## <a name="test-the-deployment"></a>Tester le déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération qui se compose de plusieurs machines virtuelles et exécuter un test de basculement pour le plan.  

Basculement de test simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez que :

- Si vous souhaitez vous connecter à l’ordinateur virtuel dans Azure à l’aide du Bureau à distance après le basculement, activer la connexion Bureau à distance sur l’ordinateur virtuel avant d’exécuter le test de basculement.
- Après le basculement, vous utiliserez une adresse IP publique pour se connecter à l’ordinateur virtuel dans Azure à l’aide du Bureau à distance. Si vous souhaitez le faire, assurez-vous que vous n’avez pas les stratégies de domaine qui vous empêchent de vous connecter à un ordinateur virtuel à l’aide d’une adresse publique.

>[AZURE.NOTE] Pour obtenir les meilleures performances lorsque vous effectuez un basculement vers Azure, assurez-vous que vous avez installé l’Agent d’Azure dans l’ordinateur protégé. Cela permet de démarrer plus rapidement et que vous permet également de diagnostic en cas de problèmes. Agent de Linux peut être trouvés [ici](https://github.com/Azure/WALinuxAgent) - et Windows l’agent se trouve [ici](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Sous l’onglet **Plans de reprise** , ajoutez un nouveau plan. Spécifiez un nom, **VMM** dans le **type de Source**, et le serveur VMM de source dans la **Source**, la cible sera Azure.

    ![Créer le plan de récupération](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)

2. Dans la page **Sélectionner les ordinateurs virtuels** , sélectionnez machines virtuelles à ajouter au plan de récupération. Ces machines virtuelles sont ajoutés au groupe par défaut de plan de récupération : groupe 1. Un maximum de 100 ordinateurs virtuels dans un plan de récupération unique ont été testés.

- Si vous souhaitez vérifier les propriétés de l’ordinateur virtuel avant de les ajouter à la planification, cliquez sur l’ordinateur virtuel dans la page de propriétés du nuage dans lequel il se trouve. Vous pouvez également configurer les propriétés de la machine virtuelle dans la console VMM.
- Tous les ordinateurs virtuels qui sont affichés ont été activées pour la protection. La liste inclut les deux ordinateurs virtuels qui sont activés pour la protection et la réplication initiale est terminée, et ceux qui sont activés pour la protection avec initiales de réplication en attente. Uniquement des ordinateurs virtuels avec la réplication initiale terminée peut basculer dans le cadre d’un plan de récupération.

    ![Créer le plan de récupération](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

Un plan de récupération a été créé, il apparaît dans l’onglet **Plans de reprise** . Vous pouvez également ajouter des [procédures opérationnelles d’Azure automation](site-recovery-runbook-automation.md) sur le plan de récupération pour automatiser des actions lors du basculement.

### <a name="run-a-test-failover"></a>Exécutez un test de basculement

Il existe deux manières d’exécuter un test de basculement vers Azure.

- **Tester le basculement sur incident sans un réseau Azure**— ce type de basculement de test vérifie que l’ordinateur virtuel s’affiche correctement dans Azure. La machine virtuelle n’est pas connectée à un réseau Azure après le basculement.
- **Tester le basculement avec un réseau Azure**— ce type de contrôles de basculement que l’environnement de réplication complet est fourni comme prévu et qui ont échoué sur les ordinateurs virtuels sera connecté à la cible spécifiée réseau Azure. Pour la gestion de sous-réseau, pour test de basculement le sous-réseau de l’ordinateur virtuel de test va être chiffré selon le sous-réseau de l’ordinateur virtuel de réplica. Ceci est différent de réplication régulières lorsque le sous-réseau d’un ordinateur virtuel de réplica est basé sur le sous-réseau de l’ordinateur virtuel source.

Si vous souhaitez exécuter un test de basculement pour une machine virtuelle activé pour une protection sur Azure sans spécifier un réseau cible Azure que vous n’avez pas besoin de préparer de quoi que ce soit. Pour exécuter un test de basculement avec une cible de réseau Azure, vous devez créer un nouveau réseau Azure qui a isolé de votre réseau de production Azure (comportement par défaut lorsque vous créez un nouveau réseau dans Azure). Observez comment [exécuter un test de basculement](site-recovery-failover.md#run-a-test-failover) pour plus de détails.


Vous devez également configurer l’infrastructure de la machine virtuelle répliquée à fonctionner comme prévu. Par exemple, un ordinateur virtuel avec le contrôleur de domaine et DNS peuvent être répliqué vers Azure à l’aide de la récupération de Site Azure et peuvent être créé dans le réseau de test à l’aide de tester le basculement. Examinez [les considérations de basculement pour active directory de test](site-recovery-active-directory.md#considerations-for-test-failover) la section pour plus de détails.

Pour exécuter un test de basculement, procédez à ce qui suit :

1. Sous l’onglet **Plans de reprise** , sélectionnez le plan, cliquez sur **Test de basculement**.
2. Dans la page **Confirmer le basculement Test** sélectionnez **Aucun** ou un réseau d’Azure spécifique.  Notez que si vous sélectionnez aucun le basculement sur incident test vérifiera que la machine virtuelle répliqués correctement vers Azure mais ne vérifie pas votre configuration de réseau de réplication.

    ![Aucun réseau](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)

3. Si le cryptage des données est activé pour le cloud, dans la **Clé de cryptage** , sélectionnez le certificat a été émis lors de l’installation du fournisseur sur le serveur VMM, lorsque vous avez activé l’option Activer le cryptage des données pour un nuage.
4. Dans l’onglet **tâches** , vous pouvez suivre des cours de basculement. Vous devez également être en mesure de voir le réplica du test de machine virtuelle dans le portail Azure. Si vous avez défini pour les ordinateurs virtuels de l’accès à votre réseau local, vous pouvez lancer une connexion Bureau à distance sur l’ordinateur virtuel.
5. Lorsque le basculement atteint la phase **de test terminée** , cliquez sur **Test complet** pour terminer le test de basculement. Vous pouvez accéder à l’onglet **tâche** pour suivre l’état et la progression de la reprise et effectuer toutes les actions qui sont nécessaires.
6. Après le basculement, vous serez en mesure de voir le réplica du test de machine virtuelle dans le portail Azure. Si vous avez défini pour les ordinateurs virtuels de l’accès à votre réseau local, vous pouvez lancer une connexion Bureau à distance sur l’ordinateur virtuel. Effectuez le des opérations suivantes :

    1. Vérifiez que les ordinateurs virtuels démarrent correctement.
    2. Si vous souhaitez vous connecter à l’ordinateur virtuel dans Azure à l’aide du Bureau à distance après le basculement, activer la connexion Bureau à distance sur l’ordinateur virtuel avant d’exécuter le test de basculement. Vous devrez également ajouter un point de terminaison RDP sur l’ordinateur virtuel. Vous pouvez exploiter une [Procédures opérationnelles de Automation Azure](site-recovery-runbook-automation.md) pour cela.
    3. Après basculement sur incident si vous utilisez une adresse IP publique pour se connecter à l’ordinateur virtuel dans Azure à l’aide du Bureau à distance, assurez-vous que vous n’avez pas les stratégies de domaine qui vous empêchent de vous connecter à un ordinateur virtuel à l’aide d’une adresse publique.

7.  Une fois le test terminé, effectuez les opérations suivantes :
    - Cliquez sur **le test de basculement est achevé**. Nettoyer l’environnement de test pour automatiquement mettre hors tension et supprimez les ordinateurs virtuels de test.
    - Cliquez sur **Notes** pour enregistrer et enregistrer les observations associées avec le basculement de test.

>

## <a name="next-steps"></a>Étapes suivantes

Obtenir des informations sur le [paramétrage des plans de récupération](site-recovery-create-recovery-plans.md) et de [basculement](site-recovery-failover.md).
