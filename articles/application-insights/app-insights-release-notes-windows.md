<properties 
    pageTitle="Notes de publication pour les informations d’Application pour Windows" 
    description="Les dernières mises à jour pour le Kit de développement logiciel de Windows Store." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2016" 
    ms.author="joshweb"/>
 
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>Notes de publication pour les perspectives d’Application SDK pour Windows Phone et stocker

Le SDK de perspectives d’Application envoie télémétrie sur votre application direct [d’Idées d’Application](https://azure.microsoft.com/services/application-insights/), où vous pouvez analyser son utilisation et ses performances.


## <a name="version-111"></a>Version 1.1.1

### <a name="windows-sdk"></a>Kit de développement Windows

- Résoudre un blocage lors de l’incident lors de l’utilisation SDK de la Windows Phone Silverlight. Après cette modification, tout blocage qui se produit au plus tard le ~ 2 secondes après l’appel à WindowsAppInitialier.InitializeAsync(...) est rendues persistantes dans disque et sera envoyé la prochaine fois que l’application est lancée. Si une panne se produit avant ~ 2 secondes après l’appel, il sera ignoré.  
- Définir les dépendances de la NuGet pour une version spécifique du noyau et Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### <a name="core-sdk"></a>Kit de développement SDK

- Core est gérée dans github. Notes de version ultérieure du Kit de développement principaux se trouvent [dans github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>Version 1.1

### <a name="core-sdk"></a>Kit de développement SDK

- Kit de développement logiciel présente maintenant le nouveau type de télémétrie ```DependencyTelemetry``` qui contient des informations sur l’appel de dépendance à partir de l’application
- Nouvelle méthode ```TelemetryClient.TrackDependency``` permet d’envoyer des informations sur les appels de dépendance à partir de l’application

## <a name="version-100"></a>Version 1.0.0

### <a name="windows-app-sdk"></a>Kit de développement logiciel Windows App

- Nouvelle syntaxe d’initialisation pour les applications Windows. Nouveaux `WindowsAppInitializer` classe avec `InitializeAsync()` permet de méthode pour démarrer l’initialisation d’une collection de SDK. Cette modification permet un contrôle plus précis et des améliorations de performances significatives app d’initialisation sur précédente ApplicationInsights.config technique.
- DeveloperMode a n’est plus automatiquement. Pour modifier le comportement de DeveloperMode, vous devez spécifier dans le code.
- Package NuGet injecte n’est plus ApplicationInsights.config. Vous recommandons d’utiliser la nouvelle WindowsAppInitializer lors de l’ajout manuel de package NuGet.
- Lit uniquement les ApplicationInsights.config `<InstrumentationKey>`, tous les autres paramètres sont ignorés dans la préférence pour les paramètres WindowsAppInitializer.
- Marché de la banque sera automatiquement collectée par le Kit de développement logiciel.
- Beaucoup de correctifs de bogues, des améliorations de stabilité et des améliorations de performances.

### <a name="core-sdk"></a>Kit de développement SDK

- Fichier de ApplicationInsights.config n’est plus obligatoires. Et pas ajoutées par le package NuGet. Configuration peut être entièrement spécifiée dans le code.
- Package NuGet ajoute un fichier de cibles n’est plus à votre solution. Cela supprime le paramètre automatique de DeveloperMode pendant une génération de débogage. DeveloperMode doit être définie manuellement dans le code.

## <a name="version-017"></a>Version 0.17

### <a name="windows-app-sdk"></a>Kit de développement logiciel Windows App

- Kit de développement logiciel Windows App prend désormais en charge des applications Windows universel créé par rapport à la version bêta de Windows 10 et avec VS 2015 RC.

### <a name="core-sdk"></a>Kit de développement SDK

- Par défaut, TelemetryClient pour initialiser le InMemoryChannel.
- Nouvelle API ajouté, `TelemetryClient.Flush()`. Cette méthode de vidage déclenchera un chargement de blocage immédiat de télémétrie tous connecté à ce client. Cela permet de déclencher manuellement le téléchargement avant l’arrêt du processus.
- Package NuGet ajouté une cible le .net 4.5. Cette cible n’a pas de dépendances externes (dépendances BCL et EventSource supprimés).

## <a name="version-016"></a>Version 0,16 

Aperçu de 2015-04-28

- Kit de développement logiciel prend désormais en charge la plate-forme cible DNX pour activer le contrôle des applications de [base de .NET framework](http://www.dotnetfoundation.org/NETCore5) .
- Instances de ```TelemetryClient``` ne pas mettre en cache clé d’Instrumentation plus. Maintenant, si la clé de l’instrumentation n’a pas été définie dans ```TelemetryClient``` explicitement ```InstrumentationKey``` retournera la valeur null. Il résout un problème lors de la ```TelemetryConfiguration.Active.InstrumentationKey``` après certains TELEMETRIE a déjà collecté, modules de télémétrie comme collecteur de dépendance, web demandes et performances des compteurs de collecteurs de données utilisera la nouvelle clé d’instrumentation.

## <a name="version-015"></a>Version 0,15

- Nouvelle propriété ```Operation.SyntheticSource``` maintenant disponible sur ```TelemetryContext```. Maintenant, vous pouvez marquer vos éléments de télémétrie comme « pas un trafic utilisateur réel » et spécifiez la manière dont ce trafic a été généré. Par exemple en définissant cette propriété, vous pouvez différencier le trafic à partir de votre automation de test à partir du trafic de test de charge.
- Canal logique a été déplacé vers le NuGet distinct appelé Microsoft.ApplicationInsights.PersistenceChannel. Canal par défaut s’appelle désormais InMemoryChannel
- Nouvelle méthode ```TelemetryClient.Flush``` permet de vider les éléments de télémétrie à partir de la mémoire tampon de façon synchrone

## <a name="version-013"></a>Version 0,13 pouce

Aucune note de version pour les versions antérieures disponibles. 
