<properties
    pageTitle="Les charges de travail pouvez vous protéger avec récupération de Site Azure ?"
    description="Récupération de Site Azure protège vos charges de travail et les applications en coordonnant la réplication, le basculement et la restauration des machines virtuelles de locaux et des serveurs physiques vers Azure ou sur un site secondaire sur site"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>

# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Les charges de travail pouvez vous protéger avec récupération de Site Azure ?


Cet article décrit les charges de travail et des applications, que vous pouvez répliquer avec le service de récupération de Site Azure.

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Les organisations doivent une stratégie commerciale de récupération (BCDR) la continuité d’activité et de reprise après sinistre pour conserver des charges de travail et des données sécurisées et disponibles pendant les temps d’arrêt planifiés et non planifiés et de restaurer les conditions de travail régulière dès que possible.

Récupération de site est un service Azure qui participe à votre stratégie de BCDR. À l’aide de la récupération de Site, vous pouvez déployer la réplication orientée applications dans le nuage ou sur un site secondaire. Si vos applications sont Windows ou basé sur Linux, en cours d’exécution sur des serveurs physiques, VMware ou Hyper-V, vous pouvez utiliser la restauration du Site d’orchestrer la réplication, effectuer des tests de récupération d’urgence et exécution de basculement et de restauration.


Récupération de site s’intègre avec les applications Microsoft, y compris SharePoint, Exchange, Dynamics, SQL Server et Active Directory. Microsoft travaille également en étroite collaboration avec les principaux fournisseurs, y compris Oracle, SAP, IBM et Red Hat. Vous pouvez personnaliser les solutions de réplication sur une base application-par-app.

## <a name="why-use-site-recovery-for-application-replication"></a>Pourquoi utiliser la récupération de Site pour la réplication de l’application ?

Récupération de site contribue à la protection au niveau de l’application et de récupération comme suit :

- App agnostique, fournissant la réplication pour les charges de travail en cours d’exécution sur un ordinateur pris en charge.
- Proche-de réplication synchrone, RPO aussi faible que 30 secondes pour répondre aux besoins des applications d’entreprise les plus importantes.
- Snapshots cohérents avec l’application, pour les applications unique ou à plusieurs niveaux.
- Intégration avec SQL Server AlwaysOn et le partenariat avec d’autres technologies de réplication au niveau de l’application, y compris la réplication Active Directory, AlwaysOn de SQL, groupes de disponibilité de base de données Exchange (DAGs) et Oracle Data Guard.
- Plans de récupération flexibles, qui vous permettent de récupérer une pile d’ensemble de l’application d’un simple clic et incluent pour inclure des scripts externes et des actions manuelles dans le plan.
- Gestion de réseau avancée dans la récupération de Site et Azure pour simplifier les conditions d’application réseau, y compris la possibilité de réserver des adresses IP, configurez l’équilibrage de charge et l’intégration avec Azure Traffic Manager, pour les basculements de réseau faibles RTO.
-  Une bibliothèque d’automation enrichi propose des scripts prêt pour la production, spécifiques à l’application qui peuvent être téléchargés et intégrées avec des plans de reprise.



## <a name="workload-summary"></a>Charge de travail Synthèse

Récupération de site peut répliquer n’importe quelle application en cours d’exécution sur un ordinateur pris en charge. Par ailleurs, nous collaborons avec les équipes produits pour effectuer des essais supplémentaires spécifiques à l’application.

**Charge de travail** | **Répliquer des ordinateurs virtuels Hyper-V sur un site secondaire** | **Répliquer des ordinateurs virtuels Hyper-V vers Azure** | **Répliquer des ordinateurs virtuels VMware vers un site secondaire** | **Répliquer des ordinateurs virtuels VMware vers Azure**
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | Y
Applications Web (IIS, SQL) | Y | Y | Y | Y
System Center Operations Manager | Y | Y | Y | Y
SharePoint | Y | Y | Y | Y
SAP<br/><br/>Répliquer le site SAP vers Azure pour hors cluster | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft)
Exchange (non DAG) | Y | À venir | Y | Y
Bureau/VDI à distance | Y | Y | Y | N/A
Linux (système d’exploitation et applications) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft)
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | À venir | Y | À venir
Oracle | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft)
Serveur de fichiers Windows | Y | Y | Y | Y


## <a name="replicate-active-directory-and-dns"></a>Réplication Active Directory et DNS

Une infrastructure DNS et Active Directory sont essentiels pour la plupart des applications d’entreprise. Lors de la récupération d’urgence, vous devrez protéger et restaurer ces composants d’infrastructure, avant la restauration de vos applications et charges de travail.

Vous pouvez utiliser la récupération de Site pour créer un plan de récupération d’urgence automatisée complète pour Active Directory et DNS. Par exemple, si vous souhaitez ne pas sur SharePoint et SAP à partir d’un serveur principal vers un site secondaire, vous pouvez configurer un plan de récupération échoue sur Active Directory, tout d’abord, puis un plan de récupération d’application spécifiques supplémentaires basculer sur les autres applications qui s’appuient sur Active Directory.

[En savoir plus](site-recovery-active-directory.md) sur la protection de Active Directory et DNS.

## <a name="protect-sql-server"></a>Protéger SQL Server

SQL Server fournit une base de services de données pour les services de données pour de nombreuses applications métier dans un centre de données sur site.  Récupération de site utilisable avec les technologies SQL Server HA/DR, pour protéger les applications d’entreprise de multicouches qui utilisent SQL Server. Fournit de la récupération de site :

