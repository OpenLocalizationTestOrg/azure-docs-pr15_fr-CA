
<properties
    pageTitle="Migration vers Azure Services cognitifs recommandations API à partir de l’API de recommandations DataMarket | Microsoft Azure"
    description="Machine Azure recommandations--migration vers le service COGNITIF de recommandations de formation"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="luisca"/>


# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>Migration vers Azure Services cognitifs recommandations API à partir de l’API de recommandations DataMarket
Cet article vous montre comment effectuer une migration à partir de l' [Interface API Microsoft DataMarket recommandations](https://datamarket.azure.com/dataset/amla/recommendations) à l' [API de recommandations Microsoft Azure COGNITIF Services](https://www.microsoft.com/cognitive-services/en-us/recommendations-api).

L’API de recommandations DataMarket cessera d’accepter de nouveaux clients au 31 décembre 2016 et le 28 février 2017 sera désapprouvée.

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>Comment démarrer avec l’API de recommandations de Services cognitifs Azure ?

Pour migrer vers l’API de recommandations de Services cognitifs, effectuez les opérations suivantes :

1.  Si vous ne disposez pas d’un abonnement Azure, [Inscrivez-vous](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) pour une. 

1.  Obtenez des instructions détaillées dans le [Guide de démarrage rapide](cognitive-services-recommendations-quick-start.md) pour s’inscrire pour l’API de recommandations de Services cognitifs et l’utiliser par programme. 

1.  Accédez à la [page d’accueil COGNITIF API de recommandations de Services](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) pour en savoir plus sur le service et rechercher de la documentation.

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>J’ai utilisé l’interface utilisateur de recommandations pour construire des Mes modèles. Existe-t-il un outil similaire pour l’API de recommandations COGNITIF Services ?

Absolument ! L’URL de la nouvelle [Interface utilisateur de recommandations](http://recommendations-portal.azurewebsites.net/) est http://recommendations-portal.azurewebsites.net. 

>[AZURE.NOTE] Vos informations d’identification de DataMarket ne fonctionnent pas ici. S’inscrire pour un abonnement dans le portail Azure et obtenir la clé de compte nécessaires pour utiliser la nouvelle [Interface utilisateur de recommandations](http://recommendations-portal.azurewebsites.net/).
Si vous n’avez pas une clé de compte, voir tâche 1 du [Guide de démarrage rapide](cognitive-services-recommendations-quick-start.md).

##<a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>Est le nouveau format API identique à l’API de recommandations DataMarket ?

L’API prend en charge les mêmes scénarios et les flux de processus en tant que ces scénarios pris en charge dans la version DataMarket, mais l’interface API réelle a été mis à jour pour être conforme aux [Directives d’API Microsoft reste](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Les API sont plus cohérentes et travail mieux avec des outils qui prennent en charge Swagger.

Pour comprendre chacune des API, consultez l' [Explorateur d’API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db).
Utilisez *essayez de* bouton pour tester un appel d’API. Le format de fichiers utilisée par l’API de recommandations (fichiers catalogue et utilisation) n’a pas changé, vous pouvez continuer à utiliser les fichiers et/ou les même infrastructure que vous avez créée pour générer ces fichiers.

##<a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>Que sont les nouvelles fonctionnalités de l’API de recommandations de Services cognitifs ?

Sur les deux derniers mois, nous avons publié les nouvelles fonctionnalités pour l’API de recommandations de Services cognitifs :
-   Recommandations de l’interface utilisateur pour la formation et de test des modèles sans avoir à écrire une seule ligne de code
-   Traitement par lots vous offrent des milliers de recommandations à la fois les scores
-   Générer la prise en charge de mesures de la qualité des modèles de recommandations de requête
-   Prise en charge des règles d’entreprise
-   Possibilité d’énumérer et de télécharger des fichiers de catalogue et d’utilisation
-   Prise en charge de génération classement pour interroger les fonctionnalités de qualité d’élément dans un modèle de recommandations
-   Il est désormais possible de rechercher un produit dans le catalogue

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>Lorsque Microsoft arrête prenant en charge l’API de recommandations DataMarket ?

[API de recommandations sur DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) cesse d’accepter de nouveaux clients après le 31 décembre 2016 et sera complètement désapprouvée par le 28 février 2017. 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>Que se passe-t-il si je n’ai pas les données dont j’ai besoin de recréer Mes modèles dans l’API de recommandations de Services cognitifs ?

Nous souhaitons que cette transition soit aussi facile que possible pour vous. Nous pouvons vous aider à déplacer vos anciens modèles à partir de votre compte de DataMarket à votre nouvel abonnement Azure COGNITIF Services recommandations API. Contactez-nous à l’adresse [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Nous vous demanderons de fournir votre DataMarket ID d’abonnement et de votre ID d’abonnement Services cognitifs, avant de nous associer les modèles à votre nouveau compte.
