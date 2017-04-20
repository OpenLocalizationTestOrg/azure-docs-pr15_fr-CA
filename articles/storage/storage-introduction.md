<properties
    pageTitle="Introduction au stockage | Microsoft Azure"
    description="Vue d’ensemble du stockage Azure, stockage de données en ligne de Microsoft dans le nuage. Apprenez à utiliser la meilleure solution de stockage cloud disponibles dans vos applications."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="introduction-to-microsoft-azure-storage"></a>Introduction au stockage de Microsoft Azure

## <a name="overview"></a>Vue d’ensemble

Le stockage Azure est la solution de stockage cloud pour les applications modernes qui s’appuient sur la durabilité, de disponibilité et d’évolutivité pour répondre aux besoins de leurs clients. En lisant cet article, les développeurs, professionnels de l’informatique et décision décideurs peuvent obtenir des informations sur :

- Quel est le stockage Azure, et comment vous pouvez tirer parti de votre nuage, mobile, serveur et applications de bureau
- Les types de données que vous pouvez stocker avec les services de stockage Azure : blob de données (objet), les données de table de NoSQL, les messages de la file d’attente et les partages de fichiers.
- La gestion de l’accès à vos données dans le stockage Azure
- Comment vos données de stockage Azure sont effectuées durables via la redondance et de réplication
- Que faire ensuite pour créer votre première application Azure Storage

Pour obtenir rapidement opérationnel avec le stockage Azure, consultez [mise en route de stockage Azure dans cinq minutes](storage-getting-started-guide.md).

