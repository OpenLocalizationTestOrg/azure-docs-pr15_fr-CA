<properties 
    pageTitle="Comment faire pour utiliser l’API de l’Engagement sur Silverlight de Windows Phone" 
    description="Comment faire pour utiliser l’API de l’Engagement sur Silverlight de Windows Phone"    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" /> 

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Comment faire pour utiliser l’API de l’Engagement sur Silverlight de Windows Phone

Ce document est un module complémentaire au document [comment intégrer l’Engagement Mobile dans votre application Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md). Il fournit en profondeur plus d’informations sur l’utilisation de l’API de l’Engagement à votre application des statistiques.

Si vous ne souhaitez que l’Engagement de signaler les sessions de votre application, activités, blocages et des informations techniques, puis le plus simple consiste à donner des votre `PhoneApplicationPage` sous-classes héritent de le `EngagementPage` classe.

Si vous souhaitez en faire plus, par exemple, si vous avez besoin de signaler les événements spécifiques de l’application, les erreurs et les tâches, ou si vous devez signaler les activités de votre application d’une manière différente que celle implémentée dans le `EngagementPage` des classes, vous devez utiliser l’API de l’Engagement.

L’API de l’Engagement est fourni par le `EngagementAgent` classe. Vous pouvez accéder à ces méthodes par l’intermédiaire de `EngagementAgent.Instance`.

Même si le module de l’agent n’a pas été initialisé, chaque appel à l’API est différé et sera à nouveau exécutée lorsque l’agent est disponible.

##<a name="engagement-concepts"></a>Concepts de l’engagement

Les parties suivantes affiner les Concepts d’Engagement Mobile pour la plateforme Windows Phone.

### <a name="session-and-activity"></a>`Session`et`Activity`

Une *activité* est généralement associé à une page de l’application, que l' *activité* démarre lorsque la page est affichée et s’arrête lorsque la page est fermée : c’est le cas lorsque le SDK de l’Engagement est intégré à l’aide de la `EngagementPage` classe.

Mais les *activités* peuvent également être contrôlés manuellement à l’aide de l’API de l’Engagement. Cela permet de diviser une page donnée dans diverses parties de sub pour obtenir plus de détails sur l’utilisation de cette page (par exemple pour connues la fréquence et la durée pendant laquelle les boîtes de dialogue sont utilisés à l’intérieur de cette page).

##<a name="reporting-activities"></a>Rapports d’activités

### <a name="user-starts-a-new-activity"></a>Utilisateur démarre une nouvelle activité

#### <a name="reference"></a>Référence

            void StartActivity(string name, Dictionary<object, object> extras = null)

Vous devez appeler `StartActivity()` chaque fois que les modifications d’activité utilisateur. Le premier appel à cette fonction démarre une nouvelle session de l’utilisateur.

> [AZURE.IMPORTANT] Le Kit de développement appelle automatiquement la méthode ActivitéFin lors de la fermeture de l’application. Par conséquent, il est vivement recommandé d’appeler la méthode StartActivity chaque fois que l’activité de la modification de l’utilisateur et jamais appeler la méthode ActivitéFin, étant donné que l’appel de cette méthode force la session en cours à terminer.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Utilisateur met fin à l’activité en cours

#### <a name="reference"></a>Référence

            void EndActivity()

Vous devez appeler `EndActivity()` au moins une fois lorsque l’utilisateur termine sa dernière activité. Cela informe le SDK de l’Engagement que l’utilisateur est inactif et que la session de l’utilisateur doivent être fermés une fois le délai d’expiration de la session expire (si vous appelez `StartActivity()` avant l’expiration du délai d’expiration de la session, la session est simplement la suite).

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.EndActivity();

##<a name="reporting-jobs"></a>Tâches de Reporting

### <a name="start-a-job"></a>Démarrage d’une tâche

#### <a name="reference"></a>Référence

            void StartJob(string name, Dictionary<object, object> extras = null)

Vous pouvez utiliser la tâche pour effectuer le suivi des tâches de certains services sur une période donnée.

#### <a name="example"></a>Exemple

            // An upload begins...
            
            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");
            
            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Fin d’une tâche

#### <a name="reference"></a>Référence

            void EndJob(string name)

Dès qu’une tâche suivie par une tâche terminée, vous devez appeler la méthode EndJob pour ce projet, en fournissant le nom de la tâche.

#### <a name="example"></a>Exemple

            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends
            
            EngagementAgent.Instance.EndJob("uploadData");

##<a name="reporting-events"></a>Événements de génération de rapports

Il existe trois types d’événements :

-   Événements autonome
-   Événements de session
-   Événements de tâche

### <a name="standalone-events"></a>Événements autonome

#### <a name="reference"></a>Référence

            void SendEvent(string name, Dictionary<object, object> extras = null)

Les événements autonome peuvent se produire en dehors du contexte d’une session.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Événements de session

#### <a name="reference"></a>Référence

            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Événements de session sont généralement utilisés pour signaler les actions exécutées par un utilisateur lors de sa session.

#### <a name="example"></a>Exemple

**Sans les données :**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");
            
            // or
            
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Avec les données :**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Événements de tâche

#### <a name="reference"></a>Référence

            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Événements de tâche sont généralement utilisés pour signaler les actions exécutées par un utilisateur lors d’un travail.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##<a name="reporting-errors"></a>Rapport d’erreurs

Il existe trois types d’erreurs :

-   Erreurs d’autonome
-   Erreurs de session
-   Erreurs de travaux

### <a name="standalone-errors"></a>Erreurs d’autonome

