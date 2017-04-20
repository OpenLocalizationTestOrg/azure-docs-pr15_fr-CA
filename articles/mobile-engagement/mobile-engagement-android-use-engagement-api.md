<properties
    pageTitle="Comment faire pour utiliser l’API de l’Engagement sur Android"
    description="Dernier SDK Android - comment faire pour utiliser l’API de l’Engagement sur Android"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="piyushjo;ricksal" />

#<a name="how-to-use-the-engagement-api-on-android"></a>Comment faire pour utiliser l’API de l’Engagement sur Android

Ce document est un module complémentaire au document [options d’avancées de Reporting pour Android SDK d’Engagement Mobile](mobile-engagement-android-advanced-reporting.md). Il fournit en profondeur plus d’informations sur l’utilisation de l’API de l’Engagement à votre application des statistiques.

Gardez à l’esprit que si vous ne souhaitez que l’Engagement pour signaler les sessions de votre application, activités, blocages et des informations techniques, puis la plus simple consiste à effectuer tous les votre `Activity` sous-classes héritent correspondantes `EngagementActivity` classe.

Si vous souhaitez en faire plus, par exemple, si vous avez besoin de signaler les événements spécifiques de l’application, les erreurs et les tâches, ou si vous devez signaler les activités de votre application d’une manière différente que celle implémentée dans le `EngagementActivity` des classes, vous devez utiliser l’API de l’Engagement.

L’API de l’Engagement est fourni par le `EngagementAgent` classe. Une instance de cette classe peut être extraite en appelant le `EngagementAgent.getInstance(Context)` méthode statique (Notez que la `EngagementAgent` objet retourné est un singleton).

##<a name="engagement-concepts"></a>Concepts de l’engagement

Les parties suivantes affiner les courants [Concepts d’Engagement Mobile](mobile-engagement-concepts.md), pour la plate-forme Android.

### <a name="session-and-activity"></a>`Session`et`Activity`

Si l’utilisateur demeure inactif entre deux *activités*de plus de quelques secondes, sa séquence *d’activités* est fractionnée dans deux *sessions*de distincts. Ces quelques secondes sont appelés « session timeout ».

Une *activité* est généralement associé à un écran de l’application, que l' *activité* démarre lorsque l’écran s’affiche et s’arrête lorsque l’écran est fermé : c’est le cas lorsque le SDK de l’Engagement est intégré à l’aide de la `EngagementActivity` classes.

Mais les *activités* peuvent également être contrôlés manuellement à l’aide de l’API de l’Engagement. Cela permet de fractionner un écran donné en plusieurs parties sub pour obtenir plus de détails sur l’utilisation de l’écran (par exemple à connues la fréquence et la durée pendant laquelle les boîtes de dialogue sont utilisés à l’intérieur de cet écran).

##<a name="reporting-activities"></a>Rapports d’activités

> [AZURE.IMPORTANT] Vous n’avez pas besoin pour le rapport d’activités telles que décrite dans cette section si vous utilisez la `EngagementActivity` classe et ses variantes, comme l’explique comment intégrer un Engagement sur document Android.

### <a name="user-starts-a-new-activity"></a>Utilisateur démarre une nouvelle activité

            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Vous devez appeler `startActivity()` chaque fois que les modifications d’activité utilisateur. Le premier appel à cette fonction démarre une nouvelle session de l’utilisateur.

Le meilleur endroit pour appeler cette fonction se trouve sur chaque activité `onResume` rappel.

### <a name="user-ends-his-current-activity"></a>Utilisateur met fin à l’activité en cours

            EngagementAgent.getInstance(this).endActivity();

Vous devez appeler `endActivity()` au moins une fois lorsque l’utilisateur termine sa dernière activité. Cela informe le SDK de l’Engagement que l’utilisateur est inactif et que la session de l’utilisateur doivent être fermés une fois le délai d’expiration de la session expire (si vous appelez `startActivity()` avant l’expiration du délai d’expiration de la session, la session est tout simplement reprise).

Le meilleur endroit pour appeler cette fonction se trouve sur chaque activité `onPause` rappel.

##<a name="reporting-events"></a>Événements de génération de rapports

### <a name="session-events"></a>Événements de session

Événements de session sont généralement utilisés pour signaler les actions exécutées par un utilisateur lors de sa session.

**Exemple sans données supplémentaires :**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Exemple avec des données supplémentaires :**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Événements autonome

