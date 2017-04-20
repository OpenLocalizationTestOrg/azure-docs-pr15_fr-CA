<properties 
   pageTitle="Vue d’ensemble de Microsoft Azure données lac Analytique | Azure" 
   description="Données lac Analytique est un service de calcul de données Azure qui vous permet d’utiliser les données pour votre entreprise à l’aide de répercuter les informations obtenues à partir de vos données dans le nuage, où elle soit ou non et quelle que soit sa taille. Données lac Analytique permet cela dans le plus simple, moyen, la plus évolutive et la plus économique possible. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Vue d’ensemble de Microsoft Azure données lac Analytique

## <a name="what-is-azure-data-lake-analytics"></a>Nouveautés d’Azure données lac Analytique ?

Analytique de LAC données Azure est un nouveau service, conçu pour faciliter l’analytique des données volumineuses. Ce service vous pouvez vous concentrer sur l’écriture, en cours d’exécution et de gestion des travaux, plutôt que d’exploitation d’une infrastructure distribuée. Au lieu de déploiement, la configuration et réglage matériel, vous écrivez des requêtes pour transformer vos données et extraire des informations précieuses. Le service analytique peut gérer des travaux de n’importe quelle échelle instantanément en définissant simplement le cadran de la puissance dont vous avez besoin. Vous ne payez que pour votre travail lorsqu’il est exécuté, ce qui la rend économique. Le service analytique prend en charge Azure Active Directory vous permettant de gérer simplement les accès et les rôles, intégrées à votre système d’identité sur site. Il inclut également les U-SQL, un langage qui unifie les avantages de SQL avec la puissance expressive du code utilisateur. Évolutives distribuée runtime du U-SQL vous permet analyser efficacement les données dans le magasin et sur les serveurs SQL Azure, base de données de SQL Azure et entrepôt de données SQL Azure.


## <a name="key-capabilities"></a>Fonctionnalités clés

- **Évolutivité dynamique** 

    Données lac Analytique est conçu dès le départ pour le nuage évolutivité et de performances.  Dynamiquement, il met les ressources et vous permet de faire analytique sur téraoctets ou même exaoctets de données. Une fois la tâche terminée, il apprêtez sans ressources automatiquement, et vous payez uniquement pour la puissance de traitement utilisée. À mesure que vous augmentez ou réduisez la taille des données stockées ou la quantité de calcul utilisée, vous n’avez pas à réécrire le code. Ce vous permet de vous concentrer sur votre logique métier uniquement et pas comment traiter et stocker des groupes de données volumineux. 

- **Développer plus rapidement, de déboguer et d’optimiser plus efficacement à l’aide d’outils familiers**

    Données lac Analytique a une forte intégration avec Visual Studio, afin que vous pouvez utiliser des outils familiers pour exécuter, déboguer et analyser votre code. Visualisations de vos travaux d’U-SQL vous permettent de voir comment votre code s’exécute à grande échelle, pour vous pouvez d’identifier facilement les performances des goulots d’étranglement et optimiser les coûts. 

- **U-SQL : simple et familier, puissante et extensible**

    Données lac Analytique inclut U-SQL, un langage de requête qui étend la nature déclarative, simple et familier de SQL avec la puissance expressive de C#. Le langage SQL-U est basé sur le même runtime distribué qui alimente les systèmes de données au sein de Microsoft. Des millions de développeurs SQL et .NET peuvent désormais traiter et analyser toutes leurs données avec les compétences qu’ils possèdent déjà.

- **S’intègre en toute transparence avec vos investissements informatiques**

    Données lac Analytique peut utiliser vos investissements informatiques existants pour l’identité, de gestion, de sécurité et de l’entrepôt de données. Cela simplifie la gestion de données et vous permettent d’étendre vos applications de données en cours. Données lac Analytique est intégré à Active Directory pour les autorisations et la gestion des utilisateurs et est livré avec intégré de surveillance et d’audit.

- **Abordable et rentable**

    Données lac Analytique est une solution économique pour les charges de données en cours d’exécution. Vous payez sur une base par projet lors du traitement de données. Aucun matériel, licences ou les accords de service à un support spécifique sont requis. Le système évolue automatiquement vers le haut ou vers le bas à mesure que le travail commence et se termine, c'est-à-dire que vous ne payez jamais plus que ce dont vous avez besoin. 

- **Fonctionne avec toutes vos données Azure**

    Données lac Analytique peut fonctionner avec un certain nombre de sources de données Azure : stockage des objets Blob Azure, de la base de données SQL d’Azure et bien entendu données lac Analytique est spécialement optimisé pour travailler avec un magasin de LAC de données Azure - offrant le plus haut niveau de performance, de débit et de parallélisation pour vous les charges de travail données volumineuses.

## <a name="see-also"></a>Voir aussi

- Mise en route
    - [Mise en route d’Analytique de LAC de données à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
    - [Mise en route de données Analytique de LAC à l’aide de PowerShell d’Azure](data-lake-analytics-get-started-powershell.md)
    - [Mise en route de données Analytique de LAC à l’aide du Kit de développement .NET Azure](data-lake-analytics-get-started-net-sdk.md)
    - [Développer des scripts SQL-U à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Mise en route de la langue du lac de données Azure Analytique U-SQL](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL & développement
    - [Mise en route de la langue du lac de données Azure Analytique U-SQL](data-lake-analytics-u-sql-get-started.md)
    - [Utilisez les fonctions de fenêtre U-SQL pour les travaux de Azure données lac Analytique](data-lake-analytics-use-window-functions.md)
    - [Développer les opérateurs U-SQL définie par l’utilisateur pour les tâches de données lac Analytique](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Gestion des
    - [Gérer l’Analytique de LAC de données Azure à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
    - [Gérer l’Analytique de LAC de données Azure à l’aide de PowerShell d’Azure](data-lake-analytics-manage-use-powershell.md)
    - [Surveiller et résoudre les problèmes de travaux Azure données lac Analytique à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
    - [L’accès aux journaux de Diagnostics pour Azure données lac Analytique](data-lake-analytics-diagnostic-logs.md)

- Didacticiel de bout en bout
    - [Utiliser les didacticiels interactifs Azure données lac Analytique](data-lake-analytics-use-interactive-tutorials.md)
    - [Analyser les journaux du site Web à l’aide d’Azure données lac Analytique](data-lake-analytics-analyze-weblogs.md)

- Faites-nous savoir ce que vous pensez
    - [Commentaires sur notre retard de documentation](data-lake-analytics-documentation-backlog.md)
    - [Soumettre une demande de fonctionnalité](http://aka.ms/adlafeedback)
    - [Obtenir de l’aide dans les forums](http://aka.ms/adlaforums)


