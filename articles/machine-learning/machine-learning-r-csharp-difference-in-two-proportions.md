<properties 
    pageTitle="Différence de Proportions Test | Microsoft Azure" 
    description="Différence de Proportions Test" 
    services="machine-learning" 
    documentationCenter="" 
    authors="aniedea" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="aniedea"/> 


#<a name="difference-in-proportions-test"></a>Différence de Proportions Test


Sont deux des proportions statistiquement différents ? Supposons qu’un utilisateur souhaite comparer deux films pour déterminer si un film a une proportion beaucoup plus élevée de 'aime' quand par rapport à l’autre. Avec un échantillon de grande taille, il peut exister une différence statistiquement significative entre les proportions 0,50 et 0.51. Avec un petit échantillon, il est peut-être pas suffisamment de données pour déterminer si ces proportions sont réellement différentes. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/prop_test) effectue un test d’hypothèse de la différence entre deux proportions en fonction de l’entrée utilisateur dans le nombre de succès et le nombre total d’essais pour les groupes de 2 comparaison. Dans un scénario possible, ce service web peut être appelé à partir d’une application de comparaison de film, qui informe l’utilisateur si un des films est vraiment 'aimé' plus souvent que les autres, basé sur la classification des films.

>Ce service web peut être consommé par les utilisateurs – via une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également comme un exemple d’utilisation de la formation de Machine Azure pour créer des services web sur le code de R. Avec seulement quelques lignes de code de R et de clics effectués sur un bouton dans un Studio de formation de Machine Azure, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le marché d’Azure et consommé par des utilisateurs et des périphériques dans le monde entier sans configuration d’infrastructure par l’auteur du service web.


##<a name="consumption-of-web-service"></a>Consommation du service web

Ce service accepte des 4 arguments et une hypothèse teste des proportions.

Les arguments d’entrée sont les suivantes :

* Successes1 - nombre d’événements de succès de l’exemple 1.
* Successes2 - nombre d’événements de succès de l’exemple 2.
* Total1 - taille de l’échantillon 1.
* Total2 - taille de l’exemple 2.

La sortie du service est le résultat de l’hypothèse de test avec la statistique, df, appel p-valeur et la quote-part dans les limites de 1/2 et l’intervalle de confiance exemple.

>Ce service, hébergé sur le marché d’Azure, est un service OData ; Il peuvent être appelés via les méthodes POST ou GET. 

Il existe plusieurs manières de consommation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Début de code C# pour la consommation du service web :

    public class Input
    {
            public string successes1;
            public string successes2;
            public string total1;
            public string total2;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
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

À partir de formation de Machine d’Azure, un nouvel essai à blanc a été créé avec deux [Exécuter un Script R] [ execute-r-script] modules. Dans le premier module que du schéma de données est défini, tandis que la deuxième module utilise la commande prop.test dans R pour effectuer le test d’hypothèse pour 2 proportions. 


###<a name="experiment-flow"></a>Déroulement de l’essai :

![Flux d’expérience][2]


####<a name="module-1"></a>Module 1 :
    ####Schema definition  
    data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
    maml.mapOutputPort("data.set"); #send data to output port
    dataset1 = maml.mapInputPort(1) #read data from input port
    

####<a name="module-2"></a>Module 2 :

    test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

    result=data.frame(
    result=ifelse(test$p.value<0.05,"The proportions are different!",
    "The proportions aren't different statistically."),
    ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
    pvalue=round(test$p.value,4),
    proportion1=round(test$estimate[1],4),
    proportion2=round(test$estimate[2],4),
    confintlow=round(test$conf.int[1],4),
    confinthigh=round(test$conf.int[2],4)) 

    maml.mapOutputPort("result"); #output port
    

##<a name="limitations"></a>Limitations 

Il s’agit d’un exemple très simple pour un test de la différence dans des proportions de 2. Comme vous pouvez le constater à partir de l’exemple de code ci-dessus, aucune interception d’erreur n’est mis en œuvre et le service suppose que toutes les variables sont continues.

##<a name="faq"></a>FAQ
Pour les questions fréquemment posées sur la consommation du service web ou la publication sur le marché d’Azure, consultez [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
