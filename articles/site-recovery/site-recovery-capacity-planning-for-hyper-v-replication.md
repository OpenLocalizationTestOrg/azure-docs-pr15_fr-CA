<properties
    pageTitle="Exécutez l’outil de planification de capacité de Hyper-V pour la récupération de Site | Microsoft Azure"
    description="Cet article contient des instructions sur l’utilisation de l’outil de planification de capacité de Hyper-V pour la récupération de Site Azure"
    services="site-recovery"
    documentationCenter="na"
    authors="rayne-wiselman"
    manager="jwhit"
    editor="" />
<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/12/2016"
    ms.author="raynew" />

# <a name="run-the-hyper-v-capacity-planner-tool-for-site-recovery"></a>Exécutez l’outil de planification de capacité de Hyper-V pour la récupération de Site

Dans le cadre de votre déploiement d’Azure récupération de Site, vous devrez déterminer votre réplication et les besoins en bande passante. L’outil Planificateur de capacité de Hyper-V pour la récupération de Site vous aide à déterminer vos besoins de réplication et de la bande passante pour la réplication d’ordinateur virtuel Hyper-V.


Cet article décrit comment faire pour exécuter l’outil de planification de capacité de Hyper-V. Cet outil doit être utilisé avec les autres outils de planification de capacité et les informations décrites dans la [planification de la capacité de récupération de Site](site-recovery-capacity-planner.md).


## <a name="before-you-start"></a>Avant de commencer

Vous exécutez l’outil sur un nœud de serveur ou un cluster Hyper-V sur votre site principal. Pour exécuter l’outil, les serveurs hôtes Hyper-V doit :

- Système d’exploitation : Windows Server® 2012 ou R2 de Windows Server® 2012
- Mémoire : 20 Mo (minimum)
- Du processeur : surcharge de 5 pour cent (minimum)
- Espace disque : 5 Mo (minimum)

Avant d’exécuter l’outil, vous devez préparer le site principal. Si vous utilisez la réplication entre deux locaux sites et vous souhaitez vérifier la bande passante, vous aurez besoin préparer un serveur réplica ainsi.


## <a name="step-1-prepare-the-primary-site"></a>Étape 1 : Préparer le site principal
1. Dressez la liste de tous les ordinateurs virtuels Hyper-V à répliquer et les hôtes/clusters Hyper-V sur lequel ils se trouvent sur le site principal. L’outil peut s’exécuter chaque fois pour plusieurs hôtes autonomes, ou pour un seul cluster, mais pas les deux ensemble. Il doit également exécuter séparément pour chaque système d’exploitation, donc vous devez rassembler et Notez vos serveurs Hyper-V comme suit :

  - Serveurs autonomes Windows Server® 2012
  - Clusters Windows Server® 2012
  - Serveurs autonomes Windows Server® 2012 R2
  - Les clusters Windows Server® 2012 R2

3. Activer l’accès à l’infrastructure WMI à distance sur tous les hôtes Hyper-V et les clusters. Exécutez cette commande sur chaque serveur/cluster pour rendre des règles de pare-feu qu’et les autorisations de l’utilisateur sont définies :

        netsh firewall set service RemoteAdmin enable

5. Activer l’analyse des performances sur les serveurs et les clusters, comme suit :

  - Ouvrez le pare-feu Windows avec le composant logiciel enfichable de **Sécurité avancée** et activer les règles de trafic entrant suivantes : **Accès réseau COM + (DCOM-IN)** et toutes les règles dans le **groupe de gestion à distance de journal des événements**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Étape 2 : Préparer un serveur réplica (sur site pour la réplication en local)

Vous n’avez pas besoin de le faire si vous effectuez une réplication vers Azure.

Nous vous recommandons de que configurer un seul hôte Hyper-V sous la forme d’un serveur de récupération afin qu’une machine virtuelle factice pour en vérifier la bande passante peut être répliquée.  Vous pouvez ignorer cette étape, mais vous ne pourrez pas mesurer la bande passante, sauf si vous le faites.

