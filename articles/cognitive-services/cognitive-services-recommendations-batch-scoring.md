
<properties
    pageTitle="Mise en route de recommandations par lots : API de recommandations d’apprentissage de l’ordinateur | Microsoft Azure"
    description="Machine Azure recommandations--mise en route de recommandations dans des lots d’apprentissage"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="luisca"/>

# <a name="get-recommendations-in-batches"></a>Obtenir des recommandations dans les batchs

>[AZURE.NOTE] Recommandations de lots est plus complexe que l’obtention de recommandations d’un à la fois. Vérifiez les API pour plus d’informations sur la façon d’obtenir des recommandations pour une requête :

> [Élément à recommandations](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br>
> [Recommandations de l’élément de l’utilisateur](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> Lot de notation fonctionne uniquement pour les builds qui ont été créées après le 21 juillet 2016.


Il existe des situations dans lesquelles vous avez besoin obtenir des recommandations pour plusieurs éléments à la fois. Par exemple, vous souhaitez peut-être créer un cache de recommandations ou encore analyser les types de recommandations que vous recevez.

Opérations de score par lots, comme nous l’appelons, sont des opérations asynchrones. Vous devez soumettre la demande, attendez que l’opération se termine et regroupe les résultats.  

Pour être plus précis, voici les étapes à suivre :

1.  Créer un conteneur de stockage Azure si vous n’en avez pas déjà.
2.  Télécharger un fichier d’entrée qui décrit chacune de vos demandes de recommandation pour le stockage des objets Blob Azure.
3.  Démarrer le traitement par lots de calcul des points.
4.  Attendez que l’opération asynchrone se termine.
5.  Une fois l’opération terminée, rassembler les résultats depuis le stockage Blob.

Examinons chacune de ces étapes.

## <a name="create-a-storage-container-if-you-dont-have-one-already"></a>Créer un conteneur de stockage si vous n’en avez pas déjà

Accédez au [portail Azure](https://portal.azure.com) et créer un nouveau compte de stockage si vous n’en avez pas déjà. Pour ce faire, accédez au **Nouveau** > **données** + **stockage** > **Compte de stockage**.

Une fois que vous avez un compte de stockage, vous devez créer les conteneurs blob où vous souhaitez stocker l’entrée et la sortie de l’exécution du traitement par lots.

Téléchargement d’un fichier d’entrée qui décrit votre recommandation chaque demande pour le stockage des objets Blob--appelons la input.json du fichier ici.
Une fois que vous avez un conteneur, vous devez télécharger un fichier qui décrit chacune des demandes que vous devez effectuer cette opération à partir du service de recommandations.

Un lot peut effectuer qu’un seul type de demande à partir d’une build spécifique. Nous expliquerons comment définir ces informations dans la section suivante. Maintenant, supposons que nous allons réaliser élément des recommandations sur une build spécifique. Le fichier d’entrée contient les informations d’entrée (dans ce cas, les éléments de semences) pour chaque requête.

Il s’agit d’un exemple de l’aspect du fichier input.json :

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Comme vous pouvez le voir, le fichier est un fichier JSON, où chaque requête dispose des informations nécessaires envoyer une demande de recommandations. Créer un fichier JSON similaire pour les requêtes dont vous avez besoin pour répondre et le copier vers le conteneur que vous venez de créer dans le stockage Blob.

## <a name="kick-start-the-batch-job"></a>Démarrer le traitement par lots

L’étape suivante consiste à soumettre un nouveau traitement par lots. Pour plus d’informations, consultez la [référence sur l’API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

Le corps de la demande de l’API doit définir les emplacements où l’entrée, de sortie et les fichiers d’erreur doivent être stockés. Il doit également définir les informations d’identification nécessaires accéder à ces emplacements. En outre, vous devez spécifier certains paramètres qui s’appliquent au lot entier (type de recommandations à la demande, le/la construction du modèle à utiliser, le nombre de résultats par appel d’et ainsi de suite).

Il s’agit d’un exemple de quoi doit ressembler le corps de la demande :

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Ici quelques points importants à noter :

-   Actuellement, **authenticationType** doit toujours être défini sur **PublicOrSas**.

-   Vous devez obtenir un jeton de Signature de l’accès partagé (SAS) pour permettre à l’API de recommandations lire et écrire depuis/vers votre compte de stockage Blob. Vous trouverez plus d’informations sur comment générer des jetons SAS sur [la page de l’API de recommandations](../storage/storage-dotnet-shared-access-signature-part-1.md).

-   seul **apiName** est actuellement pris en charge est **ItemRecommend**, qui est utilisé pour les recommandations de l’élément à. Le traitement par lots ne prend actuellement en charge les recommandations de l’élément de l’utilisateur.

## <a name="wait-for-the-asynchronous-operation-to-finish"></a>Attendez que l’opération asynchrone se termine

Lorsque vous démarrez l’opération de traitement par lots, la réponse retourne l’en-tête d’emplacement de l’opération qui vous donne les informations nécessaires effectuer le suivi de l’opération.
Effectuer le suivi de l’opération à l’aide de l' [API de statut opération récupérer]( https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da), comme vous le faîtes pour le suivi du fonctionnement d’une opération de génération.

## <a name="get-the-results"></a>Obtenir les résultats

Une fois l’opération terminée, en supposant qu’aucune erreur, vous pouvez rassembler les résultats à partir de votre sortie stockage Blob.

L’exemple ci-dessous indiquent à quoi doit ressembler la sortie. Dans cet exemple, nous afficher les résultats d’un lot avec seulement deux demandes (par souci de concision).

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## <a name="learn-about-the-limitations"></a>En savoir plus sur les limites

-   Un seul traitement par lots peut être appelée par un abonnement à la fois.
-   Un fichier d’entrée de tâche de traitement par lots ne peut pas être supérieure à 2 Mo.
