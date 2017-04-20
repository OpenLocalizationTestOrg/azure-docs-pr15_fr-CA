<properties
   pageTitle="Avec des applications de logique à l’aide de fonctions Azure | Microsoft Azure"
   description="Apprendre à utiliser les fonctions d’Azure avec des applications de logique"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="using-azure-functions-with-logic-apps"></a>À l’aide des fonctions Azure avec des applications de logique

Vous pouvez exécuter des extraits de code personnalisés de C# ou node.js au sein d’une application de logique à l’aide des fonctions d’Azure à partir de.  [Fonctions d’Azure](../azure-functions/functions-overview.md) offre sans serveur informatique de Microsoft Azure. Cela est utile pour effectuer les tâches suivantes :

* Avancées de mise en forme ou de calcul de champs au sein d’une application de logique
* Exécution de calculs dans un flux de travail
* Étendre les fonctionnalités des applications de logique avec les fonctions qui sont prises en charge dans C# ou node.js

## <a name="create-a-function-for-logic-apps"></a>Créer une fonction pour les applications de logique

Nous vous recommandons de créer une nouvelle fonction dans le portail Azure fonctions en utilisant les modèles **Génériques Webhook - nœud** ou **Webhook - générique C#** . Cela auto-remplit un modèle qui accepte `application/json` à partir d’une application de logique.  Si vous cliquez sur l’onglet de **l’intégrer** dans les fonctions d’Azure, il doit avoir **le Mode** est défini sur **Webhook** et **Webhook type** de **JSON générique**.  Les fonctions qui utilisent ces modèles sont automatiquement détectées et répertoriées dans le Concepteur d’applications de logique sous **fonctions Azure dans ma région.**

Accepter une demande de fonctions de Webhook et passez-le à la méthode via une `data` variable. Vous pouvez accéder aux propriétés de votre charge en utilisant la notation par points comme `data.foo`.  Par exemple, une fonction JavaScript simple qui convertit une valeur DateTime en chaîne date ressemble à l’exemple suivant :

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>Appeler des fonctions d’Azure à partir d’une application de logique

Dans le concepteur, si vous cliquez sur le menu **Actions** , vous pouvez sélectionner **Les fonctions Azure dans ma région**.  Il répertorie les conteneurs dans votre abonnement et vous permet de choisir la fonction que vous souhaitez appeler.  

Après avoir sélectionné la fonction, vous êtes invité à spécifier un objet d’entrée de charge utile. Il s’agit du message qui envoie de l’application de la logique de la fonction, et il doit être un objet JSON. Par exemple, si vous souhaitez passer la date **De dernière modification** d’un déclencheur de la force de vente, la charge de la fonction peut se présenter comme suit :

![Dernière date modifiée][1]

## <a name="trigger-logic-apps-from-a-function"></a>Applications de logique de déclencheur à partir d’une fonction

Il est également possible de déclencher une application logique à partir d’une fonction.  Pour ce faire, créez simplement une application logique avec un déclencheur manuel. Pour plus d’informations, consultez [applications logique comme points de terminaison pouvant être appelés](app-service-logic-http-endpoint.md).  Ensuite, dans votre fonction, vous devez générer un HTTP POST vers l’URL de déclenchement manuel avec la charge utile que vous souhaitez envoyer à l’application de la logique.

### <a name="create-a-function-from-the-designer"></a>Créez une fonction à partir du Concepteur

Vous pouvez également créer une fonction de webhook node.js à partir du concepteur. Tout d’abord, sélectionnez **Fonctions Azure dans ma région** et choisissez un conteneur pour votre fonction.  Si vous n’avez encore pas un conteneur, vous devez en créer un à partir du [portail de fonctions d’Azure](https://functions.azure.com/signin). Puis cliquez sur **Créer**.  

Pour générer un modèle basé sur les données que vous souhaitez calculer, spécifiez l’objet de contexte que vous prévoyez de passer à une fonction. Ce doit être un objet JSON. Par exemple, si vous passez le contenu du fichier à partir d’une action de FTP, la charge utile du contexte ressemblera à ceci :

![Charge utile du contexte][2]

>[AZURE.NOTE] Étant donné que cet objet n’a pas été casté sous forme de chaîne, le contenu sera ajouté directement à la charge utile JSON. Toutefois, elle provoque une erreur, s’il n’est pas un jeton JSON (autrement dit, une chaîne ou un tableau d’objets JSON). Pour effectuer un cast en tant que chaîne, simplement ajouter des guillemets comme indiqué dans la première illustration dans cet article.

Le concepteur génère ensuite un modèle de fonction que vous pouvez créer en ligne. Les variables sont créés à l’avance en fonction du contexte que vous prévoyez de passer à la fonction.




<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png
