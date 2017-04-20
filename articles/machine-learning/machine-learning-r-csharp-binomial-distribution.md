<properties 
    pageTitle="Distribution binomiale Suite | Microsoft Azure" 
    description="Distribution binomiale Suite" 
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


#<a name="binomial-distribution-suite"></a>Distribution binomiale Suite




La Suite de la Distribution binomiale est un ensemble de services web exemple ([Générateur binomiale](https://datamarket.azure.com/dataset/aml_labs/bdg5), [Probabilité calculatrice]( https://datamarket.azure.com/dataset/aml_labs/bdp4) [Calculatrice de Quantile]( https://datamarket.azure.com/dataset/aml_labs/bdq5)) qui vous permettent de générer et de gérer les distributions binomiale. Les services permettent de générer une séquence de distribution binomiale de n’importe quelle longueur, calcul étendue de donnée probabilité et calcul de probabilité à partir d’un quantile donné. Chacun des services émet des sorties différentes selon le service sélectionné (voir description ci-dessous). La Suite de la Distribution binomiale est basée sur la R fonctions qbinom, rbinom et pbinom, qui sont inclus dans le package de statistiques R. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Ces services web peuvent être consommés par les utilisateurs – potentiellement directement sur le marché, via une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également comme un exemple d’utilisation de la formation de Machine Azure pour créer des services web sur le code de R. Avec seulement quelques lignes de code de R et de clics effectués sur un bouton dans un Studio de formation de Machine Azure, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le marché d’Azure et consommé par des utilisateurs et des périphériques dans le monde entier – n’exige aucune configuration de l’infrastructure par l’auteur du service web.

##<a name="consumption-of-web-service"></a>Consommation du service web
La Suite de la Distribution binomiale inclut les services suivants de 3.

###<a name="binomial-distribution-quantile-calculator"></a>Calculatrice de Quantile loi binomiale
Ce service accepte des 4 arguments d’une distribution normale et calcule le quantile associé.
Les arguments d’entrée sont les suivantes :

- p - une seule agrégées probabilité de plusieurs essais.  
- taille - le nombre d’essais.
- prob - la probabilité de réussite d’une version d’évaluation.
- Côté - L du côté inférieur de la distribution, U du côté supérieur de la distribution. 

La sortie du service est le quantile calculé qui est associé à la probabilité donnée.

###<a name="binomial-distribution-probability-calculator"></a>Calculatrice de probabilité binomiale
Ce service accepte des 4 arguments d’une distribution binomiale et calcule la probabilité associée.
Les arguments d’entrée sont les suivantes :

- q-quantile unique d’un événement avec la distribution binomiale. 
- taille - le nombre d’essais.
- prob - la probabilité de réussite d’une version d’évaluation.
- côté - L du côté inférieur de la distribution, U du côté supérieur de la distribution, ou E qui est égal à un nombre unique de succès.

La sortie du service est la probabilité calculée qui est associée avec le quantile donné.

###<a name="binomial-distribution-generator"></a>Distribution binomiale Générateur
Ce service accepte des 3 arguments d’une distribution binomiale et génère une séquence aléatoire de nombres distribués binomially. Les arguments suivants doivent être qui lui sont fournies dans la demande :

- n - nombre d’observations. 
- taille - nombre d’essais.
- probabilité - probabilité de réussite.

La sortie du service est une séquence de longueur n, avec une distribution binomiale basée sur les arguments de taille et de la probabilité.

>Ce service, hébergé sur le marché d’Azure, est un service OData ; Il peuvent être appelés via les méthodes POST ou GET. 

Il existe plusieurs manières de consommation du service de manière automatique (exemples d’applications sont ici : [Générateur](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx), [Probabilité calculatrice](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx), [Calculatrice de Quantile](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)). 

###<a name="starting-c-code-for-web-service-consumption"></a>Début de code C# pour la consommation du service web :

###<a name="binomial-distribution-quantile-calculator"></a>Calculatrice de Quantile loi binomiale
    public class Input
    {
            public string p;
            public string size;
            public string prob;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void main()
    {
            var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="binomial-distribution-probability-calculator"></a>Calculatrice de probabilité binomiale
    public class Input
    {
            public string q;
            public string size;
            public string prob;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="binomial-distribution-generator"></a>Distribution binomiale Générateur
    public class Input
    {
            public string n;
            public string size;
            public string p;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
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

###<a name="binomial-distribution-quantile-calculator"></a>Calculatrice de Quantile loi binomiale

![Créer l’espace de travail][4]

####<a name="module-1"></a>Module 1 :
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####<a name="module-2"></a>Module 2 :

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }

    if (param$prob < 0 ) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 0
    } else if (param$prob > 1) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
    band=subset(df,x>quantile)
    } else if (param$side =='L') {
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
    band=subset(df,x<=quantile)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###<a name="binomial-distribution-probability-calculator"></a>Calculatrice de probabilité binomiale

![Créer l’espace de travail][5]

####<a name="module-1"></a>Module 1 :

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####<a name="module-2"></a>Module 2 :
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
    prob = 1 - prob
    band=subset(df,x>param$q)
    } else if (param$side =='E') {
    prob = prob.eq
    band=subset(df,x==param$q)
    } else if (param$side =='L') {
    prob = prob
    band=subset(df,x<=param$q)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###<a name="binomial-distribution-generator"></a>Distribution binomiale Générateur

![Créer l’espace de travail][6]

####<a name="module-1"></a>Module 1 :

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####<a name="module-2"></a>Module 2 :
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>Limitations 
Il s’agit d’exemples très simples qui entourent la distribution binomiale. Comme vous pouvez le constater à partir de l’exemple de code ci-dessus, l’interception des erreurs peu est implémentée.

##<a name="faq"></a>FAQ
Pour les questions fréquemment posées sur la consommation du service web ou la publication sur le marché d’Azure, consultez [ici](machine-learning-marketplace-faq.md).


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png
 
