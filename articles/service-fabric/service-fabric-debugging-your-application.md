<properties
   pageTitle="Déboguez votre application dans Visual Studio | Microsoft Azure"
   description="Améliorer la fiabilité et les performances de vos services en développant et en leur débogage dans Visual Studio sur un cluster de développement local."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="vturecek;mikhegn"/>

# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Déboguer votre application de Service Fabric à l’aide de Visual Studio

## <a name="debug-a-local-service-fabric-application"></a>Déboguer une application de tissu de Service locale

Vous gagnerez du temps et argent par le déploiement et le débogage de votre application Azure Fabric de Service dans un cluster de développement d’ordinateur local. Visual Studio peut déployer l’application sur le cluster local et il relie automatiquement le débogueur pour toutes les instances de votre application.

1. Démarrer un cluster de développement local en suivant les étapes de [configuration de votre environnement de développement de Service Fabric](service-fabric-get-started.md).

2. Appuyez sur **F5** ou cliquez sur **Debug** > **Démarrer le débogage**.

    ![Démarrer le débogage d’une application][startdebugging]

3. Définir des points d’arrêt dans votre code et l’étape par l’intermédiaire de l’application en cliquant sur les commandes dans le menu **Déboguer** .

    > [AZURE.NOTE] Visual Studio s’attache à toutes les instances de votre application. Pendant que vous êtes une exécution pas à pas à travers le code, les points d’arrêt peuvent obtenir atteints par plusieurs processus entraînant des sessions simultanées. Essayez de désactiver les points d’arrêt une fois qu’ils sont atteint, en rendant chaque point d’arrêt conditionnel sur l’ID de thread ou en utilisant des événements de diagnostic.

4. La fenêtre **Des événements de Diagnostic** s’ouvre automatiquement afin d’afficher des événements de diagnostic en temps réel.

    ![Afficher les événements de diagnostic en temps réel][diagnosticevents]

5. Vous pouvez également ouvrir la fenêtre **Des événements de Diagnostic** dans l’Explorateur de nuage.  Sous **Service de Fabric**, avec le bouton droit à n’importe quel nœud, puis choisissez les **Traces de diffusion en continu de vue**.

    ![Ouvrez la fenêtre des événements de diagnostic][viewdiagnosticevents]

    Si vous souhaitez filtrer vos traces à une application ou un service spécifique, il suffit d’activer les traces en continu sur ce service spécifique ou d’une application.

