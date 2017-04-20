<properties
    pageTitle="L’utilisation de l’API (Python) - guide des fonctionnalités de gestion de service"
    description="Découvrez comment effectuer des tâches courantes de gestion de service par programme à partir de Python."
    services="cloud-services"
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="how-to-use-service-management-from-python"></a>Comment faire pour utiliser la gestion des services à partir de Python

> [AZURE.NOTE] API de gestion de service est remplacé par la nouvelle API de gestion des ressources, actuellement disponibles dans une version d’évaluation.  Reportez-vous à la [documentation de gestion des ressources Azure](http://azure-sdk-for-python.readthedocs.org/) pour plus d’informations sur l’utilisation de la nouvelle API de gestion des ressources à partir de Python.

Ce guide vous explique comment effectuer des tâches courantes de gestion de service par programme à partir de Python. La classe **ServiceManagementService** dans le [Kit de développement logiciel Azure pour les Python](https://github.com/Azure/azure-sdk-for-python) prend en charge l’accès par programme à l’essentiel de la fonctionnalité de liées à la gestion de service qui est disponible dans [Azure portal classique] [ management-portal] (comme la **Création, la mise à jour et la suppression des services en nuage, des déploiements, des services de gestion de données et des machines virtuelles**). Cette fonctionnalité peut être utile de créer des applications qui ont besoin d’accéder par programmation à la gestion des services.

## <a name="WhatIs"> </a>Ce qu’est la gestion des services
L’API de gestion de Service fournit un accès par programme à l’essentiel de la fonctionnalité de gestion de service disponible via le [portail de classique Azure][management-portal]. SDK Azure pour les Python vous permet de gérer vos services en nuage et les comptes de stockage.

Pour utiliser l’API de gestion de Service, vous devez [créer un compte Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Concepts
Le Kit de développement Azure pour les Python encapsule l' [API de gestion des services Azure][svc-mgmt-rest-api], qui est une API REST. Toutes les opérations d’API sont effectuées via le protocole SSL et mutuellement authentifient à l’aide de certificats X.509 v3. Le service de gestion peut être accessible depuis un service exécuté dans Azure, ou directement sur Internet à partir de toute application qui peut envoyer une demande HTTPS et recevoir une réponse HTTPS.

## <a name="Installation"> </a>Installation

Toutes les fonctionnalités décrites dans cet article sont disponibles dans le `azure-servicemanagement-legacy` package, que vous pouvez installer à l’aide du pip. Pour plus d’informations sur l’installation (par exemple, si vous ne connaissez pas les Python), consultez cet article : [installer les Python et le SDK Azure](../python-how-to-install.md)

## <a name="Connect"> </a>Comment : se connecter à la gestion des services
Pour vous connecter au point de terminaison de la gestion des services, vous avez besoin de votre ID d’abonnement Azure et un certificat de gestion valide. Vous pouvez obtenir votre ID d’abonnement via le [portail de classique Azure][management-portal].

> [AZURE.NOTE] Depuis Azure SDK pour les Python v0.8.0, il est désormais possible d’utiliser les certificats créés avec OpenSSL lors de l’exécution de Windows.  Il requiert les Python 2.7.4 ou version ultérieure. Nous recommandons aux utilisateurs d’utiliser OpenSSL et non .pfx, depuis la prise en charge de .pfx certificats seront probablement supprimés à l’avenir.

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificats de gestion sous Mac/Windows/Linux (OpenSSL)
Vous pouvez utiliser [OpenSSL](http://www.openssl.org/) pour créer votre certificat de gestion.  Vous avez besoin créer les deux certificats, l’autre pour le serveur (un `.cer` fichier) et l’autre pour le client (un `.pem` fichier). Pour créer la `.pem` de fichiers, exécutez :

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Pour créer la `.cer` de certificats, exécutez :

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Pour plus d’informations sur les certificats d’Azure, consultez [Vue d’ensemble des certificats pour les Services en nuage Azure](./cloud-services-certs-create.md). Pour une description complète des paramètres de OpenSSL, consultez la documentation à l’adresse [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Après avoir créé ces fichiers, vous devez télécharger le `.cer` fichier vers Azure via l’action « Téléchargement » de l’onglet « Paramètres » d' [Azure portal classique][management-portal], et vous devez notez où vous avez enregistré le `.pem` fichier.

Après avoir obtenu votre ID d’abonnement, créé un certificat et téléchargé le `.cer` fichier Azure, vous pouvez vous connecter au point de terminaison gestion Azure en passant l’id d’abonnement et le chemin d’accès à la `.pem` fichier **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Dans l’exemple précédent, `sms` est un objet **ServiceManagementService** . La classe **ServiceManagementService** est la classe principale utilisée pour gérer les services Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificats de gestion de Windows (MakeCert)

Vous pouvez créer un certificat auto-signé gestion sur votre ordinateur à l’aide de `makecert.exe`.  Ouvrez une **invite de commandes de Visual Studio** en tant qu' **administrateur** et utilisez la commande suivante, en remplaçant *AzureCertificate* par le nom de certificat que vous souhaitez utiliser.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

La commande crée le `.cer` de fichiers et l’installe dans le magasin de certificats **personnel** . Pour plus d’informations, consultez [Vue d’ensemble des certificats pour les Services en nuage Azure](./cloud-services-certs-create.md).

Après avoir créé le certificat, vous devez télécharger le `.cer` fichier vers Azure via l’action « Téléchargement » de l’onglet « Paramètres » d' [Azure portal classique][management-portal].

Une fois que vous avez obtenu votre ID d’abonnement, créé un certificat et téléchargé le `.cer` fichier Azure, vous pouvez vous connecter au point de terminaison gestion Azure en transmettant l’id d’abonnement et l’emplacement du certificat dans votre magasin de certificats **personnel** à **ServiceManagementService** (encore une fois, remplacez *AzureCertificate* par le nom de votre certificat) :

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Dans l’exemple précédent, `sms` est un objet **ServiceManagementService** . La classe **ServiceManagementService** est la classe principale utilisée pour gérer les services Azure.

## <a name="ListAvailableLocations"> </a>Comment : liste des emplacements disponibles

Pour répertorier les emplacements disponibles pour l’hébergement de services, utilisez la **liste\_emplacements** méthode :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Lorsque vous créez un service en nuage ou un service de stockage, vous devez fournir un emplacement valide. La **liste\_emplacements** méthode retourne toujours à jour la liste des emplacements disponibles. À ce jour, les emplacements disponibles sont :

- Europe de l’ouest
- Europe du Nord
- Asie du Sud-est
- Asie de l’est
- États-Unis centre
- États-Unis centre nord
- États-Unis centre sud
- États-Unis Ouest
- Les États-Unis
- Nord-est du Japon
- Ouest du Japon
- Sud du Brésil
- Est de l’Australie
- Sud-est de l’Australie

## <a name="CreateCloudService"> </a>Comment : créer un service cloud

Lorsque vous créez une application et que vous l’exécutez dans Azure, la configuration et le code ensemble constituent un Azure [cloud service][] (connu comme étant un *service hébergé* dans les versions antérieures de Azure). Le **créer\_hébergé\_service** méthode vous permet de créer un nouveau service hébergé en fournissant un emplacement, une description, un nom de service hébergé (qui doit être unique dans Azure) et une étiquette (automatiquement encodé en base64).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Vous pouvez répertorier tous les services hébergés de votre abonnement avec le **liste\_hébergé\_services** méthode :

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Si vous souhaitez obtenir des informations sur un service hébergé particulier, vous pouvez le faire en passant le nom de service hébergé à la **obtenir\_hébergé\_service\_propriétés** méthode :

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Après avoir créé un service en nuage, vous pouvez déployer votre code au service avec le **créer\_déploiement** méthode.

## <a name="DeleteCloudService"> </a>Comment : supprimer un service cloud

Vous pouvez supprimer un service cloud en passant le nom de service pour la **Supprimer\_hébergé\_service** méthode :

    sms.delete_hosted_service('myhostedservice')

Avant de supprimer un service, tous les déploiements du service doivent être supprimées. (Voir [Comment : supprimer un déploiement](#DeleteDeployment) pour plus d’informations.)

## <a name="DeleteDeployment"> </a>Comment : supprimer un déploiement

Pour supprimer un déploiement, vous devez utiliser le **Supprimer\_déploiement** méthode. L’exemple suivant montre comment supprimer un déploiement nommé `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Comment : créer un service de stockage

Un [service de stockage](../storage/storage-create-storage-account.md) vous donne accès à Azure [BLOB](../storage/storage-python-how-to-use-blob-storage.md), les [Tables](../storage/storage-python-how-to-use-table-storage.md)et les [files d’attente](../storage/storage-python-how-to-use-queue-storage.md). Pour créer un service de stockage, vous avez besoin d’un nom pour le service (entre 3 et 24 caractères en minuscules et unique dans Azure), une description, une étiquette (100 caractères au maximum, automatiquement encodés en base64) et un emplacement. L’exemple suivant montre comment créer un service de stockage en spécifiant un emplacement.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Remarque dans l’exemple précédent qui le statut de la **créer\_stockage\_compte** opération peut être extrait en passant le résultat retourné par **créer\_stockage\_compte** pour la **obtenir\_opération\_état** méthode.  

Vous pouvez répertorier vos comptes de stockage et de leurs propriétés à l’aide de la **liste\_stockage\_comptes** méthode :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Comment : supprimer un service de stockage

Vous pouvez supprimer un service de stockage en passant le nom de service de stockage pour la **Supprimer\_stockage\_compte** méthode. Suppression d’un service de stockage supprime toutes les données stockées dans le service (des objets BLOB, des tables et des files d’attente).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Comment : liste des systèmes d’exploitation disponibles

Pour répertorier les systèmes d’exploitation qui sont disponibles pour l’hébergement de services, utilisez la **liste\_de\_systèmes** méthode :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Vous pouvez également utiliser le **liste\_de\_système\_familles** méthode, qui regroupe les systèmes d’exploitation par la famille :

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Comment : créer une image de système d’exploitation

Pour ajouter une image de système d’exploitation dans le référentiel de l’image, utilisez la **Ajouter\_os\_image** méthode :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Pour répertorier les images de système d’exploitation qui sont disponibles, utilisez la **liste\_os\_images** méthode. Il inclut toutes les plates-formes et les images de l’utilisateur :

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Comment : supprimer une image de système d’exploitation

Pour supprimer une image de l’utilisateur, utilisez la **Supprimer\_os\_image** méthode :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Comment : créer un ordinateur virtuel

Pour créer un ordinateur virtuel, vous devez d’abord créer un [service cloud](#CreateCloudService).  Puis créez le déploiement de machines virtuelles à l’aide de la **créer\_virtuels\_machine\_déploiement** méthode :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>Comment : supprimer un ordinateur virtuel

Pour supprimer un ordinateur virtuel, vous supprimez d’abord le déploiement à l’aide de la **Supprimer\_déploiement** méthode :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Le service en nuage peut ensuite être supprimé à l’aide de la **Supprimer\_hébergé\_service** méthode :

    sms.delete_hosted_service(service_name='myvm')

##<a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Comment : Créer un ordinateur virtuel à partir d’une Image capturée Virtual Machine

Pour capturer une image de machine virtuelle, vous appelez tout d’abord la **capture\_vm\_image** méthode :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Ensuite, pour vous assurer que vous avez correctement capturé l’image, utilisez la **liste\_vm\_images** api et assurez-vous que l’image s’affiche dans les résultats :

    images = sms.list_vm_images()

Pour enfin créer l’ordinateur virtuel à l’aide de l’image capturée, vous devez utiliser le **créer\_virtuels\_machine\_déploiement** méthode comme auparavant, mais cette fois passer dans la vm_image_name à la place

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Pour en savoir plus sur la capture d’une Machine virtuelle Linux, reportez-vous à la section [la Capture d’une Machine virtuelle Linux.](../virtual-machines/virtual-machines-linux-classic-capture-image.md)

Pour en savoir plus sur la capture d’une Machine virtuelle Windows, reportez-vous à la section [la Capture d’une Machine virtuelle Windows.](../virtual-machines/virtual-machines-windows-classic-capture-image.md)

## <a name="What's Next"> </a>Étapes suivantes

Maintenant que vous avez appris les notions de base de gestion des services, vous pouvez accéder à la [documentation de référence API complète pour le Kit de développement de Python Azure](http://azure-sdk-for-python.readthedocs.org/) et effectuer des tâches complexes facilement pour gérer votre application python.

Pour plus d’informations, consultez le [Centre de développement de Python](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[service de cloud]:/services/cloud-services/

