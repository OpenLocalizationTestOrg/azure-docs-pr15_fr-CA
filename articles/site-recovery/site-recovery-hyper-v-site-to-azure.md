<properties
    pageTitle="Répliquer les machines virtuelles de Hyper-V (sans VMM) pour Azure à l’aide de la récupération de Site Azure avec le portail Azure | Microsoft Azure"
    description="Décrit comment déployer la récupération de Site Azure pour orchestrer la réplication, le basculement et la restauration d’ordinateurs virtuels Hyper-V sur site qui ne sont pas gérés par VMM pour Azure via le portail Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="raynew"/>


# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Répliquer les machines virtuelles de Hyper-V (sans VMM) pour Azure à l’aide de la récupération de Site Azure avec le portail Azure

> [AZURE.SELECTOR]
- [Azure Portal](site-recovery-hyper-v-site-to-azure.md)
- [Azure classique](site-recovery-hyper-v-site-to-azure-classic.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-deploy-with-powershell-resource-manager.md)



Bienvenue dans restauration du Site Azure ! Utilisez cet article si vous souhaitez répliquer sur site Hyper-V virtual machines qui **ne sont pas** gérés dans des nuages de System Center Virtual Machines Manager (VMM) pour Azure. Cet article explique comment configurer la réplication à l’aide de la récupération de Site Azure dans le portail Azure.

> [AZURE.NOTE] Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et l’utilisation des ressources : le Gestionnaire de ressources Azure et classique. Azure dispose également de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement.

> Récupération de Site Azure prend en charge la restauration et la migration des ordinateurs virtuels Hyper-V Azure. Les étapes décrites dans cet article s’appliquent même lors de la configuration de la réplication vers Azure de reprise après sinistre ou pour la migration de machines virtuelles vers Azure

Récupération de Site Azure dans le portail Azure fournit un certain nombre de nouvelles fonctionnalités :

- Dans l’Azure portal, sauvegarde d’Azure et les services de récupération de Site Azure sont combinés en un seul coffre-fort de Services de récupération afin que vous pouvez configurer et gérer la continuité d’activité et de reprise après sinistre (BCDR) à partir d’un emplacement unique. Un tableau de bord unifié permet de contrôler et de gérer des opérations sur vos sites locaux et le nuage public Azure.
- Les utilisateurs disposant d’un abonnement Azure mis en service avec le programme fournisseur de solutions de Cloud (CSP) peuvent désormais gérer les opérations de récupération de Site dans le portail Azure.
- Restauration du site portail Azure peut répliquer des machines à des comptes de stockage de gestionnaire de ressources. Lors du basculement, récupération de Site crée le Gestionnaire de ressources sur des ordinateurs virtuels dans Azure.
- Récupération de site continue de prendre en charge les comptes de stockage classique et basculement des ordinateurs virtuels en utilisant le modèle de déploiement classique de la réplication.


Après avoir lu cet article article vos commentaires au bas de la section de commentaires Disqus. Poser des questions techniques sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Vue d’ensemble


Les organisations ont besoin d’une stratégie BCDR qui détermine comment les applications, les charges de travail et les données restent en cours d’exécution et disponible pendant les temps d’arrêt planifiés et non planifiés et récupérer dès que possible dans des conditions normales de fonctionnement. Votre stratégie de BCDR conserve les données d’entreprise d’et vous assurer que les charges de travail sont disponibles en permanence en cas de reprise après sinistre.

Récupération de site est un service Azure qui participe à votre stratégie de BCDR par l’orchestration de réplication de serveurs physiques des locaux et des ordinateurs virtuels vers le nuage (Azure) ou à un centre de données secondaire. Lorsque des pannes se produisent dans votre emplacement principal, vous pouvez basculer sur l’emplacement secondaire afin que les applications et charges de travail disponibles. Vous pouvez restauré sur votre emplacement principal lors de son retour à la normale. Pour en savoir plus [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md)

Cet article fournit toutes les informations que vous devez répliquer sur site Hyper-V virtual machines qui **ne sont pas** gérés dans des nuages de System Center Virtual Machines Manager (VMM) pour Azure. Il inclut une présentation de l’architecture, des informations et des étapes de déploiement pour la configuration des serveurs sur site, Azure, une stratégie de réplication et planification de la capacité de planification. Après que vous avez configuré l’infrastructure vous pouvez activer la réplication sur les ordinateurs que vous souhaitez protéger et effectuer un basculement sur incident de test pour valider la configuration. Vous pouvez également migrer vos ordinateurs virtuels vers Azure en effectuant d’abord un basculement planifié et ensuite effectuer la migration.

## <a name="business-advantages"></a>Avantages commerciaux

- Le basculement sur incident de hors site (Azure) prévoit de grosses charges de travail et les applications qui s’exécutent sur des ordinateurs virtuels Hyper-V.
- Fournit une seule console de Services de récupération de la simplicité d’installation et de gestion des processus de réplication, de basculement et de restauration.
- Vous permet de facilement exécuter des basculements de votre infrastructure sur site vers Azure et reprise (restauration) d’Azure sur le site local.
- Vous pouvez configurer les plans de récupération avec plusieurs ordinateurs, afin que les charges de travail applicatives hiérarchisé basculent ensemble.

