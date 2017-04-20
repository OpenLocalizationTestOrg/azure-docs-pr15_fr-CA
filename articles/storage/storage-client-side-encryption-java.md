<properties
    pageTitle="Cryptage de côté client avec Java pour le stockage de Microsoft Azure | Microsoft Azure"
    description="La bibliothèque de Client de stockage Azure pour Java prend en charge le cryptage côté client et l’intégration avec Azure clé de coffre-fort pour une sécurité maximale pour vos applications de stockage Azure."
    services="storage"
    documentationCenter="java"
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


# <a name="client-side-encryption-with-java-for-microsoft-azure-storage"></a>Cryptage de côté client avec Java pour le stockage de Microsoft Azure   

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Vue d’ensemble  

La [Bibliothèque de Client de stockage Azure pour Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) prend en charge le chiffrement des données dans les applications clientes avant le téléchargement vers le stockage Azure et le décryptage des données pendant leur téléchargement vers le client. La bibliothèque prend également en charge l’intégration avec [Azure clé coffre-fort](https://azure.microsoft.com/services/key-vault/) stockage compte gestion des clés.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Cryptage et décryptage via la technique de l’enveloppe    
Les processus de cryptage et de décryptage suivent la technique de l’enveloppe.  

### <a name="encryption-via-the-envelope-technique"></a>Cryptage via la technique de l’enveloppe  
Le cryptage via la technique de l’enveloppe fonctionne de la manière suivante :  

1.  La bibliothèque cliente de stockage Azure génère une clé de chiffrement de contenu (CEK), qui est une clé symétrique usage unique.  

2.  Les données utilisateur sont cryptées à l’aide de ce CEK.   

3.  Le CEK est ensuite encapsulé (crypté) à l’aide de la clé de cryptage de clés (KEK). Le KEK est identifiée par un identificateur de clé et peut être une paire de clés asymétrique ou une clé symétrique et peut être gérée localement ou stockée dans Azure clé coffres-forts.  
La bibliothèque cliente de stockage lui-même n’a jamais accès à KEK. La bibliothèque appelle l’algorithme de clé d’habillage qui est fourni par le stockage en chambre forte de clé. Les utilisateurs peuvent choisir d’utiliser des fournisseurs personnalisés pour conditionnement/désencapsuler une clé si vous le souhaitez.  

4.  Les données cryptées sont ensuite téléchargées sur le service de stockage Azure. La clé encapsulée avec des métadonnées de cryptage supplémentaire est stockée en tant que métadonnées (sur un objet blob) ou interpolée avec les données chiffrées (messages de la file d’attente et les entités de table).

### <a name="decryption-via-the-envelope-technique"></a>Décryptage via la technique de l’enveloppe  
Le décryptage via la technique de l’enveloppe fonctionne de la manière suivante :  

1.  La bibliothèque cliente suppose que l’utilisateur gère la clé de cryptage de clés (KEK) soit localement soit dans Azure clé coffres-forts. L’utilisateur n’a pas besoin de connaître la clé spécifique qui a été utilisée pour le chiffrement. Au lieu de cela, un résolveur de clés qui résout les différents identificateurs de clé aux clés peut être paramétré et utilisé.  

2.  La bibliothèque cliente télécharge les données chiffrées ainsi que de tout matériel de chiffrement est stockée sur le service.  

3.  La clé de chiffrement de contenu renvoyé à la ligne (CEK) est désencapsulée (décrypté) à l’aide du cryptage par clé clé (KEK). Ici encore, la bibliothèque cliente n’a pas accès à KEK. Il appelle simplement personnalisé ou algorithme de désencapsuler du fournisseur de stockage en chambre forte de clé.  

4.  La clé de chiffrement de contenu (CEK) est ensuite utilisée pour décrypter les données cryptées de l’utilisateur.

## <a name="encryption-mechanism"></a>Mécanisme de cryptage  
La bibliothèque cliente de stockage utilise [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) pour chiffrer les données de l’utilisateur. Plus précisément, [CBC (Cipher Block Chaining)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) en mode AES. Chaque service fonctionne un peu différemment, afin que nous allons étudier chacun d’eux ici.

### <a name="blobs"></a>Objets BLOB  
La bibliothèque cliente prend actuellement en charge le cryptage des blobs entiers uniquement. Plus précisément, le cryptage est pris en charge lorsque les utilisateurs utilisent le **téléchargement** * méthodes ou * *openOutputStream** méthode. Pour les téléchargements, à la fois complet et plage sont pris en charge.  

Pendant le chiffrement, la bibliothèque cliente génère un vecteur d’initialisation aléatoire (IV) de 16 octets, avec une clé de chiffrement aléatoire de contenu (CEK) de 32 octets et effectuer un chiffrement d’enveloppe des données blob à l’aide de ces informations. Le CEK encapsulé et des métadonnées de chiffrement supplémentaires sont alors enregistrés comme blob des métadonnées avec le blob crypté sur le service.

>[AZURE.WARNING] Si vous modifiez ou télécharger vos propres métadonnées pour l’objet blob, vous devez vous assurer que ces métadonnées sont conservées. Si vous téléchargez de nouvelles métadonnées sans ces métadonnées, le CEK encapsulé, IV et autres métadonnées seront perdues et le contenu de l’objet blob ne sera jamais récupérable à nouveau.

Téléchargement d’un objet blob chiffré, il faut récupérer le contenu entier du blob de l’à l’aide de la */openInputStream de *téléchargement*** méthodes pratiques. Le CEK encapsulé est dévoilé et permet de retourner les données décryptées aux utilisateurs ainsi que le vecteur d’initialisation (stocké en tant que blob métadonnées dans ce cas).

Télécharger une plage arbitraire (**downloadRange*** méthodes) dans le blob crypté implique l’ajustement de la plage fournie par les utilisateurs pour obtenir une petite quantité de données supplémentaires qui peuvent être utilisées pour décrypter la plage demandée.  

Tous les types de tache (bloquer des objets BLOB, BLOB de la page et ajouter des objets BLOB) peuvent être cryptées/décryptées à l’aide de ce schéma.

### <a name="queues"></a>Files d’attente  
Dans la mesure où les messages de la file d’attente peuvent être de n’importe quel format, la bibliothèque cliente définit un format personnalisé qui contient le vecteur d’initialisation (IV) et la clé de chiffrement de contenu crypté (CEK) dans le texte du message.  

Pendant le chiffrement, la bibliothèque cliente génère une IV aléatoire de 16 octets et un CEK aléatoire de 32 octets et effectue le chiffrement d’enveloppe du texte du message de file d’attente à l’aide de ces informations. Le CEK encapsulé et des métadonnées de chiffrement supplémentaires sont ensuite ajoutés à du message chiffré de file d’attente. Ce message modifié (voir ci-dessous) est stocké sur le service.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Lors du décryptage, la clé encapsulée est extraite à partir du message de la file d’attente et dévoilée. Le vecteur d’initialisation est également extrait du message de la file d’attente et utilisé ainsi que la clé désencapsulée pour décrypter les données de message file d’attente. Notez que les métadonnées de cryptage sont petite (sous 500 octets), alors que cela compte pour la limite de 64 Ko pour un message de la file d’attente, l’impact doit être faciles à gérer.

### <a name="tables"></a>Tables  
La bibliothèque cliente prend en charge le cryptage des propriétés de l’entité pour l’insertion d’opérations et de remplacement.

>[AZURE.NOTE] Fusion n’est pas actuellement pris en charge. Dans la mesure où un sous-ensemble de propriétés est peut-être encryptée précédemment à l’aide d’une clé différente, simplement fusionner les nouvelles propriétés et les métadonnées de mise à jour entraîne des pertes de données. La fusion soit nécessite de passer des appels de service supplémentaire à lire de l’entité existante dans le service, ou à l’aide d’une nouvelle clé par propriété, qui ne conviennent pas pour des raisons de performances.

Cryptage des données de table fonctionne comme suit :  

1.  Les utilisateurs spécifient les propriétés afin d’être crypté.  

2.  La bibliothèque cliente génère un vecteur d’initialisation aléatoire (IV) de 16 octets avec une clé de chiffrement aléatoire de contenu (CEK) de 32 octets pour chaque entité et effectue le chiffrement d’enveloppe sur les propriétés individuelles afin d’être crypté en dérivant un nouveau vecteur d’initialisation par propriété. La propriété cryptée est stockée sous forme de données binaires.  

3.  Le CEK encapsulé et des métadonnées de chiffrement supplémentaires sont alors enregistrés comme deux propriétés réservées supplémentaires. La première propriété réservée (_ClientEncryptionMetadata1) est une propriété de chaîne qui conserve des informations sur IV, version et clé encapsulée. La propriété deuxième réservée (_ClientEncryptionMetadata2) est une propriété binaire qui conserve des informations sur les propriétés qui sont cryptés. Les informations contenues dans cette seconde propriété (_ClientEncryptionMetadata2) sont elle-même cryptée.  

4.  En raison de ces propriétés réservées supplémentaires requises pour le cryptage, les utilisateurs peuvent désormais avoir que 250 propriétés personnalisées au lieu de 252. La taille totale de l’entité doit être inférieure à 1 Mo.  

    Notez que seules les propriétés de chaîne peuvent être cryptées. Si d’autres types de propriétés doivent être cryptées, ils doivent être convertis en chaînes. Les chaînes cryptées sont stockées sur le service en tant que propriétés binaires, et ils sont convertis en chaînes après le décryptage.

    Pour les tables, en plus de la stratégie de cryptage, les utilisateurs doivent spécifier les cryptage des propriétés. Pour ce faire, vous pouvez spécifier un attribut [crypter] (pour les entités POCO dérivées de la TableEntity) ou un programme de résolution de cryptage dans les options de requête. Un résolveur de cryptage est un délégué qui prend un clé de partition, la clé de ligne et le nom de la propriété et retourne une valeur booléenne qui indique si cette propriété doit être chiffrée. Pendant le chiffrement, la bibliothèque cliente utilise ces informations pour décider si une propriété doit être chiffrée pendant l’écriture sur le réseau. Le délégué offre également la possibilité de logique autour de la façon dont les propriétés sont cryptées. (Par exemple, si X, puis crypter de propriété A ; de crypter les propriétés A et B.) Notez qu’il n’est pas nécessaire de fournir cette information lors de la lecture ou de l’interrogation des entités.

### <a name="batch-operations"></a>Opérations de traitement par lots  
Dans les opérations de traitement par lots, le même KEK sera utilisé sur toutes les lignes de cette opération de traitement par lots car la bibliothèque client permet uniquement à un objet d’options (et par conséquent une stratégie/KEK) par une opération de traitement par lots. Toutefois, la bibliothèque cliente en interne génère un nouveau vecteur d’initialisation aléatoire et le CEK aléatoire par ligne dans le lot. Les utilisateurs peuvent également choisir de crypter des propriétés différentes pour chaque opération dans le lot en définissant ce comportement dans le programme de résolution de cryptage.

### <a name="queries"></a>Requêtes  
Pour effectuer des opérations de requête, vous devez spécifier un résolveur de clé qui est en mesure de résoudre toutes les clés dans le jeu de résultats. Si une entité contenue dans les résultats de la requête ne peut pas être résolue à un fournisseur, la bibliothèque cliente lèvera une erreur. Pour une requête qui effectue des projections de côté serveur, la bibliothèque client ajoutera les propriétés de métadonnées de cryptage spéciaux (_ClientEncryptionMetadata1 et _ClientEncryptionMetadata2) par défaut dans les colonnes sélectionnées.

## <a name="azure-key-vault"></a>Chambre forte de clé Azure  
Coffre-fort de clé Azure permet de sauvegarde des clés cryptographiques et les secrets utilisés par les services et les applications en nuage. À l’aide d’Azure clé Vault, les utilisateurs peuvent crypter les clés et données confidentielles (telles que l’authentification par clés, stockage compte, clés de cryptage de données. Les fichiers PFX et mots de passe) à l’aide des clés qui sont protégés par les modules de sécurité matérielle (HSM). Pour plus d’informations, consultez [Quel est le coffre-fort de clé Azure ?](../key-vault/key-vault-whatis.md).

La bibliothèque cliente de stockage utilise la bibliothèque principale de coffre-fort de clé afin de fournir un cadre commun sur Azure pour la gestion des clés. Les utilisateurs obtiennent également l’avantage de l’utilisation de la bibliothèque d’extensions de stockage en chambre forte de clé. La bibliothèque d’extensions fournit des fonctionnalités utiles, simple et transparente symétrique/RSA local et fournisseurs clés du cloud, ainsi qu’avec l’agrégation et la mise en cache.

### <a name="interface-and-dependencies"></a>Interface et dépendances  
Il existe trois packages en chambre forte de la clé :  

- Azure-keyvault-core contient le IKey et IKeyResolver. Il s’agit d’un package de petite taille sans dépendances. La bibliothèque de client de stockage pour Java la définit en tant que dépendance.
- Azure-keyvault contient le client reste de chambre forte de clé.  
- Azure-keyvault-extensions contient le code d’extension qui inclut des implémentations des algorithmes de chiffrement et un RSAKey et un SymmetricKey. Il dépend des espaces de noms principaux et KeyVault et fournit les fonctionnalités pour définir un résolveur d’agrégation (lorsque les utilisateurs souhaitent utiliser plusieurs fournisseurs de clé) et une résolution clée du cache. Bien que la bibliothèque cliente de stockage ne dépend pas directement sur ce package, si les utilisateurs souhaitent utiliser Azure clé de coffre-fort pour stocker leurs clés ou d’utiliser les extensions de stockage en chambre forte de clé pour consommer local et le nuage des fournisseurs de services cryptographiques, ils devront ce package.  

  Clé de stockage en chambre forte est conçu pour les clés principales de haute valeur et limites limitation par clé de coffre-fort sont conçus avec cela à l’esprit. Lors de l’exécution côté client de cryptage avec clé de stockage en chambre forte, le modèle par défaut est d’utiliser symétrique de clés principales stockées en tant que secrets dans le coffre-fort de la clé et la mise en cache localement. Les utilisateurs doivent effectuer les opérations suivantes :  

1.  Créer un secret en mode hors connexion et téléchargez-le dans le coffre-fort de la clé.  

2.  Utilisez identificateur de base du secret en tant que paramètre à résoudre la version actuelle de la clé secrète pour le cryptage et la mettre en cache ces informations localement. Utilisez CachingKeyResolver pour la mise en cache ; les utilisateurs ne devront pas mettre en œuvre leurs propre logique de mise en cache.  

3.  Utiliser le résolveur de mise en cache en tant qu’entrée lors de la création de la stratégie de chiffrement.
Vous trouverez plus d’informations sur l’utilisation du coffre-fort de clé dans les exemples de code de cryptage. <fix URL>  

## <a name="best-practices"></a>Meilleures pratiques  
Prise en charge du cryptage n’est disponible que dans la bibliothèque de client de stockage pour Java.

>[AZURE.IMPORTANT] Gardez à l’esprit ces points importants lors de l’utilisation du cryptage côté client :
>  
>- Lors de la lecture ou l’écriture d’un objet blob chiffré, utilisez les commandes de téléchargement ensemble blob et plage/entier blob téléchargement. Éviter d’écrire dans un blob crypté à l’aide du protocole opérations placer un bloc, placez la liste de blocage, écrire les Pages, Pages claires ou ajouter un bloc ; dans le cas contraire, vous pouvez endommager le blob crypté et rendre illisibles.
>
>- Pour les tables, il existe une contrainte similaire. Prenez soin de ne pas mettre à jour les propriétés cryptées sans mettre à jour les métadonnées de cryptage.  
>
>- Si vous définissez des métadonnées sur le blob crypté, vous pouvez remplacer les métadonnées de chiffrement requises pour le déchiffrement, étant donné que la définition des métadonnées n’est pas cumulable. Cela est également vrai pour les captures instantanées ; Évitez de spécifier des métadonnées lors de la création d’un instantané d’un objet blob chiffré. Si les métadonnées doivent être définies, veillez à appeler la méthode **downloadAttributes** pour obtenir les métadonnées de cryptage actuel et d’éviter les écritures simultanées tandis que les métadonnées sont définie.  
>
>- Activer l’indicateur **requireEncryption** dans les options de requête par défaut pour les utilisateurs qui doivent fonctionner uniquement avec des données cryptées. Pour plus d’informations, voir ci-dessous.  

## <a name="client-api--interface"></a>API cliente / Interface  
Lors de la création d’un objet EncryptionPolicy, les utilisateurs peuvent fournir une clé (mise en œuvre du IKey), seulement un programme de résolution (mise en œuvre de IKeyResolver), ou les deux. IKey est le type de clé de base qui est identifié à l’aide d’un identificateur de clé, et qui fournit la logique pour le dévoilement/le renvoi à la ligne. IKeyResolver est utilisé pour résoudre une clé pendant le processus de décryptage. Il définit une méthode ResolveKey qui renvoie une IKey reçoit un identificateur de clé. Cela fournit aux utilisateurs la possibilité de choisir entre plusieurs clés qui sont gérés dans plusieurs emplacements.

- Pour le cryptage, la clé est toujours utilisée, et l’absence d’une clé entraîne une erreur.  
- Pour le déchiffrement :  
    - Le résolveur de clé est appelé si spécifié pour obtenir la clé. Si le programme de résolution est spécifié, mais ne dispose pas d’un mappage pour l’identificateur de clé, une erreur est levée.  
    - Si le programme de résolution n’est pas spécifié, mais une clé est spécifiée, la clé est utilisée si son identificateur correspond à l’identificateur de la clé requise. Si l’identificateur ne correspond pas, une erreur est levée.  

      Les [exemples de chiffrement](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) <fix URL>ainsi vous montrer un scénario de bout en bout plus détaillé pour les objets BLOB, les files d’attente et les tables, grâce à l’intégration de la chambre forte de clé.

### <a name="requireencryption-mode"></a>Mode de RequireEncryption  
Les utilisateurs peuvent activer éventuellement d’un mode de fonctionnement dans lequel tous les téléchargements et les transferts doivent être cryptées. Dans ce mode, les tentatives de télécharger des données qui ne sont pas cryptées sur le service des données sans une stratégie de cryptage échouera sur le client. L’indicateur **requireEncryption** de l’objet d’options de demande de contrôler ce comportement. Si votre application crypte tous les objets stockés dans le stockage Azure, vous pouvez définir la propriété **requireEncryption** sur les options de requête par défaut pour l’objet de client de service.   

Par exemple, utiliser **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** pour demander le cryptage pour toutes les opérations de blob effectuées par le biais de cet objet client.

### <a name="blob-service-encryption"></a>Cryptage des service BLOB  
Créer un objet **BlobEncryptionPolicy** et la définir dans les options de requête (par API ou à un niveau client à l’aide de **DefaultRequestOptions**). Tout le reste sera géré par la bibliothèque cliente en interne.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

    // Set the encryption policy on the request options.
    BlobRequestOptions options = new BlobRequestOptions();
    options.setEncryptionPolicy(policy);

    // Upload the encrypted contents to the blob.
    blob.upload(stream, size, null, options, null);

    // Download and decrypt the encrypted contents from the blob.
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    blob.download(outputStream, null, options, null);

### <a name="queue-service-encryption"></a>Cryptage des service de file d’attente  
Créer un objet **QueueEncryptionPolicy** et la définir dans les options de requête (par API ou à un niveau client à l’aide de **DefaultRequestOptions**). Tout le reste sera géré par la bibliothèque cliente en interne.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

    // Add message
    QueueRequestOptions options = new QueueRequestOptions();
    options.setEncryptionPolicy(policy);

    queue.addMessage(message, 0, 0, options, null);

    // Retrieve message
    CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);

