<properties
 pageTitle="Entités, termes et concepts de planificateur | Microsoft Azure"
 description="Concepts de planificateur Azure, la terminologie et la hiérarchie d’entités, y compris les travaux et les collections de projet.  Montre un exemple d’une tâche planifiée."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Concepts du planificateur, la terminologie, + hiérarchie d’entités

## <a name="scheduler-entity-hierarchy"></a>Hiérarchie d’entités du planificateur

Le tableau suivant décrit les principales ressources exposées ou utilisées par l’API du planificateur :

|Ressources | Description |
|---|---|
|**Collection de travail**|Une collection de travail contient un groupe de tâches et gère des paramètres, des quotas et des limitations qui sont partagées par les tâches au sein de la collection. Une collection de travail est créée par le propriétaire d’un abonnement et groupes de tâches en fonction de l’utilisation ou l’application de limites. Il est limité à une seule région. Il permet également l’application des quotas pour limiter l’utilisation de tous les projets dans cette collection. Les quotas sont MaxJobs et MaxRecurrence.|
|**Travail**|Un travail définit une seule action récurrente, avec des stratégies simples ou complexes pour l’exécution. Actions, citons HTTP, stockage file d’attente, file d’attente de bus de service ou service bus rubrique demandes.|
|**Historique des travaux**|Un historique de travail représente les détails de l’exécution d’un travail. Il contient la réussite ou l’échec, ainsi que les détails de la réponse.|

## <a name="scheduler-entity-management"></a>Gestion des entités du planificateur

À un niveau élevé, le planificateur et l’API de gestion de service exposent les opérations suivantes sur les ressources :

|Capacité|Adresse de l’URI et de la description|
|---|---|
|**Gestion des collections de travail**|GET, PUT et DELETE prise en charge de la création et la modification des collections de travail et les travaux qui s’y trouvent. Une collection de projet est un conteneur pour les tâches et les mappe à des quotas et de paramètres partagés. Sont des exemples de quotas, décrits plus loin, nombre maximal de travaux et plus petit intervalle de périodicité. <p>PUT et DELETE :`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>Télécharger :`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**Gestion des tâches**|GET, PUT, valider, des correctifs et supprimer la prise en charge pour la création et la modification des travaux. Tous les travaux doivent appartenir à une collection de travail qui existe déjà, afin qu’il n’y a aucune création implicite. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**Gestion de l’historique des travaux**|OBTENIR de l’aide pour l’extraction de 60 jours d’historique d’exécution du travail, tels que le temps de travail et les résultats de l’exécution des travaux. Ajoute la prise en charge de paramètre de chaîne requête de filtrage en fonction de l’état et le statut. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## <a name="job-types"></a>Types de tâches

Il existe plusieurs types de tâches : tâches HTTP (y compris les travaux HTTPS qui prend en charge SSL), tâches de file d’attente de stockage, tâches de file d’attente de bus de service et tâches de rubrique de bus de service. Travaux HTTP sont idéales si vous disposez d’un point de terminaison d’une charge de travail ou un service existant. Tâches de file d’attente de stockage vous permet de publier des messages dans les files d’attente de stockage, afin que ces tâches sont idéales pour les charges de travail qui utilisent des files d’attente de stockage. De même, les travaux de bus de service sont idéales pour les charges de travail qui utilisent des rubriques et des files d’attente de bus de service.

## <a name="the-job-entity-in-detail"></a>L’entité « tâche » en détail

À un niveau basique, une tâche planifiée a plusieurs parties :

- L’action à exécuter lorsque la minuterie du travail se déclenche.  

- (Facultatif) L’heure d’exécution de la tâche  

- (Facultatif) Quand et comment souvent répéter la tâche  

- (Facultatif) Une action se déclenche en cas d’échec de l’action principale  

En interne, une tâche planifiée contient également les données fournies par le système telles que l’heure d’exécution planifiée.

Le code suivant fournit un exemple d’une tâche planifiée. Détails sont fournis dans les sections suivantes.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Comme dans l’exemple planifiée ci-dessus, une définition de tâche comporte plusieurs parties :

- Heure de début (« startTime »)  

- Action (« action »), qui inclut l’action sur erreur (« errorAction »)

- Périodicité (« périodicité »)  

- (« État »)  

- État (« status »)  

- Réessayer la stratégie (« retryPolicy »)  

Examinons chacune d’elles en détail :

## <a name="starttime"></a>heure de début

