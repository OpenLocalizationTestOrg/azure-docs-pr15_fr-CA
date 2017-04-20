<properties
    pageTitle="Résultats de test de performance et l’échelle pour sur site à la réplication de Hyper-V sur site avec restauration du Site | Microsoft Azure"
    description="Cet article fournit des informations sur le test de performance pour les locaux à la réplication en local à l’aide de la récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/06/2016"
    ms.author="raynew"/>

# <a name="performance-test-and-scale-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>Performances, tester et mettre à l’échelle des résultats pour les locaux à la réplication de Hyper-V sur site avec récupération de Site

Récupération de Site Microsoft Azure vous permet d’organiser et de gérer la réplication des machines virtuelles et des serveurs physiques vers Azure, ou à un centre de données secondaire. Cet article fournit les résultats de tests de performances que nous l’avons fait lors de la réplication entre deux ordinateurs virtuels Hyper-V sur site centres de données.



## <a name="overview"></a>Vue d’ensemble

L’objectif des tests était d’examiner le fonctionnement de la récupération de Site Azure lors de la réplication de l’état stable. État stable la réplication se produit lorsque les ordinateurs virtuels ont terminé la réplication initiale et synchronisent les modifications delta. Il est important de mesurer les performances à l’aide de l’état stable, car il est l’état dans lequel la plupart des machines virtuelles reste, sauf si des pannes inattendues se produisent.


Le déploiement de test se composait de deux sites sur site avec un serveur VMM dans chaque site. Ce déploiement de test est typique d’un déploiement de siège office/succursales, dont le siège agissant en tant que le site principal et le bureau de succursale en tant que le site secondaire ou la récupération.

### <a name="what-we-did"></a>Ce que nous avons fait

Voici ce que nous n’a dans le test réussi :

1. Permet de créer des machines virtuelles à l’aide de modèles VMM.

1. Démarrage de machines virtuelles et les métriques de performances de base capture plus de 12 heures.

1. Nuages créées sur les serveurs VMM principaux et de restauration.

1. Protection de nuage configuré dans la récupération de Site d’Azure, y compris le mappage des nuages de source et de la restauration.

1. Activé la protection pour les machines virtuelles et leur permettre de réplication initiale complet.

1. A attendu quelques heures pour la stabilisation du système.

1. Capturées sur 12 heures, des mesures de performances garantissant que tous les ordinateurs virtuels est resté dans un état de la réplication pendant les 12 heures.

1. Mesurer le delta entre les métriques de performances de base et les métriques de performances de réplication.

## <a name="test-deployment-results"></a>Résultats des tests de déploiement

### <a name="primary-server-performance"></a>Performances du serveur principal

- Hyper-V Replica asynchrone assure le suivi des modifications dans un fichier journal avec une charge minimale de stockage sur le serveur principal.

- Hyper-V Replica utilise le cache de la mémoire de mise à jour automatique pour réduire les frais Ops ES/s pour le suivi. Il stocke les écrit dans la VHDX dans la mémoire et les vide dans le fichier journal avant l’heure de l’envoi du journal pour le site de reprise. Un vidage de disque se produit également si les écritures atteint un seuil prédéterminé.

- Le graphique ci-dessous montre la charge d’e/s état stable pour la réplication. Nous pouvons voir que l’IOPS surcharge dus à la réplication est environ 5 % qui est relativement faible.

