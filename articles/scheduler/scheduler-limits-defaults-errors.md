<properties
 pageTitle="Les paramètres par défaut et les limites du planificateur"
 description="Les paramètres par défaut et les limites du planificateur"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-limits-and-defaults"></a>Les paramètres par défaut et les limites du planificateur

## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Limitations, les limites, les valeurs par défaut et les Quotas du planificateur

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>L’en-tête x-ms-demande-id

Chaque demande faite auprès du service de planificateur renvoie un en-tête de réponse nommé**x-ms--id de la demande**. Cet en-tête contient une valeur opaque qui identifie de manière unique la demande.

Si une demande ne réussit toujours et que vous avez vérifié que la demande est correctement formulée, vous pouvez utiliser cette valeur pour signaler l’erreur à Microsoft. Dans votre rapport, inclure la valeur de x-ms-demande-id, le temps approximatif que la demande a été faite, l’identificateur de l’abonnement, tâche collection et/ou de travail et le type d’opération qui a tenté de la demande.

## <a name="see-also"></a>Voir aussi


 [Quel est le planificateur ?](scheduler-intro.md)

 [Hiérarchie d’entités, terminologie et des concepts du planificateur Azure](scheduler-concepts-terms.md)

 [Mise en route à l’aide du planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Référence des API de reste de planificateur Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence d’applets de commande PowerShell de planificateur Azure](scheduler-powershell-reference.md)

 [Azure planificateur haute disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Azure d’authentification sortante du planificateur](scheduler-outbound-authentication.md)
