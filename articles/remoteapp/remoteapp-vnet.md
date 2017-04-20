
<properties
    pageTitle="Valider la VNET Azure à utiliser avec Azure RemoteApp | Microsoft Azure"
    description="Apprenez à vous assurer que votre VNET Azure est prêt à être utilisé avec Azure RemoteApp"
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



# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Valider la VNET Azure à utiliser avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Avant d’utiliser un VNET Azure avec Azure RemoteApp, vous pouvez souhaiter contrôler le VNET. Cela permet d’éviter les problèmes de connectivité.

Pour valider votre VNET d’Azure, effectuez les opérations suivantes :

1. Créer un ordinateur virtuel Azure dans le sous-réseau de le VNET Azure que vous souhaitez utiliser avec Azure RemoteApp.

2. Se connecter à cet ordinateur virtuel à l’aide de l’option **se connecter** dans le portail de gestion.
3. Joindre l’ordinateur virtuel dans le même domaine que vous souhaitez utiliser avec Azure RemoteApp. Si vous créez une collection hybride qui se connecte à votre réseau local, joindre l’ordinateur virtuel à votre domaine local.

Si l’opération réussit, le VNET Azure est prêt à être utilisé avec RemoteApp.

Pour plus d’informations sur le flux de travail collection hybride de bout en bout, consultez les articles suivants :

- [Comment planifier votre réseau virtuel Azure RemoteApp](remoteapp-planvnet.md)
- [Créer une collection hybride](remoteapp-create-hybrid-deployment.md)
- [Déployer à Azure RemoteApp à votre réseau virtuel d’Azure (avec prise en charge pour ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)
