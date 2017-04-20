<properties 
    pageTitle="Prévisions : AUTOREGRESSIVE a intégré une moyenne mobile (ARIMA) | Microsoft Azure" 
    description="Prévisions - AUTOREGRESSIVE a intégré la moyenne mobile (ARIMA)" 
    services="machine-learning" 
    documentationCenter="" 
    authors="yijichen" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="yijichen"/> 

 
#<a name="forecasting---autoregressive-integrated-moving-average-arima"></a>Prévisions - AUTOREGRESSIVE a intégré la moyenne mobile (ARIMA)

Ce [service]( https://datamarket.azure.com/dataset/aml_labs/arima) implémente AUTOREGRESSIVE a intégré le déplacement moyen (ARIMA) afin de générer des prévisions basées sur les données historiques fournies par l’utilisateur. La demande pour un produit spécifique augmente cette année ? Puis-je prévoir des ventes de mes produits pour Noël, afin que je peux ainsi planifier efficacement mon stock ? Modèles de prévision sont susceptibles de répondre à ces questions. Ces modèles étant donné les anciennes données, examinent les tendances cachées et caractère saisonnier pour prévoir les tendances pour l’avenir. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

>Ce service web peut être consommé par les utilisateurs – via une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également comme un exemple d’utilisation de la formation de Machine Azure pour créer des services web sur le code de R. Avec seulement quelques lignes de code de R et de clics effectués sur un bouton dans un Studio de formation de Machine Azure, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le marché d’Azure et consommé par des utilisateurs et des périphériques dans le monde entier sans configuration d’infrastructure par l’auteur du service web.

##<a name="consumption-of-web-service"></a>Consommation du service web 

Ce service accepte des 4 arguments et calcule les prévisions de ARIMA.
Les arguments d’entrée sont les suivantes :

* Fréquence : indique la fréquence des données brutes (quotidiennes/hebdomadaires/mensuelle/trimestrielle/annuel).
* Horizon - futur prévision de délais.
* Date : ajouter les nouvelles données de série heure pour l’heure.
* La valeur - ajouter les nouvelles valeurs de données de série de temps.

La sortie du service est les prévision et les valeurs calculées. 

Exemple d’entrée peut être : 

* Fréquence - 12
* Horizon - 12
* Date - 1/15/2012 2/15/2012 ; 3/15/2012 ; 4/15/2012 ; 5/15/2012 ; 6/15/2012 ; 7/15/2012 ; 8 / 15/2012 ; 9/15/2012 10/15/2012 ; 15/11/2012 ; 15/12/2012 ; 1/15/2013 2/15/2013 ; 3/15/2013 ; 4/15/2013 ; 5/15/2013 ; 6/15/2013 ; 7/15/2013 ; 8 / 15/2013 ; 9/15/2013 10/15/2013 ; 15/11/2013 ; 15/12/2013 ; 1/15/2014 ; 2/15/2014 ; 3/15/2014 ; 4/15/2014 ; 5/15/2014 ; 6/15/2014 ; 7/15/2014 ; 8 / 15/2014 ; 9/15/2014.
* Valeur - 3.479 ; 3.68 ; 3.832 ; 3.941 ; 3.797 ; 3.586 ; 3.508 ; 3.731 ; 3.915 ; 3.844 ; 3.634 ; 3.549 ; 3.557 ; 3.785 ; 3.782 ; 3.601 ; 3.544 ; 3.556 ; 3.65 ; 3.709 ; 3.682 ; 3.511 ; 3.429 3.51 ; 3.523 ; 3.525 ; 3.626 ; 3.695 ; 3.711 ; 3.711 ; 3.693 ; 3.571 ; 3.509
 
>Ce service, hébergé sur le marché d’Azure, est un service OData ; Il peuvent être appelés via les méthodes POST ou GET. 

Il existe plusieurs manières de consommation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/ArimaForecasting.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Début de code C# pour la consommation du service web :

    public class Input
    {
        public string frequency;
        public string horizon;
        public string date;
        public string value;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
         byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
         return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

       
    void Main()
    {
        var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
        var json = JsonConvert.SerializeObject(input);
        var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
           
        var httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        var query = httpClient.PostAsync(acitionUri,new StringContent(json));
        var result = query.Result.Content;
        var scoreResult = result.ReadAsStringAsync().Result;
    }

##<a name="creation-of-web-service"></a>Création du service web 

>Ce service web a été créé à l’aide de la formation de Machine Azure. Pour une version d’évaluation gratuite, ainsi que des vidéos d’introduction sur la création d’expériences et [publication de services web](machine-learning-publish-a-machine-learning-web-service.md), consultez le site [azure.com/ml](http://azure.com/ml). Voici une capture d’écran de l’expérience qui a créé le code d’exemple et de service web pour chacun des modules au sein de l’expérimentation.

À partir de formation de Machine d’Azure, un nouvel essai à blanc a été créé. Exemple d’entrée de données a été téléchargée avec un schéma de données prédéfinis. Liée au schéma de données est une [Exécution de Script de R] [ execute-r-script] module, ce qui génère le ARIMA de modèle de prévision à l’aide de fonctions de « auto.arima » et « prévisions » sur R. 

###<a name="experiment-flow"></a>Déroulement de l’essai :

![Créer l’espace de travail][2]

####<a name="module-1"></a>Module 1 :
 
    # Add in the CSV file with the data in the format shown below 
![Créer l’espace de travail][3]  

####<a name="module-2"></a>Module 2 :
    # data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)
    
    # preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]
    
    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)
    
    # fit a time-series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- auto.arima(train_ts)
    train_model <- forecast(fit1, h = data$horizon)
    plot(train_model)
    
    # produce forecasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
    
    # data output
    maml.mapOutputPort("data.forecast");


##<a name="limitations"></a>Limitations 

Il s’agit d’un exemple très simple pour les prévisions de ARIMA. Comme vous pouvez le constater à partir de l’exemple de code ci-dessus, aucune interception d’erreur n’est mis en œuvre et le service suppose que toutes les variables sont des valeurs de positif en continu et la fréquence doit être un entier supérieur à 1. La longueur des valeur de date et les vecteurs doit être les mêmes. La variable date doit respecter le format « mm/jj/aaaa ».

##<a name="faq"></a>FAQ
Pour les questions fréquemment posées sur la consommation du service web ou la publication sur le marché, voir [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-arima/arima-img1.png
[2]: ./media/machine-learning-r-csharp-arima/arima-img2.png
[3]: ./media/machine-learning-r-csharp-arima/arima-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
