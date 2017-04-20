<properties 
    pageTitle="Comment pour utiliser Blitline pour image traitement - Azure fonction de guide" 
    description="Apprenez à utiliser le service de Blitline pour traiter des images dans une application Azure." 
    services="" 
    documentationCenter=".net" 
    authors="blitline-dev" 
    manager="jason@blitline.com" 
    editor="jason@blitline.com"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/09/2014" 
    ms.author="support@blitline.com"/>
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Comment utiliser Blitline avec Azure et d’Azure Storage

Ce guide vous explique comment accéder aux services de Blitline et soumettre des travaux à Blitline.

## <a name="what-is-blitline"></a>Quel est Blitline ?

Blitline est une service qui fournit le traitement d’image au niveau entreprise en une fraction du prix que coûterait pour le générer vous-même de traitement d’image en nuage.

Le fait est que traitement d’image a été fait encore et encore, généralement reconstruits dès le départ pour chaque site. Nous sommes conscients de ce parce que nous les avons créés un million de fois trop. Un jour, que nous avons décidé qu’il est peut-être temps nous tout faire pour tout le monde. Nous savons comment le faire, le faire rapidement et efficacement, et d’enregistrer tout le monde de travail entre-temps.

Pour plus d’informations, consultez [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>Que Blitline n’est pas...

Pour clarifier ce qui est utile pour les Blitline, il est souvent plus facile d’identifier ce que Blitline ne fait pas avant de se déplacer vers l’avant.

- Blitline n’a pas de widgets HTML pour télécharger des images. Vous devez disposer des images disponibles publiquement ou avec des autorisations restreintes disponibles pour atteindre Blitline.

- Blitline ne live image de traitement, comme Aviary.com

- Blitline n’accepte pas de téléchargement des images, vous est impossible de diffuser vos images directement à Blitline. Vous devez les dirige vers le stockage Azure ou autres emplacements prend en charge de Blitline et ensuite d’indiquer où les obtenir Blitline.

- Blitline massivement parallèle et n’effectue aucun traitement synchrone. Ce qui signifie que vous devez nous fournir un postback_url et nous pouvons vous dire lorsque nous avons terminé traitement.

## <a name="create-a-blitline-account"></a>Créer un compte Blitline

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Comment faire pour créer un travail de Blitline

Blitline utilise JSON pour définir les actions à effectuer sur une image. Cette JSON est composé de quelques champs simples.

L’exemple le plus simple est la suivante :

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Ici nous avons JSON qui prendra une image « src » «... boys.jpeg » et redimensionnez cette image à 240 x 140.

L’ID d’Application est quelque chose que vous trouverez dans l’onglet **Infos sur la connexion** ou **Gérer** sur Azure. Il correspond à l’identificateur secrète qui vous permet d’exécuter les travaux sur Blitline.

Le paramètre « Enregistrer » identifie les informations où vous souhaitez placer l’image une fois que nous avons le traitement. Dans ce cas trivial, nous n’avons pas défini une. Si aucun emplacement n’est défini Blitline va stocker localement (et temporairement) à un emplacement unique de nuage. Vous ne pourrez pas obtenir cet emplacement dans le JSON retourné par Blitline lorsque vous effectuez le Blitline. L’identificateur « image » est obligatoire et est renvoyé lors de l’enregistrement afin d’identifier la donnée image.

Vous trouverez plus d’informations sur les *fonctions* que nous prenons en charge ici : <http://www.blitline.com/docs/functions>

Vous pouvez également rechercher la documentation sur les options de travail ici : <http://www.blitline.com/docs/api>

Une fois que vous avez votre JSON tout ce que vous devez faire est **POST** pour`http://api.blitline.com/job`

Vous obtiendrez en JSON qui ressemble à ceci :

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Cela vous indique que Blitline a reçu votre demande, il a placer dans une file d’attente de traitement, et lorsqu’il a terminé l’image sera disponible à l’adresse : **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Comment faire pour enregistrer une image pour votre compte de stockage Azure

Si vous avez un compte de stockage Azure, vous pouvez facilement avoir Blitline pousser les images traitées dans votre conteneur Azure. En ajoutant un « azure_destination », vous définissez l’emplacement et les autorisations pour Blitline pousser vers.

Voici un exemple :

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


En complétant les valeurs CAPITALIZED avec votre propre, vous pouvez soumettre cette JSON à http://api.blitline.com/job et l’image « src » est traité avec un filtre Flou et puis envoyée vers la destination Azure vous.

###<a name="please-note"></a>Veuillez noter :

Les associations de sécurité doivent contenir l’url complète de SAS, y compris le nom de fichier du fichier de destination.

Exemple :

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Vous pouvez également lire la dernière édition de documents de Blitline stockage Azure [ici](http://www.blitline.com/docs/azure_storage).


## <a name="next-steps"></a>Étapes suivantes

Visitez le site blitline.com pour en savoir plus sur nos autres fonctionnalités :

* Point de terminaison API Blitline de documents <http://www.blitline.com/docs/api>
* Des fonctions API de Blitline <http://www.blitline.com/docs/functions>
* Exemples de Blitline API <http://www.blitline.com/docs/examples>
* Troisième partie Nuget bibliothèque <http://nuget.org/packages/Blitline.Net>
