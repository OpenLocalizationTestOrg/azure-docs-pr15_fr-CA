<properties 
    pageTitle="Résolution des problèmes et Questions sur les aspects de l’Application" 
    description="Un élément de Visual Studio Application perspectives sur la vague ou ne fonctionne pas ? Essayez ici." 
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
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="questions---application-insights-for-aspnet"></a>Questions - les perspectives d’Application pour ASP.NET

## <a name="configuration-problems"></a>Problèmes de configuration

*J’ai un paramètre du problème mes :*

* [Applications .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Surveillance d’une application déjà actif](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnostics de Windows Azure](app-insights-azure-diagnostics.md)
* [Application web de Java](app-insights-java-troubleshoot.md)
* [Autres plates-formes](app-insights-platforms.md)

*N’obtenir aucune donnée à partir de mon serveur*

* [Définir les exceptions de pare-feu](app-insights-ip-addresses.md)
* [Configurer un serveur d’ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurer un serveur Java](app-insights-java-agent.md)


## <a name="can-i-use-application-insights-with-"></a>Puis-je utiliser des perspectives d’Application avec... ?

[Reportez-vous à la section plates-formes][platforms]


## <a name="is-it-free"></a>Est-ce gratuit ?

* Oui, si vous choisissez la libre [niveau de tarification](app-insights-pricing.md). Vous obtenez la plupart des fonctionnalités et un contingent généreux de données. 
* Vous devez fournir vos données de carte de crédit pour enregistrer avec Microsoft Azure, mais aucun frais ne seront faites, sauf si vous utilisez un autre payés pour un service Azure, ou explicitement mise à niveau vers un niveau de paiement.
* Si votre application envoie plus de données que le quota mensuel pour la couche libre, il s’arrête en cours d’enregistrement. Si cela se produit, vous pouvez choisir de début du paiement ou patienter jusqu'à ce que le quota est réinitialisé à la fin du mois.
* Les données de session et de l’utilisation de base ne sont pas soumis à un quota.
* Il existe également un essai gratuit de 30 jours, au cours de laquelle vous offre les fonctionnalités de payé gratuites.
* Chaque ressource de l’application a un quota distinct, et vous définissez son niveau de tarification indépendamment des autres.

#### <a name="what-do-i-get-if-i-pay"></a>Que faire si je paie ?

