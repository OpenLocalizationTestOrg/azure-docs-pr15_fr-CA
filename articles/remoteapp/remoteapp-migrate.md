
<properties
    pageTitle="Migrer les données utilisateur à partir d’Azure RemoteApp | Microsoft Azure"
    description="Apprenez à migrer vos données utilisateur et Azure RemoteApp."
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



# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Comment migrer des données dans et en dehors d’Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Vous pouvez utiliser de nombreuses méthodes et différents outils pour transférer les [données utilisateur](remoteapp-upd.md) dans et en dehors d’Azure RemoteApp. Voici quelques méthodes :

- Copier et coller à l’aide de partage du Presse-papiers
- Copier des fichiers et des données sur un serveur de fichiers
- Copier les fichiers dans OneDrive pour l’entreprise via un navigateur
- Copier des fichiers à l’aide de la redirection

>[AZURE.NOTE] 
> Vous ne pouvez pas activer le OneDrive pour les agents de synchronisation professionnels et particuliers - elles [ne sont pas pris en charge](remoteapp-onedrive.md) dans Azure RemoteApp.

## <a name="use-copy-and-paste-in-file-explorer"></a>Utilisez copier-coller dans l’Explorateur de fichiers

Copier et coller à l’aide du Presse-papiers est activée [par défaut](remoteapp-redirection.md)l’option RemoteApp les déploiements. Cela permet aux utilisateurs de copier des fichiers entre leurs applications RemoteApp et PC locales. Souvent, dans le cours normal de l’utilisation d’applications de RemoteApp, utilisateurs ont enregistré fichiers sur leurs UPDs - déplacement que les données de RemoteApp sont faciles :

1. [Explorateur de fichiers publier sous la forme d’une application](remoteapp-publish.md) dans une collection de RemoteApp. (Notez qu’il s’agit d’une tâche administrative).
2. Diriger vos utilisateurs pour lancer l’application Explorateur de fichiers que vous avez publié et qui permet de copier et coller des fichiers à la fois dans leur UPD et en sortir.

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>Télécharger des fichiers et des données sur un serveur de fichiers à l’aide de la copie de fichiers réseau standard

Souvent, les organisations utilisent des serveurs de fichiers pour stocker des données générales. Si vous connaissez le nom du serveur ou l’emplacement, vos utilisateurs peuvent parcourir le réseau local pour le serveur et puis de copier leurs fichiers, comme c’était le cas ci-dessus. À nouveau, vous voudrez publier Explorateur de fichiers pour RemoteApp et partager avec vos utilisateurs.

>[AZURE.NOTE] 
> Le serveur de fichiers doit être sur le réseau routable que RemoteApp a été déployé dans.

## <a name="copy-files-to-onedrive-for-business"></a>Copier des fichiers à OneDrive pour les entreprises
Bien que vous ne pouvez pas activer la OneDrive de l’agent de synchronisation Business dans RemoteApp, vous pouvez copier les fichiers à partir de votre UPD à OneDrive pour l’entreprise via un navigateur. 

1. Publier un Explorateur de fichiers pour RemoteApp et ensuite d’indiquer aux utilisateurs d’accéder aux fichiers par le biais de cette application. 
2. Il est plus facile de transférer des fichiers si elles sont compressées, afin que les utilisateurs doivent créer un fichier .zip qui contient tous les fichiers pour passer à OneDrive pour les entreprises.
3. Demandez aux utilisateurs d’accéder au portail Office 365, puis accédez à OneDrive et téléchargez le fichier .zip.

## <a name="copy-files-by-using-drive-redirection"></a>Copier les fichiers à l’aide de la redirection de lecteur

Si vous avez activé [la redirection de lecteur](remoteapp-redirection.md), vous avez déjà créé un lecteur mappé pour les utilisateurs. Dans ce cas, ils peuvent leurs fichiers sur le lecteur zip et puis les enregistrer sur leur ordinateur local.