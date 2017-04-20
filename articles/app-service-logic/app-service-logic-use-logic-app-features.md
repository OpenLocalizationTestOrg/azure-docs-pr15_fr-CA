<properties 
    pageTitle="Utiliser les fonctionnalités de l’application de la logique | Microsoft Azure" 
    description="Apprenez à utiliser les fonctionnalités avancées des applications de logique." 
    authors="stepsic-microsoft-com" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/28/2016"
    ms.author="stepsic"/> 
    
# <a name="use-logic-apps-features"></a>Utiliser les fonctionnalités des applications de logique

Dans la [rubrique précédente](app-service-logic-create-a-logic-app.md), vous avez créé votre première application logique. Nous allons maintenant vous montrer générer un processus plus complète à l’aide de la logique de Services App Apps. Cette rubrique présente les nouveaux concepts de logique applications suivants :

- Logique conditionnelle, qui exécute une action uniquement si une certaine condition est remplie.
- Mode code pour modifier une application logique existant.
- Options de démarrage d’un workflow.

Avant de terminer cette rubrique, vous devez effectuer les étapes dans [créer une nouvelle application logique](app-service-logic-create-a-logic-app.md). Dans [Azure portal], accédez à votre application logique et cliquez sur les **déclencheurs et les Actions** dans le résumé de modifier la définition d’application logique.

## <a name="reference-material"></a>Documents de référence

Vous consultez les documents suivants :

