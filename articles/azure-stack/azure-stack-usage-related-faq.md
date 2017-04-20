<properties
    pageTitle="Questions fréquentes relatives à l’utilisation de | Microsoft Azure"
    description="Liste des compteurs de la pile d’Azure, comparaison avec les API d’utilisation Azure, utilisation et temps signalé, codes d’erreur."
    services="azure-stack"
    documentationCenter=""
    authors="AlfredoPizzirani"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="alfredop"/>

# <a name="azure-stack-usage-api-faqs"></a>FAQ d’utilisation des API de pile Azure
Cet article répond à certaines questions fréquemment posées sur l’API de l’utilisation de pile Azure.

## <a name="what-meter-ids-can-i-see"></a>L’ID de compteur puis-je voir ?

Actuellement, l’utilisation est signalée pour le réseau, de stockage et les fournisseurs de ressources de calcul.

| **Fournisseur de ressources** | **ID compteur** |**Nom du compteur** | **Unité** | **Infos supplémentaires** |
| --------------------------- | --------------------------------------- | -------------------------- | ---------------------------- | ----------------------------------------- |
| **Réseau** | f114cb19-ea64-40b5-bcd7-aee474b62853 | Utilisation d’adresses IP publiques | Adresse IP |                    
| **Stockage**  | B4438D5D-453B-4EE1-B42A-DC72E377F1E4 | TableCapacity | Go\*heures | Capacité totale consommée par les tables |
|              | B5C15376-6C94-4FDD-B655-1A69D138ACA3 | PageBlobCapacity | Go\*heures | Capacité totale consommée par les objets BLOB de page |
|              | B03C6AE7-B080-4BFA-84A3-22C800F315C6 | QueueCapacity  | Go\*heures  | Capacité totale consommée par la file d’attente |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 | BlockBlobCapacity | Go\*heures  | Capacité totale consommée par les objets BLOB de bloc |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 | TableTransactions  | Nombre de demandes de 10,000s   | Demandes de service de table (dans 10,000s) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D | TableDataTransIn | Données de pénétration dans Go | Entrée de données de service de table en Go |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 | TableDataTransOut | Outgress en Go | Sortie de données de service de table en Go |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 | BlobTransactions | Nombre de demandes de 10,000s | Demandes de service d’objet BLOB (dans 10,000s) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810   | BlobDataTransIn    | Données de pénétration dans Go          | Entrée de données de service de BLOB dans Go 
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8   | BlobDataTransOut   | Outgress en Go              | Sortie de données BLOB service dans Go 
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA   | QueueTransactions  | Nombre de demandes de 10,000s   | File d’attente des demandes de service (dans 10,000s) 
| | E518E809-E369-4A45-9274-2017B29FFF25   | QueueDataTransIn          | Données de pénétration dans Go         | Entrée de données de service de file d’attente dans Go 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2   | QueueDataTransOut         | Outgress en Go  | Sortie de données de service de file d’attente dans Go 
| **Calculer** | 6DAB500F-A4FD-49C4-956D-229BB9C8C793 | Heures de taille de machine virtuelle | Taille de la machine virtuelle |



## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Comment les API de l’utilisation de pile Azure par rapport à l' [API de l’utilisation d’Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (actuellement en version d’évaluation) ?

-   L’API de l’utilisation de clients est cohérente avec l’API d’Azure, à une exception près : l’indicateur *showDetails* actuellement n'est pas pris en charge dans la pile d’Azure.

-   L’API du fournisseur de l’utilisation s’applique uniquement à la pile d’Azure.

-   Actuellement, l' [API de RateCard](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) qui est disponible dans Azure n’est pas disponible dans la pile d’Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Quelle est la différence entre le temps d’utilisation et a signalé le temps ?

Rapports d’utilisation des données ont deux valeurs de temps principale :

-   **Le temps déclaré**. Lorsque l’événement utilisation entrée du système d’utilisation

-   **Temps d’utilisation**. Le temps lorsque la ressource de la pile d’Azure a été consommée

Vous pouvez voir une différence dans les valeurs de temps d’utilisation et a signalé une fois pour un événement spécifique de l’utilisation. Le délai peut être aussi long que plusieurs heures dans n’importe quel environnement.

Actuellement, vous pouvez interroger *uniquement par a signalé un temps*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Que signifient ces codes d’erreur des API de l’utilisation ?

| **Code d’état HTTP** | **Code d’erreur** | **Description** |
| ---------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Demande incorrecte dans laquelle/400        | *NoApiVersion*     | Le paramètre de *version de l’api* de requête est manquant.
| Demande incorrecte dans laquelle/400        | *InvalidProperty*  | Une propriété est manquante ou a une valeur non valide. Le message dans le code d’erreur dans le corps de réponse identifie la propriété manquante.
| Demande incorrecte dans laquelle/400        | *RequestEndTimeIsInFuture*  | La valeur de *ReportedEndTime* est dans le futur. À l’avenir les valeurs ne sont pas autorisées pour cet argument.
| Demande incorrecte dans laquelle/400        | *SubscriberIdIsNotDirectTenant*    | Un appel de l’API de fournisseur utilisé un ID d’abonnement qui n’est pas un client valide de l’appelant.
| Demande incorrecte dans laquelle/400        | *SubscriptionIdMissingInRequest*   | L’ID d’abonnement de l’appelant est manquant.
| Demande incorrecte dans laquelle/400        | *InvalidAggregationGranularity*   | Une granularité d’agrégation non valide a été demandée. Les valeurs valides sont tous les jours et horaires.
| 503                    | *ServiceUnavailable*   | Une erreur récupérable s’est produite car le service est occupé ou de l’appel est limité. |

## <a name="next-steps"></a>Étapes suivantes
[Facturation client et refacturation dans la pile d’Azure](azure-stack-billing-and-chargeback.md)

[Utilisation des ressources API fournisseur](azure-stack-provider-resource-api.md)

[L’utilisation des ressources API de client](azure-stack-tenant-resource-usage-api.md)