* Un plus grand [quota mensuel de données](https://azure.microsoft.com/pricing/details/application-insights/).
* Option de paiement « limites d’âge' pour continuer la collecte de données sur le quota mensuel. Si vos données dépasse de quota, vous êtes facturés par Mo.
* [Exportation de continu](app-insights-export-telemetry.md).


## <a name="q14"></a>Ce qui modifie les perspectives d’Application dans mon projet ?

Les détails dépendent du type de projet. Pour une application web :


+ Ajoute ces fichiers à votre projet :

 + ApplicationInsights.config. 
 + ai.js


+ Installe les packages NuGet :

 -  *Idées d’application API* - l’API principale

 -  *Idées d’applications API pour les Applications Web* - utilisé pour l’envoi de télémétrie à partir du serveur

 -  *Idées d’applications API pour les Applications JavaScript* - utilisé pour l’envoi de télémétrie à partir du client

    Les packages comprennent ces assemblys :

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Insère des éléments dans :

 - Web.config

 - packages.config

+ (Nouveaux projets uniquement : Si vous [Ajoutez des perspectives d’Application à un projet existant][start], vous devez le faire manuellement.) Insère des extraits de code dans le code client et serveur pour les initialiser avec l’ID de ressource de perspectives de l’Application. Par exemple, dans une application MVC, le code est inséré dans la page maître Views/Shared/_Layout.cshtml


## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Comment passer d’anciennes versions du Kit de développement logiciel ?

Consultez les [notes de version](app-insights-release-notes.md) pour le Kit de développement logiciel approprié à votre type d’application. 



## <a name="update"></a>Comment puis-je modifier une ressource Azure qui envoie des données à mon projet ?

Dans l’Explorateur de solutions, cliquez sur `ApplicationInsights.config` et choisissez les **Perspectives d’Application de mise à jour**. Vous pouvez envoyer les données à une ressource nouvelle ou existante dans Azure. L’Assistant de mise à jour modifie la clé de l’instrumentation dans ApplicationInsights.config, qui détermine où le Kit de développement logiciel du serveur envoie vos données. Sauf si vous désactivez l’option « Tout mettre à jour », il modifiera également la clé où il apparaît dans vos pages web.


#### <a name="data"></a>Combien de données sont conservées dans le portail ? Est-il sécurisé ?

Jetez un coup de [rétention des données et confidentialité][data].

## <a name="logging"></a>Enregistrement dans le journal

#### <a name="post"></a>Comment voir les données de publication dans la recherche de Diagnostic ?

Nous n’enregistre pas automatiquement les données de publication, mais vous pouvez utiliser un appel de TrackTrace : placer les données dans le paramètre de message. Ceci a une limite de taille plus longue que les limites sur les propriétés de type chaîne, même si vous ne pouvez pas filtrer sur celui-ci. 

## <a name="security"></a>Sécurité

#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Est-ce que mes données est sécurisée dans le portail ? Combien de temps il est conservé ?

Consultez [données de rétention et confidentialité][data].


## <a name="q17"></a>Ai activé tous les éléments de perspectives d’Application ?

<table border="1">
<tr><th>Vous devriez voir</th><th>Comment faire pour l’obtenir</th><th>Raison pour laquelle vous souhaitez qu’il</th></tr>
<tr><td>Graphiques de disponibilité</td><td><a href="../app-insights-monitor-web-app-availability/">Tests Web</a></td><td>Connaissez que votre application web est actif</td></tr>
<tr><td>Performances d’application serveur : temps de réponse,...
</td><td><a href="../app-insights-asp-net/">Ajouter des informations d’Application à votre projet</a><br/>ou <br/><a href="../app-insights-monitor-performance-live-website-now/">Installer le moniteur de statut AI sur serveur</a> (ou écrire votre propre code pour <a href="../app-insights-api-custom-events-metrics/#track-dependency">effectuer le suivi des dépendances</a>)</td><td>Détecter les problèmes de performances</td></tr>
<tr><td>TELEMETRIE de dépendance</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installer le moniteur de statut AI sur serveur</a></td><td>Diagnostiquer les problèmes avec les bases de données ou d’autres composants externes</td></tr>
<tr><td>Obtenir des traces de la pile d’exceptions</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Insérer les appels de TrackException dans le code</a> (mais certains sont signalés automatiquement)</td><td>Détecter et diagnostiquer les exceptions</td></tr>
<tr><td>Recherche journal des traces</td><td><a href="../app-insights-search-diagnostic-logs/">Ajouter une enregistrement de carte</a></td><td>Diagnostiquer les exceptions, les problèmes de performances</td></tr>
<tr><td>Principes fondamentaux de l’utilisation de client : vues de page, les sessions,...</td><td><a href="../app-insights-javascript/">Initialiseur de JavaScript dans les pages web</a></td><td>Analytique de l’utilisation</td></tr>
<tr><td>Mesures personnalisées de client</td><td><a href="../app-insights-api-custom-events-metrics/">Suivi des appels dans les pages web</a></td><td>Améliorer l’expérience utilisateur</td></tr>
<tr><td>Mesures personnalisées de serveur</td><td><a href="../app-insights-api-custom-events-metrics/">Suivi des appels dans le code serveur</a></td><td>Analyse décisionnelle</td></tr>
</table>


## <a name="automation"></a>Automation

Vous pouvez [écrire des scripts PowerShell](app-insights-powershell.md) pour créer et mettre à jour les ressources de l’Application aperçu.

## <a name="more-answers"></a>Autres réponses

* [Forum de perspectives d’application](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 