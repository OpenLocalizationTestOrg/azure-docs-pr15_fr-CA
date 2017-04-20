<properties
    pageTitle="À l’aide du service de gestion de l’API pour générer des requêtes HTTP"
    description="Apprenez à utiliser des stratégies de demande et de réponse dans Gestion de l’API pour appeler des services externes de votre API"
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


# <a name="using-external-services-from-the-azure-api-management-service"></a>À l’aide des services externes à partir du service de gestion d’API Azure

Les stratégies disponibles dans le service de gestion d’API Azure peuvent faire une grande variété de travail utile dépend uniquement de la requête entrante, la réponse sortante et les informations de configuration de base. Toutefois, la possibilité d’interagir avec les services externes à partir des API de gestion stratégies s’ouvre des opportunités plus nombreuses.

Nous avons déjà vu comment nous pouvons interagir avec le [service Azure événement concentrateur de journalisation, surveillance et analytique](api-management-log-to-eventhub-sample.md). Dans cet article, nous expliquera service basé sur des stratégies qui vous permettent d’interagir avec n’importe quel HTTP externe. Ces stratégies peuvent être utilisées pour le déclenchement d’événements à distance ou d’extraction d’informations qui seront utilisées pour manipuler la demande et la réponse d’une certaine manière d’origine.

## <a name="send-one-way-request"></a>Un-moyen-demande d’envoi
Éventuellement l’interaction externe la plus simple est le style feu et oubliez de demande qui permet à un service externe d’être averti d’un certain type d’événement important. Nous pouvons utiliser la stratégie de contrôle de flux `choose` pour détecter tout type de condition que nous intéresse et puis, si la condition est satisfaite, nous pouvons effectuer une demande HTTP externe à l’aide de la stratégie [un-moyen-demande d’envoi](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) . Cela peut être une demande pour un système de messagerie comme Hipchat ou marge ou une API de messagerie tels que SendGrid ou MailChimp, ou en cas d’incidents de support stratégique quelque chose comme PagerDuty. Tous ces systèmes de messagerie ont APIs HTTP simple que nous pouvons appeler facilement.

### <a name="alerting-with-slack"></a>Les alertes avec la marge
L’exemple suivant montre comment envoyer un message à une salle de conversation marge si le code d’état de réponse HTTP est supérieure ou égale à 500. Une erreur de 500 plage indique un problème avec notre back-end API client de notre API ne peut résoudre. Elle requiert généralement un certain type d’intervention de notre part.  

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

La marge a la notion de raccordements de web entrante. Lorsque vous configurez un hook web entrant, marge génère une URL spéciale qui vous permet d’effectuer une requête POST simple et de transmettre un message dans le canal de marge. Le corps JSON que vous créez est basé sur un format défini par la marge.

