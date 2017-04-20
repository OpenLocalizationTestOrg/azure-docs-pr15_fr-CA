<properties
    pageTitle="Surveiller et résoudre les problèmes de protection pour les ordinateurs virtuels et les serveurs physiques | Microsoft Auzre" 
    description="Récupération de Site Azure coordonne la réplication, le basculement et la restauration des machines virtuelles situées sur des serveurs locaux pour Azure ou un centre de données secondaire. Cet article permet de surveiller et de résoudre les problèmes de protection de VMM ou Site de Hyper-V." 
    services="site-recovery" 
    documentationCenter="" 
    authors="anbacker" 
    manager="mkjain" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/13/2016"    
    ms.author="rajanaki"/>
    
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Surveiller et résoudre les problèmes de protection pour les ordinateurs virtuels et les serveurs physiques

Cette analyse et le Guide de dépannage vous permet d’apprendre le suivi de l’état de santé de la réplication et les techniques de dépannage pour la récupération de Site Azure.

## <a name="understanding-the-components"></a>Compréhension des composants

### <a name="vmwarephysical-site-deployment-for-replication-between-on-premises-and-azure"></a>Déploiement de Site VMware/physiques pour la réplication entre des locaux et d’Azure.
Pour configurer DR entre la machine de VMware/physique sur site ; Configuration, forme de base cible et processus Server doit être configuré. Lors de l’activation de la protection pour le serveur source Azure récupération de Site va installer les services de mobilité. Publication sur site arrêt une fois que le serveur source bascule sur Azure, besoins des clients vous permet de configurer un serveur de traitement dans Azure et un serveur cible de maître sur site afin de protéger le serveur source à reconstruit sur site. 

