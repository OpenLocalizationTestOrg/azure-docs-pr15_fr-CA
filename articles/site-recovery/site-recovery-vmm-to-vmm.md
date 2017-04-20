<properties
    pageTitle="Répliquer les machines virtuelles de Hyper-V dans les nuages VMM sur un site secondaire de VMM via le portail Azure | Microsoft Azure"
    description="Décrit comment déployer la récupération de Site Azure pour orchestrer la réplication, le basculement et la récupération des ordinateurs virtuels de Hyper-V dans les nuages VMM sur un site secondaire de VMM via le portail Azure."
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
    ms.date="08/23/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Répliquer les machines virtuelles de Hyper-V dans les nuages VMM sur un site secondaire de VMM via le portail Azure

> [AZURE.SELECTOR]
- [Azure portal](site-recovery-vmm-to-vmm.md)
- [Portail classique](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bienvenue dans restauration du Site Azure ! Utilisez cet article si vous souhaitez répliquer les machines virtuelles de Hyper-V sur site géré dans des nuages de System Center Virtual Machine Manager (VMM) vers un site secondaire. Cet article explique comment configurer la réplication à l’aide de la récupération de Site Azure dans le portail Azure.

> [AZURE.NOTE] Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et l’utilisation des ressources : le Gestionnaire de ressources Azure et classique. Azure dispose également de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement.


Récupération de Site Azure dans le portail Azure fournit plusieurs nouvelles fonctionnalités :

- Dans l’Azure portal, sauvegarde d’Azure et les services de récupération de Site Azure sont combinées dans un coffre-fort de Services de récupération unique, afin que vous pouvez configurer et gérer la continuité d’activité et de reprise après sinistre (BCDR) à partir d’un emplacement unique. Un tableau de bord unifié vous permet de surveiller et de gérer des opérations sur vos sites locaux et le nuage public Azure.
- Les utilisateurs disposant d’un abonnement Azure mis en service avec le programme fournisseur de solutions de Cloud (CSP) peuvent désormais gérer les opérations de récupération de Site dans le portail Azure.


Après avoir lu cet article, valider les commentaires en bas dans les commentaires de Disqus. Poser des questions techniques sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Vue d’ensemble

Les organisations ont besoin d’une stratégie BCDR qui détermine comment les applications, les charges de travail et les données restent en cours d’exécution et disponible pendant les temps d’arrêt planifiés et non planifiés et récupérer dès que possible dans des conditions normales de fonctionnement. Votre stratégie de BCDR doit conserver les données d’entreprise d’et s’assurer que les charges de travail restent disponibles en permanence en cas de reprise après sinistre.

Récupération de site est un service Azure qui participe à votre stratégie de BCDR par l’orchestration de réplication de serveurs physiques des locaux et des ordinateurs virtuels vers le nuage (Azure) ou à un centre de données secondaire. Lorsque des pannes se produisent dans votre emplacement principal, vous basculent vers l’emplacement secondaire afin que les applications et charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’il est retourné à un fonctionnement normal. Pour en savoir plus [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md)

Cet article fournit toutes les informations dont vous avez besoin pour répliquer les VM Hyper-V dans des nuages VMM sur un site secondaire de VMM sur site. Il inclut une présentation de l’architecture, des informations et des étapes de déploiement pour la configuration des serveurs en local, les paramètres de réplication et planification de la capacité de planification. Une fois que vous avez configuré l’infrastructure que vous pouvez activer la réplication sur les ordinateurs que vous voulez protéger et vérifier que le basculement fonctionne.

## <a name="business-advantages"></a>Avantages commerciaux

- Récupération de site offre une protection pour les grosses charges de travail et les applications qui s’exécutent sur des ordinateurs virtuels Hyper-V en les répliquant sur un serveur secondaire de Hyper-V.
- Le portail de Services de récupération fournit un emplacement unique pour configurer, gérer et contrôler la réplication, de basculement et de restauration.
- Vous pouvez exécuter des basculements exécuter facilement votre infrastructure sur site primaire vers le site secondaire et le retour arrière (restauration) à partir du site secondaire vers le serveur principal.
- Vous pouvez configurer les plans de récupération avec plusieurs ordinateurs afin que les charges de travail applicatives hiérarchisé basculent ensemble.

## <a name="scenario-architecture"></a>Architecture de scénario

Voici les composants du scénario :

- **Site principal**: dans le site principal, il existe un ou plusieurs serveurs d’hôte Hyper-V exécute les ordinateurs virtuels de source que vous souhaitez répliquer. Ces serveurs hôte principal se trouvent dans un cloud privé de VMM.
- **Site secondaire**: dans le site secondaire, il existe un ou plusieurs serveurs hôtes Hyper-V exécute les ordinateurs virtuels de cible à laquelle vous répliquez des ordinateurs virtuels principales. Ces serveurs sont situés dans un cloud privé de VMM. Le nuage peut être sur le serveur principal (si vous avez uniquement un seul serveur VMM) ou sur un serveur secondaire de VMM.
- **Fournisseur**: lors du déploiement de la récupération de Site, vous installez le fournisseur de récupération de Site Azure sur les serveurs VMM et enregistrer ces serveurs dans un coffre-fort de Services de récupération. Le fournisseur qui s’exécute sur le serveur VMM communique avec récupération de Site via HTTPS 443 répliquer d’orchestration. Réplication des données se produit entre serveurs d’hôte Hyper-V principal et secondaire. Les données répliquées restent dans les réseaux et les sites local et ne sont pas envoyées vers Azure. Apprenez-en plus sur la [confidentialité](#privacy-information-for-site-recovery).

![Topologie de E2E](./media/site-recovery-vmm-to-vmm/architecture.png)

### <a name="data-privacy-overview"></a>Présentation de confidentialité des données

Le tableau suivant résume la façon dont les données sont stockées dans ce scénario :
****
Action | **Détails** | **Données collectées** | **Utilisation** | **Obligatoire**
--- | --- | --- | --- | ---
**Inscription** | Vous enregistrez un serveur VMM dans un coffre-fort de Services de récupération. Si vous décidez d’annuler l’inscription d’un serveur, vous pouvez le faire en supprimant les informations du serveur à partir du portail Azure. | Après avoir enregistré un serveur VMM récupération de Site collecte, processus et transfère les métadonnées sur le serveur VMM et les noms des nuages VMM détectés lors de la récupération de Site. | Les données sont utilisées pour identifier et communiquer avec le serveur VMM approprié et configurer les paramètres pour les nuages VMM appropriés. | Cette fonctionnalité est requise. Si vous ne souhaitez pas envoyer ces informations à la récupération de Site vous n’utilisez pas le service de récupération de Site.
**Activer la réplication** | Le fournisseur de récupération de Site Azure est installé sur le serveur VMM et est le canal d’échange pour la communication avec le service de récupération de Site. Le fournisseur est une bibliothèque de liens dynamiques (DLL) hébergée dans le processus VMM. Après avoir installé le fournisseur, la fonctionnalité de « Récupération de Datacenter » obtient activée dans la console Administrateur VMM. Nouveaux et existants de machines virtuelles peuvent activer cette fonctionnalité Activer la protection d’un ordinateur virtuel. | Avec cette propriété définie, le fournisseur envoie le nom et l’ID de la machine virtuelle à la récupération de Site.  La réplication est activée par Windows Server 2012 ou de Windows Server 2012 R2 Hyper-V Replica. Les machine virtuelle de données répliquées à partir d’un hôte Hyper-V à l’autre (généralement situé dans un centre de données différent de « récupération »). | Récupération de site utilise les métadonnées pour remplir les informations de mémoire virtuelle dans le portail Azure. | Cette fonctionnalité est une partie essentielle du service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations, ne pas activer la protection de récupération de Site pour les machines virtuelles. Notez que toutes les données envoyées par le fournisseur pour la récupération de Site sont envoyées via le protocole HTTPS.
**Plan de récupération** | Plans de vous aident à élaborer un plan d’orchestration pour le centre de récupération des données. Vous pouvez définir l’ordre dans lequel les ordinateurs virtuels ou un groupe de machines virtuelles doit être démarré sur le site de. Vous pouvez également spécifier des scripts automatisés pour être ou l’exécution manuelle de toute action à entreprendre, au moment de la récupération pour chaque machine virtuelle. Basculement sur incident est généralement déclenché au niveau du plan de récupération pour récupération coordonnée. | Récupération de site collecte, traite et transmet les métadonnées pour le plan de récupération, y compris la machine virtuelle et les métadonnées de tous les scripts d’automatisation et notes d’action manuelle. | Les métadonnées sont utilisées pour générer le plan de récupération dans le portail Azure. | Cette fonctionnalité est une partie essentielle du service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations à la récupération de Site, ne créez pas de plans de reprise.
**Mappage réseau** | Mappe les informations de réseau à partir du centre de données principal vers le centre de récupération des données. Lorsque les ordinateurs virtuels sont récupérés sur le site de reprise, mappage réseau permet d’établir la connectivité réseau. | Récupération de site collecte, traite et transmet les métadonnées des réseaux logiques pour chaque site (primaire et centre de données). | Les métadonnées sont utilisées pour remplir les paramètres réseau afin que vous pouvez mapper les informations réseau. | Cette fonctionnalité est une partie essentielle du service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations à la récupération de Site, n’utilisez pas de mappage réseau.
**Basculement sur incident (planifié/non planifié/essai)** | Basculement échoue sur les ordinateurs virtuels d’un centre de données gérées par VMM à l’autre. L’action de basculement sur incident est déclenchée manuellement dans le portail Azure. | Le fournisseur sur le serveur VMM est notifié de l’événement de basculement en récupération de Site et exécute une action de basculement sur incident sur l’hôte Hyper-V via des interfaces VMM. Basculement sur incident réel d’un ordinateur virtuel est d’un hôte Hyper-V à un autre et géré par Windows Server 2012 ou de Windows Server 2012 R2 Hyper-V Replica. Une fois le basculement terminé, le fournisseur sur le serveur VMM dans le centre de récupération des données envoie les informations de réussite pour la récupération de Site. | Récupération de site utilise les informations envoyées à remplir le statut de l’information d’action de basculement dans le portail Azure. | Cette fonctionnalité est une partie essentielle du service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations à la récupération de Site, n’utilisez pas de basculement.


## <a name="azure-prerequisites"></a>Conditions préalables Azure

Voici ce que vous devez dans Azure pour déployer ce scénario :

**Conditions préalables** | **Détails**
--- | ---
**Azure**| Vous avez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) . Vous pouvez démarrer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification de la récupération de Site.


## <a name="on-premises-prerequisites"></a>Conditions préalables du local

Voici ce que vous devez sur les sites principal et secondaire sur site pour le déploiement de ce scénario :

**Conditions préalables** | **Détails**
--- | ---
**VMM** | Nous vous recommandons de que déployer un serveur VMM sur le site principal et un serveur VMM sur le site secondaire.<br/><br/> Vous pouvez également [répliquer entre des clouds sur un seul serveur VMM](site-recovery-single-vmm.md). Pour ce faire, vous devez au moins deux clouds configurés sur le serveur VMM.<br/><br/> Serveurs VMM doivent être en cours d’exécution au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Chaque serveur VMM doit disposer d’un ou plusieurs nuages configurés et tous les nuages doivent avoir le profil de capacité de Hyper-V. <br/><br/>Nuages doivent contenir un ou plusieurs groupes d’hôtes VMM.<br/><br/>Pour en savoir plus sur la configuration des nuages VMM dans la [configuration de la structure du nuage VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), et [procédure pas à pas : création des nuages privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Serveurs VMM ont besoin d’un accès à internet.
**Hyper-V** | Serveurs Hyper-V doivent être en cours d’exécution au moins Windows Server 2012 avec le rôle Hyper-V et installé les dernières mises à jour.<br/><br/> Un serveur Hyper-V doit contenir les ordinateurs virtuels d’un ou plusieurs.<br/><br/>  Serveurs de hôte Hyper-V doivent être placés dans les groupes hôtes dans les nuages VMM principales et secondaires.<br/><br/> Si vous utilisez Hyper-V dans un cluster Windows Server 2012 R2, vous devez installer [la mise à jour de 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Si vous utilisez Hyper-V dans un cluster Windows Server 2012, notez que broker de cluster n’est pas créé automatiquement si vous disposez d’un cluster de base d’adresses IP statique. Vous devez configurer le service broker de cluster manuellement. [Pour en savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Fournisseur** | Lors du déploiement de la récupération de Site, vous installez le fournisseur de récupération de Site Azure sur les serveurs VMM. Le fournisseur communique avec récupération de Site sur 443 HTTPS pour orchestrer la réplication. Réplication de données a lieu entre les serveurs Hyper-V principales et secondaires sur le réseau local ou une connexion VPN.<br/><br/> Le fournisseur qui s’exécute sur le serveur VMM a besoin d’accéder à ces URL : *. hypervrecoverymanager.windowsazure.com ; *. AccessControl.Windows.NET ; *. backup.windowsazure.com ; *. BLOB.Core.Windows.NET ; *. store.core.windows.net.<br/><br/> En outre, autoriser la communication de pare-feu à partir des serveurs VMM pour les [plages IP de centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et autoriser le protocole HTTPS (443).

## <a name="prepare-for-deployment"></a>Préparer pour le déploiement

Pour vous préparer pour le déploiement, vous devez :

1. [Préparer le serveur VMM](#prepare-the-vmm-server) pour le déploiement de la récupération de Site.
2. [Préparation de mappage réseau](#prepare-for-network-mapping). Définir des réseaux pour que vous puissiez configurer mappage réseau.

### <a name="prepare-the-vmm-server"></a>Préparer le serveur VMM

Assurez-vous que le serveur VMM est conforme à la [Configuration requise](#on-premises-prerequisites) et qu’il peut accéder à l’URL listées.


### <a name="prepare-for-network-mapping"></a>Préparation de mappage réseau

Mappages de mappage réseau entre réseaux VMM VM sur les serveurs VMM principales et secondaires pour :

- Optimal, placez VMs de réplica sur les hôtes Hyper-V secondaires après le basculement.
- Connexion VM de réplica à des réseaux d’ordinateur virtuel appropriés.
- Si vous ne configurez pas les réplicas de mappage réseau qu'ordinateurs virtuels ne sont pas connectés à un réseau après le basculement.
- Si vous souhaitez configurer réseau lors de la récupération de Site de mappage déploiement ici est ce dont vous avez besoin :

    - Assurez-vous que les ordinateurs virtuels sur le serveur hôte de Hyper-V source sont connectés à un réseau de VMM VM. Ce réseau doit être lié à un réseau logique qui est associé avec le nuage.
    - Vérifiez que le nuage secondaire que vous utilisez pour la restauration possède un réseau de machine virtuelle correspondant configuré. Ce réseau de la machine virtuelle doit être lié à un réseau logique associé le nuage secondaire.

- [En savoir plus](site-recovery-network-mapping.md) sur le fonctionne du mappage réseau.

## <a name="prepare-for-deployment-with-a-single-vmm-server"></a>Préparer pour le déploiement avec un seul serveur VMM

Si vous n’avez qu’un seul serveur VMM vous pouvez répliquer des ordinateurs virtuels dans des hôtes Hyper-V dans le nuage VMM pour [Azure](site-recovery-vmm-to-azure.md) ou pour un nuage VMM secondaire. Nous vous recommandons de que la première option, car la réplication entre des clouds n’est pas transparente, mais si vous devez faire cela ici est ce que vous devez faire :

1. **Configurer VMM sur un ordinateur virtuel Hyper-V**. Lorsque nous avons pour cela nous vous suggérons de que vous peut-être co-implanter l’instance de SQL Server utilisée par VMM sur l’ordinateur virtuel même. Cela fait gagner du temps en seulement un ordinateur virtuel doit être créé. Si vous souhaitez utiliser une instance distante de SQL Server et une panne se produit, vous devez récupérer cette instance avant de pouvoir récupérer VMM.
2. **Assurez-vous que le serveur VMM a au moins deux clouds configurés**. Un nuage contient les ordinateurs virtuels vous souhaitez répliquer et autre nuage servira à l’emplacement secondaire. Le nuage qui contient les ordinateurs virtuels que vous souhaitez protéger doit être compatibles avec les [conditions requises](#on-premises-prerequisites).
3. Configurer la récupération de Site comme décrit dans cet article. Créer et inscrire le serveur VMM dans la chambre forte, définir une stratégie de réplication et activer la réplication. Vous devez spécifier que la réplication initiale s’effectue via le réseau.
4. Lorsque vous configurez le mappage réseau vous mappez le réseau de la machine virtuelle pour le nuage principal sur le réseau de la machine virtuelle pour le nuage secondaire.
5. Dans la console du Gestionnaire Hyper-V, activer Hyper-V Replica sur l’hôte Hyper-V qui contient la VM VMM et activer la réplication sur l’ordinateur virtuel. Assurez-vous que vous n’ajoutez pas la machine virtuelle VMM à nuages qui sont protégés par la restauration du Site, afin de garantir que les paramètres de Hyper-V Replica ne sont pas remplacés par la récupération de Site.
6. Si vous créez des plans de récupération pour le basculement, vous utilisez le même serveur VMM pour source et cible.
7. Basculement et la récupération comme suit :

    - Basculer manuellement le VMM VM sur le site secondaire à l’aide du Gestionnaire Hyper-V avec un basculement planifié.
    - Basculer vers les ordinateurs virtuels.
    - Une fois que le principal VMM VM a été récupéré, connexion au portail Azure -> Services de récupération de chambre forte et exécuter un basculement non planifié des ordinateurs virtuels à partir du site secondaire pour le site principal.
    - Une fois le basculement non planifié est terminée, toutes les ressources sont accessibles à partir du site principal à nouveau.


### <a name="create-a-recovery-services-vault"></a>Créer un coffre-fort de Services de récupération

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **gestion** > **Services de récupération**. Vous pouvez également cliquer sur **Parcourir** > **Services de récupération des** coffres-forts > **Ajouter**.

    ![Nouveau coffre-fort](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. Dans la zone **nom** , spécifiez un nom convivial pour identifier le coffre-fort. Si vous avez plus d’un abonnement, sélectionnez un d'entre eux.
4. [Créer un nouveau groupe de ressources](../resource-group-template-deploy-portal.md) ou sélectionnez-en un et spécifier une région Azure. Machines seront répliqués à cette région. Pour vérifier les régions pris en charge consultez disponibilité géographique dans les [Détails de la tarification de récupération de Site de Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si vous souhaitez accéder rapidement à la chambre forte du tableau de bord, cliquez sur **fixer au tableau de bord** > **créer coffre-fort**.

    ![Nouveau coffre-fort](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

Le nouveau coffre-fort s’affiche sur le **tableau de bord** > **toutes les ressources**et sur les principaux **Services de récupération des coffres-forts** lame.




## <a name="getting-started"></a>Mise en route

Récupération de site fournit une prise en main qui vous aide à déployer aussi rapidement que possible. Mise en route, vérifie les conditions préalables et vous guide tout au long des étapes de déploiement dans le bon ordre de récupération de Site.

Dans mise en route vous permet de sélectionner le type d’ordinateurs que vous souhaitez répliquer, et dans lequel vous souhaitez répliquer sur. Vous configurez des serveurs sur site, créez des règles de réplication et effectuez la planification de la capacité. Après que vous avez configuré votre infrastructure vous activez la réplication pour les machines virtuelles. Vous pouvez exécuter des basculements sur incident pour les machines spécifiques, ou créer des plans de récupération basculer sur plusieurs machines.

Commencer la mise en route, choisissez comment vous souhaitez déployer la récupération de Site. Le flux de mise en route change légèrement en fonction de vos besoins en matière de réplication.

## <a name="step-1-choose-your-protection-goal"></a>Étape 1 : Choisissez votre objectif de protection

Sélectionnez ce que vous voulez répliquer et dans lequel vous souhaitez répliquer sur.

1. La lame de **Services de récupération des coffres-forts** Sélectionnez votre coffre-fort et cliquez sur **paramètres**.
2. Dans les **paramètres** > **Mise en route** cliquez sur **Restauration du Site** > **étape 1 : préparation de l’Infrastructure** > **objectif de Protection**.
3. Dans **l’objectif de Protection** permet au **site de reprise**et sélectionnez **Oui, avec Hyper-V**.
4. Sélectionnez **Oui** pour indiquer que vous utilisez VMM pour gérer les hôtes Hyper-V, sélectionnez **Oui** si vous disposez d’un serveur secondaire de VMM. Si vous déployez la réplication entre des clouds sur un seul serveur VMM vous cliquez sur **non**. Puis cliquez sur **OK**.

    ![Choisissez des objectifs](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## <a name="step-2-set-up-the-source-environment"></a>Étape 2 : Configurer l’environnement source

Installez le fournisseur de récupération de Site Azure sur les serveurs VMM et enregistrer des serveurs dans la chambre forte.

1. Cliquez sur **l’étape 2 : préparer l’Infrastructure** > **Source**.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. Dans **source de préparation** cliquez sur **+ VMM** pour ajouter un serveur VMM.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. Dans le chèque de lame de **Add Server** **System Center VMM serveur** s’affiche dans le **type de serveur** et que le serveur VMM répond aux [conditions préalables et aux exigences des URL](#on-premises-prerequisites).
4. Téléchargez le fichier d’installation de fournisseur de récupération de Site Azure.
5. Télécharger la clé d’enregistrement. Vous devez cela lorsque vous exécutez le programme d’installation. La clé est valide pendant 5 jours une fois que vous le générez.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. Installez le fournisseur de récupération de Site Azure sur le serveur VMM.

> [AZURE.NOTE] Vous n’avez pas besoin de rien installer explicitement sur les serveurs hôtes Hyper-V.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurer le fournisseur de récupération de Site Azure

1. Exécutez le fournisseur de fichier d’installation sur chaque serveur VMM. Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois l’installer sur un nœud actif et terminer l’installation pour inscrire le serveur VMM dans la chambre forte. Puis, installez le fournisseur sur les autres nœuds. Les nœuds de cluster doivent exécuter la même version du fournisseur.
2. Le programme d’installation exécute quelques vérifications prerequirement et demande l’autorisation d’arrêter le service VMM. Le Service VMM sera redémarré automatiquement lorsque le programme d’installation se termine. Si vous installez sur un cluster VMM que vous serez invité à arrêter le rôle Cluster.

2.  Dans **Microsoft Update,** vous pouvez la choisir les mises à jour afin que les mises à jour du fournisseur sont installés en fonction de votre stratégie de Microsoft Update.
3. Dans **Installation** accepter ou modifier l’emplacement d’installation de fournisseur par défaut et cliquez sur **installer**.

    ![Emplacement d’installation](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. Après l’installation cliquez sur **Enregistrer** pour enregistrer le serveur dans la chambre forte.

    ![Emplacement d’installation](./media/site-recovery-vmm-to-vmm/provider-register.png)

9. Dans la zone **nom de coffre-fort**, vérifiez le nom du coffre-fort dans lequel le serveur est inscrit. Cliquez sur *suivant*.

    ![Inscription du serveur](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. **Connexion à Internet** de spécifier comment le fournisseur en cours d’exécution sur le serveur VMM se connecte à Internet. Sélectionnez **se connecter avec les paramètres de serveur proxy existants** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

    ![Paramètres Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

    - Si vous souhaitez utiliser un proxy personnalisé, que vous devez le configurer avant d’installer le fournisseur. Lorsque vous configurez les paramètres de proxy personnalisés, un test s’exécute pour vérifier la connexion du proxy.
    - Si vous utilisez un proxy personnalisé ou votre proxy par défaut requiert une authentification, que vous devez entrer les détails de proxy, y compris l’adresse proxy et le port.
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

    ![Serveur](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

11. Une fois que le serveur est disponible dans la console de récupération de Site, dans la **Source** > **préparation source** sélectionnez le serveur VMM, puis le nuage dans lequel se trouve l’hôte Hyper-V. Puis cliquez sur **OK**.

#### <a name="command-line-installation"></a>Installation de ligne de commande

Le fournisseur de récupération de Site Azure peut être installé à partir de la ligne de commande. Cette méthode peut être utilisée pour installer le fournisseur sur le Server Core de Windows Server 2012 R2.

1. Téléchargez la clé de l’enregistrement de fichiers et d’installation de fournisseur dans un dossier. Par exemple, C:\ASR.
2. Arrêtez-le System Center Virtual Machine Manager.
3. À partir d’une invite de commandes avec élévation de privilèges, exécutez ces commandes pour extraire le programme d’installation du fournisseur :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Exécutez cette commande pour installer le fournisseur :

        C:\ASR> setupdr.exe /i

5. Puis exécutez ces commandes pour inscrire le serveur dans le coffre-fort :

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Où les paramètres sont :

 - **/Credentials**: un paramètre obligatoire qui spécifie l’emplacement dans lequel se trouve le fichier de clé d’enregistrement  
 - **/FriendlyName**: un paramètre obligatoire pour le nom du serveur hôte Hyper-V qui s’affiche dans le portail de récupération de Site Azure.
 - **/EncryptionEnabled**: un paramètre facultatif que vous utilisez uniquement lors de la réplication à partir de VMM sur Azure.
 - **/ProxyAddress**: paramètre optionnel qui spécifie l’adresse du serveur proxy.
 - **/ProxyPort**: paramètre optionnel qui spécifie le port du serveur proxy.
 - **/proxyUsername**: paramètre optionnel qui spécifie le nom d’utilisateur Proxy (si le proxy exige une authentification).
 - **/proxyPassword**: paramètre optionnel qui spécifie le mot de passe pour l’authentification avec le serveur proxy (si le proxy exige une authentification).  

## <a name="step-3-set-up-the-target-environment"></a>Étape 3 : Définir l’environnement cible

Sélectionnez le serveur VMM de cible et le nuage.

1. Cliquez sur **préparer infrastructure** > **cible** et sélectionnez le serveur VMM cible que vous souhaitez utiliser.
2.  Nuages qui sont synchronisés avec la récupération de Site sur le serveur seront affiche. Sélectionnez le nuage cible.

    ![Cible](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="step-4-set-up-replication-settings"></a>Étape 4 : Définir des paramètres de réplication

1. Pour créer une nouvelle réplication de stratégie, cliquez sur **préparer infrastructure** > **Les paramètres de réplication** > **+ créer et associer**.

    ![Réseau](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. **Créer** et associer stratégie, spécifiez un nom de stratégie. Le type de la source et la cible doit être **Hyper-V**.
3. Dans la **version d’hôte Hyper-V** permet de sélectionner le système d’exploitation est en cours d’exécution sur l’hôte.

    > [AZURE.NOTE] Le nuage VMM peut contenir des hôtes Hyper-V qui exécutent différentes versions (pris en charge) de Windows Server, mais une stratégie de réplication est appliqués hôtes exécutant le même système d’exploitation. Si vous avez des hôtes exécutant plus de version d’un système d’exploitation puis créer des stratégies de réplication séparée.

4. Dans **type d’authentification** et de **l’authentification du port** , spécifiez comment le trafic est authentifié entre les serveurs de l’hôte Hyper-V principaux et de restauration. Sélectionnez le **certificat** , sauf si vous avez un environnement Kerberos qui fonctionne. Récupération de Site Azure pour configurer automatiquement des certificats pour l’authentification HTTPS. Vous n’avez pas besoin de faire quoi que ce soit manuellement. Par défaut, port 8083 et 8084 (pour les certificats) s’ouvre dans le pare-feu Windows sur les serveurs hôtes Hyper-V. Si vous n’activez pas **Kerberos**, vous servira un ticket Kerberos pour l’authentification mutuelle des serveurs hôtes. Notez que ce paramètre n’est pertinent que pour les serveurs hôte Hyper-V dans Windows Server 2012 R2.
3. Dans la zone **Copier de fréquence** , spécifiez la fréquence à laquelle vous souhaitez répliquer les données du delta après la réplication initiale (toutes les 30 secondes, 5 ou 15 minutes).
4. Dans la **rétention de point de récupération**, spécifier en heures combien de temps la fenêtre de rétention pour chaque point de récupération. Ordinateurs protégés peuvent être récupérés à n’importe quel point dans une fenêtre.
6. Dans **fréquence des snapshots cohérents au niveau de l’application** , spécifiez la fréquence (de 1 à 12 heures) des points de récupération contenant des snapshots cohérents avec l’application sera créé. Hyper-V utilise deux types de captures instantanées, une capture instantanée standard qui fournit un instantané incrémentiel de la machine virtuelle complète et une capture instantanée cohérente de l’application qui prend un instantané de point-à-temps des données de l’application à l’intérieur de l’ordinateur virtuel. Des snapshots cohérents avec l’application utilisent Volume Shadow Copy Service (VSS) pour s’assurer que les applications sont dans un état cohérent lorsque l’instantané est pris. Notez que si vous activez des snapshots cohérents avec l’application, elle affectera les performances des applications s’exécutant sur des machines virtuelles de source. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.
7. **Compression de transfert de données** de spécifier si les données répliquées transférées doivent être compressées.
8. Sélectionnez **Supprimer le réplica VM** pour spécifier que la machine virtuelle de réplicas doivent être supprimée si vous désactivez la protection de la source de machine virtuelle. Si vous activez ce paramètre, lorsque vous désactivez la protection de la source de VM, il est supprimé de la console de récupération de Site, les paramètres de récupération de Site pour VMM sont supprimés à partir de la console, et le réplica est supprimé.
3. Dans **la méthode de réplication initiale** si vous utilisez la réplication sur le réseau spécifier si vous souhaitez démarrer la réplication initiale ou la planifier. Pour économiser la bande passante du réseau, vous pouvez souhaiter le planifier en dehors de vos heures de disponibilité. Puis cliquez sur **OK**.

    ![Stratégie de réplication](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. Lorsque vous créez une nouvelle stratégie, il est automatiquement associé le nuage VMM. Dans **la stratégie de réplication** , cliquez sur **OK**. Vous pouvez associer des nuages VMM supplémentaires (et les ordinateurs virtuels dans les) dans les **paramètres de**cette stratégie de réplication > **réplication** > nom de la stratégie > **Associer un nuage VMM**.

    ![Stratégie de réplication](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### <a name="prepare-for-offline-initial-replication"></a>Préparation pour la réplication initiale en mode hors connexion

Vous pouvez effectuer la réplication en mode hors connexion pour la copie de données initiales. Vous pouvez le préparer comme suit :

- Sur le serveur source, indiquez un emplacement de chemin d’accès à partir de laquelle l’exportation de données aura lieu. Attribuer le contrôle total des autorisations NTFS et de partage au service VMM sur le chemin d’exportation. Sur le serveur cible, indiquez un emplacement de chemin d’accès à partir duquel l’importation de données aura lieu. Affecter les mêmes autorisations sur ce chemin d’accès d’importation.
- Si le chemin d’accès d’importation ou d’exportation est partagée, attribuer l’appartenance au groupe administrateur, utilisateur avec pouvoir, opérateur d’impression ou opérateurs de serveur pour le compte de service VMM sur l’ordinateur distant sur lequel le partagé se trouve.
- Si vous utilisez des comptes d’exécuter en tant que pour ajouter des hôtes, sur les chemins d’accès d’importation et d’exportation, affecter en lecture et en écriture à l’exécuter en tant que comptes dans VMM.
- Les actions d’importation et d’exportation ne doivent pas être trouver sur tout ordinateur utilisé comme serveur hôte Hyper-V, car la configuration de bouclage n’est pas pris en charge par Hyper-V.
- Dans Active Directory, sur chaque Hyper-V server hôte qui contient les ordinateurs virtuels que vous souhaitez protéger, d’activer et de configurer la délégation contrainte pour autoriser les ordinateurs à distance sur laquelle les chemins d’accès d’importation et d’exportation sont situés, comme suit :
    1. Sur le contrôleur de domaine, ouvrez **Active Directory utilisateurs et ordinateurs**.
    2. Dans l’arborescence de la console, cliquez sur **nom_domaine** > **ordinateurs**.
    3. Cliquez sur le nom du serveur hôte Hyper-V > **Propriétés**.
    4. Sous l’onglet **délégation** , cliquez sur **Approuver cet ordinateur pour la délégation aux services spécifiés**.
    5. Cliquez sur **utiliser tout protocole d’authentification**.
    6. Cliquez sur **Ajouter des** > **les utilisateurs et les ordinateurs**.
    7. Tapez le nom de l’ordinateur qui héberge le chemin d’exportation > **OK**. Dans la liste des services disponibles, maintenez la touche CTRL enfoncée et cliquez sur **cifs** > **OK**. Répétez ces étapes pour le nom de l’ordinateur qui héberge le chemin d’accès d’importation. Répétez cette procédure pour les autres serveurs d’hôte Hyper-V.


### <a name="configure-network-mapping"></a>Configurer le mappage réseau

Configurez le mappage réseau entre les réseaux source et cible.

- Le fait de [lecture](#prepare-for-network-mapping) pour un aperçu rapide du mappage réseau. En plus de [lire](site-recovery-network-mapping.md) pour une explication plus approfondie.
- Vérifiez que les ordinateurs virtuels sur les serveurs VMM sont connectés à un réseau de la machine virtuelle.

Configurer le mappage comme suit :

1. **Paramètres** > **Infrastructure de restauration de Site** > **Mappage du réseau** > **mappages réseau** cliquez sur **+ le mappage réseau**.

    ![Mappage réseau](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Dans le **mappage réseau d’Ajouter** onglet Sélectionner la source et cible les serveurs VMM. Les réseaux de machine virtuelle associés les serveurs VMM sont récupérés.
3. **Réseau Source**, sélectionnez le réseau que vous souhaitez utiliser à partir de la liste des réseaux d’ordinateur virtuel associé au serveur VMM principal.
6. Permet de sélectionner le réseau que vous souhaitez utiliser sur le serveur VMM secondaire dans le **réseau cible** . Puis cliquez sur **OK**.

    ![Mappage réseau](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Voici ce qui se produit lorsque le mappage réseau commence :

- N’importe quel réplica des machines virtuelles existantes qui correspondent au réseau de la machine virtuelle source sera être connectés au réseau de la machine virtuelle cible.
- De nouvelles machines virtuelles qui sont connectés au réseau de la machine virtuelle source va être connectés au réseau cible mappé après la réplication.
- Si vous modifiez un mappage avec un réseau existant, les machines virtuelles de réplica sera connectés avec les nouveaux paramètres.
- Si le réseau cible a plusieurs sous-réseaux et de ces sous-réseaux a le même nom que le sous-réseau sur lequel se trouve l’ordinateur virtuel source, l’ordinateur virtuel de réplica sera connecté à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, l’ordinateur virtuel va être connecté pour le premier sous-réseau dans le réseau.

### <a name="configure-storage-mapping"></a>Configurer le mappage du stockage
Par défaut lorsque vous répliquez un ordinateur virtuel sur un serveur hôte de Hyper-V source sur un serveur hôte de Hyper-V cible, les données répliquées sont stockées dans l’emplacement par défaut qui est indiqué pour l’hôte Hyper-V de cible dans le Gestionnaire Hyper-V. Pour plus de contrôle sur lequel sont stockées les données répliquées, vous pouvez configurer le mappage de stockage<br/><br/> Pour configurer un mappage de stockage, vous devez paramétrer des classifications de stockage sur la source et la cible des serveurs VMM avant de commencer le déploiement. Actuellement la mappage de stockage via le nouveau portail Azure n’est pas pris en charge. Toutefois, elle peut être activée via Powershell. [En savoir plus](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-6-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Étape 5 : Planification de la capacité

Maintenant que vous avez votre basic infrastructure configuration vous pouvez réfléchir à la planification de la capacité et déterminer si vous avez besoin de ressources supplémentaires.

Récupération de site fournit un planificateur de capacité de type Excel pour vous aider à allouer les ressources de votre environnement source, composants de restauration de site, mise en réseau et de stockage. Vous pouvez exécuter le Planificateur de mode rapide pour les estimations basées sur un nombre moyen de machines virtuelles, des disques et du stockage, ou en mode détaillé, dans laquelle vous allez saisir les chiffres du niveau de charge de travail. Avant de commencer à vous devrez :

- Rassembler des informations sur votre environnement de réplication, y compris les machines virtuelles, des disques par les ordinateurs virtuels et stockage par disque.
- Estimer le taux de modification (évolution du) quotidiennes, que vous aurez de données répliquées delta. Vous pouvez utiliser le [Planificateur de capacités pour Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) pour vous aider à effectuer cette opération.

1.  Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. [Lire l’article](site-recovery-capacity-planner.md) accompagnant l’outil.
2.  Lorsque vous avez terminé de **vous avez exécuté le Planificateur de capacités**, sélectionnez **Oui** ?

    ![Planification de la capacité](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="control-bandwidth"></a>Bande passante de contrôle

Une fois que vous avez collecté des informations sur la réplication delta en temps réel à l’aide de la capacité de Gestionnaire Hyper-V Replica, l’outil basé sur Excel de capacity planner vous aide à calculer la bande passante que nécessaire pour la réplication (initial et delta). Pour contrôler la quantité de bande passante utilisée pour la réplication, vous pouvez configurer la stratégie de NetQos à l’aide de la stratégie de groupe ou Windows PowerShell. Il existe plusieurs moyens pour cela :

**PowerShell** | **Détails**
--- | ---
**Nouveau - NetQosPolicy « QoS au sous-réseau de destination »** | Limiter le trafic en provenance d’un hôte Hyper-V exécute Windows Server 2012 R2 à un sous-réseau secondaire. Utilisez si vos sous-réseaux principal et secondaire est différentes.
**Nouveau - NetQosPolicy « QoS au port de destination »** | Limiter le trafic en provenance d’un hôte Hyper-V exécute Windows Server 2012 R2 à un port de destination.
**Nouvelle - NetQosPolicy « Le trafic de limitation de VMM »** | Limiter le trafic à partir de vmms.exe. Cela accélèrera le trafic de Hyper-V et le trafic de Migration Live. Vous pouvez faire correspondre les protocoles IP et des ports pour affiner.

Vous pouvez utiliser les paramètres de poids de la bande passante ou limiter le trafic par bits / secondaire. Si vous utilisez un cluster, que vous devez effectuer cette opération sur tous les nœuds du cluster. Pour plus d’informations, consultez :


- Blog de Thomas Maurer sur [La limitation du trafic de duplication de Hyper-V](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)
- Informations supplémentaires sur l' [applet de commande New-NetQosPolicy](https://technet.microsoft.com/library/hh967468.aspx).


## <a name="step-6-enable-replication"></a>Étape 6 : Réplication d’activer

Activer la réplication comme suit :

1. Cliquez sur **étape 2 : application de répliquer** > **Source**. Une fois que vous avez activé la réplication pour la première fois, vous cliquez sur **+ répliquer** dans le coffre-fort pour activer la réplication pour d’autres ordinateurs.

    ![Activer la réplication](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. De la lame de la **Source** > sélectionnez le serveur VMM et le nuage dans lequel se trouvent les hôtes Hyper-V que vous souhaitez répliquer. Puis cliquez sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. Dans la **cible** de lame, vérifiez le serveur secondaire de VMM et du cloud.
4. Dans des **machines virtuelles** permet de sélectionner les ordinateurs virtuels que vous souhaitez protéger dans la liste.

    ![Activer la protection de l’ordinateur virtuel](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Vous pouvez suivre la progression de l’action **d’Activation de la Protection** dans les paramètres > **travaux** > **tâches de récupération de Site**. Après l’exécution de la tâche de **Protection de finaliser** l’ordinateur virtuel est prêt pour le basculement.


>[AZURE.NOTE] Vous pouvez également activer la protection des machines virtuelles dans la console VMM. Cliquez sur **Activer la Protection** dans la barre d’outils dans les propriétés de la machine virtuelle > onglet **Récupération de Site Azure** .

Après avoir activé la réplication pour afficher les propriétés de la machine virtuelle dans les **paramètres** > **Les éléments répliqués** > nom de la machine virtuelle. Sur le tableau de bord **Essentials** , vous trouverez des informations sur la stratégie de réplication pour la machine virtuelle et son état. Pour plus de détails, cliquez sur **Propriétés** .

### <a name="onboard-existing-virtual-machines"></a>Intégré des ordinateurs virtuels existants

Si vous avez des ordinateurs virtuels existants dans VMM qui répliquent avec Hyper-V Replica vous pouvez intégrée leur protection de récupération de Site Azure comme suit :

1. Assurez-vous que le serveur Hyper-V qui héberge l’ordinateur virtuel existant se trouve dans le nuage principal et que le serveur Hyper-V qui héberge l’ordinateur virtuel de réplica se trouve dans le nuage secondaire.
2. Assurez-vous que la stratégie de réplication est configurée pour le nuage VMM principal.
2. Activer la réplication de l’ordinateur virtuel principal. Azure de reprise et de VMM garantit que le même hôte pour la duplication et la machine virtuelle est détecté et reprise Azure va réutiliser et de rétablir la réplication à l’aide des paramètres spécifiés.


## <a name="step-7-test-your-deployment"></a>Étape 7 : Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération qui contient un ou plusieurs ordinateurs virtuels.

### <a name="prepare-for-failover"></a>Préparer pour le basculement

- Pour tester votre déploiement, vous avez besoin d’une infrastructure pour la machine répliquée à fonctionner comme prévu. Si vous souhaitez tester Active Directory et DNS, vous pouvez créer un ordinateur virtuel comme un contrôleur de domaine dans DNS et répliquer ces paramètres sur Azure à l’aide de la récupération de Site Azure. Lire en plusieurs [Considérations de basculement test pour Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Les instructions de cet article décrivent comment faire pour exécuter un test de basculement avec aucun réseau. Cette option va tester que la machine virtuelle bascule mais ne tester les paramètres de réseau de la machine virtuelle. [En savoir plus](site-recovery-failover.md#run-a-test-failover) sur les autres options.
- Si vous souhaitez exécuter un basculement non planifié et non d’un test de basculement, notez les points suivants :

    - Si possible vous devez arrêter les ordinateurs principales avant d’exécuter un basculement non planifié. Cela garantit que vous n’avez pas les ordinateurs source et de réplica en cours d’exécution en même temps.
    - Lorsque vous exécutez un basculement non planifié il cesse de réplication des données à partir d’ordinateurs principales afin que n’importe quel delta de données ne seront pas transférée après qu’un basculement non planifié commence. En outre si vous exécutez un basculement non planifié sur un plan de récupération il s’exécutera jusqu'à la fin, même si une erreur se produit.




### <a name="run-a-test-failover"></a>Exécutez un test de basculement

1. Basculer sur un ordinateur virtuel unique dans les **paramètres** > **Répliquées des éléments**, cliquez sur la machine virtuelle > **+ basculement de Test**.

    ![Basculement de test](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. Pour basculer d’un plan de récupération, dans les **paramètres** > de**Plans de reprise**, droit le plan > **Test de basculement**. Pour créer une récupération envisagez de [suivre ces instructions](site-recovery-create-recovery-plans.md).
2. Dans le **Test de basculement**, sélectionnez **Aucun**. Avec cette option, vous testez bascule de la machine virtuelle comme prévu, mais de la machine virtuelle répliquée n’est pas connectée à un réseau.

    ![Basculement de test](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. Cliquez sur **OK** pour lancer le basculement. Vous pouvez suivre la progression en cliquant sur la machine virtuelle pour ouvrir ses propriétés ou sur le projet de **Test de basculement** dans les **paramètres** > **travaux** > **tâches de récupération de Site**.
4. Lorsque le travail de basculement atteint la phase **de test terminée** , effectuez les opérations suivantes :

    -  Permet d’afficher le réplica de machine virtuelle dans le nuage VMM secondaire.
    -  Cliquez sur **Terminer le test** pour terminer le test de basculement.
    -  Cliquez sur **Notes** pour enregistrer et enregistrer les observations associées avec le basculement de test.

5. La machine virtuelle de test sera créée sur le même hôte que l’hôte sur lequel la machine virtuelle de réplica existe. Il est ajouté dans le même nuage dans lequel se trouve l’ordinateur virtuel de réplica.
6. Après avoir vérifié que démarrage de machines virtuelles avec succès sur **le basculement de test est terminé**. À ce stade, tous les éléments créés automatiquement lors de la récupération de Site pendant le basculement du test sont supprimés.  

    > [AZURE.NOTE] Si un basculement sur incident test continue de plus de deux semaines, il est rempli en vigueur.



### <a name="update-dns-with-the-replica-vm-ip-address"></a>Mettre à jour le DNS avec l’adresse IP de la VM de réplica

Une fois le basculement de la machine virtuelle de réplica n’est peut-être pas la même adresse IP que l’ordinateur virtuel principal.

- Machines virtuelles met à jour le serveur DNS qu’ils utilisent après leur démarrage.
- Vous pouvez aussi manuellement mettre à jour DNS comme suit :

#### <a name="retrieve-the-ip-address"></a>Récupérer l’adresse IP

Exécuter cet exemple de script pour récupérer l’adresse IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="update-dns"></a>Mise à jour DNS

Exécuter cet exemple de script pour mettre à jour le DNS, en spécifiant l’adresse IP que vous avez récupéré à l’aide de l’exemple de script précédent.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>Étapes suivantes

Une fois votre déploiement est configuré et en cours d’exécution, [en savoir plus](site-recovery-failover.md) sur les différents types de basculement.
