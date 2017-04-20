<properties
   pageTitle="Introduction à l’intégration du journal Microsoft Azure | Microsoft Azure"
   description="Obtenir des informations sur l’intégration du journal d’Azure, ses fonctionnalités clées et comment il fonctionne."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Introduction à l’intégration du journal Microsoft Azure (aperçu)

Obtenir des informations sur l’intégration du journal d’Azure, ses fonctionnalités clées et comment il fonctionne.

## <a name="overview"></a>Vue d’ensemble

Plateforme en tant que Service (PaaS) et l’Infrastructure en tant que Service (IaaS) hébergé dans Azure générer une grande quantité de données dans des journaux de sécurité. Ces journaux contiennent des informations vitales qui peuvent fournir intelligence et puissantes analyses dans les violations de stratégie, les menaces internes et externes, des problèmes de respect de la réglementation et les anomalies dans le réseau, l’hôte et l’activité des utilisateurs.

Intégration du journal d’Azure vous permet d’intégrer des journaux bruts provenant de vos ressources d’Azure dans vos systèmes d’informations de sécurité et de gestion de l’événement (SIEM) sur site. Intégration du journal d’Azure collecte Azure Diagnostics à partir de votre Windows *(WAD)* ordinateurs virtuels, ainsi que les tests de diagnostic à partir des solutions de partenaires comme un pare-feu d’Application Web (WAF). Cette intégration fournit un tableau de bord unifiée pour tous vos actifs, sur site ou dans le nuage, afin que vous pouvez agréger, mettre en corrélation, analyse et alerte pour les événements de sécurité.

![Intégration du journal d’Azure][1]

## <a name="what-logs-can-i-integrate"></a>Les journaux puis-je intégrer ?

Azure génère la journalisation complète pour tous les services Azure. Ces journaux est classés en deux types principaux :

- **Les journaux de contrôle et de gestion**, qui fournissent une visibilité sur les opérations de création de gestionnaire de ressources Azure, UPDATE et DELETE. Les journaux d’Audit Azure est un exemple de ce type de journal.
- **Journaux de données de plan**, ce qui donne une visibilité sur les événements déclenchés dans le cadre de l’utilisation d’une ressource d’Azure. Exemples de ce type de journal l’événement Windows, système de sécurité, et journaux d’applications dans une machine virtuelle.

Intégration du journal d’Azure prend actuellement en charge l’intégration d’Azure, journaux de machine virtuelle et les alertes du centre de sécurité Azure.

Si vous avez des questions sur l’intégration du journal d’Azure, veuillez envoyer un e-mail à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez abordé intégration du journal d’Azure. Pour plus d’informations sur l’intégration du journal d’Azure et les types de journaux pris en charge, consultez les rubriques suivantes :

- [Intégration du journal Microsoft Azure pour les journaux Azure (aperçu)](https://www.microsoft.com/download/details.aspx?id=53324) – centre de téléchargement pour plus d’informations, configuration système requise et instructions d’installation sur l’intégration du journal d’Azure.
- [Mise en route de l’intégration du journal d’Azure](security-azure-log-integration-get-started.md) - ce didacticiel vous guide d’installation de l’intégration du journal d’Azure et d’intégrer des journaux de stockage Azure, Azure et les alertes du centre de sécurité.
- [Les étapes de configuration de partenaire](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – ce billet de blog vous indique comment configurer l’intégration de journal Azure pour fonctionner avec les solutions partenaire Splunk ArcSight de HP et IBM QRadar.
- [Journal azure Integration Forum aux questions (FAQ)](security-azure-log-integration-faq.md) - Forum aux questions sur cette répond aux questions à propos de l’intégration du journal d’Azure.
- [Alertes du centre de sécurité intégration avec Azure journal intégration](../security-center/security-center-integrating-alerts-with-log-integration.md) – ce document vous montre comment synchroniser les alertes du centre de sécurité, ainsi que les événements de sécurité d’ordinateur virtuel collectées par les Diagnostics d’Azure et d’Azure les journaux d’Audit, avec votre journal analytique ou d’une solution SIEM.
- [Nouvelles fonctionnalités pour les diagnostics de Windows Azure et d’Azure les journaux d’Audit](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – ce billet de blog présente Azure les journaux d’Audit et d’autres fonctionnalités qui vous aident à obtenir des informations sur les opérations de vos ressources d’Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
