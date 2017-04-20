<properties
    pageTitle="Récupération de Site Azure : Questions fréquentes | Microsoft Azure"
    description="Cet article traite des questions fréquentes sur la récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="get-started-article"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>


# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Récupération de Site Azure : Questions fréquemment posées (FAQ)

Cet article contient les questions fréquemment posées à propos de la récupération de Site Azure. Si vous avez des questions après avoir lu cet article, les publier sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Général

### <a name="what-does-site-recovery-do"></a>Quelle est la récupération de Site ?

Récupération de site contribue à votre stratégie commerciale, la continuité d’activité et de reprise après sinistre récupération (BCDR), par l’orchestration et l’automatisation de la réplication à partir d’ordinateurs virtuels de locaux et des serveurs physiques vers Azure, ou à un centre de données secondaire. [En savoir plus](site-recovery-overview.md).


### <a name="what-can-site-recovery-protect"></a>Ce qui peut protéger la récupération de Site ?

- **Ordinateurs virtuels Hyper-V**: récupération de Site peut protéger toute la charge de travail en cours d’exécution sur un ordinateur virtuel Hyper-V.
- **Serveurs physiques**: récupération de Site peut protéger les serveurs physiques exécutant Windows ou Linux.
- **Les machines virtuelles VMware**: récupération de Site peut protéger toute la charge de travail en cours d’exécution dans une VM de VMware.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>Récupération de Site prend en charge le modèle de gestionnaire de ressources Azure ?

En plus de la récupération de Site dans Azure portal classique, la récupération de Site est disponible dans le portail Azure avec prise en charge pour le Gestionnaire de ressources. Pour la plupart des scénarios de déploiement récupération de Site dans l’Azure portal offre une expérience de déploiement rationalisé et vous pouvez répliquer des ordinateurs virtuels et des serveurs physiques dans le stockage classique ou de stockage du Gestionnaire de ressources. Voici les déploiements pris en charge :

- [Répliquer des ordinateurs virtuels VMware ou les serveurs physiques vers Azure dans le portail Azure](site-recovery-vmware-to-azure.md)
- [Répliquer les machines virtuelles de Hyper-V dans les nuages VMM à Azure dans le portail Azure](site-recovery-vmm-to-azure.md)
- [Répliquer les machines virtuelles de Hyper-V (sans VMM) pour Azure dans le portail Azure](site-recovery-hyper-v-site-to-azure.md)
- [Répliquer les machines virtuelles de Hyper-V dans les nuages VMM sur un site secondaire dans le portail Azure](site-recovery-vmm-to-vmm.md)


### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Que dois-je dans Hyper-V pour orchestrer la réplication avec récupération de Site ?

Pour le serveur hôte Hyper-V ce dont vous avez besoin dépend du scénario de déploiement. Consultez les conditions préalables de Hyper-V dans :

