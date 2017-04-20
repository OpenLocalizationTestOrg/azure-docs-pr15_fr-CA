
<properties
    pageTitle="Récupération de Site Azure : Réplication Hyper-V virtual machines sur un seul serveur VMM | Microsoft Azure"
    description="Cet article décrit comment faire pour répliquer des ordinateurs virtuels Hyper-V lorsque vous disposez d’un seul serveur VMM."
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
    ms.workload="backup-recovery"
    ms.date="08/24/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-on-a-single-vmm-server"></a>Répliquer les machines virtuelles de Hyper-V sur un seul serveur VMM

Lisez cet article pour savoir comment répliquer des ordinateurs virtuels de Hyper-V que qui se trouve sur un serveur hôte de Hyper-V dans un nuage VMM lorsque vous disposez d’un seul serveur VMM dans votre déploiement.

Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et l’utilisation des ressources : le Gestionnaire de ressources Azure et classique. Azure dispose également de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement. Cet article contient des instructions pour la configuration de la réplication dans le portail Azure.


Si vous avez des questions après avoir lu cet article, les valider dans les commentaires Disqus en bas de cet article ou sur le [forum des Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Si vous souhaitez répliquer des ordinateurs virtuels Hyper-V en situés sur les hôtes Hyper-V dans VMM et n’avoir qu’un seul serveur VMM, vous pouvez [répliquer vers Azure](site-recovery-vmm-to-azure.md), ou entre des clouds sur le serveur VMM.

Nous vous recommandons de répliquer vers Azure car le basculement et la restauration ne sont pas transparentes lors de la réplication entre des clouds, et un certain nombre d’étapes manuelles est nécessaires. Si vous ne souhaitez pas répliquer à l’aide d’uniquement le serveur VMM, vous pouvez effectuer les opérations suivantes :

- Répliquer avec le serveur VMM autonome unique
- Répliquer avec un seul serveur VMM déployé dans un cluster Windows étiré


## <a name="replicate-across-sites-with-a-single-standalone-vmm-server"></a>Réplication entre les sites avec un serveur VMM autonome unique

![Serveur VMM virtuel autonome](./media/site-recovery-single-vmm/single-vmm-standalone.png)

Dans ce scénario vous déployez le serveur VMM unique sous la forme d’un ordinateur virtuel dans le site principal et répliquez cet ordinateur virtuel vers un site secondaire à l’aide de Hyper-V Replica et la récupération de Site.

1. Configurer VMM sur un ordinateur virtuel Hyper-V. Nous vous recommandons de qu'installer l’instance de SQL Server utilisée par VMM sur l’ordinateur virtuel même pour gagner du temps. Si vous souhaitez utiliser une instance distante de SQL Server et une panne se produit, vous devez récupérer cette instance avant de pouvoir récupérer VMM.
2. Assurez-vous qu’au moins deux clouds configurés sur le serveur VMM. Un nuage contient les ordinateurs virtuels vous souhaitez répliquer et autre le nuage sert à l’emplacement secondaire. Le nuage qui contient les ordinateurs virtuels que vous souhaitez protéger doit avoir :

    - Un ou plusieurs groupes hôtes VMM qui contient un ou plusieurs des serveurs hôtes Hyper-V dans chaque groupe hôte.
    - Au moins un ordinateur virtuel Hyper-V sur chaque hôte de serveur Hyper-V.

3. Créer un coffre-fort de Services de récupération, générer et télécharger une clé d’enregistrement de coffre-fort et inscrire le serveur VMM dans la chambre forte. Lors de l’inscription, vous installez le fournisseur de récupération de Site Azure sur le serveur VMM.
4. Configurer un ou plusieurs nuages sur la VM VMM et ajoutez les hôtes Hyper-V pour ces nuages.
3. Configurer les paramètres de protection pour les nuages. Vous spécifiez le nom du serveur VMM unique en tant que les emplacements source et cible. Pour configurer le mappage réseau, vous mappez le réseau de la machine virtuelle pour le nuage avec les ordinateurs virtuels que vous souhaitez protéger, sur le réseau de la machine virtuelle pour le nuage de réplication.
4. Activer la réplication initiale pour les machines virtuelles que vous souhaitez protéger sur le réseau, car les deux nuages se trouvent sur le même serveur.
4. Dans la console du Gestionnaire Hyper-V, activer Hyper-V Replica sur l’hôte Hyper-V qui contient la VM VMM et activer la réplication sur l’ordinateur virtuel. Assurez-vous que vous n’ajoutez pas la VM VMM pour les nuages qui sont protégés par la restauration du Site. Cela garantit que les paramètres de Hyper-V Replica ne sont pas remplacés par la récupération de Site.
5. Si vous souhaitez créer des plans de récupération, vous spécifiez le même serveur VMM pour source et cible.

Suivez les instructions [cet article](site-recovery-vmm-to-vmm.md) pour créer un coffre-fort, inscrire le serveur et configurer la protection.

### <a name="what-to-do-in-an-outage"></a>Que faire dans une panne

Si une panne complète se produit et que vous avez besoin fonctionner à partir du site secondaire, effectuez les opérations suivantes :

1.  Dans la console du Gestionnaire Hyper-V dans le site secondaire, exécutez un basculement non planifié pour basculer sur la VM VMM à partir de la primaire vers le site secondaire.
2.  Vérifiez que la VM VMM est en cours d’exécution sur le site secondaire.
3.  Dans le coffre-fort de Services de récupération, exécutez un basculement non planifié pour basculer de la charge de travail ordinateurs virtuels à partir de l’ordinateur principal à nuages secondaires. Pour effectuer le basculement non planifié des ordinateurs virtuels, valider le basculement sur incident, ou sélectionnez un point de restauration différent comme requis.
4.  Après que le basculement non planifié est terminée, les utilisateurs peuvent accéder aux ressources de la charge de travail sur le site secondaire.

Lorsque le site principal fonctionne normalement à nouveau, effectuez les opérations suivantes :

1.  Dans la console du Gestionnaire Hyper-V, activer la réplication inverse de VMM VM, démarrer répliquant depuis secondaire vers le principal.
2.  Dans la console du Gestionnaire Hyper-V, exécuter un basculement planifié pour une restauration de la VM VMM pour le site principal. Valider le basculement pour la terminer. Activez ensuite la réplication inverse lancer la réplication VMM du primaire au secondaire.
3.  Dans le coffre-fort de Services de restauration, activez la réplication inverse pour les machines virtuelles, pour démarrer les répliquer à partir de secondaire principal de la charge de travail.
4.  Dans le coffre-fort de Services de récupération, exécutez un basculement planifié pour une restauration de la charge d’ordinateurs virtuels sur le site principal. Valider le basculement pour la terminer. Activez ensuite la réplication inverse lancer la réplication des machines virtuelles de la charge de travail du principal sur le site secondaire.



## <a name="replicate-across-sites-with-a-single-vmm-server-in-a-stretched-cluster"></a>Réplication entre les sites avec un seul serveur VMM dans un cluster étiré

![Cluster de serveur VMM virtuel](./media/site-recovery-single-vmm/single-vmm-cluster.png)

Au lieu de déployer un serveur VMM autonome comme un ordinateur virtuel qui réplique vers un site secondaire, vous pouvez rendre VMM hautement disponible en le déployant comme une machine virtuelle dans un cluster de basculement Windows. Cela fournit la résilience de la charge de travail et de protection contre les pannes de matériel. Pour déployer avec récupération de Site le VMM VM doit être déployée dans un cluster étendu entre des sites géographiquement distincts. Pour ce faire :

1. Installation de VMM sur un ordinateur virtuel dans un cluster de basculement Windows et sélectionnez l’option pour exécuter le serveur comme étant hautement disponible lors de l’installation.
2. L’instance de SQL Server qui est utilisée par VMM doit être répliquée avec groupes de disponibilité AlwaysOn de SQL Server afin qu’il existe un réplica de la base de données du site secondaire.
3. Suivez les instructions [cet article](site-recovery-vmm-to-vmm.md) pour créer un coffre-fort, inscrire le serveur et configurer la protection. Vous devez enregistrer chaque serveur VMM du cluster dans le coffre-fort de Services de récupération. Pour ce faire, vous installez le fournisseur sur un nœud actif et enregistrez le serveur VMM. Puis, vous installez le fournisseur sur les autres nœuds.

### <a name="what-to-do-in-an-outage"></a>Que faire dans une panne

En cas de panne, le serveur VMM sa base de données SQL Server correspondant a basculé et sont accessibles à partir du site secondaire.


## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-vmm-to-vmm.md) sur le déploiement de Site récupération détaillée pour VMM à la réplication de VMM.
