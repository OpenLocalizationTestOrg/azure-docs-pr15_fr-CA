<properties
   pageTitle="Gérer les enregistrements DNS inverses pour vos services Azure (classique) à l’aide de PowerShell | Microsoft Azure"
   description="Comment gérer les enregistrements DNS inversées ou enregistrements PTR pour les services Azure à l’aide de PowerShell dans le modèle de déploiement classique. "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Comment faire pour gérer les enregistrements DNS inverses pour vos services Azure (classique) à l’aide de PowerShell d’Azure

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validation de l’enregistrement DNS inverse
Pour garantir qu'un tiers ne peut pas créer l’enregistrement DNS inverse, mappage vers des domaines de votre DNS, Azure permet uniquement la création d’un enregistrement DNS inverse, où un des éléments suivants est vrai :

- L’inverse DNS FQDN est le nom du Service Cloud pour lesquels il a été spécifié ou n’importe quel nom de Service en nuage au sein de l’abonnement même par exemple, DNS inversée est « contosoapp1.cloudapp.net. ».
- Le FQDN DNS inverse avant résout le nom ou l’adresse IP du Service Cloud pour lesquels il a été spécifié, ou à n’importe quel nom de Service Cloud ou l’abonnement même par exemple, DNS inversée est « app1.contoso.com ». qui est un alias CName pour contosoapp1.cloudapp.net.

Contrôles de validation effectuent uniquement lorsque la propriété DNS inverse pour un Service en nuage est définie ou modifiée. Validation de réévaluation périodique n’est pas effectuée.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Ajouter inversée DNS aux Services de Cloud existants
Vous pouvez ajouter un enregistrement DNS inverse à un Service Cloud existant à l’aide de l’applet de commande « Set-AzureService » :

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Créer un Service Cloud avec DNS inversée
Vous pouvez ajouter un nouveau Service en nuage avec la propriété DNS inverse spécifiée à l’aide de l’applet de commande « Set-AzureService » :

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Affichage DNS inverse pour les Services en nuage existant
Vous pouvez afficher la valeur configurée pour un Service Cloud existant à l’aide de l’applet de commande « Get-AzureService » :

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Supprimer DNS inversée à partir des Services en nuage existant
Vous pouvez supprimer une propriété DNS inverse d’un Service Cloud existant à l’aide de l’applet de commande « Set-AzureService ». Cela en définissant la valeur de la propriété DNS inversée à vide :

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]
