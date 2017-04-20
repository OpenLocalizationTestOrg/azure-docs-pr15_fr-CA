<properties 
    pageTitle="Recommandations d’apprentissage machine : Intégration JavaScript | Microsoft Azure" 
    description="Documentation de recommandations d’apprentissage Machine - intégration JavaScript - Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="javascript" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="azure-machine-learning-recommendations---javascript-integration"></a>Machine Azure recommandations - intégration JavaScript d’apprentissage

>[AZURE.NOTE]Vous devez démarrer à l’aide du Service COGNITIF API de recommandations au lieu de cette version. Le Service COGNITIF recommandations remplacera ce service et toutes les nouvelles fonctionnalités seront développées il. Il a de nouvelles fonctionnalités telles que le traitement par lots à la prise en charge, une meilleure API Explorer, expérience de surface et plus cohérente d’inscription/facturation nettoyeur API, etc..
> Pour en savoir plus sur la [migration vers le nouveau Service COGNITIF](http://aka.ms/recomigrate)


Ce document décrivent comment intégrer votre site à l’aide de JavaScript. Le code JavaScript vous permet d’envoyer des événements d’Acquisition de données et d’utiliser les recommandations une fois que vous créez un modèle de la recommandation. Toutes les opérations effectuées via JS peuvent également être effectuées côté serveur.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="1-general-overview"></a>1. présentation générale
Intégration de votre site avec Azure ML recommandations se composent sur 2 Phases :

1.  Envoyer des événements dans les recommandations Azure ML. Cela permettra de créer un modèle de recommandation.
2.  Utiliser les recommandations. Une fois le modèle créé, vous pouvez utiliser les recommandations. (Il n’explique pas comment créer un modèle, de lire le guide de démarrage rapide pour obtenir plus d’informations sur la façon de).


<ins>Phase I</ins>

Dans la première phase vous insérez dans vos pages html une petite bibliothèque JavaScript qui permet à la page envoyer des événements dès qu’ils surviennent dans la page html en serveurs Azure ML recommandations (par l’intermédiaire de marché de données) :

![Dessin1][1]

<ins>Phase II</ins>

Dans la deuxième phase, lorsque vous souhaitez afficher les recommandations sur la page, vous sélectionnez une des options suivantes :

1.les votre serveur (sur la phase de rendu de la page) appelle Azure ML recommandations Server (via le marché des données) pour obtenir des recommandations. Les résultats incluent une liste des id d’éléments. Votre serveur a besoin enrichir les résultats avec les éléments de métadonnées (par exemple, des images, description) et envoyer la page créée dans le navigateur.

![Drawing2][2]

2 l’autre option consiste à utiliser le petit fichier JavaScript à partir de la première phase pour obtenir une simple liste d’éléments. Les données reçues ici sont plus légers que celui de la première option.

![Drawing3][3]

##<a name="2-prerequisites"></a>2. conditions préalables

1. Créer un nouveau modèle à l’aide de l’API. Sur la procédure à suivre, consultez le guide de démarrage rapide.
2. Coder votre &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt; avec base64. (Cela servira pour l’authentification de base pour activer le code JS appeler les API).


##<a name="3-send-data-acquisition-events-using-javascript"></a>3. envoi d’événements d’Acquisition de données à l’aide de JavaScript
Les étapes suivantes facilitent l’envoi d’événements :

1.  Inclure la bibliothèque JQuery dans votre code. Vous pouvez le télécharger à partir de nuget dans l’URL suivante.

        http://www.nuget.org/packages/jQuery/1.8.2
2.  Inclure la bibliothèque Script Java de recommandations à partir de l’URL suivante : http://aka.ms/RecoJSLib1

3.  Initialisation de la bibliothèque d’Azure ML recommandations avec les paramètres appropriés.

        <script>
            AzureMLRecommendationsStart("<base64encoding of username:key>",
            "<model_id>");
        </script>

4.  Envoyer l’événement approprié. Consultez la section détaillée ci-dessous sur tous les types d’événements (exemple de cliquez sur événement)

        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") {      
                        AzureMLRecommendationsEvent = [];
                    }
            AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
        </script>


###<a name="31-limitations-and-browser-support"></a>3.1. Limitations et prise en charge du navigateur
Il s’agit d’une implémentation de référence et il est donné en l’état. Il doit prendre en charge de tous les principaux navigateurs.

###<a name="32-type-of-events"></a>3.2. Type d’événements
Il existe 5 types d’événements qui prend en charge de la bibliothèque : cliquez sur, recommandation, ajouter au panier d’achats, supprimer de panier d’achats et d’achat. Il existe un événement supplémentaire qui est utilisé pour définir le contexte d’utilisateur appelé connexion.

####<a name="321-click-event"></a>3.2.1. Cliquez sur l’événement
Cet événement doit être utilisé à chaque fois qu’un utilisateur a cliqué sur un élément. Généralement lorsque l’utilisateur clique sur un élément une nouvelle page est ouvert avec les détails de l’élément ; dans cette page, cet événement doit être déclenché.

Paramètres :
- « Cliquez sur l’événement (chaîne obligatoire) »
- élément (chaîne obligatoire) - identificateur Unique de l’élément
- nom de l’élément (string, facultatif) - le nom de l’élément
- DescriptionArticle (string, facultatif) - la description de l’article
- itemCategory (chaîne, facultatif) - la catégorie de l’élément
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
        </script>

Ou avec des données facultatives :

        <script>
            if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
        </script>


####<a name="322-recommendation-click-event"></a>3.2.2. Recommandation cliquez sur événement
Cet événement doit être utilisé à chaque fois qu’un utilisateur a cliqué sur un élément qui a été reçu à partir de recommandations de ML Azure comme un élément recommandé. Généralement lorsque l’utilisateur clique sur un élément une nouvelle page est ouvert avec les détails de l’élément ; dans cette page, cet événement doit être déclenché.

