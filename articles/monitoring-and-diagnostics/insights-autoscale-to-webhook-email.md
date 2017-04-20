<properties
    pageTitle="Utilisez les actions de l’échelle pour envoyer des notifications d’alerte e-mail et webhook. | Microsoft Azure"
    description="Voir comment autoscale actions permet d’appeler des URL web ou envoyer des notifications par courrier électronique dans le moniteur d’Azure. "
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="ashwink"/>

# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Actions d’échelle automatique permet d’envoyer un e-mail et webhook notifications d’alerte dans le moniteur d’Azure

Cet article explique comment permet de paramétrer les déclencheurs afin que vous pouvez appeler des URL web spécifique ou envoyer des messages électroniques en fonction des actions autoscale dans Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks vous permettent d’acheminer les notifications d’alerte Azure à d’autres systèmes pour les notifications de post-traitement ou personnalisées. Par exemple, l’alerte de routage aux services qui peuvent traiter une requête web entrante pour envoyer des que SMS, journal des bogues, notification services d’une équipe à l’aide de chat ou de messagerie, etc.. L’URI de webhook doit être un point de terminaison HTTP ou HTTPS valide.

## <a name="email"></a>Messagerie
E-mail peut être envoyé à n’importe quelle adresse de messagerie valide. Seront également avertis lorsque des administrateurs et l’abonnement lorsque la règle est en cours d’exécution.


## <a name="cloud-services-and-web-apps"></a>Services en nuage et les applications Web
Vous pouvez opt-in à partir du portail Azure pour les Services en nuage et les batteries de serveurs (applications Web).

- Cliquez sur la mesure de **l’échelle par** .

![mettre à l’échelle](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## <a name="virtual-machine-scale-sets"></a>Jeux d’échelle de Machine virtuelle
Pour les plus récents de Machines virtuelles créées avec le Gestionnaire de ressources (jeux échelle de l’ordinateur virtuel), vous pouvez la configurer à l’aide des API REST, Gestionnaire de ressources modèles, PowerShell et CLI. Une interface de portail n’est pas encore disponible.
Lorsque vous utilisez le modèle de l’API REST ou le Gestionnaire de ressources, inclure l’élément notifications avec les options suivantes.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
|Champ                              |Obligatoire ? |Description|
|---                                |---        |---|
|opération                          |Oui        |la valeur doit être « Echelle »|
|sendToSubscriptionAdministrator    |Oui        |la valeur doit être « true » ou « false »|
|sendToSubscriptionCoAdministrators |Oui        |la valeur doit être « true » ou « false »|
|customEmails                       |Oui        |valeur peut être null [de] ou tableau de chaînes d’e-mails|
|webhooks                           |Oui        |la valeur peut être null ou valide des Uri|
|serviceUri                         |Oui        |un Uri de https valide|
|propriétés                         |Oui        |valeur doit être {} vide ou peut contenir des paires clé-valeur.|


## <a name="authentication-in-webhooks"></a>Authentification dans webhooks
Il existe deux formes d’URI d’authentification :

1. Authentification de base-jeton, où vous enregistrez l’URI de webhook avec un ID de jeton sous la forme d’un paramètre de requête. Par exemple, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Authentification de base, lorsque vous utilisez un ID utilisateur et le mot de passe. Par exemple,https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## <a name="autoscale-notification-webhook-payload-schema"></a>Schéma de charge AutoScale notification webhook
Lorsque la notification autoscale est générée, les métadonnées suivante sont incluses dans la charge utile de webhook :

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|Champ  |Obligatoire ?|    Description|
|---|---|---|
|état |Oui    |L’état qui indique qu’une action d’échelle automatique a été générée.|
|opération| Oui |Pour une augmentation des instances, il sera « Échelle Out » et pour une diminution des instances, il sera « Échelle dans »|
|contexte|   Oui |Le contexte d’action échelle automatique|
|horodatage| Oui |Horodatage de l’action d’échelle automatique a été déclenchée.|
|ID |Oui|   ID du Gestionnaire de ressources du paramètre échelle automatique|
|nom   |Oui|   Le nom du paramètre échelle automatique|
|Détails|   Oui |Explication de l’action qui a pris le service de l’échelle et la modification dans le nombre d’instances|
|subscriptionId|    Oui |ID de la ressource cible qui est mis à l’échelle de l’abonnement|
|resourceGroupName| Oui|    Nom du groupe de ressources de la ressource cible qui est mis à l’échelle|
|nom_ressource   |Oui|   Nom de la ressource cible qui est mis à l’échelle|
|type de ressource   |Oui|   Les trois valeurs de prises en charge : « microsoft.classiccompute/domainnames/slots/roles » - Cloud Service rôles, « microsoft.compute/virtualmachinescalesets » - jeux d’échelle de Machine virtuelle et « Microsoft.Web/serverfarms » - Web App|
|resourceId |Oui|ID du Gestionnaire de ressources de la ressource cible qui est mis à l’échelle|
|portalLink |Oui    |Lien de portail Azure à la page de résumé de la ressource cible|
|oldCapacity|   Oui |Le nombre d’instances (ancienne) actuelle lorsque l’échelle automatique a effectué une action de l’échelle|
|newCapacity|   Oui |Le nombre d’instances de nouvelle échelle mise à l’échelle de la ressource|
|Propriétés|    N°| Facultatif. Ensemble de < clé, valeur > paires (par exemple, Dictionary < String, String >). Le champ de propriétés est facultatif. Dans une interface utilisateur personnalisée ou d’un workflow d’application en fonction de logique, vous pouvez entrer des clés et des valeurs qui peuvent être passées à l’aide de la charge utile. Une autre façon de passer des propriétés personnalisées à l’appel de webhook sortant est d’utiliser la webhook URI lui-même (en tant que paramètres de la requête)|
