<properties 
    pageTitle="Modèle de cluster | Microsoft Azure" 
    description="Modèle de cluster" 
    services="machine-learning" 
    documentationCenter="" 
    authors="FrancescaLazzeri" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="lazzeri"/> 


#<a name="cluster-model"></a>Modèle de cluster    

Comment nous pouvons pour prédire groupes de comportements de leur détenteur de crédit afin de réduire le risque de l’imputation des frais d’émetteurs de carte de crédit ? Comment pouvons-nous nous définir des groupes des traits de personnalité des employés afin d’améliorer leurs performances au travail ? Comment les médecins peuvent classer les patients en groupes basés sur les caractéristiques de leurs maladies ? En principe, toutes ces questions peuvent être une réponse via l’analyse du cluster.   


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
   
Analyse du cluster classifie un jeu d’observations en deux groupes ou plus qui s’excluent mutuellement inconnus basées sur les combinaisons de variables. L’objectif de l’analyse du cluster est de découvrir un système d’organisation dans des groupes, des observations, généralement des personnes ou leurs caractéristiques, où les membres des groupes ont des propriétés en commun. Ce [service](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) utilise la méthodologie de K-Means, une technique couramment utilisée de clustering, de données arbitraires dans des groupes du cluster. Ce service web prend les données et le nombre de Ko clusters comme entrée et produit des prévisions dont des groupes k à laquelle appartient chaque observations. 

>Ce service web peut être consommé par les utilisateurs – via une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également comme un exemple d’utilisation de la formation de Machine Azure pour créer des services web sur le code de R. Avec seulement quelques lignes de code de R et de clics effectués sur un bouton dans un Studio de formation de Machine Azure, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le marché d’Azure et consommé par des utilisateurs et des périphériques dans le monde entier sans configuration d’infrastructure par l’auteur du service web.  

##<a name="consumption-of-web-service"></a>Consommation du service web   
Ce service web regroupe les données d’un ensemble de groupes de k et sorties de l’affectation de groupe pour chaque ligne. Le service web s’attend à l’utilisateur final d’entrer des données sous la forme d’une chaîne où lignes sont séparées par des virgules (,) et les colonnes sont séparées par des points-virgules ( ;). Le service web attend 1 ligne à la fois. Un jeu de données d’exemple pourrait ressembler à ceci :

![Exemples de données][1]

Supposons que l’utilisateur souhaite séparer en 3 groupes qui s’excluent mutuellement. Les données d’entrée pour le groupe de données ci-dessus serait la suivante : valeur = « 10 ; 5 ; 2,18 ; 1 ; 6,7 ; 5 ; 5,22 ; 3 ; 4,12 ; 2 ; 1,10 ; 3 ; 4 » ; k = « 3 ». La sortie est l’appartenance de groupe prévue pour chacune des lignes.

>Ce service, hébergé sur le marché d’Azure, est un service OData ; Il peuvent être appelés via les méthodes POST ou GET. 

Il existe plusieurs manières de consommation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Début de code C# pour la consommation du service web :

    public class Input
    {
            public string value;
            public string k;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

À partir de formation de Machine d’Azure, un nouvel essai à blanc a été créé et les deux [Exécuter un Script R] [ execute-r-script] modules extraits sur l’espace de travail. Le schéma de données a été créé avec une simple [Exécution de Script de R][execute-r-script]. Ensuite, le schéma de données a été lié à la section de modèle de cluster, créée à nouveau avec une [Exécution de Script de R][execute-r-script]. Dans l' [Exécution de Script de R] [ execute-r-script] utilisé pour le modèle de cluster, le service web utilise ensuite la fonction « k-means », qui est prédéfinie dans l' [Exécution de Script de R] [ execute-r-script] d’apprentissage d’ordinateur Azure.    
   

     
![Flux d’expérience][3]

####<a name="module-1"></a>Module 1 : 
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
    
    maml.mapOutputPort("mydata");     
    

####<a name="module-2"></a>Module 2 :
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");
   
 
##<a name="limitations"></a>Limitations
Il s’agit d’un exemple très simple d’un service web de gestion de clusters. Comme vous pouvez le constater à partir de l’exemple de code ci-dessus, aucune interception d’erreur n’est mis en œuvre et le service suppose que tout ce qui est une variable continue (pas par catégorie de fonctionnalités autorisés), en tant que service uniquement les entrées numériques valeurs au moment de la création de ce service web. Également, le service gère actuellement taille de données limitées, dues à la nature de la demande/réponse de l’appel de service web et le fait que le modèle est en cours apte à chaque fois que le service web est appelé. 

##<a name="faq"></a>FAQ
Pour les questions fréquemment posées sur la consommation du service web ou la publication sur le marché d’Azure, consultez [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
