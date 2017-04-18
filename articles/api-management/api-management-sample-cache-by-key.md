<properties
    pageTitle="La mise en cache personnalisés dans Azure API de gestion"
    description="Découvrez comment mettre en cache les éléments par la clé Gestion des API Azure"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="custom-caching-in-azure-api-management"></a>La mise en cache personnalisés dans Azure API de gestion
Service de gestion de l’API d’Azure a prise en charge intégrée pour [la mise en cache de la réponse HTTP](api-management-howto-cache.md) à l’aide de l’URL de la ressource comme clé. La clé peut être modifiée par des en-têtes de demande à l’aide de la `vary-by` propriétés. Cela est utile pour la mise en cache des réponses complètes de HTTP (également appelé représentations), mais il est parfois utile de cache seulement une partie d’une représentation. Les nouvelles stratégies de [cache-recherche-valeur](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) et [valeur de base de cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) permettent de stocker et de récupérer des éléments arbitraires de données à partir de définitions de stratégie. Cette possibilité ajoute également valeur pour introduit de [demande d’envoi](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) , car vous pouvez maintenant mettre en cache les réponses à partir des services externes.

## <a name="architecture"></a>Architecture  
Service de gestion de l’API utilise un cache de données de clients partagés afin que, mise à l’échelle de plusieurs unités, vous obtiendrez toujours accès au même les données en mémoire cache. Toutefois, lorsque vous travaillez avec un déploiement de plusieurs région, il existe des caches indépendantes dans chacune des régions. Par conséquent, il est important ne pas traiter le cache comme un magasin de données où elle est la seule source d’un élément des. Si vous avez et ultérieurement décidez de tirer parti du déploiement de plusieurs région, puis les clients avec les utilisateurs qui voyagent peuvent perdre l’accès à ces données mises en cache.

## <a name="fragment-caching"></a>La mise en cache de fragment
Il existe certains cas où les réponses renvoyées contiennent une partie des données sont coûteuse déterminer et restent encore fraîches pour un laps de temps raisonnable. Par exemple, considérons un service généré par une compagnie aérienne qui fournit des informations concernant des réservations de vols, état de vol, etc.. Si l’utilisateur est un membre du programme de points de compagnies aériennes, ils devaient également les informations relatives à leur état actuel et le kilométrage cumulé. Ces informations relatives à l’utilisateur peuvent être stockées dans un autre système, mais il peut être souhaitable d’inclure dans les réponses renvoyées concernant les réservations et l’état de vol. Cela peut être effectué à l’aide d’un processus appelé mise en cache de fragment. La représentation primaire peut être retournée à partir du serveur d’origine à l’aide d’un type de jeton pour indiquer où insérer les informations relatives à l’utilisateur. 

Pensez à la réponse JSON suivante à partir d’une API de back-end.


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

Et ressources secondaires à `/userprofile/{userid}` qui ressemble à,

     { "username" : "Bob Smith", "Status" : "Gold" }

Pour déterminer les informations utilisateur appropriées à inclure, nous devons identifier qui est l’utilisateur final. Ce mécanisme est dépend de l’implémentation. Par exemple, j’utilise le `Subject` revendication d’un `JWT` jeton. 

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Nous stockons cette `enduserid` valeur dans une variable de contexte pour une utilisation ultérieure. L’étape suivante consiste à déterminer si une demande précédente a déjà extrait les informations de l’utilisateur et stocké dans le cache. Pour cela, nous utilisons le `cache-lookup-value` stratégie.

      <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

S’il n’existe aucune entrée dans le cache qui correspond à la valeur de la clé, puis n° `userprofile` variable de contexte sera créé. Nous vérifions la réussite de la recherche à l’aide de la `choose` stratégie de flux de contrôle.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Si le `userprofile` variable de contexte n’existe pas, puis nous allons devoir effectuer une demande HTTP pour la récupérer.

    <send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
    </send-request>

Nous utilisons le `enduserid` pour construire l’URL à la ressource de profil utilisateur. Une fois la réponse, nous pouvons extraire le texte du corps de la réponse et stockez-le dans une variable de contexte.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

Pour éviter de nous avoir à rendre de nouveau, cette requête HTTP lorsque l’utilisateur effectue une autre demande, nous pouvons stocker le profil utilisateur dans le cache.

    <cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

Nous stocker la valeur dans le cache à l’aide de la même clé que nous a tenté de récupérer avec. La durée que vous choisissez de stocker la valeur doit être selon la façon dont les modifications des informations utilisateurs sans oublier la tolérance sont souvent des informations à jour. 

Il est important de réaliser que récupération à partir du cache est toujours un out-of-process, la demande réseau et potentiellement peut toujours ajouter des dizaines de millisecondes à la demande. Les avantages sont fournis pour déterminer que les informations de profil utilisateur dure plus longtemps que celle en raison d’avoir à de la base de données de requêtes ou regrouper les informations à partir de plusieurs back-ends.

La dernière étape du processus est mise à jour de la réponse retournée avec nos informations de profil utilisateur.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

J’ai choisi d’inclure les guillemets dans le cadre du jeton de sorte que même lorsque le remplacement ne se produit pas, la réponse était valide en JSON. Il s’agit principalement de faciliter le débogage.

