<properties 
    pageTitle="Mettre à jour des Services de support après laminage de clés d’accès de stockage | Microsoft Azure" 
    description="Cet article vous donne des conseils sur la mise à jour des Services de support après laminage de clés d’accès de stockage." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="milangada;cenkdin;juliako"/>

#<a name="update-media-services-after-rolling-storage-access-keys"></a>Mise à jour des Services de support après laminage de clés d’accès de stockage

Lorsque vous créez un nouveau compte Azure Media Services, vous êtes également invité à sélectionner un compte de stockage Azure qui est utilisé pour stocker du contenu multimédia. Notez que vous pouvez [Ajouter plusieurs comptes de stockage](meda-services-managing-multiple-storage-accounts.md) pour votre compte de Services de support.

Lors de la création d’un nouveau compte de stockage, Azure génère deux clés d’accès stockage de 512 bits, qui sont utilisés pour authentifier l’accès à votre compte de stockage. Pour sécuriser vos connexions de stockage, il est recommandé de périodiquement de régénérer et de faire pivoter votre clé d’accès de stockage. Deux clés d’accès (principales et secondaires) sont fournis pour vous permettent de mettre à jour les connexions pour le compte de stockage à l’aide d’une touche d’accès rapide pendant que vous régénérez l’autre touche d’accès rapide. Cette procédure est également appelée « touches d’accès rapide propagée ».

Media Services dépend d’une clé de stockage qui lui sont fournie. En particulier, les localisateurs qui sont utilisés pour transmettre en continu ou télécharger vos ressources dépendent de la clé d’accès de stockage spécifié. Lors de la création d’un compte AMS il prend une dépendance sur la clé d’accès de stockage principal par défaut, mais en tant qu’utilisateur vous pouvez mettre à jour la clé de stockage qui dispose de l’AMS. Il se peut que vous devez vous assurer de Media Services vous permettent de savoir quelle clé utiliser en suivant les étapes décrites dans cette rubrique. Également, lors du déploiement des clés d’accès de stockage, vous devez veillez à mettre à jour votre localisateurs donc il sera sans interruption de votre service de transmission en continu (cette étape est également décrite dans la rubrique).

>[AZURE.NOTE]Si vous avez plusieurs comptes de stockage, vous réalisez cette procédure avec chaque compte de stockage.
>
>Avant d’exécuter les étapes décrites dans cette rubrique sur un compte de production, veillez à les tester sur un compte de préproduction.


## <a name="step-1-regenerate-secondary-storage-access-key"></a>Étape 1 : Régénérer la clé d’accès de stockage secondaire

Démarrer avec la régénération de clé de stockage secondaire. Par défaut, la clé secondaire n’est pas utilisée par les Services de support.  Pour plus d’informations sur la façon de restaurer les clés de stockage, reportez-vous à la section [Comment : afficher, copier et accéder aux clés de stockage régénérer](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Étape 2 : Mise à jour de Media Services pour utiliser la nouvelle clé de stockage secondaire

Mise à jour de Media Services pour utiliser la touche d’accès de stockage secondaire. Vous pouvez utiliser une des deux méthodes suivantes pour synchroniser de la clé de stockage régénérée avec Media Services.

- Utiliser le portail Azure : pour rechercher le nom et la clé des valeurs, accédez au portail Azure et sélectionnez votre compte. La fenêtre Paramètres s’affiche sur la droite. Dans la fenêtre Paramètres, sélectionnez clés. En fonction de la clé de stockage souhaité pour les Services de médias à synchroniser avec, sélectionnez la clé primaire de synchroniser ou synchroniser le bouton clé secondaire. Dans ce cas, utilisez la clé secondaire.

- Utiliser la gestion des Services de support API REST.

L’exemple de code suivant montre comment construire la https://endpoint/***subscriptionId/services/mediaservices/comptes/nom de compte*/StorageAccounts/*storageAccountName*demande/Key afin de synchroniser la clé de stockage spécifié avec Media Services. Dans ce cas, la valeur de clé de stockage secondaire est utilisée. Pour plus d’informations, consultez [Comment : Media Services API reste de gestion](http://msdn.microsoft.com/library/azure/dn167656.aspx).
    
    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);
        
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);
        
        
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }
        
        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

