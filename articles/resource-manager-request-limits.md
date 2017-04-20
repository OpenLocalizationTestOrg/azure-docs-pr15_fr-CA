<properties
   pageTitle="Limites de demande de gestionnaire de ressources Azure | Microsoft Azure"
   description="Décrit comment faire pour utiliser la limitation avec le Gestionnaire de ressources Azure demandes lorsque la limite d’abonnements a été atteint."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="tomfitz"/>

# <a name="throttling-resource-manager-requests"></a>La limitation de requêtes du Gestionnaire de ressources

Pour chaque abonnement et les clients, les limites de gestionnaire de ressources requêtes de lecture à 15 000 par heure et demandes d’écriture à 1 200 par heure. Si votre application ou le script atteint ces limites, vous devez limiter vos demandes. Cette rubrique vous indique comment déterminer les autres demandes que vous avez avant d’atteindre la limite et comment réagir si vous avez atteint la limite.

Lorsque vous atteignez la limite, vous recevez le code d’état HTTP **429 trop grand nombre de demandes**.

Le nombre de demandes est portée à votre abonnement ou de vos clients. Si vous avez plusieurs, plusieurs applications effectuant des demandes dans votre abonnement, les demandes de ces applications sont ajoutés pour déterminer le nombre de demandes restantes.

Demandes d’abonnement portée sont celles du Impliquez en passant votre id d’abonnement, telles que l’extraction de la ressource groupes dans votre abonnement. Les demandes de clients portée n’incluent pas votre id d’abonnement, telles que l’extraction des emplacements Azure valides.

## <a name="remaining-requests"></a>Autres demandes

Vous pouvez déterminer le nombre de demandes restantes en examinant les en-têtes de réponse. Chaque demande contient les valeurs pour le nombre de requêtes d’écriture et de lecture restant. Le tableau suivant décrit les en-têtes de réponse que vous pouvez examiner ces valeurs :

| En-tête de réponse | Description |
| --------------- | ----------- |
| x-MS-ratelimit-Remaining-Subscription-reads | Abonnement portée lit restant |
| x-MS-ratelimit-Remaining-Subscription-Writes | Abonnement portée écrit restant |
| x-MS-ratelimit-Remaining-tenant-reads | Portée des clients lit restant |
| x-MS-ratelimit-Remaining-tenant-Writes | Portée des clients écrit restant |
| x-MS-ratelimit-Remaining-Subscription-Resource-Requests | Abonnement portée des demandes de type de ressources restantes.<br /><br />Cette valeur d’en-tête est uniquement renvoyée si un service a remplacé la limite par défaut. Le Gestionnaire de ressources ajoute cette valeur au lieu de l’abonnement lectures ou écritures. |
| x-MS-ratelimit-Remaining-Subscription-Resource-Entities-Read | Abonnement portée des demandes de collection de type de ressources restantes.<br /><br />Cette valeur d’en-tête est uniquement renvoyée si un service a remplacé la limite par défaut. Cette valeur fournit le nombre de demandes de collecte des restants (ressources de la liste). |
| x-MS-ratelimit-Remaining-tenant-Resource-Requests | Demandes de type de ressources clients portée restant.<br /><br />Cet en-tête est ajouté uniquement pour les demandes au niveau des clients, et uniquement si un service a remplacé la limite par défaut. Le Gestionnaire de ressources ajoute cette valeur au lieu du locataire lectures ou écritures. |
| x-MS-ratelimit-Remaining-tenant-Resource-Entities-Read | Clients portée des demandes de collection de type de ressources restantes.<br /><br />Cet en-tête est ajouté uniquement pour les demandes au niveau des clients, et uniquement si un service a remplacé la limite par défaut. |

## <a name="retrieving-the-header-values"></a>Récupérer les valeurs d’en-tête

La récupération de ces valeurs d’en-tête dans votre code ou un script n’est pas différente de celle de la récupération de n’importe quelle valeur d’en-tête. 

Par exemple, en **C#**, vous récupérez la valeur d’en-tête d’un objet **HttpWebResponse** nommé **réponse** avec le code suivant :

    response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)

Dans **PowerShell**, vous récupérez la valeur de l’en-tête d’une opération Invoke-WebRequest.

    $r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
    $r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
    
Ou, pour voir les autres demandes pour le débogage, vous pouvez fournir le **-Debug** paramètre dans votre applet de commande **PowerShell** .

    Get-AzureRmResourceGroup -Debug
    
Qui renvoie beaucoup d’informations, y compris la valeur de la réponse suivante :

    ...
    DEBUG: ============================ HTTP RESPONSE ============================

    Status Code:
    OK

    Headers:
    Pragma                        : no-cache
    x-ms-ratelimit-remaining-subscription-reads: 14999
    ...

Dans **Azure CLI**, vous récupérez la valeur d’en-tête à l’aide de l’option la plus détaillée.

    azure group list -vv --json

Qui renvoie beaucoup d’informations, y compris l’objet suivant :

    ...
    silly: returnObject
    {
      "statusCode": 200,
      "header": {
        "cache-control": "no-cache",
        "pragma": "no-cache",
        "content-type": "application/json; charset=utf-8",
        "expires": "-1",
        "x-ms-ratelimit-remaining-subscription-reads": "14998",
        ...

## <a name="waiting-before-sending-next-request"></a>Attente avant d’envoyer la demande suivante

Lorsque vous atteignez la limite de demande, le Gestionnaire de ressources renvoie le code d’état **429** HTTP et une **Nouvelle tentative après** la valeur dans l’en-tête. La **Nouvelle tentative après** valeur spécifie le nombre de secondes d’attente de votre application (ou mise en veille) avant d’envoyer la demande suivante. Si vous envoyez une demande avant la valeur de nouvelle tentative écoulé, votre demande n’est pas traitée et une nouvelle valeur de la nouvelle tentative est retournée.
