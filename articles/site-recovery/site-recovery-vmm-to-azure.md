<properties
    pageTitle="Répliquer les machines virtuelles de Hyper-V dans les nuages VMM à Azure à l’aide de la récupération de Site avec le portail Azure | Microsoft Azure"
    description="Décrit comment déployer la récupération de Site Azure pour orchestrer la réplication, le basculement et la récupération de machines virtuelles de Hyper-V dans les nuages VMM vers Azure via le portail Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-azure-site-recovery-with-the-azure-portal--microsoft-azure"></a>Répliquer les machines virtuelles de Hyper-V dans les nuages VMM à Azure à l’aide de la récupération de Site Azure avec le portail Azure | Microsoft Azure

> [AZURE.SELECTOR]
- [Azure portal](site-recovery-vmm-to-azure.md)
- [Azure classique](site-recovery-vmm-to-azure-classic.md)
- [Gestionnaire de ressources de PowerShell](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [PowerShell classique](site-recovery-deploy-with-powershell.md)

Bienvenue dans restauration du Site Azure ! Utilisez cet article si vous souhaitez répliquer les machines virtuelles de Hyper-V sur site géré dans des nuages de System Center Virtual Machine Manager (VMM) pour Azure à l’aide de la récupération de Site Azure dans le portail Azure.

> [AZURE.NOTE]Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model
> ) pour la création et l’utilisation des ressources : le Gestionnaire de ressources Azure et classique. Azure dispose également de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement.


Récupération de Site Azure dans le portail Azure fournit plusieurs nouvelles fonctionnalités :

- Dans le portail Azure les services Azure sauvegarde et récupération de Site Azure sont combinées dans un coffre-fort de Services de récupération unique, afin que vous pouvez configurer et gérer la continuité d’activité et de reprise après sinistre (BCDR) à partir d’un emplacement unique. Un tableau de bord unifié vous permet de surveiller et de gérer des opérations sur vos sites locaux et le nuage public Azure.
- Les utilisateurs disposant d’un abonnement Azure mis en service avec le programme fournisseur de solutions de Cloud (CSP) peuvent désormais gérer les opérations de récupération de Site dans le portail Azure.
- Restauration du site portail Azure peut répliquer des machines à des comptes de stockage Azure le Gestionnaire de ressources. Lors du basculement, récupération de Site crée le Gestionnaire de ressources sur des ordinateurs virtuels dans Azure.
- Récupération de site continue de prendre en charge les comptes de stockage classique de la réplication. Lors du basculement, récupération de Site crée des machines virtuelles à l’aide du modèle classique.


Après avoir lu cet article, valider les commentaires en bas dans les commentaires de Disqus. Poser des questions techniques sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Les organisations ont besoin d’une stratégie BCDR qui détermine comment les applications, les charges de travail et les données restent en cours d’exécution et disponible pendant les temps d’arrêt planifiés et non planifiés et récupérer dès que possible dans des conditions normales de fonctionnement. Votre stratégie de BCDR doit conserver les données d’entreprise d’et s’assurer que les charges de travail restent disponibles en permanence en cas de reprise après sinistre.

Récupération de site est un service Azure qui contribue à votre stratégie de BCDR en orchestrant ces opérations de réplication de serveurs physiques des locaux et des ordinateurs virtuels vers le nuage (Azure), ou à un centre de données secondaire. Lorsque des pannes se produisent dans votre emplacement principal, vous basculent vers l’emplacement secondaire afin que les applications et charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’il est retourné à un fonctionnement normal. Pour en savoir plus [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md)

Cet article fournit toutes les informations dont vous avez besoin pour répliquer les VM Hyper-V dans des nuages VMM à Azure sur site. Il inclut une présentation de l’architecture, des informations et des étapes de déploiement pour la configuration d’Azure, des serveurs sur site, les paramètres de réplication et planification de la capacité de planification. Une fois que vous avez configuré l’infrastructure que vous pouvez activer la réplication sur les ordinateurs que vous voulez protéger et vérifier que le basculement fonctionne.


## <a name="business-advantages"></a>Avantages commerciaux

- Récupération de site fournit une protection hors site pour les grosses charges de travail et les applications qui s’exécutent sur des ordinateurs virtuels Hyper-V.
- Le portail de Services de récupération fournit un emplacement unique pour configurer, gérer et contrôler la réplication, de basculement et de restauration.
- Vous pouvez facilement exécuter des basculements de votre infrastructure sur site d’Azure et de retour arrière (restauration) d’Azure pour les serveurs hôtes Hyper-V dans votre site local.
- Vous pouvez configurer les plans de récupération avec plusieurs ordinateurs afin que les charges de travail applicatives hiérarchisé basculent ensemble.


## <a name="scenario-architecture"></a>Architecture de scénario


Voici les composants du scénario :

- **Serveur VMM**: un serveur VMM sur site avec un ou plusieurs nuages.
- **Hôte Hyper-V ou cluster**: les serveurs hôtes Hyper-V ou clusters gérés dans les nuages VMM.
- **Fournisseur de récupération de Site Azure et d’agent de récupération de services**: au cours du déploiement, vous installez le fournisseur de récupération de Site Azure sur le serveur VMM et l’agent de Services de récupération de Microsoft Azure sur les serveurs hôtes Hyper-V. Le fournisseur sur le serveur VMM communique avec récupération de Site via HTTPS 443 répliquer d’orchestration. L’agent sur le serveur hôte Hyper-V réplique les données de stockage Azure via HTTPS 443 par défaut.
- **Azure**: vous devez un abonnement Azure, un compte de stockage Azure pour stocker de répliquer des données et un réseau virtuel Azure afin que les ordinateurs virtuels d’Azure sont connectés à un réseau après le basculement.

![Topologie de E2A](./media/site-recovery-vmm-to-azure/architecture.png)


## <a name="azure-prerequisites"></a>Conditions préalables Azure

Voici ce que vous devez dans Azure pour déployer ce scénario.

