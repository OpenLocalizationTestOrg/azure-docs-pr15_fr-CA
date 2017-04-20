<properties
    pageTitle="Procédure pas à pas de concentrateurs n’Archive d’événements Azure | Microsoft Azure"
    description="Exemple qui utilise les Python d’Azure SDK pour illustrer l’utilisation de la fonction d’archivage de concentrateurs d’événement."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="event-hubs-archive-walkthrough-python"></a>Procédure pas à pas les archives de concentrateurs d’événement : Python

Archive de concentrateurs d’événements est une nouvelle fonctionnalité de concentrateurs d’événements qui vous permet de fournir automatiquement les données de flux dans votre événement de supervision à un compte de stockage des objets Blob Azure de votre choix. Cela rend très simple à exécuter sur les données de transmission en continu en temps réel de traitement par lots. Cet article décrit l’utilisation de concentrateurs n’Archive d’événements avec les Python. Pour plus d’informations sur l’archivage de concentrateurs d’événement, consultez l' [article de présentation](event-hubs-archive-overview.md).

Cet exemple utilise les Python d’Azure SDK pour illustrer l’utilisation de la fonction d’archivage. Le sender.py envoie la télémétrie de l’environnement simulé à des concentrateurs d’événement au format JSON. Le concentrateur d’événements est configuré pour utiliser la fonctionnalité d’archivage pour écrire des données BLOB de stockage dans des lots. La archivereader.py lit ensuite ces objets BLOB et crée un fichier d’ajout par périphérique et écrit les données dans des fichiers .csv.

Ce qui va être accompli

1.  Créer un compte de stockage des objets Blob Azure et un conteneur blob, via le portail Azure

2.  Créer un espace de noms concentrateur d’événements, l’utilisation du portail Azure

3.  Créer un concentrateur d’événements avec la fonction d’archivage activée, en utilisant le portail Azure

4.  Envoyer des données vers le concentrateur d’événements avec un script Python

5.  Lire les fichiers de l’archive et de les traiter avec un autre script de Python

Conditions préalables

1.  Python 2.7.x

2.  Un abonnement Azure

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Créer un compte de stockage Azure

1.  Ouvrez une session sur le [portail Azure][].

2.  Dans le volet de navigation gauche du portail, cliquez sur Nouveau, puis cliquez sur données + de stockage, puis cliquez sur compte de stockage.

3.  Renseignez les champs de la lame de compte de stockage et cliquez sur **créer**.

    ![][1]

4.  Lorsque vous voyez le message **Déploiements a réussi** , cliquez sur le nouveau compte de stockage et de la lame **d’Essentials** , cliquez sur **objets BLOB**. Lorsque la blade de **service d’objet Blob** s’ouvre, cliquez sur **+ le conteneur** en haut. Nom de **l’archive**du conteneur, puis fermer la lame **Blob de service** .

5.  Cliquez sur les **touches d’accès rapide** dans la lame gauche et copiez le nom du compte de stockage et la valeur de **l’argument key1**. Enregistrer ces valeurs dans le bloc-notes ou un autre emplacement temporaire.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Créer un script de Python pour envoyer des événements à votre événement de supervision

1.  Ouvrez votre éditeur favori de Python, comme le [Code de Visual Studio][].

2.  Créer un script appelé **sender.py**. Ce script envoie 200 événements à votre événement de supervision. Ils sont relevés de l’environnement simples envoyés au format JSON.

3.  Collez le code suivant dans sender.py :

    ```
    import uuid
    import datetime
    import random
    import json
    from azure.servicebus import ServiceBusService
    
    sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    for y in range(0,20):
        for dev in devices:
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading)
            sbs.send\_event('myhub', s)
        print y
    ```
4.  Mettre à jour le code précédent pour utiliser le nom d’espace de noms et les valeurs de clé que vous avez obtenu lors de la création de l’espace de noms de concentrateurs de l’événement.

## <a name="create-a-python-script-to-read-your-archive-files"></a>Créer un script de Python pour lire vos fichiers archives

1.  Remplir la lame et cliquez sur **créer**.

2.  Créer un script appelé **archivereader.py**. Ce script va lire les fichiers de l’archive et créer un fichier par périphérique pour écrire les données uniquement pour ce périphérique.

3.  Collez le code suivant dans archivereader.py :

    ```
    import os
    import string
    import json
    import avro.schema
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    from azure.storage.blob import BlockBlobService
    
    def processBlob(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed\_json = json.loads(reading["Body"])
            if not 'id' in parsed\_json:
                return
            if not dict.has\_key(parsed\_json['id']):
            list = []
            dict[parsed\_json['id']] = list
        else:
            list = dict[parsed\_json['id']]
            list.append(parsed\_json)
        reader.close()
        for device in dict.keys():
            deviceFile = open(device + '.csv', "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

    def startProcessing(accountName, key, container):
        print 'Processor started using path: ' + os.getcwd()
        block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
        generator = block\_blob\_service.list\_blobs(container)
        for blob in generator:
            if blob.properties.content\_length != 0:
                print('Downloaded a non empty blob: ' + blob.name)
                cleanName = string.replace(blob.name, '/', '\_')
                block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
                processBlob(cleanName)
                os.remove(cleanName)
            block\_blob\_service.delete\_blob(container, blob.name)
    startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  Veillez à coller les valeurs appropriées pour votre nom de compte de stockage et de la clé dans l’appel à `startProcessing`.

## <a name="run-the-scripts"></a>Exécuter les scripts

1.  Ouvrez une invite de commande qui a les Python dans son chemin d’accès et exécutez ces commandes pour installer les packages de composants requis de Python :

    ```
    pip install azure-storage
    pip install azure-servicebus
    pip install avro
    ```
  
    Si vous avez une version antérieure d’un stockage azure ou azure vous devrez peut-être utiliser le **-mise à niveau** option

    Vous devrez peut-être également exécuter ce qui suit (pas nécessaire dans la plupart des systèmes) :

    ```
    pip install cryptography
    ```

2.  Modifier le répertoire où vous avez enregistré sender.py et archivereader.py et exécutez la commande suivante :

    ```
    start python sender.py
    ```
    
    Démarre un nouveau processus de Python pour exécuter l’expéditeur.

3. Maintenant, attendez quelques minutes pour l’archive à exécuter. Puis tapez la commande suivante dans la fenêtre de commande d’origine :

    ```
    python archivereader.py
    ```

Ce processeur archive utilise le répertoire local pour télécharger tous les objets BLOB à partir du compte/conteneur de stockage. Il traitera tout ce qui ne sont pas vides et écrire les résultats au format .csv dans le répertoire local.

## <a name="next-steps"></a>Étapes suivantes

Vous en apprendrez davantage sur événement concentrateurs en consultant les liens suivants :

- [Vue d’ensemble des événements concentrateurs archiver][]
- Un [exemple d’application qui utilise des concentrateurs d’événement][]complète.
- L’exemple de [mise à l’échelle traitement avec les concentrateurs d’événement de l’événement][] .
- [Vue d’ensemble des concentrateurs événements][]
 

[Azure portal]: https://portal.azure.com/
[Vue d’ensemble des événements concentrateurs archiver]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
[Code de Visual Studio]: https://code.visualstudio.com/
[Vue d’ensemble des concentrateurs événements]: event-hubs-overview.md
[exemple d’application qui utilise des concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec les concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
