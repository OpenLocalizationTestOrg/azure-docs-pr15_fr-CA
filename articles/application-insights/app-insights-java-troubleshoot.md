<properties 
    pageTitle="Résoudre les problèmes de perspectives de l’Application dans un projet web de Java" 
    description="Guide de dépannage - surveillance des applications Java direct avec aperçus de l’Application." 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/01/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Résolution des problèmes et A et Q perspectives d’Application pour Java

Questions ou problèmes [Visual Studio]issues de l’Application d’analyses dans Java[java]? Voici quelques conseils.


## <a name="build-errors"></a>Erreurs de génération

*Dans Eclipse, lors de l’ajout du Kit de développement de perspectives Application via Maven ou Gradle, j’obtiens la build ou total de contrôle des erreurs de validation.*

* Si la dépendance <version> élément est à l’aide d’un modèle avec des caractères génériques (par exemple, (Maven) `<version>[1.0,)</version>` ou (Gradle) `version:'1.0.+'`), essayez de spécifier une version spécifique d’à la place comme `1.0.2`. Consultez les [notes de version](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) pour la version la plus récente.

## <a name="no-data"></a>Aucune donnée 

*J’ai ajouté avec succès les perspectives d’Application et exécuté mon application, mais je n’ai jamais vu de données dans le portail.*

* Attendez une minute et cliquez sur Actualiser. Les graphiques s’actualiser périodiquement, mais vous pouvez également actualiser manuellement. L’intervalle d’actualisation dépend de la plage horaire du graphique.
* Vérifiez que vous disposez d’une clé d’instrumentation définie dans le fichier ApplicationInsights.xml (dans le dossier ressources dans votre projet)
* Vérifiez qu’il existe aucun `<DisableTelemetry>true</DisableTelemetry>` le nœud dans le fichier xml.
* Dans votre pare-feu, vous devrez ouvrir les ports TCP 80 et 443 pour le trafic sortant à dc.services.visualstudio.com. Consultez la [liste complète des exceptions de pare-feu](app-insights-ip-addresses.md)
* Dans le Microsoft Azure démarrer carte, consulter la carte d’état de service. S’il existe des indications d’alerte, patientez jusqu'à ce qu’ils ont renvoyé sur OK puis fermez et ouvrez de nouveau la blade d’application perspectives d’Application.
* Activer l’enregistrement dans la fenêtre de console IDE, en ajoutant une `<SDKLogger />` élément sous le nœud racine dans le fichier ApplicationInsights.xml (dans le dossier ressources dans votre projet) et vérifiez les entrées précédées de [erreur].
* Assurez-vous que le fichier ApplicationInsights.xml a été correctement chargé par le Kit de développement Java, en consultant les messages de sortie de la console pour une instruction « fichier de Configuration a été trouvé ».
* Si le fichier de configuration n’est pas trouvé, vérifiez les messages de sortie pour voir où le fichier de configuration est recherché et assurez-vous que le ApplicationInsights.xml se trouve dans un de ces emplacements de recherche. En règle générale, vous pouvez placer le fichier de configuration près de la JARs du Kit de développement logiciel Application perspectives. Par exemple : Tomcat, cela signifie que le dossier WEB-INF/lib.



#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Permet de visualiser les données, mais il s’est arrêté.