### <a name="table-service-encryption"></a>Cryptage des service de table  
En plus de la création d’une stratégie de cryptage et définition des options de requête, vous devez spécifier un **EncryptionResolver** de **TableRequestOptions**, ou la valeur de l’attribut [Encrypt] sur l’accesseur Get et l’accesseur Set de l’entité.

### <a name="using-the-resolver"></a>À l’aide de l’utilitaire de résolution  

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

    TableRequestOptions options = new TableRequestOptions()
    options.setEncryptionPolicy(policy);
    options.setEncryptionResolver(new EncryptionResolver() {
        public boolean encryptionResolver(String pk, String rk, String key) {
            if (key == "foo")
            {
                return true;
            }
            return false;
        }
    });

    // Insert Entity
    currentTable.execute(TableOperation.insert(ent), options, null);

    // Retrieve Entity
    // No need to specify an encryption resolver for retrieve
    TableRequestOptions retrieveOptions = new TableRequestOptions()
    retrieveOptions.setEncryptionPolicy(policy);

    TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
    TableResult result = currentTable.execute(operation, retrieveOptions, null);

### <a name="using-attributes"></a>À l’aide d’attributs  
Comme mentionné ci-dessus, si l’entité implémente TableEntity, puis la lecture et propriétés peuvent être décorées avec l’attribut [Encrypt] au lieu de spécifier **EncryptionResolver**.

    private string encryptedProperty1;

    @Encrypt
    public String getEncryptedProperty1 () {
        return this.encryptedProperty1;
    }

    @Encrypt
    public void setEncryptedProperty1(final String encryptedProperty1) {
        this.encryptedProperty1 = encryptedProperty1;
    }

## <a name="encryption-and-performance"></a>Cryptage et performances  
Notez que le cryptage de votre stockage de données aboutit à une surcharge de performance supplémentaires. La clé de contenu et le vecteur doivent être généré, le contenu proprement dit doit être chiffré et meta-données supplémentaires doivent être mis en forme et téléchargées. Cette surcharge peut varier en fonction de la quantité de données à crypter. Nous vous recommandons de toujours tester leurs performances au cours du développement des applications.

## <a name="next-steps"></a>Étapes suivantes  

- La [Bibliothèque de Client de stockage Azure pour Java Maven package](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) de téléchargement  
- Télécharger la [bibliothèque Client de stockage Azure pour le Code Source Java à partir de GitHub](https://github.com/Azure/azure-storage-java)   
- Télécharger la clé Azure coffre-fort la bibliothèque Maven pour les packages Java Maven :
    - Package de [base](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core)
    - Package [client](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault)
- Consultez la [Documentation chambre forte de clé Azure](../key-vault/key-vault-whatis.md)  
