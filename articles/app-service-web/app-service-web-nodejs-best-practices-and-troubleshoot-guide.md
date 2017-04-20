<properties
    pageTitle="Meilleures pratiques et guide de dépannage pour les applications de nœud sur Azure Web Apps"
    description="Découvrez les meilleures pratiques et les étapes de dépannage pour les applications de nœud sur Azure Web Apps."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="ranjithr"
    manager="wadeh"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="06/06/2016"
    ms.author="ranjithr;wadeh"/>
    
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Meilleures pratiques et guide de dépannage pour les applications de nœud sur Azure Web Apps

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Dans cet article, vous apprendrez les meilleures pratiques et les étapes de dépannage pour les [applications du nœud](app-service-web-nodejs-get-started.md) en cours d’exécution sur Azure WebApp (avec [iisnode](https://github.com/azure/iisnode)).

>[AZURE.WARNING] Soyez prudent lorsque vous utilisez les étapes de dépannage sur votre site de production. Il est vivement recommandé de dépanner votre application sur une installation de production non par exemple votre emplacement de zone de transit et lorsque le problème est résolu, remplacez votre emplacement intermédiaire avec votre emplacement de production.

## <a name="iisnode-configuration"></a>Configuration de IISNODE

Ce [fichier de schéma](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) montre tous les paramètres qui peuvent être configurés pour iisnode. Certains des paramètres qui seront utiles pour votre application sont les suivantes :

* nodeProcessCountPerApplication

    Ce paramètre contrôle le nombre de processus de nœud qui sont lancés par une application IIS. Valeur par défaut est 1. Vous pouvez lancer autant de node.exe en tant que votre nombre de noyau VM par la définition de ce paramètre sur 0. La valeur recommandée est 0 pour la plupart des applications, vous pouvez utiliser tous les cœurs sur votre ordinateur. Node.exe est single threaded un node.exe va consommer un maximum de 1 noyau et à optimiser les performances de votre application de nœud que vous ne souhaitez pas utiliser tous les cœurs.

* nodeProcessCommandLine

    Ce paramètre détermine le chemin d’accès à la node.exe. Vous pouvez définir cette valeur pour pointer vers la version node.exe.

* maxConcurrentRequestsPerProcess

    Ce paramètre contrôle le nombre maximal de demandes simultanées envoyé par iisnode pour chaque node.exe. Sur azure WebApp, la valeur par défaut est infinie. Vous ne devez pas à vous soucier de ce paramètre. En dehors d’azure WebApp, la valeur par défaut est 1024. Vous pouvez souhaiter configurer selon le nombre de requêtes qu'obtienne de votre application et de la vitesse à laquelle votre application traite chaque demande.

* maxNamedPipeConnectionRetry

    Ce paramètre contrôle le nombre maximal de tentatives iisnode sera en connexion sur le canal de communication nommé pour envoyer la demande sur à node.exe. Ce paramètre en association avec namedPipeConnectionRetryDelay détermine le délai d’expiration total de chaque demande dans iisnode. Valeur par défaut est 200 sur Azure WebApp. Nombre total de délai d’attente en secondes = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

* namedPipeConnectionRetryDelay

    Ce paramètre contrôle la quantité de temps (en ms) iisnode attend entre chaque tentative d’envoyer la demande à node.exe sur le canal nommé. Valeur par défaut est 250ms.
    Nombre total de délai d’attente en secondes = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

    Par défaut, le délai d’expiration total d’iisnode sur les applications Web azure est 200 \* 250ms = 50 secondes.

* logDirectory

    Ce paramètre contrôle le répertoire dans lequel iisnode se connecteront stdout/stderr. Valeur par défaut est iisnode, qui est relatif au répertoire du script principal (répertoire dans lequel server.js principal est présent)

* debuggerExtensionDll

    Ce paramètre contrôle la version de nœud-inspecteur iisnode utiliser lors du débogage de votre application de nœud. Actuellement inspecteur-iisnode-0.7.3.dll et iisnode-inspector.dll sont les seuls 2 valeurs valides pour ce paramètre. Valeur par défaut est inspecteur-iisnode-0.7.3.dll. version du iisnode-0.7.3.dll-inspecteur utilise le nœud-inspecteur-0.7.3 et utilise le protocole WebSocket, vous devez activer le protocole WebSocket sur votre webapp azure pour utiliser cette version. Consultez <http://www.ranjithr.com/?p=98> pour plus de détails sur la configuration iisnode pour utiliser le nouvel inspecteur de nœud.

* flushResponse

    Le comportement par défaut de IIS est qu’il met en mémoire tampon des données de réponse à 4 Mo avant de vider ou jusqu'à la fin de la réponse, selon ce qui se produit en premier. iisnode offre un paramètre de configuration pour substituer ce comportement : pour vider un fragment du corps d’entité réponse dès qu’iisnode qu’il reçoit de node.exe, vous devez définir le iisnode/@flushResponse l’attribut dans le fichier web.config à 'true' :
    
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```

    L’activation de la consommation de chaque fragment du corps d’entité réponse ajoute une surcharge de performance qui réduit le débit du système de ~ 5 % (à partir de v0.1.13), il vaut donc mieux à la portée de ce paramètre qu’aux points de terminaison qui requièrent la transmission en continu de la réponse (par exemple, à l’aide de la <location> élément dans le fichier web.config)

    En outre, pour la diffusion en continu des applications, vous devez également définir le responseBufferLimit de votre gestionnaire d’iisnode sur 0.
    
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```

* watchedFiles

    Voici la liste des fichiers qui seront surveillés pour modifications séparés par des points-virgules. Une modification apportée à un fichier, l’application à la Corbeille. Chaque entrée se compose d’un nom de répertoire facultatif plus d’un nom de fichier requis qui sont relatifs au répertoire où se trouve le point d’entrée principal d’application. Les caractères génériques sont autorisés dans la partie de nom de fichier uniquement. La valeur par défaut est "\*. js;web.config »

* recycleSignalEnabled

    Valeur par défaut est false. Si activée, votre application de nœud peut se connecter à un canal nommé (variable d’environnement IISNODE\_contrôle\_tuyau) et envoyer un message de « recyclage ». Cela entraînera le w3wp à recycler de manière appropriée.

* idlePageOutTimePeriod

    Valeur par défaut est 0, qui signifie que cette fonctionnalité est désactivée. Lorsque la valeur à une valeur supérieure à 0, iisnode sera la page à tous ses processus enfants toutes les millisecondes 'idlePageOutTimePeriod'. Pour comprendre dans quelle page des moyens, veuillez vous reporter à cette [documentation](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx). Ce paramètre est utile que pour les applications qui consomment beaucoup de mémoire et souhaitez parfois pageout de mémoire sur le disque pour libérer de la mémoire RAM.

>[AZURE.WARNING] Soyez prudent lors de l’activation des paramètres de configuration suivants sur les applications de production. Il est recommandé de ne pas activer les sur les applications de production en direct.

* debugHeaderEnabled

    La valeur par défaut est false. Si la valeur true, iisnode ajoute un en-tête de réponse HTTP iisnode-debug à chaque réponse HTTP, il envoie que la valeur d’en-tête d’iisnode-debug est une URL. Les éléments individuels d’informations de diagnostic peuvent être acquise en examinant le fragment d’URL, mais une quantité une meilleure visualisation est obtenue en ouvrant l’URL dans le navigateur.

* loggingEnabled

    Ce paramètre contrôle l’enregistrement de stdout et stderr par iisnode. Iisnode va capturer stdout/stderr à partir de processus de nœud qu’il lance et écrire dans le répertoire spécifié dans le paramètre 'logDirectory'. Une fois que cela est activée, votre application doit écrire les journaux en fonction de la quantité de journalisation faite l’application et le système de fichiers, il peut y avoir des répercussions sur les performances.

* devErrorsEnabled

    Valeur par défaut est false. Lorsque la valeur true, iisnode affiche le code d’état HTTP et le code d’erreur Win32 sur votre navigateur. Le code win32 sera utile dans le débogage de certains types de problèmes.
    
* debuggingEnabled (n’activez pas sur le site de production en direct)

    Ce paramètre contrôle la fonctionnalité de débogage. Iisnode est intégré avec l’inspecteur-nœud. En activant ce paramètre, vous activez le débogage de votre application de nœud. Une fois que ce paramètre est activé, iisnode sera à disposition les fichiers d’inspecteur-nœud nécessaires dans le répertoire de 'debuggerVirtualDir' à la première demande de débogage à votre application de nœud. Vous pouvez charger l’inspecteur de nœud en envoyant une demande à http://yoursite/server.js/debug. Vous pouvez contrôler le segment d’URL de débogage avec le paramètre de 'debuggerPathSegment'. Par défaut debuggerPathSegment = 'debug'. Vous pouvez définir ceci à un GUID, par exemple afin qu’il soit plus difficile à découvrir par d’autres utilisateurs.

    Vérifiez ce [lien](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) pour plus d’informations sur le débogage.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scénarios et recommandations/dépannage

### <a name="my-node-application-is-making-too-many-outbound-calls"></a>Mon application de nœud effectue trop d’appels sortants.

De nombreuses applications souhaiteraient les connexions sortantes dans le cadre de leur fonctionnement normal. Par exemple, lorsqu’une demande arrive, votre application de nœud souhaiteriez contacter une API REST ailleurs et obtenir des informations pour traiter la requête. Vous pouvez utiliser un agent de keep alive lors d’appels http ou https. Par exemple, vous pouvez utiliser le module agentkeepalive comme votre agent actif conserver lors de l’exécution de ces appels sortants. Cela permet de garantir que les supports sont réutilisés dans votre webapp azure VM et en réduisant la charge de la création de nouveaux sockets pour toutes les demandes sortantes. En outre, cela permet de garantir que vous utilisez un moindre nombre de sockets à adresser de nombreuses demandes sortantes et par conséquent ne pas dépasser la maxSockets allouées par machine virtuelle. Recommandation sur Azure WebApp serait pour définir la valeur de maxSockets agentKeepAlive pour un total de 160 sockets par machine virtuelle. Cela signifie que si vous avez 4 node.exe en cours d’exécution sur l’ordinateur virtuel, vous pourriez définir la maxSockets agentKeepAlive à 40 par node.exe qui est 160 total par machine virtuelle.

Exemple de configuration agentKeepALive :

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

Cet exemple suppose que vous disposez de 4 node.exe en cours d’exécution sur votre ordinateur virtuel. Si vous avez un nombre différent de node.exe en cours d’exécution sur l’ordinateur virtuel, vous devrez modifier le maxSockets définir en conséquence.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>Mon application nœud consomme trop de ressources processeur.

Vous obtiendrez probablement une recommandation d’Azure WebApp sur votre portail sur la consommation d’UC élevée. Vous pouvez également configurer les écrans d’observer certaines [mesures](web-sites-monitor.md). Lors de la vérification de l’utilisation du processeur sur le [Tableau de bord de portail Azure](../application-insights/app-insights-web-monitor-performance.md), vérifiez les valeurs MAX pour l’UC afin que vous ne manquez pas les valeurs de crête.
Dans les cas où vous pensez que votre application consomme trop de ressources processeur, et vous ne pouvez pas expliquer pourquoi, vous devez profiler votre application de nœud.

### 

#### <a name="profiling-your-node-application-on-azure-webapps-with-v8-profiler"></a>Profilage d’application de votre nœud sur azure WebApp avec profileur-V8

Par exemple, supposons que vous avez une application hello world que vous souhaitez profiler comme indiqué ci-dessous :

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Accédez à votre scm site https://yoursite.scm.azurewebsites.net/DebugConsole

Vous verrez une invite de commande, comme indiqué ci-dessous. Allez dans votre répertoire/wwwroot du site

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Exécutez la commande « npm installer v8-profileur »

Il doit installer v8-profileur sous le nœud\_répertoire modules et toutes ses dépendances.
À présent, modifiez votre server.js pour profiler votre application.

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Les modifications ci-dessus va définir le profil de la fonction WriteConsoleLog et puis écrire la sortie de profil vers le fichier 'profile.cpuprofile' sous wwwroot de votre site. Envoyer une demande à votre application. Vous verrez un fichier 'profile.cpuprofile' créé sous wwwroot de votre site.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Télécharger ce fichier, et vous devez ouvrir ce fichier avec les outils de Chrome F12. D’appuyer sur F12 sur chrome, puis cliquez sur l’onglet « Profils ». Cliquez sur le bouton « Charger ». Sélectionnez le fichier profile.cpuprofile que vous venez de télécharger. Cliquez sur le profil que vous venez de télécharger.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Vous verrez que 95 % du temps consommé en fonction de la WriteConsoleLog comme indiqué ci-dessous. Il vous indique également les numéros de ligne exacte et les fichiers source à l’origine du problème.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Mon application nœud consomme trop de mémoire.

Vous obtiendrez probablement une recommandation d’Azure WebApp sur votre portail sur la consommation de mémoire haute. Vous pouvez également configurer les écrans d’observer certaines [mesures](web-sites-monitor.md). Lors de la vérification de l’utilisation de la mémoire sur le [Tableau de bord de portail Azure](../application-insights/app-insights-web-monitor-performance.md), vérifiez les valeurs MAX pour mémoire afin que vous ne manquez pas les valeurs de crête.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>La détection des fuites et comparaison de tas pour node.js 

Vous pouvez utiliser le [nœud-memwatch](https://github.com/lloyd/node-memwatch) pour vous aider à identifier les fuites de mémoire.
Vous pouvez installer memwatch comme v8-Générateur de profils et modifier votre code de capture et de comparaison des fuites de segments pour identifier la mémoire dans votre application.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Mon node.exe mise en route mise à mort au hasard 

Il existe plusieurs raisons pourquoi cela peut se produire :

1.  Votre application est levée d’exceptions non interceptées – Veuillez à cocher d:\\domestique\\fichiers journaux\\Application\\fichier de journalisation-errors.txt pour plus de détails sur l’exception levée. Ce fichier a la trace de la pile pour vous pouvez de résoudre votre application sur cette base.

2.  Votre application consomme trop de mémoire qui affecte les autres processus de mise en route. Si la mémoire virtuelle totale est proche de 100 %, votre node.exe peut être mis à mort par le Gestionnaire de processus aux autres processus vous permettent de vous exercer à effectuer un travail. Pour résoudre ce problème, assurez-vous que votre application n’est pas une fuite de mémoire ou si votre application doit réellement utiliser beaucoup de mémoire, veuillez évoluer vers une plus grande VM avec beaucoup plus de RAM.

### <a name="my-node-application-does-not-start"></a>Mon application de nœud ne démarre pas

Si votre application retourne 500 erreurs au démarrage, peut avoir plusieurs causes :

1.  Node.exe n’est pas présent à l’emplacement correct. Vérifiez le paramètre de nodeProcessCommandLine.

2.  Fichier de script principal n’est pas présent à l’emplacement correct. Web.config et assurez-vous que le nom du fichier de script principal dans la section gestionnaires correspond au fichier de script principal.

3.  Configuration Web.config n’est pas correcte, vérifiez les valeurs/noms de paramètres.

4.  Démarrage à froid – votre application prend trop de temps pour le démarrage. Si votre application met plue de (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 secondes, iisnode renverra une erreur 500. Augmentez les valeurs de ces paramètres pour correspondre à l’heure de début d’application pour empêcher les iisnode d’expiration et retourne une erreur 500.

### <a name="my-node-application-crashed"></a>Mon application de nœud est tombé en panne

Votre application est levée d’exceptions non interceptées – Veuillez à cocher d:\\domestique\\fichiers journaux\\Application\\fichier de journalisation-errors.txt pour plus de détails sur l’exception levée. Ce fichier a la trace de la pile pour vous pouvez de résoudre votre application sur cette base.

### <a name="my-node-application-takes-too-much-time-to-startup-cold-start"></a>Mon application nœud prend trop de temps pour le démarrage (démarrage à froid)

La raison la plus courante pour cela est que l’application dispose d’un grand nombre de fichiers dans le nœud\_modules et l’application tente de charger la plupart de ces fichiers au démarrage. Par défaut, dans la mesure où les fichiers se trouvent sur le partage réseau sur les applications Web d’Azure, chargement de nombreux fichiers peut prendre du temps.
Des solutions pour accélérer ce sont les suivantes :

1.  Assurez-vous de qu'avoir une structure de dépendance plat et sans dépendances en double à l’aide de npm3 pour installer vos modules.

2.  Essayez paresseux charger votre nœud\_modules et pas charger tous les modules au démarrage. Cela signifie que l’appel à require('module') doit être effectuée lorsque vous en avez réellement besoin au sein de la fonction que vous essayez d’utiliser le module.

3.  Applications Web Azure offre une fonctionnalité appelée cache local. Cette fonction copie le contenu à partir du partage réseau sur le disque local sur l’ordinateur virtuel. Dans la mesure où les fichiers sont en local, le temps de chargement du nœud\_modules est beaucoup plus rapide. -Cette [documentation](../app-service/app-service-local-cache.md) explique comment utiliser le Cache Local de façon plus détaillée.

## <a name="iisnode-http-status-and-substatus"></a>Sous-état et état IISNODE http

Ce [fichier source](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) répertorie tous l’iisnode de combinaison possible de statut/sous-état peut renvoyer en cas d’erreur.

Activer FREB pour votre application pour voir le code d’erreur win32 (Vérifiez que vous activez FREB uniquement sur les sites de production non pour des raisons de performances.).

| État HTTP | Sous-état de http | Raison possible ?                                                                                                                                                                                            
|-------------|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------
| 500         | 1000           | Il y a un problème d’envoi de la demande à IISNODE – Vérifiez si node.exe a été démarré. Node.exe pourrait être bloqué au démarrage. Vérifiez votre configuration web.config des erreurs.                                                                                                                                                                                                                                                                                     |
| 500         | 1001           | -Win32Error 0 x 2 - application ne répond pas à l’URL. Vérifiez les règles de réécriture d’URL ou si votre application expresse dispose des itinéraires appropriés définis. -Win32Error 0x6d – le canal nommé est occupé – Node.exe n’accepte pas de demandes, car le canal est occupé. Vérifiez l’utilisation élevée du processeur. -Autres erreurs – Vérifiez si node.exe est tombé en panne.
| 500         | 1002           | Node.exe s’est bloqué, vérifiez d:\\domestique\\fichiers journaux\\errors.txt-l’enregistrement de trace de la pile.                                                                                                                                                                                                                                                                                                                                                                                        |
| 500         | 1003           | Problème de configuration de canal – vous ne devriez jamais voir ceci, mais dans ce cas, la configuration de canal nommé est incorrecte.                                                                                                                                                                                                                                                                                                                                                          |
| 500         | 1004-1018      | Une erreur s’est produite lors de l’envoi de la demande ou le traitement de la réponse vers/à partir de node.exe. Vérifiez si node.exe est tombé en panne. vérifier d:\\domestique\\fichiers journaux\\errors.txt-l’enregistrement de trace de la pile.                                                                                                                                                                                                                                                                                    |
| 503         | 1000           | Pas assez de mémoire pour allouer plus les connexions de canaux nommés. Vérification de la raison pour laquelle votre application consomme beaucoup de mémoire. Vérifiez la valeur du paramètre maxConcurrentRequestsPerProcess. Si pas infini et que vous possédez un grand nombre de demandes, augmentez cette valeur pour éviter cette erreur.                                                                                                                                                                                                                                                                                                                  |
| 503         | 1001           | Demande n’a pas pu être expédié vers node.exe parce que le recyclage de l’application. Une fois que l’application a été recyclée, les demandes doivent être servies normalement.                                                                                                                                                                                                                                                                                                               |
| 503         | 1002           | Impossible de distribuer le code d’erreur win32 à cocher pour connaître la raison – demande à un node.exe.                                                                                                                                                                                                                                                                                                                                                                               |
| 503         | 1003           | Canal de communication nommé est trop occupé – Vérifiez si nœud ne consomme pas beaucoup d’UC                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
Il existe un paramètre appelé nœud de NODE.exe\_en attente\_tuyau\_INSTANCES. Par défaut, en dehors d’azure WebApp, cette valeur est 4. Cela signifie que ce node.exe accepte uniquement les demandes de 4 à la fois sur le canal nommé. Sur les applications Web d’Azure, cette valeur est définie à 5 000, et cette valeur doit être suffisante pour la plupart des applications du nœud en cours d’exécution sur les applications Web azure. Vous ne devriez pas voir 503.1003 sur azure WebApp car nous avons une valeur élevée pour le nœud\_en attente\_tuyau\_INSTANCES.  |

## <a name="more-resources"></a>Plus de ressources

Cliquez sur ces liens pour en savoir plus sur les applications node.js sur le Service d’application Azure.

* [Mise en route avec les applications web Node.js dans le Service d’application Azure](app-service-web-nodejs-get-started.md)
* [Comment faire pour déboguer une application web de Node.js dans le Service d’application Azure](web-sites-nodejs-debug.md)
* [À l’aide de Modules de Node.js avec des applications Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure Application Service Web applications : Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centre de développement Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Exploration de la Console de débogage Ultrasecret Kudu](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)