* Consultez le [blog de l’état](http://blogs.msdn.com/b/applicationinsights-status/).
* Vous avez atteint votre quota mensuel de points de données ? Ouvrez les tarifs et les paramètres/Quota pour savoir. Dans ce cas, vous pouvez mettre à niveau votre plan ou payer pour une capacité supplémentaire. Consultez le [mécanisme de tarification](https://azure.microsoft.com/pricing/details/application-insights/).

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Je ne vois pas toutes les données que je suis attendu.

* Permet d’ouvrir les contingents et tarification des lames et vérifiez si [l’échantillonnage](app-insights-sampling.md) est en fonctionnement. (transmission de 100 % signifie que l’échantillonnage n’est pas dans l’opération). Le service informations d’Application peut être défini pour n'accepter qu’une fraction de la télémétrie qui arrive à partir de votre application. Cela vous aide à conserver dans votre quota mensuel de télémétrie. 

## <a name="no-usage-data"></a>Aucune donnée d’utilisation

*Voir données utilisateur ou des données sur les demandes et les temps de réponse, mais aucun mode page, le navigateur.*

Vous correctement configuré votre application pour envoyer de télémétrie à partir du serveur. Maintenant l’étape suivante consiste à [configurer vos pages web pour envoyer des données télémétriques fournies par le navigateur web][usage].

Vous pouvez également, si votre client est une application dans un [téléphone ou un autre périphérique][platforms], vous pouvez envoyer de télémétrie à partir de là. 

Utiliser la même clé d’instrumentation pour configurer votre client et le serveur de télémétrie. Les données s’affichent dans la même ressource de perspectives de l’Application, et vous serez en mesure de mettre en corrélation les événements de client et le serveur.



## <a name="disabling-telemetry"></a>La désactivation de télémétrie

*Comment puis-je désactiver le collection de télémétrie ?*

Dans le code :

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Ou** 

Mise à jour ApplicationInsights.xml (dans le dossier ressources dans votre projet). Ajoutez la ligne suivante sous le nœud racine :

    <DisableTelemetry>true</DisableTelemetry>

À l’aide de la méthode XML, vous devez redémarrer l’application lorsque vous modifiez la valeur.

## <a name="changing-the-target"></a>Modification de la cible

*Comment puis-je modifier une ressource Azure qui envoie des données à mon projet ?*

* [Obtention de la clé de l’instrumentation de la nouvelle ressource.][java]
* Si vous ajouté des perspectives d’Application à votre projet à l’aide de la Shared Computer Toolkit Azure pour Eclipse, droit de la souris cliquez sur votre projet web, sélectionnez **Azure**, **Configurer les perspectives sur l’Application**et modifier la clé.
* Dans le cas contraire, mise à jour de la clé ApplicationInsights.xml dans le dossier de ressources dans votre projet.


## <a name="the-azure-start-screen"></a>L’écran de démarrage Azure

*Je recherche sur [le portail Azure](https://portal.azure.com). La carte indique quelque chose de mon application ?*

Non, il affiche l’état de santé des serveurs Azure dans le monde entier.

*De la carte de démarrage Azure (écran d’accueil), comment trouver données de mon application ?*

En supposant que vous [Configurez votre application pour avoir un aperçu Application][java]et cliquez sur Parcourir, sélectionnez les perspectives de l’Application, sélectionnez la ressource d’application que vous avez créé pour votre application. Pour obtenir il plus rapidement dans le futur, vous pouvez ajouter votre application à la carte de démarrage.

## <a name="intranet-servers"></a>Serveurs intranet

*Puis-je analyser un serveur sur mon intranet ?*

Oui, à condition que votre serveur peut envoyer de télémétrie pour le portail d’idées d’Application par le biais de l’internet public. 

Dans votre pare-feu, vous devrez ouvrir les ports TCP 80 et 443 pour le trafic sortant à dc.services.visualstudio.com et f5.services.visualstudio.com.

## <a name="data-retention"></a>Rétention des données 

*Combien de données sont conservées dans le portail ? Est-il sécurisé ?*

Reportez-vous à la section [confidentialité et rétention des données][data].

## <a name="next-steps"></a>Étapes suivantes

*Définir des perspectives d’Application pour mon application de serveur Java. Que puis-je faire ?*

* [Surveiller la disponibilité de vos pages web][availability]
* [Surveiller l’utilisation de la page web][usage]
* [Suivre l’utilisation et de diagnostiquer les problèmes dans vos applications de périphérique][platforms]
* [Écrire du code pour effectuer le suivi de l’utilisation de votre application.][track]
* [Capturer les journaux de diagnostic][javalogs]


## <a name="get-help"></a>Obtenir de l’aide

* [Débordement de pile](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 