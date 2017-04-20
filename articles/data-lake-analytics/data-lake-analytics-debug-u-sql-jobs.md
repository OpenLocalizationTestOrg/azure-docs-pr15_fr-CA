<properties 
   pageTitle="Déboguer les travaux SQL-U | Microsoft Azure" 
   description="Découvrez comment déboguer les vertex échec U-SQL à l’aide de Visual Studio. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>



#<a name="debug-c-code-in-u-sql-for-data-lake-analytics-jobs"></a>Déboguer le code C# dans U-SQL pour les tâches de données lac Analytique 

Apprenez à utiliser les outils de Visual Studio du lac données Azure pour déboguer des travaux U-SQL ayant échoué à cause de bogues dans le code de l’utilisateur. 

L’outil Visual Studio vous permet de télécharger le code compilé et les données de vertex nécessaire du cluster au traçage et de débogage des travaux ayant échoué.

Systèmes de données fournissent généralement le modèle d’extensibilité via des langages tels que Java, C#, Python, etc.. Grand nombre de ces systèmes fournit des informations, ce qui le rend difficile à déboguer les erreurs d’exécution dans le code personnalisé de débogage du runtime limité. Les derniers outils de Visual Studio est fourni avec une fonctionnalité appelée « Échec de Vertex Debug ». À l’aide de cette fonctionnalité, vous pouvez télécharger les données d’exécution à partir d’Azure pour station de travail locale à afin que vous puissiez déboguer échec C# code personnalisé en utilisant le même runtime et exacte des données d’entrée à partir du cloud.  Une fois les problèmes résolus, vous pouvez réexécuter le code révisé dans Azure à partir des outils.

Pour une présentation vidéo de cette fonctionnalité, reportez-vous à [Déboguer votre code personnalisé dans Azure données lac Analytique](https://mix.office.com/watch/1bt17ibztohcb).

>[AZURE.NOTE] Visual Studio peut se bloquer ou se bloquer si vous n’avez pas les mises à niveau de deux fenêtres suivantes : [Microsoft Visual C++ 2015 redistribuable mise à jour 2](https://www.microsoft.com/download/details.aspx?id=51682), [Universal Runtime C pour Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).


##<a name="prerequisites"></a>Conditions préalables
-   Ont subi de l’article [mise en route](data-lake-analytics-data-lake-tools-get-started.md) .

## <a name="create-and-configure-debug-projects"></a>Créer et configurer des projets de débogage

Lorsque vous ouvrez une tâche ayant échouée dans l’outil de Visual Studio de données lac, vous obtiendrez une alerte. Les informations d’erreur détaillées seront affichera dans l’onglet de l’erreur et la barre d’alerte jaune en haut de la fenêtre. 

![Sommet du téléchargement de visual studio du débogage Analytique de LAC données U-SQL Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**Pour télécharger le sommet et créer une solution de débogage**

1.  Ouvrez l’échec d’un travail U-SQL dans Visual Studio.
2.  Cliquez sur **Télécharger** pour télécharger toutes les ressources requises et les flux d’entrée. Si le téléchargement a échoué, cliquez sur **Réessayer** .
3.  Lorsque le téléchargement est terminé pour créer un projet de débogage local, cliquez sur **Ouvrir** . Une nouvelle solution Visual Studio appelée **VertexDebug** avec un projet vide appelé **LocalVertexHost** est créée.

Si les opérateurs définis par l’utilisateur sont utilisées dans le code SQL-U (Script.usql.cs), vous devez créer un projet C# de bibliothèque de classe avec le code d’opérateurs définis par l’utilisateur et inclure le projet dans la VertexDebug Solution.

Si vous avez inscrit les assemblys .dll à votre base de données Analytique de LAC de données, vous devez ajouter le code source des assemblys à la VertexDebug Solution.
 
Si vous avez créé distinct C# bibliothèque de classes pour votre code de U-SQL et les assemblys .dll inscrits à la base de données lac Analytique, vous devez ajouter le code source C# projet des assemblys à la VertexDebug Solution.

Dans certains cas rares, vous utilisez les opérateurs définis par l’utilisateur dans SQL-U fichier code-behind (Script.usql.cs) dans la solution d’origine. Si vous souhaitez qu’il fonctionne, vous devez créer une bibliothèque C# contenant le code source et modifier le nom de l’assembly à celui enregistré dans le cluster. Vous pouvez obtenir le nom d’assembly enregistré dans le cluster en vérifiant le script qui est en cours d’exécution dans le cluster. Vous pouvez le faire en ouvrant la tâche SQL-U et cliquez sur « script » dans le panneau projet. 

**Configuration de la solution**

1.  À partir de l’Explorateur de solutions, droit sur le projet C#, que vous venez de créer, puis cliquez sur **Propriétés**.
2.  Définir le chemin d’accès de la sortie en tant que projet LocalVertexHost chemin de répertoire de travail. Vous pouvez obtenir le chemin d’accès du répertoire de travail de projet LocalVertexHost par l’intermédiaire de propriétés de LocalVertexHost.
3.  Générez votre projet C# afin de placer le fichier .pdb dans le répertoire de travail du projet LocalVertexHost, ou vous pouvez copier manuellement le fichier .pdb dans ce dossier.
4.  Dans les **Paramètres d’Exception**, vérifiez les Exceptions Common Language Runtime :

![Paramètres de visual studio de débogage Analytique de LAC données U-SQL Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##<a name="debug-the-job"></a>Déboguer le projet.

Une fois que vous avez a créé une solution de débogage en téléchargeant le sommet et que vous avez configuré l’environnement, vous pouvez démarrer le débogage de votre code SQL-U.

1.  À partir de l’Explorateur de solutions, cliquez sur le projet **LocalVertexHost** que vous venez de créer, pointez sur **Déboguer**, puis cliquez sur **Démarrer une nouvelle instance**. Le LocalVertexHost doit être défini comme projet de démarrage. Le message suivant peut s’afficher pour la première fois que vous pouvez l’ignorer. Il peut prendre jusqu'à une minute pour accéder à l’écran de débogage.
 
    ![Avertissement de visual studio de débogage Analytique de LAC données U-SQL Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.  Visual Studio d’utiliser selon l’expérience de débogage (espion, variables, etc.) pour résoudre le problème. 
5.  Une fois que vous avez identifié un problème, corrigez le code et puis reconstruire le projet C# avant de tester à nouveau jusqu'à ce que tous les problèmes sont résolus. Une fois le débogage a été effectuée correctement, la fenêtre Sortie affiche le message suivant 

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##<a name="resubmit-the-job"></a>Le soumettre

Une fois que vous avez terminé de déboguer votre code SQL-U, vous pouvez soumettre à nouveau travail ayant échoué.

1. Inscrire les nouveaux assemblys .dll dans votre base de données ADLA.

    1.  Explorateur de serveurs/nuage de Explorer dans l’outil données lac de Visual Studio, développez le nœud **bases de données** 
    2.  Droit des assemblys pour les assemblys de Registre. 
    3.  Enregistrer vos nouveaux assemblys .dll à la base de données ADLA.
 
2.  Ou copiez votre code C# pour script.usql.cs--C# fichier code-behind.
3.  Soumettez à nouveau votre travail.

##<a name="next-steps"></a>Étapes suivantes

- [Didacticiel : Mise en route de la langue du lac de données Azure Analytique U-SQL](data-lake-analytics-u-sql-get-started.md)
- [Didacticiel : développer des scripts de U-SQL à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Développer les opérateurs U-SQL définie par l’utilisateur pour les travaux d’Azure données lac Analytique](data-lake-analytics-u-sql-develop-user-defined-operators.md)

