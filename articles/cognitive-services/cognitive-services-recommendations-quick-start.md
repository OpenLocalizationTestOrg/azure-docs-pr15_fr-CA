<properties
    pageTitle="Guide de démarrage rapide : API de recommandations d’apprentissage Machine | Microsoft Azure"
    description="Azure apprentissage automatique recommandations - Guide de démarrage rapide"
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
    ms.date="08/22/2016"
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-cognitive-services-recommendations-api"></a>Guide de démarrage rapide pour l’API de recommandations de Services cognitifs

Ce document décrit comment intégrée pour votre service ou votre application d’utiliser l' [API de recommandations](http://go.microsoft.com/fwlink/?LinkId=759710).
Vous trouverez plus d’informations sur l’API de recommandations et d’autres Services cognitifs [ici](http://go.microsoft.com/fwlink/?LinkId=759709). Tout au long de ce guide, vous pouvez également trouver la [Référence de l’API recommandations](http://go.microsoft.com/fwlink/?LinkId=759348) pratiques.


<a name="Overview"></a>
## <a name="general-overview"></a>Vue d’ensemble

Ce document est un guide pas à pas. Notre objectif est de vous guider à travers les étapes nécessaires pour former un modèle et vous oriente vers des ressources qui vous permettront d’utiliser le modèle à partir de votre environnement de production.

Cet exercice prend environ 30 minutes.

Pour utiliser [l’API de recommandations](http://go.microsoft.com/fwlink/?LinkId=759710), vous devez procéder comme suit :

1. Créer un modèle, un modèle est un conteneur de vos données d’utilisation, de données de catalogue et le modèle de recommandation.
1. Importer un catalogue - catalogue des données contient des informations de métadonnées sur les éléments.
1. Importer les données d’utilisation - données d’utilisation peuvent être téléchargés dans l’une des 2 méthodes (ou les deux) :
  -  En téléchargeant un fichier qui contient les données d’utilisation.
  -  Envoi d’événements d’acquisition de données.
  Généralement, vous téléchargez un fichier de l’utilisation pour pouvoir créer un modèle de la recommandation initiale (démarrage) et l’utiliser jusqu'à ce que le système de collecte suffisamment de données en utilisant le format d’acquisition de données.
1. Créer un modèle de recommandation : il s’agit d’une opération asynchrone, dans lequel le système de recommandation prend toutes les données d’utilisation et crée un modèle de recommandation. Cette opération peut prendre plusieurs minutes, voire plusieurs heures selon la taille des données et les paramètres de configuration de génération. Lors du déclenchement de la build, vous obtenez un code de génération. Il permet de vérifier si le processus de génération est terminée avant de commencer à utiliser les recommandations.
1. Utiliser les recommandations - obtenir des recommandations pour un article spécifique ou une liste d’éléments.

<a name="GetStarted"></a>
### <a name="lets-get-started"></a>Allons-y !

Vous allez commencer la création d’un modèle de recommandations. Puis nous allons vous guide sur l’utilisation des résultats générés par le modèle pour les recommandations de puissance sur un site de commerce électronique.

<a name="Ex1Task1"></a>
#### <a name="task-1---signing-up-for-the-recommendations-api"></a>Tâche 1 - signature pour l’API de recommandations ####

Dans cette tâche, vous allez inscrire le service API de recommandations et créer un modèle de recommandations.

1. Accédez au **Portail Azure** à [http://portal.azure.com/](http://portal.azure.com/) et connectez-vous avec votre compte Azure.

1.  Cliquez sur **+ nouvelle**.

1. Sélectionnez l’option **d’analyse décisionnelle** .

1. Sélectionnez le produit de **l’API des Services cognitifs** .
Ce produit vous permettra de démarrer un abonnement pour les services cognitifs API (Face, texte Analytique, Vision de l’ordinateur, etc.). Aujourd'hui, nous nous concentrerons sur l’API de recommandations.

1. Sur la page de lancement de l’API des Services cognitifs, entrez le **nom de compte** pour votre abonnement de recommandations. (Pour l’instance : « MyRecommendations »). Ce nom ne doit pas avoir d’espaces dans il.

1. Dans **type d’API**, sélectionnez **les recommandations**.

1. Sur **les niveaux de tarification**, vous pouvez sélectionner un plan. Vous pouvez sélectionner la couche **libre** pour 10 000 transactions par mois. Il s’agit d’un plan de libre, il est un bon moyen de commencer à essayer le système. Une fois que vous accédez à la production, nous vous recommandons de considérer votre volume de demande et de modifier le type de plan en conséquence. (Remarque : vous pouvez avoir qu’un seul abonnement gratuit de couche à la fois)

1. Sélectionnez un **Groupe de ressources**, ou créez-en un nouveau si vous n’en avez pas déjà.

1. Vous pouvez modifier d’autres éléments dans la boîte de dialogue Créer. Nous devons vous signaler que le fournisseur de ressources aujourd'hui est uniquement prise en charge des centres de données des États-Unis.

1. Une fois que vous avez terminé les sélections, cliquez sur **créer**.

1. Attendez quelques minutes que la ressource doit être déployée.
Une fois qu’il est déployé, vous pouvez accéder à la section **clés** de la lame de **paramètres** où vous disposez d’une clé primaire et secondaire d’utiliser l’API.  Copiez la clé primaire, car vous en aurez besoin lors de la création de votre premier modèle.

<a name="Ex1Task2"></a>
#### <a name="task-2---did-you-bring-your-data"></a>Tâche 2 : avez vous récupérez vos données ? ####

L’API de recommandations apprendrez à partir de votre catalogue et vos transactions afin de fournir des recommandations de bon produit. Cela signifie que vous devez insérer des données sur les produits (nous appelons cela un fichier de **catalogue** ) et un ensemble de transactions assez grands pour qu’il puisse trouver intéressant des modèles de consommation (nous appelons cette **utilisation**).

1. Généralement, vous souhaitez interroger votre base de données de transactions pour ces éléments d’information.
Au cas où vous ne les avez pratique, nous vous proposons des exemples de données en fonction des données de transaction de Microsoft Store.

 Vous pouvez télécharger les données à partir [d’ici](http://aka.ms/RecoSampleData). Copier et décompresser la MsStoreData.Zip dans un dossier sur votre ordinateur local.

 > **Remarque :** L’exemple de code que vous allez télécharger et exécuter dans la tâche 3 a des exemples de données déjà incorporés à l’intérieur, cette tâche est facultative.  Ceci dit, cette tâche vous permettra de télécharger des jeux de données plus réalistes et vous permettent de comprendre les entrées dans l’API de recommandations mieux.

1.  Maintenant examinons le fichier de catalogue. Naviguez jusqu'à l’emplacement où vous avez copié les données.
 Dans **le bloc-notes**, ouvrez le fichier de catalogue.

 Vous remarquerez que le fichier de catalogue est assez simple. Elle a le format suivant`<itemid>,<item name>,<product category>`

 >  AAA-04294, E34 OfficeLangPack 2013 32/64 en ligne DwnLd, Office <br>
 > AAA-04303, ET OfficeLangPack 2013 32/64 en ligne DwnLd, Office  <br>
 > C9F-00168, KRUSELL Kiruna retourner garde pour Nokia Lumia 635 - mixte, Accessoires

 Nous devons vous signaler qu’un fichier de catalogue peut être beaucoup plus riche, vous pouvez par exemple ajouter des métadonnées sur les produits (nous appelons ces *fonctionnalités de l’élément*). Vous devez voir la section [format de catalogue](http://go.microsoft.com/fwlink/?LinkID=760716) dans la référence des API pour plus de détails sur le format de catalogue.

1. Nous allons faire de même avec les données d’utilisation. Vous remarquerez que la date d’utilisation est au format `<User Id>,<Item Id>,<Time Stamp>,<Event>`.

  > 00037FFEA61FCA16, 288186200, 2015/08/04T11:02:52, 0003BFFDD4C2148C de l’achat, 297833400, 2015/08/04T11:02:50, 0003BFFDD4C2118D de l’achat, 297833300, 2015/08/04T11:02:40, achat, 00030000D16C4237, 297833300, 2015/08/04T11:02:37, 0003BFFDD4C20B63 de l’achat, 297833400, 2015/08/04T11:02:12, 00037FFEC8567FB8 de l’achat, 297833400, 2015/08/04T11:02:04, achat

Notez que les trois premiers éléments sont obligatoires. Le type d’événement est facultatif. Vous pouvez extraire le [format d’utilisation](http://go.microsoft.com/fwlink/?LinkID=760712) pour plus d’informations sur ce sujet.

 > **La quantité de données avez-vous besoin ?**
 <p>
Eh bien, cela dépend vraiment les données d’utilisation. Le système apprend que lorsque les utilisateurs achètent des articles différents. Pour certaines versions comme FBT, il est important de savoir quels éléments sont achetés dans les mêmes transactions. (Nous appelons cette *Collaboration occurrences*). Une bonne méthode empirique est d’utiliser la plupart des éléments dans des transactions de 20 ou plus, donc si vous avez 10 000 éléments dans votre catalogue, nous vous recommande que vous disposez d’au moins 20 fois ce nombre de transactions ou d’environ 200 000.
Une fois encore, il s’agit d’une règle générale. Vous devrez faire plusieurs essais avec vos données.
</p>

<a name="Ex1Task3"></a>
####Tâche 3 - Création d’un modèle de recommandations####

Maintenant que vous disposez d’un compte et que vous disposez de données, nous allons créer votre premier modèle.

Dans cette tâche, vous utiliserez l’exemple d’application pour créer votre premier modèle.

1. Tout d’abord, vous devez être conscient de la [Référence des API de recommandations](http://go.microsoft.com/fwlink/?LinkId=759348).

1. Télécharger l' [Exemple d’Application](http://go.microsoft.com/fwlink/?LinkID=759344) dans un dossier local.

1. Ouvrez la solution **RecommendationsSample.sln** située dans le dossier **C#** dans Visual Studio.

1. Ouvrez le fichier **SampleApp.cs** . Notez que les étapes suivantes dans le fichier :
 + Créer un modèle
 + Ajouter un fichier de catalogue
 + Ajouter un fichier d’utilisation
 + Déclencher une build pour le modèle
 + Obtenir une recommandation basée sur une paire d’éléments
<p></p>

1. Remplacez la valeur du champ **AccountKey** de la clé à partir de la tâche 1.

1. Les étapes de la solution, et vous verrez comment un modèle est créé.

1. Essayez de remplacer les fichiers de catalogue et l’utilisation que vous venez de télécharger pour créer un nouveau modèle pour Microsoft Store ou pour des recommandations du carnet. Vous devez modifier le nom du modèle et les éléments pour lesquels vous demandez de recommandations.

1. Lorsque le modèle est créé, prenez note de l' **ID de modèle** car vous en aurez besoin lors de la demande de recommandations dans votre environnement de production.

>  En savoir plus sur la build types et comment évaluer la qualité des builds [ici](cognitive-services-recommendations-buildtypes.md).

<a name="Ex1Task4"></a>
### <a name="putting-your-model-in-production"></a>Placer votre modèle en production ! ###

Maintenant que vous comprenez comment créer un modèle et d’utiliser les recommandations, l’étape suivante consiste à mettre en production sur votre site Web, application mobile ou à intégrer dans votre système de CRM ou ERP.
Bien évidemment, chacune de ces implémentations est différent. Dans la mesure où les API de recommandations sont demandées sous la forme d’un service web, vous devez être en mesure d’appeler facilement depuis n’importe lequel de ces différents environnements.

**Important :**  Si vous souhaitez afficher les recommandations à partir d’un client public (par exemple, votre site de commerce électronique), vous devez créer un serveur proxy pour fournir des recommandations. Ceci est important pour que votre clé API n’est pas exposée à des entités externes (potentiellement non fiables).

Voici quelques idées des emplacements où vous pouvez utiliser les recommandations :

### <a name="product-page"></a>Page produit

**Recommandations de l’article**
<p>Si le modèle a été formé sur les données de l’achat, il permet à votre client pour *découvrir les produits qui sont susceptibles d’être utiles aux personnes qui ont acheté l’élément source*.</p>
<p>Si le modèle a été formé sur les données, cliquez sur il permet à votre client pour *découvrir les produits qui sont susceptibles d’être visitées par les personnes qui ont visité l’élément source*. Ce type de modèle peut retourner des objets similaires.</p>

**Fréquemment achetés ensemble des recommandations**
<p>A fréquemment acheté ensemble build pourrait être formé, afin d’obtenir des ensembles d’éléments sont susceptibles d’être achetés avec cet élément.</p>

### <a name="check-out-page"></a>Extraction de Page

**Recommandations de l’article**
<p>Recommandations pourrait prendre comme le modèle d’entrée à une liste d’éléments. Ainsi, vous pouvez passer tous les éléments dans le panier en tant qu’entrée pour obtenir des recommandations.
Dans ce cas, le modèle fournit les recommandations qui sont d’intérêt étant donné tous les éléments dans le panier.
</p>

### <a name="landing-page"></a>Page d’accueil

**Recommandations de l’utilisateur**
<p>
Un modèle peut prendre comme de recommandations d’entrée de l’id de l’utilisateur.  Utiliser l’historique des transactions par l’utilisateur à fournir des recommandations personnalisées pour l’utilisateur spécifié.
</p>

Consultez la [Documentation de recommandations élément obtenir](http://go.microsoft.com/fwlink/?LinkID=760719).

<a name="Ex1Task6"></a>
### <a name="whats-next"></a>Quel est l’avenir ?
Félicitations, si vous avez effectué ce bien ! Pour en savoir que plus, vous pouvez visiter la complète de [Référence sur l’API recommandations](http://go.microsoft.com/fwlink/?LinkId=759348) si vous avez des questions, n’hésitez pas à nous contacter aumlapi@microsoft.com
