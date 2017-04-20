<properties
    pageTitle="Cryptage de côté client avec les Python pour le stockage de Microsoft Azure | Microsoft Azure"
    description="La bibliothèque de Client de stockage Azure pour les Python prend en charge le cryptage côté client pour une sécurité maximale pour vos applications de stockage Azure."
    services="storage"
    documentationCenter="python"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Cryptage de côté client avec les Python pour le stockage de Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Vue d’ensemble

La [Bibliothèque de Client de stockage Azure pour les Python](https://pypi.python.org/pypi/azure-storage) prend en charge le chiffrement des données dans les applications clientes avant le téléchargement vers le stockage Azure et le décryptage des données pendant leur téléchargement vers le client.

>[AZURE.NOTE] La bibliothèque de Python de stockage Azure est en mode Aperçu.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Cryptage et décryptage via la technique de l’enveloppe
Les processus de cryptage et de décryptage suivent la technique de l’enveloppe.

### <a name="encryption-via-the-envelope-technique"></a>Cryptage via la technique de l’enveloppe
Le cryptage via la technique de l’enveloppe fonctionne de la manière suivante :

1.  La bibliothèque cliente de stockage Azure génère une clé de chiffrement de contenu (CEK), qui est une clé symétrique usage unique.

2.  Les données utilisateur sont cryptées à l’aide de ce CEK.

3.  Le CEK est ensuite encapsulé (crypté) à l’aide de la clé de cryptage de clés (KEK). Le KEK est identifiée par un identificateur de clé et peut être une paire de clés asymétrique ou une clé symétrique, qui est gérée localement.
La bibliothèque cliente de stockage lui-même n’a jamais accès à KEK. La bibliothèque appelle l’algorithme de clé d’habillage qui est fourni par le KEK. Les utilisateurs peuvent choisir d’utiliser des fournisseurs personnalisés pour conditionnement/désencapsuler une clé si vous le souhaitez.

4.  Les données cryptées sont ensuite téléchargées sur le service de stockage Azure. La clé encapsulée avec des métadonnées de cryptage supplémentaire est stockée en tant que métadonnées (sur un objet blob) ou interpolée avec les données chiffrées (messages de la file d’attente et les entités de table).

### <a name="decryption-via-the-envelope-technique"></a>Décryptage via la technique de l’enveloppe
Le décryptage via la technique de l’enveloppe fonctionne de la manière suivante :

1.  La bibliothèque cliente suppose que l’utilisateur gère la clé de cryptage de clés (KEK) localement. L’utilisateur n’a pas besoin de connaître la clé spécifique qui a été utilisée pour le chiffrement. Au lieu de cela, un résolveur de clés qui résout les différents identificateurs de clé aux clés, peut être paramétré et utilisé.

2.  La bibliothèque cliente télécharge les données chiffrées ainsi que de tout matériel de chiffrement est stockée sur le service.

3.  La clé de chiffrement de contenu renvoyé à la ligne (CEK) est désencapsulée (décrypté) à l’aide du cryptage par clé clé (KEK). Ici encore, la bibliothèque cliente n’a pas accès à KEK. Elle appelle simplement les algorithme désencapsuler du fournisseur personnalisé.

4.  La clé de chiffrement de contenu (CEK) est ensuite utilisée pour décrypter les données cryptées de l’utilisateur.

## <a name="encryption-mechanism"></a>Mécanisme de cryptage  
La bibliothèque cliente de stockage utilise [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) pour chiffrer les données de l’utilisateur. Plus précisément, [CBC (Cipher Block Chaining)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) en mode AES. Chaque service fonctionne un peu différemment, afin que nous allons étudier chacun d’eux ici.

### <a name="blobs"></a>Objets BLOB
La bibliothèque cliente prend actuellement en charge le cryptage des blobs entiers uniquement. Plus précisément, le cryptage est pris en charge lorsque les utilisateurs utilisent le **créer*** méthodes. Pour les téléchargements, à la fois complet et plage sont prises en charge de parallélisation de chargement et de téléchargement est disponible.

Pendant le chiffrement, la bibliothèque cliente génère un vecteur d’initialisation aléatoire (IV) de 16 octets, avec une clé de chiffrement aléatoire de contenu (CEK) de 32 octets et effectuer un chiffrement d’enveloppe des données blob à l’aide de ces informations. Le CEK encapsulé et des métadonnées de chiffrement supplémentaires sont alors enregistrés comme blob des métadonnées avec le blob crypté sur le service.

>[AZURE.WARNING] Si vous modifiez ou télécharger vos propres métadonnées pour l’objet blob, vous devez vous assurer que ces métadonnées sont conservées. Si vous téléchargez de nouvelles métadonnées sans ces métadonnées, le CEK encapsulé, IV et autres métadonnées seront perdues et le contenu de l’objet blob ne sera jamais récupérable à nouveau.

Téléchargement d’un objet blob chiffré, il faut récupérer le contenu entier du blob de l’à l’aide d' **obtenir*** méthodes pratiques. Le CEK encapsulé est dévoilé et permet de retourner les données décryptées aux utilisateurs ainsi que le vecteur d’initialisation (stocké en tant que blob métadonnées dans ce cas).

Télécharger une plage arbitraire (**get*** méthodes avec les paramètres de plage passé) dans le blob crypté implique l’ajustement de la plage fournie par les utilisateurs pour obtenir une petite quantité de données supplémentaires qui peuvent être utilisées pour décrypter la plage demandée.

BLOB de page et les objets BLOB de bloc seulement peut être crypté/décryptée à l’aide ce régime. Il n’existe actuellement aucune prise en charge pour le cryptage ajouter des objets BLOB.

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

3.  Le CEK encapsulé et des métadonnées de chiffrement supplémentaires sont alors enregistrés comme deux propriétés réservées supplémentaires. Le premier réservé propriété (\_ClientEncryptionMetadata1) est une propriété de chaîne qui conserve des informations sur IV, version et clé encapsulée. Le second réservé propriété (\_ClientEncryptionMetadata2) est une propriété binaire qui conserve des informations sur les propriétés qui sont cryptés. Les informations contenues dans cette seconde propriété (\_ClientEncryptionMetadata2) est lui-même crypté.

4.  En raison de ces propriétés réservées supplémentaires requises pour le cryptage, les utilisateurs peuvent désormais avoir que 250 propriétés personnalisées au lieu de 252. La taille totale de l’entité doit être inférieure à 1 Mo.

    Notez que seules les propriétés de chaîne peuvent être cryptées. Si d’autres types de propriétés doivent être cryptées, ils doivent être convertis en chaînes. Les chaînes cryptées sont stockées sur le service en tant que propriétés binaires, et ils sont convertis en chaînes (chaînes brutes, pas EntityProperties de type EdmType.STRING) après avoir été déchiffrés.

    Pour les tables, en plus de la stratégie de cryptage, les utilisateurs doivent spécifier les cryptage des propriétés. Ceci est possible en stocker ces propriétés dans les objets de TableEntity dont la valeur type EdmType.STRING et chiffrer la valeur true ou la définition de l’encryption_resolver_function sur l’objet tableservice. Un résolveur de cryptage est une fonction qui prend une clé de partition, la clé de ligne et le nom de la propriété et retourne une valeur booléenne qui indique si cette propriété doit être chiffrée. Pendant le chiffrement, la bibliothèque cliente utilise ces informations pour décider si une propriété doit être chiffrée pendant l’écriture sur le réseau. Le délégué offre également la possibilité de logique autour de la façon dont les propriétés sont cryptées. (Par exemple, si X, puis crypter de propriété A ; de crypter les propriétés A et B.) Notez qu’il n’est pas nécessaire de fournir cette information lors de la lecture ou de l’interrogation des entités.

### <a name="batch-operations"></a>Opérations de traitement par lots
Une stratégie de cryptage s’applique à toutes les lignes dans le lot. La bibliothèque cliente en interne génère un nouveau vecteur d’initialisation aléatoire et CEK aléatoire par ligne dans le lot. Les utilisateurs peuvent également choisir de crypter des propriétés différentes pour chaque opération dans le lot en définissant ce comportement dans le programme de résolution de cryptage.
Si un lot est créé comme un gestionnaire de contexte via la méthode batch() de tableservice, stratégie de chiffrement de le tableservice automatiquement s’appliquera pour le traitement par lots. Si un lot est explicitement créé en appelant le constructeur, la stratégie de chiffrement doit être passée comme un paramètre et reste inchangée pour la durée de vie du lot.
Notez que les entités sont chiffrées qu’elles sont insérées dans le lot à l’aide de la stratégie de chiffrement du lot (les entités ne sont pas cryptées au moment de la validation du lot à l’aide de la stratégie de cryptage de la tableservice).

### <a name="queries"></a>Requêtes
Pour effectuer des opérations de requête, vous devez spécifier un résolveur de clé qui est en mesure de résoudre toutes les clés dans le jeu de résultats. Si une entité contenue dans les résultats de la requête ne peut pas être résolue à un fournisseur, la bibliothèque cliente lèvera une erreur. Pour une requête qui effectue des projections de côté serveur, la bibliothèque cliente ajoute les propriétés de métadonnées spéciale de cryptage (\_ClientEncryptionMetadata1 et \_ClientEncryptionMetadata2) par défaut pour les colonnes sélectionnées.

>[AZURE.IMPORTANT] Gardez à l’esprit ces points importants lors de l’utilisation du cryptage côté client :
>
>- Lors de la lecture ou l’écriture d’un objet blob chiffré, utilisez les commandes de téléchargement ensemble blob et plage/entier blob téléchargement. Éviter d’écrire dans un blob crypté à l’aide du protocole opérations placer un bloc, placez la liste de blocage, écrire des Pages ou effacer des Pages ; dans le cas contraire, vous pouvez endommager le blob crypté et rendre illisibles.
>
>- Pour les tables, il existe une contrainte similaire. Prenez soin de ne pas mettre à jour les propriétés cryptées sans mettre à jour les métadonnées de cryptage.
>
>- Si vous définissez des métadonnées sur le blob crypté, vous pouvez remplacer les métadonnées de chiffrement requises pour le déchiffrement, étant donné que la définition des métadonnées n’est pas cumulable. Cela est également vrai pour les captures instantanées ; Évitez de spécifier des métadonnées lors de la création d’un instantané d’un objet blob chiffré. Si les métadonnées doivent être définies, veillez à appeler la méthode **get_blob_metadata** pour obtenir les métadonnées de cryptage actuel et d’éviter les écritures simultanées tandis que les métadonnées sont définie.
>
>- Activer l’indicateur **require_encryption** sur l’objet de service pour les utilisateurs qui doivent fonctionner uniquement avec des données cryptées. Pour plus d’informations, voir ci-dessous.

La bibliothèque cliente de stockage attend le KEK fourni et le résolveur clé pour implémenter l’interface suivante. La prise en charge pour la gestion de Python KEK [Azure clé coffre-fort](https://azure.microsoft.com/services/key-vault/) est en attente et est intégrée dans cette bibliothèque lorsque terminée.


## <a name="client-api--interface"></a>API cliente / Interface
Après avoir créé un objet de service de stockage (par exemple, blockblobservice), l’utilisateur peut affecter des valeurs aux champs qui constituent une stratégie de cryptage : key_encryption_key, key_resolver_function et require_encryption. Les utilisateurs peuvent fournir uniquement un KEK, seulement un programme de résolution, ou les deux. key_encryption_key est le type de clé de base qui est identifié à l’aide d’un identificateur de clé, et qui fournit la logique pour le dévoilement/le renvoi à la ligne. key_resolver_function est utilisé pour résoudre une clé pendant le processus de décryptage. Elle renvoie un KEK valide un identificateur de clé. Cela fournit aux utilisateurs la possibilité de choisir entre plusieurs clés qui sont gérés dans plusieurs emplacements.

La KEK doit implémenter les méthodes suivantes pour le chiffrement des données :
- wrap_key(cek) : encapsule le CEK spécifié (octets) à l’aide d’un algorithme de choix de l’utilisateur. Retourne la clé encapsulée.
- get_key_wrap_algorithm() : retourne l’algorithme utilisé pour encapsuler les clés.
- get_kid() : retourne l’id de clé de chaîne de cette KEK.
La KEK doit implémenter les méthodes suivantes pour déchiffrer des données :
- unwrap_key (cek, algorithme) : retourne la forme désencapsulée de la CEK spécifiée à l’aide de l’algorithme de la chaîne spécifiée.
- get_kid() : retourne un id de clé de chaîne pour ce KEK.

Le résolveur de clé doit au moins implémenter une méthode renvoyant, étant donné un identificateur de clé, la KEK correspondant qui implémente l’interface ci-dessus. Seulement cette méthode est à affecter à la propriété key_resolver_function sur l’objet de service.

- Pour le cryptage, la clé est toujours utilisée, et l’absence d’une clé entraîne une erreur.
- Pour le déchiffrement :
    - Le résolveur de clé est appelé si spécifié pour obtenir la clé. Si le programme de résolution est spécifié, mais ne dispose pas d’un mappage pour l’identificateur de clé, une erreur est levée.
    - Si le programme de résolution n’est pas spécifié, mais une clé est spécifiée, la clé est utilisée si son identificateur correspond à l’identificateur de la clé requise. Si l’identificateur ne correspond pas, une erreur est levée.

      Les exemples de cryptage de azure.storage.samples <fix URL>illustrent un scénario de bout en bout plus détaillé pour les objets BLOB, les files d’attente et les tables.
        Exemples d’implémentation de la KEK et la résolution de clé sont fournis respectivement dans les fichiers d’exemple comme KeyWrapper et KeyResolver.

### <a name="requireencryption-mode"></a>Mode de RequireEncryption
Les utilisateurs peuvent activer éventuellement d’un mode de fonctionnement dans lequel tous les téléchargements et les transferts doivent être cryptées. Dans ce mode, les tentatives de télécharger des données qui ne sont pas cryptées sur le service des données sans une stratégie de cryptage échouera sur le client. L’indicateur **require_encryption** sur l’objet de service de contrôler ce comportement.

### <a name="blob-service-encryption"></a>Cryptage des service BLOB
Définissez le codage des champs de stratégie sur l’objet blockblobservice. Tout le reste sera géré par la bibliothèque cliente en interne.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_block_blob_service.key_encryption_key = kek
    my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

    # Upload the encrypted contents to the blob.
    my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

    # Download and decrypt the encrypted contents from the blob.
    blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)

### <a name="queue-service-encryption"></a>Cryptage des service de file d’attente
Définissez le codage des champs de stratégie sur l’objet queueservice. Tout le reste sera géré par la bibliothèque cliente en interne.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_queue_service.key_encryption_key = kek
    my_queue_service.key_resolver_funcion = key_resolver.resolve_key

    # Add message
    my_queue_service.put_message(queue_name, content)

    # Retrieve message
    retrieved_message_list = my_queue_service.get_messages(queue_name)

### <a name="table-service-encryption"></a>Cryptage des service de table
En plus de la création d’une stratégie de cryptage et définition des options de requête, vous devez spécifier un **encryption_resolver_function** sur la **tableservice**, ou définir l’attribut de cryptage sur le EntityProperty.

### <a name="using-the-resolver"></a>À l’aide de l’utilitaire de résolution

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Define the encryption resolver_function.
    def my_encryption_resolver(pk, rk, property_name):
            if property_name == 'foo':
                    return True
            return False

    # Set the KEK and key resolver on the service object.
    my_table_service.key_encryption_key = kek
    my_table_service.key_resolver_funcion = key_resolver.resolve_key
    my_table_service.encryption_resolver_function = my_encryption_resolver

    # Insert Entity
    my_table_service.insert_entity(table_name, entity)

    # Retrieve Entity
    # Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
    my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])

### <a name="using-attributes"></a>À l’aide d’attributs
Comme mentionné ci-dessus, une propriété peut être marquée pour le cryptage en la stockant dans un objet EntityProperty et la définition du champ de la crypter.

    encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)

## <a name="encryption-and-performance"></a>Cryptage et performances
Notez que le cryptage de votre stockage de données aboutit à une surcharge de performance supplémentaires. Le contenu clé et un IV doivent être généré, le contenu proprement dit doit être chiffré et métadonnées supplémentaires doivent être mis en forme et téléchargées. Cette surcharge peut varier en fonction de la quantité de données à crypter. Nous vous recommandons de toujours tester leurs performances au cours du développement des applications.

## <a name="next-steps"></a>Étapes suivantes

- La [Bibliothèque de Client de stockage Azure pour Java PyPi package](https://pypi.python.org/pypi/azure-storage) de téléchargement
- Télécharger la [bibliothèque Client de stockage Azure pour les Python de Source de Code à partir de GitHub](https://github.com/Azure/azure-storage-python)
