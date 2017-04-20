<properties
    pageTitle="Votre application de test de charge à l’aide de Visual Studio Team Services | Microsoft Azure"
    description="Apprenez à test de stress vos applications Azure Fabric de Service à l’aide de Visual Studio Team Services."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Votre application de test de charge à l’aide de Visual Studio Team Services

Cet article explique comment utiliser les fonctionnalités de test de charge Visual Studio de Microsoft pour le test de contrainte à une application. Il utilise un back-end Azure Fabric de Service service dynamique et un service sans état web frontal. L’exemple d’application utilisé ici est un simulateur d’emplacement avion. Vous fournir un ID de l’avion, le heure de départ et la destination. Back-end de l’application traite les demandes, et le front-end affiche sur une carte qui correspond aux critères de l’avion.

Le diagramme suivant illustre l’application de Service Fabric que vous allez tester.

![Diagramme de l’exemple d’application emplacement avion][0]

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer, vous devez effectuer les opérations suivantes :

- Obtenir un compte de Services d’équipe Visual Studio. Vous pouvez en obtenir un gratuitement à [Visual Studio Team Services](https://www.visualstudio.com).
- Obtenir et installer Visual Studio 2013 ou 2015 de Visual Studio. Cet article utilise l’édition Visual Studio Enterprise de 2015, mais Visual Studio 2013 et autres éditions devraient fonctionner de la même façon.
- Déployer votre application dans un environnement de test. Pour plus d’informations à ce sujet, reportez-vous à la section [comment déployer des applications sur un cluster distant à l’aide de Visual Studio](service-fabric-publish-app-remote-cluster.md) .
- Comprendre le modèle d’utilisation de votre application. Ces informations sont utilisées pour simuler le modèle de charge.
- Comprendre l’objectif de votre test de charge. Cela vous permet d’interpréter et d’analyser les résultats de test de charge.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Créer et exécuter le projet de Test de charge et de performances de site Web

### <a name="create-a-web-performance-and-load-test-project"></a>Créez un projet de Test de charge et de performances de site Web

1. Ouvrez Visual Studio 2015. Choisissez la commande **fichier** > **Nouveau** > le**projet** dans la barre de menu pour ouvrir la boîte de dialogue **Nouveau projet** .

2. Développez le nœud **Visual C#** , cliquez sur **Test** > **projet de Test de charge et de performances de site Web**. Donnez un nom au projet, puis choisissez le bouton **OK** .

    ![Capture d’écran de la boîte de dialogue Nouveau projet][1]

    Vous devriez voir un nouveau projet de Test de charge et de performances de site Web dans l’Explorateur de solutions.

    ![Capture d’écran de l’Explorateur de solutions affiche le nouveau projet][2]

### <a name="record-a-web-performance-test"></a>Enregistrement d’un test de performances de site web

1. Ouvrez le projet .webtest.

2. Cliquez sur l’icône **Ajouter l’enregistrement** pour démarrer une session d’enregistrement dans votre navigateur.

    ![Capture d’écran de l’icône Ajouter un enregistrement dans un navigateur][3]

    ![Capture d’écran du bouton Enregistrer dans un navigateur][4]

3. Accédez à l’application de Service Fabric. Le panneau d’enregistrement doit afficher les requêtes web.

    ![Capture d’écran de requêtes web dans le panneau d’enregistrement][5]

4. Effectuer une série d’actions que vous comptez les utilisateurs à effectuer. Ces actions sont utilisées comme modèle pour générer la charge.

5. Lorsque vous avez terminé, cliquez sur le bouton **Arrêter** pour arrêter l’enregistrement.

    ![Capture d’écran du bouton Arrêter][6]

    Le projet .webtest dans Visual Studio doit avoir capturé une série de requêtes. Les paramètres dynamiques sont remplacés automatiquement. À ce stade, vous pouvez supprimer les demandes de dépendance supplémentaire et répétées qui ne font pas partie de votre scénario de test.

6. Enregistrez le projet, puis choisissez la commande **Exécuter les tests** à exécuter le test de performances web localement et vous assurer que tout fonctionne correctement.

    ![Capture d’écran de la commande Exécuter le Test][7]

### <a name="parameterize-the-web-performance-test"></a>Paramétrer le test de performances de site web

Vous pouvez paramétrer le test de performances de site web en le convertissant en un test de performances de site web codé, puis en modifiant le code. Comme alternative, vous pouvez lier le test de performances de site web à une liste de données afin que le test effectue une itération dans les données. Pour plus d’informations sur la façon de convertir le test de performances de site web à un test codé, consultez [Générer et exécuter un test de performances web codé](https://msdn.microsoft.com/library/ms182552.aspx) . Pour plus d’informations sur la façon de lier des données à un test de performances de site web, consultez [Ajouter une source de données de performances de site web de test](https://msdn.microsoft.com/library/ms243142.aspx) .

Pour cet exemple, nous allons convertir le test de performances de site web à un test codé d’afin de remplacer l’ID d’un avion avec un GUID généré et ajouter d’autres requêtes pour envoyer des vols à des emplacements différents.

### <a name="create-a-load-test-project"></a>Créez un projet de test de charge

Un projet de test de charge se compose d’un ou plusieurs scénarios décrits par le test de performances de site web et le test d’unité, ainsi que les paramètres de test de charge spécifié supplémentaire. Les étapes suivantes montrent comment créer un projet de test de charge :

1. Dans le menu contextuel de votre projet de Test de charge et de performances de site Web, cliquez sur **Ajouter** > **Test de charge**. Dans l’Assistant **Test de charge** , cliquez sur le bouton **suivant** pour configurer les paramètres de test.

2. Dans la section **Modèle de charge** , choisissez si vous souhaitez une charge utilisateur constante ou une charge par étape, qui commence par quelques utilisateurs et augmente les utilisateurs au fil du temps.

    Si vous avez une bonne estimation de la quantité de charge de l’utilisateur et que vous souhaitez voir le fonctionnement du système en cours, choisissez **Une charge constante**. Si votre objectif est de savoir si le système effectue régulièrement sous différentes charges, choisissez la **Charge par étape**.

3. Dans la section de **La combinaison de tests** , cliquez sur le bouton **Add** et sélectionnez le test que vous souhaitez inclure dans le test de charge. La colonne **Distribution** vous permet de spécifier le pourcentage du nombre total de tests exécutés pour chaque test.

4. Dans la section **Paramètres d’exécution** , spécifiez la durée du test de charge.

    >[AZURE.NOTE] L’option **d’Itérations de tests** est disponible uniquement lorsque vous exécutez un test de charge localement à l’aide de Visual Studio.

5. Dans la section **emplacement** de **Paramètres d’exécution**, spécifiez l’emplacement dans lequel les requêtes de tests de charge sont générés. L’Assistant peut vous inviter à se connecter à votre compte de Services de l’équipe. Ouvrir une session, puis choisissez un emplacement géographique. Lorsque vous avez terminé, cliquez sur le bouton **Terminer** .

6. Après avoir créé le test de charge, ouvrez le projet .loadtest et choisissez le paramètre, telles que les **Paramètres d’exécution**d’exécution actuel > **exécution1 [actifs]**. Les paramètres d’exécution s’affiche dans la fenêtre **Propriétés** .

7. Dans la section **résultats** de la fenêtre de propriétés de **Paramètres d’exécution** , le paramètre de **Stockage des détails de minuterie** doit avoir la valeur **Aucune** comme valeur par défaut. Modifiez cette valeur pour **Tous les détails individuels** pour obtenir plus d’informations sur le chargement des résultats de test. Pour plus d’informations sur la façon de se connecter à Visual Studio Team Services et d’exécuter un test de charge, consultez la section [Chargement de test](https://www.visualstudio.com/load-testing.aspx) .

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Exécutez le test de charge à l’aide de Visual Studio Team Services

Choisissez la commande **Exécuter le Test de charge** pour démarrer le test à exécuter.

![Capture d’écran de la commande Exécuter un Test de charge][8]

## <a name="view-and-analyze-the-load-test-results"></a>Permet d’afficher et d’analyser les résultats de test de charge

Comme la charge test progresse, représenter graphiquement les informations sur les performances. Vous devriez voir quelque chose de similaire à la courbe suivante.

![Capture d’écran de graphiques de performances pour les résultats de test de charge][9]

1. Cliquez sur le lien **Télécharger le rapport** vers le haut de la page. Une fois que le rapport est téléchargé, cliquez sur le bouton **Afficher le rapport** .

    Dans l’onglet **graphique** , vous pouvez voir graphiques différents compteurs de performance. Sous l’onglet **Résumé** , les résultats du test s’affichent. L’onglet **Tables** indique le nombre total de tests de charge réussites et les échecs.

2. Cliquez sur les liens de numéro sur le **Test**de > **a échoué** et les **erreurs** > **nombre** de colonnes pour afficher les détails de l’erreur.

    L’onglet **Détail** affiche les informations virtuelles du scénario d’utilisateur et de test pour les demandes qui ont échoué. Ces données peuvent être utiles si le test de charge contient plusieurs scénarios.

Pour plus d’informations sur l’affichage des résultats de test de charge, reportez-vous à la section [Analyse Load Test Results dans la vue graphiques de l’Analyseur de Test de charge](https://www.visualstudio.com/load-testing.aspx) .

## <a name="automate-your-load-test"></a>Automatiser votre test de charge

Test de charge de Visual Studio Team Services fournit des API pour vous aider à gérer les tests de charge et d’analyser les résultats dans un compte de Services de l’équipe. Pour plus d’informations, reportez-vous à la section [API des reste de test de charge de nuage](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) .

## <a name="next-steps"></a>Étapes suivantes
- [Surveillance et diagnostic des services dans une configuration de développement local machine](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