- Une solution de récupération après sinistre simple et économique pour SQL Server. Répliquer plusieurs versions et éditions de clusters et les serveurs autonomes de SQL Server vers Azure ou sur un site secondaire.  
- Intégration avec des groupes de disponibilité AlwaysOn de SQL, à gérer le basculement et restauration automatique avec les plans de récupération d’Azure récupération de Site.
- Plans de restauration de bout en bout pour les toutes les couches dans une application, y compris les bases de données SQL Server.
- Mise à l’échelle de SQL Server pour les pics de charge avec récupération de Site, par « rupture » dans plus grandes tailles d’ordinateur virtuel IaaS dans Azure.
- Facile, tests de reprise après sinistre de SQL Server. Vous pouvez exécuter des basculements de test pour analyser les données et exécuter des vérifications de conformité, sans impact sur votre environnement de production.

[En savoir plus](site-recovery-sql.md) sur la protection des serveurs SQL.

##<a name="protect-sharepoint"></a>Protection de SharePoint

Récupération de Site Azure permet de protéger les déploiements de SharePoint, comme suit :

- Élimine la nécessité et les coûts d’infrastructure associés pour une batterie de secours pour une reprise après sinistre. Récupération de Site permet de répliquer une ensemble batterie (niveaux Web, application et base de données) pour Azure ou sur un site secondaire.
- Simplifie la gestion et le déploiement de l’application. Mises à jour déployées sur le site principal sont répliquées automatiquement et sont donc disponibles après le basculement et la restauration d’une batterie de serveurs dans un site secondaire. Permet également de réduire la complexité de la gestion et les coûts associés à une batterie de secours mise à jour.
- Simplifie le développement d’applications SharePoint et les tests en créant un environnement de réplica de la demande de type production copie de test et de débogage.
- Simplifie la transition vers le nuage pour migrer les déploiements de SharePoint vers Azure à l’aide de la récupération de Site.

[En savoir plus](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) sur la protection de SharePoint.


## <a name="protect-dynamics-ax"></a>Protection de Dynamics AX

Récupération de Site Azure protège votre solution Dynamics AX ERP, par :

- Orchestration de la réplication de tout votre Dynamics AX environnement (niveaux Web et AOS, niveaux de base de données, SharePoint) pour Azure, ou pour un site secondaire.
- Simplification de la migration des déploiements de Dynamics AX vers le nuage (Azure).
- Simplification du développement d’application Dynamics AX et de test en créant une copie du type de production à la demande, de test et de débogage.

[En savoir plus](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) sur la protection de Dynamics AX.

## <a name="protect-rds"></a>Protection de RDS

Services de bureau à distance (RDS) permet d’infrastructure virtual desktop infrastructure (VDI), des ordinateurs de bureau basés sur session et des applications, permettant aux utilisateurs de travailler n’importe où. Récupération de Site Azure vous pouvez de :

- Répliquer des bureaux virtuels mis en pool managés ou non managé vers un site secondaire et les applications distantes et les sessions pour un site secondaire ou l’Azure.
- Voici ce que vous pouvez répliquer :

**RDS** | **Répliquer des ordinateurs virtuels Hyper-V sur un site secondaire** | **Répliquer des ordinateurs virtuels Hyper-V vers Azure** | **Répliquer des ordinateurs virtuels VMware vers un site secondaire** | **Répliquer des ordinateurs virtuels VMware vers Azure** | **Répliquer les serveurs physiques sur un site secondaire** | **Répliquer les serveurs physiques vers Azure**
---|---|---|---|---|---|---
**Poste de travail virtuel mis en pool (non managée)** | Oui | N° | Oui | N° | Oui | N°
**Pool de bureau virtuel (managé et sans UPD)** | Oui | N° | Oui | N° | Oui | N°
**Applications à distance et des sessions de bureau (sans UPD)** | Oui | Oui | Oui | Oui | Oui | Oui


[En savoir plus](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sur la protection de RDS.


## <a name="protect-exchange"></a>Protéger Exchange

Récupération de site permet de protéger Exchange, comme suit :

- Pour les petits déploiements Exchange, comme un serveur unique ou en mode autonome, récupération de Site peut répliquer et basculer vers Azure ou sur un site secondaire.
- Pour les déploiements plus importants, récupération de Site s’intègre avec Exchange DAGS.
- DAGs d’Exchange sont la solution recommandée pour la reprise d’Exchange dans une entreprise.  Plans de restauration de site peuvent inclure la DAGs, pour orchestrer DAG basculement entre les sites.


[En savoir plus](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sur la protection d’Exchange.

## <a name="protect-sap"></a>Protection de SAP

Récupération de Site permet de protéger votre déploiement SAP, comme suit :

- Activer la protection de la totalité du déploiement de SAP, en répliquant les couches de déploiement différentes pour Azure, ou pour un site secondaire.
- Simplifier la migration de nuage, à l’aide de la récupération de Site pour faire migrer votre déploiement de SAP vers Azure.
- Simplifier le développement de SAP et de tests, copiez en créant un type de production à la demande pour tester et déboguer des applications.

[En savoir plus](http://aka.ms/asr-sap) sur la protection de SAP.

## <a name="next-steps"></a>Étapes suivantes

[Préparer pour le déploiement de la récupération de Site](site-recovery-best-practices.md) 