- [Gestion et autres API d’exécution](https://msdn.microsoft.com/library/azure/mt643787.aspx) - y compris comment appeler directement des applications de logique
- [Référence du langage](https://msdn.microsoft.com/library/azure/mt643789.aspx) : une liste complète de toutes les fonctions prises en charge/expressions
- [Types de déclencheur et action](https://msdn.microsoft.com/library/azure/mt643939.aspx) - les différents types d’actions et les entrées qu’ils prennent.
- [Vue d’ensemble du Service d’application](../app-service/app-service-value-prop-what-is.md) - description de quels sont les composants à créer une solution

## <a name="adding-conditional-logic"></a>Ajout d’une logique conditionnelle

Bien que le flux d’origine fonctionne, il existe certains domaines qui pourraient être améliorés. 


### <a name="conditional"></a>Conditionnelle
Cette application logique risque de vous l’obtention d’un grand nombre de messages. Les étapes suivantes ajoutent logique afin de vous assurer que vous recevez uniquement un e-mail lorsque celui-ci provient d’une personne avec un certain nombre de barreaux. 

1. Cliquez sur le signe plus et de trouver l’action *Utilisateur d’obtenir* pour Twitter.

2. Passez dans le champ **Tweeted par** à partir du déclencheur pour obtenir les informations relatives à l’utilisateur de Twitter.

    ![Obtenir l’utilisateur](./media/app-service-logic-use-logic-app-features/getuser.png)

3. Cliquez à nouveau sur le signe plus, mais cette fois, sélectionnez **Ajouter une Condition**

4. Dans la première zone, cliquez sur le **...** sous **Obtenir l’utilisateur** pour rechercher le champ **nombre de marché** .

5. Dans la liste déroulante, sélectionnez **supérieure à**

6. Dans la deuxième zone, tapez le nombre de barreaux de votre choix aux utilisateurs.

    ![Conditionnelle](./media/app-service-logic-use-logic-app-features/conditional.png)

7.  Enfin, la zone glisser-déplacer le courrier électronique dans la zone **Si Oui** . Cela signifie que vous obtiendrez uniquement les e-mails lorsque le nombre de suiveur est remplie.

## <a name="repeating-over-a-list-with-foreach"></a>Répétition sur une liste à l’aide de forEach

La boucle forEach spécifie un tableau pour répéter une action sur. Si elle n’est pas un tableau le flux échoue. Par exemple, si vous avez action1 qui renvoie un tableau des messages, et que vous souhaitez envoyer chaque message, vous pouvez inclure cette instruction forEach dans les propriétés de votre action : forEach :"@action('action1').outputs.messages"
 

## <a name="using-the-code-view-to-edit-a-logic-app"></a>À l’aide du mode code pour modifier une application logique

Outre le concepteur, vous pouvez modifier directement le code qui définit une logique d’application, comme suit. 

1. Cliquez sur le bouton **d’affichage du Code** dans la barre de commandes. 

    Cela ouvre un éditeur complet qui présente la définition que vous venez de modifier.

    ![Affichage du code](./media/app-service-logic-use-logic-app-features/codeview.png)

    À l’aide de l’éditeur de texte, vous pouvez copier et coller n’importe quel nombre d’actions au sein de la même application logique ou entre les applications de la logique. Vous pouvez facilement ajouter ou supprimer des sections entières de la définition, et vous pouvez également partager des définitions avec d’autres personnes.

2. Après avoir apporté vos modifications en mode code, cliquez sur **Enregistrer**. 

### <a name="parameters"></a>Paramètres
Il existe des fonctionnalités des applications de logique qui ne peut être utilisé en mode code. Paramètres en est un exemple. Paramètres facilitent la réutilisation de valeurs dans l’ensemble de votre application logique. Par exemple, si vous avez une adresse de messagerie que vous souhaitez utiliser dans plusieurs actions, vous devez le définir en tant que paramètre.

La commande suivante met à jour votre application logique existant pour utiliser des paramètres pour le terme de requête.

1. Dans le mode code, recherchez la `parameters : {}` objet et insérer l’objet suivant de la rubrique :

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }
    
2. Faites défiler jusqu'à la `twitterconnector` action, recherchez la valeur de la requête et remplacez-la par `#@{parameters('topic')}`.
    Vous pouvez également utiliser la fonction **concat** pour joindre deux ou plusieurs chaînes, par exemple : `@concat('#',parameters('topic'))` est identique à ce qui précède. 
 
Les paramètres sont un bon moyen d’extraire les valeurs que vous pouvez modifier un lot. Ils sont particulièrement utiles lorsque vous souhaitez substituer les paramètres dans différents environnements. Pour plus d’informations sur le remplacement de paramètres en fonction de l’environnement, reportez-vous à notre [documentation de l’API REST](https://msdn.microsoft.com/library/mt643787.aspx).

Maintenant, lorsque vous cliquez sur **Enregistrer**, toutes les heures vous obtenez tout nouveau tweet qui ont plus de 5 retweets remis dans un dossier nommé **tweet** dans votre dossier d’échange.

Pour en savoir plus sur les définitions de l’application de la logique, voir [créer des définitions de la logique d’application](app-service-logic-author-definitions.md).

## <a name="starting-a-logic-app-workflow"></a>Démarrage d’un workflow d’application logique
Il existe plusieurs options pour démarrer le flux de travail défini dans une application de logique de vous. Un flux de travail peut toujours être démarré à la demande dans le [portail Azure].

### <a name="recurrence-triggers"></a>Déclencheurs de périodicité
Un déclencheur de périodicité s’exécute à un intervalle que vous spécifiez. Lorsque le déclencheur a une logique conditionnelle, le déclencheur détermine si le flux de travail doit exécuter. Un déclencheur indique qu’il doit exécuter en retournant un `200` code d’état. Lorsqu’il n’a pas besoin d’exécuter, il renvoie une `202` code d’état.

### <a name="callback-using-rest-apis"></a>Rappel à l’aide des API de repos
Les services peuvent appeler un point de terminaison logique app pour démarrer un flux de travail. Pour plus d’informations, consultez [applications logique comme points de terminaison pouvant être appelés](app-service-logic-connector-http.md) . Pour ce type de logique app à la demande, cliquez sur le bouton **Exécuter maintenant** sur la barre de commandes. 

<!-- Shared links -->
[Azure portal]: https://portal.azure.com 