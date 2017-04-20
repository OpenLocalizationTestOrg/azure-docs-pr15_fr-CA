<properties
    pageTitle="Liste des ressources de stockage Azure avec la bibliothèque de Client de stockage Microsoft Azure pour C++ | Microsoft Azure"
    description="Apprenez à utiliser l’API de liste dans la bibliothèque de Client Microsoft Azure stockage pour C++ pour énumérer les conteneurs, les BLOB, les files d’attente, les tables et les entités."
    documentationCenter=".net"
    services="storage"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="list-azure-storage-resources-in-c"></a>Liste des ressources de stockage Azure dans C++

Liste des opérations sont essentielles à de nombreux scénarios de développement avec stockage Azure. Cet article décrit comment énumérer plus efficacement les objets dans le stockage Azure à l’aide de la liste des API fournies dans la bibliothèque de Client Microsoft Azure stockage pour C++.

>[AZURE.NOTE] Ce guide vise à la bibliothèque de Client de stockage Azure pour C++ version 2.x, ce qui est disponible via [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

La bibliothèque cliente de stockage fournit des méthodes pour les objets de liste ou de la requête dans le stockage Azure. Cet article traite des scénarios suivants :

-   Liste des conteneurs d’un compte
-   Liste des BLOB dans un conteneur ou d’un répertoire de blob virtuel
-   Files d’attente de la liste dans un compte
-   Liste des tables dans un compte
-   Entités de requête dans une table

Chacune de ces méthodes est affiché à l’aide des surcharges différentes pour différents scénarios.

## <a name="asynchronous-versus-synchronous"></a>Asynchrone et synchrone

La bibliothèque cliente de stockage pour C++ est basée sur la [bibliothèque du reste de C++](https://github.com/Microsoft/cpprestsdk), nous, par nature, prend en charge les opérations asynchrones à l’aide de [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Par exemple :

    pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;

Opérations synchrones encapsulez les opérations asynchrones correspondantes :

    list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
    {
        return list_blobs_segmented_async(token).get();
    }

Si vous travaillez avec plusieurs applications ou services de thread, nous vous recommandons d’utiliser l’API d’async directement au lieu de créer un thread d’appeler l’API, ce qui affecte considérablement les performances de synchronisation.

## <a name="segmented-listing"></a>Liste segmentée

L’échelle d’un stockage en nuage requiert la liste segmentée. Par exemple, peut avoir sur un million des BLOB dans un conteneur blob Azure ou sur un milliard des entités dans une Table d’Azure. Ceux-ci ne sont pas des numéros théoriques, mais des cas d’utilisation réels des clients.

Il est donc impossible de lister tous les objets dans une seule réponse. Au lieu de cela, vous pouvez répertorier les objets à l’aide de la pagination. Chaque option de la liste des API a un *segmenté* de surcharge.

La réponse à une opération de liste segmentée comprend :

-   <i>_segment</i>, qui contient le jeu de résultats retournés pour un seul appel à l’API de la liste.
-   *continuation_token*, qui est passée à l’appel suivant pour obtenir la page de résultats suivante. Lorsqu’il n’y a pas plus de résultats à retourner, le jeton de continuation est null.

Par exemple, un appel classique à la liste de tous les objets BLOB dans un conteneur peut ressembler à l’extrait de code suivant. Le code est disponible dans nos [exemples](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

    // List blobs in the blob container
    azure::storage::continuation_token token;
    do
    {
        azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_diretory(it->as_directory());
        }
    }

        token = segment.continuation_token();
    }
    while (!token.empty());

Notez que le nombre de résultats renvoyés dans une page peut être contrôlé par le paramètre *max_results* dans la surcharge de chaque API, par exemple :

    list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
        blob_listing_details::values includes, int max_results, const continuation_token& token,
        const blob_request_options& options, operation_context context)

Si vous ne spécifiez pas le paramètre *max_results* , la valeur maximale par défaut jusqu'à 5 000 résultats est retournée dans une seule page.

Notez également qu’une requête contre le stockage Azure Table peut retourner aucun enregistrement ou moins d’enregistrements que la valeur du paramètre *max_results* que vous avez spécifié, même si le jeton de continuation n’est pas vide. Une raison peut être que la requête n’a pas pu terminer en cinq secondes. Tant que le jeton de continuation n’est pas vide, la requête doit continuer, et votre code ne doit pas supposer la taille des résultats du segment.

Le modèle de codage recommandé pour la plupart des scénarios est segmenté répertoriant, qui fournit une progression explicite de l’annonce ou l’interrogation et la manière dont le service répond à chaque demande. En particulier pour les applications de C++ ou de services, contrôle de niveau inférieur de la progression de la liste peut aider à mémoire de contrôle et de performances.

