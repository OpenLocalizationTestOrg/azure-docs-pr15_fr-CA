<properties
   pageTitle="Service de base de données de SQL Azure et d’Azure le centre de sécurité | Microsoft Azure"
   description="Cet article explique comment le centre de sécurité peut vous aider à sécuriser vos bases de données dans la base de données de SQL Azure."
   services="sql-database"
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
   ms.date="10/18/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-and-azure-sql-database-service"></a>Service de base de données de SQL Azure et d’Azure le centre de sécurité

[Le centre de sécurité Azure](https://azure.microsoft.com/documentation/services/security-center/) vous aide à empêcher, détecter et répondre aux menaces. Il fournit la gestion de stratégie et de surveillance de la sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec un vaste écosystème de solutions de sécurité.

Cet article explique comment le centre de sécurité peut vous aider à sécuriser vos bases de données dans la base de données de SQL Azure.

## <a name="why-use-security-center"></a>Pourquoi utiliser le centre de sécurité ?

Centre de sécurité vous aide à protéger les données dans la base de données de SQL en fournissant une visibilité sur la sécurité de tous vos serveurs et bases de données. Avec le centre de sécurité, vous pouvez :

- Définir des stratégies pour le cryptage de la base de données SQL et l’audit.
- Surveiller la sécurité des ressources de base de données de SQL sur tous vos abonnements.
- Identifier et résoudre les problèmes de sécurité.
- Intégrer les alertes à partir de la [base de données de SQL Azure une détection des menaces](../sql-database/sql-database-threat-detection-get-started.md).

En plus de la protection de vos ressources de base de données SQL, le centre de sécurité fournit également le contrôle de la sécurité et de gestion pour les machines virtuelles Azure, Services en nuage, Services d’application, les réseaux virtuels et plus. En savoir plus sur le centre de sécurité [ici](security-center-intro.md).

## <a name="prerequisites"></a>Conditions préalables

Pour vous familiariser avec le centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Avec votre abonnement, la couche libre du centre de sécurité est activée. Pour plus d’informations sur libre du centre de sécurité et les couches Standard, consultez [Tarification des centre de sécurité](https://azure.microsoft.com/pricing/details/security-center/).

Centre de sécurité prend en charge l’accès par rôle. Pour en savoir plus sur le contrôle d’accès basé sur les rôles (RBAC) dans Azure, consultez [Contrôle d’accès basé sur le rôle de répertoire actif Azure](../active-directory/role-based-access-control-configure.md). Le Forum aux questions du centre de sécurité fournit des informations sur [la gestion des autorisations dans le centre de sécurité](security-center-faq.md#how-are-permissions-handled-in-azure-security-center).

## <a name="access-security-center"></a>Centre de sécurité de l’accès

Pour accéder à Security Center à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). [Connectez-vous au portail](https://portal.azure.com/) et sélectionnez l' **option du centre de sécurité**.

![Option du centre de sécurité][1]

La lame du **Centre de sécurité** s’ouvre.
![Lame de centre de sécurité][2]

## <a name="set-security-policy"></a>Définir la stratégie de sécurité

Une stratégie de sécurité définit le jeu de contrôles qui sont recommandés pour les ressources au sein de l’abonnement spécifié ou d’un groupe de ressources. Dans le centre de sécurité, vous définissez des stratégies pour vos abonnements ou les groupes de ressources en fonction des besoins de sécurité de votre société et le type des applications ou des données dans chaque abonnement.

Vous pouvez définir une stratégie pour afficher les recommandations pour l’audit de SQL et de chiffrement de données transparent SQL (TDE).

- Lorsque vous activez **l’audit de SQL et de détection des menaces**, le centre de sécurité vous recommande que l’audit de l’accès à la base de données Azure soit activé pour la conformité, avancées de détection et les fins de l’enquête.
- Lorsque vous activez le **chiffrement de données transparent de SQL**, le centre de sécurité recommande que le cryptage au repos être activée pour votre base de données de SQL Azure, les sauvegardes associées et les fichiers journaux des transactions.

Pour définir une stratégie de sécurité, sélectionnez la vignette de la **stratégie** sur la lame du centre de sécurité. Sur la lame de la **stratégie de sécurité** , sélectionnez l’abonnement sur lequel vous souhaitez activer la stratégie de sécurité. Sélectionnez la **politique de prévention** et **d’Activer les recommandations de sécurité que vous souhaitez utiliser pour cet abonnement** .
![Stratégie de sécurité][3]

Pour plus d’informations, voir [définition des stratégies de sécurité](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Gérer la recommandation de sécurité

Centre de sécurité analyse périodiquement l’état de sécurité de vos ressources d’Azure. Lorsque le centre de sécurité identifie les failles de sécurité potentielles, il crée des recommandations. Les recommandations vous guident tout au long du processus de configuration des contrôles nécessaires.

Après avoir défini une stratégie de sécurité, le centre de sécurité analyse l’état de la sécurité de vos ressources pour identifier les vulnérabilités potentielles. Les recommandations sont affichées dans un tableau où chaque ligne représente une recommandation particulière. Utilisez le tableau suivant comme référence pour vous aider à comprendre les recommandations disponibles pour la base de données de SQL Azure, et de ce que fait chaque recommandation si vous l’appliquez. Sélection d’une recommandation permet d’atteindre un article qui explique comment mettre en oeuvre la recommandation dans le centre de sécurité.

| Recommandation | Description |
| ----- | ----- |
| [Activer la détection d’audit et les menaces sur les serveurs SQL](security-center-enable-auditing-on-sql-servers.md) | Vous recommande d’activer la détection de menaces et de l’audit pour les serveurs de base de données SQL. (Service de base de données SQL uniquement. N’inclut pas Microsoft SQL Server est en cours d’exécution sur vos ordinateurs virtuels.) |
| [Activer la détection d’audit et les menaces sur les bases de données SQL](security-center-enable-auditing-on-sql-databases.md) | Vous recommande d’activer la détection d’audit et de menace pour les bases de données de la base de données SQL. (Service de base de données SQL uniquement. N’inclut pas Microsoft SQL Server est en cours d’exécution sur vos ordinateurs virtuels.) |
| [Activer le chiffrement de données Transparent](security-center-enable-transparent-data-encryption.md) | Recommande d’activer le cryptage pour les bases de données SQL. (Service de base de données SQL uniquement). |

Pour afficher des recommandations pour vos ressources d’Azure, sélectionnez la mosaïque de **recommandations** sur la lame du centre de sécurité. Sur la lame de **recommandations** , sélectionnez une recommandation pour consulter les détails. Dans cet exemple, nous allons sélectionner **Activer l’audit et la détection des menaces sur les serveurs SQL**.

![Recommandations][4]

Comme illustré ci-dessous, centre de sécurité vous indique les serveurs SQL où la détection des menaces et de l’audit ne sont pas activés. Après avoir activé l’audit, vous pouvez configurer les paramètres de détection de menace et les paramètres de messagerie pour recevoir des alertes de sécurité. Détection des menaces vous avertit lorsqu’il détecte des activités anormales de la base de données qui indiquent les menaces de sécurité potentielles à la base de données. Les alertes sont affichées dans le tableau de bord du centre de sécurité.
![Détection de menace et de l’audit][5]

Suivez les étapes de [mise en route de la détection des menaces de base de données SQL](../sql-database/sql-database-threat-detection-get-started.md) pour activer et configurer la détection de menaces et de configurer la liste des e-mails qui recevra les alertes de sécurité lors de la détection des activités anormales.

Pour en savoir plus sur les recommandations, reportez-vous à la section [Gestion des recommandations de sécurité](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Surveiller la santé sécurité

Après avoir activé les [stratégies de sécurité](security-center-policies.md) pour les ressources d’un abonnement, le centre de sécurité analyse la sécurité de vos ressources pour identifier les vulnérabilités potentielles.  Vous pouvez afficher l’état de la sécurité de vos ressources dans la mosaïque de **l’état de sécurité de ressource** . Lorsque vous cliquez sur **données** dans la mosaïque de **l’état de sécurité de ressource** , la lame de **Ressources de données** s’ouvre avec des recommandations SQL pour les problèmes telles que le chiffrement de données transparent et audit de non activée. Il comporte également des recommandations relatives à l’état de santé général de la base de données.
![État de sécurité de ressource][6]

Pour plus d’informations, consultez [contrôle de la sécurité sanitaire](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Gérer et répondre aux alertes de sécurité

Centre de sécurité automatiquement recueille, analyse et intègre les données de journal à partir de [SQL Azure une détection des menaces](../sql-database/sql-database-threat-detection-get-started.md), ainsi que des autres ressources Azure, pour détecter les menaces réelles et de réduire les faux positifs. Liste des alertes de sécurité par ordre de priorité est affichée dans le centre de sécurité ainsi que les informations que vous devez étudier rapidement le problème et des recommandations sur la manière de corriger une attaque.

Pour afficher des alertes, sélectionnez la vignette **d’alertes de sécurité** sur la lame du centre de sécurité. Sur la blade **d’alertes de sécurité** , sélectionnez une alerte pour en savoir plus sur les événements qui ont déclenché l’alerte et que, si tout, la procédure à suivre pour convertir une attaque. Dans cet exemple, nous allons sélectionner **potentiels SQL injection**.
![Alertes de sécurité][7]

Comme illustré ci-dessous, le centre de sécurité fournit des détails supplémentaires qui offrent un aperçu de ce qui a déclenché l’alerte, la ressource cible, le cas échéant l’adresse IP source et des recommandations sur la façon de résoudre.
![Injection de SQL potentielle][8]

Pour plus d’informations, consultez [gestion et répondre aux alertes de sécurité](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Étapes suivantes

- [Forum aux questions du centre de sécurité](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Guide de planification et des opérations du centre de sécurité](security-center-planning-and-operations-guide.md) - suivre un ensemble d’étapes et de tâches pour optimiser votre utilisation du centre de sécurité basée sur les exigences de sécurité de votre organisation et le modèle de gestion de cloud.
- [Données du centre de sécurité](security-center-data-security.md) – Découvrez comment le centre de sécurité recueille et traite les données concernant vos ressources Azure, y compris les informations de configuration, métadonnées, journaux des événements, des fichiers de vidage sur incident et bien plus encore.
- [Gestion des incidents de sécurité](security-center-incident.md) - Apprenez à utiliser la fonctionnalité d’alerte de sécurité dans le centre de sécurité pour vous aider dans la gestion des incidents de sécurité.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
