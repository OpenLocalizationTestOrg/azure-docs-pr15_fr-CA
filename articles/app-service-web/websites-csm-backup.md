<properties
    pageTitle="RESTE permet de sauvegarder et de restaurer des applications de Service d’application"
    description="Découvrez comment utiliser les appels API RESTful pour sauvegarder et restaurer une application dans le Service d’application Azure"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>RESTE permet de sauvegarder et de restaurer des applications de Service d’application

> [AZURE.SELECTOR]
- [PowerShell](../app-service/app-service-powershell-backup.md)
- [API REST](websites-csm-backup.md)

[Les applications de Service d’application](https://azure.microsoft.com/services/app-service/web/) peuvent être sauvegardés en tant que BLOB dans le stockage Azure. La sauvegarde peut également contenir des bases de données de l’application. Si l’application est déjà accidentellement supprimée, ou si l’application doit être rétabli une précédente version, elle peut être restaurée à partir d’une sauvegarde précédente. Sauvegardes peuvent être effectuées à tout moment à la demande, ou les sauvegardes peuvent être planifiées à des intervalles appropriés.

Cet article explique comment sauvegarder et restaurer une application avec les demandes d’API RESTful. Si vous souhaitez créer et gérer des sauvegardes d’application graphique via le portail Azure, reportez-vous à la section [sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md)

<a name="gettingstarted"></a>
## <a name="getting-started"></a>Mise en route
Pour envoyer des demandes REST, vous devez connaître le **nom**de votre application, **groupe de ressources**et **id de l’abonnement**. Vous trouverez ces informations en cliquant sur votre application dans la lame de **Service de l’application** du [portail Azure](https://portal.azure.com). Pour les exemples de cet article, nous allons configurer le site Web **backuprestoreapiexamples.azurewebsites.net**. Il est stocké dans le groupe de ressources par défaut-Web-WestUS et est en cours d’exécution sur un abonnement avec l’ID 00001111-2222-3333-4444-555566667777.

![Informations de l’exemple de site Web][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## <a name="backup-and-restore-rest-api"></a>Sauvegarde et restauration des API REST
Nous allons maintenant plusieurs exemples d’utilisation de l’API REST pour sauvegarder et restaurer une application. Chaque exemple inclut un corps de demande URL et HTTP. L’URL de l’exemple contient les espaces réservés encapsulés dans des accolades, comme {id d’abonnement}. Remplacez les espaces réservés par les informations correspondant à votre application. Pour référence, voici une explication de chaque espace réservé qui apparaît dans l’URL de l’exemple.

* abonnement-id : ID de l’abonnement Azure contenant l’application
* ressources-nom-groupe – nom du groupe de ressources contenant l’application
* nom – nom de l’application
* sauvegarde-id : ID de la sauvegarde de l’application

Pour la documentation complète de l’API, y compris plusieurs paramètres facultatifs qui peuvent être inclus dans la demande HTTP, consultez l' [Explorateur de ressources Azure](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## <a name="backup-an-app-on-demand"></a>Sauvegarde d’une application à la demande
Pour sauvegarder une application immédiatement, envoyez une demande **POST** à **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/**.

Voici à quoi ressemble l’URL à l’aide de notre site Web d’exemple. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/Backup/**

Fournir un objet JSON dans le corps de votre demande de spécifier le compte de stockage à utiliser pour stocker la sauvegarde. L’objet JSON doit avoir une propriété nommée **storageAccountUrl**, qui contient une [URL de SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) , l’octroi d’un accès en écriture au conteneur de stockage Azure qui contient l’objet blob de sauvegarde. Si vous souhaitez sauvegarder vos bases de données, vous devez également fournir une liste contenant les noms, les types et les chaînes de connexion à des bases de données à sauvegarder.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Une sauvegarde de l’application commence immédiatement lors de la réception de la demande. Le processus de sauvegarde peut prendre un certain temps. La réponse HTTP contient un code que vous pouvez utiliser dans une autre demande pour connaître l’état de la sauvegarde. Voici un exemple du corps de la réponse HTTP à notre demande de sauvegarde.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] Vous pouvez trouver les messages d’erreur dans la propriété log de la réponse HTTP.

<a name="schedule-automatic-backups"></a>
## <a name="schedule-automatic-backups"></a>Planifier des sauvegardes automatiques
En plus de la sauvegarde d’une application à la demande, vous pouvez également planifier une sauvegarde automatique.

### <a name="set-up-a-new-automatic-backup-schedule"></a>Configurer une nouvelle planification de sauvegarde automatique
Pour configurer une planification de sauvegarde, envoyer une demande de **mise** à **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**.

Voici à quoi ressemble l’URL pour le site Web de notre exemple. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/config/Backup**

Le corps de la requête doit avoir un objet JSON qui spécifie la configuration de la sauvegarde. Voici un exemple avec tous les paramètres requis.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

Cet exemple configure l’application à sauvegarder automatiquement tous les sept jours. Les paramètres **frequencyInterval** et **frequencyUnit** ensemble de déterminer la fréquence à laquelle les sauvegardes se produisent. Les valeurs valides de **frequencyUnit** sont **heure** et le **jour**. Par exemple, pour sauvegarder une application toutes les 12 heures, affectez la valeur à 12 et frequencyUnit à l’heure.

Anciennes sauvegardes sont supprimées automatiquement à partir du compte de stockage. Vous pouvez contrôler l’âge que les sauvegardes peuvent être en définissant le paramètre **retentionPeriodInDays** . Si vous souhaitez toujours avoir au moins une sauvegarde enregistrée, quelle que soit l’ancienneté il, **keepAtLeastOneBackup** sur true.

### <a name="get-the-automatic-backup-schedule"></a>Obtenir la planification de sauvegarde automatique
Pour obtenir la configuration de la sauvegarde d’une application, envoyer une demande **POST** vers l’URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list**.

L’URL pour le site de notre exemple est **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>
## <a name="get-the-status-of-a-backup"></a>Obtenir l’état d’une sauvegarde
En fonction de la taille est de l’application, une sauvegarde peut prendre un certain temps. Les sauvegardes peuvent également échouer, le délai d’attente, ou réussir partiellement. Pour voir l’état des sauvegardes de tous les l’application, envoie une requête **GET** à l’URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups**.

Pour afficher l’état d’une sauvegarde spécifique, envoyez une demande GET à l' URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Voici à quoi ressemble l’URL pour le site Web de notre exemple. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/Backups/1**

Le corps de la réponse contient un objet JSON similaire à l’exemple.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

L’état d’une sauvegarde est un type énuméré. Voici tous les états possibles.

* 0 – InProgress : la sauvegarde a commencé mais n’est pas encore terminée.
* 1 – a échoué : La sauvegarde a échoué.
* 2 – a réussi : La sauvegarde s’est terminée correctement.
* 3 – TimedOut : La sauvegarde ne s’est pas terminée dans le temps et a été annulée.
* 4 : créé : La requête de sauvegarde est en attente mais n’a pas été démarrée.
* 5 – ignorée : La sauvegarde n’est pas allé à cause d’un planning de sauvegardes trop de déclenchement.
* 6 – PartiallySucceeded : Réussite de la sauvegarde, mais certains fichiers ont été sauvegardés pas parce qu’ils ne pourraient pas être lus. Cela se produit généralement parce qu’un verrou exclusif a été placé sur les fichiers.
* 7 – DeleteInProgress : La sauvegarde a été demandée à supprimer, mais n’a ne pas encore été supprimé.
* 8 – Échec de la suppression : La sauvegarde n’a pas pu être supprimée. Cela peut se produire parce que l’URL de SAS qui a été utilisé pour créer la sauvegarde a expiré.
* 9 – supprimé : La sauvegarde a été supprimée.

<a name="restore-app"></a>
## <a name="restore-an-app-from-a-backup"></a>Restauration d’une application à partir d’une sauvegarde
Si votre application a été supprimée, ou si vous souhaitez rétablir votre application à une version antérieure, vous pouvez restaurer l’application à partir d’une sauvegarde. Pour appeler une restauration, envoyez une demande **POST** vers l’URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore**.

Voici à quoi ressemble l’URL pour le site Web de notre exemple. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/Backups/1/Restore**

Dans le corps de la demande, envoyer un objet JSON qui contient les propriétés de l’opération de restauration. Voici un exemple qui contient toutes les propriétés requises :

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>Restaurer vers une nouvelle app
Parfois vous pouvez souhaiter créer une nouvelle application lorsque vous restaurez une sauvegarde, plutôt que de remplacer une application existante. Pour ce faire, modifiez l’URL de la demande pour pointer vers la nouvelle application que vous souhaitez créer et modifier la propriété de **remplacement** dans le JSON à **false**.

<a name="delete-app-backup"></a>
## <a name="delete-an-app-backup"></a>Supprimer une application de sauvegarde
Si vous souhaitez supprimer une sauvegarde, envoyez une demande de **suppression** pour l' URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Voici à quoi ressemble l’URL pour le site Web de notre exemple. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/Backups/1**

<a name="manage-sas-url"></a>
## <a name="manage-a-backups-sas-url"></a>Gérer des URL de SAS d’une sauvegarde
Service d’application Azure va tenter de supprimer votre sauvegarde du stockage Azure à l’aide de l’URL SAS qui a été fourni lors de la sauvegarde a été créée. Si cette URL SAS n’est plus valide, la sauvegarde ne peut pas être supprimée par le biais de l’API REST. Toutefois, vous pouvez mettre à jour l’URL de SAS associé à une sauvegarde par l’envoi d’une demande **POST** vers l’URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list**.

Voici à quoi ressemble l’URL pour le site Web de notre exemple. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/Backups/1/List**

Dans le corps de la demande, envoyer un objet JSON qui contient la nouvelle URL de SAS. Voici un exemple.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] Pour des raisons de sécurité, l’URL de SAS associée à une sauvegarde n’est pas renvoyé lors de l’envoi d’une demande GET pour une sauvegarde spécifique. Si vous souhaitez afficher l’URL SAS associé à une sauvegarde, envoyez une demande POST à la même URL ci-dessus. Inclure un objet JSON vide dans le corps de la demande. La réponse du serveur contient toutes les informations de sauvegarde, y compris son URL SAS.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png
