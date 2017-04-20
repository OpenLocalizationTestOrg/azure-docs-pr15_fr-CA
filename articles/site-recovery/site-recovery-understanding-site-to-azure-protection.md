<properties
    pageTitle="Réplication de Hyper-V avec récupération de Site Azure | Microsoft Azure"
    description="Cet article permet de comprendre les concepts techniques qui vous aideront à installer, configurer et gérer la récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="mkjain"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/12/2016"
    ms.author="rajanaki"/>  


# <a name="hyper-v-replication-with-azure-site-recovery"></a>Réplication de Hyper-V avec récupération de Site Azure

Cet article décrit les concepts de techniques qui peuvent vous aider à configurer et gérer un site de Hyper-V ou System Center Virtual Machine Manager (VMM) pour Azure protection à l’aide de récupération de Site Azure.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>Configuration de l’environnement source pour la réplication entre les locaux et Azure

Dans le cadre de la configuration de reprise après sinistre entre locaux et Azure, veillez à télécharger et installer le fournisseur de récupération Azure Site sur le serveur VMM. Installer l’Agent de récupération de Services Azure sur chaque hôte Hyper-V.

![Déploiement de site VMM pour la réplication entre les locaux et Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Configuration de l’environnement source dans une infrastructure gérée de Hyper-V est similaire à la configuration de l’environnement source dans une infrastructure gérée de VMM. La seule différence est que le fournisseur et l’agent sont installés sur l’hôte Hyper-V. Dans l’environnement de VMM, le fournisseur est installé sur le serveur VMM et l’agent est installé sur les hôtes Hyper-V (dans le cas de la réplication Azure).

## <a name="workflows"></a>Flux de travail

### <a name="enable-protection"></a>Activer la protection
Après avoir protégé un ordinateur virtuel à partir du portail Azure ou sur site, un travail de récupération de Site nommé **Activer la protection** démarre. Vous pouvez le surveiller sous l’onglet **tâches** .

![« Activer la protection » de travail dans la liste des tâches](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

La tâche **Activer la protection** vérifie les conditions préalables avant d’appeler la méthode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) . Cette méthode crée une réplication vers Azure à l’aide des entrées qui sont configurées au cours de protection.

La tâche **Activer la protection** démarre la réplication initiale à partir de locaux en appelant la méthode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) . Cette méthode envoie des disques virtuels de la machine virtuelle vers Azure.

![Détails de la tâche « Activer la protection »](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>Finalisation de la protection sur l’ordinateur virtuel
Prise d’un [instantané d’ordinateur virtuel Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) lors du déclenchée de la réplication initiale. Disques durs virtuels sont traités un par un jusqu'à ce que tous les disques sont téléchargés vers Azure. Cette procédure prend généralement un certain temps, en fonction de la taille du disque et la bande passante. Pour optimiser l’utilisation de votre réseau, voir [comment gérer sur site pour l’utilisation de la bande passante du réseau protection Azure](https://support.microsoft.com/kb/3056159).

Une fois la première réplication terminée, la tâche de **protection Finalize sur l’ordinateur virtuel** configure les paramètres réseau et après la réplication. Lors de la réplication initiale est en cours :

- Toutes les modifications apportées aux disques sont suivies. 
- Stockage de disque supplémentaire est utilisé pour la capture instantanée et les fichiers journaux de réplica de Hyper-V (HRL).

À la fin de la réplication initiale, l’instantané d’ordinateur virtuel Hyper-V est supprimé. Cette suppression des résultats de la fusion des modifications apportées aux données après la réplication initiale au disque parent.

![« Finaliser la protection sur l’ordinateur virtuel » de tâche dans la liste des tâches](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Réplication delta
Suivi de réplication des réplicas de Hyper-V, qui fait partie du moteur de réplication de réplicas Hyper-V, les modifications des pistes sur un disque dur virtuel en tant que fichiers de journal de réplica de Hyper-V (*.hrl). Fichiers HRL sont dans le même répertoire que les disques associés.

Chaque disque qui est configurée pour la réplication a un fichier HRL associé. Ce journal est envoyé à un compte de stockage du client après que la réplication initiale est terminée. Lorsque le journal est en transit vers Azure, les modifications dans le serveur principal sont suivies dans un autre fichier journal dans le même répertoire.

Au cours de la réplication initiale ou delta, vous pouvez surveiller la santé de réplication de machine virtuelle dans la vue de la machine virtuelle, comme mentionné dans la [santé de la réplication moniteur de machine virtuelle](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).  

### <a name="resynchronization"></a>Resynchronisation
Un ordinateur virtuel est marqué pour la resynchronisation lors de l’échec de la réplication delta et de réplication initiale complet est coûteuse en termes de bande passante du réseau ou l’heure. Par exemple, lorsque la taille du fichier HRL accumule jusqu'à 50 % de la taille totale du disque, l’ordinateur virtuel est marqué pour la resynchronisation. Resynchronisation réduit la quantité de données envoyées sur le réseau par le calcul des totaux de contrôle des machine virtuelle les disques source et cible et en l’envoyant uniquement sur l’écart.

Une fois la resynchronisation terminée, la réplication delta normale doit se poursuivre. Vous pouvez reprendre la resynchronisation si une panne de réseau ou d’une autre panne se produit.

Par défaut, planifiée automatiquement de resynchronisation est configurée pour se produire en dehors des heures de travail. Si l’ordinateur virtuel doit être resynchronisé manuellement, sélectionnez l’ordinateur virtuel à partir du portail, puis cliquez sur **resynchroniser**.

![Resynchronisation manuelle](media/site-recovery-understanding-site-to-azure-protection/image04.png)

Resynchronisation utilise un algorithme de segmentation des blocs fixes où les fichiers source et cible sont divisées en blocs fixes. Les totaux de contrôle pour chaque segment est générées et puis comparés afin de déterminer les blocs à partir de la source doivent être appliquée à la cible.

### <a name="retry-logic"></a>Réessayer la logique
Il est logique de relance intégrée pour les erreurs de réplication. Cette logique peut être classée en deux catégories :

| Catégorie                  | Scénarios                                    |
|---------------------------|----------------------------------------------|
| Erreur non récupérable     | Aucune nouvelle ne tentative. État de réplication d’ordinateur virtuel est **critique**et l’intervention de l’administrateur est requise. Voici quelques exemples : <ul><li>Rompre la chaîne VHD</li><li>État non valide pour l’ordinateur virtuel de réplica</li><li>Erreur d’authentification de réseau</li><li>Erreur d’autorisation</li><li>Ordinateur virtuel qui n’est pas trouvé, dans le cas d’un serveur autonome Hyper-V</li></ul>|
| Erreur récupérable         | Nouvelles tentatives produisent à chaque intervalle de réplication, à l’aide un exponentielle recul qui augmente l’intervalle avant nouvelle tentative du début de la première tentative (1, 2, 4, 8, 10 minutes). Si l’erreur persiste, recommencez toutes les 30 minutes. Voici quelques exemples : <ul><li>Erreur réseau</li><li>Espace disque insuffisant</li><li>Condition de mémoire insuffisante</li></ul>|

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Cycle de vie de protection et de restauration ordinateur virtuel Hyper-V

![Cycle de vie de protection et de restauration ordinateur virtuel Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>Autres références

- [Surveiller et résoudre les problèmes de protection de VMware, VMM, Hyper-V et physiques des sites](./site-recovery-monitoring-and-troubleshooting.md)
- [Approcher pour Support Microsoft](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Erreurs de récupération de Site Azure courantes et leurs solutions](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)
