<properties
   pageTitle="Vue d’ensemble de la Suite de gestion (OMS) opérations | Microsoft Azure"
   description="Suite de gestion des opérations Microsoft (OMS) est en nuage solution de gestion informatique de Microsoft qui vous aide à gérer et protéger vos locaux et cloud d’infrastructure.  Cet article identifie les différents services inclus dans OMS et fournit des liens vers leur contenu détaillé."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="bwren" />

# <a name="what-is-operations-management-suite-oms"></a>Quelle est la Suite de gestion des opérations (OMS) ?

Suite de gestion des opérations Microsoft (OMS) est en nuage solution de gestion informatique de Microsoft qui vous aide à gérer et protéger vos locaux et cloud d’infrastructure.  Puisque l’OMS est implémenté sous la forme d’un service basé sur le nuage, vous pouvez les en cours d’exécution rapidement avec un investissement minimal dans les services d’infrastructure.  Nouvelles fonctionnalités sont automatiquement fournies, vous enregistrez la maintenance continue et les coûts de mise à niveau.

En plus de fournir des services précieux sur son propre, OMS peut intégrer de System Center Operations Manager afin d’étendre vos investissements existants de gestion dans le nuage, les composants de System Center.  System Center et l’OMS peuvent travailler ensemble pour fournir une expérience de gestion hybride.

Les sections suivantes fournissent une description de haut niveau des zones de valeur différente de l’OMS et les services qui les implémentent.  Vous pouvez faire référence à l’architecture d’OMS pour une vue d’ensemble des différents composants OMS avant d’examiner la documentation détaillée pour chaque.


## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Insight et Analytique](media/operations-management-suite-overview/icon-insight-analytics.png) Insight et Analytique

[Analytique de journal](http://azure.microsoft.com/documentation/services/log-analytics) vous permet de collecter, mettre en corrélation, rechercher et agir sur les données de performance et les journaux générées par les systèmes d’exploitation et des applications. Il vous donne des conseils opérationnels en temps réel à l’aide de la recherche intégrée et tableaux de bord personnalisés facilement analyser des millions d’enregistrements sur tous vos charges de travail et les serveurs, quel que soit leur emplacement physique.

Vous pouvez facilement ajouter des solutions à Analytique de journal qui définissent les données à collecter et à la logique de son analyse.  Les solutions peuvent inclure des fonctionnalités supplémentaires qui sont automatiquement remies aux agents avec très peu ou aucune configuration.  En plus d’utiliser les outils d’analyse fournies par les solutions individuelles, vous pouvez exécuter des recherches personnalisées sur l’ensemble du dataset pour corréler les données entre les systèmes et les applications.  


## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Contrôle et automatisation](media/operations-management-suite-overview/icon-automation-control.png) Contrôle et automatisation

Automation Azure automatise les processus administratifs avec les [procédures opérationnelles](../automation/automation-runbook-types.md) basées sur PowerShell et exécutés dans le nuage Azure.  Procédures opérationnelles peut accéder à n’importe quel produit ou le service qui peut être géré avec PowerShell, y compris les ressources dans d’autres nuages comme Amazon Web Services (AWS).  Procédures opérationnelles peut également être exécutée sur un serveur dans votre centre de données local pour gérer les ressources locales.

Azure Automation fournit la gestion de la configuration avec [PowerShell DSC](../automation/automation-dsc-overview.md).  Vous pouvez créer et gérer les ressources de DSC hébergés dans Azure et les appliquer à des systèmes de cloud et sur site pour définir et appliquer automatiquement de leur configuration.


## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Protection et restauration](media/operations-management-suite-overview/icon-protection-recovery.png) Protection et récupération d’urgence

[Azure sauvegarde](http://azure.microsoft.com/documentation/services/backup) protège vos données d’application et les conserve des années sans aucun investissement et les coûts d’exploitation minimale.  Il peut sauvegarder des données à partir de serveurs de Windows physiques et virtuels en plus des charges de travail applicatives telles que SQL Server et SharePoint.  Il peut également être utilisé par System Center Data Protection Manager (DPM) pour répliquer des données protégées sur Azure pour la redondance et le stockage à long terme.

[Récupération de Site Azure](http://azure.microsoft.com/documentation/services/site-recovery) contribue à votre continuité d’activité et une stratégie de rétablissement (BCDR) par l’orchestration de réplication, le basculement et la restauration de machines virtuelles de Hyper-V sur site, les machines virtuelles VMware et des serveurs physiques Windows/Linux. Vous pouvez répliquer des ordinateurs à un centre de données secondaire ou étendre votre centre de données en les répliquant sur Azure. Récupération de site fournit également un basculement simple et la restauration pour les charges de travail. Il s’intègre avec les mécanismes de récupération après sinistre comme AlwaysOn de SQL Server et fournit des plans de reconstitution pour basculer facilement des charges de travail qui sont montés en cascade sur plusieurs ordinateurs.


## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![Conformité et sécurité de l’OMS](media/operations-management-suite-overview/icon-security-compliance.png) Sécurité et conformité
Sécurité et conformité vous aide à identifier, évaluer et atténuer les risques de sécurité de votre infrastructure.  Ces fonctionnalités d’OMS sont implémentées par le biais de plusieurs solutions dans journal Analytique qui analysent les données des journaux et configuration des systèmes de l’agent pour vous aider à assurer la sécurité permanente de votre environnement.

- La [solution de sécurité et d’Audit](oms-security-getting-started.md ) collecte et analyse des événements de sécurité sur les systèmes gérés afin d’identifier toute activité suspecte.
- La [solution de logiciels anti-programmes malveillants](log-analytics-malware.md ) des rapports sur l’état de la protection contre les logiciels malveillants sur les systèmes gérés.  
- La solution de mises à jour système effectue une analyse des mises à jour de sécurité et autres mises à jour sur vos systèmes gérés afin que vous identifier facilement les systèmes nécessitant une correction.


## <a name="next-steps"></a>Étapes suivantes
- Obtenir des informations sur le [journal Analytique](http://azure.microsoft.com/documentation/services/log-analytics).
- Obtenir des informations sur [l’Automation Azure](../automation/automation-intro.md).
- Obtenir des informations sur la [sauvegarde d’Azure](http://azure.microsoft.com/documentation/services/backup).
- Obtenir des informations sur la [récupération de Site Azure](http://azure.microsoft.com/documentation/services/site-recovery).