## <a name="scenario-architecture"></a>Architecture de scénario

Voici les composants du scénario :

- **Hôte Hyper-V ou cluster**: clusters ou des serveurs sur site Hyper-V. Les hôtes Hyper-V qui exécute les ordinateurs virtuels que vous souhaitez protéger sont rassemblées dans les sites de Hyper-V logiques au cours du déploiement de la récupération de Site.
- **Fournisseur de récupération de Site Azure et d’agent de récupération de services**: au cours du déploiement vous installez le fournisseur de récupération de Site Azure et l’agent de Services de récupération de Microsoft Azure sur les serveurs hôtes Hyper-V. Le fournisseur communique avec récupération de Site Azure sur 443 HTTPS pour répliquer d’orchestration. L’agent sur le serveur hôte Hyper-V réplique les données de stockage Azure via HTTPS 443 par défaut.
- **Azure**: vous devez un abonnement Azure, un compte de stockage Azure pour stocker de répliquer des données et un réseau virtuel Azure afin que les ordinateurs virtuels d’Azure sont connectés à un réseau après le basculement.

![Architecture du site de Hyper-V](./media/site-recovery-hyper-v-site-to-azure/architecture.png)



## <a name="azure-prerequisites"></a>Conditions préalables Azure

Voici ce que vous devrez dans Azure déployer ce scénario.

