<properties 
    pageTitle="Opérations courantes dans l’API de recommandations d’apprentissage Machine | Microsoft Azure" 
    description="Application exemple de recommandation Azure ML" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 


# <a name="recommendations-api-sample-application-walkthrough"></a>Procédure pas à pas de recommandations exemple d’API Application

>[AZURE.NOTE]Vous devez démarrer à l’aide du Service COGNITIF API de recommandations au lieu de cette version. Le Service COGNITIF recommandations remplacera ce service et toutes les nouvelles fonctionnalités seront développées il. Il a de nouvelles fonctionnalités telles que le traitement par lots à la prise en charge, une meilleure API Explorer, expérience de surface et plus cohérente d’inscription/facturation nettoyeur API, etc..
> Pour en savoir plus sur la [migration vers le nouveau Service COGNITIF](http://aka.ms/recomigrate)

##<a name="purpose"></a>Objectif

Ce document illustre l’utilisation de l’API de recommandations de formation de Machine Azure via un [exemple d’application](https://code.msdn.microsoft.com/Recommendations-144df403).

Cette application n’est pas envisagée d’inclure des fonctionnalités complètes, et n’utilise pas toutes les API. Il illustre les opérations courantes à effectuer lorsque vous voulez d’abord lire avec le service de recommandation d’apprentissage automatique. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="introduction-to-machine-learning-recommendation-service"></a>Introduction au service de recommandation d’apprentissage automatique

Recommandations via le service de recommandation d’apprentissage automatique sont activées lorsque vous créez un modèle de recommandation basé sur les données suivantes :

* Un référentiel des éléments que vous souhaitez est recommandé, également connu sous le nom d’un catalogue
* Données représentant l’utilisation d’éléments par un utilisateur ou une session (ce peut être acquis au fil du temps par le biais d’acquisition de données, pas en tant que partie de l’exemple d’application)

Après la construction d’un modèle de recommandation, vous pouvez l’utiliser pour prévoir les éléments susceptibles d’être un utilisateur souhaite, en fonction d’un ensemble d’éléments (ou un seul élément) l’utilisateur sélectionne.

Pour activer le scénario précédent, procédez comme suit dans le service de recommandation d’apprentissage automatique :

* Créer un modèle : il s’agit d’un conteneur logique qui contient les données (catalogue et utilisation) et les modèles de prévision. Chaque conteneur de modèle est identifié par un ID unique, qui est attribué lors de sa création. Ce code est appelé ID de modèle, et il est utilisé par la plupart des API. 
* Téléchargement de catalogue : lors de la création d’un conteneur de modèle, vous pouvez associer à celui-ci un catalogue.

**Remarque**: création d’un modèle et en transmettant à un catalogue sont généralement exécutées qu’une seule fois pour le cycle de vie du modèle.

* Télécharger utilisation : cela ajoute des données d’utilisation pour le conteneur de modèle.
* Créer un modèle de recommandation : une fois que vous avez suffisamment de données, vous pouvez construire le modèle de recommandation. Cette opération utilise les algorithmes d’apprentissage automatique supérieur pour créer un modèle de recommandation. Chaque build est associée à un ID unique. Vous devez conserver un enregistrement de ce code car il n’est nécessaire pour la fonctionnalité de certaines API.
* Surveiller le processus de construction : construction d’un modèle de recommandation est une opération asynchrone, et elle peut prendre de quelques minutes à plusieurs heures, selon la quantité de données (catalogue et utilisation) et les paramètres de génération. Par conséquent, vous devez surveiller la génération. Un modèle de la recommandation est créé que si sa build associée se termine avec succès.
* (Facultatif) Choisissez une version de modèle de recommandation active : cette étape est nécessaire uniquement si vous avez plus d’un modèle de recommandation intégré dans votre conteneur de modèle. Toute demande pour obtenir des recommandations sans indiquant le modèle de recommandation active est redirigé automatiquement par le système pour la génération active par défaut. 

**Remarque**: un modèle de recommandation active est prêts pour la production et il est conçu pour la charge de production. Cela diffère d’un modèle non actif recommandation, qui reste dans un environnement test (parfois appelé mise en attente).

* Obtenir des recommandations : une fois que vous avez un modèle de recommandation, vous pouvez déclencher des recommandations pour un élément unique ou une liste d’éléments que vous sélectionnez. 

Obtenir une recommandation vous appellera généralement pendant une certaine période de temps. Au cours de cette période de temps, vous pouvez rediriger les données d’utilisation pour le système de recommandation d’apprentissage automatique, qui ajoute ces données dans le conteneur de modèle spécifié. Lorsque vous avez suffisamment de données l’utilisation, vous pouvez créer un nouveau modèle de recommandation qui incorpore les données d’utilisation supplémentaires. 

##<a name="prerequisites"></a>Conditions préalables

* Visual Studio 2013
* Accès à Internet 
* Abonnement aux recommandations API (https://datamarket.azure.com/dataset/amla/recommendations).

##<a name="azure-machine-learning-sample-app-solution"></a>Solution d’application échantillon apprentissage automatique Azure

Cette solution contient le code source, exemple d’utilisation, le fichier de catalogue et les directives pour télécharger les packages qui sont requises pour la compilation.

##<a name="the-apis-used"></a>Les API utilisées

L’application utilise la fonctionnalité de recommandation d’apprentissage automatique via un sous-ensemble des API disponibles. Les API suivantes sont illustrées dans l’application :

* Créer le modèle : créez un conteneur logique pour contenir des modèles de données et de la recommandation. Un modèle est identifié par un nom, et vous ne pouvez pas créer plusieurs modèles portant le même nom.
* Télécharger le fichier de catalogue : permet de télécharger des données de catalogue.
* Télécharger le fichier d’utilisation : permet de télécharger des données d’utilisation.
* Déclencher la génération : permet de créer un modèle de recommandation.
* Surveiller l’exécution de la génération : permet de surveiller l’état de construction d’un modèle de recommandation.
* Choisir un modèle intégré pour recommandation : permet d’indiquer quel modèle de recommandation à utiliser par défaut pour un certain conteneur de modèle. Cette étape est nécessaire uniquement si vous avez plus d’un modèle de recommandation et que vous souhaitez activer une génération non actif comme modèle de recommandation active.
* Obtenir la recommandation : permet de récupérer des éléments en fonction d’un article unique ou un ensemble d’éléments. 

Pour une description complète de l’API, reportez-vous à la documentation de Microsoft Azure Marketplace. 

**Remarque**: un modèle peut avoir plusieurs builds au fil du temps (et non pas simultanément). Chaque build est créée avec le même ou un catalogue mis à jour et les données d’utilisation supplémentaires.

## <a name="common-pitfalls"></a>Pièges courants

* Vous devez fournir votre nom d’utilisateur et de votre clé de compte principal Microsoft Azure Marketplace pour exécuter l’exemple d’application.
* Exécution de l’exemple d’application consécutivement échouera. Le flux de l’application inclut la création, téléchargement, création du moniteur et obtenir des recommandations d’un modèle prédéfini ; Par conséquent, il échouera consécutives de l’exécution si vous ne modifiez pas le nom du modèle entre les appels.
* Recommandations peuvent retourner sans données. L’exemple d’application utilise un très petit fichier de catalogue et d’utilisation. Par conséquent, certains éléments du catalogue n’aura aucun élément recommandé.

## <a name="disclaimer"></a>Exclusion de responsabilité
L’exemple d’application n’est pas destiné à être exécuté dans un environnement de production. Les données fournies dans le catalogue sont très faible, et elle ne fournit pas un modèle explicite de recommandation. Les données sont fournies sous la forme d’une démonstration. 
 
