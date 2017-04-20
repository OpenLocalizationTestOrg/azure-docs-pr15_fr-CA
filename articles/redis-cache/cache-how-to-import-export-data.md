<properties 
    pageTitle="Importer et exporter des données dans le Cache de Redis Azure | Microsoft Azure" 
    description="Découvrez comment importer et exporter des données vers et depuis le stockage blob avec vos instances de Cache Redis d’Azure premium" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="import-and-export-data-in-azure-redis-cache"></a>Importer et exporter des données dans le Cache de Redis Azure

Importation/exportation est une opération de gestion de données de Cache Redis d’Azure, qui vous permet d’importer des données dans le Cache de Redis Azure ou exporter des données à partir du Cache de Redis Azure par l’importation et l’exportation d’une capture instantanée de base de données de Cache Redis (RDB) à partir d’un cache de prime à un blob de page dans un compte de stockage Azure. Importation/exportation vous permet de migrer entre les différentes instances de Cache Redis d’Azure ou de remplir le cache avec les données avant de l’utiliser.

Cet article fournit un guide pour l’importation et l’exportation de données avec Azure Redis de Cache et fournit les réponses aux questions fréquemment posées.

>[AZURE.IMPORTANT] Importation/exportation est en aperçu et n’est disponible que pour les caches de [niveau de prime](cache-premium-tier-intro.md) .

## <a name="import"></a>Importation

Importation permet de mettre les fichiers RDB compatibles Redis à partir de n’importe quel serveur de Redis en cours d’exécution dans un nuage ou l’environnement, y compris les Redis en cours d’exécution sur n’importe quel fournisseur de nuage tels que Amazon Web Services, Windows ou Linux. Importation de données est un moyen simple de créer un cache de données prédéfinies. Pendant le processus d’importation, Cache de Redis Azure charge les fichiers RDB du stockage Azure en mémoire et insère ensuite les clés dans le cache.