Après cette étape, mettre à jour des repères existants (qui ont des dépendances sur l’ancienne clé de stockage), comme indiqué dans l’étape suivante.

>[AZURE.NOTE]Attendez 30 minutes avant d’effectuer des opérations avec les Services de support (par exemple, créer de nouveaux Locator) afin d’éviter tout impact sur les travaux en attente.

##<a name="step-3-update-locators"></a>Étape 3 : Repères de mise à jour

>[AZURE.NOTE]Lors du déploiement des clés d’accès de stockage, vous devez veiller à mettre à jour votre localisateurs existants afin qu’il n’y a aucune interruption de votre service en continu.

Attendez au moins 30 minutes après la synchronisation de la nouvelle clé de stockage avec l’AMS. Ensuite, vous pouvez recréer votre localisateurs à la demande afin qu’ils prennent la dépendance de la clé de stockage spécifié et mettre à jour les URL existante.

Notez que lorsque vous mettez à jour (ou recréez) un localisateur SAS, l’URL sera toujours modifier.

>[AZURE.NOTE] Pour vous assurer que vous conservez les URL existantes des localisateurs de votre demande, vous devez supprimer le localisateur existant et en créer un nouveau avec le même ID.

L’exemple .NET suivant montre comment recréer un repère avec le même ID.

Private ILocator RecreateLocator(CloudMediaContext context, ILocator locator) statique {/ / enregistrer les propriétés de recherche existant.
immobilisation de var = locator. Actif ; var accessPolicy = locator. AccessPolicy ; var locatorId = locator. ID ; var startDate = locator. Heure de début ; var locatorType = locator. Type ; var locatorName = locator. Nom ;

Supprimer les ancien locator.
localisateur. Delete() ;

Si (localisateur. ExpirationDateTime < = DateTime.UtcNow) {throw new Exception (String.Format ("Impossible de recréer le localisateur Id = {0}, car son délai d’expiration de localisateur est dans le passé », locator. ID)) ; }
    
        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);
    
    
    
        return newLocator;
    }


##<a name="step-5-regenerate--primary-storage-access-key"></a>Étape 5 : Régénérer la clé d’accès de stockage principal

Régénérer la clé d’accès de stockage principal. Pour plus d’informations sur la façon de restaurer les clés de stockage, reportez-vous à la section [Comment : afficher, copier et accéder aux clés de stockage régénérer](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##<a name="step-6-update-media-services-to-use-the-new-primary-storage-key"></a>Étape 6 : Mise à jour de Media Services pour utiliser la nouvelle clé de stockage principal
    
Utilisez la même procédure comme décrit à [l’étape 2](media-services-roll-storage-access-keys.md#step2) de cette fois synchroniser la nouvelle clé d’accès de stockage principal avec le compte de Services de support.

>[AZURE.NOTE]Attendez 30 minutes avant d’effectuer des opérations avec les Services de support (par exemple, créer de nouveaux Locator) afin d’éviter tout impact sur les travaux en attente.

##<a name="step-7-update-locators"></a>Étape 7 : Repères de mise à jour  

Après 30 minutes, vous pouvez recréer votre localisateurs à la demande afin qu’ils prennent la dépendance sur la nouvelle clé de stockage principal et maintenir l’URL existante.

Utilisez la même procédure, comme indiqué à [l’étape 3](media-services-roll-storage-access-keys.md#step-3-update-locators).


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###<a name="acknowledgments"></a>Accusés de réception 

Nous aimerions remercier les personnes suivantes qui a contribué à la création de ce document : Cenk Dingiloglu, Milan, Gada, Seva Titov.