![Déploiement de Site VMware/physiques pour la réplication entre les locaux et Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises-site"></a>Déploiement de Site VMM pour la réplication entre le site local.

Dans le cadre de la configuration de reprise après sinistre entre l’emplacement des deux sur site ; Fournisseur de récupération de Site Azure doit être téléchargé et installé sur le serveur VMM. Fournisseur a besoin de connectivité internet pour s’assurer que toutes les opérations qui sont déclenchées à partir du portail Azure obtient traduite sur site telles que les opérations activer la protection, arrêt côté primaire virtual machines dans le cadre des basculements, etc..

![Déploiement de Site VMM pour la réplication entre le site local](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises--azure"></a>Déploiement de Site VMM pour la réplication entre des locaux et d’Azure.

Dans le cadre de la configuration de reprise après sinistre entre local & Azure ; Fournisseur de récupération de Site Azure doit être téléchargé et installé sur le serveur VMM et Agent des Services de récupération Azure qui doit être installé sur chaque hôte Hyper-V. Pour plus d’informations, reportez-vous à la rubrique [Site de présentation à la Protection d’Azure](./site-recovery-understanding-site-to-azure-protection.md) .

![Déploiement de Site VMM pour la réplication entre les locaux et Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises--azure"></a>Déploiement de Site Hyper-V pour la réplication entre les locaux et Azure

C’est le même que celui du déploiement de VMM – seule la différence est le fournisseur et l’Agent est installée sur l’hôte Hyper-V. Pour plus d’informations, reportez-vous à la rubrique [Site de présentation à la Protection d’Azure](./site-recovery-understanding-site-to-azure-protection.md) .

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Surveiller les opérations de configuration, de protection et de restauration

Chaque opération de récupération automatique du système obtient d’audit et de suivi sous l’onglet « Travaux ». Dans le cas d’une configuration, la protection ou l’erreur de récupération, accédez à l’onglet tâches et voir s’il existe des échecs.

![Surveiller les opérations de configuration, de protection et de restauration](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Une fois que vous trouvez les échecs dans la vue tâches, sélectionnez la tâche, puis cliquez sur Détails de l’erreur pour cette tâche.

![Surveiller les opérations de configuration, de protection et de restauration](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Les détails des erreurs vous aidera à identifier la cause et la recommandation pour le même problème.

![Surveiller les opérations de configuration, de protection et de restauration](media/site-recovery-monitoring-and-troubleshooting/image5.png)

Dans le cas ci-dessus semble avoir une autre opération qui est en cours, en raison de laquelle Échec de configuration de Protection. Veiller à résoudre le problème conformément à la recommandation – là après avoir cliquez sur redémarrez votre pour relancer l’opération.

![Surveiller les opérations de configuration, de protection et de restauration](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Option de redémarrage n’est pas disponible pour toutes les opérations – pour ceux qui n’a pas l’option de redémarrage naviguer en arrière vers l’objet et de refaire l’opération une fois de plus. Chaque tâche peut être annulé à tout moment lors de l’utilisation du bouton Annuler en cours.

![Surveiller les opérations de configuration, de protection et de restauration](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machine"></a>Surveiller l’état de réplication pour la machine virtuelle

Fournisseur de récupération automatique du système central et surveillance via le portail Azure pour chacune des entités protégées à distance. Accédez aux éléments protégés après y sélectionner des nuages VMM ou des groupes de PROTECTION. Onglet de nuages VMM est uniquement pour les déploiements de VMM de base et tous les autres scénarios ont les entités protégées sous l’onglet groupes de PROTECTION.

![Surveiller l’état de réplication pour la machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Après il y sélectionner l’entité protégée sous le nuage respectif ou le groupe de protection. Une fois que vous sélectionnez l’entité de toutes protégée autorisée opérations sont affichées dans le volet inférieur.

![Surveiller l’état de réplication pour la machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Comme indiqué ci-dessus au cas où l’ordinateur virtuel que santé est critique, vous pouvez cliquer sur le bouton Détails de l’erreur sur le bas pour afficher l’erreur. Basé sur les « causes Possible » et « Recommandation » mentionnée résoudre le problème.

![Surveiller l’état de réplication pour la machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Surveiller l’état de réplication pour la machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Remarque : S’il y a des opérations actives qui sont en cours ou échec puis accédez à l’affichage de tâches comme indiqué plus haut pour afficher l’erreur spécifique du projet.

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Résoudre les problèmes de Hyper-V sur site

Se connecter à la console du Gestionnaire Hyper-V sur site, sélectionnez l’ordinateur virtuel et voir l’état de santé de la réplication.

![Résoudre les problèmes de Hyper-V sur site](media/site-recovery-monitoring-and-troubleshooting/image12.png)

Dans ce cas, *Fonctionnement de la réplication* est en cours indiqué comme critique : *Santé de la réplication de vue* pour afficher les détails.

![Résoudre les problèmes de Hyper-V sur site](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Pour les cas où la réplication est suspendue pour la machine virtuelle, sélectionnez *réplication*avec le bouton droit->*reprise de la réplication*
![dépannage sur site des problèmes de Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image19.png)

En cas de machine virtuelle migre un nouvel hôte Hyper-V (à l’intérieur du cluster ou un ordinateur autonome), qui a été configuré par le biais de récupération automatique du système, la réplication de l’ordinateur virtuel n’est pas affectée. Assurez-vous que le nouvel hôte Hyper-V répond à tous les par-requis et qu’il est configuré à l’aide de la récupération automatique du système.

### <a name="event-log"></a>Journal des événements

| Sources d’événements                | Détails                                                                                                                                                                                           |
|-------------------------  |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| **Applications et Service journaux/Microsoft/VirtualMachineManager/serveur/Admin** (Serveur VMM)   |  Cela permet d’enregistrement utile pour le dépannage de nombreux autres facteurs VMM. |
| **Les applications et les journaux de Service/MicrosoftAzureRecoveryServices/réplication** (Hôte hyper-V)   | Ainsi, journalisation utile pour résoudre des problèmes d’Agent des Services de récupération Microsoft Azure nombreux. <br/> ![Source d’événement pour l’hôte Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Applications et services Site de journaux/Microsoft/Azure récupération fournisseur/opérationnels** (Hôte hyper-V)   | Ainsi, journalisation utile pour le dépannage de nombreux problèmes de Service de récupération de Site Microsoft Azure. <br/> ![Source d’événement pour l’hôte Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Applications et Service journaux/Microsoft/Windows/Hyper-V à VMM/Admin** (Hôte hyper-V) | Ainsi, l’enregistrement utile pour résoudre de nombreux problèmes de gestion d’ordinateur virtuel Hyper-V. <br/> ![Source d’événement pour l’hôte Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### <a name="hyper-v-replication-logging-options"></a>Options de journalisation de réplication de Hyper-V

Tous les événements se rapportant à Hyper-V Replica sont consignés dans Hyper-V à VMM\\Admin journal situé sous **journaux des Applications et Services\\Microsoft\\Windows**. En outre, un journal d’analyse peut être activé pour Hyper-V à VMM. Pour activer ce journal, tout d’abord afficher les journaux d’analyse et de débogage dans l’Observateur d’événements. Ouvrir l’Observateur d’événements, puis dans le **menu Affichage**, cliquez sur **Afficher les analyse et les journaux de débogage**.

![Résoudre les problèmes de Hyper-V sur site](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Un journal d’analyse est visible sous Hyper-V à VMM

![Résoudre les problèmes de Hyper-V sur site](media/site-recovery-monitoring-and-troubleshooting/image15.png)

Dans le volet **Actions** , cliquez sur **Activer le journal**. Une fois activé, il apparaît dans **L’Analyseur de performances** comme une Session de suivi d’événements est situé sous **des ensembles de collecteurs de données.**

![Résoudre les problèmes de Hyper-V sur site](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Pour afficher les informations collectées, tout d’abord arrêter la session de suivi en désactivant le journal, puis enregistrer le journal et ouvrez-le à nouveau dans l’Observateur d’événements ou utiliser d’autres outils pour la convertir comme vous le souhaitez.



## <a name="reaching-out-for-microsoft-support"></a>Approcher pour Support Microsoft

### <a name="log-collection"></a>Collection de journaux

Pour la protection du Site de VMM, reportez-vous à la rubrique de la [Collection de journaux de récupération automatique du système à l’aide d’outil de prise en charge des Diagnostics plate-forme (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) pour collecter les journaux requis.

Pour la protection de Site d’Hyper-V, téléchargez l' [outil](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) et l’exécuter sur l’hôte Hyper-V pour collecter les journaux.

Pour les scénarios de VMware/physiques, reportez-vous à la section [Collection de journaux de récupération de Site Azure pour la protection du site VMware et physique](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) pour collecter les journaux requis.

Outil de collecte les fichiers journaux localement dans un sous-dossier nommé de façon aléatoire sous **%LocalAppData%\ElevatedDiagnostics**

![Exemples de procédures de protection du site Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="opening-a-support-ticket"></a>Ouverture d’un ticket de support

Pour déclencher le ticket de support pour ASR, atteindre Azure prend en charge l’URL à <http://aka.ms/getazuresupport>

## <a name="kb-articles"></a>Articles de la base de connaissances

-   [Comment faire pour conserver la lettre de lecteur pour les machines virtuelles protégées qui sont basculés ou migrés vers Azure](http://support.microsoft.com/kb/3031135)
-   [Comment faire pour gérer sur site pour l’utilisation de la bande passante du réseau protection Azure](https://support.microsoft.com/kb/3056159)
-   [ASR : erreur « la ressource de cluster n’a pas pu être trouvée » lorsque vous essayez d’activer la protection d’un ordinateur virtuel](http://support.microsoft.com/kb/3010979)
-   [Comprendre et résoudre les problèmes de Hyper-V Replica Guide](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## <a name="common-asr-errors-and-their-resolutions"></a>Erreurs de récupération automatique du système courantes et leurs solutions

Vous trouverez ci-dessous les erreurs courantes que vous pouvez atteindre et leur résolution. Chacun de l’erreur est documenté dans une autre page WIKI.

### <a name="general"></a>Général
-   <span style="color:green;">Nouveau</span> Tâches [échoue avec l’erreur « une opération est en cours. » Erreur 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">Nouveau</span> Travaux de [échoue avec l’erreur « Le serveur n’est pas connecté à Internet ». Erreur 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Programme d’installation
-   [Le serveur VMM ne peut pas être enregistré en raison d’une erreur interne. Reportez-vous à la vue des tâches dans le portail de récupération de Site pour plus de détails sur l’erreur. Exécutez le programme d’installation à nouveau pour inscrire le serveur.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [Une connexion ne peut pas être établie vers la chambre forte de Hyper-V Recovery Manager. Vérifiez les paramètres de proxy ou réessayez ultérieurement.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Configuration de
-   [Impossible de créer le groupe de Protection : une erreur s’est produite lors de la récupération de la liste des serveurs.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Cluster d’hôte Hyper-V contient au moins une carte réseau statique, ou aucune carte connecté n’est configurées pour utiliser DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM n’a pas les autorisations nécessaires pour exécuter une action](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [Impossible de sélectionner le compte de stockage dans l’abonnement lors de la configuration de la protection](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Protection
- <span style="color:green;">Nouveau</span> Échec de [activation de la Protection avec l’erreur « Protection n’a pas pu être configurée pour l’ordinateur virtuel ». Erreur 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">Nouveau</span> Échec de [activation de la Protection avec l’erreur « Protection n’a pas pu être activée pour l’ordinateur virtuel ». Erreur 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">Nouveau</span> Erreur de migration en direct [23848 - l’ordinateur virtuel va être déplacé à l’aide de type Live. Ceci risque de perturber l’état de protection de reprise de la machine virtuelle.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [Activer la protection a échoué, car l’Agent pas installé sur l’ordinateur hôte](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [Impossible de trouver un hôte approprié pour l’ordinateur virtuel de réplica - en raison de la faible ressources de calcul](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [Un hôte approprié pour l’ordinateur virtuel de réplica ne peut pas être trouvé - en raison d’aucune logique rattaché au réseau](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [Impossible de se connecter sur l’ordinateur hôte du réplica - aucune connexion n’a pu être établie](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### <a name="recovery"></a>Récupération
- VMM ne peut pas terminer l’opération de l’hôte :
    -   [Basculer vers le point de récupération sélectionné pour la machine virtuelle : erreur accès général refusé.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [Impossible de basculer vers le point de récupération sélectionné pour la machine virtuelle Hyper-V : opération abandonnée essayez d’un point de récupération plus récent. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   Une connexion avec le serveur n’a pas pu être établie (0x00002EFD)
        -   [Impossible d’activer la réplication inverse pour la machine virtuelle de Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Hyper-V n’a pas pu activer la réplication pour la machine virtuelle de machine virtuelle](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [Basculement de la machine virtuelle n’a pas pu être valider](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [Le plan de récupération contient les ordinateurs virtuels qui ne sont pas prêtes pour basculement planifié](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [La machine virtuelle n’est pas prête pour un basculement planifié](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [Machine virtuelle n’est pas en cours d’exécution et qu’il n’est pas éteint](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [La sortie de l’opération de bande s’est produite sur une machine virtuelle et le basculement de validation a échoué](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Tester le basculement
    -   [Basculement sur incident n’a pas pu démarrer car le basculement de test est en cours](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
-   <span style="color:green;">Nouveau</span>  Basculement sur incident arrive à expiration avec 'task PreFailoverWorkflow WaitForScriptExecutionTaskTimeout' en raison des paramètres de configuration sur le groupe de sécurité de réseau associé à l’ordinateur virtuel ou le sous-réseau auquel appartient l’ordinateur. Pour plus d’informations, reportez-vous à [« Tâche de PreFailoverWorkflow WaitForScriptExecutionTaskTimeout »](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) .


### <a name="configuration-server-process-server-master-target"></a>Configuration serveur, serveur de traitement, Masque cible
Configuration serveur (CS), processus (PS), forme de base cible (MT)
-   [L’hôte ESXi sur lequel le PS/SC est hébergé comme une machine virtuelle échoue avec un écran violet de la mort.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Bureau à distance, dépannage après basculement
-   De nombreux clients ont dû faire face les problèmes pour vous connecter à l’échec de sur une machine virtuelle dans Azure. [Utiliser le document de dépannage à RDP dans la machine virtuelle](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Ajout d’une adresse IP publique sur un ordinateur virtuel du Gestionnaire des ressources
Si le bouton **se connecter** sur le portail est grisé et vous n’êtes pas connecté à Azure via une gamme de Express ou une connexion VPN de Site à Site, vous devez créer et attribuer à votre machine virtuelle une adresse IP publique avant de pouvoir utiliser RDP/SSH. Suivez les étapes ci-dessous pour ajouter une adresse IP publique sur l’interface du réseau de l’ordinateur virtuel.  

![Échec de l’ajout d’une adresse IP publique sur l’interface réseau de sur machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)