<properties 
    pageTitle="Vue d’ensemble du Pack d’intégration entreprise | Service d’application Microsoft Azure | Microsoft Azure" 
    description="Les fonctionnalités du Pack d’intégration entreprise permet d’activer des scénarios d’intégration et de processus métier à l’aide du service de Microsoft Azure App" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-xml-transforms"></a>Intégration avec des transformations XML

## <a name="overview"></a>Vue d’ensemble
Le connecteur de transformation d’intégration entreprise convertit les données d’un format vers un autre format. Par exemple, peut avoir un message entrant contenant la date actuelle dans le format YearMonthDay. Vous pouvez utiliser une transformation pour remettre en forme de la date dans le format MonthDayYear.

## <a name="what-does-a-transform-do"></a>Que fait une transformation ?
Une transformation, qui est également connu comme un mappage, se compose d’un schéma XML de la Source (l’entrée) et un schéma cible XML (sortie). Vous pouvez utiliser différentes fonctions intégrées afin de manipuler ou de contrôler les données, y compris des manipulations de chaînes, affectations conditionnelles, expressions arithmétiques, formateurs d’heure date et même des constructions de bouclage.

## <a name="how-to-create-a-transform"></a>Comment faire pour créer une transformation ?
Vous pouvez créer un plan de transformation/à l’aide du [SDK d’intégration entreprise](https://aka.ms/vsmapsandschemas)de Visual Studio. Lorsque vous avez terminé de créer et de tester la transformation, vous téléchargez la transformation dans votre compte d’intégration. 

## <a name="how-to-use-a-transform"></a>Comment faire pour utiliser une transformation
Après avoir téléchargé la transformation dans votre compte d’intégration, vous pouvez l’utiliser pour créer une application de logique. L’application logique s’exécutera alors vos transformations chaque fois que l’application de la logique est déclenchée (et il est saisie du contenu qui doit être transformée).

**Voici la procédure pour utiliser une transformation**:

### <a name="prerequisites"></a>Conditions préalables 
Dans l’aperçu, vous devez :  

-  [Création d’un conteneur de fonctions d’Azure] (https://ms.portal.azure.com/#create/Microsoft.FunctionApp "Création d’un conteneur de fonctions d’Azure")  
-  [Ajouter une fonction pour le conteneur de fonctions d’Azure] (https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "Ce modèle crée une fonction de webhook de base de C# azure avec capacités de transformation à utiliser dans les scénarios d’intégration d’applications logique")    
-  Créer un compte d’intégration et de lui ajouter un mappage  

>[AZURE.TIP] Prenez note de la le nom du conteneur Azure fonctions et la fonction d’Azure, vous en aurez besoin dans l’étape suivante.  

Maintenant que vous avez prise en charge des composants requis, il est temps de créer votre application logique :  

1. Créer une application de logique et [liez-le à votre compte d’intégration](./app-service-logic-enterprise-integration-accounts.md "apprendre à lier un compte de l’intégration à une application de logique") qui contient le mappage.
2. Ajouter un déclencheur **- requête HTTP d’un lors de la demande** à votre application logique  
![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)    
3. Ajoutez l’action de la **Transformation de code XML** en première sélection **Ajouter une action**   
![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)   
4. Entrez le mot *transformation* dans la zone de recherche pour filtrer toutes les actions à celui que vous souhaitez utiliser  
![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)  
5. Sélectionnez l’action de la **Transformation de code XML**   
![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)  
6. Sélectionnez le **Conteneur de la fonction** qui contient la fonction que vous utilisez. C’est le nom du conteneur d’Azure fonctions que vous avez créé précédemment dans cette procédure.
7. Sélectionnez la **fonction** que vous souhaitez utiliser. C’est le nom de la fonction d’Azure que vous avez créé précédemment.
8. Ajoutez le code XML **contenu** qui vous rendra. Notez que vous pouvez utiliser des données XML que vous recevez dans la demande HTTP en tant que le **contenu**. Dans cet exemple, sélectionnez le corps de la demande HTTP qui a déclenché l’application logique.
9. Sélectionnez le nom de la **carte** que vous voulez utiliser pour effectuer la transformation. La carte doit être déjà dans votre compte d’intégration. À une étape précédente, vous avez déjà l’accès de votre application logique à votre compte d’intégration qui contient votre mappage.
10. Enregistrez votre travail.  
![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png) 

À ce stade, vous avez terminé la configuration de votre carte. Dans une application réelle, vous souhaitez peut-être stocker les données transformées dans une application métier, tels que la force de vente. Vous pouvez facilement en tant qu’action pour envoyer la sortie de la transformation de la force de vente. 

Vous pouvez maintenant tester votre fichier de transformation en effectuant une demande au point de terminaison HTTP.  

## <a name="features-and-use-cases"></a>Fonctions et exemples d’utilisation

- La transformation créée dans un mappage peut être simple, par exemple la copie d’un nom et une adresse d’un document à un autre. Ou bien, vous pouvez créer des transformations plus complexes, l’utilisation des opérations de l’emploi de mappage.  
- Plusieurs opérations de mappage ou de fonctions sont disponibles, y compris les chaînes, les fonctions date heure et ainsi de suite.  
- Vous pouvez faire une copie de données directe entre les schémas. Dans le Mappeur inclus dans le Kit de développement de logiciel, c’est aussi simple que le dessin d’une ligne qui relie les éléments dans le schéma source avec leurs équivalents dans le schéma de destination.  
- Lorsque vous créez un mappage, vous permet d’afficher une représentation graphique de la carte, qui affiche toutes les relations et les liens que vous créez.
- Pour ajouter un exemple de message XML, utilisez la fonction de plan de Test. D’un simple clic, vous pouvez tester le mappage que vous avez créé et consultez la sortie générée.  
- Télécharger les mappages existants  
- Prend en charge le format XML.


## <a name="learn-more"></a>Pour en savoir plus
- [En savoir plus sur le Pack d’intégration entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le Pack d’intégration entreprise")  
- [En savoir plus sur les cartes] (./app-service-logic-enterprise-integration-maps.md "En savoir plus sur l’intégration d’entreprise mappé")  
 