<properties
   pageTitle="FAQ coucher de soleil Business Edition et de Web de base de données SQL Azure | Microsoft Azure"
   description="Découvrez les bases de données Web de SQL Azure et de l’entreprise seront déclassés et en savoir plus sur les fonctionnalités des nouveaux niveaux de service."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/08/2016"
   ms.author="sstein" />

# <a name="web-and-business-edition-sunset-faq"></a>Forum aux questions du coucher de soleil Business Edition et Web

Bases de données SQL Web et Business Azure sont retraités maintenant. Les niveaux basique, Standard, Premium et élastique remplacent les bases de données Web et de l’entreprise retraite.

Pour vous aider avec la mise à niveau des bases de données Web et de l’entreprise, le service de base de données SQL recommande une couche et performances niveau de service approprié (niveau de tarification) pour chaque base de données en fonction de sa charge de travail historique.

**Pour obtenir la tarification des recommandations de couche :**

- [Mise à niveau V12 de base de données SQL via le portail Azure](sql-database-upgrade-server-portal.md)
- [Mise à niveau V12 de base de données SQL à l’aide de PowerShell](sql-database-upgrade-server-powershell.md)
- [Modifier le niveau de tarification d’une base de données Web ou d’entreprise](sql-database-service-tier-advisor.md)



## <a name="why-does-the-azure-portal-show-my-web-and-business-edition-databases-as-retired"></a>Pourquoi le portail Azure n’affiche pas mes Web et Business edition bases de données comme étant déclassés ?

Dans la mesure où les bases de données d’édition Web et de l’entreprise ne seront pas disponibles après septembre 2015, le portail des étiquettes de bases de données Web et de l’entreprise comme étant déclassés. L’étiquette déclassé fournit également un rappel que les bases de données Web et de l’entreprise doivent être mis à niveau Standard, Basic ou Premium. Pour plus d’informations sur la mise à niveau de bases de données Web ou entreprise vers les nouveaux niveaux de service, consultez [mise à niveau vers V12 de base de données SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="which-new-service-tier-is-the-best-choice-to-upgrade-my-existing-web-or-business-database-to"></a>Le nouveau niveau de service est le meilleur choix pour la mise à niveau de ma base de données Web ou activité existant à ?

Sélection d’un nouveau service niveau et performances niveau approprié pour votre base de données Web ou une entreprise dépend des fonctionnalité et les performances des exigences spécifiques de votre application.

Utilisez les recommandations de niveau tarification décrites ci-dessus, ou pour obtenir des informations détaillées pour vous aider dans le choix d’un nouveau niveau de service approprié, consultez [mise à niveau vers V12 de base de données SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="why-is-microsoft-introducing-new-service-tiers"></a>Pourquoi Microsoft introduit de nouveaux niveaux de service ?

En fonction des commentaires des clients, base de données de SQL Azure introduit de nouveaux niveaux de service pour aider les clients plus facilement prendre en charge les charges de travail de base de données relationnelle. Les nouveaux niveaux sont conçus pour offrir des performances prévisibles dans un spectre de sept niveaux de légers aux demandes des applications transactionnelles de poids lourds. En outre, les nouveaux niveaux proposent une gamme de fonctionnalités de continuité d’activité, un SLA de disponibilité plus puissant, plus grande taille de bases de données pour moins d’argent et une expérience améliorée de facturation.

## <a name="where-can-i-learn-more-about-the-new-service-tiers"></a>Où puis-je obtenir plus d’informations sur les nouveaux niveaux de service ?

Pour obtenir des informations détaillées sur les nouveaux niveaux de service et d’un modèle de performances, consultez [les niveaux de Service](sql-database-service-tiers.md). Pour obtenir des informations pour les nouveaux niveaux de service de tarification détaillées, consultez [la tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="what-features-or-functionality-will-not-be-available-in-basic-standard-and-premium"></a>Quelles fonctionnalités ou les fonctionnalités ne seront pas disponibles dans Basic, Standard et Premium ?

La fonctionnalité de fédérations est déclassée dans les éditions entreprise et Web. Les clients souhaitant cloisonnée de leurs bases de données sont encouragés à utiliser à la place ou de migrer vers [les outils de base de données élastique](sql-database-elastic-scale-get-started.md) pour [Base de données de SQL Azure](sql-database-elastic-scale-get-started.md), qui simplifie la création et la gestion d’une application qui utilise ont. Une bibliothèque .NET permet aux applications de définir comment les données sont mappées au milieu des fragments et des itinéraires demandes OLTP vers des bases de données appropriées. Pour prendre en charge les opérations de gestion que reconfigurer la façon dont les données sont réparties sur le milieu des fragments, un modèle de service cloud Azure est inclus que vous pouvez héberger votre propre abonnement Azure. En plus des [Outils de base de données élastique](sql-database-elastic-scale-get-started.md), Microsoft va continuer à créer et publier des [instructions de modèles et pratiques ont personnalisé](https://msdn.microsoft.com/library/azure/dn764977.aspx) basé sur les apprentissages à partir des engagements clients complète. Nouveaux clients qui ont besoin de fonctionnalités de mise à l’échelle doivent extraire des [Outils de base de données élastique](sql-database-elastic-scale-get-started.md) ou contacter le Support Microsoft pour évaluer leurs options.

Microsoft modifie également l’expérience de la copie de base de données avec les bases de données Premium. Précédemment que le quota de base de données de prime a été limité, créer une base de données... En tant que copie d’en T-SQL créé une base de données de prime de suspension sans ressources réservées, qui a été facturé à la même vitesse sous la forme d’une base de données de l’entreprise. Quota de la prime n’est maintenant plus librement disponible, suspendu la prime est n’est plus pris en charge. Copie de la base de données va être créé avec la même édition et le niveau de performances comme source et sera facturé en conséquence. Peut choisir de mettre à niveau les bases de données copiées à un niveau de performances ou de niveau service différent pour réduire leurs coûts, si vous le souhaitez. Bases de données existantes suspendu la prime seront convertis en édition entreprise dans le cadre de cette version. Il est prévu que l’introduction de la [Restauration de Point-à-temps](sql-database-recovery-using-backups.md#point-in-time-restore) permet de réduire la nécessité d’effectuer des copies de sauvegarde des bases de données.

## <a name="how-does-basic-standard-and-premium-improve-my-billing-experience"></a>Comment Basic, Standard et Premium améliore-t-elle mon expérience de facturation ?

Basique, Standard et les bases de données SQL Azure de prime sont facturés à l’heure, et vous avez la possibilité de mettre à l’échelle de chaque base de données vers le haut ou vers le bas. Vous êtes facturé à un taux fixe basé sur le service niveau et performance optimale que vous choisissez pour chaque heure. En outre, les niveaux de performance (exemple : Basic, S1 et P2) sont décomposés dans la facturation vous permet d’afficher le nombre de jours/heures de base de données vous engagés dans un mois unique pour chaque niveau de performances. Les bases de données Web et de l’entreprise continuent à facturer l’utilisation d’unités de base de données en fonction de la taille de la base de données. Visitez la [page de tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/) pour en savoir plus sur les tarifs et les différences entre les niveaux de service de nouveau.


## <a name="see-also"></a>Voir aussi

[Base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)

[Web et le prix de l’entreprise](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Niveaux de service](sql-database-service-tiers.md)

[Mise à niveau vers V12 de base de données SQL Azure](sql-database-upgrade-server-portal.md)