Contrairement aux événements de la session, les événements autonome peuvent se produire en dehors du contexte d’une session.

**Exemple :**

Supposons que vous souhaitez rapport les événements survenant lors du déclenche d’un récepteur de diffusion :

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

##<a name="reporting-errors"></a>Rapport d’erreurs

### <a name="session-errors"></a>Erreurs de session

Erreurs de session sont généralement utilisés pour signaler les erreurs de l’impact sur l’utilisateur au cours de sa session.

**Exemple :**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Erreurs d’autonome

Contrairement aux erreurs de session, les erreurs autonome peuvent se produire en dehors du contexte d’une session.

**Exemple :**

L’exemple suivant montre comment signaler une erreur chaque fois que la mémoire est insuffisante sur le téléphone pendant que votre application est en cours.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

##<a name="reporting-jobs"></a>Tâches de Reporting

### <a name="example"></a>Exemple

Supposons que vous voulez créer un rapport de la durée de votre processus de connexion :

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Signaler les erreurs lors d’un travail

Les erreurs peuvent être associés à une tâche en cours d’exécution, et non en cours liées à la session utilisateur en cours.

**Exemple :**

Supposons que vous souhaitez signaler une erreur au cours de vous connecter de processus :

[...] Connexion publique void (contexte de contexte,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Rapport d’événements lors d’un travail

Les événements peuvent être associés à une tâche en cours d’exécution, et non en cours liées à la session utilisateur en cours.

**Exemple :**

Supposons que nous disposons d’un réseau social et nous utilisons une tâche de rapport à la durée totale pendant laquelle l’utilisateur est connecté au serveur. L’utilisateur peut rester connecté en arrière-plan même quand il utilise une autre application ou lorsque le téléphone est en veille, afin qu’il n’y a aucune session.

L’utilisateur peut recevoir des messages à partir de ses amis, il s’agit d’un événement de tâche.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

##<a name="extra-parameters"></a>Paramètres supplémentaires

Données arbitraires peuvent être associées à des événements, des erreurs, des activités et des tâches.

Ces données peuvent être structurées, il utilise la classe de groupe de Android (en fait, il fonctionne comme les paramètres supplémentaires dans les modes Android). Notez qu’un lot peut contenir les tableaux ou les instances offre un autre.

> [AZURE.IMPORTANT] Si vous placez dans les paramètres parcelable ou sérialisables, vérifiez que leurs `toString()` méthode est implémentée pour retourner une chaîne lisible par l’homme. Les classes sérialisables qui contiennent des champs non transitoires qui ne sont pas sérialisables rendra Android incident lorsque vous appelez`bundle.putSerializable("key",value);`

> [AZURE.WARNING] Tableaux creux dans Paramètres supplémentaires ne sont pas pris en charge, autrement dit, il ne sérialisé comme un tableau. Vous devez les convertir en tableaux standard avant de l’utiliser dans des paramètres supplémentaires.

### <a name="example"></a>Exemple

            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Clés

Chaque clé dans le `Bundle` doit correspondre à l’expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille

Extras sont limitées à **1024** caractères par appel (une fois codées au format JSON par le service de l’Engagement).

Dans l’exemple précédent, le JSON envoyé au serveur est 58 caractères :

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Informations sur l’Application de création de rapports

Vous pouvez manuellement rapport suivi d’informations (ou toutes autres informations spécifiques aux applications) à l’aide de la `sendAppInfo()` fonction.

Notez que ces informations peuvent être envoyées de façon incrémentielle : uniquement la dernière valeur d’une clé spécifique sera conservée pour un périphérique donné.

Comme extras de l’événement, la classe de l’offre groupée est utilisée pour extraire des informations sur l’application, notez que les tableaux ou les bottes secondaire seront traités sous forme de chaînes à deux dimensions (à l’aide de la sérialisation JSON).

### <a name="example"></a>Exemple

Voici un exemple de code pour envoyer la date de naissance et le sexe de l’utilisateur :

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Clés

Chaque clé dans le `Bundle` doit correspondre à l’expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille

Informations sur l’application sont limitées à **1024** caractères par appel (une fois codées au format JSON par le service de l’Engagement).

Dans l’exemple précédent, le JSON envoyé au serveur est 44 caractères :

            {"expiration":"2016-12-07","status":"premium"}