Paramètres :
- événement (chaîne obligatoire) - « recommendationclick »
- élément (chaîne obligatoire) - identificateur Unique de l’élément
- nom de l’élément (string, facultatif) - le nom de l’élément
- DescriptionArticle (string, facultatif) - la description de l’article
- itemCategory (chaîne, facultatif) - la catégorie de l’élément
- graines (tableau de chaînes, facultatif) - la génération de la requête de la recommandation.
- recoList (tableau de chaînes, facultatif) - le résultat de la demande de recommandation qui a généré l’élément que l’utilisateur a cliqué.
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
        </script>

Ou avec des données facultatives :

        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"]               });
        </script>


####<a name="323-add-shopping-cart-event"></a>3.2.3. Ajouter l’événement de panier d’achat
Cet événement doit être utilisé lorsque l’utilisateur Ajouter un élément au panier.
Paramètres :
* événement (chaîne obligatoire) - « addshopcart »
* élément (chaîne obligatoire) - identificateur Unique de l’élément
* nom de l’élément (string, facultatif) - le nom de l’élément
* DescriptionArticle (string, facultatif) - la description de l’article
* itemCategory (chaîne, facultatif) - la catégorie de l’élément
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
        </script>

####<a name="324-remove-shopping-cart-event"></a>3.2.4. Supprimer l’événement de panier d’achat
Cet événement doit être utilisé lorsque l’utilisateur supprime un élément dans le panier d’achat.

Paramètres :
* événement (chaîne obligatoire) - « removeshopcart »
* élément (chaîne obligatoire) - identificateur Unique de l’élément
* nom de l’élément (string, facultatif) - le nom de l’élément
* DescriptionArticle (string, facultatif) - la description de l’article
* itemCategory (chaîne, facultatif) - la catégorie de l’élément
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
        </script>

####<a name="325-purchase-event"></a>3.2.5. Événement d’achat
Cet événement doit être utilisé lorsque l’utilisateur acheté à son panier d’achat.

Paramètres :
* événement (chaîne) - « fournisseur »
* achats ([]) - tableau contenant une entrée pour chaque article acheté.<br><br>
Format acheté :
    * élément (string) : identificateur Unique de l’élément.
    * (int ou string) - nombre d’éléments qui ont été achetées.
    * prix (float ou chaîne) - champ facultatif - le prix de l’article.

L’exemple ci-dessous illustre l’achat de 3 articles (33, 34, 35), deux avec tous les champs remplis (article, nombre, prix) et l’autre (article 34) sans un prix.

        <script>
            if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
        </script>

####<a name="326-user-login-event"></a>3.2.6. Événements d’ouverture de session utilisateur
Bibliothèque d’événement de recommandations ML Azure crée et utiliser un cookie pour identifier les événements provenant du même navigateur. Afin d’améliorer les résultats du modèle Azure ML recommandations permet de définir une identification unique utilisateur qui va remplacer l’utilisation de cookies.

Cet événement doit être utilisé après la connexion de l’utilisateur à votre site.

Paramètres :
* événement (string) : « userlogin »
* utilisateur (chaîne) - l’identification unique de l’utilisateur.
        <script>
  Si (typeof AzureMLRecommendationsEvent == « undefined ») {AzureMLRecommendationsEvent = [] ;} AzureMLRecommendationsEvent.push ({événement : « userlogin », utilisateur : "ABCD10AA"}) ;      </script>

##<a name="4-consume-recommendations-via-javascript"></a>4. Utilisation des recommandations via JavaScript
Le code qui utilise la recommandation est déclenché par un événement JavaScript par page de Web du client. La réponse de la recommandation comprend les ID des éléments recommandés, leurs noms et leurs évaluations. Il est préférable d’utiliser cette option uniquement pour un affichage de la liste des éléments recommandés - gestion plus complexe (par exemple, l’ajout de métadonnées de l’élément) doit être effectuée sur l’intégration du côté serveur.

###<a name="41-consume-recommendations"></a>4.1 Utilisation de recommandations
Pour consommer des recommandations que vous devez incluent les bibliothèques JavaScript requises dans votre page et d’appeler AzureMLRecommendationsStart. Reportez-vous à la section 2.

Pour consommer des recommandations pour un ou plusieurs éléments, vous devez appeler une méthode appelée : AzureMLRecommendationsGetI2IRecommendation.

Paramètres :
* Un ou plusieurs éléments pour obtenir des recommandations pour les éléments (tableau de chaînes -). Si vous consommez une génération Fbt vous pouvez définir ici qu’un seul élément.
* numberOfResults (int) - nombre de résultats requis.
* includeMetadata (boolean, facultatif) - si la valeur 'true' indique que le champ de métadonnées doit être rempli dans le résultat.
* Renvoyé de la fonction - une fonction qui gérera les recommandations de traitement. Les données sont retournées sous la forme d’un tableau de :
    * Id unique d’article - article
    * nom - nom de l’option (si existent dans le catalogue)
    * évaluation - évaluation de recommandation
    * métadonnées - une chaîne qui représente les métadonnées de l’élément

Exemple : Le code suivant demande 8 recommandations pour l’élément « 64f6eb0d-947a-4c18-a16c-888da9e228ba » (et en ne spécifiant ne pas includeMetadata - il implicitement indique qu’aucune métadonnée n’est requise), elle concaténer ensuite les résultats dans une mémoire tampon.

        <script>
            var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
                var buff = "";
                for (var ii = 0; ii < reco.length; ii++) {
                    buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
                }
                alert(buff);
            });
        </script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 