6. Les événements de diagnostic peuvent être vus dans le fichier **ServiceEventSource.cs** généré automatiquement et sont appelées à partir de code d’application.

    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```

7. La fenêtre **Des événements de Diagnostic** prend en charge le filtrage, la suspension et inspecter des événements en temps réel.  Le filtre est une recherche simple chaîne du message d’événement, y compris son contenu.

    ![Filtrer, de suspendre et de reprendre ou d’examiner des événements en temps réel][diagnosticeventsactions]

8. Les services de débogage sont identique au débogage d’une autre application. Normalement, vous définirez les points d’arrêt dans Visual Studio pour le débogage facile. Bien que les Collections fiable répliquer sur plusieurs nœuds, ils toujours d’implémenter IEnumerable. Cela signifie que vous pouvez utiliser l’affichage des résultats dans Visual Studio pendant le débogage pour voir ce que vous avez stocké à l’intérieur. Définissez simplement un point d’arrêt n’importe où dans votre code.

    ![Démarrer le débogage d’une application][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Déboguer une application de Service Fabric à distance

Si vos applications Fabric de Service sont en cours d’exécution sur un cluster Service Fabric dans Azure, vous ne pouvez déboguer à distance ces, directement à partir de Visual Studio.

> [AZURE.NOTE] La fonctionnalité requiert le [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) et [Azure SDK pour .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [AZURE.WARNING] Le débogage distant est conçu pour les scénarios de développement/test et ne pas pour être utilisée dans des environnements de production, en raison de l’impact sur les applications en cours d’exécution.

1. Accédez à votre cluster dans **l’Explorateur de nuage**, avec le bouton droit et choisissez **Activer le débogage**

    ![Activer le débogage distant][enableremotedebugging]

    Ce sera coup le processus d’activation de l’extension de débogage à distance sur vos nœuds de cluster, ainsi que les configurations de réseau requis.

2. Cliquez sur le nœud de cluster dans le **Cloud Explorer**et choisissez **Joindre le débogueur**

    ![Attacher le débogueur][attachdebugger]

3. Dans la boîte de dialogue **Attacher au processus** , sélectionnez le processus que vous souhaitez déboguer, puis cliquez sur **attacher**

    ![Choisissez le processus][chooseprocess]

    Le nom du processus que vous souhaitez joindre, est égal à celui du nom de votre assembly de projet service.

    Le débogueur s’attache à tous les nœuds du processus en cours d’exécution.
    - Dans le cas où vous déboguez un service sans état, toutes les instances du service sur tous les nœuds font partie de la session de débogage.
    - Si vous déboguez un service dynamique, seul le réplica principal de n’importe quelle partition sera active et par conséquent capturées par le débogueur. Si le réplica principal se déplace au cours de la session de débogage, le traitement de ce réplica sera toujours partie de la session de débogage.
    - Pour intercepter uniquement les partitions pertinentes ou des instances d’un service donné, vous pouvez utiliser des points d’arrêt conditionnels de ne s’arrêter une partition spécifique ou une instance.

    ![Point d’arrêt conditionnel][conditionalbreakpoint]

    > [AZURE.NOTE] Actuellement nous ne prennent pas en charge un cluster Service Fabric avec plusieurs instances du même nom service exécutable de débogage.

4. Une fois que vous avez terminé de déboguer votre application, vous pouvez désactiver l’extension de débogage à distance en double-cliquant sur le cluster de **Cloud Explorer** et choisissez **Désactiver le débogage**

    ![Désactiver le débogage à distance][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Traces de diffusion en continu à partir d’un nœud de cluster distant

Vous pouvez également sur les traces de flux de données directement à partir d’un nœud de cluster distant à Visual Studio. Cette fonctionnalité vous permet des événements de suivi de flux ETW, produits sur un nœud de cluster Service Fabric, directement dans Visual Studio.

> [AZURE.NOTE] La fonctionnalité requiert le [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) et [Azure SDK pour .NET 2.9](https://azure.microsoft.com/downloads/).

<!-- -->
> [AZURE.WARNING]Traces de diffusion en continu est conçue pour les scénarios de développement/test et ne pas pour être utilisée dans des environnements de production, en raison de l’impact sur les applications en cours d’exécution.
> Dans un scénario de production, vous devez compter sur le transfert des événements à l’aide des Diagnostics d’Azure.

1. Accédez à votre cluster dans **l’Explorateur de nuage**, avec le bouton droit et choisissez **Activer les Traces de diffusion en continu**

    ![Activer les traces de diffusion en continu à distance][enablestreamingtraces]

    Ce sera coup le processus d’activation de l’extension de traces en continu sur vos nœuds de cluster, ainsi que les configurations de réseau requis.

2. Développez l’élément de **nœuds** dans le **Cloud Explorer**, cliquez sur le nœud que vous souhaitez à traces à partir de flux et de choisir des **Traces de diffusion en continu de vue**

    ![Affichage à distance en continu des traces][viewremotestreamingtraces]

    Répétez l’étape 2 pour autant de nœuds que vous souhaitez voir à partir de traces. Chaque flux de nœuds s’afficheront dans une fenêtre dédiée.

    Vous êtes maintenant en mesure de voir les traces émis par le Service de Fabric et vos services. Si vous souhaitez filtrer les événements pour afficher uniquement une application spécifique, tapez simplement le nom de l’application du filtre.

    ![Affichage en continu de traces][viewingstreamingtraces]

4. Une fois que vous avez terminé les traces de transmission en continu de votre cluster, vous pouvez désactiver des traces de diffusion en continu à distance, en double-cliquant sur le cluster dans **l’Explorateur de nuage** et choisissez **Désactiver les Traces de diffusion en continu**

    ![Désactiver des traces de diffusion en continu à distance][disablestreamingtraces]

## <a name="next-steps"></a>Étapes suivantes

- [Test d’un service de Fabric du Service](service-fabric-testability-overview.md).
- [Gestion de vos applications de Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
