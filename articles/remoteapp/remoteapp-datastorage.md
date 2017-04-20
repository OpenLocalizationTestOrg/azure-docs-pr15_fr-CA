
<properties
    pageTitle="Ne stockez jamais des données sensibles sur des images personnalisées pour Azure RemoteApp | Microsoft Azure"
    description="En savoir plus sur les règles de stockage des données dans des images personnalisées dans Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="never-store-sensitive-data-on-custom-images"></a>Ne stockez jamais des données sensibles sur des images personnalisées

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Lorsque vous hébergez votre propre application dans Azure RemoteApp, la première étape consiste à créer une image personnalisée. Nous utilisons cette image personnalisée pour créer des instances de machine virtuelle qui servent de vos applications à vos utilisateurs. L’image personnalisée doit contenir uniquement les applications et les données sensibles ne peuvent être perdues, comme les bases de données SQL, fichiers du personnel ou des fichiers de données particulières telles que des fichiers de la société QuickBooks. Toutes les données sensibles doivent résider externe pour Azure RemoteApp sur un serveur de fichiers, un autre Azure VM, ou dans SQL Azure. L’image doit héberger uniquement de l’application qui se connecte à la source de données et présente les données. Pour plus d’informations, consultez [Configuration requise pour les images d’Azure RemoteApp](remoteapp-imagereqs.md) . 

Pour comprendre pourquoi vous ne devez pas stocker les données sensibles, vous devez comprendre le fonctionnement d’Azure RemoteApp. Lorsqu’une collection est créée ou mis à jour en tâche de fond plusieurs clones ou des copies de l’image sont créés. Toutes les instances de ces machines virtuelles sont des répliques exactes de l’image personnalisée ; Lorsque les utilisateurs lancent des applications qu’ils sont connectés à l’une de ces instances de machine virtuelle. Mais la même instance n’est pas garantie et importe peu car ils ne sont pas permanentes. Les instances de la machine virtuelle qui héberge les applications ne sont pas permanentes et peuvent être détruits ou supprimés en fonction, par exemple, lors de la mise à jour de la collection. 

Une fois que la collection est configurée et que les utilisateurs commencent à se connecter aux ordinateurs virtuels, les données utilisateur sont persistantes et protégée car elle est enregistrée sur le stockage séparé au sein d’un disque dur virtuel, que nous appelons un [disque de profil utilisateur (UDP)](remoteapp-upd.md), qui est le profil utilisateur dans c:\users\<userprofile >. Lorsqu’une application démarre, l’UPD est monté et traité comme un profil utilisateur local par le système d’exploitation. En savoir plus sur la façon [d’Azure RemoteApp enregistre les données et paramètres utilisateur](remoteapp-upd.md).

Exemple de données ne doit pas résider dans l’image :

- Partage des données pour les utilisateurs d’accéder à
- Base de données SQL ou base de données QuickBooks
- Toutes les données de D:\

Exemple de données pouvant résider dans le profil par défaut à copier dans UPD chaque utilisateurs :

- Fichiers de configuration par utilisateur
- Scripts qui l’utilisateur a besoin des conservées dans leur UPD

Points clés :

- Jamais banque de données sensibles qui peuvent causer la perte de l’image lors de la création d’une image personnalisée.
- Les données sensibles doivent toujours résider sur un serveur de fichiers distinct, séparé Azure VM, sur le nuage et toujours externes pour les instances de la machine virtuelle qui héberge vos applications Azure RemoteApp. 
- Données utilisateur sont enregistrées et conservée dans le disque de profil utilisateur (UDP)


