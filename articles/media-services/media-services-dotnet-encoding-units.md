<properties 
    pageTitle="Comment faire pour ajouter des unités de codage" 
    description="Découvrez comment l’ajout d’unités de codage avec .NET"  
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016"
    ms.author="juliako;milangada;gtrifonov"/>


#<a name="how-to-scale-encoding-with-net-sdk"></a>Comment faire pour mettre à l’échelle de codage avec le Kit de développement .NET

> [AZURE.SELECTOR]
- [Portail](media-services-portal-scale-media-processing.md )
- [.NET](media-services-dotnet-encoding-units.md)
- [RESTE](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Vue d’ensemble

>[AZURE.IMPORTANT] Veillez à consulter la rubrique de [vue d’ensemble](media-services-scale-media-processing-overview.md) pour obtenir plus d’informations sur la mise à l’échelle de traitement rubrique de support.
 
Pour modifier le type d’unité réservé et le nombre d’unités réservées à l’aide du Kit de développement .NET de codage, effectuez les opérations suivantes :

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
    
    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();
    
    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##<a name="opening-a-support-ticket"></a>Ouverture d’un Ticket de Support

Par défaut, chaque compte de Services de support peut évoluer jusqu'à 25 le codage et le 5 On-Demand Streaming réservé unités. Vous pouvez demander une limite plus élevée en ouvrant un ticket de support.

###<a name="open-a-support-ticket"></a>Ouvrir un ticket de support

Pour ouvrir une prise en charge de ticket effectuez les opérations suivantes :

1. Cliquez sur [obtenir de l’aide](https://manage.windowsazure.com/?getsupport=true). Si vous n’êtes pas connecté, vous êtes invité à entrer vos informations d’identification.

1. Sélectionnez votre abonnement.

1. Sous type de prise en charge, sélectionnez « Techniques ».

1. Cliquez sur « Créer le Ticket ».

1. Sélectionnez « Azure Media Services » dans la liste des produits présentés sur la page suivante.

1. Sélectionnez un type de problème « » qui est appropriée pour votre problème.

1. Cliquez sur Continuer.

1. Suivez les instructions affichées sur la page suivante et entrez les informations concernant votre problème.

1. Cliquez sur Envoyer pour ouvrir le ticket.



##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
