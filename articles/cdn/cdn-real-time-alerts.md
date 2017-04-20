<properties
    pageTitle="Alertes en temps réel d’Azure CDN | Microsoft Azure"
    description="Alertes en temps réel dans Microsoft Azure CDN. Alertes en temps réel fournissent des notifications sur les performances des points de terminaison dans votre profil CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="casoper"/>

# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alertes en temps réel dans Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]


## <a name="overview"></a>Vue d’ensemble

Ce document explique les alertes en temps réel dans Microsoft Azure CDN. Cette fonctionnalité fournit des notifications en temps réel sur les performances des points de terminaison dans votre profil CDN.  Vous pouvez configurer e-mail ou alertes HTTP en fonction de :

* Bande passante
* Codes d’état
* Statuts de cache
* Connexions

## <a name="creating-a-real-time-alert"></a>Création d’une alerte en temps réel

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre profil CDN.

    ![Lame de profil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)

2. À partir de la blade de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

3. Pointez sur l’onglet **Analytique** , puis survolez le Lanceur **Les statistiques en temps réel** .  Cliquez sur **alertes en temps réel**.

    ![Portail de gestion CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)

    La liste des configurations d’alerte existantes (le cas échéant) s’affiche.

4. Cliquez sur le bouton **Ajouter une alerte** .

    ![Ajouter le bouton alerte](./media/cdn-real-time-alerts/cdn-add-alert.png)

    Formulaire de création d’une nouvelle mise en garde s’affiche.

    ![Nouveau formulaire d’alerte](./media/cdn-real-time-alerts/cdn-new-alert.png)

5. Si vous souhaitez que cette alerte soit active lorsque vous cliquez sur **Enregistrer**, cochez la case de **l’Alerte est activée** .

6. Dans le champ **nom** , entrez un nom descriptif pour l’alerte.

7. Dans la liste déroulante **Type de média** , sélectionnez **HTTP des objets volumineux**.

    ![Type de média HTTP volumineux l’objet sélectionné](./media/cdn-real-time-alerts/cdn-http-large.png)

    > [AZURE.IMPORTANT] Vous devez sélectionner **l’Objet HTTP** comme le **Type de média**.  Les autres options ne sont pas utilisées par **Azure CDN auprès de Verizon**.  Sélectionnez **l’Objet HTTP** provoquera l’alerte jamais déclenché.

8. Créer une **Expression** à surveiller en sélectionnant une **mesure**, un **opérateur**et une **valeur de déclenchement**.

    - **Métrique**, sélectionnez le type de condition à surveiller.  **Mbits/s de bande passante** est la quantité de bande passante en mégabits par seconde.  **Nombre total de connexions** est le nombre de connexions HTTP simultanées nos serveurs edge.  Pour les définitions des différents statuts de cache et les codes d’état, consultez [Codes d’état Azure CDN du Cache](https://msdn.microsoft.com/library/mt759237.aspx) et les [Codes d’état HTTP Azure CDN](https://msdn.microsoft.com/library/mt759238.aspx)
    - **Opérateur** est l’opérateur mathématique qui établit la relation entre la mesure et la valeur du déclencheur.
    - **Valeur de déclenchement** est la valeur de seuil qui doit être satisfaite avant une notification sera envoyée.

    Dans le sous exemple, l’expression que j’ai créé indique que je souhaite être averti lorsque le nombre de codes d’état 404 est supérieur à 25.

    ![Exemple d’alerte en temps réel d’expression](./media/cdn-real-time-alerts/cdn-expression.png)

9. Pour l' **intervalle**, entrez la fréquence à laquelle vous souhaitez que l’expression évaluée.

10. Dans la liste déroulante **Notify sur** , sélectionnez lorsque vous souhaitez être averti lorsque l’expression est vraie.
    
    - **Condition de démarrage** indique qu’une notification est envoyée lorsque la condition spécifiée est détectée en premier.
    - **Condition de fin** indique qu’une notification est envoyée lorsque la condition spécifiée n’est plus détectée. Cette notification ne peut être déclenchée après que notre réseau de surveillance du système a détecté que la condition spécifiée s’est produite.
    - **Continue** indique qu’une notification sera envoyée chaque fois que le système de surveillance réseau détecte la condition spécifiée. Gardez à l’esprit que le système de surveillance réseau vérifie uniquement une fois par intervalle de la condition spécifiée.
    - **Condition de début et fin** indique qu’une notification sera envoyée la première fois que la condition spécifiée est détectée et qu’une seule fois à nouveau lorsque la condition n’est plus détectée.

11. Si vous souhaitez recevoir des notifications par courrier électronique, cochez la case de la **notification par courrier électronique** .  

    ![Notifier par formulaire électronique](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    Dans le champ **à** , entrez l’adresse e-mail que vous où vous souhaitez que les notifications envoyées. Pour **l’objet** et le **corps**, vous pouvez laisser la valeur par défaut, ou vous pouvez personnaliser le message à l’aide de la liste de **mots clés disponibles** pour insérer dynamiquement les données d’alerte lorsque le message est envoyé.

    > [AZURE.NOTE] Vous pouvez tester la notification par e-mail en cliquant sur le bouton **Tester la Notification** , mais uniquement après que la configuration d’alerte a été enregistrée.

12. Si vous souhaitez que des notifications à valider sur un serveur web, cochez la case de **notifier par HTTP Post** .

    ![Notifier par la publication HTTP](./media/cdn-real-time-alerts/cdn-notify-http.png)

    Dans le champ **Url** , entrez l’URL où vous souhaitez que le message HTTP enregistrée. Dans la zone **en-têtes** , entrez les en-têtes HTTP à envoyer dans la demande.  Pour le **corps** , vous pouvez personnaliser le message à l’aide de la liste de **mots clés disponibles** pour insérer dynamiquement les données d’alerte lorsque le message est envoyé.  Par défaut les **en-têtes** et les **corps** à une charge XML semblable à la Voici un exemple.

    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```

    > [AZURE.NOTE] Vous pouvez tester la notification HTTP Post en cliquant sur le bouton **Tester la Notification** , mais uniquement après que la configuration d’alerte a été enregistrée.

13. Cliquez sur le bouton **Enregistrer** pour enregistrer votre configuration d’alerte.  Si vous avez coché l’option **Alerte activée** à l’étape 5, l’alerte est maintenant active.

## <a name="next-steps"></a>Étapes suivantes

- Analyser [les statistiques en temps réel dans Azure CDN](cdn-real-time-stats.md)
- Aller plus loin avec [les états HTTP avancés](cdn-advanced-http-reports.md)
- Analyser les [modèles d’utilisation](cdn-analyze-usage-patterns.md)