![Résultats principaux](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

Hyper-V Replica utilise la mémoire sur le serveur principal afin d’optimiser les performances du disque. Comme le montre le graphique suivant, mémoire, temps sur tous les serveurs dans le cluster principal est marginal. La mémoire surcharge indiquée est le pourcentage de mémoire utilisée par la réplication par rapport à la mémoire totale installée sur le serveur Hyper-V.

![Résultats principaux](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Hyper-V Replica a surcharge minimale de l’UC. Comme illustré dans le graphique, la surcharge de réplication est dans la plage de 2 à 3 %.

![Résultats principaux](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

### <a name="secondary-recovery-server-performance"></a>Performances du serveur secondaire (récupération)

Hyper-V Replica utilise une petite quantité de mémoire sur le serveur de récupération afin d’optimiser le nombre d’opérations de stockage. Le graphique résume l’utilisation de la mémoire sur le serveur de récupération. La mémoire surcharge indiquée est le pourcentage de mémoire utilisée par la réplication par rapport à la mémoire totale installée sur le serveur Hyper-V.

![Résultats secondaires](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

Le montant des opérations d’e/s sur le site de récupération est une fonction du nombre d’opérations d’écriture sur le site principal. Nous allons examiner les totaux des opérations d’e/s sur le site de récupération en comparaison avec les total des opérations d’e/s et écrire des opérations sur le site principal. Les graphiques indiquent que le total des Ops ES/s sur le site de récupération est

- Environ 1,5 fois l’écriture e/s sur le serveur principal.

- Environ 37 % de l’IOPS total sur le site principal.

![Résultats secondaires](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![Résultats secondaires](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

### <a name="effect-of-replication-on-network-utilization"></a>Effet de la réplication sur l’utilisation du réseau

Une moyenne de 275 Mo par seconde de bande passante réseau utilisée entre les nœuds principaux et de restauration (avec compression) par rapport à une bande passante existante de 5 Go par seconde.

![Résultats de l’utilisation du réseau](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

### <a name="effect-of-replication-on-virtual-machine-performance"></a>Effet de la réplication sur les performances des machines virtuelles

Une considération importante est l’impact de la réplication sur des charges de travail en cours d’exécution sur les ordinateurs virtuels. Si le site principal est correctement configuré pour la réplication, il ne doit pas être tout impact sur les charges de travail. Lightweight du Hyper-V Replica mécanisme de suivi permet de s’assurer que les charges de travail en cours d’exécution sur les ordinateurs virtuels ne sont pas concernés lors de la réplication de l’état stable. Ceci est illustré dans les graphiques suivants.

Ce graphique présente des Ops ES/s effectuées par les machines virtuelles exécutant des charges de travail différentes avant et après que la réplication a été activée. Vous pouvez observer qu’il n’existe aucune différence entre les deux.

![Résultats d’effet de réplica](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

Le graphique suivant montre le débit des machines virtuelles exécutant des charges de travail différentes avant et après que la réplication a été activée. Vous pouvez observer que la réplication n’a aucun impact significatif.

![Effets de réplica de résultats](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

### <a name="conclusion"></a>Conclusion

Les résultats montrent clairement que récupération de Site d’Azure, couplés avec Hyper-V Replica, évolue parfaitement avec un minimum de surcharge pour un grand cluster.  Récupération de Site Azure fournit déploiement simple, de réplication, de gestion et de surveillance. Hyper-V Replica fournit l’infrastructure nécessaire pour la mise à l’échelle d’une réplication réussie. Pour planifier un déploiement optimal nous vous suggérons de que télécharger le [Réplica d’Hyper-V, Capacity Planner](https://www.microsoft.com/download/details.aspx?id=39057).

## <a name="test-environment-details"></a>Détails de l’environnement de test

### <a name="primary-site"></a>Site principal

- Le site principal a un cluster contenant cinq serveurs Hyper-V en cours d’exécution des machines virtuelles 470.

- Les ordinateurs virtuels s’exécutent différentes charges de travail, et leur protection de récupération de Site Azure activée.

- Stockage du nœud de cluster est fourni par un SAN iSCSI. Modèle – Hitachi HUS130.

- Chaque serveur du cluster possède quatre cartes réseau (NIC) de 1 Gbit/s chaque.

- Deux des cartes réseau sont connectées à un réseau privé iSCSI et les deux sont connectés à un réseau d’entreprise externe. Un des réseaux externes est réservé pour les communications de cluster uniquement.

![Configuration requise du matériel principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

|Serveur|MÉMOIRE VIVE|Modèle|Processeur|Nombre de processeurs|CARTE D’INTERFACE RÉSEAU|Logiciel|
|---|---|---|---|---|---|---|
|Serveurs Hyper-V dans le cluster : <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25|128ESTLAB-HOST25 a 256|Dell™ PowerEdge™ R820|Intel (r) Xeon processeur E5-4620 0 @ 2,20 GHz|4|Je Gbit/s x 4|Windows Server Datacenter 2012 R2 (x64) + le rôle Hyper-V|
|Serveur VMM|2|||2|1 Gbit/s|Windows Server 2012 de la base de données R2 (x 64) + VMM 2012 R2|

### <a name="secondary-recovery-site"></a>Site de secondaire (récupération)

- Le site secondaire a un cluster de basculement de six nœuds.

- Stockage du nœud de cluster est fourni par un SAN iSCSI. Modèle – Hitachi HUS130.

![Spécification du matériel principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

|Serveur|MÉMOIRE VIVE|Modèle|Processeur|Nombre de processeurs|CARTE D’INTERFACE RÉSEAU|Logiciel|
|---|---|---|---|---|---|---|
|Serveurs Hyper-V dans le cluster : <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10|96|Dell™ PowerEdge™ R720|Intel (r) Xeon processeur E5-2630 0 @ 2,30 GHz|2|Je Gbit/s x 4|Windows Server Datacenter 2012 R2 (x64) + le rôle Hyper-V|
|ESTLAB-HOST17|128|Dell™ PowerEdge™ R820|Intel (r) Xeon processeur E5-4620 0 @ 2,20 GHz|4||Windows Server Datacenter 2012 R2 (x64) + le rôle Hyper-V|
|ESTLAB-HOST24|256|Dell™ PowerEdge™ R820|Intel (r) Xeon processeur E5-4620 0 @ 2,20 GHz|2||Windows Server Datacenter 2012 R2 (x64) + le rôle Hyper-V|
|Serveur VMM|2|||2|1 Gbit/s|Windows Server 2012 de la base de données R2 (x 64) + VMM 2012 R2|

### <a name="server-workloads"></a>Charges de travail

- À des fins de test, nous prélevés couramment utilisés dans les scénarios de client d’entreprise des charges de travail.

- Nous utilisons [IOMeter](http://www.iometer.org) avec les caractéristiques de la charge de travail résumées dans le tableau pour la simulation.

- Tous les profils de IOMeter sont définies pour écrire des octets aléatoires pour simuler le pire écrire des modèles pour les charges de travail.

|Charge de travail|Taille d’e/s (Ko)|% Accès|% En lecture|E/s en attente|Modèle d’e/s|
|---|---|---|---|---|---|
|Serveur de fichiers|48163264|60 % 20 %, 5 %5 % 10 %|80 % 80 % 80 % 80 % 80 %|88888|Toutes les 100 % aléatoires|
|SQL Server (volume 1) SQL Server (volume 2)|864|100 % 100 %|70 % de %0|88|% de random100 de 100 % séquentiel|
|Exchange|32|100 %|67 %|8|100 % aléatoires|
|Station de travail/VDI|464|66 % 34 %|95 % de 70 %|11|Les deux 100 % aléatoires|
|Serveur de fichiers Web|4864|33 % 34 % de 33 %|95 % de 95 95 %|888|Tous les 75 % aléatoires|

### <a name="virtual-machine-configuration"></a>Configuration de machine virtuelle

- 470 les machines virtuelles sur le cluster principal.

- Tous les ordinateurs virtuels avec un disque VHDX.

- Machines virtuelles exécutant des charges de travail présentés dans le tableau. Tous ont été créés avec des modèles VMM.

|Charge de travail|# Ordinateurs virtuels|(Go) de RAM minimum|(Go) de RAM maximum|Taille du disque logique (Go) par machine virtuelle|Ops ES/s maximum|
|---|---|---|---|---|---|
|SQL Server|51|1|4|167|10|
|Exchange Server|71|1|4|552|10|
|Serveur de fichiers|50|1|2|552|22|
|VDI|149|.5|1|80|6|
|Serveur Web|149|.5|1|80|6|
|TOTAL|470|||96.83 TO|4108|

### <a name="azure-site-recovery-settings"></a>Paramètres de récupération de Site Azure

- Azure récupération de Site a été configurée sur site pour la protection de locaux

- Le serveur VMM a quatre nuages configurés, contenant les serveurs de cluster Hyper-V et leurs machines virtuelles.

|Nuage VMM principal|Ordinateurs virtuels protégés dans le nuage|Fréquence de réplication|Points de récupération supplémentaires|
|---|---|---|---|
|PrimaryCloudRpo15m|142|15 minutes|Aucun|
|PrimaryCloudRpo30s|47|30 secondes|Aucun|
|PrimaryCloudRpo30sArp1|47|30 secondes|1|
|PrimaryCloudRpo5m|235|5 min|Aucun|

### <a name="performance-metrics"></a>Mesures de performances

Le tableau récapitule les mesures de performances et les compteurs qui ont été mesurées dans le déploiement.

|Métrique|Compteur|
|---|---|
|UNITÉ CENTRALE|\Processor(_Total)\% temps processeur|
|Mémoire disponible|\Memory\Available Mo|
|OPS ES/S|\PhysicalDisk (_Total) \Disk/s|
|Opérations de lecture (IOPS) / s VM|Dispositif de stockage virtuel de \Hyper-V (<VHD>) \Read opérations/s|
|Les opérations d’écriture (IOPS) VM/s|Dispositif de stockage virtuel de \Hyper-V (<VHD>) \Write opérations/S|
|Débit de lecture de machine virtuelle|Dispositif de stockage virtuel de \Hyper-V (<VHD>) \Read octets/s|
|Débit d’écriture de machine virtuelle|Dispositif de stockage virtuel de \Hyper-V (<VHD>) \Write octets/s|


## <a name="next-steps"></a>Étapes suivantes

- [Définir la protection entre les deux sites VMM sur site](site-recovery-vmm-to-vmm.md)
