<properties
   pageTitle="Installation de Visual Studio et SSDT pour SQL Data Warehouse | Microsoft Azure"
   description="Installation de Visual Studio et les outils de développement SQL Server (SSDT) pour l’entrepôt de données SQL Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Installer Visual Studio 2015 et SSDT pour l’entrepôt de données SQL

Pour développer des applications pour l’entrepôt de données SQL, nous vous recommandons d’à l’aide de Visual Studio 2015 avec la version la plus récente de SQL Server données outils (SSDT).  Visual Studio 2013 mise à jour 5 avec SSDT est également prise en charge pour la compatibilité descendante.  

À l’aide de Visual Studio avec SSDT vous permettra d’utiliser l’Explorateur d’objets SQL Server à visuellement Explorer les tables, vues, procédures stockées et beaucoup plus d’objets dans votre magasin de données SQL ainsi qu’exécuter des requêtes.

> [AZURE.NOTE] SQL Data Warehouse ne prend pas en charge les projets de base de données de Visual Studio.  Cette fonctionnalité sera ajoutée dans une version future.

## <a name="step-1-install-visual-studio-2015"></a>Étape 1 : Installer Visual Studio 2015

Cliquez sur ces liens pour télécharger et installer Visual Studio 2015. Si vous avez déjà Visual Studio 2013 ou 2015 installé, vous pouvez passer à l’étape 2, installation de SSDT.

1. [Télécharger Visual Studio 2015][].
2. Suivez le guide [d’Installation de Visual Studio][] sur MSDN et choisissez les configurations par défaut.

## <a name="step-2-install-ssdt"></a>Étape 2 : Installer SSDT

Pour installer SSDT pour Visual Studio simplement vérifier pour une mise à jour SSDT à partir de Visual Studio en suivant ces étapes.

1. Dans Visual Studio, cliquez sur **Outils** / **les Extensions et les mises à jour...**  /  **Mises à jour**
2. Sélectionnez les **Mises à jour de produit** et recherchez des **Outils de mise à jour de Microsoft SQL Server pour la base de données**

Si une mise à jour n’est pas trouvé, vous devez avoir installé la version la plus récente.  Pour confirmer la SSDT est installé, cliquez sur **aide de** / **à propos de Microsoft Visual Studio** et recherchez des outils de données de SQL Server dans la liste.  La dernière version de SSDT est 14.0.60525.0.  Si l’option d’installation n’est pas disponible à partir de Visual Studio, vous pouvez également visiter la page de [Téléchargement de SSDT][] pour télécharger et installer manuellement les SSDT.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez la dernière version de SSDT, vous êtes prêt à [vous connecter][] à votre magasin de données SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[se connecter]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Télécharger Visual Studio 2015]: https://www.visualstudio.com/downloads/
[L’installation de Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Téléchargement SSDT]: https://msdn.microsoft.com/library/mt204009.aspx
