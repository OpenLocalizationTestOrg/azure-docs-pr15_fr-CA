
<properties
    pageTitle="Exigences d’image RemoteApp Azure | Microsoft Azure"
    description="En savoir plus sur la configuration requise pour la création d’images à utiliser avec Azure RemoteApp"
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



# <a name="requirements-for-azure-remoteapp-images"></a>Exigences pour les images d’Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

RemoteApp Azure utilise une image de Windows Server 2012 R2 pour héberger tous les programmes que vous souhaitez partager avec les utilisateurs. Pour créer une image personnalisée, vous pouvez démarrer avec une image existante ou de [créer un nouveau](remoteapp-create-custom-image.md).

> [AZURE.TIP] Saviez-vous que votre abonnement Azure RemoteApp vous donne accès à une image Windows Server 2012 R2 dans la galerie d’Azure VM que vous pouvez utiliser pour créer votre propre image de modèle ? [Extraire](remoteapp-image-on-azurevm.md).  


La configuration requise pour l’image qui peut être téléchargé pour une utilisation avec Azure RemoteApp est les suivantes :


- Applications personnalisées ne stockent localement des données sur l’image. Ces images sont sans état et ne doivent contenir que des applications.
- L’image ne contient-elle pas de données qui peuvent être perdues.
- La taille de l’image doit être un multiple de MBs. Si vous essayez de télécharger une image qui n’est pas un multiple exact, le téléchargement échoue.
- La taille de l’image doit être de 127 Go ou plus petite.
- Il doit être sur un fichier de disque dur virtuel (fichiers VHDX ne sont pas actuellement pris en charge).
- Le disque dur virtuel ne doit pas être une machine virtuelle de génération 2.
- Le disque dur virtuel peut être de taille fixe ou dynamique. Un disque dur virtuel de taille dynamique est recommandé parce qu’il prend moins de temps à télécharger vers Azure qu’un fichier de disque dur virtuel de taille fixe.
- Le disque doit être initialisé à l’aide de l’enregistrement de démarrage principal (MBR) style de partitionnement. Le style de partition GUID partition table (GPT) n’est pas pris en charge.
- Le disque dur virtuel doit contenir une seule installation de Windows Server 2012 R2. Il peut contenir plusieurs volumes, mais un seul qui contient une installation de Windows.
- Le rôle de l’hôte de Session à distance Bureau (RDSH) et de la fonctionnalité Expérience Bureau doivent être installés.
- Le rôle de Broker pour les connexions Bureau à distance doit *pas* être installé.
- Le système EFS (ENCRYPTING File System) doit être désactivé.
- L’image doit être préparée avec Sysprep en utilisant les paramètres **/oobe /generalize /shutdown** (ne pas utiliser le paramètre **/mode:vm** ).
- Téléchargement de votre disque dur virtuel à partir d’une chaîne d’instantané n’est pas pris en charge.

Pour plus d’informations sur la création d’images pour Azure RemoteApp, voir [créer une image d’Azure RemoteApp](remoteapp-imageoptions.md) .
