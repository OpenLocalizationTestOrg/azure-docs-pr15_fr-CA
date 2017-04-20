<properties
    pageTitle="Comment faire pour utiliser l’API d’Engagement sur iOS"
    description="Dernière iOS SDK - comment faire pour utiliser l’API d’Engagement sur iOS"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-use-the-engagement-api-on-ios"></a>Comment faire pour utiliser l’API d’Engagement sur iOS

Ce document est un module complémentaire au document comment intégrer un Engagement sur iOS : il fournit en profondeur plus d’informations sur l’utilisation de l’API de l’Engagement à votre application des statistiques.

Gardez à l’esprit que si vous ne souhaitez que l’Engagement pour signaler les sessions de votre application, activités, blocages et des informations techniques, puis la plus simple consiste à faire tout votre personnalisé `UIViewController` objets héritent correspondantes `EngagementViewController` classe.

Si vous souhaitez en faire plus, par exemple, si vous avez besoin de signaler les événements spécifiques de l’application, les erreurs et les tâches, ou si vous devez signaler les activités de votre application d’une manière différente que celle implémentée dans le `EngagementViewController` des classes, vous devez utiliser l’API de l’Engagement.

L’API de l’Engagement est fourni par le `EngagementAgent` classe. Une instance de cette classe peut être extraite en appelant le `[EngagementAgent shared]` méthode statique (Notez que la `EngagementAgent` objet retourné est un singleton).

Avant tous les appels d’API, la `EngagementAgent` objet doit être initialisé en appelant la méthode`[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

##<a name="engagement-concepts"></a>Concepts de l’engagement

Les parties suivantes affiner les courants [Concepts d’Engagement Mobile](mobile-engagement-concepts.md) pour la plateforme iOS.

### <a name="session-and-activity"></a>`Session`et`Activity`

Une *activité* est généralement associé à un écran de l’application, que l' *activité* démarre lorsque l’écran s’affiche et s’arrête lorsque l’écran est fermé : c’est le cas lorsque le SDK de l’Engagement est intégré à l’aide de la `EngagementViewController` classes.

Mais les *activités* peuvent également être contrôlés manuellement à l’aide de l’API de l’Engagement. Cela permet de fractionner un écran donné en plusieurs parties sub pour obtenir plus de détails sur l’utilisation de l’écran (par exemple à connues la fréquence et la durée pendant laquelle les boîtes de dialogue sont utilisés à l’intérieur de cet écran).

##<a name="reporting-activities"></a>Rapports d’activités

### <a name="user-starts-a-new-activity"></a>Utilisateur démarre une nouvelle activité

            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Vous devez appeler `startActivity()` chaque fois que les modifications d’activité utilisateur. Le premier appel à cette fonction démarre une nouvelle session de l’utilisateur.

### <a name="user-ends-his-current-activity"></a>Utilisateur met fin à l’activité en cours

            [[EngagementAgent shared] endActivity];

> [AZURE.WARNING] Vous devez **jamais** appeler cette fonction par vous-même, sauf si vous souhaitez fractionner une utilisation de votre application dans plusieurs sessions : un appel à cette fonction serait fin de la session immédiatement, par conséquent, un appel ultérieur à `startActivity()` doit démarrer une nouvelle session. Cette fonction est appelée automatiquement par le Kit de développement lors de la fermeture de votre application.

##<a name="reporting-events"></a>Événements de génération de rapports

### <a name="session-events"></a>Événements de session

Événements de session sont généralement utilisés pour signaler les actions exécutées par un utilisateur lors de sa session.

**Exemple sans données supplémentaires :**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Exemple avec des données supplémentaires :**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Événements autonome

Contrairement aux événements de la session, les événements autonome peuvent être utilisés en dehors du contexte d’une session.

**Exemple :**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

##<a name="reporting-errors"></a>Rapport d’erreurs

### <a name="session-errors"></a>Erreurs de session

Erreurs de session sont généralement utilisés pour signaler les erreurs de l’impact sur l’utilisateur au cours de sa session.

**Exemple :**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Erreurs d’autonome

Contrairement aux erreurs de session, les erreurs autonome peuvent être utilisés en dehors du contexte d’une session.

**Exemple :**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

##<a name="reporting-jobs"></a>Tâches de Reporting

**Exemple :**

Supposons que vous voulez créer un rapport de la durée de votre processus de connexion :

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Signaler les erreurs lors d’un travail

Les erreurs peuvent être associés à une tâche en cours d’exécution, et non en cours liées à la session utilisateur en cours.

**Exemple :**

Supposons que vous souhaitez signaler une erreur pendant le processus de connexion :

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Événements lors d’un travail

Les événements peuvent être associés à une tâche en cours d’exécution, et non en cours liées à la session utilisateur en cours.

**Exemple :**

Supposons que nous disposons d’un réseau social et nous utilisons une tâche de rapport à la durée totale pendant laquelle l’utilisateur est connecté au serveur. L’utilisateur peut recevoir des messages à partir de ses amis, il s’agit d’un événement de tâche.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

##<a name="extra-parameters"></a>Paramètres supplémentaires

Données arbitraires peuvent être associées à des événements, des erreurs, des activités et des tâches.

Ces données peuvent être structurées, il utilise la classe de NSDictionary d’iOS.

Notez que l’option extras peuvent contenir `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` ou autres `NSDictionary` instances.

> [AZURE.NOTE] Le paramètre supplémentaire est sérialisé au format JSON. Si vous souhaitez passer des objets autre que ceux décrits ci-dessus, vous devez implémenter la méthode suivante dans votre classe :
>
             -(NSString*)JSONRepresentation;
>
> La méthode doit retourner une représentation sous forme de JSON de votre objet.

### <a name="example"></a>Exemple

    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Limites

#### <a name="keys"></a>Clés

Chaque clé dans le `NSDictionary` doit correspondre à l’expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille

Extras sont limitées à **1024** caractères par appel (une fois codées au format JSON par l’agent de l’Engagement).

Dans l’exemple précédent, le JSON envoyé au serveur est 58 caractères :

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Informations sur l’Application de création de rapports

Vous pouvez manuellement rapport suivi d’informations (ou toutes autres informations spécifiques aux applications) à l’aide de la `sendAppInfo:` fonction.

Notez que ces informations peuvent être envoyées de façon incrémentielle : uniquement la dernière valeur d’une clé spécifique sera conservée pour un périphérique donné.

Comme les suppléments de l’événement, le `NSDictionary` classe est utilisée pour extraire des informations sur l’application, notez que les tableaux ou les dictionnaires secondaire seront traités sous forme de chaînes à deux dimensions (à l’aide de la sérialisation JSON).

**Exemple :**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Limites

#### <a name="keys"></a>Clés

Chaque clé dans le `NSDictionary` doit correspondre à l’expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille

Informations sur l’application sont limitées à **1024** caractères par appel (une fois codées au format JSON par l’agent de l’Engagement).

Dans l’exemple précédent, le JSON envoyé au serveur est 44 caractères :

    {"birthdate":"1983-12-07","gender":"female"}