**Condition préalable** | **Détails**
--- | ---
**Compte Azure**| Vous devez disposer d’un compte [Microsoft Azure](http://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification de la récupération de Site.
**Stockage Azure** | Vous aurez besoin d’un compte de stockage standard. Vous pouvez utiliser un compte de stockage LRS ou GRS. Nous vous recommandons de GRS afin que les données sont résilientes si une panne régionale se produit, ou si la région principale ne peut pas être récupérée. [En savoir plus](../storage/storage-redundancy.md). Le compte doit être dans la même région, comme le coffre-fort de Services de récupération.<br/><br/> Stockage de la prime n’est pas pris en charge.<br/><br/> Les données répliquées sont stockées dans le stockage Azure et d’Azure VM sont créés lorsqu’un basculement se produit.<br/><br/> [En savoir plus sur](../storage/storage-introduction.md) Stockage Azure.
**Réseau Azure** | Vous aurez besoin d’un réseau virtuel Azure qui se connectera à Azure VM lors du basculement. Le réseau virtuel Azure doit être dans la même région, comme le coffre-fort de Services de récupération.

## <a name="on-premises-prerequisites"></a>Conditions préalables du local

Voici ce dont vous aurez besoin sur site.

**Condition préalable** | **Détails**
--- | ---
**Hyper-V** | Un ou plusieurs locaux serveurs exécutant **Windows Server 2012 R2** avec les dernières mises à jour et le rôle Hyper-V est activé ou **Microsoft Hyper-V Server 2012 R2**.<br/><br/>Le serveur Hyper-V doit contenir un ou plusieurs ordinateurs virtuels.<br/><br/>Serveurs Hyper-V doivent être connectés à Internet, soit directement, soit via un proxy.<br/><br/>Serveurs Hyper-V doivent avoir les correctifs mentionnés dans [KB2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977") installé.
**Fournisseur et l’agent** | Au cours du déploiement de la récupération de Site d’Azure, vous allez installer le fournisseur de récupération de Site Azure. L’installation du fournisseur installe également l’Agent de Services de récupération Azure sur chaque serveur Hyper-V qui exécute les ordinateurs virtuels que vous souhaitez protéger. Tous les serveurs Hyper-V dans un coffre-fort de récupération de Site doivent avoir les mêmes versions du fournisseur et de l’agent.<br/><br/>Le fournisseur devront se connecter à la récupération de Site Azure via Internet. Le trafic peut être envoyé directement ou via un proxy. Notez que les proxy HTTPS en fonction n’est pas pris en charge. Le serveur proxy doit autoriser l’accès à : <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blog.core.windows.net <br/><br/> *Store.Core.Windows.NET <br/><br/> https://www.msftncsi.com/ncsi.txt<br/><br/>Si vous avez des règles de pare-feu basé sur l’adresse IP sur le serveur, vérifiez que les règles autorisent les communications vers Azure. Vous devez autoriser les [Plages d’Azure centre de données IP](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/><br/>Autoriser les plages d’adresses IP pour la région Azure de votre abonnement et ouest des États-Unis.

## <a name="protected-machine-prerequisites"></a>Conditions préalables d’ordinateur protégé


**Condition préalable** | **Détails**
--- | ---
**Ordinateurs virtuels protégés** | Avant de basculer sur un ordinateur virtuel, vous devrez vous assurer que le nom qui sera affecté à la machine virtuelle d’Azure est conforme aux [conditions préalables Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Vous pouvez modifier le nom après avoir activé la réplication pour la machine virtuelle.<br/><br/> Capacité de chaque disque sur les ordinateurs protégés ne doit pas être supérieure à 1 023 go. Une machine virtuelle peut avoir jusqu'à 16 disques (ainsi jusqu'à 16 To).<br/><br/> Partagé invité disque clusters ne sont pas pris en charge.<br/><br/> Si la source de machine virtuelle a l’association de cartes réseau, il est converti à une seule carte réseau après le basculement vers Azure.<br/><br/>Protection des ordinateurs virtuels exécutant Linux avec une adresse IP statique n’est pas pris en charge.

## <a name="prepare-for-deployment"></a>Préparer pour le déploiement

Pour vous préparer pour le déploiement, vous devez :

1. [Configurer un réseau Azure](#set-up-an-azure-network) dans lequel Azure VM se trouve lorsqu’elles sont créées après le basculement.
2. [Configurer un compte de stockage Azure](#set-up-an-azure-storage-account) pour les données répliquées.
3. [Les hôtes Hyper-V de préparation](#prepare-the-hyper-v-hosts) pour garantir qu’ils peuvent accéder à l’URL requises.

### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Permet de paramétrer un réseau Azure. Vous aurez besoin afin que les ordinateurs virtuels d’Azure créés après le basculement sont connectés à un réseau.

- Le réseau doit être dans la même région que celui dans lequel vous allez déployer le coffre-fort de Services de récupération.
- Selon le modèle de ressource que vous souhaitez utiliser pour basculé Azure VM, vous définirez le réseau Azure en [mode de gestionnaire de ressources](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [en mode classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Nous vous recommandons de que configurer un réseau avant de commencer. Dans le cas contraire, vous devrez le faire pendant le déploiement de la récupération de Site.

> [AZURE.NOTE] [Migration de réseaux](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les réseaux utilisés pour le déploiement de récupération de Site.

### <a name="set-up-an-azure-storage-account"></a>Configurer un compte de stockage Azure

- Vous aurez besoin d’un compte standard de stockage Azure pour contenir les données répliquées vers Azure.
- Selon le modèle de ressource que vous souhaitez utiliser pour basculé Azure VM, vous devez définir un compte dans le [Gestionnaire de ressources du mode](../storage/storage-create-storage-account.md) ou [classique](../storage/storage-create-storage-account-classic-portal.md).
- Nous vous recommandons de configurer un compte de stockage avant de commencer. Dans le cas contraire, vous devrez le faire pendant le déploiement de la récupération de Site. Les comptes doivent être dans la même région, comme le coffre-fort de Services de récupération.

> [AZURE.NOTE] [Migration des comptes de stockage](../resource-group-move-resources.md) entre les groupes de ressources de l’abonnement même ou à travers des abonnements n’est pas pris en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

### <a name="prepare-the-hyper-v-hosts"></a>Préparer les hôtes Hyper-V

- Assurez-vous que les hôtes Hyper-V sont conformes avec les [conditions requises](#on-premises-prerequisites).

### <a name="create-a-recovery-services-vault"></a>Créer un coffre-fort de Services de récupération

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **gestion** > **sauvegarde et restauration de Site (OMS)**. Vous pouvez également cliquer sur **Parcourir** > **Services de récupération des** coffres-forts > **Ajouter**.

    ![Nouveau coffre-fort](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)

3. Dans la zone **nom** , spécifiez un nom convivial pour identifier le coffre-fort. Si vous avez plus d’un abonnement, sélectionnez un d'entre eux.
4. [Créer un nouveau groupe de ressources](../resource-group-template-deploy-portal.md) ou sélectionnez-en un et spécifier une région Azure. Machines seront répliqués à cette région. Pour vérifier les régions pris en charge consultez disponibilité géographique dans les [Détails de la tarification de récupération de Site de Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si vous souhaitez accéder rapidement à la chambre forte du tableau de bord, cliquez sur **fixer au tableau de bord** et puis cliquez sur **créer coffre-fort**.

    ![Nouveau coffre-fort](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

Le nouveau coffre-fort s’affiche sur le **tableau de bord** > **toutes les ressources**et sur les principaux **Services de récupération des coffres-forts** lame.

## <a name="getting-started"></a>Mise en route

Récupération de site fournit une prise en main qui vous aide à déployer aussi rapidement que possible. Mise en route, vérifie les conditions préalables et vous guide tout au long des étapes de déploiement dans le bon ordre de récupération de Site.

Dans mise en route vous permet de sélectionner le type d’ordinateurs que vous souhaitez répliquer, et dans lequel vous souhaitez répliquer sur. Permet de paramétrer des serveurs sur site, comptes de stockage Azure et réseaux. Vous créez des règles de réplication et effectuez la planification de la capacité. Après que vous avez configuré votre infrastructure vous activez la réplication pour les machines virtuelles. Vous pouvez exécuter des basculements sur incident pour les machines spécifiques, ou créer des plans de récupération basculer sur plusieurs machines.

Commencer la mise en route, choisissez comment vous souhaitez déployer la récupération de Site. Le flux de mise en route change légèrement en fonction de vos besoins en matière de réplication.



## <a name="step-1-choose-your-protection-goals"></a>Étape 1 : Choisissez vos objectifs de protection

Sélectionnez ce que vous voulez répliquer et dans lequel vous souhaitez répliquer sur.

1. La lame de **Services de récupération des coffres-forts** Sélectionnez votre coffre-fort et cliquez sur **paramètres**.
2. Dans les **paramètres** > **Mise en route** cliquez sur **Restauration du Site** > **étape 1 : préparation de l’Infrastructure** > **objectif de Protection**.

    ![Choisissez des objectifs](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)

3. Dans **l’objectif de Protection** sélectionner **Sur Azure**et sélectionnez **Oui, avec Hyper-V**. Sélectionnez **non** pour confirmer que vous n’utilisez pas de VMM. Puis cliquez sur **OK**.

    ![Choisissez des objectifs](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Étape 2 : Configurer l’environnement source

Configurer le site de Hyper-V, installez le fournisseur de récupération de Site Azure et l’agent de Services de récupération Azure sur les hôtes Hyper-V et enregistrer les hôtes dans la chambre forte.


1. Cliquez sur **l’étape 2 : préparer l’Infrastructure** > **Source**. Pour ajouter un nouveau site de Hyper-V en tant que conteneur pour vos hôtes Hyper-V ou de clusters, cliquez sur **+ Site de Hyper-V**.

    ![Configurer la source](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)

2. Dans le **site de création de Hyper-V** de lame de spécifier un nom pour le site. Puis cliquez sur **OK**. Sélectionnez le site que vous venez de créer.

    ![Configurer la source](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. Cliquez sur **+ Hyper-V Server** pour ajouter un serveur pour le site.
4. Dans **Ajouter un serveur** > **type de serveur de** vérifier que **Hyper-V server** s’affiche. Assurez-vous que le serveur Hyper-V que vous souhaitez ajouter est conforme à la [Configuration requise](#on-premises-prerequisites) et qu’il est en mesure d’accéder à l’URL spécifiées.
4. Téléchargez le fichier d’installation de fournisseur de récupération de Site Azure. Vous devez exécuter ce fichier pour installer le fournisseur et l’agent de récupération Services sur chaque hôte Hyper-V.
5. Télécharger la clé d’enregistrement. Vous aurez besoin lorsque vous exécutez le programme d’installation. La clé est valide pendant 5 jours une fois que vous le générez.

    ![Configurer la source](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)

6. Exécutez le fournisseur de fichier d’installation sur chaque hôte que vous avez ajouté au site Hyper-V. Si vous installez sur un cluster Hyper-V, exécutez le programme d’installation sur chaque nœud du cluster. Installation et enregistrement de chaque nœud de Cluster Hyper-V permet de s’assurer que les ordinateurs virtuels restent protégées même si ils migrent entre les nœuds.

### <a name="install-the-provider-and-agent"></a>Installez le fournisseur et l’agent

1. Exécutez le fournisseur de fichier de configuration.
2. Dans **Microsoft Update,** vous pouvez la choisir les mises à jour afin que les mises à jour du fournisseur sont installés en fonction de votre stratégie de Microsoft Update.
3. Dans **Installation** accepter ou modifier l’emplacement d’installation de fournisseur par défaut et cliquez sur **installer**.
4. Dans la page **Paramètres de coffre-fort** , cliquez sur **Parcourir** pour sélectionner le fichier de clé de chambre forte que vous avez téléchargé. Spécifier l’abonnement Azure récupération de Site, le nom de la chambre forte et le site de Hyper-V auquel appartient le serveur Hyper-V.

    ![Inscription du serveur](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5 dans les **Paramètres de Proxy** , spécifiez comment le fournisseur qui est installé sur le serveur se connectera à la récupération de Site Azure via internet.

- Si vous souhaitez que le fournisseur pour vous connecter directement, sélectionnez **se connecter directement sans proxy**.
- Si vous souhaitez vous connecter avec le proxy qui est actuellement configuré sur le serveur, sélectionnez **se connecter avec les paramètres de serveur proxy existants**.
- Si votre proxy existant requiert une authentification, ou que vous souhaitez utiliser un proxy personnalisé pour la sélection de connexion de fournisseur **se connecter avec les paramètres de proxy personnalisés**.
- Si vous utilisez un proxy personnalisé, que vous devez spécifier les informations d’identification, le port et l’adresse
- Si vous utilisez un serveur proxy, vérifiez que l’URL indiqué dans les [conditions préalables](#on-premises-prerequisites) sont autorisés par son intermédiaire.

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6 une fois l’installation terminée cliquez sur **Enregistrer** pour enregistrer le serveur dans la chambre forte.

![Emplacement d’installation](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7 une fois l’inscription terminée métadonnées Hyper-V server est extraites lors de la récupération de Site Azure et le serveur s’affiche dans les **paramètres** > **Infrastructure de restauration de Site** > lame**d’Hôtes Hyper-V** .


### <a name="command-line-installation"></a>Installation de ligne de commande

Le fournisseur de récupération de Site Azure et d’un agent peuvent également être installés à l’aide de la ligne de commande. Cette méthode peut être utilisée pour installer le fournisseur sur un Server Core de Windows Server 2012 R2.

1. Téléchargez la clé de l’enregistrement de fichiers et d’installation de fournisseur dans un dossier. Par exemple, C:\ASR.
2. À partir d’une invite de commandes avec élévation de privilèges, exécutez ces commandes pour extraire le programme d’installation du fournisseur :

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Exécutez cette commande pour installer les composants :

            C:\ASR> setupdr.exe /i

4. Puis exécutez ces commandes pour inscrire le serveur dans le coffre-fort : CD C:\Program Files\Microsoft Azure Site récupération Provider\
            Fournisseur de récupération de Site Azure C:\Program Files\Microsoft\> DRConfigurator.exe /r /Friendlyname <friendly name of the server> /les informations d’identification<path of the credentials file>

<br/>
Où :

- **/Credentials** : un paramètre obligatoire qui spécifie l’emplacement dans lequel se trouve le fichier de clé d’enregistrement  
- **/FriendlyName** : un paramètre obligatoire pour le nom du serveur hôte Hyper-V qui s’affiche dans le portail de récupération de Site Azure.
- **/ProxyAddress** : paramètre optionnel qui spécifie l’adresse du serveur proxy.
- **/ProxyPort** : paramètre optionnel qui spécifie le port du serveur proxy.
- **/proxyUsername** : paramètre optionnel qui spécifie le nom d’utilisateur Proxy (si le proxy exige une authentification).
- **/proxyPassword** : paramètre optionnel qui spécifie le mot de passe pour l’authentification avec le serveur proxy (si le proxy exige une authentification).


## <a name="step-3-set-up-the-target-environment"></a>Étape 3 : Définir l’environnement cible

Spécifiez le compte de stockage Azure à utiliser pour la réplication et le réseau Azure auquel Azure VM se connectera après le basculement.

1.  Cliquez sur **préparer infrastructure** > **cible** et sélectionnez l’abonnement Azure que vous souhaitez utiliser.
2.  Spécifiez le modèle de déploiement que vous souhaitez utiliser pour les machines virtuelles après le basculement.
3.  Récupération de site vérifie que vous disposez d’un ou plusieurs comptes de stockage Azure compatible et des réseaux.

    ![Stockage](./media/site-recovery-hyper-v-site-to-azure/select-target.png)

4.  Si vous n’avez pas créé un compte de stockage et que vous souhaitez en créer un à l’aide du Gestionnaire de ressources cliquez sur **+ stockage** compte faire qu’en ligne. Sur la lame de **créer de compte de stockage** , spécifiez un nom de compte, un type, un abonnement et un emplacement. Le compte doit être dans le même emplacement que le coffre-fort de Services de récupération.

    ![Stockage](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)

    Si vous souhaitez créer un compte de stockage en utilisant le modèle classique, vous ferez que [dans le portail Azure](../storage/storage-create-storage-account-classic-portal.md).

5.  Si vous n’avez pas créé un réseau Azure et si vous souhaitez en créer un à l’aide du Gestionnaire de ressources cliquez sur **+ réseau** à faire dont inline. Sur la lame de **créer des réseaux virtuels** , spécifiez un nom de réseau, plage d’adresses, détails du sous-réseau, abonnement et l’emplacement. Le réseau doit être dans le même emplacement que le coffre-fort de Services de récupération.

    ![Réseau](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

    Si vous souhaitez créer un réseau utilisant le modèle classique, vous ferez ce [portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).


## <a name="step-4-set-up-replication-settings"></a>Étape 4 : Définir des paramètres de réplication

1. Pour créer une nouvelle réplication de stratégie, cliquez sur **préparer infrastructure** > **Les paramètres de réplication** > **+ créer et associer**.

    ![Réseau](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)

2. **Créer** et associer stratégie, spécifiez un nom de stratégie.
3. Dans la zone **Copier de fréquence** , spécifiez la fréquence à laquelle vous souhaitez répliquer les données du delta après la réplication initiale (toutes les 30 secondes, 5 ou 15 minutes).
4. Dans la **rétention de point de récupération**, spécifier en heures combien de temps la fenêtre de rétention pour chaque point de récupération. Ordinateurs protégés peuvent être récupérés à n’importe quel point dans une fenêtre.
6. Dans **fréquence des snapshots cohérents au niveau de l’application** , spécifiez la fréquence (de 1 à 12 heures) des points de récupération contenant des snapshots cohérents avec l’application sera créé. Hyper-V utilise deux types de captures instantanées, une capture instantanée standard qui fournit un instantané incrémentiel de la machine virtuelle complète et une capture instantanée cohérente de l’application qui prend un instantané de point-à-temps des données de l’application à l’intérieur de l’ordinateur virtuel. Des snapshots cohérents avec l’application utilisent Volume Shadow Copy Service (VSS) pour s’assurer que les applications sont dans un état cohérent lorsque l’instantané est pris. Notez que si vous activez des snapshots cohérents avec l’application, elle affectera les performances des applications s’exécutant sur des machines virtuelles de source. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.
3. Dans **heure de début de la réplication initiale** spécifier le moment auquel démarrer la réplication initiale. La réplication se produit sur votre bande passante internet, vous pouvez donc choisir de le planifier en dehors de vos heures de disponibilité. Puis cliquez sur **OK**.

    ![Stratégie de réplication](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Lorsque vous créez une nouvelle stratégie, il est automatiquement associé le site Hyper-V. Cliquez sur **OK**. Vous pouvez associer un site Hyper-V (et les ordinateurs virtuels qu’il contient) avec plusieurs stratégies de réplication dans les **paramètres** > **réplication** > nom de la stratégie > **Associer le Site Hyper-V**.

## <a name="step-5-capacity-planning"></a>Étape 5 : Planification de la capacité

Maintenant que vous avez votre basic infrastructure configuration vous pouvez réfléchir à la planification de la capacité et déterminer si vous avez besoin de ressources supplémentaires.

Récupération de site fournit un planificateur de capacités pour vous aider à allouer les ressources de votre environnement source, composants de restauration de site, mise en réseau et de stockage. Vous pouvez exécuter le Planificateur de mode rapide pour les estimations basées sur un nombre moyen de machines virtuelles, des disques et du stockage, ou en mode détaillé, dans laquelle vous allez saisir les chiffres du niveau de charge de travail. Avant de commencer à vous devrez :

- Rassembler des informations sur votre environnement de réplication, y compris les machines virtuelles, des disques par les ordinateurs virtuels et stockage par disque.
- Estimer le taux de modification (évolution du) quotidiennes que vous avez pour les données répliquées. Vous pouvez utiliser le [Planificateur de capacités pour Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) pour vous aider à effectuer cette opération.

1.  Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. [Lire l’article](site-recovery-capacity-planner.md) accompagnant l’outil.
2.  Lorsque vous avez terminé de **vous avez exécuté le Planificateur de capacités**, sélectionnez **Oui** ?

    ![Planification de la capacité](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considérations relatives à la bande passante réseau

Vous pouvez utiliser l’outil capacity planner pour calculer la bande passante que nécessaire pour la réplication (réplication initiale puis delta). Vous disposez de plusieurs options pour contrôler la quantité d’utilisation de la bande passante pour la réplication :

- **Limiter la bande passante**: le trafic de Hyper-V qui réplique vers Azure passe par un hôte Hyper-V. Vous pouvez limiter la bande passante sur le serveur hôte.
- **Optimiser la bande passante**: vous pouvez influencer la bande passante utilisée pour la réplication à l’aide de deux clés de Registre.

#### <a name="throttle-bandwidth"></a>Limiter la bande passante

1. Ouvrez le composant logiciel enfichable MMC de Microsoft Azure sauvegarde sur le serveur hôte Hyper-V. Par défaut, un raccourci pour Microsoft Azure Backup est disponible sur le bureau ou dans C:\Program Files\Microsoft Azure récupération Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Sur l’onglet **régulation** sélectionnez **Activer l’utilisation de la bande passante internet de limitation pour les opérations de sauvegarde**et définir les limites pour le travail et non liés au travail heures. Les plages valides sont comprises entre 512 Kbits/s et 102 Mbps par seconde.

    ![Limiter la bande passante](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting-NoThrottle** indique qu’aucune limitation n’est requis.


#### <a name="influence-network-bandwidth"></a>Influence de la bande passante réseau

1. Dans le Registre, accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
    - Pour influer sur le trafic de la bande passante sur un disque de réplication, modifiez la valeur de **UploadThreadsPerVM**, ou créer la clé si elle n’existe pas.
    - Pour influer sur la bande passante pour le trafic de retour arrière d’Azure, modifiez la valeur **DownloadThreadsPerVM**.
2. La valeur par défaut est 4. Dans un réseau « overprovisioned », ces clés de Registre doivent être modifiés à partir des valeurs par défaut. La valeur maximale est 32. Surveiller le trafic afin d’optimiser la valeur.

## <a name="step-6-enable-replication"></a>Étape 6 : Réplication d’activer

Activer la réplication comme suit :

1. Cliquez sur **étape 2 : application de répliquer** > **Source**. Après avoir activé la réplication pour la première fois que vous cliquerez sur **+ répliquer** dans le coffre-fort pour activer la réplication pour d’autres ordinateurs.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)

2. De la lame de la **Source** > sélectionnez le site de Hyper-V. Puis cliquez sur **OK**.
3. Dans **cible** , sélectionnez l’abonnement de coffre-fort et le modèle de basculement que vous souhaitez utiliser dans Azure (gestion classique ou ressource) après le basculement.
4. Sélectionnez le compte de stockage que vous souhaitez utiliser. Si vous souhaitez utiliser un compte de stockage autre que celles vous avez vous pouvez [en créer un](#set-up-an-azure-storage-account). Pour créer un espace de stockage compte en utilisant le modèle de gestionnaire de ressources, cliquez sur **Créer nouveau**. Si vous souhaitez créer un compte de stockage en utilisant le modèle classique, vous ferez que [dans le portail Azure](../storage/storage-create-storage-account-classic-portal.md). Puis cliquez sur **OK**.
5.  Sélectionnez le réseau Azure et le sous-réseau auquel Azure VM va se connecter lorsqu’ils vous lancés après le basculement. Sélectionnez **configurer pour les ordinateurs sélectionnés** pour appliquer le paramètre de réseau à tous les ordinateurs, que vous sélectionnez pour la protection. Sélectionnez **configurer ultérieurement** pour sélectionner le réseau Azure par machine. Si vous souhaitez utiliser un autre réseau de ceux vous avez vous pouvez [créer un](#set-up-an-azure-network). Pour créer un réseau en utilisant le modèle de gestionnaire de ressources, cliquez sur **Créer nouveau**. Si vous souhaitez créer un réseau utilisant le modèle classique, vous ferez ce [portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Le cas échéant, sélectionnez un sous-réseau. Puis cliquez sur **OK**.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. Dans des **Machines virtuelles** > **Sélectionnez machines virtuelles** cliquez sur et sélectionnez chaque ordinateur que vous souhaitez répliquer. Vous ne pouvez sélectionner que les machines pour lesquelles la réplication peut être activée. Puis cliquez sur **OK**.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication5.png)

11. Dans les **Propriétés** > **Propriétés de configuration**, sélectionnez le système d’exploitation pour les ordinateurs virtuels sélectionnés et que le disque du système d’exploitation. Vérifiez que le nom d’Azure VM (nom de la cible) est conforme aux [exigences de l’Azure VM](site-recovery-best-practices.md#azure-virtual-machine-requirements) et le modifier si vous le souhaitez. Puis cliquez sur **OK**. Vous pouvez définir des propriétés supplémentaires ultérieurement.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication6.png)

12. Dans les **paramètres de réplication** > de**configurer les paramètres de réplication**, sélectionnez la stratégie de réplication que vous souhaitez appliquer pour les ordinateurs virtuels protégés. Puis cliquez sur **OK**. Vous pouvez modifier la stratégie de réplication dans les **paramètres** > **règles de réplication** > nom de la stratégie > **Modifier les paramètres**. Vous appliquez des modifications seront utilisées pour les ordinateurs qui répliquent déjà et les nouvelles machines.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

Vous pouvez suivre la progression de la tâche **d’Activation de la Protection** dans les **paramètres** > **travaux** > **tâches de récupération de Site**. Après l’exécution de la tâche de **Protection de finaliser** l’ordinateur est prêt pour le basculement.

### <a name="view-and-manage-vm-properties"></a>Permet d’afficher et de gérer les propriétés de la machine virtuelle

Nous vous recommandons de vérifier les propriétés de l’ordinateur source.

1. Cliquez sur **paramètres** > **Protégé par les éléments** > **Les éléments répliqués** > et sélectionnez l’ordinateur.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)

2. Dans **Propriétés** vous pouvez afficher des informations de réplication et le basculement de la machine virtuelle.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)

3. Dans le **calcul et le réseau** > **Propriétés de calcul** vous pouvez spécifier la taille du nom et de la cible Azure VM. Modifier le nom pour satisfaire aux exigences Azure si vous avez besoin. Vous pouvez également afficher et modifier les informations sur le réseau cible, le sous-réseau et l’adresse IP qui sera affecté à la machine virtuelle d’Azure. Notez les points suivants :

    - Vous pouvez définir l’adresse IP cible. Si vous ne fournissez pas une adresse, l’échec sur l’ordinateur utilise DHCP. Si vous définissez une adresse qui n’est pas disponible lors du basculement, le basculement échoue. La même adresse IP cible peut servir pour le test de basculement si l’adresse est disponible dans le réseau de basculement test.
    - Le nombre de cartes réseau est déterminée par la taille que vous spécifiez pour l’ordinateur virtuel de la cible, comme suit :

        - Si le nombre de cartes réseau sur l’ordinateur source est inférieur ou égal au nombre de cartes autorisé pour la taille de l’ordinateur cible, la cible ont le même nombre de cartes comme source.
        - Si le nombre de cartes sur l’ordinateur virtuel source dépasse le nombre autorisé de la taille cible puis le maximum de la taille cible sera utilisé.
        - Par exemple, si une machine source possède deux cartes réseau et la taille de la machine cible prend en charge quatre, l’ordinateur cible aura deux cartes. Si l’ordinateur source dispose de deux cartes, mais la taille cible prise en charge prend uniquement en charge un l’ordinateur cible aura qu’une seule carte.     
        - Si l’ordinateur virtuel possède plusieurs cartes réseau qu’ils seront tous se connecter au même réseau.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

5.  Dans les **disques** vous pouvez voir le système d’exploitation et les disques de données sur l’ordinateur virtuel qui sera répliquée.


## <a name="step-7-test-the-deployment"></a>Étape 7 : Tester le déploiement

Pour tester le déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle ou un plan de récupération qui contient un ou plusieurs ordinateurs virtuels.


### <a name="prepare-for-test-failover"></a>Préparer pour le test de basculement

- Pour exécuter un test de basculement, nous vous recommandons de créer un nouveau réseau Azure qui a isolé de votre réseau de production Azure (il s’agit de comportement par défaut lorsque vous créez un nouveau réseau dans Azure). [En savoir plus](site-recovery-failover.md#run-a-test-failover) sur l’exécution du test de basculement.
- Pour obtenir les meilleures performances lorsque vous basculer sur Azure, installez l’Agent Azure sur l’ordinateur protégé. Il rend le démarrage plus rapide et simplifie le dépannage. Installez l’agent [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Pour tester votre déploiement, vous devez une infrastructure pour la machine répliquée à fonctionner comme prévu. Si vous souhaitez tester Active Directory et DNS, vous pouvez créer un ordinateur virtuel comme un contrôleur de domaine dans DNS et répliquer ces paramètres sur Azure à l’aide de la récupération de Site Azure. Lire en plusieurs [Considérations de basculement test pour Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
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

- Ajouter une adresse IP publique à la carte réseau associée à la machine virtuelle d’Azure pour autoriser le protocole RDP.
- Assurez-vous que vous n’avez pas les stratégies de domaine qui vous empêchent de vous connecter à un ordinateur virtuel à l’aide d’une adresse publique.
- Essayez de vous connecter. Si vous ne pouvez pas vous connecter Vérifiez que l’ordinateur virtuel est en cours d’exécution. Pour plus de conseils de dépannage de lire cet [article](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

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

    ![Basculement de test](./media/site-recovery-hyper-v-site-to-azure/run-failover1.png)

2. Pour basculer d’un plan de récupération, dans les **paramètres** > de**Plans de reprise**, droit le plan > **Test de basculement**. Pour créer une récupération envisagez de [suivre ces instructions](site-recovery-create-recovery-plans.md).

3. Dans le **Test de basculement** , sélectionnez réseau Azure auquel Azure VM sera connecté après basculement.

    ![Basculement de test](./media/site-recovery-hyper-v-site-to-azure/run-failover2.png)

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


## <a name="failover"></a>Basculement sur incident

Après que la réplication initiale est terminée pour vos ordinateurs, vous pouvez appeler des basculements en cas de besoin. Récupération de site prend en charge différents types de basculements - basculement du Test, basculement de planifié et non planifié.
[En savoir plus](site-recovery-failover.md) sur les différents types de basculements et des descriptions détaillées des quand et comment effectuer chacune d’elles.

> [AZURE.NOTE] Si votre intention est de migrer des machines virtuelles vers Azure, nous vous recommandons vivement d’utiliser une [opération planifiée de basculement](site-recovery-failover.md#run-a-planned-failover-primary-to-secondary) pour faire migrer les machines virtuelles vers Azure. Une fois que l’application migrée est validée dans Azure à l’aide du test de basculement, suivez les étapes mentionnées sous [Une Migration complète](#Complete-migration-of-your-virtual-machines-to-Azure) pour effectuer la migration de vos machines virtuelles. Vous n’avez pas besoin d’effectuer une validation ou une suppression. Migration complète assure la migration, supprime la protection de l’ordinateur virtuel et cesse de facturation Azure Site récupération pour l’ordinateur.


###<a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié

Il doit être choisi lorsque un site principal devient inaccessible en raison d’un incident inattendu, comme une attaque de virus ou de panne d’alimentation. Cette procédure décrit comment exécuter un basculement non planifié pour un plan de récupération. Vous pouvez également exécuter le basculement pour une seule machine virtuelle sur l’onglet ordinateurs virtuels. Avant de commencer, assurez-vous que tous les ordinateurs virtuels que vous souhaitez basculer ont terminé la réplication initiale.

1. Sélectionnez **Plans de reprise > recoveryplan_name**.
2. Sur la lame de plan de récupération, cliquez sur **Basculement non planifié**.
3. Sur la page de **basculement non planifié** , choisissez les emplacements source et cible.
4. Sélectionnez **Arrêter les machines virtuelles et synchroniser les données les plus récentes pour spécifier que la récupération de Site doit essayer à arrêter les ordinateurs virtuels protégés et synchroniser les données de manière à ce que la dernière version des données est alors basculée.**
5. Après le basculement, les ordinateurs virtuels sont dans une validation en état d’attente.  Cliquez sur **validation** pour valider le basculement.

[Pour en savoir plus](site-recovery-failover.md#run-an-unplanned-failover)

## <a name="complete-migration-of-your-virtual-machines-to-azure"></a>Achèvement de la migration de vos machines virtuelles vers Azure


>[AZURE.NOTE] Les étapes suivantes s’appliquent uniquement si vous migrez des machines virtuelles vers Azure

1. Effectuer un basculement planifié comme mentionné [ici](site-recovery-failover.md)
2. Dans **Paramètres > éléments répliqués**, avec le bouton droit de la machine virtuelle, puis sélectionnez **Une Migration complète**

    ![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

2. Cliquez sur **OK** pour terminer la migration. Vous pouvez suivre la progression en cliquant sur la machine virtuelle pour ouvrir ses propriétés, ou à l’aide de la tâche de Migration complète dans **Paramètres > tâches de récupération de Site**.


## <a name="monitor-your-deployment"></a>Surveillez votre déploiement

Voici comment vous pouvez contrôler les paramètres de configuration, l’état et la santé pour votre déploiement de récupération de Site :

1. Cliquez sur le nom de coffre-fort pour accéder à **l’éducation** du tableau de bord. Dans ce tableau de bord, vous pouvez travaux restauration du Site, état de la réplication, les plans de récupération, l’intégrité du serveur et événements.  Vous pouvez personnaliser Essentials pour afficher les carreaux et les dispositions qui sont plus utiles, y compris l’état des autres coffres-forts de sauvegarde et de récupération de Site.

    ![Essentials](./media/site-recovery-hyper-v-site-to-azure/essentials.png)

2. Dans la mosaïque de **la santé** , vous pouvez surveiller les serveurs de site qui en cas de problème et les événements déclenchés par la récupération de Site au cours des 24 dernières heures.
3. Vous pouvez gérer et contrôler la réplication dans **Réplication des éléments**, des **Plans de reprise**, et les **Tâches de récupération de Site** dispose en mosaïque. Vous pouvez affiner les travaux dans les **paramètres** -> **travaux** -> **Tâches de récupération de Site**.