#### <a name="reference"></a>Référence

            void SendError(string name, Dictionary<object, object> extras = null)

Contrairement aux erreurs de session, les erreurs autonome peuvent se produire en dehors du contexte d’une session.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Erreurs de session

#### <a name="reference"></a>Référence

            void SendSessionError(string name, Dictionary<object, object> extras = null)

Erreurs de session sont généralement utilisés pour signaler les erreurs de l’impact sur l’utilisateur au cours de sa session.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Erreurs de travaux

#### <a name="reference"></a>Référence

            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Les erreurs peuvent être associés à une tâche en cours d’exécution, et non en cours liées à la session utilisateur en cours.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##<a name="reporting-crashes"></a>Reporting des pannes

L’agent fournit deux méthodes pour faire face à des incidents.

### <a name="send-an-exception"></a>Envoyer une exception

#### <a name="reference"></a>Référence

            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Exemple

Vous pouvez envoyer une exception à tout moment en appelant :

            EngagementAgent.Instance.SendCrash(aCatchedException);

Vous pouvez également utiliser un paramètre facultatif pour mettre fin à la session de l’engagement en même temps que l’envoi de l’incident. Pour ce faire, appelez :

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Si vous procédez ainsi, la session et les travaux seront fermés juste après l’envoi de l’incident.

### <a name="send-an-unhandled-exception"></a>Envoyer une exception non gérée

#### <a name="reference"></a>Référence

            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement fournit également une méthode pour envoyer les exceptions non gérées. Ceci est particulièrement utile lorsqu’elle est utilisée à l’intérieur du Gestionnaire d’événement UnhandledException silverlight.

Cette méthode ne **toujours** mettre fin à la session de l’engagement et les tâches après son appel.

#### <a name="example"></a>Exemple

Vous pouvez l’utiliser pour implémenter votre propre gestionnaire UnhandledException (surtout si vous avez désactivé la fonctionnalité de l’Engagement de rapport d’incident automatique). Par exemple, dans le `Application_UnhandledException` méthode de le `App.xaml.cs` fichier :

            // In your App.xaml.cs file
            
            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code
            
              EngagementAgent.Instance.SendCrash(e);
            }

##<a name="onactivated"></a>OnActivated

### <a name="reference"></a>Référence

            void OnActivated(ActivatedEventArgs e)

Lorsque l’utilisateur navigue vers l’avant, à partir d’une application, après le déclenche de l’événement Deactivated, le système d’exploitation va tenter de placer l’application dans un état dormant. Ensuite, l’application est la désactivation tombstone. Dans ce processus, une application est arrêtée, mais certaines données sur l’état de l’application et les pages de l’application sont conservées.

Vous devez insérer `EngagementAgent.Instance.OnActivated(e)` dans les `Application_Activated` méthode à partir du fichier App.xaml.cs pour rétablir l’Agent de l’Engagement, lorsque l’application a été désactivé.

### <a name="example"></a>Exemple

            // Inside your App.xaml.cs file
            
            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

##<a name="device-id"></a>Id de périphérique

            String GetDeviceId()

Vous pouvez obtenir l’id de périphérique engagement en appelant cette méthode.

##<a name="extras-parameters"></a>Paramètres de suppléments

Données arbitraires peuvent être associées à un événement, une erreur, une activité ou une tâche. Ces données peuvent être structurées à l’aide d’un dictionnaire. Les clés et les valeurs peuvent être de n’importe quel type.

Les données de Extras sont sérialisées afin que si vous souhaitez insérer votre propre type de suppléments que vous deviez ajouter un contrat de données pour ce type.

### <a name="example"></a>Exemple

Nous créons une nouvelle classe « Personne ».

            using System.Runtime.Serialization;
            
            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }
            
                // Properties
            
                [DataMember]
                public int Age
                {
                  get;
                  set;
                }
            
                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Ensuite, nous allons ajouter une `Person` instance pour un fichier extra.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);
            
            EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Si vous placez les autres types d’objets, vérifiez que leur méthode ToString() est implémentée pour retourner une chaîne explicite.

### <a name="limits"></a>Limites

#### <a name="keys"></a>Clés

Chaque clé de l’objet doit correspondre à l’expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*$`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille

Extras sont limitées à **1024** caractères par appel.

##<a name="reporting-application-information"></a>Informations sur l’Application de création de rapports

### <a name="reference"></a>Référence

            void SendAppInfo(Dictionary<object, object> appInfos)

Vous pouvez signaler manuellement les fonction de suivi d’informations (ou toutes autres informations spécifiques aux applications) à l’aide de la SendAppInfo().

Notez que ces informations peuvent être envoyées de façon incrémentielle : uniquement la dernière valeur d’une clé spécifique sera conservée pour un périphérique donné. Comme l’événement extras, utiliser un dictionnaire\<objet, objet\> pour joindre les informations.

### <a name="example"></a>Exemple

            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };
            
            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Clés

Chaque clé de l’objet doit correspondre à l’expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*$`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille

Informations sur l’application sont limitées à **1024** caractères par appel.

Dans l’exemple précédent, le JSON envoyé au serveur est 44 caractères :

            {"subscription":"2013-12-07","premium":"true"}
 
##<a name="logging"></a>Enregistrement dans le journal
###<a name="enable-logging"></a>Activer la journalisation

Le SDK peut être configuré pour produire des journaux des tests dans la console de l’IDE.
Ces journaux n’est pas activées par défaut. Pour le personnaliser, mettre à jour de la propriété `EngagementAgent.Instance.TestLogEnabled` à l’une de disponible à partir de la valeur de la `EngagementTestLogLevel` énumération, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
