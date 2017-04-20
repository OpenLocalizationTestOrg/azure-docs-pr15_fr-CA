<properties
   pageTitle="Naviguez et sélectionnez les images de machine virtuelle de Windows | Microsoft Azure"
   description="Apprenez à déterminer l’éditeur, offre et point de stock pour les images lors de la création d’une machine virtuelle Windows avec le modèle de déploiement du Gestionnaire de ressources."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell-or-the-cli"></a>Naviguer et sélectionner des images de machine virtuelle Windows dans Azure avec PowerShell ou l’interface CLI

Cette rubrique décrit comment trouver les éditeurs d’image de machine virtuelle, offres, références et versions pour chaque emplacement dans lequel vous pouvez déployer. Pour donner un exemple, certaines images de machine virtuelle de Windows couramment utilisés sont les suivants :

## <a name="table-of-commonly-used-windows-images"></a>Table d’images de Windows couramment utilisés


| PublisherName                        | Offre                                 | Point de stock                         |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| MicrosoftDynamicsNAV             | DynamicsNAV                                | 2015                             |
| MicrosoftSharePoint              | MicrosoftSharePointServer                  | 2013                             |
| MicrosoftSQLServer               | SQL2014-WS2012R2                           | Entreprise-optimisé-pour-DW      |
| MicrosoftSQLServer               | SQL2014-WS2012R2                           | Entreprise-optimisé-pour-OLTP    |
| MicrosoftWindowsServer           | Windows2003 Server Edition                              | 2012 R2-centre de données                  |
| MicrosoftWindowsServer           | Windows2003 Server Edition                              | 2012-centre de données               |
| MicrosoftWindowsServer           | Windows2003 Server Edition                              | 2008-R2-SP1 |
| MicrosoftWindowsServer           | Windows2003 Server Edition                              | Serveur Windows-Aperçu technique |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials                    | WindowsServerEssentials          |
| MicrosoftWindowsServerHPCPack    | WindowsServerHPCPack                       | 2012R2                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]
