<properties
   pageTitle="Sécurité de Suite de gestion des opérations et de la sécurité des données d’Audit Solution | Microsoft Azure"
   description="Ce document explique comment les données sont gérées et sauvegardées dans les opérations de gestion Suite de Solution de sécurité et d’Audit."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="yurid"/>

# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Sécurité des données de solution Operations Management Suite de sécurité et d’Audit

Pour aider les clients à empêcher, détecter et répondre aux menaces, [sécurité de Suite (OMS) de gestion des opérations et de la Solution d’Audit](operations-management-suite-overview.md) collecte et traite les données sur les ressources, qui inclut :

- Journal des événements de sécurité
- Événements de suivi pour Windows (ETW)
- Événements d’audit AppLocker
- Journal du pare-feu Windows
- Événements de menace Analytique avancées
- Résultats de l’évaluation de la ligne de base
- Résultats de l’évaluation des logiciels anti-programmes malveillants
- Résultats de l’évaluation de la mise à jour/correctifs
- Flux syslogs qui sont explicitement activés sur l’agent

Nous rendre fort engagement à protéger la confidentialité et la sécurité de ces données. Microsoft respecte les directives de sécurité et de conformité strictes, depuis le codage pour l’exploitation d’un service.
Cet article explique comment les données sont gérées et préservées dans la Solution d’Audit et de sécurité de l’OMS.

## <a name="data-sources"></a>Sources de données

Solution d’Audit et sécurité de l’OMS analysent les données de vos ordinateurs virtuels et des ordinateurs physiques où est installé l’Agent de l’OMS. OMS Solution de sécurité et d’Audit peuvent collecter des informations de configuration sur les événements de sécurité, tels que les événements Windows, les journaux d’audit, les journaux IIS et les messages syslog. Voici quelques exemples de ce type de données : type de système d’exploitation et de version, processus, nom de l’ordinateur, adresse IP, en cours d’exécution est consigné dans utilisateur et ID de client.  

## <a name="data-protection"></a>Protection des données

**Segmentation des données**: les données sont conservées logiquement séparées sur chaque composant dans le service. Toutes les données est balisé par l’organisation. Ce balisage persiste tout au long du cycle de vie des données, et elle est appliquée au niveau de chaque couche du service. 

**Accès aux données**: pour fournir des recommandations de sécurité et étudier les menaces de sécurité potentielles, personnel de Microsoft peut-être accéder à des informations collectées ou analysé par les services. Nous adhérer aux [Termes de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) et [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), quel état que Microsoft ne doit pas utiliser les données clients ou dériver des informations à partir de celui-ci, à des fins commerciales de publicité ou similaires. Pour fournir des recommandations de sécurité et étudier les menaces de sécurité potentielles, personnel de Microsoft peut-être accéder à informations collectées ou par les services d’analyse. Nous uniquement utiliser les données client en fonction des besoins pour vous fournir des services Azure, y compris à des fins compatibles avec la fourniture de ces services. Vous conservez tous les droits à vos propres données.

**Utilisent de données**: Microsoft utilise des modèles et des menaces, vu les différents utilisateurs pour améliorer nos capacités de détection et de prévention ; nous le faire conformément aux engagements de confidentialité décrits dans notre [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [AZURE.NOTE] Emplacement des données est configuré au niveau de l’espace de travail OMS, lors de la création de l’espace de travail, qui fait partie du processus initial de configuration d’Audit et de sécurité de l’OMS.

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment les données sont gérées et garanties de l’OMS. Pour en savoir plus sur les solutions de sécurité de l’OMS et l’Audit, consultez :

- [Vue d’ensemble de la Suite de gestion (OMS) opérations](operations-management-suite-overview.md)
- [Surveiller et répondre aux alertes de sécurité dans les opérations Management Suite Solution de sécurité et d’Audit](oms-security-responding-alerts.md)
- [Surveillance des ressources dans des opérations de sécurité Suite de gestion et de la Solution d’Audit](oms-security-monitoring-resources.md)