1. Si vous souhaitez utiliser un nœud de cluster comme le réplica configurer broker d’Hyper-V Replica :

    - Dans le **Gestionnaire de serveur**, ouvrez le **Gestionnaire du Cluster de basculement**.
    - Connectez-vous au cluster, sélectionnez le nom du cluster, puis cliquez sur **Actions** > **Configurer le rôle** pour ouvrir l’Assistant haute disponibilité.
    - **Sélectionnez** le rôle, cliquez sur **Broker de réplica de Hyper-V**. Dans l’Assistant, indiquez un **nom NetBIOS** et **l’adresse IP** à utiliser comme point de connexion au cluster (appelé point d’accès client). Le **Service Broker de réplica de Hyper-V** sera configuré, ce qui donne un nom de point d’accès client que vous devez noter.
    - Vérifiez que le rôle Hyper-V réplica Broker est fourni en ligne et peut basculer entre tous les nœuds du cluster. Pour ce faire, cliquez avec le bouton droit sur le rôle, pointez sur **déplacer**, puis cliquez sur **Sélectionner un noeud**. Sélectionnez un nœud > **OK**.
    - Si vous utilisez l’authentification basée sur des certificats, assurez-vous que chaque nœud du cluster et le client ont tous le certificat installé de point d’accès.
2.  Activer un serveur de réplication :

    - Pour un cluster à ouvrir le Gestionnaire de Cluster d’échec, connectez-vous au cluster et cliquez sur **rôles** > sélectionnez rôle > **Réplication paramètre**s > **Activer ce cluster comme un serveur de réplicas**. Notez que si vous utilisez un cluster en tant que le réplica vous devez avoir le rôle Hyper-V réplica Broker présent sur le cluster dans le site principal.
    - Pour un serveur autonome, ouvrez le Gestionnaire Hyper-V. Dans le volet **Actions** , cliquez sur **Les paramètres de Hyper-V** pour le serveur que vous souhaitez activer et configurer la **Réplication** , cliquez sur **Activer cet ordinateur comme un serveur de réplicas**.
3. Configurer l’authentification :

    - Dans **ports d’authentification et** Sélectionnez comment authentifier le serveur principal et les ports d’authentification. Si vous utilisez un certificat cliquez sur **Sélectionner un certificat** pour en sélectionner un. Utiliser Kerberos si les hôtes Hyper-V principaux et de restauration sont dans le même domaine ou de domaines approuvés. Utiliser des certificats pour des domaines différents ou un déploiement de groupe de travail.
    - Dans la section **d’autorisation et de stockage** , permettent à **n’importe quel** serveur authentifié (principal) envoyer des données de réplication pour ce serveur de réplicas. Cliquez sur **OK** ou sur **Appliquer**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

    - Exécutez **netsh http afficher servicestate** pour vérifier que l’écouteur est en cours d’exécution pour le protocole/port spécifié :  
4. Configurer des pare-feux. Lors de l’installation de Hyper-V, les règles de pare-feu sont créées pour autoriser le trafic sur la valeur par défaut de ports (HTTPS sur 443, Kerberos sur 80). Activer ces règles comme suit :

        - Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
        - Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
        - Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
        - Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## <a name="step-3-run-the-capacity-planner-tool"></a>Étape 3 : Exécution de l’outil capacity planner

Une fois que vous avez préparé votre site principal et un serveur de récupération, vous pouvez exécuter l’outil.