Pour plus d’informations sur les outils, les bibliothèques et autres ressources pour l’utilisation de stockage Azure, voir les [Étapes](#next-steps) ci-dessous.

## <a name="what-is-azure-storage"></a>Quel est le stockage Azure ?

Le cloud computing permet de nouveaux scénarios pour les applications nécessitant un stockage évolutif, hautement disponible et durable de leurs données – ce qui est exactement pourquoi Microsoft a développé le stockage Azure. Outre les permettant aux développeurs de créer des applications à grande échelle pour prendre en charge de nouveaux scénarios, le stockage Azure fournit la base du stockage Machines virtuelles pour Azure, une preuve supplémentaire sa robustesse.

Stockage Azure étant hautement évolutive, vous pouvez stocker et processus des centaines de téraoctets de données pour prendre en charge les scénarios de données volumineuses requises par l’analyse scientifique, financière et applications multimédia. Ou bien, vous pouvez stocker les petites quantités de données requises pour un site Web de petite entreprise. Chaque fois que vos besoins sont, vous payez uniquement les données que vous stockez. Stockage Azure actuellement stocke des dizaines de codage des objets de client unique et gère des millions de requêtes par seconde en moyenne.

Stockage Azure étant élastique, vous pouvez concevoir des applications pour un public global volumineux et adapter ces applications - la quantité de données stockées et le nombre de requêtes effectuées par rapport à elle. Vous ne payez que pour ce que vous utilisez, et uniquement lorsque vous l’utilisez.

Stockage Azure utilise un système de partitionnement automatique qui répartit automatiquement la charge vos données basées sur le trafic. Cela signifie que, comme les besoins en développement de votre application, le stockage Azure alloue automatiquement les ressources appropriées pour y répondre.

Stockage Azure est accessible à partir de n’importe où dans le monde entier, à partir de n’importe quel type d’application, s’il s’exécute dans le nuage, sur le bureau, sur un serveur local ou sur un appareil mobile ou périphérique tablette. Vous pouvez utiliser le stockage Azure dans les scénarios où l’application stocke un sous-ensemble de données sur le périphérique et le synchronise avec un ensemble complet de données stockées dans le nuage.

Stockage Azure prend en charge les clients à l’aide d’un ensemble varié de systèmes d’exploitation (y compris Windows et Linux) et d’une variété de langages de programmation (y compris .NET, Java, Node.js, Python, Ruby, PHP et C++ et les langages de programmation mobiles) pour le développement de la pratique. Stockage Azure expose également les ressources de données via des API reste simple, qui sont disponibles à tout client capable d’envoyer et de recevoir des données via HTTP/HTTPS.

Stockage de prime Azure offre une prise en charge de disque hautes performances et à faible latence pour les charges de travail intensives d’e/s en cours d’exécution sur Azure Virtual Machines. Avec le stockage Azure Premium, vous pouvez attacher plusieurs disques de données persistantes sur une machine virtuelle et les configurer pour répondre à vos besoins en matière de performances. Chaque disque de données est sauvegardée par un disque SSD dans le stockage Azure Premium pour optimiser les performances d’e/s. Consultez [prime stockage : stockage hautes performances pour les charges de travail de Machine virtuelle Azure](storage-premium-storage.md) pour plus de détails.

## <a name="introducing-the-azure-storage-services"></a>Présentation des Services de stockage Azure

Le stockage Azure fournit les quatre services suivants : Blob de stockage, stockage de Table, stockage de la file d’attente et le stockage de fichiers.

- Stockage des objets BLOB stocke les données de l’objet non structurées. Un blob peut être n’importe quel type de données texte ou binaires, comme un document, un fichier multimédia ou un programme d’installation de l’application. Stockage des objets BLOB est également appelée stockage d’objets.
- Stockage de table stocke les jeux de données structurées. Stockage de table est un magasin de données de l’attribut clé NoSQL qui permet un développement rapide et un accès rapide à grandes quantités de données.
- Stockage de la file d’attente fournit une messagerie fiable pour le traitement de flux de travail et pour la communication entre les composants des services en nuage.
- Stockage de fichiers offre un stockage partagé utilisant le protocole SMB standard des applications héritées. Des machines virtuelles Azure et les services en nuage peuvent partager des données entre les composants d’application via les partages montés, et les applications sur site peuvent accéder à des données de fichier dans un partage via le service de fichier API REST.

Un compte de stockage Azure est un compte sécurisé qui vous donne accès aux services dans le stockage Azure. Votre compte de stockage fournit l’espace de noms unique pour vos ressources de stockage. L’image ci-dessous montre les relations entre les ressources de stockage Azure dans un compte de stockage :

![Ressources de stockage Azure](./media/storage-introduction/storage-concepts.png)

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[AZURE.INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Stockage des objets BLOB

Pour les utilisateurs avec de grandes quantités de données non structurées de l’objet à stocker dans le nuage, stockage Blob offre une solution économique et évolutive. Vous pouvez utiliser le stockage Blob pour stocker du contenu tel que :

- Documents
- Données sociales telles que des blogs, des vidéos, musique et photos
- Sauvegardes de fichiers, des ordinateurs, des bases de données et des périphériques
- Images et du texte pour les applications web
- Données de configuration des applications en nuage
- Données volumineuses, telles que les journaux et autres grands ensembles de données

Chaque objet blob est organisé dans un conteneur. Les conteneurs fournissent également un moyen pratique d’affecter des stratégies de sécurité à des groupes d’objets. Un compte de stockage peut contenir un nombre quelconque de conteneurs et un conteneur peut contenir un nombre quelconque d’objets BLOB, dans la limite de capacité de 500 To du compte de stockage.  

BLOB de stockage offre trois types d’objets BLOB, bloquer les objets BLOB, ajouter des objets BLOB et les objets BLOB (disques) de la page.

- BLOB de bloc est optimisées en continu et le stockage d’objets de nuage et constitue un bon choix pour le stockage des documents, des fichiers multimédias, des sauvegardes, etc..
- Ajouter des objets BLOB sont semblables aux objets BLOB de bloc, mais sont optimisés pour les opérations d’ajout. Un blob append peut être mis à jour uniquement par l’ajout d’un nouveau bloc à la fin. Ajouter des objets BLOB sont un bon choix pour les scénarios tels que la journalisation, où les nouvelles données doivent être écrites uniquement à la fin de l’objet blob.
- BLOB de page est optimisés pour les représentant les disques IaaS et aléatoire écrit et peut être jusqu'à 1 To de taille. Un réseau des machines virtuelles Azure joint IaaS disque est un disque dur virtuel stocké sous la forme d’un blob de la page.

Pour les très grands jeux de données où les contraintes de réseau que transfert ou le téléchargement de données pour le stockage des objets Blob sur le réseau irréaliste, vous pouvez expédier un disque dur à Microsoft pour importer ou exporter des données directement à partir du centre de données. Reportez-vous à la section [utiliser le Service d’importation/exportation de Microsoft Azure pour transférer les données pour le stockage Blob](storage-import-export-service.md).

## <a name="table-storage"></a>Stockage de table

Les applications modernes exigent souvent des banques de données avec une plus grande évolutivité et de flexibilité que les générations précédentes de logiciels requis. Stockage de table offre un stockage hautement disponible et très évolutif, afin que votre application peut automatiquement mettre à l’échelle pour répondre à la demande de l’utilisateur. Stockage de table magasin de clé/attribut NoSQL de Microsoft, il a une conception schemaless, ce qui diffère des bases de données relationnelles traditionnelles. Avec un magasin de données schemaless, il est facile d’adapter vos données selon les besoins du evolve de votre application. Stockage de table est facile à utiliser, afin que les développeurs peuvent créer rapidement des applications. Accès aux données est rapide et économique pour toutes sortes d’applications.  Stockage de table est en général sensiblement inférieur de coût SQL traditionnel pour les volumes de données similaires.

Stockage de table est un magasin de la clé-attribut, c'est-à-dire que chaque valeur dans une table est stockée sous un nom de propriété typée. Le nom de propriété peut être utilisé pour filtrer et définir des critères de sélection. Une collection de propriétés et leurs valeurs comprennent une entité. Stockage de Table étant schemaless, deux entités dans la même table peuvent contenir différents ensembles de propriétés, et ces propriétés peuvent être de différents types.

Vous pouvez utiliser le stockage de Table pour stocker des groupes de données flexible, comme les données utilisateur pour les applications web, des carnets d’adresses, informations sur les périphériques et tout autre type de métadonnées nécessaires à votre service.  Vous pouvez stocker n’importe quel nombre d’entités dans une table, et un compte de stockage peut contenir un nombre quelconque de tables, dans la limite de la capacité du compte de stockage.

Comme les BLOB et les files d’attente, les développeurs peuvent gérer et accéder à Table protocoles de stockage à l’aide de reste standard, mais le stockage de Table prend également en charge un sous-ensemble du protocole OData, simplifiant l’activation JSON et AtomPub (XML basé) et capacités de requêtes avancées formats.

Pour les applications basées sur Internet aujourd'hui, bases de données NoSQL comme stockage de Table offrent une alternative populaire pour les bases de données relationnelles traditionnelles.

## <a name="queue-storage"></a>Stockage de la file d’attente

Dans la conception d’applications à l’échelle, les composants d’application sont souvent découplées, afin qu’ils peuvent faire évoluer indépendamment. Stockage de la file d’attente fournit une solution de messagerie fiable pour la communication asynchrone entre les composants de l’application, si elles sont en cours d’exécution dans le nuage, sur le bureau, sur un serveur local ou sur un périphérique mobile. Stockage de la file d’attente prend également en charge la gestion des tâches asynchrones et la création de workflows de processus.

Un compte de stockage peut contenir n’importe quel nombre de files d’attente. Une file d’attente peut contenir un nombre illimité de messages, dans la limite de la capacité du compte de stockage. Des messages peuvent être jusqu'à 64 Ko.

## <a name="file-storage"></a>Stockage de fichiers

Stockage Azure offre des partages de fichiers SMB en nuage, afin que vous pouvez migrer des applications existantes qui reposent sur des partages de fichiers sur Azure rapidement et sans réécritures coûteux. Avec le stockage Azure, applications s’exécutant dans des machines virtuelles Azure ou de services en nuage peuvent monter un partage de fichiers dans le nuage, tout comme une application de bureau monte un partage SMB par défaut. N’importe quel nombre de composants de l’application peut ensuite monter et accéder au partage de stockage de fichiers simultanément.

Dans la mesure où un partage de stockage de fichiers est un partage de fichiers SMB standard, applications qui s’exécutent dans Azure peuvent accéder les données dans le partage via le système de fichiers I/O APIs. Les développeurs peuvent donc exploiter le code existant et leurs à migrer les applications existantes. Professionnels de l’informatique peuvent utiliser les applets de commande PowerShell pour créer, monter et gérer les partages de stockage de fichiers dans le cadre de l’administration des applications Azure.

Comme les autres services de stockage Azure, le stockage de fichiers expose une API REST pour accéder aux données dans un partage. Les applications sur site peuvent appeler l’API REST pour accéder aux données dans un partage de fichiers de stockage de fichiers. De cette façon, une entreprise peut choisir faire migrer certaines applications héritées vers Azure et poursuivre l’exécution que d’autres au sein de leur propre organisation. Notez que le montage d’un partage de fichiers est uniquement possible pour les applications en cours d’exécution dans Azure ; le partage de fichiers via l’API REST peut uniquement accéder à une application sur site.

Applications distribuées permet également le stockage de fichiers pour stocker et partager des outils de test et de développement et de données d’une application utile. Par exemple, une application peut stocker des fichiers de configuration et exporte les données de diagnostic, tels que des journaux, des statistiques et des incidents dans un fichier de stockage partage afin qu’ils soient disponibles pour plusieurs machines virtuelles ou des rôles. Les développeurs et les administrateurs peuvent stocker des utilitaires dont ils ont besoin pour créer ou gérer une application dans un partage de stockage de fichiers qui est disponible pour tous les composants, au lieu de les installer sur chaque ordinateur virtuel ou d’une instance de rôle.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Accès à l’objet Blob, Table, file d’attente et les ressources de fichier

Par défaut, seul le propriétaire du compte de stockage peut accéder aux ressources du compte de stockage. Pour la sécurité de vos données, chaque demande faite par rapport aux ressources de votre compte doit être authentifié. L’authentification repose sur un modèle de clé partagée. BLOB peut également être configuré pour prendre en charge de l’authentification anonyme.

Votre compte de stockage est affecté à deux clés d’accès privé lors de la création sont utilisés pour l’authentification. Deux clés permet de garantir que votre application reste disponible lorsque vous régénérez régulièrement les clés sous la forme d’un courant de gestion des clés de sécurité.

Si vous n’avez pas besoin pour permettre aux utilisateurs un accès commandé à vos ressources de stockage, puis vous pouvez créer une signature d’accès partagé. Une signature d’accès partagé (SAS) est un jeton qui peut être ajouté à une URL qui permet l’accès délégué à une ressource de stockage. Toute personne qui possède le jeton peut accéder à la ressource, qu'il pointe sur les autorisations qu’il indique, pour la période qu’il est valide. Depuis la version 2015-04-05, le stockage Azure prend en charge deux types de signatures d’accès partagé : service SAS et associations de sécurité de compte.

Le service SAS délègue l’accès à une ressource dans un seul des services de stockage : le service d’objet Blob, file d’attente, un tableau ou fichier.

Un compte SAS délègue l’accès aux ressources dans un ou plusieurs des services de stockage. Vous pouvez déléguer l’accès aux opérations de niveau de service qui ne sont pas disponibles avec un service SAS. Vous pouvez également déléguer l’accès à lire, écrire et supprimer des opérations sur les conteneurs blob, les tables, les files d’attente et les partages de fichiers qui ne sont pas autorisés avec un service SAS.

Enfin, vous pouvez spécifier qu’un conteneur et ses objets BLOB ou un blob spécifique, sont disponibles pour l’accès public. Lorsque vous indiquez qu’un conteneur ou un objet blob est public, tout le monde peut le lire de manière anonyme ; Aucune authentification n’est requise.  Conteneurs publics et les objets BLOB est utiles pour l’exposition des ressources telles que les supports et les documents qui sont hébergés sur des sites Web.  Pour réduire la latence du réseau pour un public international, vous pouvez mettre en cache les données blob utilisées par des sites Web avec le Azure CDN.

Pour plus d’informations sur les signatures d’accès partagé, consultez [L’aide partagés accès Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) . Pour plus d’informations sur l’accès sécurisé à votre compte de stockage, reportez-vous à la section [accès anonyme en lecture de gestion pour les conteneurs et les objets BLOB](storage-manage-access-to-resources.md) et [l’authentification pour les Services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx) .

## <a name="replication-for-durability-and-high-availability"></a>Réplication de durabilité et de haute disponibilité

Les données de votre compte de stockage Microsoft Azure sont toujours répliquées pour garantir la haute disponibilité et durabilité. La réplication copie vos données, au sein du même centre de données, soit à un deuxième centre de données, selon l’option de réplication que vous choisissez. La réplication protège vos données et préserve votre application le temps de fonctionnement en cas de défaillance du matériel en régime transitoire. Si vos données sont répliquées vers un deuxième centre de données, également pour la protection de vos données contre une défaillance irrémédiable dans l’emplacement principal.

La réplication garantit que votre compte de stockage remplit le [Contrat de niveau de Service (SLA) pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/) de même face à des échecs. Consultez que le contrat SLA pour plus d’informations sur le stockage Azure garanties de durabilité et de disponibilité. 

Lorsque vous créez un compte de stockage, vous pouvez sélectionner une des options de réplication suivantes :  

- **Stockage redondant localement (LRS).** Stockage redondant localement gère trois copies de vos données. LRS sont répliquées trois fois au sein d’un centre de données unique dans une seule région. LRS protège vos données contre les pannes de matériel normal, mais pas à partir de l’échec d’un centre de données unique.  

    LRS est offert avec une remise. Pour une durabilité maximale, nous vous conseillons de stockage géo-redondants, décrit ci-dessous.


- **Stockage redondant de zone (ZRS).** Le stockage redondant zone gère trois copies de vos données. ZRS n’est répliqué trois fois sur deux à trois installations au sein d’une même région ou entre les deux régions, offrant une durabilité élevée que LRS. ZRS garantit que vos données sont durables au sein d’une seule région.  

    ZRS fournit un niveau de durabilité supérieur LRS ; Toutefois, pour une durabilité maximale, nous recommandons que vous utilisez le stockage redondant géo, décrit ci-dessous.  

    > [AZURE.NOTE] ZRS n’est actuellement disponible uniquement pour les objets BLOB de bloc et est uniquement pris en charge pour les versions 2014-02-14 et ultérieures.
    >
    > Une fois que vous avez créé votre compte de stockage et sélectionné des ZRS, vous ne peut pas le convertir à utiliser pour tout autre type de réplication, ou vice versa.

- **Stockage redondant Geo (GRS)**. GRS conserve de six copies de vos données. Avec GRS, vos données sont répliquées de trois fois dans la région principale et sont également répliquées trois fois dans une zone secondaire des centaines de miles en dehors de la région principale, offrant le plus haut niveau de durabilité. En cas de défaillance au niveau de la zone principale, le stockage Azure sont basculées vers la zone secondaire. GRS garantit que vos données sont durables dans deux zones distinctes.

    Pour plus d’informations sur les jumelages principales et secondaires par région, reportez-vous à la section [Régions d’Azure](https://azure.microsoft.com/regions/).

- **Accès en lecture de stockage redondant geo (RA-GRS)**. Accès en lecture le stockage redondant geo réplique vos données à un emplacement géographique secondaire et fournit également un accès en lecture à vos données dans l’emplacement secondaire. Stockage redondant geo d’accès en lecture vous permet de d’accéder à vos données depuis le site secondaire, ou principal dans le cas où un emplacement n’est pas disponible. Le stockage redondant géo-accès en lecture est l’option par défaut pour votre compte de stockage par défaut lors de sa création. 

    > [AZURE.IMPORTANT] Vous pouvez modifier comment vos données sont répliquées après que votre compte de stockage a été créé, sauf si vous spécifiez ZRS lorsque vous avez créé le compte. Toutefois, notez que vous risquez de subir un transfert de données unique supplémentaires coût si vous passez du LRS au GRS ou RA-GRS.

Pour plus d’informations sur les options de réplication de stockage, voir [réplication de stockage Azure](storage-redundancy.md) .

Informations de tarification pour la réplication de compte de stockage, reportez-vous à la section [Tarification de stockage Azure](https://azure.microsoft.com/pricing/details/storage/). Pour plus d’informations sur les services sont disponibles dans chaque région, reportez-vous à la section [Régions d’Azure](https://azure.microsoft.com/regions/#services) .

Pour plus d’informations sur la durabilité avec stockage Azure architecturales, consultez [SOSP papier - stockage Azure : A hautement disponible Cloud Service de stockage grâce à la cohérence fort](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).


## <a name="transferring-data-to-and-from-azure-storage"></a>Transfert des données vers et à partir du stockage Azure

Vous pouvez utiliser l’utilitaire de ligne de commande AzCopy pour copier les blob, les fichiers et les données de la table dans votre compte de stockage ou entre des comptes de stockage. Pour plus d’informations, consultez [transférer des données avec l’utilitaire de ligne de commande de AzCopy](storage-use-azcopy.md) .

AzCopy est basé sur la [Bibliothèque de mouvement de données Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), ce qui est actuellement disponible dans l’aperçu.

Le service d’importation/exportation d’Azure permet d’importer des données blob ou exporter des données blob à partir de votre compte de stockage via un disque dur envoyé au centre de données Azure. Pour plus d’informations sur le service d’importation/exportation, voir [utilisation de Service Microsoft Azure importation/exportation pour transférer les données pour le stockage des objets Blob](storage-import-export-service.md).

## <a name="pricing"></a>Tarification

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>Outils, les bibliothèques et les API de stockage

Les ressources de stockage Azure sont accessibles par n’importe quel langage qui peut effectuer des demandes HTTP/HTTPS. En outre, le stockage Azure propose des bibliothèques de programmation pour plusieurs langues courantes. Ces bibliothèques de simplifient de nombreux aspects de l’utilisation de stockage Azure par gestion des détails tels que les appels synchrones et asynchrones, le traitement par lots des opérations, gestion des exceptions, les tentatives automatiques, comportement opérationnel et ainsi de suite. Les bibliothèques sont actuellement disponibles pour les langages et plates-formes, avec d’autres personnes dans le pipeline suivants :

### <a name="azure-storage-data-services"></a>Services de données de stockage Azure

- [Services de stockage API REST](http://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Bibliothèque Client de stockage pour .NET, Windows Phone et Windows Runtime](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Bibliothèque Client de stockage pour C++](https://github.com/Azure/azure-storage-cpp)
- [Bibliothèque Client de stockage pour Java/Android](/develop/java/)
- [Bibliothèque Client de stockage pour Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
- [Bibliothèque Client de stockage pour PHP](/develop/php/)
- [Librairie à stockage Client Ruby](/develop/ruby/)
- [Bibliothèque Client de stockage pour les Python](/develop/python/)
- [Applets de commande de stockage pour PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Services de gestion du stockage Azure

- [Référence de l’API de stockage ressource fournisseur reste](https://msdn.microsoft.com/library/azure/mt163683.aspx)
- [Bibliothèque Client fournisseur de ressources de stockage pour .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)
- [Cmdlets de fournisseur de ressources de stockage pour PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt607151.aspx)
- [Stockage Service gestion reste API (classique)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Services de déplacement de données de stockage Azure

- [API de stockage importation/exportation Service REST](https://msdn.microsoft.com/library/azure/dn529096.aspx)
- [Bibliothèque de Client de mouvement de données de stockage pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Outils et utilitaires

- [Explorateur de stockage Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
- [Outils clients de stockage Azure](storage-explorers.md)
- [Outils et azure SDK](https://azure.microsoft.com/tools/)
- [Émulateur de stockage Azure](http://www.microsoft.com/download/details.aspx?id=43709)
- [PowerShell Azure](../powershell-install-configure.md)
- [Utilitaire de ligne de commande AzCopy](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le stockage Azure, Explorez ces ressources :

### <a name="documentation"></a>Documentation

- [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)

### <a name="for-administrators"></a>Pour les administrateurs

- [À l’aide de PowerShell Azure avec stockage Azure](storage-powershell-guide-full.md)
- [À l’aide de la CLI Azure avec stockage Azure](storage-azure-cli.md)

### <a name="for-net-developers"></a>Pour les développeurs .NET

- [Mise en route de stockage Azure Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md)
- [Mise en route de stockage Azure Table à l’aide de .NET](storage-dotnet-how-to-use-tables.md)
- [Mise en route de stockage Azure file à l’aide de .NET](storage-dotnet-how-to-use-queues.md)
- [Mise en route de stockage Azure sous Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Pour les développeurs Java/Android

- [Comment faire pour utiliser le stockage Blob à partir de Java](storage-java-how-to-use-blob-storage.md)
- [Comment faire pour utiliser le stockage de Table à partir de Java](storage-java-how-to-use-table-storage.md)
- [Comment faire pour utiliser le stockage de file d’attente à partir de Java](storage-java-how-to-use-queue-storage.md)
- [Comment faire pour utiliser le stockage de fichiers à partir de Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Pour les développeurs de Node.js

- [Comment faire pour utiliser le stockage Blob de Node.js](storage-nodejs-how-to-use-blob-storage.md)
- [Comment faire pour utiliser le stockage de Table à partir de Node.js](storage-nodejs-how-to-use-table-storage.md)
- [Comment faire pour utiliser le stockage de file d’attente à partir de Node.js](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Pour les développeurs PHP

- [Comment faire pour utiliser le stockage Blob à partir de PHP](storage-php-how-to-use-blobs.md)
- [Comment faire pour utiliser le stockage de Table à partir de PHP](storage-php-how-to-use-table-storage.md)
- [Comment faire pour utiliser le stockage de file d’attente à partir de PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Pour les développeurs Ruby

- [Comment faire pour utiliser le stockage Blob de Ruby](storage-ruby-how-to-use-blob-storage.md)
- [Comment faire pour utiliser le stockage de Table à partir de Ruby](storage-ruby-how-to-use-table-storage.md)
- [Comment faire pour utiliser le stockage de file d’attente à partir de Ruby](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Pour les développeurs les Python

- [Comment faire pour utiliser le stockage Blob à partir de Python](storage-python-how-to-use-blob-storage.md)
- [Comment faire pour utiliser le stockage de Table à partir de Python](storage-python-how-to-use-table-storage.md)
- [Comment faire pour utiliser le stockage de file d’attente à partir de Python](storage-python-how-to-use-queue-storage.md)
- [Comment faire pour utiliser le stockage de fichiers à partir de Python](storage-python-how-to-use-file-storage.md)