## <a name="greedy-listing"></a>Annonce gourmand

Les versions antérieures de la bibliothèque de Client de stockage pour C++ (aperçu des versions 0.5.0 et versions antérieures) inclus non segmenté annonce API pour les tables et les files d’attente, comme dans l’exemple suivant :

    std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
    std::vector<table_entity> execute_query(const table_query& query) const;
    std::vector<cloud_queue> list_queues() const;

Ces méthodes ont été implémentées comme des wrappers d’API segmenté. Pour chaque réponse de liste segmentée, le code ajouté les résultats à un vecteur et retourné tous les résultats une fois les conteneurs complets ont été analysés.

Cette approche peut fonctionner lorsque le compte de stockage ou de la table contient un petit nombre d’objets. Toutefois, avec une augmentation du nombre d’objets, la mémoire requise peut augmenter sans limite, dans la mesure où tous les résultats sont restés dans la mémoire. Une opération de vente peut prendre beaucoup de temps, au cours de laquelle l’appelant ne dispose pas d’informations sur sa progression.

Ces annonce gourmand API dans le SDK n’existent pas dans C#, Java ou l’environnement JavaScript Node.js. Pour éviter les problèmes potentiels de l’utilisation de ces API gourmand, nous avons supprimé dans la version 0.6.0 aperçu.

Si votre code appelle ces API gourmand :

    std::vector<azure::storage::table_entity> entities = table.execute_query(query);
    for (auto it = entities.cbegin(); it != entities.cend(); ++it)
    {
        process_entity(*it);
    }

Puis, vous devez modifier votre code pour utiliser la liste segmentée API :

    azure::storage::continuation_token token;
    do
    {
        azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
        {
            process_entity(*it);
        }

        token = segment.continuation_token();
    } while (!token.empty());

En spécifiant le paramètre *max_results* du segment, vous pouvez trouver un équilibre entre le nombre de demandes et l’utilisation de la mémoire pour satisfaire aux considérations de performances pour votre application.

En outre, si vous utilisez les API de liste segmentée, mais stockez les données dans une collection locale dans un style « grossistes », également fortement recommandé que vous refactorisez votre code pour gérer le stockage des données dans une collection locale avec soin à grande échelle.

## <a name="lazy-listing"></a>Annonce différée

Annonce gourmand déclenché les problèmes potentiels, mais il est pratique s’il n’y a pas trop d’objets dans le conteneur.

Si vous utilisez également C# ou Oracle Java SDK, vous devez être familiarisé avec le modèle de programmation énumérable, qui offre un paresseux de style liste, où les données à un certain offset sont lue uniquement si cela est nécessaire. En C++, le modèle basé sur un itérateur fournit également une approche similaire.

Une liste différée typique API, à l’aide de **list_blobs** , par exemple, se présente comme suit :

    list_blob_item_iterator list_blobs() const;

Un extrait de code par défaut qui utilise le modèle de vente différée peut ressembler à ceci :

    // List blobs in the blob container
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_directory(it->as_directory());
        }
    }

Notez que paresseux annonce est uniquement disponible en mode synchrone.

Annonce différée par rapport à la liste gourmande, extrait les données uniquement lorsque cela est nécessaire. Dans les coulisses, il extrait les données de stockage Azure uniquement lorsque l’itérateur suivant se déplace dans le segment suivant. Par conséquent, l’utilisation de la mémoire est contrôlée avec une taille limitée, et l’opération est rapide.

API de liste différée sont incluses dans la bibliothèque Client de stockage pour C++ version 2.2.0.

## <a name="conclusion"></a>Conclusion

Dans cet article, nous avons abordé les différentes surcharges pour répertorier les API pour les divers objets de la bibliothèque de Client de stockage pour C++. Pour résumer :

-   Async APIs sont fortement recommandés dans plusieurs scénarios de threads.
-   Liste segmentée est recommandée pour la plupart des scénarios.
-   Annonce différée est fourni dans la bibliothèque sous la forme d’un wrapper utile dans les scénarios synchrones.
-   Annonce gourmande n’est pas recommandée et a été supprimé de la bibliothèque.

##<a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le stockage Azure et la bibliothèque cliente pour C++, consultez les ressources suivantes.

-   [Comment faire pour utiliser le stockage Blob à partir de C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Comment faire pour utiliser le stockage de Table à partir de C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Comment faire pour utiliser le stockage de file d’attente à partir de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Bibliothèque Client de stockage Azure pour la documentation de l’API C++.](http://azure.github.io/azure-storage-cpp/)
-   [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
