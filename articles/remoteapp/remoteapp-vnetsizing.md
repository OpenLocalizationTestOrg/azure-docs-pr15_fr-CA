
<properties
    pageTitle="Dimensionnement des informations pour un VNET dans Azure RemoteApp | Microsoft Azure"
    description="En savoir plus sur la configuration d’adresse IP pour RemoteApp Azure en cours d’exécution avec une VNET"
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



# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Informations sur la taille d’une VNET dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Lorsque vous utilisez Azure RemoteApp avec un réseau virtuel (VNET), RemoteApp utilise des adresses IP au sein du sous-réseau. En fonction de l’échelle de votre service de RemoteApp, vous devez vous assurer que votre sous-réseau a suffisamment d’adresses IP disponibles pour les ordinateurs virtuels de RemoteApp. Bien que ce guide de dimensionnement n’est pas parfait indiqué comment RemoteApp dynamiquement tourne et tourne à l’arrêt des machines virtuelles au sein d’une collection, il vous permettra d’estimer votre plage de sous-réseau. Ceci est particulièrement important que, une fois qu’un service de RemoteApp est placé dans une VNET, vous ne peut pas augmenter la taille de sous-réseau sans suppression de RemoteApp.

Pour chaque collection de RemoteApp que vous souhaitez exécuter à sa capacité maximale, vous devez disposer de 100 adresses IP disponibles. Par exemple, si vous avez une seule collection de RemoteApp dans le plan Standard, et vous souhaitez que le nombre maximal de 500 utilisateurs, vous devez disposer de 100 adresses IP pour cette collection. De même, vous devez 100 adresses IP pour une collection de RemoteApp dans le plan de base qui a 800 utilisateurs. Si vous envisagez d’avoir le moins d’utilisateurs (inférieur à la valeur maximale), vous pouvez réduire les adresses IP que nécessaires par collection. L’exigence de taille minimale de sous-réseau est 30 adresses IP (/ 27).

Vérifiez les informations suivantes pour vous assurer que votre VNET est correctement configuré et qu’il fonctionne :

- [Migrer à partir d’un VNET personnel vers un VNET Azure](remoteapp-migratevnet.md)
- [Valider la VNET Azure à utiliser avec Azure RemoteApp](remoteapp-vnet.md)
