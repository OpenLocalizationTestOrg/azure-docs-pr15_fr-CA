<properties 
    pageTitle="Lexique basé Sentiment analyse | Microsoft Azure" 
    description="Lexique basé Sentiment analyse" 
    services="machine-learning" 
    documentationCenter="" 
    authors="pengxia" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="pengxia"/> 



#<a name="lexicon-based-sentiment-analysis"></a>Lexique basé Sentiment analyse 

Comment pouvez-vous mesurer attitude des marques ou des rubriques dans les réseaux sociaux en ligne et les avis des utilisateurs tels que Facebook publie, Tweet, révision, etc.. ? Analyse de sentiment fournit une méthode pour l’analyse de ces questions.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Il existe généralement deux méthodes pour l’analyse de sentiment. Un utilise un algorithme d’apprentissage de mise sous contrôle, et l’autre peut être considéré comme apprentissage. Un algorithme d’apprentissage contrôlés crée généralement un modèle de classification sur un corpus annote volumineux. Sa précision est principalement basée sur la qualité de l’annotation, et généralement le processus de formation prendra beaucoup de temps. En outre, lorsque nous appliquons l’algorithme pour un autre domaine, le résultat est généralement pas utile. Par rapport aux supervisé utilise d’apprentissage apprentissage, basé sur le lexique, un dictionnaire sentiment, qui ne nécessite pas stocker un corpus de données volumineuses et de formation, qui rend le processus plus rapide. 

Notre [service](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) est construit sur le lexique de subjectivité de MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), qui est un des lexiques de subjectivité plus couramment utilisés. Il existe 5097 mots positifs négatifs et 2533 dans MPQA. Et tous ces mots sont annotées comme polarité forte ou faible. Le corpus complet est sous licence publique générale GNU. Le service web peut être appliqué à des phrases, des publications de Facebook Tweet. 

>Ce service web peut être consommé par les utilisateurs – via une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également comme un exemple d’utilisation de la formation de Machine Azure pour créer des services web sur le code de R. Avec seulement quelques lignes de code de R et de clics effectués sur un bouton dans un Studio de formation de Machine Azure, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le marché d’Azure et consommé par des utilisateurs et des périphériques dans le monde entier sans configuration d’infrastructure par l’auteur du service web.

##<a name="consumption-of-web-service"></a>Consommation du service web

Les données d’entrée peuvent être n’importe quel texte, mais que le service web fonctionne mieux avec des phrases courtes. Le résultat est une valeur numérique comprise entre -1 et 1. Toute valeur inférieure à 0 indique que le sentiment du texte est négative ; positif si supérieur à 0. La valeur absolue du résultat indique l’intensité du sentiment associé. 

>Ce service, hébergé sur le marché d’Azure, est un service OData ; Il peuvent être appelés via les méthodes POST ou GET. 

Il existe plusieurs manières de consommation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/)).

###<a name="starting-c-code-for-web-service-consumption"></a>Début de code C# pour la consommation du service web :

    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();
    
                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



L’entrée « est aujourd'hui une bonne journée. » La sortie est « 1 », qui indique un sentiment positif associé à la phrase d’entrée. 

##<a name="creation-of-web-service"></a>Création du service web
>Ce service web a été créé à l’aide de la formation de Machine Azure. Pour une version d’évaluation gratuite, ainsi que des vidéos d’introduction sur la création d’expériences et [publication de services web](machine-learning-publish-a-machine-learning-web-service.md), consultez le site [azure.com/ml](http://azure.com/ml). Voici une capture d’écran de l’expérience qui a créé le code d’exemple et de service web pour chacun des modules au sein de l’expérimentation.


À partir de formation de Machine d’Azure, un nouvel essai à blanc a été créé. La figure ci-dessous illustre le flux de l’expérience d’analyse de sentiment de basé sur le lexique. Le fichier « sent_dict.csv » est le lexique de subjectivité MPQA et est défini comme une des deux entrées [d’Exécuter un Script R][execute-r-script]. Une autre entrée est une analyse échantillonnée du DataSet de révision Amazon pour test, où nous avons effectué la sélection, la modification du nom de colonne et opérations de fractionnement. Un package de hachage nous permet de stocker le lexique de subjectivité dans la mémoire et accélérer le processus de calcul du score. Le texte complet sera sous forme de jetons par lot de « tm » et comparé le mot dans le dictionnaire de sentiment. Enfin, un score est calculé en ajoutant le poids de chaque mot subjective dans le texte. 

###<a name="experiment-flow"></a>Déroulement de l’essai :

![tester le flux][2]


####<a name="module-1"></a>Module 1 :
    
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # <a name="install-hash-package"></a>Installez le package de hachage
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }
          
        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
    


##<a name="limitations"></a>Limitations

Du point de vue de l’algorithme, l’analyse basée sur le lexique le sentiment est un outil d’analyse sentiment général, qui ne peut pas effectuer de meilleures performances que la méthode de classification pour des champs spécifiques. Le problème de négation n’est pas correctement traité. Nous coder en dur une négation plusieurs mots dans notre programme, mais le mieux est à l’aide d’un dictionnaire de négation et certaines règles de génération. Le service web fonctionne mieux sur les phrases courts et simples, tels que des articles sur Facebook et de tweet que phrases longues et complexes tels que les revues de Amazon. 

##<a name="faq"></a>FAQ
Pour les questions fréquemment posées sur la consommation du service web ou la publication sur le marché d’Azure, consultez [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 
