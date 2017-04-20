
<properties
    pageTitle="Liste des Ports et des URL à la liste d’autorisation pour le déploiement de RemoteApp Azure dans le réseau virtuel client | Microsoft Azure"
    description="Découvrez les ports et les adresses URL vous devrez configurer pour la communication par le biais de RemoteApp d’Azure."
    services="remoteapp"
    documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="elizapo" />



# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Liste des Ports et des URL pour autoriser l’accès pour le déploiement de RemoteApp Azure client réseau virtuel 

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Les éléments suivants s’applique aux Azure RemoteApp une collection nuage ou hybride si vous le déployez sur un réseau virtuel (VNET). Pour plus d’informations sur les réseaux virtuels, consultez [Vue d’ensemble de réseau virtuel](../virtual-network/virtual-networks-overview.md). Si vous avez créé un groupe de sécurité réseau (NSG) restriction du trafic aux ressources de votre réseau virtuel que vous avez choisi pour Azure RemoteApp, vérifiez que les éléments suivants sont accessibles et autorisées via les stratégies de sécurité sur le réseau virtuel. Pour plus d’informations sur les groupes de sécurité réseau, veuillez consulter [ce qu’est un groupe de sécurité réseau ? (NSG)](../virtual-network/virtual-networks-nsg.md).

##  <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Sous-réseau de RemoteApp Azure a besoin d’accéder à ces points de terminaison et l’URL : 
*   *. servicebus.windows.net
*    *. servicebus.net
*    https://*.RemoteApp.windwsazure.com  
*    https://www.RemoteApp.windowsazure.com 
*    https://*RemoteApp.windowsazure.com  
*    https://*.Core.Windows.NET  
*    Sortant : TCP : TCP 443, : 10101-10175 
*    Facultatif – UDP : 10201-10275  
 
## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp clients nécessitent l’accès à ces points de terminaison et l’URL : 

Par les clients, on entend les ordinateurs de bureau, les périphériques etc. que les gens utilisent pour se connecter à des applications déployées dans la collection Azure RemoteApp.

-  https://telemetry.RemoteApp.windowsazure.com  
-  https://*.RemoteApp.windowsazure.com (les ports UDP facultatifs sont pour cette adresse) 
-  https://login.Windows.NET  
-  https://login.microsoftonline.com  
-  https://www.RemoteApp.windowsazure.com 
-  https://*.Core.Windows.NET  
-  Sortant : TCP : 443  
-  Facultatif - UDP : 3391 