**Condition préalable** | **Détails**
--- | ---
**Compte Azure**| Vous avez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification de la récupération de Site.
**Stockage Azure** | Vous avez besoin d’un compte standard de stockage Azure pour stocker les données répliquées. Vous pouvez utiliser un compte de stockage LRS ou GRS. Nous vous recommandons de GRS afin que les données sont résilientes si une panne régionale se produit, ou si la région principale ne peut pas être récupérée. [En savoir plus](../storage/storage-redundancy.md). Le compte doit être dans la même région, comme le coffre-fort de Services de récupération.<br/><br/>Stockage de la prime n’est pas pris en charge.<br/><br/> Les données répliquées sont stockées dans le stockage Azure et d’Azure VM sont créés lorsqu’un basculement se produit. <br/><br/> [En savoir plus sur](../storage/storage-introduction.md) Stockage Azure.
**Réseau Azure** | Vous avez besoin d’un réseau virtuel Azure Azure VMs de se connecter à lors du basculement. Le réseau doit être dans la même région, comme le coffre-fort de Services de récupération.

## <a name="on-premises-prerequisites"></a>Conditions préalables du local

Voici ce dont vous avez besoin sur site

**Condition préalable** | **Détails**
--- | ---
**VMM**| Un ou plusieurs serveurs VMM s’exécutant sur System Center 2012 R2. Chaque serveur VMM doit avoir un ou plusieurs nuages configurés. Un nuage doit contenir :<br/><br/> Un ou plusieurs groupes hôtes VMM.<br/><br/> Un ou plusieurs serveurs d’hôte Hyper-V ou clusters dans chaque groupe hôte.<br/><br/>[En savoir plus](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) sur la configuration des nuages VMM.
**Hyper-V** | Serveurs de l’hôte Hyper-V doivent être en cours d’exécution au moins **Windows Server 2012 R2** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012 R2** et installé les dernières mises à jour.<br/><br/> Un serveur Hyper-V doit contenir les ordinateurs virtuels d’un ou plusieurs.<br/><br/> Un serveur d’hôte Hyper-V ou le cluster qui inclut les ordinateurs virtuels que vous souhaitez répliquer doit être géré dans un nuage VMM.<br/><br/>Serveurs Hyper-V doivent être connectés à Internet, soit directement, soit via un proxy.<br/><br/>Serveurs Hyper-V doivent avoir les correctifs mentionnés dans l’article [2961977](https://support.microsoft.com/kb/2961977) et installé.<br/><br/>Les serveurs d’hôte Hyper-V doivent accéder à internet pour la réplication de données vers Azure.
**Fournisseur et l’agent** | Au cours du déploiement de la récupération de Site d’Azure, vous allez installer le fournisseur de récupération de Site Azure sur le serveur VMM et l’agent de récupération Services sur des hôtes Hyper-V. Le fournisseur et l’agent doivent se connecter pour Azure sur internet directement ou via un proxy. Notez qu’un proxy basée sur HTTPS n’est pas pris en charge. Le serveur proxy sur le serveur VMM et les hôtes Hyper-V doit autoriser l’accès à : <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blob.core.windows.net <br/><br/> *. store.core.windows.net<br/><br/>Si vous avez des règles de pare-feu basé sur l’adresse IP sur le serveur VMM, vérifiez que les règles autorisent les communications vers Azure. Vous devez autoriser les [Plages d’Azure centre de données IP](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/><br/>Autoriser les plages d’adresses IP pour la région Azure de votre abonnement et ouest des États-Unis.<br/><br/>De plus,. le serveur proxy sur le serveur VMM a besoin d’accéder à https://www.msftncsi.com/ncsi.txt


## <a name="protected-machine-prerequisites"></a>Conditions préalables d’ordinateur protégé


**Condition préalable** | **Détails**
--- | ---
**Ordinateurs virtuels protégés** | Avant de basculer sur un ordinateur virtuel, assurez-vous que le nom est affecté à la machine virtuelle d’Azure est conforme aux [conditions préalables Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Vous pouvez modifier le nom après avoir activé la réplication pour la machine virtuelle. <br/><br/> Capacité de chaque disque sur les ordinateurs protégés ne doit pas être supérieure à 1 023 go. Une machine virtuelle peut avoir jusqu'à 16 disques (ainsi jusqu'à 16 To).<br/><br/> Partagé invité disque clusters ne sont pas pris en charge.<br/><br/> Unified Extensible Firmware Interface UEFI () Extensible Firmware Interface(EFI) démarrage n’est pas pris en charge.<br/><br/> Si la source de machine virtuelle a l’association de cartes réseau, il est converti à une seule carte réseau après le basculement vers Azure.<br/><br/>Protection des ordinateurs virtuels exécutant Linux avec une adresse IP statique n’est pas pris en charge.

## <a name="prepare-for-deployment"></a>Préparer pour le déploiement

Pour vous préparer pour le déploiement, vous devez :

1. [Configurer un réseau Azure](#set-up-an-azure-network) dans lequel Azure VM se trouve après le basculement.
2. [Configurer un compte de stockage Azure](#set-up-an-azure-storage-account) pour les données répliquées.
4. [Préparer le serveur VMM](#prepare-the-vmm-server) pour le déploiement de la récupération de Site.
5. [Préparation de mappage réseau](#prepare-for-network-mapping). Configurer des réseaux afin que vous puissiez configurer mappage réseau lors du déploiement de la récupération de Site.

### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Vous avez besoin d’un réseau d’Azure afin que les ordinateurs virtuels d’Azure créé après que basculement doit se connecter.

- Le réseau doit être dans la même région que celui dans lequel vous déployez le coffre-fort de Services de récupération.
- Selon le modèle de ressource que vous souhaitez utiliser pour basculé Azure VM, vous définirez le réseau Azure en [mode de gestionnaire de ressources](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [en mode classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Nous vous recommandons de que configurer un réseau avant de commencer. Si vous ne le faites pas, vous devez le faire pendant le déploiement de la récupération de Site.

> [AZURE.NOTE] [Migration de réseaux](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les réseaux utilisés pour le déploiement de récupération de Site.


### <a name="set-up-an-azure-storage-account"></a>Configurer un compte de stockage Azure

- Vous aurez besoin d’un compte standard de stockage Azure pour contenir les données répliquées vers Azure. Le compte doit être dans la même région, comme le coffre-fort de Services de récupération.
- Selon le modèle de ressource que vous souhaitez utiliser pour basculé Azure VM, permet de paramétrer un compte dans le [Gestionnaire de ressources du mode](../storage/storage-create-storage-account.md) ou [classique](../storage/storage-create-storage-account-classic-portal.md).
- Nous vous conseillons de définir un compte avant de commencer. Si vous ne le faites pas, vous devez le faire pendant le déploiement de la récupération de Site.

> [AZURE.NOTE] [Migration des comptes de stockage](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

### <a name="prepare-the-vmm-server"></a>Préparer le serveur VMM

- Assurez-vous que le serveur VMM est conforme à la [Configuration requise](#on-premises-prerequisites).
- Au cours du déploiement de la récupération de Site, vous pouvez spécifier que tous les nuages sur un serveur VMM doivent être disponibles sur le portail Azure. Si vous souhaitez uniquement les nuages spécifiques apparaisse dans le portail, vous pouvez activer ce paramètre sur le nuage dans la console administrateur de VMM.


### <a name="prepare-for-network-mapping"></a>Préparation de mappage réseau

Vous devez configurer le mappage réseau lors du déploiement de la récupération de Site. Mappage réseau établit la correspondance entre la source de VMM VM réseaux et cibler des réseaux Azure pour activer les éléments suivants :

- Machines de basculer sur le même réseau peuvent se connecter à l’autre, même s’il ne sont pas a basculé de la même manière ou dans le même plan de récupération.
- Si une passerelle réseau est configurée sur la cible de réseau Azure, les ordinateurs virtuels Azure peuvent se connecter en local des machines virtuelles.
- Pour configurer le réseau, mappage ici est que vous devez préparer :

    - Assurez-vous que les ordinateurs virtuels sur le serveur hôte de Hyper-V source sont connectés à un réseau de VMM VM. Ce réseau doit être lié à un réseau logique qui est associé avec le nuage.
    - Un réseau Azure comme décrit [ci-dessus](#set-up-an-azure-network)

- [En savoir plus](site-recovery-network-mapping.md) sur le fonctionne du mappage réseau.


## <a name="create-a-recovery-services-vault"></a>Créer un coffre-fort de Services de récupération

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **gestion** > **Services de récupération**. Vous pouvez également cliquer sur **Parcourir** > **Services de récupération des** coffres-forts > **Ajouter**.

    ![Nouveau coffre-fort](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. Dans la zone **nom**, spécifiez un nom convivial pour identifier le coffre-fort. Si vous avez plus d’un abonnement, sélectionnez un d'entre eux.
4. [Créer un groupe de ressources](../resource-group-template-deploy-portal.md), ou sélectionnez-en un. Permet de spécifier une région Azure. Machines seront répliqués à cette région. Pour vérifier les régions pris en charge consultez disponibilité géographique dans les [Détails de la tarification de récupération de Site de Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si vous souhaitez accéder rapidement à la chambre forte du tableau de bord, cliquez sur **fixer au tableau de bord** > **créer coffre-fort**.

    ![Nouveau coffre-fort](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

Le nouveau coffre-fort s’affiche sur le **tableau de bord** > **toutes les ressources**et sur les principaux **Services de récupération des coffres-forts** lame.

## <a name="getting-started"></a>Mise en route

Récupération de site fournit une prise en main qui vous aide à déployer aussi rapidement que possible. Mise en route, vérifie les conditions préalables et vous guide tout au long des étapes de déploiement dans le bon ordre de récupération de Site.

Dans mise en route vous permet de sélectionner le type d’ordinateurs que vous souhaitez répliquer, et dans lequel vous souhaitez répliquer sur. Permet de paramétrer des serveurs sur site, comptes de stockage Azure et réseaux. Vous créez des règles de réplication et effectuez la planification de la capacité. Après que vous avez configuré votre infrastructure vous activez la réplication pour les machines virtuelles. Vous pouvez exécuter des basculements sur incident pour les machines spécifiques, ou créer des plans de récupération basculer sur plusieurs machines.

Commencer la mise en route, choisissez comment vous souhaitez déployer la récupération de Site. Le flux de mise en route change légèrement en fonction de vos besoins en matière de réplication.



## <a name="step-1-choose-your-protection-goals"></a>Étape 1 : Choisissez vos objectifs de protection

Sélectionnez ce que vous voulez répliquer et dans lequel vous souhaitez répliquer sur.

1. La lame de **Services de récupération des coffres-forts** , sélectionnez votre coffre-fort et cliquez sur **paramètres**.
2. Dans **Mise en route** , cliquez sur **Restauration du Site** > **étape 1 : préparation de l’Infrastructure** > **objectif de Protection**.

    ![Choisissez des objectifs](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. Dans **l’objectif de Protection** sélectionner **Sur Azure**et sélectionnez **Oui, avec Hyper-V**. Cliquez sur **Oui** pour confirmer que vous utilisez VMM pour gérer les hôtes Hyper-V et le site de reprise. Puis cliquez sur **OK**.

    ![Choisissez des objectifs](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## <a name="step-2-set-up-the-source-environment"></a>Étape 2 : Configurer l’environnement source

Installez le fournisseur de récupération de Site Azure sur le serveur VMM et inscrire le serveur dans le coffre-fort. Installez l’agent des Services de récupération Azure sur les hôtes Hyper-V.

1. Cliquez sur **l’étape 2 : préparer l’Infrastructure** > **Source**.

    ![Configurer la source](./media/site-recovery-vmm-to-azure/set-source1.png)

2. Dans **source de préparation** cliquez sur **+ VMM** pour ajouter un serveur VMM.

    ![Configurer la source](./media/site-recovery-vmm-to-azure/set-source2.png)

3. Dans le chèque de lame de **Add Server** **System Center VMM serveur** s’affiche dans le **type de serveur** et que le serveur VMM répond aux [conditions préalables et aux exigences des URL](#on-premises-prerequisites).
4. Téléchargez le fichier d’installation de fournisseur de récupération de Site Azure.
5. Télécharger la clé d’enregistrement. Vous devez cela lorsque vous exécutez le programme d’installation. La clé est valide pendant cinq jours une fois que vous le générez.

    ![Configurer la source](./media/site-recovery-vmm-to-azure/set-source3.png)

6. Installez le fournisseur de récupération de Site Azure sur le serveur VMM.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurer le fournisseur de récupération de Site Azure

1.  Exécutez le fournisseur de fichier de configuration.
2. Dans **Microsoft Update,** vous pouvez la choisir les mises à jour afin que les mises à jour du fournisseur sont installés en fonction de votre stratégie de Microsoft Update.
3. Dans **Installation**, accepter ou modifier l’emplacement d’installation de fournisseur par défaut et cliquez sur **installer**.

    ![Emplacement d’installation](./media/site-recovery-vmm-to-azure/provider2.png)

4. Fin de l’installation cliquez sur **Enregistrer** pour enregistrer le serveur VMM dans la chambre forte.
5. Dans la page **Paramètres de coffre-fort** , cliquez sur **Parcourir** pour sélectionner le fichier de clé de coffre-fort. Spécifier l’abonnement Azure récupération de Site et le nom de la chambre forte.

    ![Inscription du serveur](./media/site-recovery-vmm-to-azure/provider10.PNG)

6. Dans **Connexion à Internet**, spécifiez comment le fournisseur en cours d’exécution sur le serveur VMM se connectera à la récupération de Site sur internet.

    - Si vous souhaitez que le fournisseur pour vous connecter directement, sélectionnez **se connecter directement à la récupération de Site Azure sans proxy**.
    - Si votre proxy existant requiert une authentification, ou que vous souhaitez utiliser une instruction select proxy personnalisé **se connecter à la récupération de Site Azure à l’aide d’un serveur proxy**.
    - Si vous utilisez un proxy personnalisé, spécifiez les informations d’identification, le port et l’adresse.
    - Si vous utilisez un serveur proxy vous avez devez déjà autorisé les URL décrites dans les [conditions préalables](#on-premises-prerequisites).
    - Si vous utilisez un proxy personnalisé qu'un compte RunAs de VMM (DRAProxyAccount) est créé automatiquement avec les informations d’identification du proxy spécifié. Configurer le serveur proxy afin que ce compte peut s’authentifier avec succès. Les paramètres de compte de VMM RunAs peuvent être modifiés dans la console VMM. Dans **paramètres**, développez **sécurité** > **Exécuter en tant que comptes**, puis modifiez le mot de passe pour DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.

    ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)

7. Acceptez ou modifiez l’emplacement d’un certificat SSL qui est généré automatiquement pour le cryptage des données. Ce certificat est utilisé si vous activez le cryptage des données pour un nuage protégé par Azure dans le portail de récupération de Site Azure. Conservez ce certificat. Lorsque vous exécutez un basculement sur incident sur Azure, vous aurez besoin pour décrypter, si le cryptage des données est activé.


8. Dans **nom du serveur**, spécifiez un nom convivial pour identifier le serveur VMM dans la chambre forte. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.
9. Activer les **métadonnées de nuage de synchronisation** si vous souhaitez synchroniser les métadonnées pour tous les nuages sur le serveur VMM à la chambre forte. Cette action ne doit se produire qu’une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les nuages, vous pouvez laisser ce paramètre désactivé et synchroniser chaque nuage individuellement dans les propriétés du nuage dans la console VMM. Cliquez sur **Enregistrer** pour terminer le processus.

    ![Inscription du serveur](./media/site-recovery-vmm-to-azure/provider16.PNG)

10. L’enregistrement démarre. Une fois l’inscription terminée, le serveur s’affiche dans les **paramètres** > lame de**serveurs** dans la chambre forte.


#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Installation de ligne de commande pour le fournisseur de récupération de Site Azure

Le fournisseur de récupération de Site Azure peut être installé à partir de la ligne de commande. Cette méthode peut être utilisée pour installer le fournisseur sur le Server Core de Windows Server 2012 R2.

1. Téléchargez la clé de l’enregistrement de fichiers et d’installation de fournisseur dans un dossier. Par exemple, C:\ASR.
2. À partir d’une invite de commandes avec élévation de privilèges, exécutez ces commandes pour extraire le programme d’installation du fournisseur :

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Exécutez cette commande pour installer les composants :

            C:\ASR> setupdr.exe /i

4. Puis exécutez ces commandes pour inscrire le serveur dans le coffre-fort :

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Où :

- **/Credentials**: un paramètre obligatoire qui spécifie où se trouve le fichier de clé d’enregistrement.  
- **/FriendlyName**: un paramètre obligatoire pour le nom du serveur hôte Hyper-V qui s’affiche dans le portail de récupération de Site Azure.
- - **/EncryptionEnabled**: un paramètre optionnel lorsque vous utilisez la réplication VMs de Hyper-V dans VMM nuages à Azure. Spécifiez si vous souhaitez crypter les machines virtuelles dans Azure (au cryptage de repos). Assurez-vous que le nom du fichier a une extension **.pfx** . Le cryptage est désactivé par défaut.
- **/ProxyAddress**: paramètre optionnel qui spécifie l’adresse du serveur proxy.
- **/ProxyPort**: paramètre optionnel qui spécifie le port du serveur proxy.
- **/proxyUsername**: paramètre optionnel qui spécifie le nom d’utilisateur proxy (si le proxy exige une authentification).
- **/proxyPassword**: paramètre optionnel qui spécifie le mot de passe pour l’authentification avec le serveur proxy (si le proxy exige une authentification).


### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Installer l’agent de Services de récupération Azure sur les hôtes Hyper-V

1. Une fois que vous avez défini le fournisseur, vous devez télécharger le fichier d’installation de l’agent des Services de récupération Azure. Exécutez le programme d’installation sur chaque serveur Hyper-V dans le nuage VMM.

    ![Sites de Hyper-V](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. Sur la page **Prerequisites Check** , cliquez sur **suivant**. Les composants requis manquants seront automatiquement installés.

    ![Agent de Services de récupération de conditions préalables](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. Dans la page **Paramètres d’Installation** , acceptez ou modifiez l’emplacement de l’installation et l’emplacement du cache. Vous pouvez configurer le cache sur un lecteur disposant d’au moins 5 Go de stockage disponible, mais nous vous recommandons d’un lecteur de cache avec 600 Go ou plus d’espace libre. Puis cliquez sur **installer**.
4. Une fois l’installation terminée, cliquez sur **Fermer** pour terminer.

    ![Inscrire l’Agent de MARS](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Installation de ligne de commande pour l’agent de Services de récupération de Site Azure

Vous pouvez installer l’Agent de Services de récupération de Microsoft Azure à partir de la ligne de commande à l’aide de la commande suivante :

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Définir les accès proxy à internet pour la récupération de Site à partir des hôtes Hyper-V

L’agent de récupération Services s’exécutant sur les hôtes Hyper-V nécessite un accès internet pour Azure pour la réplication de la machine virtuelle. Si vous accédez à internet via un serveur proxy, définissez-le comme suit :

1. Ouvrez le composant logiciel enfichable MMC de Microsoft Azure sauvegarde sur l’hôte Hyper-V. Par défaut, un raccourci pour Microsoft Azure Backup est disponible sur le bureau ou dans C:\Program Files\Microsoft Azure récupération Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Sous l’onglet **Configuration du serveur Proxy** , spécifiez les informations du serveur proxy.

    ![Inscrire l’Agent de MARS](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. Assurez-vous que l’agent peut atteindre l’URL indiqué dans les [conditions préalables](#on-premises-prerequisites).


## <a name="step-3-set-up-the-target-environment"></a>Étape 3 : Définir l’environnement cible

Spécifiez le compte de stockage Azure à utiliser pour la réplication et le réseau Azure auquel Azure VM se connectera après le basculement.

1.  Cliquez sur **préparer infrastructure** > **cible** et sélectionnez l’abonnement Azure que vous souhaitez utiliser.
2.  Spécifiez le modèle de déploiement que vous souhaitez utiliser pour les machines virtuelles après le basculement.
3.  Récupération de site vérifie que vous disposez d’un ou plusieurs comptes de stockage Azure compatible et des réseaux.

    ![Stockage](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4.  Si vous n’avez pas créé un compte de stockage et que vous souhaitez en créer un à l’aide du Gestionnaire de ressources, cliquez sur **+ compte de stockage** pour faire qu’inline.  Sur la lame de **créer de compte de stockage** , spécifiez un nom de compte, un type, un abonnement et un emplacement. Le compte doit être dans le même emplacement que le coffre-fort de Services de récupération.

    ![Stockage](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

    Notez que :

    - Si vous souhaitez créer un compte de stockage en utilisant le modèle classique, vous le faire dans le portail Azure. [Pour en savoir plus](../storage/storage-create-storage-account-classic-portal.md)
    - Si vous utilisez un compte de prime de stockage pour les données répliquées, vous devez définir un compte de stockage standard supplémentaire pour stocker les journaux de réplication qui capturent les modifications en cours pour les données locales.

4.  Si vous n’avez pas créé un réseau Azure et si vous souhaitez en créer un à l’aide du Gestionnaire de ressources cliquez sur **+ réseau** à faire dont inline. Sur la lame de **créer des réseaux virtuels** , spécifiez un nom de réseau, plage d’adresses, détails du sous-réseau, abonnement et l’emplacement. Le réseau doit être dans le même emplacement que le coffre-fort de Services de récupération.

    ![Réseau](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

    Si vous souhaitez créer un réseau utilisant le modèle classique vous procéderez dans Azure portal. [En savoir plus](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Configurer le mappage réseau

- [Lire](#prepare-for-network-mapping) un aperçu rapide du mappage réseau effectue. Pour une explication plus approfondie, [Lisez ceci](site-recovery-network-mapping.md) .
- Vérifiez que les ordinateurs virtuels sur le serveur VMM sont connectés à un réseau de la machine virtuelle et que vous avez créé au moins un réseau virtuel Azure. Plusieurs réseaux de l’ordinateur virtuel peuvent être mappés à un seul réseau Azure.

Configurer le mappage comme suit :

1. Dans les **paramètres** > **Infrastructure de restauration de Site** > **mappages réseau** > **Mappage du réseau**, cliquez sur l’icône **+ le mappage réseau** .

    ![Mappage réseau](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Dans le **mappage réseau d’Ajouter** sélectionner le serveur VMM de source et **Azure** comme cible.
3. Vérifiez que l’abonnement et le modèle de déploiement après le basculement.
4. **Réseau Source**, sélectionnez le réseau de machine virtuelle sur site source que vous souhaitez mapper à partir de la liste associée au serveur VMM.
5. Dans le **réseau de la cible**, sélectionnez le réseau Azure dans les réplicas Azure VM se trouve lorsqu’elles sont créées. Puis cliquez sur **OK**.

    ![Mappage réseau](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Voici ce qui se produit lorsque le mappage réseau commence :

- Machines virtuelles existantes sur le réseau de la machine virtuelle source sont connectés au réseau cible lors de la mise en correspondance commence. Connecté au réseau de la machine virtuelle source de nouveaux ordinateurs virtuels sont connectés au réseau Azure mappé lors de la réplication se produit.
- Si vous modifiez un mappage réseau existant, les machines virtuelles de réplica sera connectés avec les nouveaux paramètres.
- Si le réseau cible a plusieurs sous-réseaux et de ces sous-réseaux a le même nom que le sous-réseau sur lequel se trouve l’ordinateur virtuel source, l’ordinateur virtuel de réplica sera connecté à ce sous-réseau cible après le basculement.
- S’il n’existe aucun sous-réseau cible avec un nom correspondant, l’ordinateur virtuel va être connecté pour le premier sous-réseau dans le réseau.



## <a name="step-4-set-up-replication-settings"></a>Étape 4 : Définir des paramètres de réplication


1. Pour créer une nouvelle stratégie de réplication, cliquez sur **préparer infrastructure** > **Les paramètres de réplication** > **+ créer et associer**.

    ![Réseau](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. **Création et association de stratégie**, spécifiez un nom de stratégie.
3. Dans la zone **fréquence de copie**, spécifiez la fréquence à laquelle vous souhaitez répliquer les données du delta après la réplication initiale (toutes les 30 secondes, 5 ou 15 minutes).
4. Dans la **rétention de point de récupération**, spécifier en heures combien de temps la fenêtre de rétention pour chaque point de récupération. Ordinateurs protégés peuvent être récupérés à n’importe quel point dans une fenêtre.
6. De **fréquence des snapshots cohérents au niveau de l’application**, spécifiez la fréquence (de 1 à 12 heures) des points de récupération contenant des snapshots cohérents avec l’application sera créé. Hyper-V utilise deux types de captures instantanées, une capture instantanée standard qui fournit un instantané incrémentiel de la machine virtuelle complète et une capture instantanée cohérente de l’application qui prend un instantané de point-à-temps des données de l’application à l’intérieur de l’ordinateur virtuel. Des snapshots cohérents avec l’application utilisent Volume Shadow Copy Service (VSS) pour s’assurer que les applications sont dans un état cohérent lorsque l’instantané est pris. Notez que si vous activez des snapshots cohérents avec l’application, elle affectera les performances des applications s’exécutant sur des machines virtuelles de source. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.
3. Dans **heure de début de la réplication initiale**, vous pouvez indiquer quand lancer la réplication initiale. La réplication se produit sur votre bande passante internet, vous pouvez donc choisir de le planifier en dehors de vos heures de disponibilité.
5. **Chiffrer les données stockées sur Azure**, spécifier s’il faut chiffrer les données reste dans le stockage Azure. Puis cliquez sur **OK**.

    ![Stratégie de réplication](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. Lorsque vous créez une nouvelle stratégie, il est automatiquement associé le nuage VMM. Cliquez sur **OK**. Vous pouvez associer des nuages VMM supplémentaires (et les ordinateurs virtuels dans les) dans les **paramètres de**cette stratégie de réplication > **réplication** > nom de la stratégie > **Associer un nuage VMM**.

    ![Stratégie de réplication](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>Étape 5 : Planification de la capacité

Maintenant que vous avez votre basic infrastructure configuration vous pouvez réfléchir à la planification de la capacité et déterminer si vous avez besoin de ressources supplémentaires.

Récupération de site fournit un planificateur de capacités pour vous aider à allouer les ressources de votre environnement source, composants de restauration de site, mise en réseau et de stockage. Vous pouvez exécuter le Planificateur de mode rapide pour les estimations basées sur un nombre moyen de machines virtuelles, des disques et du stockage, ou en mode détaillé, dans laquelle vous allez saisir les chiffres du niveau de charge de travail. Avant de commencer à vous devrez :

- Rassembler des informations sur votre environnement de réplication, y compris les machines virtuelles, des disques par les ordinateurs virtuels et stockage par disque.
- Estimer le taux de modification (évolution du) quotidiennes que vous avez pour les données répliquées. Vous pouvez utiliser le [Planificateur de capacités pour Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) pour vous aider à effectuer cette opération.

1.  Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. [Lire l’article](site-recovery-capacity-planner.md) accompagnant l’outil.
2.  Lorsque vous avez terminé de **vous avez exécuté le Planificateur de capacités**, sélectionnez **Oui** ?

    ![Planification de la capacité](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considérations relatives à la bande passante réseau

Vous pouvez utiliser l’outil capacity planner pour calculer la bande passante que nécessaire pour la réplication (réplication initiale puis delta). Vous disposez de plusieurs options pour contrôler la quantité d’utilisation de la bande passante pour la réplication :

- **Limiter la bande passante**: le trafic de Hyper-V qui réplique vers un site secondaire passe par un hôte Hyper-V. Vous pouvez limiter la bande passante sur le serveur hôte.
- **Optimiser la bande passante**: vous pouvez influencer la bande passante utilisée pour la réplication à l’aide de deux clés de Registre.

#### <a name="throttle-bandwidth"></a>Limiter la bande passante

1. Ouvrez le composant logiciel enfichable MMC de Microsoft Azure sauvegarde sur le serveur hôte Hyper-V. Par défaut, un raccourci pour Microsoft Azure Backup est disponible sur le bureau ou dans C:\Program Files\Microsoft Azure récupération Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Sur l’onglet **régulation** sélectionnez **Activer l’utilisation de la bande passante internet de limitation pour les opérations de sauvegarde**et définir les limites pour le travail et non liés au travail heures. Les plages valides sont comprises entre 512 Kbits/s et 102 Mbps par seconde.

    ![Limiter la bande passante](./media/site-recovery-vmm-to-azure/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting-NoThrottle** indique qu’aucune limitation n’est requis.


#### <a name="influence-network-bandwidth"></a>Influence de la bande passante réseau

La valeur de Registre **UploadThreadsPerVM** contrôle le nombre de threads qui sont utilisés pour le transfert des données (réplication initiale ou delta) d’un disque. Une valeur plus élevée augmente la bande passante utilisée pour la réplication. La valeur de Registre **DownloadThreadsPerVM** Spécifie le nombre de threads utilisé pour le transfert de données pendant la restauration.

1. Dans le Registre, accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.

    - Modifiez la valeur **UploadThreadsPerVM** (ou créer la clé si elle n’existe pas) pour contrôler les threads utilisés pour la réplication de disque.
    - Modifiez la valeur **DownloadThreadsPerVM** (ou créer la clé si elle n’existe pas) pour contrôler les threads utilisés pour le trafic de restauration à partir d’Azure.
2. La valeur par défaut est 4. Dans un réseau « overprovisioned », ces clés de Registre doivent être modifiés à partir des valeurs par défaut. La valeur maximale est 32. Surveiller le trafic afin d’optimiser la valeur.

## <a name="step-6-enable-replication"></a>Étape 6 : Réplication d’activer

Activer la réplication comme suit :

1. Cliquez sur **étape 2 : application de répliquer** > **Source**. Après avoir activé la réplication pour la première fois que vous cliquez sur **+ répliquer** dans le coffre-fort pour activer la réplication pour d’autres ordinateurs.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. De la lame de la **Source** > sélectionnez le serveur VMM et le nuage dans lequel se trouvent les hôtes Hyper-V. Puis cliquez sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. Dans la **cible** , sélectionnez l’abonnement, modèle de déploiement de post-basculement et du compte de stockage que vous utilisez pour les données répliquées.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. Sélectionnez le compte de stockage que vous souhaitez utiliser. Si vous souhaitez utiliser un compte de stockage autre que celles vous avez vous pouvez [en créer un](#set-up-an-azure-storage-account). Pour créer un espace de stockage compte en utilisant le modèle de gestionnaire de ressources, cliquez sur **Créer nouveau**. Si vous souhaitez créer un compte de stockage en utilisant le modèle classique, vous faites que [dans le portail Azure](../storage/storage-create-storage-account-classic-portal.md). Puis cliquez sur **OK**.
5. Sélectionnez le réseau Azure et le sous-réseau auquel Azure VM va se connecter lorsqu’ils vous lancés après le basculement. Sélectionnez **configurer pour les ordinateurs sélectionnés** pour appliquer le paramètre de réseau à tous les ordinateurs, que vous sélectionnez pour la protection. Sélectionnez **configurer ultérieurement** pour sélectionner le réseau Azure par machine. Si vous souhaitez utiliser un autre réseau de ceux vous avez vous pouvez [créer un](#set-up-an-azure-network). Pour créer un réseau en utilisant le modèle de gestionnaire de ressources, cliquez sur **Créer nouveau**. Si vous souhaitez créer un réseau utilisant le modèle classique, vous ferez ce [portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Le cas échéant, sélectionnez un sous-réseau. Puis cliquez sur **OK**.
6. Dans des **Machines virtuelles** > **Sélectionnez machines virtuelles** cliquez sur et sélectionnez chaque ordinateur que vous souhaitez répliquer. Vous ne pouvez sélectionner que les machines pour lesquelles la réplication peut être activée. Puis cliquez sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. Dans les **Propriétés** > **Propriétés de configuration**, sélectionnez le système d’exploitation pour les ordinateurs virtuels sélectionnés et que le disque du système d’exploitation. Puis cliquez sur **OK**. Vous pouvez définir des propriétés supplémentaires ultérieurement.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication6.png)


12. Dans les **paramètres de réplication** > de**configurer les paramètres de réplication**, sélectionnez la stratégie de réplication que vous souhaitez appliquer pour les ordinateurs virtuels protégés. Puis cliquez sur **OK**. Vous pouvez modifier la stratégie de réplication dans les **paramètres** > **règles de réplication** > nom de la stratégie > **Modifier les paramètres**. Vous appliquez les modifications sont utilisées pour les ordinateurs qui répliquent déjà et les nouvelles machines.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Vous pouvez suivre la progression de la tâche **d’Activation de la Protection** dans les **paramètres** > **travaux** > **tâches de récupération de Site**. Après l’exécution de la tâche de **Protection de finaliser** l’ordinateur est prêt pour le basculement.

### <a name="view-and-manage-vm-properties"></a>Permet d’afficher et de gérer les propriétés de la machine virtuelle

Nous vous recommandons de vérifier les propriétés de l’ordinateur source. N’oubliez pas que le nom d’Azure VM doit être conforme aux [exigences d’Azure VM](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Cliquez sur **paramètres** > **Protégé des éléments** > **Les éléments répliqués** > et sélectionnez l’ordinateur pour afficher ses détails.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. Dans **Propriétés** vous pouvez afficher des informations de réplication et le basculement de la machine virtuelle.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. Dans le **calcul et le réseau** > **Propriétés de calcul** vous pouvez spécifier la taille du nom et de la cible Azure VM. Modifier le nom pour satisfaire aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) si vous avez besoin. Vous pouvez également afficher et modifier les informations sur le réseau cible, le sous-réseau et l’adresse IP qui est attribuée à la machine virtuelle d’Azure. Notez les points suivants :

    - Vous pouvez définir l’adresse IP cible. Si vous ne fournissez pas une adresse, l’échec sur l’ordinateur utilise DHCP. Si vous définissez une adresse qui n’est pas disponible lors du basculement, le basculement échoue. La même adresse IP cible peut servir pour le test de basculement si l’adresse est disponible dans le réseau de basculement test.
    - Le nombre de cartes réseau est déterminée par la taille que vous spécifiez pour l’ordinateur virtuel de la cible, comme suit :

        - Si le nombre de cartes réseau sur l’ordinateur source est inférieur ou égal au nombre de cartes autorisé pour la taille de l’ordinateur cible, la cible ont le même nombre de cartes comme source.
        - Si le nombre de cartes sur l’ordinateur virtuel source dépasse le nombre autorisé pour la taille de la cible, puis le maximum de la taille cible est utilisé.
        - Par exemple, si une machine source possède deux cartes réseau et la taille de la machine cible prend en charge quatre, l’ordinateur cible aura deux cartes. Si l’ordinateur source dispose de deux cartes, mais la taille cible prise en charge prend uniquement en charge un l’ordinateur cible aura qu’une seule carte.     
        - Si l’ordinateur virtuel possède plusieurs cartes réseau qu’ils seront tous se connecter au même réseau.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.  Dans les **disques** vous pouvez voir le système d’exploitation et les disques de données sur l’ordinateur virtuel qui sera répliquée.



## <a name="step-7-test-your-deployment"></a>Étape 7 : Tester votre déploiement

Pour tester le déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle ou un plan de récupération qui contient un ou plusieurs ordinateurs virtuels.


### <a name="prepare-for-failover"></a>Préparer pour le basculement

- Pour exécuter un test de basculement, nous vous recommandons de créer un nouveau réseau Azure qui a isolé de votre réseau de production Azure (il s’agit de comportement par défaut lorsque vous créez un nouveau réseau dans Azure). [En savoir plus](site-recovery-failover.md#run-a-test-failover) sur l’exécution du test de basculement.
- Pour obtenir les meilleures performances lorsque vous basculer sur Azure, installez l’Agent Azure sur l’ordinateur protégé. Il rend le démarrage plus rapide et simplifie le dépannage. Installez l’agent [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Pour tester votre déploiement, vous avez besoin d’une infrastructure pour la machine répliquée à fonctionner comme prévu. Si vous souhaitez tester Active Directory et DNS, vous pouvez créer un ordinateur virtuel comme un contrôleur de domaine dans DNS et répliquer ces paramètres sur Azure à l’aide de la récupération de Site Azure. Lire en plusieurs [Considérations de basculement test pour Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Si vous souhaitez exécuter un basculement non planifié et non d’un test de basculement, notez les points suivants :

    - Si possible vous devez arrêter les ordinateurs principales avant d’exécuter un basculement non planifié. Cela garantit que vous n’avez pas les ordinateurs source et de réplica en cours d’exécution en même temps.
    - Lorsque vous exécutez un basculement non planifié il cesse de réplication des données à partir d’ordinateurs principales afin que n’importe quel delta de données ne seront pas transférée après qu’un basculement non planifié commence. En outre si vous exécutez un basculement non planifié sur un plan de récupération il s’exécutera jusqu'à la fin, même si une erreur se produit.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer pour se connecter aux ordinateurs virtuels d’Azure après basculement

Si vous souhaitez vous connecter aux ordinateurs virtuels Azure en utilisant le protocole RDP après le basculement, assurez-vous que vous effectuez les opérations suivantes :

**Sur l’ordinateur local avant le basculement**:

- Pour un accès via internet activent le protocole RDP, assurez-vous que les règles de protocoles TCP et UDP sont ajoutées pour le **Public**et que RDP est autorisé dans le **Pare-feu Windows** -> **les applications autorisées et fonctionnalités** pour tous les profils.
- Pour l’accès via une connexion de site à site activent le protocole RDP sur l’ordinateur et vous assurer que le protocole RDP est autorisé dans le **Pare-feu Windows** -> **les applications autorisées et fonctionnalités** pour les réseaux **privé** et de **domaine** .
- Installation de l' [agent d’Azure VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) sur l’ordinateur local.
- Assurez-vous que l’option stratégie de SAN du système d’exploitation est définie sur OnlineAll. [Pour en savoir plus]( https://support.microsoft.com/kb/3031135)
- Désactiver le service IPSec avant d’exécuter le basculement.

**Sur l’Azure VM après basculement sur incident**:

- Ajouter un point de terminaison public pour le protocole RDP (le port 3389) et spécifiez les informations d’identification pour la connexion.
- Assurez-vous que vous n’avez pas les stratégies de domaine qui vous empêchent de vous connecter à un ordinateur virtuel à l’aide d’une adresse publique.
- Essayez de vous connecter. Si vous ne pouvez pas vous connecter, vérifiez que l’ordinateur virtuel est en cours d’exécution. Pour plus de conseils de dépannage de lire cet [article](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Si vous souhaitez accéder à un ordinateur virtuel de Azure exécutant Linux après le basculement à l’aide d’un SSH client (ssh), effectuez les opérations suivantes :

**Sur l’ordinateur local avant le basculement**:

- Assurez-vous que le service de Shell sécurisé sur l’ordinateur virtuel d’Azure est défini pour démarrer automatiquement au démarrage du système.
- Vérifiez que les règles de pare-feu autorisent une connexion SSH.

**Sur l’Azure VM après basculement sur incident**:

- Les règles du groupe de sécurité réseau sur l’échec de la machine virtuelle et le sous-réseau Azure auquel il est connecté est nécessaire autoriser les connexions entrantes sur le port SSH.
- Un point de terminaison public doit être créé pour autoriser les connexions entrantes sur le port SSH (port TCP 22 par défaut).
- Si la machine virtuelle est accessible via une connexion VPN (Express itinéraire ou VPN de site à site) le client peut être utilisé pour se connecter directement à la machine virtuelle via SSH.


### <a name="run-a-test-failover"></a>Exécutez un test de basculement

Pour exécuter le test de basculement sur incident ne les éléments suivants :

1. Basculer sur un ordinateur virtuel unique dans les **paramètres** > **Répliquées des éléments**, cliquez sur la machine virtuelle > **+ basculement de Test**.
2. Pour basculer d’un plan de récupération, dans les **paramètres** > de**Plans de reprise**, droit le plan > **Test de basculement**. Pour créer une récupération envisagez de [suivre ces instructions](site-recovery-create-recovery-plans.md).

3. Dans le **Test de basculement** , sélectionnez Azure réseau auquel se connecter Azure VM après que basculement sur incident se produit.
4. Cliquez sur **OK** pour lancer le basculement. Vous pouvez suivre la progression en cliquant sur la machine virtuelle pour ouvrir ses propriétés ou sur le projet de **Test de basculement** dans les **paramètres** > **tâches de récupération de Site**.
5. Lorsque le basculement atteint la phase **de test terminée** , effectuez les opérations suivantes :

    1. Permet d’afficher la machine virtuelle de réplica dans le portail Azure. Vérifiez que l’ordinateur virtuel démarre correctement.
    2. Si vous avez défini pour les ordinateurs virtuels de l’accès à votre réseau local, vous pouvez lancer une connexion Bureau à distance sur l’ordinateur virtuel.
    3. Cliquez sur **Terminer le test** pour le terminer.
    4. Cliquez sur **Notes** pour enregistrer et enregistrer les observations associées avec le basculement de test.
    5. Cliquez sur **le test de basculement est achevé**. Nettoyer l’environnement de test pour mettre hors tension automatiquement et de supprimer l’ordinateur virtuel de test.
    6. À ce stade, tous les éléments ou ordinateurs virtuels créés automatiquement lors de la récupération de Site pendant le basculement du test sont supprimés. Tous les éléments supplémentaires que vous avez créé pour le test de basculement ne sont pas supprimés.

    > [AZURE.NOTE] Si un basculement sur incident test continue de plus de deux semaines, il est rempli en vigueur.

6. Une fois le basculement terminé vous devez également être en mesure de voir le réplica Azure machine apparaît dans le portail Azure > **Machines virtuelles**. Il se peut que vous devez vous assurer que la machine virtuelle est la taille appropriée, auquel il est connecté au réseau approprié, et qu’il s’exécute.
7. Si vous [prêt pour les connexions après basculement](#prepare-to-connect-to-Azure-VMs-after-failover) vous devez être en mesure de se connecter à la machine virtuelle d’Azure.


## <a name="monitor-your-deployment"></a>Surveillez votre déploiement

Voici comment vous pouvez contrôler les paramètres de configuration, l’état et la santé pour votre déploiement de récupération de Site :

1. Cliquez sur le nom de coffre-fort pour accéder à **l’éducation** du tableau de bord. Dans ce tableau de bord, vous pouvez travaux restauration du Site, état de la réplication, les plans de récupération, l’intégrité du serveur et événements.  Vous pouvez personnaliser Essentials pour afficher les carreaux et les dispositions qui sont plus utiles, y compris l’état des autres coffres-forts de sauvegarde et de récupération de Site.

    ![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)

2. Dans la mosaïque de **la santé** , vous pouvez surveiller des serveurs de site (serveurs VMM ou configuration) qui rencontrent le problème et les événements déclenchés par la récupération de Site au cours des 24 dernières heures.
3. Vous pouvez gérer et contrôler la réplication dans **Réplication des éléments**, des **Plans de reprise**, et les **Tâches de récupération de Site** dispose en mosaïque. Vous pouvez affiner les travaux dans les **paramètres** -> **travaux** -> **Tâches de récupération de Site**.


## <a name="next-steps"></a>Étapes suivantes

Une fois votre déploiement est configuré et en cours d’exécution, [en savoir plus](site-recovery-failover.md) sur les différents types de basculement.