![Raccordement de marge Web](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Est l’incendie et oubliez pas suffisamment intéressante ?
Il existe certains compromis lors de l’utilisation d’un style d’incendie et d’oublier de demande. Si pour une raison quelconque, la demande échoue, puis l’échec n’est pas signalé. Dans ce cas particulier, la complexité de disposer une rapports système et les coûts supplémentaires de l’attente de la réponse d’erreur secondaire n’est pas justifiée. Pour les scénarios où il est indispensable de vérifier la réponse, puis la stratégie de [demande d’envoi](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) est une meilleure option.

## <a name="send-request"></a>Demande d’envoi
Le `send-request` permet de stratégie à l’aide d’un service externe pour exécuter des fonctions de traitement complexe et de renvoyer des données à la gestion de l’API de service qui peut être utilisé pour un autre traitement de la stratégie.

### <a name="authorizing-reference-tokens"></a>Autorisation des jetons de référence
Une fonction majeure de gestion de l’API protège les ressources de serveur principal. Si le serveur d’autorisation utilisé par votre API crée des [jetons de JWT](http://jwt.io/) dans le cadre de son flux de OAuth2, comme le fait [d’Azure Active Directory](../active-directory/active-directory-aadconnect.md) , vous pouvez ensuite utiliser le `validate-jwt` stratégie de vérifier la validité du jeton. Toutefois, certains serveurs de l’autorisation créent ce qu’on appelle des [jetons de référence](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) qui ne peut pas être vérifié sans un appel vers le serveur d’autorisation.

### <a name="standardized-introspection"></a>Introspection standardisée
Dans le passé n’ont été aucune méthode normalisée de vérifier un jeton de référence avec un serveur d’autorisation. Toutefois, un standard récemment proposées [RFC 7662](https://tools.ietf.org/html/rfc7662) a été publié par l’IETF qui définit comment un serveur de ressources peut vérifier la validité d’un jeton.

### <a name="extracting-the-token"></a>Extrayez le jeton
La première étape consiste à extraire le jeton de l’en-tête d’autorisation. La valeur d’en-tête doit être mis en forme avec les `Bearer` schéma d’autorisation, un espace, puis le jeton d’autorisation conformément à [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Malheureusement il existe des cas où le jeu d’autorisations est omis. Pour en tenir compte lors de l’analyse, nous fractionner la valeur d’en-tête sur un espace perso et sélectionnez la dernière chaîne du tableau de chaînes retourné. Cela fournit une solution pour les en-têtes d’autorisation mal formaté.

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### <a name="making-the-validation-request"></a>Qui effectue la demande de validation
Une fois le jeton d’autorisation, nous pouvons effectuer la demande de valider le jeton. RFC 7662 appelle l’introspection de ce processus et requiert que vous `POST` un formulaire HTML à la ressource d’introspection. Le formulaire HTML doit contenir au moins d’une paire clé/valeur avec la clé `token`. Cette demande au serveur d’autorisation doit également être authentifiée pour vous assurer que les clients malveillants ne peut pas aller chalutage de jetons.

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### <a name="checking-the-response"></a>Vérification de la réponse
Le `response-variable-name` attribut est utilisé pour accéder à la réponse renvoyée. Le nom défini dans cette propriété peut être utilisé comme clé dans le `context.Variables` dictionnaire pour accéder à la `IResponse` objet.

À partir de l’objet response, nous pouvons récupérer le corps et la RFC 7622 nous indique que la réponse doit être un objet JSON et doit contenir au moins une propriété appelée `active` qui est une valeur booléenne. Lors de la `active` a la valeur true, le jeton est considérée comme valide.

### <a name="reporting-failure"></a>Échec de création de rapports
Nous utilisons un `<choose>` stratégie pour détecter si le jeton n’est pas valide et le cas échéant, renvoyer une réponse 401.

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

Conformément à [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) qui décrit comment `bearer` jetons doivent être utilisés, nous avons également renvoyer un `WWW-Authenticate` avec la réponse 401. Le WWW-Authenticate vise à indiquer un client sur la construction d’une demande dûment autorisée. En raison de la grande variété d’approches possibles avec le framework de OAuth2, il est difficile de communiquer toutes les informations nécessaires. Heureusement, il existe des efforts en cours pour aider [les clients à découvrir comment autoriser correctement les demandes à un serveur de la ressource](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Solution de final
Placer tous les éléments ensemble, nous obtenons la stratégie suivante :

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
            <!-- Check active property in response -->
            <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
                <!-- Return 401 Unauthorized with http-problem payload -->
                <return-response response-variable-name="existing response variable">
                    <set-status code="401" reason="Unauthorized" />
                    <set-header name="WWW-Authenticate" exists-action="override">
                        <value>Bearer error="invalid_token"</value>
                    </set-header>
                </return-response>
            </when>
        </choose>
      <base />
    </inbound>

Il s’agit uniquement d’un des nombreux exemples de la façon dont `send-request` stratégie peut être utilisée pour intégrer des services externes utiles dans le processus de demandes et les réponses doivent passer à travers le service de gestion de l’API.

## <a name="response-composition"></a>Composition de la réponse
Le `send-request` stratégie peut être utilisée pour améliorer une demande principale à un système back-end, comme nous l’avons vu dans l’exemple précédent, ou il peut être utilisé comme un remplacement complet pour de l’appel de back-end. À l’aide de cette technique nous pouvons facilement créer les ressources de composite qui sont agrégées à partir de plusieurs systèmes différents.

### <a name="building-a-dashboard"></a>Création d’un tableau de bord   
Vous souhaitez parfois pouvoir pour exposer des informations qui existe dans plusieurs systèmes back-end, par exemple, pour un tableau de bord. Indicateurs de performance clés provenant de tous les principaux différents, mais vous préférez ne pas fournir un accès direct et il peut être intéressant si toutes les informations peuvent être récupérées dans une demande unique. Peut-être certaines informations back-end a besoin de certains de découpage et de découpe et d’expurgation un peu de tout d’abord ! La possibilité de mettre en cache cette ressource de composite serait un utile réduire la charge du back-end que vous savez que les utilisateurs ont une habitude de marteler la touche F5 pour voir si leurs mesures défaillant peut changer.    

### <a name="faking-the-resource"></a>Émulant la ressource
La première étape pour créer notre ressource de tableau de bord consiste à configurer une nouvelle opération dans le portail d’éditeur de gestion de l’API. Il s’agit d’une opération d’espace réservé permet de configurer de notre stratégie de composition pour créer notre ressource dynamique.

![Opération de tableau de bord](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Rendre les demandes
Une fois la `dashboard` opération a été créée nous pouvons configurer une stratégie spécifiquement pour cette opération. 

![Opération de tableau de bord](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

La première étape consiste à extraire les paramètres de la requête à partir de la demande entrante, afin que nous pouvons transmettre à notre back-end. Dans cet exemple notre tableau de bord affiche des informations basées sur une période donnée une a par conséquent une `fromDate` et `toDate` paramètre. Nous pouvons utiliser le `set-variable` stratégie pour extraire les informations à partir de l’URL de requête.

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

Une fois ces informations nous pouvons faire des demandes pour tous les systèmes back-end. Chaque requête construit une nouvelle URL avec les informations de paramètre et appelle son propre serveur et stocke la réponse dans une variable de contexte.

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

Ces demandes seront exécuteront en séquence, ce qui n’est pas idéal. Dans une prochaine version nous lancerons une nouvelle stratégie nommée `wait` qui permettra à toutes ces demandes à exécuter en parallèle.

### <a name="responding"></a>Répondre

Pour construire la réponse composite, nous pouvons utiliser la stratégie de [retour-réponse](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) . Le `set-body` élément peut utiliser une expression pour générer une nouvelle `JObject` avec toutes les représentations de composant incorporées en tant que propriétés.

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

La stratégie complète se présente comme suit :

    <policies>
        <inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
    </policies>

Dans la configuration de l’espace réservé opération que nous pouvons configurer la ressource du tableau de bord pour être mis en cache pendant au moins une heure, nous sommes conscients que la nature des données signifie que même si elle est d’une heure à jour, qu'il sera toujours suffisamment efficace transmettre des informations importantes pour les utilisateurs.

## <a name="summary"></a>Résumé
Service de gestion de l’API d’Azure permet des stratégies flexibles qui peuvent être appliqués de manière sélective le trafic HTTP et composition de services principaux. Si vous souhaitez améliorer votre passerelle API avec déclenchement d’alerte de fonctions, la vérification des fonctionnalités de validation ou de créer de nouvelles ressources de composites basées sur plusieurs services back-end, la `send-request` et les stratégies associées ouvrir à un monde de possibilités.

## <a name="watch-a-video-overview-of-these-policies"></a>Regarder une vidéo de présentation de ces stratégies.
Pour plus d’informations sur l' [envoi-un-moyen-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) [-demande d’envoi](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)et les stratégies de [retour-réponse](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) abordés dans cet article, veuillez consulter la vidéo suivante.

> [AZURE.VIDEO send-request-and-return-response-policies]
