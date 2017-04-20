<properties 
    pageTitle="La Suite Service Distribution normale Web | Microsoft Azure" 
    description="Suite de Service Web de Distribution normale" 
    services="machine-learning" 
    documentationCenter="" 
    authors="ireiter" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="ireiter"/> 

#<a name="normal-distribution-suite"></a>Suite de la Distribution normale



La Suite de la Distribution normale est un ensemble de services web exemple ([Générateur]( https://datamarket.azure.com/dataset/aml_labs/ndg7), [Quantile calculatrice]( https://datamarket.azure.com/dataset/aml_labs/ndq5), [Calculatrice de probabilité]( https://datamarket.azure.com/dataset/aml_labs/ndp5)) qui vous permettent de générer et gérer les distributions normal. Les services permettent de générer une séquence de distribution normale de n’importe quelle longueur, le calcul de l’étendue d’une probabilité donnée et calcul de probabilité à partir d’un quantile donné. Chacun des services émet des sorties différentes selon le service sélectionné (voir description ci-dessous). La Suite de la Distribution normale est basée sur la R fonctions qnorm, rnorm et pnorm, qui sont inclus dans le package de statistiques R.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Ce service web peut être consommé par les utilisateurs – via une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également comme un exemple d’utilisation de la formation de Machine Azure pour créer des services web sur le code de R. Avec seulement quelques lignes de code de R et de clics effectués sur un bouton dans un Studio de formation de Machine Azure, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le marché d’Azure et consommé par des utilisateurs et des périphériques dans le monde entier sans configuration d’infrastructure par l’auteur du service web.  
 

##<a name="consumption-of-web-service"></a>Consommation du service web
La Suite de la Distribution normale inclut les services suivants de 3.

###<a name="normal-distribution-quantile-calculator"></a>Calculatrice de Quantile de Distribution normale
Ce service accepte des 4 arguments d’une distribution normale et calcule le quantile associé.

Les arguments d’entrée sont les suivantes :

* p - une seule probabilité d’un événement avec une distribution normale. 
* Moyenne - moyenne distribution normale.
* SD - l’écart de distribution normale. 
* Côté - L du côté inférieur de la distribution et U du côté supérieur de la distribution.

La sortie du service est le quantile calculé qui est associé à la probabilité donnée.

###<a name="normal-distribution-probability-calculator"></a>Calculatrice de probabilité de répartition normale
Ce service accepte des 4 arguments d’une distribution normale et calcule la probabilité associée.

Les arguments d’entrée sont les suivantes :

* q-quantile unique d’un événement avec une distribution normale. 
* Moyenne - moyenne distribution normale.
* SD - l’écart de distribution normale. 
* Côté - L du côté inférieur de la distribution et U du côté supérieur de la distribution.

La sortie du service est la probabilité calculée qui est associée avec le quantile donné.

###<a name="normal-distribution-generator"></a>Générateur de Distribution normale
Ce service accepte des 3 arguments d’une distribution normale et génère une séquence aléatoire de nombres qui sont distribuées normalement. Les arguments suivants doivent être qui lui sont fournies dans la demande :

* n - le nombre d’observations. 
* Moyenne - moyenne distribution normale.
* SD - l’écart de distribution normale. 

La sortie du service est une séquence de longueur n, avec une distribution normale basée sur les arguments moyenne et sd.

>Ce service, hébergé sur le marché d’Azure, est un service OData ; Il peuvent être appelés via les méthodes POST ou GET. 

Il existe plusieurs manières de consommation du service de manière automatique (exemples d’applications sont ici : [Générateur](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx), [Probabilité calculatrice](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx), [Calculatrice de Quantile](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Début de code C# pour la consommation du service web :

###<a name="normal-distribution-quantile-calculator"></a>Calculatrice de Quantile de Distribution normale
    public class Input
    {
            public string p;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="normal-distribution-probability-calculator"></a>Calculatrice de probabilité de répartition normale
    public class Input
    {
            public string q;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="normal-distribution-generator"></a>Générateur de Distribution normale
    public class Input
    {
            public string n;
            public string mean;
            public string sd;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
>Ce service web a été créé à l’aide de la formation de Machine Azure. Pour une version d’évaluation gratuite, ainsi que des vidéos d’introduction sur la création d’expériences et [publication de services web](machine-learning-publish-a-machine-learning-web-service.md), consultez le site [azure.com/ml](http://azure.com/ml). 

Voici une capture d’écran de l’expérience qui a créé le code d’exemple et de service web pour chacun des modules au sein de l’expérimentation.

###<a name="normal-distribution-quantile-calculator"></a>Calculatrice de Quantile de Distribution normale

Déroulement de l’essai :

![Flux d’expérience][2]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }
    q = qnorm(param$p,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    q = 2* param$mean - q
    } else if (param$side =='L') {
    q = q
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(q)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-probability-calculator"></a>Calculatrice de probabilité de répartition normale
Déroulement de l’essai :

![Flux d’expérience][3]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(q=-1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    prob = pnorm(param$q,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    prob = 1 - prob
    } else if (param$side =='B') {
    prob = ifelse(prob<=0.5,prob * 2, 1)
    } else if (param$side =='L') {
    prob = prob
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-generator"></a>Générateur de Distribution normale
Déroulement de l’essai :

![Flux d’expérience][4]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,mean=0,sd=1);
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    dist = rnorm(param$n,mean=param$mean,sd=param$sd)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>Limitations 

Il s’agit d’exemples très simples qui entourent la distribution normale. Comme vous pouvez le constater à partir de l’exemple de code ci-dessus, l’interception des erreurs peu est implémentée.

##<a name="faq"></a>FAQ
Pour les questions fréquemment posées sur la consommation du service web ou la publication sur le marché d’Azure, consultez [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
 
