<properties
    pageTitle="Réessayer la logique dans le Kit de développement de Services de support pour .NET | Microsoft Azure"
    description="La rubrique fournit une vue d’ensemble de la logique de nouvelle tentative dans le Kit de développement de Services de support pour .NET."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>


# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Réessayer la logique dans le Kit de développement de Services de support pour .NET

Lorsque vous travaillez avec les services Microsoft Azure, les défaillances temporaires peuvent se produire. Si une erreur passagère se produit, dans la plupart des cas, après plusieurs tentatives, que l’opération réussit. Le Kit de développement de Services de support pour .NET implémente la logique des nouvelles tentatives afin de gérer les défaillances temporaires associés liés des exceptions et des erreurs provoquées par des requêtes web, l’exécution de requêtes, l’enregistrement des modifications et les opérations de stockage.  Par défaut, le Kit de développement de Services de support pour .NET exécute quatre tentatives avant de lever à nouveau l’exception à votre application. Le code de votre application doit ensuite gérer cette exception correctement.  
  
 Voici une brève orientation de stratégies de requête Web, stockage, requête et SaveChanges :  
  
-   La stratégie de stockage est utilisée pour les opérations de stockage blob (téléchargements ou le téléchargement de fichiers de ressources).  
  
-   La stratégie de demande de Web est utilisée pour les demandes web générique (par exemple, pour l’obtention d’un jeton d’authentification et de résoudre le point de terminaison du cluster aux utilisateurs).  
  
-   La stratégie de requête permet d’interroger des entités à partir d’autres (par exemple, mediaContext.Assets.Where(...)).  
  
-   La stratégie de SaveChanges est utilisée pour faire tout ce que les modifications des données au sein du service (par exemple, la création d’une entité à une entité, l’appel d’une fonction de service d’une opération de mise à jour).  
  
 Cette rubrique répertorie les types d’exceptions et des codes d’erreur qui sont gérés par le Kit de développement de Services de support pour .NET réessayer logique.  
  
## <a name="exception-types"></a>Types d’exception  

Le tableau suivant décrit les exceptions qui le Media Services SDK pour .NET gère ou ne gère pas pour certaines opérations qui peuvent provoquer des défaillances temporaires.  
  
Exception|Requête Web|Stockage|Requête|SaveChanges
----|------|----|---|---
WebException<br/>Pour plus d’informations, reportez-vous à la section [codes de statut de WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) .|Oui|Oui|Oui|Oui  
DataServiceClientException<br/> Pour plus d’informations, consultez [codes d’état HTTP Erreur](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|N°|Oui|Oui|Oui
DataServiceQueryException<br/> Pour plus d’informations, consultez [codes d’état HTTP Erreur](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|N°|Oui|Oui|Oui  
DataServiceRequestException<br/> Pour plus d’informations, consultez [codes d’état HTTP Erreur](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|N°|Oui|Oui|Oui  
DataServiceTransportException|N°|N°|Oui|Oui
Exception TimeoutException|Oui|Oui|Oui|N°
SocketException|Oui|Oui|Oui|Oui  
StorageException|N°|Oui|N°|N° 
IOException|N°|Oui|N°|N°
  
###  <a name="WebExceptionStatus"></a>Codes d’état WebException  

Le tableau suivant présente les codes d’erreur WebException est mis en œuvre la logique des nouvelles tentatives. L’énumération [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) définit les codes d’état.  
  
État|Requête Web|Stockage|Requête|SaveChanges  
-----|-----------------|-------------|-----------|----------  
ConnectFailure|Oui|Oui|Oui|Oui
NameResolutionFailure|Oui|Oui|Oui|Oui  
ProxyNameResolutionFailure|Oui|Oui|Oui|Oui  
SendFailure|Oui|Oui|Oui|Oui
PipelineFailure|Oui|Oui|Oui|N°  
ConnectionClosed|Oui|Oui|Oui|N°  
KeepAliveFailure|Oui|Oui|Oui|N°  
UnknownError|Oui|Oui|Oui|N° 
ReceiveFailure|Oui|Oui|Oui|N°  
RequestCanceled|Oui|Oui|Oui|N°  
Délai d’attente|Oui|Oui|Oui|N°
ProtocolError <br/>La nouvelle tentative de le ProtocolError est contrôlée par la manipulation de code d’état HTTP. Pour plus d’informations, consultez [codes d’état HTTP Erreur](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Oui|Oui|Oui|Oui|  
  
###  <a name="HTTPStatusCode"></a>Codes d’état HTTP Erreur  

Lorsque les opérations de requête et SaveChanges lever DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, le code d’état HTTP erreur est retourné dans la propriété StatusCode.  Le tableau suivant présente les codes d’erreur est mis en œuvre la logique des nouvelles tentatives.  
  
 
État|Requête Web|Stockage|Requête|SaveChanges 
---|----|----|----|----
401|N°|Oui|N°|N°
403|N°|Oui<br/>Tentatives de gestion avec les attentes de plus de temps.|N°|N°  
408|Oui|Oui|Oui|Oui
429|Oui|Oui|Oui|Oui  
500|Oui|Oui|Oui|N°  
502|Oui|Oui|Oui|N°  
503|Oui|Oui|Oui|Oui  
504|Oui|Oui|Oui|N°  
  
Si vous souhaitez prendre un coup de œil à l’implémentation réelle de Media Services SDK pour .NET logique des nouvelles, consultez [azure sdk de media services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
