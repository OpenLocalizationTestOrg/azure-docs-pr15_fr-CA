<properties
   pageTitle="Créer votre première fonction Azure | Microsoft Azure"
   description="Créer votre première fonction d’Azure, une application sans serveur, en moins de deux minutes."
   services="functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="hero-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="glenga"/>

#<a name="create-your-first-azure-function"></a>Créer votre première fonction Azure

##<a name="overview"></a>Vue d’ensemble
Fonctions Azure est une expérience pilotées par événements, compute-à la demande qui s’étend de la plate-forme d’application Azure existante avec les fonctionnalités à implémenter le code déclenchée par des événements se produisant dans d’autres services Azure, produits de SaaS et les systèmes sur site. Avec des fonctions d’Azure, vos applications mise à l’échelle en fonction de la demande et que vous payez uniquement pour les ressources que vous utilisez. Azure permet de fonctions vous permet de créer planifiée ou déclenchée des unités de code implémenté dans une variété de langages de programmation. Pour en savoir plus sur les fonctions d’Azure, consultez la [Vue d’ensemble des fonctions Azure](functions-overview.md).

Cette rubrique vous indique comment utiliser le fonctions d’Azure quickstart dans le portail pour créer une fonction Node.js « hello world » simple qui est appelée par un déclencheur de HTTP. Vous pouvez également visionner une courte vidéo pour voir comment ces étapes sont effectuées dans le portail.

## <a name="watch-the-video"></a>Regarder la vidéo

La vidéo suivante montrent comment effectuer les étapes de base dans ce didacticiel. 

[AZURE.VIDEO create-your-first-azure-function-simple]

##<a name="create-a-function-from-the-quickstart"></a>Créer une fonction de démarrage rapide

Une application de fonction héberge l’exécution de vos fonctions dans Azure. Suivez ces étapes pour créer une nouvelle application de la fonction, ainsi que la nouvelle fonction. La nouvelle fonction app est créé avec une configuration par défaut. Pour obtenir un exemple de la façon de créer explicitement votre application de fonction, consultez [le didacticiel de démarrage rapide de fonctions d’Azure autres](functions-create-first-azure-function-azure-portal.md).

Avant de créer votre première fonction, vous devez disposer d’un compte Azure actif. Si vous ne disposez pas d’un compte Azure, [comptes gratuits sont disponibles](https://azure.microsoft.com/free/).

1. Accédez au [portail de fonctions d’Azure](https://functions.azure.com/signin) et reconnectez-vous avec votre compte Azure.

2. Tapez un **nom** de unique pour votre nouvelle application de fonction ou accepter celui généré, sélectionnez votre préférence **région**, puis cliquez sur **créer + mise en route**. 

3. Dans l’onglet **démarrage rapide** , cliquez sur **WebHook + API** **JavaScript**, puis cliquez sur **créer une fonction**. Une nouvelle fonction Node.js prédéfinie est créée. 

    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (Facultatif) À ce stade dans le démarrage rapide, vous pouvez choisir d’afficher une présentation rapide des fonctionnalités des fonctions d’Azure dans le portail.   Une fois que vous avez terminé ou ignoré la visite guidée, vous pouvez tester votre nouvelle fonction à l’aide du déclencheur HTTP.

##<a name="test-the-function"></a>Test de la fonction

Étant donné que les Démarrages rapides d’Azure fonctions contient le code fonctionnel, vous pouvez tester immédiatement votre nouvelle fonction.

1. Dans l’onglet **développer** , examinez la fenêtre **Code** et notez que ce code Node.js attend une demande HTTP avec une valeur de *nom* passée dans le corps du message ou dans une chaîne de requête. Lorsque la fonction s’exécute, cette valeur est retournée dans le message de réponse.

    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Faites défiler jusqu'à la zone de texte **corps de requête** , modifiez la valeur de la propriété de *nom* à votre nom, puis sur **exécuter**. Vous verrez que l’exécution est déclenchée par une requête HTTP de test, les informations sont écrites dans les journaux de transmission en continu, et la réponse « hello » s’affiche dans la **sortie**. 

3. Pour déclencher l’exécution de la même fonction à partir d’une autre fenêtre de navigateur ou un autre onglet, copier la valeur de **l’URL de la fonction** à partir de l’onglet **développer** et collez-le dans une barre d’adresse du navigateur, puis ajoutez la valeur de chaîne de requête `&name=yourname` et appuyez sur ENTRÉE. Les mêmes informations sont écrites dans les journaux et le navigateur affiche la réponse « hello » comme avant.

##<a name="next-steps"></a>Étapes suivantes

Ce démarrage rapide montre une exécution très simple d’une fonction déclenché par HTTP de base. Consultez ces rubriques pour plus d’informations sur l’exploitant la puissance des fonctions d’Azure dans vos applications.

+ [Référence du développeur de fonctions Azure](functions-reference.md)  
Référence du programmeur pour les fonctions de codage et de définition des déclencheurs et des liaisons.
+ [Test des fonctions Azure](functions-test-a-function.md)  
Décrit les divers outils et techniques pour le test de vos fonctions.
+ [Comment faire évoluer les fonctions Azure](functions-scale.md)  
Décrit des plans de service disponibles avec fonctions d’Azure, y compris le plan de service dynamique et comment choisir la solution adaptée. 
+ [Quel est le Service d’application Azure ?](../app-service/app-service-value-prop-what-is.md)  
Fonctions Azure s’appuie sur la plate-forme de services d’application Azure pour les fonctionnalités principales telles que les déploiements, les variables d’environnement et les tests de diagnostic. 

[AZURE.INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]
