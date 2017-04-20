<properties
   pageTitle="Activer l’audit sur les bases de données SQL Azure centre de sécurité | Microsoft Azure"
   description="Ce document vous montre comment implémenter la recommandation Azure le centre de sécurité, **activez l’audit sur les bases de données SQL**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-auditing-on-sql-databases-in-azure-security-center"></a>Activer l’audit sur les bases de données SQL Azure centre de sécurité

Le centre de sécurité Azure recommande que vous activez l’audit pour toutes les bases de données SQL, si l’audit n’est pas déjà activé. L’audit peut vous aider à assurer la conformité réglementaire, comprendre l’activité de la base de données et mieux les différences et les anomalies qui peuvent indiquer des problèmes de l’entreprise ou suspectés de violations de la sécurité.

Une fois que vous avez activé l’audit, vous pouvez configurer les paramètres de détection des menaces et des courriels pour recevoir des alertes de sécurité. Détection des menaces détecte des activités anormales de la base de données indiquant les menaces de sécurité potentielles à la base de données. Cela vous permet de détecter et répondre aux menaces potentielles qu’elles se produisent.

Cette recommandation s’applique au service Azure SQL uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. De la lame de **recommandations** , sélectionnez **Activer l’audit sur les bases de données SQL**.  La blade **d’Activer l’audit sur les bases de données SQL** s’ouvre.
![Activer l’audit sur les bases de données SQL][1]

2. Sélectionnez une base de données SQL pour activer l’audit sur. La lame de **détection audit & menace** s’ouvre.
![Détection de menace et de l’audit][2]
3. Sur la lame de **détection d’audit et de la menace** , sélectionnez **ON** sous **Auditing**.
![Activer la détection de menaces et de l’audit][3]


5. Suivez les étapes de [mise en route de la détection des menaces de base de données SQL](../sql-database/sql-database-threat-detection-get-started.md) pour activer et configurer la détection de menaces et de configurer la liste des e-mails qui recevra les alertes de sécurité lors de la détection des activités anormales.

## <a name="see-also"></a>Voir aussi

Cet article vous a expliqué comment mettre en œuvre de la recommandation du centre de sécurité « Activer l’audit sur les bases de données SQL ». Pour en savoir plus sur la sécurisation de votre base de données SQL, consultez les rubriques suivantes :

- [Sécurisation de votre base de données SQL](../sql-database/sql-database-security.md)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- [Blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) : obtenir les informations et les dernières informations de sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]:./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection.png
[3]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
