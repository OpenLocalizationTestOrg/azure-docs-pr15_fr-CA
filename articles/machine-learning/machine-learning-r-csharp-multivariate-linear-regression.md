<properties 
    pageTitle="MULTIVARIÉE régression linéaire | Microsoft Azure" 
    description="MULTIVARIÉE régression linéaire" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="multivariate-linear-regression"></a>MULTIVARIÉE régression linéaire   
 

 
Supposons que vous avez un groupe de données et que vous souhaitez rapidement prédire une variable dépendante (y), pour chaque individu (i) en fonction de variables indépendantes. Régression linéaire est une technique statistique les plus courants utilisée pour ces prévisions. Ici la variable dépendante y est supposé pour être une valeur continue.  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

Un scénario simple serait où l’Organise-notes tente de prédire le poids d’un individu (y) en fonction de leur hauteur (x). Un scénario plus avancé pourrait être où l’Organise-notes a des informations supplémentaires pour la personne (par exemple, poids, sexe, race) et tente de deviner le poids de la personne. Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) correspond au modèle de régression linéaire pour les données et renvoie la valeur prévue (y) pour chacun des observations dans les données.

>Ce service web peut être consommé par les utilisateurs – via une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également comme un exemple d’utilisation de la formation de Machine Azure pour créer des services web sur le code de R. Avec seulement quelques lignes de code de R et de clics effectués sur un bouton dans un Studio de formation de Machine Azure, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le marché d’Azure et consommé par des utilisateurs et des périphériques dans le monde entier sans configuration d’infrastructure par l’auteur du service web.  

##<a name="consumption-of-web-service"></a>Consommation du service web  
Ce service web donne les valeurs prévues de la variable dépendante basée sur les variables indépendantes pour toutes les observations. Le service web s’attend à l’utilisateur final d’entrer des données sous la forme d’une chaîne où lignes sont séparées par des virgules (,) et les colonnes sont séparées par des points-virgules ( ;). Le service web attend 1 ligne à la fois et attend la première colonne à la variable dépendante. Un jeu de données d’exemple pourrait ressembler à ceci :

![Exemples de données][1]

Observations sans une variable dépendante doivent être entrées en tant que « NA » pour y. Les données d’entrée pour le groupe de données ci-dessus serait la chaîne suivante : « 10 ; 5 2,18 ; 1 ; 6,6 ; 5.3 ; 2.1,7 ; 5 ; 5,22 ; 3 ; 4,12 ; 2 ; 1, NA ; 3 ; 4 ». La sortie est la valeur prévue pour chacune des lignes selon les variables indépendantes. 

>Ce service, hébergé sur le marché d’Azure, est un service OData ; Il peuvent être appelés via les méthodes POST ou GET. 

Il existe plusieurs manières de consommation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Début de code C# pour la consommation du service web :

    public class Input
    {
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




##<a name="creation-of-web-service"></a>Création du service web  
>Ce service web a été créé à l’aide de la formation de Machine Azure. Pour une version d’évaluation gratuite, ainsi que des vidéos d’introduction sur la création d’expériences et [publication de services web](machine-learning-publish-a-machine-learning-web-service.md), consultez le site [azure.com/ml](http://azure.com/ml). Voici une capture d’écran de l’expérience qui a créé le code d’exemple et de service web pour chacun des modules au sein de l’expérimentation.


À partir de formation de Machine d’Azure, un nouvel essai à blanc a été créé et les deux [Exécuter un Script R] [ execute-r-script] modules ont été extraits sur l’espace de travail. Ce service web exécute une expérience d’apprentissage automatique de Azure avec un script R sous-jacent. Il existe 2 parties à cette expérience : définition de schéma, modèle de formation et de notation. Le premier module définit la structure attendue du dataset d’entrée, où la première variable est la variable dépendante et les autres variables sont indépendantes. Le deuxième module correspond à un modèle générique de régression linéaire pour les données d’entrée.  
  
![Flux d’expérience][3]

####<a name="module-1"></a>Module 1 :
 
####<a name="schema-definition"></a>Définition de schéma  
    data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####<a name="module-2"></a>Module 2 :
####<a name="lm-modeling"></a>Modélisation de LM   
    data <- maml.mapInputPort(1) # class: data.frame  
  
    data.split <- strsplit(data[1,1], ",")[[1]]  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- as.data.frame(t(data.split)) 
    data.split <- data.matrix(data.split) 
    data.split <- data.frame(data.split) 
    model <- lm(data.split)  

    out=data.frame(predict(model,data.split))  
    out <- data.frame(t(out))

    maml.mapOutputPort("out");  
 
##<a name="limitations"></a>Limitations
Il s’agit d’un exemple très simple d’un service web de plusieurs régression linéaire. Comme vous pouvez le constater à partir de l’exemple de code ci-dessus, aucune interception d’erreur n’est mis en œuvre et le service suppose que tout ce qui est une variable continue (pas par catégorie de fonctionnalités autorisés), en tant que service uniquement les entrées numériques valeurs au moment de la création de ce service web. Également, le service gère actuellement taille de données limitées, dues à la nature de la demande/réponse de l’appel de service web et le fait que le modèle est en cours apte à chaque fois que le service web est appelé. 

##<a name="faq"></a>FAQ
Pour les questions fréquemment posées sur la consommation du service web ou la publication sur le marché d’Azure, consultez [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
