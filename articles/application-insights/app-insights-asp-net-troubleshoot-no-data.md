<properties 
    pageTitle="Résolution des problèmes d’aucune donnée - perspectives d’Application pour .NET" 
    description="Si vous n’apercevez ne pas de données dans Visual Studio perspectives d’Application ? Essayez ici." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Résolution des problèmes d’aucune donnée - perspectives d’Application pour .NET

## <a name="some-of-my-telemetry-is-missing"></a>Certains de mes télémétrie est manquant

*Dans les analyses de l’Application, je ne vois qu’une fraction des événements qui sont générés par mon application.*

* Si vous voyez toujours la même fraction, il est probablement adaptive [échantillonnage](app-insights-sampling.md). Pour confirmer cela, ouvrez recherche (à partir de la blade de vue d’ensemble) et observez une instance d’une demande ou d’autres événements. Cliquez sur «... » pour obtenir les détails complets de propriété en bas de la section Propriétés. Si nombre > 1 de la requête, puis l’échantillonnage est en fonctionnement. 
* Dans le cas contraire, il est possible que vous rencontrez une [limite de débit](app-insights-pricing.md#limits-summary) pour votre plan de tarification. Ces limites sont appliquées par minute.

## <a name="no-data-from-my-server"></a>Pas de données à partir de mon serveur

*J’ai installé mon application sur mon serveur web, et maintenant je ne vois pas les données télémétriques fournies par elle. Il a travaillé OK sur mon ordinateur de développement.*

* Probablement un problème de pare-feu. [Définir les exceptions de pare-feu pour les perspectives d’Application envoyer des données](app-insights-ip-addresses.md).

*J’ai [installé le moniteur de statut](app-insights-monitor-performance-live-website-now.md) sur mon serveur web pour surveiller les applications existantes. Je ne vois pas tous les résultats.*

* Consultez [résolution des problèmes de moniteur d’état](app-insights-monitor-performance-live-website-now.md#troubleshooting). 


## <a name="q01"></a>Aucune option « Ajouter des idées Application » dans Visual Studio

*Lorsque je crée un nouveau projet dans Visual Studio, ou je le droit à un projet existant dans l’Explorateur de solutions, je ne vois pas les options d’aperçu de l’Application.*

+ Pas tous les types de projet .NET sont pris en charge par les outils. Projets Web et WCF sont pris en charge. Pour d’autres types de projet, telles que les applications de bureau ou de service, vous pouvez toujours [Ajouter une Application SDK de perspectives à votre projet manuellement](app-insights-windows-desktop.md).
+ Vérifiez que vous avez [mise à jour de Visual Studio 2013 3 ou version ultérieure](http://go.microsoft.com/fwlink/?LinkId=397827). Il est préinstallé avec outils de perspectives d’Application.
+ Sélectionnez les **Outils**, **les Extensions et les mises à jour** et vérifier que les **Outils de perspectives d’Application** est installé et activé. Si tel est le cas, cliquez sur **mises à jour** pour voir si une mise à jour est disponible.
+ Ouvrez la boîte de dialogue Nouveau projet, puis choisissez application Web d’ASP.NET. Si vous voyez l’option Aperçu de l’Application, puis les outils sont installés. Si ce n’est pas le cas, essayez de désinstaller puis réinstaller les outils de l’aperçu.


## <a name="q02"></a>Échec de l’ajout des informations d’Application

*Lorsque je crée un nouveau projet web ou lors de l’ajout des perspectives d’Application à un projet existant, voir un message d’erreur.*

Causes probables :

* Échec de la communication avec le portail de perspectives de l’Application ; ou
* Il existe un problème avec votre compte Azure ;
* Vous avez uniquement [un accès en lecture pour l’abonnement ou du groupe dans lequel vous essayez de créer la nouvelle ressource](app-insights-resources-roles-access-control.md).

Correctif :

+ Vérifiez que vous avez fourni les informations de connexion pour le compte Azure vers la droite. 
+ Dans votre navigateur, vérifiez que vous disposez d’un accès au [portail Azure](https://portal.azure.com). Ouvrir paramètres et voir s’il existe des restrictions.
+ [Idées d’Application ajouter à votre projet](app-insights-asp-net.md): dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet et choisissez « Ajouter des perspectives d’Application ».
+ Si elle ne fonctionne toujours pas, suivez la [procédure manuelle](app-insights-windows-services.md) pour ajouter une ressource dans le portail, puis le Kit de développement pour votre projet. 

## <a name="emptykey"></a>J’obtiens une erreur « clé de l’Instrumentation ne peut pas être vide »

Il semble que quelque chose s’est passé pendant que vous installiez les perspectives d’Application ou peut-être une carte d’enregistrement.

Dans l’Explorateur de solutions, cliquez sur `ApplicationInsights.config` et cliquez sur **Configurer les perspectives sur l’Application**. Vous obtiendrez une boîte de dialogue qui vous invite à vous connecter à Azure et créer une ressource d’Application Insights, ou réutilisez un existant.


##<a name="NuGetBuild"></a>« Les packages NuGet sont manquants » sur mon serveur de builds

*Tout repose OK lorsque je suis le débogage sur mon ordinateur de développement, mais j’obtiens une erreur NuGet sur le serveur de build.*

Reportez-vous à [Restaurer de Package NuGet](http://docs.nuget.org/Consume/Package-Restore) et la [Restauration automatique du Package](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Commande de menu manquantes pour ouvrir des perspectives de l’Application à partir de Visual Studio

*Lorsque j’ai droit sur mon projet de l’Explorateur de solutions, je ne vois pas toutes les commandes de perspectives de l’Application, ou je ne vois pas une commande d’idées d’Application ouverts.*

Causes probables :

* Si vous avez créé la ressource de l’Application aperçu manuellement, ou si le projet est d’un type qui n’est pas pris en charge par les outils de l’aperçu de l’Application.
* Les outils de l’aperçu de l’Application sont désactivés dans votre Visual Studio.
* Votre Visual Studio est antérieure à la mise à jour 2013 3.

Correctif :

* Vérifiez que votre version de Visual Studio est mise à jour de 2013 3 ou version ultérieure.
* Sélectionnez les **Outils**, **les Extensions et les mises à jour** et vérifier que les **Outils de perspectives d’Application** est installé et activé. Si tel est le cas, cliquez sur **mises à jour** pour voir si une mise à jour est disponible.
* Cliquez droit sur votre projet dans l’Explorateur de solutions. Si vous voyez la commande **Configurer les perspectives sur l’Application**, utiliser pour votre projet de se connecter à la ressource dans le service informations d’Application.


Dans le cas contraire, votre type de projet n’est pas directement pris en charge par les outils de l’aperçu de l’Application. Pour voir votre télémétrie, connectez-vous au [portail Azure](https://portal.azure.com), choisissez les perspectives d’Application dans la barre de navigation de gauche et sélectionnez votre application.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>« Accès refusé » lors de l’ouverture des perspectives d’Application à partir de Visual Studio

*La commande de menu 'Perspectives d’Application ouverts' pour atteindre le portail Azure, mais j’obtiens une erreur « accès refusé ».*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

Le Microsoft sign-in que vous avez utilisé dans votre navigateur par défaut n’a pas accès à [la ressource qui a été créée lors de l’aperçu de l’Application a été ajouté à cette application](app-insights-asp-net.md). Il existe deux raisons probables : 

* Vous avez plus d’un compte Microsoft - peut-être un travail et un compte personnel de Microsoft ? Le signe-que vous avez utilisé dans votre navigateur par défaut était pour un autre compte que celui qui a accès à [Ajouter des informations d’Application au projet](app-insights-asp-net.md). 

 * Correctif : Cliquez sur votre nom en haut droite de la fenêtre du navigateur et vous déconnecter. Connectez-vous avec le compte qui a accès. Dans la barre de navigation de gauche, cliquez sur Aperçu de l’Application, puis sélectionnez votre application.

* Quelqu'un d’autre ajouté des perspectives d’Application au projet, et il a oublié de vous donner [accès au groupe de ressources](app-insights-resources-roles-access-control.md) dans lequel il a été créé. 

 * Correctif : Si elles utilisé un compte d’organisation, ils peuvent vous ajouter à l’équipe ; ou bien, ils peuvent accorder l’accès au groupe de ressources individuel.



## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>« Ressource introuvable » lors de l’ouverture des perspectives d’Application à partir de Visual Studio

*La commande de menu 'Perspectives d’Application ouverts' pour atteindre le portail Azure, mais j’obtiens une erreur « ressource introuvable ».*

Causes probables :

* La ressource d’informations d’Application pour votre application a été supprimée ; ou
* La clé de l’instrumentation a été définie ou modifiée dans la ApplicationInsights.config en la modifiant directement, sans mettre à jour le fichier projet. 

La clé de l’instrumentation dans les contrôles de ApplicationInsights.config où la télémétrie est envoyé. Une ligne dans le fichier projet contrôle de ressource qui est ouvert lorsque vous utilisez la commande dans Visual Studio. 

Correctif :

* Dans l’Explorateur de solutions, droit sur le projet, puis choisissez Application Insights, configurer les perspectives sur l’Application. Dans la boîte de dialogue, vous pouvez choisir d’envoyer de télémétrie à une ressource existante, ou créer un nouveau. Ou :
* Ouvrez la ressource directement. Vous connecter au [portail Azure](https://portal.azure.com)et cliquez sur Aperçu de l’Application dans la barre de navigation de gauche, puis sélectionnez votre application.



## <a name="where-do-i-find-my-telemetry"></a>Où puis-je trouver mon télémétrie ?

*Je connecté au [portail Microsoft Azure](https://portal.azure.com), et que je recherche le tableau de bord personnel Azure. Par conséquent, où puis-je trouver mes données d’idées d’Application ?*

* Dans la barre de navigation de gauche, cliquez sur Aperçu de l’Application, puis le nom de votre application. Si vous n’avez pas il tous les projets, vous devez [Ajouter ou configurer des perspectives d’Application dans votre projet web](app-insights-asp-net.md).

    Vous allez voir certains graphiques de synthèse. Vous pouvez cliquer sur les utiliser pour voir plus de détails.

* Dans Visual Studio, pendant que vous déboguez votre application, cliquez sur le bouton Aperçu de l’Application.


## <a name="q03"></a>Aucune donnée du serveur (ou pas du tout de données)

*J’ai exécuté mon application et puis d’ouvrir le service informations d’Application dans Microsoft Azure, mais tous les graphiques affichent « Savoir comment collecter... » ou « N’est pas configuré. »* Ou, *uniquement les données utilisateur et d’affichage de la Page, mais aucune donnée du serveur.*

+ Exécutez votre application en mode débogage dans Visual Studio (F5). Utilisez l’application pour générer certains télémétrie. Vérifiez que vous pouvez voir les événements consignés dans la fenêtre Sortie de Visual Studio. 

    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)

+ Dans le portail de perspectives de l’Application, ouvrez [Recherche de Diagnostic](app-insights-diagnostic-search.md). Données généralement s’affiche ici.
+ Cliquez sur le bouton Actualiser. La blade s’actualise régulièrement, mais vous pouvez le faire aussi manuellement. L’intervalle d’actualisation est la plus longue pour les plages de temps plus grandes.
+ Vérifiez que les clés de l’instrumentation correspondent. Sur la lame principale pour votre application dans le portail d’idées d’Application, dans le menu déroulant **Essentials** , examinez la **clé de l’Instrumentation**. Dans votre projet dans Visual Studio, ouvrez ensuite ApplicationInsights.config et recherchez la `<instrumentationkey>`. Vérifiez que les deux clés sont égales. Si ce n’est pas le cas :
 + Dans le portail, cliquez sur Aperçu de l’Application et recherchez la ressource de l’application avec la touche de droite ; ou
 + Dans l’Explorateur de solutions de Visual Studio, droit sur le projet, puis choisissez Application Insights, configurer. Réinitialiser l’application pour envoyer de télémétrie pour la ressource de droite.
 + Si vous ne trouvez pas les clés correspondantes, vérifiez que vous utilisez les mêmes informations de connexion dans Visual Studio en tant que dans le portail.

    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
    
+ Dans le [tableau de bord personnel Microsoft Azure](https://portal.azure.com), consulter la carte du fonctionnement du Service. S’il existe des indications d’alerte, patientez jusqu'à ce qu’ils ont renvoyé sur OK puis fermez et ouvrez de nouveau la blade d’application perspectives d’Application.
+ Vérifiez également [notre blog d’état](http://blogs.msdn.com/b/applicationinsights-status/).
+ Avez vous écrire du code pour le [Kit de développement logiciel côté serveur](app-insights-api-custom-events-metrics.md) susceptible de modifier la clé de l’instrumentation dans `TelemetryClient` instances ou dans `TelemetryContext`? Ou avez-vous écrit une [configuration de filtre ou de l’échantillonnage](app-insights-api-filtering-sampling.md) peut être filtrage en trop ?
+ Si vous avez modifié ApplicationInsights.config, vérifiez attentivement la configuration [TelemetryInitializers et TelemetryProcessors](app-insights-api-filtering-sampling.md). Une type d’incorrectement nommé ou un paramètre peut provoquer le SDK n’envoyer aucune donnée.

## <a name="q04"></a>Aucune donnée sur l’utilisation de vues de Page, les navigateurs,

*Voir les données dans les graphiques en temps de réponse de serveur et des requêtes du serveur, mais pas de données dans le temps de chargement de la Page Afficher, ou dans les navigateur ou l’utilisation des lames.*

Les données proviennent de scripts dans les pages web. 

+ Si vous avez ajouté des perspectives d’Application à un projet web existant, [que vous devez ajouter les scripts à la main](app-insights-javascript.md).
+ Assurez-vous que Internet Explorer n’affiche pas votre site en mode de compatibilité.
+ Utilisez la fonctionnalité de débogage du navigateur (F12 dans certains navigateurs, puis choisissez réseau) pour vérifier que les données sont envoyées à `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Aucune donnée de dépendance ou d’exception

Consultez [télémétrie de dépendance](app-insights-asp-net-dependencies.md) et de [télémétrie d’exception](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>Aucune donnée de performance

Les données de performance (UC, les taux d’e/s et ainsi de suite) est disponible pour [les services web Java](app-insights-java-collectd.md), [les applications de bureau Windows](app-insights-windows-desktop.md), [IIS web des applications et services si vous installez le moniteur d’état](app-insights-monitor-performance-live-website-now.md)et [Services Cloud Azure](app-insights-azure.md). vous le trouverez sous paramètres de serveurs.

Il n’est pas disponible pour les sites Web Azure.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Pas de données (serveur) depuis que j’ai publié l’application sur mon serveur

+ Vérifiez que vous avez copié en fait tous les Microsoft. DLL ApplicationInsights sur le serveur, avec Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
+ Dans votre pare-feu, vous devrez peut-être [ouvrir certains ports TCP](app-insights-ip-addresses.md#data-access-api).
+ Si vous devez utiliser un proxy pour l’envoi de votre réseau d’entreprise, définissez [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) dans Web.config
+ Windows Server 2008 : Assurez-vous que vous avez installé les mises à jour suivantes : [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).


## <a name="i-used-to-see-data-but-it-has-stopped"></a>Permet de visualiser les données, mais il s’est arrêté.

* Consultez le [blog de l’état](http://blogs.msdn.com/b/applicationinsights-status/).
* Vous avez atteint votre quota mensuel de points de données ? Ouvrir les paramètres/Quota et la tarification pour découvrir. Dans ce cas, vous pouvez mettre à niveau votre plan ou payer pour une capacité supplémentaire. Consultez le [mécanisme de tarification](https://azure.microsoft.com/pricing/details/application-insights/).


## <a name="i-dont-see-all-the-data-im-expecting"></a>Je ne vois pas toutes les données que je suis attendu.

Si votre application envoie un lot de données et que vous utilisez le Kit de développement du perspectives d’Application pour ASP.NET version 2.0.0-beta3 ou version ultérieure, la fonctionnalité [d’échantillonnage adapté](app-insights-sampling.md) peut fonctionner et envoyer uniquement un pourcentage de votre télémétrie. 

Vous pouvez le désactiver, mais cela n’est pas recommandé. L’échantillonnage est conçu pour télémétrie connexe est correctement transmis, à des fins de Diagnostics. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>Données géographiques incorrect de télémétrie d’utilisateur

La ville, la région et les dimensions de pays sont dérivées provenant d’adresses IP et ne sont pas toujours précises.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exception « méthode introuvable » sur l’exécution de Services Cloud Azure

Vous n’avez généré pour .NET 4.6 ? 4.6 n’est pas pris en charge dans les rôles d’Azure Cloud Services automatiquement. [4.6 d’installer sur chaque rôle](../cloud-services/cloud-services-dotnet-install-dotnet.md) avant d’exécuter votre application.

## <a name="still-not-working"></a>Encore ne fonctionne ne pas...

* [Forum de perspectives d’application](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