1. [Téléchargez](https://www.microsoft.com/download/details.aspx?id=39057) l’outil à partir du Microsoft Download Center.
2. Exécutez l’outil à partir d’un des serveurs principaux (ou un des nœuds du cluster principal). Cliquez sur le fichier .exe et puis cliquez sur **Exécuter en tant qu’administrateur**.
3. **Avant de commencer** spécifier combien de temps vous souhaitez collecter des données. Nous vous recommandons de qu'exécuter l’outil pendant les heures de production à garantir que les données sont représentatif. Si vous essayez uniquement valider la connectivité de réseau, vous pouvez collecter pendant une minute seulement.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. Dans **les détails du site principal** spécifier le nom du serveur ou le nom de domaine complet d’un hôte autonome, ou pour un cluster de spécifier le nom de domaine complet du client accepte n’importe quel nœud du cluster, nom du cluster ou point et puis cliquez sur **suivant**. L’outil détecte automatiquement le nom du serveur, sur qu'il est en cours d’exécution. L’outil prélève des machines virtuelles qui peuvent être surveillés pour les serveurs spécifiés.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. Dans **Les détails du Site réplique** les si vous effectuez une réplication vers Azure, ou si vous utilisez la réplication pour un centre de données secondaire et que vous n’avez pas défini un serveur réplica, sélectionnez **Ignorer les tests impliquant le site répliqué**. Si vous effectuez une réplication vers un centre de données secondaire et que vous avez configuré un type de réplica dans le nom de domaine complet du serveur autonome ou le point d’accès client pour le cluster dans **nom du serveur (ou) CAP de Broker de réplica de Hyper-V**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. Dans **l’Étendue des détails de réplica** activer **Ignorer les tests impliquant le site répliqué d’étendue**. Ils ne sont pas pris en charge par la récupération de Site.
7. Dans **Choisir les ordinateurs virtuels à répliquer** les outils se connecte au serveur ou au cluster et affiche des ordinateurs virtuels et les disques en cours d’exécution sur le serveur principal, selon les paramètres vous avez spécifié sur la page de **Détails du Site principal** . Notez que les ordinateurs virtuels qui sont déjà activés pour la réplication, ou qui ne sont pas en cours d’exécution ne s’affiche. Sélectionnez les ordinateurs virtuels pour lesquels vous voulez collecter des mesures. Sélection automatique des disques durs virtuels et collecte les données trop pour les ordinateurs virtuels.
9. Si vous avez configuré un serveur de réplication ou un cluster, les informations **réseau** spécifier la bande passante WAN approximative vous pensez sera utilisé entre les sites principal et le réplica et sélectionnez les certificats que si vous avez configuré l’authentification par certificat.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. Dans **Résumé des** paramètres et cliquez sur **suivant** pour commencer la collecte de métriques. État et la progression de l’outil s’affiche sur la page de **Calculer la capacité** . Lorsque la fin d’outil en cours d’exécution cliquez sur **Afficher le rapport** pour passer en revue la sortie. Par défaut, des rapports et journaux sont stockés dans **%systemdrive%\Users\Public\Documents\Capacity agent de planification**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## <a name="step-4-interpret-the-results"></a>Étape 4 : Interpréter les résultats
Voici les mesures importantes. Vous pouvez ignorer les mesures qui ne sont pas répertoriés ici. Elles ne sont pas pertinentes pour la récupération de Site.

### <a name="on-premises-to-on-premises-replication"></a>Sur le site pour la réplication locale
  - Impact de la réplication sur le calcul de l’hôte principal, mémoire
  - Impact de la réplication sur le principal, espace disque de stockage d’hôtes de récupération, e/s
  - Bande passante totale requise pour la réplication delta (Mbits/s)
  - Bande passante observée entre l’hôte principal et l’hôte de restauration (Mbits/s)
  - Suggestion pour le nombre idéal d’actives transferts parallèles entre deux hôtes/clusters

### <a name="on-premises-to-azure-replication"></a>Sur le site de réplication Azure
  - Impact de la réplication sur le calcul de l’hôte principal, mémoire
  - Impact de la réplication sur l’espace disque de stockage de l’hôte principal, Ops ES/s
  - Bande passante totale requise pour la réplication delta (Mbits/s)

## <a name="more-resources"></a>Plus de ressources

- Pour plus d’informations sur l’outil, lisez le document qui accompagne le téléchargement de l’outil.
- Suivre une procédure pas à pas de l’outil sur le [blog TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx)de Keith Mayer.
- [Obtenir les résultats](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) de notre test de performance pour sur site pour la réplication locale Hyper-V



## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez terminé la planification de la capacité, vous pouvez démarrer restauration du Site de déploiement :

- [Répliquer les machines virtuelles de Hyper-V dans les nuages VMM vers Azure](site-recovery-vmm-to-azure.md)
- [Répliquer les machines virtuelles de Hyper-V (sans VMM) pour Azure](site-recovery-hyper-v-site-to-azure.md)
- [Répliquer des ordinateurs virtuels Hyper-V entre les sites VMM](site-recovery-vmm-to-vmm.md)
- [Répliquer des ordinateurs virtuels Hyper-V entre les sites VMM avec SAN](site-recovery-vmm-san.md)
- [Répliquer hyper-V ordinateurs virtuels sur un seul serveur VMM](site-recovery-single-vmm.md)