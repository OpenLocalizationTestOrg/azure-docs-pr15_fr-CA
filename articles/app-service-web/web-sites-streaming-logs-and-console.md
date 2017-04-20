<properties 
    pageTitle="Diffusion en continu de journaux et la console" 
    description="Diffusion en continu de journaux et présentation de la console" 
    authors="btardif" 
    manager="wpickett" 
    editor="" 
    services="app-service\web" 
    documentationCenter=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="byvinyal"/>

# <a name="streaming-logs-and-the-console"></a>Diffusion en continu de journaux et la Console

## <a name="streaming-logs"></a>Journaux de transmission en continu

Le **portail Azure** fournit une visionneuse du journal en continu intégrée qui vous permet d’afficher les événements de suivi à partir de vos applications de **Service de l’application** en temps réel.  

Configuration de cette fonctionnalité requiert quelques étapes simples :

- Écrire les traces dans votre code
- Activer l’Application **des journaux de Diagnostic** pour votre application
- Permet d’afficher le flux de données à partir de l’interface utilisateur **En flux continu de journaux** intégrée dans le **portail Azure**.

### <a name="how-to-write-traces-in-your-code"></a>L’écriture de traces dans votre code. ###

Il est facile d’écrire les traces dans votre code.  Dans C#, il est aussi simple que d’écrire le code suivant :

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

La classe Trace se trouve dans l’espace de noms System.Diagnostics.

Dans une application node.js, vous pouvez écrire ce code pour obtenir le même résultat :

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Comment activer et afficher la diffusion en continu se connecte.
![][BrowseSitesScreenshot]Tests de diagnostic sont activés sur une base par l’application. Commencez par parcourir le site que vous souhaitez activer cette fonctionnalité.  
  
![][DiagnosticsLogs]À partir du menu Paramètres, faites défiler jusqu'à la section **analyse** et cliquez sur **Journaux de Diagnostic (1)**. Puis **Activer (2)** , **L’Application enregistre (système de fichiers)** ou **L’Application enregistre (blob)** l’option **niveau** vous permet de modifier le niveau de gravité de traces pour capturer. Si vous essayez simplement de vous familiariser avec la fonctionnalité, définissez le niveau de **commentaires** pour s’assurer que toutes vos instructions de traçage sont collectées.

Cliquez sur **Enregistrer** en haut de la lame et vous êtes prêt afficher les journaux.

>[AZURE.NOTE] Plus le **niveau de gravité** les plus de ressources sont consommées au journal et les plus suivis sont produits. Assurez-vous que le **niveau de gravité** est configuré sur le niveau de détail approprié pour une production ou un site de trafic élevé. 

![][StreamingLogsScreenshot]Pour afficher les **journaux de diffusion en continu** à partir au sein du portail Azure, cliquez sur **flux de journal (1)** , également dans la section **analyse** du menu Paramètres. Si votre application écrit activement des instructions de traçage, vous devez les voir dans la **diffusion en continu (2) connecte à l’interface utilisateur** en temps quasi réel.

## <a name="console"></a>Console
Le **portail Azure** fournit un accès à votre application. Vous pouvez explorer le système de fichiers de votre application et exécuter des scripts powershell/cmd. Vous êtes lié par les mêmes autorisations que votre code d’application en cours d’exécution lors de l’exécution de commandes de la console. Accès à des répertoires protégés ou exécute des scripts qui nécessitent des autorisations élevées est bloqué.  

![][ConsoleScreenshot]À partir du menu Paramètres, faites défiler jusqu'à la section **Outils de développement** et cliquez sur **Console (1)** et la **console (2)** de l’interface utilisateur s’affiche à droite.

Pour vous familiariser avec la **console**, essayez les commandes de base telles que :

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