Une fois que vous combinez toutes ces étapes, le résultat final est une stratégie qui ressemble à celle qui suit.

     <policies>
        <inbound>
            <!-- How you determine user identity is application dependent -->
            <set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

            <!--Look for userprofile for this user in the cache -->
            <cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

            <!-- If we don’t find it in the cache, make a request for it and store it -->
            <choose>
                <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                    <!—Make HTTP request to get user profile -->
                    <send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
                        <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                        <set-method>GET</set-method>
                    </send-request>

                    <!—Store response body in context variable -->
                    <set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                    <!—Store result in cache -->
                    <cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
                </when>
            </choose>
            <base />
        </inbound>
        <outbound>
            <!—Update response body with user profile-->
            <find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
            <base />
        </outbound>
     </policies>

Cette approche de mise en cache est principalement utilisée dans les sites web où HTML est composé du côté serveur afin qu’il peut être restitué sous la forme d’une seule page. Toutefois, il peut être également utile dans les API où les clients ne client côté de mise en cache HTTP, ou qu’il est souhaitable de ne pas placer cette responsabilité sur le client.

Ce même type de mise en cache de fragment peut également être effectué sur les serveurs web de back-end à l’aide d’un Redis la mise en cache de serveur, cependant, à l’aide du service de gestion de l’API pour exécuter ce travail est utile lorsque les fragments mises en cache provenant de différents back end que les réponses principales.

## <a name="transparent-versioning"></a>Versioning transparent
Il est de pratique courante pour plusieurs versions d’une implémentation différente d’une API pris en charge à la fois. Ce sera peut-être pour prendre en charge des environnements différents, tels que le développement, test, production, etc., ou il peut être prise en charge des versions antérieures de l’API pour accorder du temps pour les consommateurs d’API migrer vers des versions plus récentes. 

Une approche à gérer au lieu de demander aux développeurs de client modifier l’URL à partir de `/v1/customers` à `/v2/customers` consiste à stocker dans les données de profil du consommateur, la version de l’API actuellement souhaitent utiliser et appeler l’URL du serveur principal approprié. Pour déterminer l’URL du serveur principal correct à appeler pour un client particulier, il est nécessaire d’interroger des données de configuration. En mettant en cache les données de cette configuration, nous pouvons réduire la baisse des performances de cette recherche.

La première étape consiste à déterminer l’identificateur utilisé pour configurer la version souhaitée. Dans cet exemple, j’ai choisi d’associer la version à la clé d’inscription de produit. 

        <set-variable name="clientid" value="@(context.Subscription.Key)" />

Nous puis effectuez une recherche de cache pour voir si nous avons déjà récupéré la version client de votre choix.

        <cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

Puis nous vérifions si nous n’avons trouvé qu’il dans le cache.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">

Si nous n’avez pas nous aller et le récupérer.

    <send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
    </send-request>

Extrait le texte de réponse de la réponse.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Stocker dans le cache pour une utilisation ultérieure.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

Et enfin mettre à jour l’URL de back-end pour sélectionner la version du service souhaité par le client.

    <set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

La stratégie complètement est comme suit.

     <inbound>
        <base />
        <set-variable name="clientid" value="@(context.Subscription.Key)" />
        <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("clientversion"))">
                <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                    <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>
                <!-- Store response body in context variable -->
                <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
                <!-- Store result in cache -->
                <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
            </when>
        </choose>
        <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
    </inbound>


Qui permettent aux consommateurs d’API contrôler en toute transparence la version back-end est utilisée par les clients sans avoir à mettre à jour et de redéployer des clients est une solution élégante qui résout de nombreux problèmes de versioning d’API.

## <a name="tenant-isolation"></a>Isolation des clients

Dans les déploiements de plus grandes, mutualisées certaines sociétés créer des groupes distincts de clients sur des déploiements distincts du matériel du serveur principal. Cela permet de réduire le nombre de clients qui sont affectés par un problème matériel sur le serveur principal. Il permet également de nouvelles versions du logiciel être déployée en plusieurs étapes. Idéalement, cette architecture back-end doit être transparente pour les consommateurs de l’API. Ceci peut être réalisé de manière similaire au contrôle de version transparente car elle est basée sur la même technique de manipulation de l’URL du serveur principal à l’aide de l’état de configuration de clé API par.  

Au lieu de retourner une version par défaut de l’API pour chaque clé de l’abonnement, vous devez retourner un identificateur qui concerne un client du groupe matériel attribué. Cet identificateur peut être utilisé pour construire l’URL du serveur principal approprié.

## <a name="summary"></a>Résumé
La liberté d’utiliser le cache de la gestion des API d’Azure pour stocker tout type de données permet un accès efficace aux données de configuration qui peuvent affecter la façon dont une requête entrante est traitée. Il peut également être utilisé pour stocker des fragments de données qui peuvent augmenter les réponses renvoyées par un API de back-end.

## <a name="next-steps"></a>Étapes suivantes
Veuillez envoyez-nous vos commentaires dans le thread Disqus pour cette rubrique si d’autres scénarios que ces stratégies ont activé pour vous, ou s’il existe des scénarios vous souhaite obtenir mais ne pas sentir sont actuellement possible.
