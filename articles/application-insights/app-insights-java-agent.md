<properties 
    pageTitle="Surveiller les dépendances, les exceptions et les temps d’exécution dans les applications web Java" 
    description="Étendue d’analyse de votre site Web de Java avec aperçus de l’Application" 
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
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Surveiller les dépendances, les exceptions et les temps d’exécution dans les applications web Java

*Idées d’application est en mode Aperçu.*

Si vous devez [instrumenter votre application de web Java avec les perspectives d’Application][java], vous pouvez utiliser l’Agent Java se référant plus profond, sans aucune modification de code :


* **Dépendances :** Données sur les appels passés par votre application à d’autres composants, y compris :
 * **Autres appels** effectués via HttpClient, OkHttp et RestTemplate (Spring).
 * **Redis** les appels effectués via le client Jedis. Si l’appel dure plu de 10 s, l’agent extrait également les arguments de l’appel.
 * **[Appels JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - MySQL, SQL Server, PostgreSQL, SQLite, base de données Oracle ou Apache Derby DB. les appels de « executeBatch » sont pris en charge. MySQL et PostgreSQL, si l’appel dure plu de 10 s, l’agent transmet le plan de requête. 
* **Interceptée des exceptions :** Données sur les exceptions qui sont gérées par votre code.
* **Exécution de la méthode :** Données sur le temps que nécessaire pour exécuter des méthodes spécifiques.

Pour utiliser l’agent de Java, vous l’installez sur votre serveur. Vos applications web doivent être instrumentées avec le [Kit de développement Java Application perspectives sur][java].

## <a name="install-the-application-insights-agent-for-java"></a>Installation de l’agent d’idées d’Application pour Java

1. Sur l’ordinateur exécutant votre serveur Java, [Téléchargez l’agent](https://aka.ms/aijavasdk).
2. Modifier le script de démarrage d’application serveur et ajoutez la machine virtuelle Java suivant :

    `javaagent:`*chemin d’accès complet pour le fichier JAR de l’agent*

    Par exemple, dans Tomcat sur une machine Linux :

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Redémarrez votre serveur d’application.

## <a name="configure-the-agent"></a>Configuration de l’agent

Créez un fichier nommé `AI-Agent.xml` et placez-le dans le même dossier que le fichier JAR de l’agent.

Définissez le contenu du fichier xml. Modifier l’exemple suivant pour inclure ou omettre les fonctionnalités que vous souhaitez. 

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>
           
           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Vous devez activer l’exception de rapports et de minutage de méthode pour chaque méthode.

Par défaut, `reportExecutionTime` a la valeur true et `reportCaughtExceptions` a la valeur false.

## <a name="view-the-data"></a>Afficher les données

Dans la ressource d’Application Insights, agrégées temps d’exécution dépendance et méthode à distance apparaît [sous la mosaïque de performances][metrics]. 

Pour rechercher des instances individuelles de la dépendance, d’exception et méthode de rapports, ouvrez la [recherche][diagnostic]. 

[Diagnostic des problèmes de dépendance - en savoir plus](app-insights-dependencies.md#diagnosis).



## <a name="questions-problems"></a>Questions ? Problèmes ?

* Aucune donnée ? [Définir les exceptions de pare-feu](app-insights-ip-addresses.md)
* [Résolution des problèmes liés à Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 
