<properties
   pageTitle="Créer un conteneur de service cloud avec PowerShell | Microsoft Azure"
   description="Cet article explique comment créer un conteneur de service cloud avec PowerShell. Le conteneur héberge les rôles web et worker."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="cawa"/>

# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Une commande PowerShell d’Azure permet de créer un conteneur de service cloud vide
Cet article explique comment créer rapidement un conteneur de Services en nuage à l’aide des applets de commande PowerShell d’Azure. Veuillez suivre les étapes ci-dessous :

1. Installez l’applet de commande PowerShell de Microsoft Azure à partir de la page de [téléchargements de PowerShell d’Azure](http://aka.ms/webpi-azps) .
2. Ouvrez l’invite de commande PowerShell.
3. L' [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) permet de vous connecter.

    > [AZURE.NOTE] Pour obtenir plus d’informations sur l’installation de l’applet de commande PowerShell de Azure et la connexion à votre abonnement Azure, reportez-vous à [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

4. Pour créer un conteneur de service cloud d’Azure vide, utilisez l’applet de commande **New-AzureService** .

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Suivez cet exemple pour appeler l’applet de commande :
```
New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
```

Pour plus d’informations sur la création du service de cloud Azure, exécutez :
```
Get-help New-AzureService
```

### <a name="next-steps"></a>Étapes suivantes

 * Pour gérer le déploiement de service cloud, consultez les commandes [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [AzureService-Remove](https://msdn.microsoft.com/library/azure/dn495120.aspx)et [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) . Pour plus d’informations, vous pouvez également faire référence à la [procédure de configuration des services en nuage](cloud-services-how-to-configure.md) .

 * Pour publier votre projet de service cloud sur Azure, reportez-vous à l’exemple de code **PublishCloudService.ps1** de [livraison continue de service cloud dans Azure](cloud-services-dotnet-continuous-delivery.md).
