<properties
   pageTitle="Les exceptions FabricClient levées | Microsoft Azure"
   description="Décrit les exceptions courantes et les erreurs qui peuvent être levées par les APIs FabricClient lors de l’exécution des applications et des opérations de gestion de cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Des exceptions et des erreurs lorsque vous travaillez avec les APIs FabricClient commune
Les API de [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) permettent aux administrateurs de cluster et d’application effectuer des tâches administratives sur un cluster, service ou application Fabric du Service. Par exemple, le déploiement d’applications, mise à niveau et la suppression, la vérification de l’état de santé un cluster, ou test d’un service. Les développeurs d’applications et administrateurs de cluster permet le APIs FabricClient à développer des outils pour gérer le cluster à structure de Service et les applications.

Il existe de nombreux types d’opérations qui peuvent être effectuées à l’aide de FabricClient.  Chaque méthode peut lever des exceptions pour les erreurs liées à l’entrée incorrecte, des erreurs d’exécution ou des problèmes de l’infrastructure en régime transitoire.  Consultez la documentation de référence API pour rechercher les exceptions qui sont levées par une méthode spécifique. Il existe quelques exceptions, toutefois, peuvent être levées par de nombreuses API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) différents. Le tableau suivant répertorie les exceptions qui sont communes à la APIs FabricClient.

|Exception| Levée lorsque|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|L’objet [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) est dans un état fermé. Supprimer l’objet de [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) vous utilisez et instanciez un nouvel objet [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) . |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|L’opération a expiré. [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) est retourné lorsque l’opération prend plus de MaxOperationTimeout pour terminer.|
|[Exception System.UnauthorizedAccessException](https://msdn.microsoft.com/en-us/library/system.unauthorizedaccessexception.aspx)|Échec de la vérification d’accès pour l’opération. E_ACCESSDENIED est renvoyé.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Une erreur d’exécution s’est produite lors de l’exécution de l’opération. Une des méthodes FabricClient susceptibles de lever des [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx), la propriété [ErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) indique la cause exacte de l’exception. Codes d’erreur sont définis dans l’énumération [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) .|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|L’opération a échoué en raison d’une condition d’erreur transitoire d’un certain type. Par exemple, une opération peut échouer, car un quorum de réplicas n’est temporairement pas accessible. Exceptions transitoire correspondent à des opérations ayant échoué peuvent être relancées.|

Certains [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) les erreurs courantes qui peuvent être retournées dans un [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx):

|Erreur| Condition|
|---------|:-----------|
|CommunicationError|Une erreur de communication a provoqué l’opération échoue, recommencez l’opération.|
|InvalidCredentialType|Le type d’informations d’identification n’est pas valide.|
|InvalidX509FindType|Le X509FindType n’est pas valide.|
|InvalidX509StoreLocation|Le X509 magasin n’est pas valide.|
|InvalidX509StoreName|Le X509 nom de la banque d’informations n’est pas valide.|
|InvalidX509Thumbprint|Le X509 chaîne d’empreinte numérique du certificat n’est pas valide.|
|InvalidProtectionLevel|Le niveau de protection n’est pas valide.|
|InvalidX509Store|Le X509 Impossible d’ouvrir le magasin de certificats.|
|InvalidSubjectName|Le nom du sujet est non valide.|
|InvalidAllowedCommonNameList|Le format de chaîne de la liste nom commune n’est pas valide. Il doit être une liste séparée par des virgules.|