Le « startTime » est l’heure de début et permet à l’appelant de spécifier un décalage de fuseau horaire sur le câble au [format ISO-8601](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>action et errorAction

« action » est l’action appelée sur chaque occurrence et décrit un type d’appel de service. L’action est ce qui sera exécuté sur le tableau fourni. Planificateur prend en charge HTTP, file d’attente de stockage, rubrique bus de service et actions de file d’attente de bus de service.

L’action dans l’exemple ci-dessus est une action de HTTP. Vous trouverez ci-dessous un exemple d’une action de file d’attente de stockage :

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Voici un exemple d’une action de rubrique de bus de service.

  « action » : {« type » : « serviceBusTopic », « serviceBusTopicMessage » : {« topicPath » : « t1 »,  
      « namespace » : « mySBNamespace », « type » de transport : « netMessaging », / / peut être netMessaging ou AMQP « authentification » : {« sasKeyName » : « QPolicy », « type » : « sharedAccessKey »}, « message » : « Un message », « brokeredMessageProperties » : {}, « customMessageProperties » : {« appname » : « FromScheduler »}},}

Voici un exemple d’une action de file d’attente de bus de service :


  « action » : {« serviceBusQueueMessage » : {« nom » : « T1 »,  
      « namespace » : « mySBNamespace », « type de transport » : « netMessaging », / / peut être netMessaging ou AMQP « authentification » : {  
        « sasKeyName » : « QPolicy », « type » : « sharedAccessKey »}, « message » : « Un message »,  
      « brokeredMessageProperties » : {}, « customMessageProperties » : {« appname » : « FromScheduler »}}, « type » : « serviceBusQueue »}

La « errorAction » est le Gestionnaire d’erreurs, l’action appelée en cas d’échec de l’action principale. Vous pouvez utiliser cette variable pour appeler un point de terminaison de gestion des erreurs ou envoyer une notification de l’utilisateur. Cela peut être utilisé pour atteindre un point de terminaison secondaire dans le cas que le serveur principal n’est pas disponible (par exemple, en cas de sinistre sur le site du point de terminaison) ou peut être utilisé pour notifier une erreur de traitement du point de terminaison. Comme action principale, l’action d’erreur peut être simple ou composite logique en fonction des autres actions. Pour apprendre à créer un jeton d’associations de sécurité, reportez-vous à la rubrique pour [créer et utiliser une Signature accès partagé](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="recurrence"></a>Périodicité

Périodicité a plusieurs parties :

- Fréquence : Un des minute, heure, jour, semaine, mois, année  

- Intervalle : De l’intervalle à une fréquence donnée de la périodicité  

- Prescrit de planification : spécifiez les minutes, heures, jours de la semaine, de mois et monthdays de la périodicité  

- : Nombre d’occurrences  

- Heure de fin : aucune tâche ne s’exécutera après l’heure de fin spécifiée  

Un projet est récurrent si elle a un objet d’abonnement spécifié dans sa définition de JSON. Si nombre et l’heure de fin sont spécifiées, la règle d’exécution qui se produit en premier est respectée.

## <a name="state"></a>état

L’état de la tâche est une des quatre valeurs : activé, désactivé, terminé ou erreur. Vous pouvez placer ou de travaux de correctif pour mettre à jour l’état activé ou désactivé. Si une tâche a été terminée ou défectueuse, qui est un état final qui ne peut pas être mis à jour (même si le travail peut encore être supprimé). Un exemple de la propriété state est la suivante :


        "state": "disabled", // enabled, disabled, completed, or faulted
Tâches achevées et défectueux sont supprimés au bout de 60 jours.

## <a name="status"></a>état

Lorsqu’un travail du planificateur a démarré, les informations sont renvoyées sur l’état actuel de la tâche. Cet objet n’est pas définissable par l’utilisateur, elle est définie par le système. Toutefois, il est inclus dans l’objet job (plutôt qu’une ressource liée distincte) afin que le langage permet d’obtenir facilement le statut d’une tâche.

Statut de la tâche comprend le temps de l’exécution précédente (le cas échéant), l’heure de la prochaine exécution (pour les travaux en cours) et le nombre d’exécutions de la tâche.

## <a name="retrypolicy"></a>retryPolicy

En cas d’échec d’une tâche du planificateur, il est possible de spécifier une stratégie de nouvelle tentative pour déterminer si et comment l’action est répétée. Cela est déterminé par l’objet **retryType** , il a la valeur **none** si aucune stratégie de nouvelle tentative, comme indiqué ci-dessus. Valeur **fixe** s’il existe une stratégie de nouvelle tentative.

Pour définir une stratégie de nouvelle tentative, deux paramètres supplémentaires peuvent être spécifiés : un intervalle avant nouvelle tentative (**retryInterval**) et le nombre de tentatives (**retryCount**).

L’intervalle avant nouvelle tentative, spécifié avec l’objet **retryInterval** , est l’intervalle entre les tentatives. Sa valeur par défaut est de 30 secondes, sa valeur configurable minimale est de 15 secondes, et sa valeur maximale est de 18 mois. Travaux dans les collections de travail libre ont une valeur configurable minimale de 1 heure.  Il est défini dans le format ISO 8601. De même, la valeur du nombre de tentatives est spécifiée avec l’objet **retryCount** ; Il est le nombre de fois qu’une nouvelle tentative. Sa valeur par défaut est de 4, et sa valeur maximale est de 20\. les deux **retryInterval** et **retryCount** sont facultatifs. Elles reçoivent leur valeur par défaut de valeurs si **retryType** est défini sur **fixe** et aucune valeur n’est spécifiée explicitement.

## <a name="see-also"></a>Voir aussi

 [Quel est le planificateur ?](scheduler-intro.md)

 [Mise en route à l’aide du planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Comment créer des planifications complexes et périodicité avancée avec le planificateur d’Azure](scheduler-advanced-complexity.md)

 [Référence des API de reste de planificateur Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence d’applets de commande PowerShell de planificateur Azure](scheduler-powershell-reference.md)

 [Azure planificateur haute disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites de planificateur Azure, les valeurs par défaut et les codes d’erreur](scheduler-limits-defaults-errors.md)

 [Azure d’authentification sortante du planificateur](scheduler-outbound-authentication.md)
