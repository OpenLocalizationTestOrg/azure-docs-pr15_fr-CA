<properties
    pageTitle="L’utilisation des ressources API fournisseur | Microsoft Azure"
    description="Référence pour l’utilisation des ressources API, laquelle récupérer des informations sur l’utilisation de pile d’Azure."
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

# <a name="provider-resource-usage-api"></a>Utilisation des ressources API fournisseur

Le fournisseur de terme s’applique à l’administrateur de service et tous les fournisseurs de délégué. Administrateurs du service et des fournisseurs de délégué permet l’API du fournisseur de l’utilisation pour permet d’afficher l’utilisation de leurs clients directs. Par exemple, P0 peuvent appeler l’API fournisseur pour obtenir des informations sur l’utilisation directe de P1 et de P2 utilisation et P1 peut appeler pour obtenir des informations sur P3 et P4.

![Modèle conceptuel d’une hiérarchie de fournisseur](media/azure-stack-provider-resource-api/image1.png)


## <a name="api-call-reference"></a>Référence de l’appel API

### <a name="request"></a>Demande

La demande Obtient des informations de consommation pour les abonnements demandées et pour la période demandée. Il n’y a pas de corps de requête.

L’utilisation de cette API est une API fournisseur, afin que l’appelant doit être affecté à un rôle de propriétaire, collaborateur ou lecteur dans les abonnement du fournisseur.

| **Méthode**  | **URI de la demande** |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|  Télécharger        | https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Arguments

| **Argument**              | **Description** |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| *armendpoint*             | Azure endpoint de gestionnaire de ressources de votre environnement de pile d’Azure. La convention de la pile d’Azure est le nom du point de terminaison ARM dans le format https://api. {nom de domaine}'. Par exemple, si le nom de domaine est azurestack.local, le point de terminaison ARM sera https://api.azurestack.local. |
| *subId*                   | ID de l’abonnement de l’utilisateur qui effectue l’appel. |
| *reportedStartTime*       | Heure de début de la requête. La valeur de *DateTime* doit être au format UTC et au début de l’heure, par exemple, 13:00. Pour l’agrégation de tous les jours, cette valeur d’UTC minuit. Le format est *une séquence d’échappement* ISO 8601, par exemple, 2015-06-16T18 % 3a53 % 3a11 % 2b00 % 3a00Z, où le signe deux-points sont une séquence d’échappement pour % 3 et plus est échappé en % 2 b afin qu’il soit convivial d’URI. |
| *reportedEndTime*         | Heure de fin de la requête. Les contraintes qui s’appliquent à *reportedStartTime* s’appliquent également à cet argument. La valeur de *reportedEndTime* ne peut pas être dans le futur. |
| *aggregationGranularity*  | Paramètre optionnel qui possède deux valeurs possibles discrètes : tous les jours et toutes les heures. Comme le suggèrent les valeurs, une renvoie les données de granularité quotidienne, et l’autre est une résolution toutes les heures. L’option quotidien est la valeur par défaut. |
| *subscriberId*            | ID d’abonnement. Pour obtenir les données filtrées, l’ID d’abonnement d’un client direct du fournisseur est obligatoire. Si aucun paramètre d’ID de souscription n’est spécifié, l’appel retourne les données d’utilisation pour les locataires direct tous du fournisseur. |
| *version de l’API*             | Version du protocole qui est utilisé pour effectuer cette demande. Vous devez utiliser 2015-06-01-aperçu. |
| *continuationToken*       | Jeton récupéré depuis le dernier appel au fournisseur de l’utilisation des API. Ceci est nécessaire lorsqu’une réponse est supérieure à 1 000 lignes. C’est le signet de l’état d’avancement. Si celui-ci est absent, les données sont récupérées à partir du début de la journée ou heure, en fonction de la granularité passé. |



### <a name="response"></a>Réponse

OBTENIR des /subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00 % 3a00 % 3a00 % 2b00 % 3a00 & aggregationGranularity = tous les jours & subscriberId = sub1.1 & version de l’api = 1.0

{

« valeur » :\[

{

« id » : « /subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1 »,

« nom » : « sub1.1-meterID1 »,

« type » : « Microsoft.Commerce/UsageAggregate »,

« propriétés » : {

« subscriptionId » : « sub1.1 »,

« usageStartTime » : « 2015-03-03T00:00:00 + 00:00 »,

« usageEndTime » : « 2015-03-04T00:00:00 + 00:00 »,

« instanceData » : "{\\» Microsoft.Resources\\« : {\\« ResourceURI des services Web\\« :\\» resourceUri1\\«,\\« emplacement\\

« :\\« Alaska\\«,\\« balises\\« : null,\\« additionalInfo\\« : null}} »,

:2.4000000000 de la « quantité »,

« meterId » : « meterID1 »

}

},

…

### <a name="response-details"></a>Détails de la réponse


| **Argument**       | **Description**
| ------------------ | ------------------------------------------------------------------------------------------------------------- |
| *ID*               | ID unique de l’agrégat d’utilisation
| *nom*             | Nom de l’agrégat d’utilisation
| *type de*             | Définition de la ressource
| *subscriptionId*   | Identificateur d’abonnement de l’utilisateur de la pile d’Azure
| *usageStartTime*   | Heure UTC heure de début de la plage d’utilisation à laquelle appartient cet agrégat d’utilisation
| *usageEndTime*     | Heure de fin UTC de la plage d’utilisation à laquelle appartient cet agrégat d’utilisation
| *instanceData*     | Paires clé-valeur des détails de l’instance (dans un nouveau format) :<br> *resourceUri des services Web*: complet sur les ID de ressource, qui inclut les groupes de ressources et le nom de l’instance <br> *emplacement*: région dans laquelle ce service a été exécuté. <br> *balises*: les balises de ressources qui sont spécifiées par l’utilisateur <br> *additionalInfo*: plus d’informations sur la ressource consommée, par exemple, type d’image ou de la version du système d’exploitation |
| *quantité*         | Montant de la consommation des ressources qui se sont produites dans ce laps de temps |
| *meterId*          | ID unique de la ressource qui a été utilisé (également appelé *ResourceID*) |

## <a name="next-steps"></a>Étapes suivantes

[Utilisation des ressources client référence sur l’API](azure-stack-tenant-resource-usage-api.md)

[Forum aux questions sur liés à l’utilisation](azure-stack-usage-related-faq.md)