>[AZURE.NOTE] Avant de commencer l’opération d’importation, assurez-vous que votre base de données Redis (RDB) ou les fichiers sont téléchargés dans le BLOB de page dans le stockage Azure, dans la même région et d’abonnement comme votre instance de Cache Redis d’Azure. Pour plus d’informations, consultez [mise en route de stockage des objets Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md). Si vous avez exporté votre fichier RDB à l’aide de la fonctionnalité [d’Exportation de Cache Redis Azure](#export) , votre fichier RDB est déjà stocké dans un objet blob de page et est prêt pour l’importation.

1. Pour importer une ou plusieurs BLOB mémoire cache exportés, [accédez à votre cache](cache-configure.md#configure-redis-cache-settings) dans Azure portal, puis cliquez sur **Importer des données** à partir de la blade de **paramètres** de votre instance de cache.

    ![Importation de données][cache-import-data]

2. Cliquez sur **Choisir une Blob(s)** et sélectionnez le compte de stockage contenant les données à importer.

    ![Choisissez le compte de stockage][cache-import-choose-storage-account]

3. Cliquez sur le conteneur qui contient les données à importer.

    ![Choisir un conteneur][cache-import-choose-container]

4. Sélectionnez un ou plusieurs des objets BLOB à importer en cliquant sur la zone à gauche du nom du blob, puis cliquez sur **Sélectionner**.

    ![Choisissez les objets BLOB][cache-import-choose-blobs]

5. Cliquez sur **Importer** pour commencer le processus d’importation.

    >[AZURE.IMPORTANT] Le cache n’est pas accessible par les clients du cache pendant le processus d’importation, et toutes les données existantes dans le cache sont supprimées.

    ![Importation][cache-import-blobs]

    Vous pouvez surveiller la progression de l’opération d’importation en suivant les notifications à partir du portail Azure ou en affichant les événements de l' [audit de connexion](cache-configure.md#support-amp-troubleshooting-settings).

    ![Importation en cours][cache-import-data-import-complete] 


## <a name="export"></a>Exporter

Exporter vous permet d’exporter les données stockées dans le Cache de Redis Azure pour Redis les fichiers RDB compatibles. Vous pouvez utiliser cette fonction pour déplacer des données d’une instance de Cache Redis d’Azure à un autre ou vers un autre serveur Redis. Pendant le processus d’exportation, un fichier temporaire est créé sur l’ordinateur virtuel qui héberge l’instance de serveur de Cache de Redis d’Azure, et le fichier est téléchargé vers le compte de stockage désigné. Une fois l’opération d’exportation terminée avec soit un état de réussite ou d’échec, le fichier temporaire est supprimé.

1. Pour exporter le contenu actuel du cache de stockage, [accédez à votre cache](cache-configure.md#configure-redis-cache-settings) dans Azure portal, puis cliquez sur **exporter des données** à partir de la blade de **paramètres** de votre instance de cache.

    ![Choisir un conteneur de stockage][cache-export-data-choose-storage-container]

2. Cliquez sur **Choisir un conteneur de stockage** et sélectionnez le compte de stockage de votre choix. Le compte de stockage doit être dans le même abonnement et région comme votre cache.

    >[AZURE.IMPORTANT] Importation/exportation fonctionne avec les objets BLOB de page, qui est pris en charge par les deux classique et les comptes de stockage ARM, mais n’est pas pris en charge par [Blob des comptes de stockage](../storage/storage-blob-storage-tiers.md#blob-storage-accounts) pour l’instant.

    ![Compte de stockage][cache-export-data-choose-account]

3. Choisissez le conteneur blob souhaité et cliquez sur **Sélectionner**. Pour utiliser de nouveau un conteneur, cliquez sur **Ajouter un conteneur** pour les ajouter en premier et sélectionnez-le dans la liste.

    ![Choisir un conteneur de stockage][cache-export-data-container]

4. Tapez un **préfixe de nom d’objet Blob** et cliquez sur **Exporter** pour démarrer le processus d’exportation. Le préfixe de nom d’objet blob est utilisé pour préfixer les noms de fichiers générés par cette opération d’exportation.

    ![Exporter][cache-export-data]

    Vous pouvez surveiller la progression de l’opération d’exportation en suivant les notifications à partir du portail Azure ou en affichant les événements de l' [audit de connexion](cache-configure.md#support-amp-troubleshooting-settings).

    ![][cache-export-data-export-complete]

    Caches restent disponibles pour une utilisation pendant le processus d’exportation.


## <a name="importexport-faq"></a>Forum aux questions d’importation/exportation

Cette section contient des questions fréquemment posées sur la fonctionnalité d’importation/exportation.

-   [Quelle tarification niveaux permet l’importation/exportation ?](#what-pricing-tiers-can-use-importexport)
-   [Puis-je importer des données à partir de n’importe quel serveur de Redis ?](#can-i-import-data-from-any-redis-server)
-   [Mon cache seront disponible au cours d’une opération d’importation/exportation ?](#will-my-cache-be-available-during-an-importexport-operation)
-   [Puis-je utiliser importation/exportation avec Redis cluster ?](#can-i-use-importexport-with-redis-cluster)
-   [Comment importer/exporter ne fonctionne pas avec un paramètre personnalisé de la bases de données ?](#how-does-importexport-work-with-a-custom-databases-setting)
-   [Comment est importation/exportation différente de persistance de Redis ?](#how-is-importexport-different-from-redis-persistence)
-   [Puis-je automatiser importation/exportation à l’aide de PowerShell, CLI ou autres clients de gestion ?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-   [J’ai reçu une erreur de délai d’attente pendant mon opération d’importation/exportation. Ce que cela signifie ?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-   [J’ai reçu une erreur lors de l’exportation de mes données pour le stockage des objets Blob Azure. Que s'est-il passé ?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### <a name="what-pricing-tiers-can-use-importexport"></a>Quelle tarification niveaux permet l’importation/exportation ?

Importation/exportation n’est disponible que dans la prime, niveau de tarification.

### <a name="can-i-import-data-from-any-redis-server"></a>Puis-je importer des données à partir de n’importe quel serveur de Redis ?

Oui, en plus de l’importation de données exportées à partir d’instances de Cache Redis d’Azure, vous pouvez importer des fichiers RDB à partir de n’importe quel serveur de Redis en cours d’exécution dans un nuage ou l’environnement, tels que Linux, Windows, ou fournisseurs de Amazon Web Services en nuage. Pour ce faire, téléchargez le fichier RDB à partir du serveur Redis dans un blob de page dans un compte de stockage Azure et puis l’importer dans votre instance de Cache Redis d’Azure premium. Par exemple, vous souhaitez exporter les données depuis le cache de votre production et l’importer dans un cache utilisé dans le cadre d’un environnement de test pour le test ou la migration. 

### <a name="will-my-cache-be-available-during-an-importexport-operation"></a>Mon cache seront disponible au cours d’une opération d’importation/exportation ?

-   **Exporter** - Caches restent disponibles et vous pouvez continuer à utiliser votre cache au cours d’une opération d’exportation.
-   **Importer** - Caches ne sont plus disponibles au démarrage d’une opération d’importation et sont disponibles à l’issue de l’opération d’importation.


### <a name="can-i-use-importexport-with-redis-cluster"></a>Puis-je utiliser importation/exportation avec Redis cluster ?

Oui, et vous pouvez importer/exporter entre un cache en cluster et un cache non mis en cluster. Depuis Redis cluster [prend uniquement en charge la base de données de 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), toutes les données de bases de données autres que 0 ne seront pas importées. Lors de l’importation des données du cache en cluster, les clés sont redistribués entre les milieu des fragments du cluster. 

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Comment importer/exporter ne fonctionne pas avec un paramètre personnalisé de la bases de données ?

Certains niveaux de tarification ont différentes [limites de bases de données](cache-configure.md#databases), il existe certaines considérations lors de l’importation si vous avez configuré une valeur personnalisée pour le `databases` définition lors de la création du cache.

-   Lors de l’importation à un niveau de tarification à un bas `databases` limite de la couche à partir duquel vous avez exporté :
    -   Si vous utilisez le nombre par défaut de `databases` qui n’est 16 pour tous les niveaux de tarification, aucune donnée n’est perdue.
    -   Si vous utilisez un nombre personnalisé de `databases` qui se situe dans les limites de la couche à laquelle vous importez, aucune donnée n’est perdue.
    -   Si les données exportées contiennent des données dans une base de données qui dépassent les limites de la nouvelle couche, les données de ces bases de données plus élevés ne sont pas importées.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Comment est importation/exportation différente de persistance de Redis ?

Persistance de Redis de Cache Azure vous permet rendre persistantes les données stockées dans Redis pour le stockage Azure. Lors de la persistance est configuré, le Cache de Redis Azure persiste un instantané du cache Redis dans un format binaire de Redis sur le disque selon une fréquence de sauvegarde configurable. Si un événement catastrophique se produit qui désactive le cache principal et le réplica, les données du cache sont restaurées automatiquement à l’aide de l’instantané le plus récent. Pour plus d’informations, consultez [comment configurer la persistance des données pour un Cache de Redis prime Azure](cache-how-to-premium-persistence.md).

Importation / exportation vous permet d’importer des données dans ou d’exporter à partir du Cache de Redis d’Azure. Il ne pas configurer la sauvegarde et restauration à l’aide de la persistance de Redis.


### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Puis-je automatiser importation/exportation à l’aide de PowerShell, CLI ou autres clients de gestion ?

Oui, pour PowerShell instructions voir [Importer un cache de Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) et [Exporter un cache Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).



### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>J’ai reçu une erreur de délai d’attente pendant mon opération d’importation/exportation. Ce que cela signifie ?

Si vous restez sur la blade **d’Importer des données** ou **d’exporter des données** pendant plus de 15 minutes avant de lancer l’opération, vous recevrez une erreur semblable à la suivante.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Pour résoudre ce problème, démarrer l’importation ou l’exportation avant 15 minutes s’est écoulé.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>J’ai reçu une erreur lors de l’exportation de mes données pour le stockage des objets Blob Azure. Que s'est-il passé ?

Importation/exportation ne fonctionne qu’avec les fichiers RDB stockés en tant que BLOB de la page. Autres types d’objet blob ne sont pas pris en charge pour l’instant, y compris les comptes de stockage blob avec couches à chaud et refroidir.


## <a name="next-steps"></a>Étapes suivantes
Apprenez à utiliser les fonctionnalités de cache plus premium.

-   [Introduction à la couche de prime de Cache Redis Azure](cache-premium-tier-intro.md)    

  
<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png








