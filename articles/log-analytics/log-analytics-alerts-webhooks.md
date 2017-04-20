<properties
   pageTitle="Exemple de journal Analytique webhook alerte"
   description="L’une des actions que vous pouvez exécuter en réponse à une alerte de journal Analytique est un *webhook*, qui vous permet d’appeler un processus externe via une requête HTTP unique. Cet article décrit un exemple de création d’une action de webhook une alerte de journal Analytique à l’aide de la marge."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="webhooks-in-log-analytics-alerts"></a>Webhooks dans les alertes de journal Analytique

L’une des actions que vous pouvez exécuter en réponse à une [alerte de journal Analytique](log-analytics-alerts.md) est un *webhook*, qui vous permet d’appeler un processus externe via une requête HTTP unique.  Vous pouvez lire les détails des alertes et webhooks dans les [alertes dans le journal Analytique](log-analytics-alerts.md)

Dans cet article, nous traiterons un exemple de création d’une action de webhook une alerte de journal Analytique à l’aide d’une marge qui est un service de messagerie.

>[AZURE.NOTE] Vous devez avoir un compte de marge pour exécuter cet exemple.  Vous pouvez vous inscrire pour un compte gratuit à [slack.com](http://slack.com).

## <a name="step-1---enable-webhooks-in-slack"></a>Étape 1 : activer les webhooks dans la marge
2.  Connectez-vous à la marge à [slack.com](http://slack.com).
3.  Dans la section des **canaux** dans le volet gauche, sélectionnez un canal.  C’est le canal qui sera envoyé pour le message.  Vous pouvez sélectionner un des canaux par défaut par exemple **Général** ou **aléatoire**.  Dans un scénario de production, vous créerez probablement un canal spécial tel que **criticalservicealerts**. <br>

    ![Canaux de marge](media/log-analytics-alerts-webhooks/oms-webhooks01.png)

3. Cliquez sur **Ajouter une application ou une intégration personnalisée** pour ouvrir le répertoire de l’application.
3.  Tapez *webhooks* dans la zone Rechercher, puis sélectionnez **WebHooks entrants**. <br>

    ![Canaux de marge](media/log-analytics-alerts-webhooks/oms-webhooks02.png)

4.  Le nom de votre équipe, cliquez sur **installer** .
5.  Cliquez sur **Ajouter une Configuration**.
6.  Sélectionnez le canal que vous allez utiliser pour cet exemple, puis cliquez sur **Ajouter un entrant WebHooks intégration**.  
6. Copiez l' **URL de Webhook**.  Vous allez coller cela dans la configuration des alertes. <br>

    ![Canaux de marge](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Étape 2 : créer une règle d’alerte dans le journal Analytique
1.  [Créer une règle d’alerte](log-analytics-alerts.md) avec les paramètres suivants.
    - Requête :```    Type=Event EventLevelName=error ```
    - Recherchez cette alerte chaque : 5 minutes
    - Le nombre de résultats est : supérieur à 10
    - Dans cette fenêtre de temps : 60 minutes
    - Sélectionnez **Oui** pour **Webhook** et **non** pour les autres actions.
7. Collez l’URL de la marge dans le champ **URL de Webhook** .
8. Sélectionnez l’option pour **inclure une charge JSON personnalisée**.
9. La marge attend une charge utile au format JSON avec un paramètre nommé « *text*».  C’est le texte qu’il affichera dans le message qu’il crée.  Vous pouvez utiliser une ou plusieurs des paramètres d’alerte à l’aide de la *#* de symboles telles que dans l’exemple suivant.

    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```

    ![charge utile JSON d’exemple](media/log-analytics-alerts-webhooks/oms-webhooks07.png)

9.  Cliquez sur **Enregistrer** pour enregistrer la règle d’alerte.

10. Temps d’attente suffisant pour une alerte être créé puis vérifiez la marge pour un message qui sera semblable à la suivante.

    ![webhook exemple de marge](media/log-analytics-alerts-webhooks/oms-webhooks08.png)


### <a name="advanced-webhook-payload-for-slack"></a>Advanced webhook charge de marge

Vous pouvez personnaliser largement les messages entrants avec une marge. Pour plus d’informations, consultez [Webhooks entrant](https://api.slack.com/incoming-webhooks) sur le site Web de marge. Voici une surcharge plus complexe pour créer un message enrichi avec mise en forme :

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


Cela génère un message dans une marge semblable à la suivante.

![exemple de message dans la marge](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Résumé

Avec cette règle d’alerte en place, vous aurez un message envoyé à une marge chaque fois que les critères sont remplis.  

Il s’agit d’un seul exemple d’action que vous pouvez créer en réponse à une alerte.  Vous pouvez créer une action webhook qui appelle un autre service externe, une action de procédure opérationnelle pour démarrer une procédure opérationnelle dans Azure Automation ou une action de courrier électronique pour envoyer un courrier pour vous-même ou d’autres destinataires.   

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur plus sur les [alertes dans le journal Analytique](log-analytics-alerts.md) , y compris d’autres actions.
- [Procédures opérationnelles de créer dans Azure Automation](../automation/automation-webhooks.md) qui peut être appelée à partir d’un webhook.