- [Réplication des machines virtuelles de Hyper-V (sans VMM) vers Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Réplication des machines virtuelles de Hyper-V (avec VMM) vers Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Réplication des ordinateurs virtuels Hyper-V sur un centre de données secondaire](site-recovery-vmm-to-vmm.md#before-you-start)

- Si vous utilisez la réplication vers un centre de données secondaire en savoir plus sur [les systèmes d’exploitation invités pris en charge pour les ordinateurs virtuels Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Si vous utilisez la réplication vers Azure, récupération de Site prend en charge tous les systèmes d’exploitation invités sont [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Puis-je protéger des ordinateurs virtuels lorsque le Hyper-V est en cours d’exécution sur un système d’exploitation de client ?

Non, machines virtuelles doivent se trouver sur un serveur hôte Hyper-V qui s’exécute sur un ordinateur de serveur de Windows prises en charge. Si vous avez besoin protéger un ordinateur client vous a le répliquer comme un ordinateur physique pour [Azure](site-recovery-vmware-to-azure.md) ou un [Centre de données secondaire](site-recovery-vmware-to-vmware.md).


### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Les charges de travail puis-je protéger avec récupération de Site ?

Vous pouvez utiliser la restauration du Site pour protéger la plupart des charges de travail s’exécutant sur une machine virtuelle pris en charge ou d’un serveur physique. Récupération de site prend en charge la réplication multi-applications, afin que les applications peuvent être restaurées à un état intelligent. Il s’intègre aux applications Microsoft telles que SharePoint, Exchange, Dynamics, SQL Server et Active Directory et travaille en étroite collaboration avec les principaux fournisseurs, y compris Oracle, SAP, IBM et Red Hat. [En savoir plus](site-recovery-workload.md) sur la protection de la charge de travail.


### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hôtes Hyper-V est nécessaire dans les nuages VMM ?

Si vous souhaitez répliquer sur un centre de données secondaire, puis les ordinateurs virtuels Hyper-V doivent être sur Hyper-V héberge des serveurs situés dans un nuage VMM. Si vous souhaitez répliquer sur Azure, vous pouvez répliquer des ordinateurs virtuels sur les serveurs hôtes Hyper-V avec ou sans les nuages VMM. [Pour en savoir plus](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Puis-je déployer récupération de Site avec VMM si je n’ai qu’un seul serveur VMM ?

Oui. Vous pouvez soit répliquer les machines virtuelles dans des serveurs Hyper-V dans le nuage VMM sur Azure, ou vous pouvez répliquer entre nuages VMM sur un même serveur. Sur site pour la réplication locale, nous recommandons que vous disposez d’un serveur VMM sur les sites principales et secondaires.  [Pour en savoir plus](site-recovery-single-vmm.md)

### <a name="what-physical-servers-can-i-protect"></a>Les serveurs physiques puis-je protéger ?

Vous pouvez répliquer les serveurs physiques exécutant Windows et Linux vers Azure ou sur un site secondaire. [En savoir plus sur les](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) systèmes d’exploitation requis.  Les mêmes prescriptions s’appliquent si vous utilisez la réplication des serveurs physiques vers Azure, ou sur un site secondaire.

Notez que les serveurs physiques seront exécutera en tant que machines virtuelles dans Azure si votre serveur local tombe en panne. Restauration d’un serveur physique sur site n’est pas pris en charge actuellement, mais vous pouvez restauré vers une machine virtuelle s’exécutant sur Hyper-V ou VMware.


### <a name="what-vmware-vms-can-i-protect"></a>Les machines virtuelles de VMware puis-je protéger ?

Pour protéger des ordinateurs virtuels VMware vous aurez besoin d’un hyperviseur vSphere et les machines virtuelles exécutant les outils VMware. Nous vous recommandons également d’avoir à gérer les hyperviseurs VMware vCenter server. [En savoir plus](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sur les exigences exactes pour la réplication des serveurs VMware et machines virtuelles vers Azure, ou sur un site secondaire.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Puis-je gérer de reprise après sinistre pour mes sites distants avec récupération de Site ?

Oui. Lors de la récupération de Site vous permet d’orchestrer la réplication et la reprise de vos sites distants, vous obtiendrez une orchestration unifiée et un affichage de toutes les charges de travail votre office branche dans un emplacement central. Vous pouvez facilement exécuter des basculements et administrer la récupération d’urgence de toutes les branches à partir de votre siège social, sans visiter les branches.

## <a name="security"></a>Sécurité

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Les données de réplication sont envoyées au service de récupération de Site ?

Non, récupération de Site n’intercepter les données répliquées et ne contient aucune information sur ce qui est en cours d’exécution sur des ordinateurs virtuels ou physiques.
Les données de réplication sont échangées entre les hôtes Hyper-V sur site, les hyperviseurs VMware, serveurs physiques et stockage Azure ou votre site secondaire. Récupération de site n’a aucune possibilité d’intercepter ces données. Seules les métadonnées nécessaires pour orchestrer la réplication et le basculement sont envoyée au service de récupération de Site.

Récupération de site est ISO 27001:2013, 27018, HIPAA, DPA certifiés et est en cours d’évaluation SOC2 et FedRAMP JAB.


### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Pour des raisons de respect de la réglementation, même nos métadonnées locales doivent rester dans la même région géographique. Récupération de Site peut aider à nous ?

Oui. Lorsque vous créez un coffre-fort de récupération de Site dans une zone, nous assurer que toutes les métadonnées que nous avons besoin activer et gérer la réplication et de basculement sur incident reste dans cette région 's géographique limite.

### <a name="does-site-recovery-encrypt-replication"></a>Récupération de Site crypte-t-il la réplication ?

Pour les machines virtuelles et des serveurs physiques, la réplication entre les sites locaux de cryptage en transit est pris en charge. Pour les ordinateurs virtuels et les serveurs physiques en répliquant sur Azure, cryptage de transit et de cryptage au repos (dans Azure) sont pris en charge.


## <a name="replication"></a>Réplication


### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Existe-t-il des conditions requises pour la réplication de machines virtuelles vers Azure ?

Ordinateurs virtuels que vous souhaitez répliquer sur Azure doit satisfaire aux [exigences Azure](site-recovery-best-practices.md#virtual-machines).

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Puis-je répliquer des machines virtuelles de génération 2 Hyper-V vers Azure ?

Oui. Récupération de site convertit de la génération 2 à la génération 1 lors du basculement. Au retour arrière, l’ordinateur est converti à la génération 2. [Pour en savoir plus](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Si les répliquer vers Azure comment puis-je payer pour les machines virtuelles d’Azure ?

Lors de la réplication normale, sont répliquées vers redondants géo stockage Azure et vous n’avez pas besoin de payer les frais de machine virtuelle Azure IaaS, fournissant un avantage significatif. Lorsque vous exécutez un basculement vers Azure, récupération de Site crée automatiquement les machines virtuelles de Azure IaaS, et après que vous serez facturé pour les ressources de calcul que vous consommez dans Azure.


### <a name="is-there-an-sdk-i-can-use-to-automate-the-asr-workflow"></a>Existe-t-il un kit de développement logiciel que je peux utiliser pour automatiser le processus de récupération automatique du système ?

Oui. Vous pouvez automatiser les workflows de restauration du Site à l’aide de l’API Rest, PowerShell ou l’Azure SDK. Scénarios pris en charge pour le déploiement de restauration de Site à l’aide de PowerShell :

- [Répliquer les machines virtuelles de Hyper-V dans VMM de nuages de PowerShell d’Azure classique](site-recovery-deploy-with-powershell.md)
- [Répliquer les machines virtuelles de Hyper-V dans VMM de nuages pour le Gestionnaire de ressources PowerShell Azure](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Répliquer les machines virtuelles de Hyper-V sans VMM pour PowerShell d’Azure classique](site-recovery-hyper-v-site-to-azure-classic.md)
- [Répliquer les machines virtuelles de Hyper-V sans VMM au Gestionnaire de ressources de PowerShell Azure](site-recovery-deploy-with-powershell-resource-manager.md)


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Si pour répliquer vers Azure quel type de compte de stockage ai-je besoin ?

- **Azure portal classique**: Si vous déployez restauration du Site portail Azure classique, vous aurez besoin d’un [compte de stockage redondant geo standard](../storage/storage-redundancy.md#geo-redundant-storage). Stockage de la prime n’est pas pris en charge actuellement. Le compte doit être dans la même région, comme le coffre-fort de la récupération de Site.

- **Azure portal**: Si vous déployez récupération de Site dans Azure portal, vous devez posséder un compte de stockage LRS ou GRS. Nous vous recommandons de GRS afin que les données sont résilientes si une panne régionale se produit, ou si la région principale ne peut pas être récupérée. Le compte doit être dans la même région, comme le coffre-fort de Services de récupération. Stockage de la prime est pris en charge uniquement si vous utilisez la réplication ordinateurs virtuels VMware ou les serveurs physiques.

### <a name="how-often-can-i-replicate-data"></a>À quelle fréquence puis-je répliquer des données ?

- **Hyper-v :** Ordinateurs virtuels Hyper-V peut répliquer toutes les 30 secondes, 5 minutes ou 15 minutes. Si vous avez configuré la réplication SAN la réplication est synchrone.
- **VMware et des serveurs physiques :** Une fréquence de réplication n’est pas pertinente ici. La réplication est continue.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Puis-je prolonger la réplication à partir du site de récupération existant vers un autre site tertiaire ?

Réplication étendue ou enchaînée n’est pas pris en charge. Demander cette fonctionnalité dans les [Evaluations](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Puis-je faire une réplication en mode hors connexion la première fois pour répliquer vers Azure ?

Cela n’est pas pris en charge. Demander cette fonctionnalité dans les [Evaluations](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### <a name="can-i-exclude-specific-disks-from-replication"></a>Puis-je exclure des disques spécifiques de la réplication ?

Cela est pris en charge lorsque vous êtes à [répliquer des ordinateurs virtuels VMware et des serveurs physiques](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) vers Azure, via le portail Azure.


### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Puis-je répliquer des machines virtuelles avec les disques dynamiques ?

Les disques dynamiques sont pris en charge lors de la réplication des ordinateurs virtuels Hyper-V. Ils sont également pris en charge lors de la réplication des ordinateurs virtuels VMware et les machines physiques vers Azure. Le disque du système d’exploitation doit être un disque de base.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Puis-je ajouter un nouvel ordinateur à un groupe de réplication existant ?

Ajout de nouveaux ordinateurs à des groupes de réplication existants est pris en charge. Pour ce faire, sélectionnez le groupe de réplication (à partir de lames de 'Éléments répliqué') et d’un menu de contexte/sélectionner cliquez le bouton droit sur le groupe de réplication et sélectionnez l’option appropriée.

![Ajouter à un groupe de réplication](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Est-il possible de limiter la bande passante allouée pour le trafic de réplication de Hyper-V ?

Oui. Vous pouvez en savoir plus sur la limitation de bande passante dans les articles de déploiement :

- [Planification de capacité pour répliquer des ordinateurs virtuels VMware et des serveurs physiques](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [Planification de capacité pour répliquer des ordinateurs virtuels de Hyper-V dans les nuages VMM](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [Planification de capacité pour répliquer des ordinateurs virtuels de Hyper-V sans VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## <a name="failover"></a>Basculement sur incident


### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Si je suis basculent vers Azure, comment accéder les ordinateurs virtuels Azure après le basculement ?

Vous pouvez accéder à la VM Azure via une connexion Internet sécurisée, via une connexion VPN de site à site ou sur Azure ExpressRoute. Vous devez préparer un certain nombre de choses pour vous connecter. En savoir plus dans :

- [Se connecter aux ordinateurs virtuels d’Azure après basculement sur incident des ordinateurs virtuels VMware ou les serveurs physiques](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [Se connecter aux ordinateurs virtuels d’Azure après basculement sur incident des ordinateurs virtuels de Hyper-V dans les nuages VMM](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [Se connecter aux ordinateurs virtuels d’Azure après basculement sur incident des ordinateurs virtuels de Hyper-V sans VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Si basculer vers Azure comment Azure fait-il que mes données sont résilientes ?

Azure est conçu pour les basculements. Récupération de site est déjà conçue pour le basculement vers un datacenter Azure secondaire, selon le SLA Azure si le besoin se fait sentir. Dans ce cas, nous assurer que vos métadonnées et coffres-forts restent dans la même région géographique que vous avez choisi pour votre chambre forte.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Si je suis réplication que se passe-t-il si mon centre de données principal subit une panne inattendue entre deux centres de données ?

Vous pouvez déclencher un basculement non planifié du site secondaire. Récupération de site n’a pas besoin de la connectivité à partir du site principal pour effectuer le basculement.

### <a name="is-failover-automatic"></a>Est le basculement automatique ?

Basculement sur incident n’est pas automatique. Vous initiez des basculements sur incident avec un seul clic dans le portail, ou vous pouvez utiliser [PowerShell de récupération de Site](https://msdn.microsoft.com/library/dn850420.aspx) pour déclencher un basculement sur incident. Remise en place est une action simple dans le portail de récupération de Site.

Pour automatiser vous pourrait permet de détecter une panne de machine virtuelle sur site Orchestrator ou Operations Manager et puis de déclencher le basculement à l’aide du SDK.

- [Pour en savoir plus](site-recovery-create-recovery-plans.md) sur les plans de reprise.
- [Pour en savoir plus](site-recovery-failover.md) sur le basculement sur incident.
- [Pour en savoir plus](site-recovery-failback-azure-to-vmware.md) sur l’échec sauvegarder des ordinateurs virtuels VMware et des serveurs physiques


## <a name="service-providers"></a>Fournisseurs de services


### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Je suis un fournisseur de services. Récupération de Site fonctionne pour les modèles d’infrastructure dédiée et partagée ?

Oui, récupération de Site prend en charge deux modèles d’infrastructure dédiée et partagée.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Pour un fournisseur de service, est l’identité de mon client partagé avec le service de récupération de Site ?

N° Identité de la cliente reste anonyme. Votre locataires ne doivent pas accéder au portail de récupération de Site. Seul l’administrateur du fournisseur de service interagit avec le portail.


### <a name="will-tenant-application-data-ever-go-to-azure"></a>Données de l’application cliente se passera jamais vers Azure ?

Lors de la réplication entre les sites appartenant à un fournisseur de service, les données d’application vont jamais vers Azure. Données sont cryptées en transit et répliqués directement entre les sites de fournisseur de service.

Si vous utilisez la réplication vers Azure, données d’application sont envoyées au stockage Azure, mais pas pour le service de récupération de Site. Données cryptées de transit et restent cryptées dans Azure.


### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Mes locataires reçoit une nomenclature des services Azure ?

N° Relation de facturation d’Azure est directement avec le fournisseur de services. Fournisseurs de services sont responsables pour la génération de nomenclatures spécifiques pour leurs locataires.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Si je suis réplication vers Azure, nous devons exécuter des machines virtuelles dans Azure en permanence ?

Non, les données sont répliquées vers un compte de stockage Azure dans votre abonnement. Lorsque vous effectuez un basculement sur incident test (perceuse DR) ou un basculement sur incident réel, récupération de Site crée automatiquement des ordinateurs virtuels dans votre abonnement.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Vous assurer d’isolation au niveau du locataire quand je réplique vers Azure ?

Oui.

### <a name="what-platforms-do-you-currently-support"></a>Quelles sont les plates-formes vous prennent en charge ?

Nous prenons en charge Pack d’Azure, système de plate-forme de nuage, et System Center en fonction des déploiements (2012 et versions ultérieures). [En savoir plus](https://technet.microsoft.com/library/dn850370.aspx) sur l’intégration d’Azure Pack et de récupération de Site.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Prenez-vous en charge seul Pack d’Azure et les déploiements de serveur VMM unique ?

Oui, vous pouvez répliquer des ordinateurs virtuels Hyper-V sur Azure, ou la réplication entre sites du fournisseur de service.  Notez que si vous répliquez entre les sites de fournisseur de service, intégration de procédure opérationnelle Azure n’est pas disponible.


## <a name="next-steps"></a>Étapes suivantes

- Lire la [Présentation de la récupération de Site](site-recovery-overview.md)
- En savoir plus sur [l’architecture de récupération de Site](site-recovery-components.md)  
