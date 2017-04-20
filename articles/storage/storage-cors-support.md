<properties
    pageTitle="La prise en charge d’origine croisée ressource partage (CORS) | Microsoft Azure"
    description="Découvrez comment activer la prise en charge de CORS pour les Services de stockage de Microsoft Azure."
    services="storage"
    documentationCenter=".net"
    authors="cbrooks"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="cbrooks"/>

# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Prise en charge (CORS) pour les Services de stockage Azure le partage des ressources entre-origine

Depuis la version 2013-08-15, les services de stockage Azure prend en charge le partage de ressources entre origine (CORS) pour les Blob, Table, file d’attente, services de fichiers et. CORS est une fonctionnalité HTTP qui permet à une application web en cours d’exécution dans un domaine d’accéder aux ressources dans un autre domaine. Navigateurs Web implémentent une restriction de sécurité appelée [stratégie de même origine](http://www.w3.org/Security/wiki/Same_Origin_Policy) qui empêche une page web à partir de l’appel d’API dans un autre domaine. CORS offre un moyen sécurisé pour permettre à un domaine (le domaine d’origine) appeler des API dans un autre domaine. Consultez la [spécification de CORS](http://www.w3.org/TR/cors/) pour plus de détails sur les CORS.

Vous pouvez définir des règles CORS individuellement pour chacun des services de stockage, en appelant de [Définir les propriétés d’objet Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [Définir les propriétés de file d’attente de Service](https://msdn.microsoft.com/library/hh452232.aspx)et [Définir les propriétés de Table Service](https://msdn.microsoft.com/library/hh452240.aspx). Une fois que vous définissez les règles CORS pour le service, une demande authentifiée correctement effectuée sur le service à partir d’un autre domaine sera évaluée pour déterminer si elle est autorisée selon les règles que vous avez spécifié.

>[AZURE.NOTE] Notez que CORS n’est pas un mécanisme d’authentification. Toute demande effectuée sur une ressource de stockage lorsque CORS est activée doit avoir une signature d’authentification correcte, ou doit être effectuée par rapport à une ressource publique.

## <a name="understanding-cors-requests"></a>Présentation des demandes CORS

Une demande CORS à partir d’un domaine d’origine peut comprendre deux demandes distinctes :

- Une demande de contrôle en amont, qui interroge les restrictions CORS imposées par le service. La demande de contrôle en amont est requise à moins que la méthode de requête est une [méthode simple](http://www.w3.org/TR/cors/), ce qui signifie GET, HEAD ou POST.

- La demande réelle, effectuée par rapport à la ressource souhaitée.

### <a name="preflight-request"></a>Demande de contrôle en amont

Les requêtes de contrôle en amont demande les restrictions CORS qui ont été établies pour le service de stockage par le propriétaire du compte. Le navigateur web (ou un autre agent utilisateur) envoie une demande de OPTIONS qui inclut les en-têtes de demande, le domaine d’origine et de la méthode. Le service de stockage évalue l’opération prévue basée sur un jeu préconfiguré de règles CORS qui spécifient le domaine d’origine, les méthodes de demande et les en-têtes de demande peuvent être spécifiés sur une demande réelle par rapport à une ressource de stockage.

Si CORS est activée pour le service et il existe une règle CORS correspondant à la demande de contrôle en amont, le service répond avec un code d’état 200 (OK) et inclut les en-têtes de contrôle d’accès requis dans la réponse.

Si CORS n’est pas activé pour le service ou aucune règle CORS correspondant à la demande de contrôle en amont, le service répond avec un code d’état 403 (refusé).

Si la OPTIONS de requête ne contient pas les en-têtes CORS requis (les en-têtes d’origine et contrôle-demande-méthode d’accès), le service répond avec un code d’état 400 (demande incorrecte).

Notez qu’une demande de contrôle en amont est évaluée par rapport au service (Blob, file d’attente et Table) et non par rapport à la ressource demandée. Le compte du propriétaire doit avoir activé CORS dans le cadre des propriétés du service de compte afin que la requête réussisse.

### <a name="actual-request"></a>Demande réelle

Une fois la demande de contrôle en amont est acceptée et la réponse est renvoyée, le navigateur envoie la demande réelle par rapport à la ressource de stockage. Le navigateur refuse la demande immédiatement si la demande de contrôle en amont est rejetée.

La demande est considérée comme normal d’une requête contre le service de stockage. La présence de l’en-tête d’origine indique que la demande est une demande CORS et que le service vérifie les règles de correspondance CORS. Si une correspondance est trouvée, les en-têtes de contrôle d’accès sont ajoutés à la réponse et envoyés au client. Si une correspondance est introuvable, les en-têtes de contrôle d’accès CORS ne sont pas retournés.

## <a name="enabling-cors-for-the-azure-storage-services"></a>L’activation de CORS pour les services de stockage Azure

Règles CORS sont définies au niveau du service, vous devez activer ou désactiver les CORS pour chaque service (Blob, file d’attente et tableau) séparément. Par défaut, les CORS est désactivée pour chaque service. Pour activer les CORS, vous devez définir les propriétés de service approprié à l’aide de la version 2013-08-15 ou version ultérieure et ajouter des règles CORS pour les propriétés du service. Pour plus d’informations sur comment activer ou désactiver les CORS pour un service et comment définir des règles CORS, reportez-vous à [Définir les propriétés d’objet Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [Définir les propriétés de file d’attente de Service](https://msdn.microsoft.com/library/hh452232.aspx)et [Définir les propriétés de Table Service](https://msdn.microsoft.com/library/hh452240.aspx).

Voici un exemple d’une règle CORS unique, spécifiée à l’aide d’une opération de définition des propriétés de Service :

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Chaque élément inclus dans la règle CORS est décrite ci-dessous :

- **AllowedOrigins**: le domaine d’origine qui est autorisés à effectuer une requête contre le service de stockage via les CORS. Le domaine d’origine est le domaine d'où provient la demande. Notez que l’origine doit être une correspondance de casse exacte à l’origine de l’âge de l’utilisateur envoie au service. Vous pouvez également utiliser le caractère générique ' *' pour permettre à tous les domaines d’origine effectuer des demandes via CORS. Dans l’exemple ci-dessus, les domaines [http://www.contoso.com](http://www.contoso.com) et [http://www.fabrikam.com](http://www.fabrikam.com) peuvent envoyer des demandes au service à l’aide de CORS.

- **AllowedMethods**: les méthodes (verbes de requête HTTP) que le domaine d’origine peut utiliser pour une demande CORS. Dans l’exemple ci-dessus, seules les demandes PUT et GET sont autorisés.

- **AllowedHeaders**: les en-têtes de demande, le domaine d’origine peut spécifier sur la demande CORS. Dans l’exemple ci-dessus, x-ms-meta-données, x-ms-meta-cible et x-ms-meta-abc à partir de tous les en-têtes de métadonnées sont autorisés. Notez que le caractère générique ' *' indique que tout début d’en-tête avec le préfixe spécifié est autorisé.

- **ExposedHeaders**: les en-têtes de réponse qui peuvent être envoyées dans la réponse à la demande CORS et exposées par le navigateur à l’émetteur de la demande. Dans l’exemple ci-dessus, le navigateur doit exposer depuis n’importe quel en-tête x-ms-meta.

- **MaxAgeInSeconds**: demande de la durée maximale qu’un navigateur doit mettre en cache les OPTIONS de contrôle en amont.

Les services de stockage Azure prend en charge la spécification préfixés d’en-têtes pour les éléments de la **AllowedHeaders** et le **ExposedHeaders** . Pour permettre à une catégorie d’en-têtes, vous pouvez spécifier un préfixe commun à cette catégorie. Par exemple, la spécification *x-ms-meta** comme un en-tête préfixé établit une règle qui correspond à tous les en-têtes qui commencent par x-ms-meta.

Les limitations suivantes s’appliquent aux règles CORS :

- Vous pouvez spécifier jusqu'à cinq règles CORS par le service de stockage (Blob, Table et file d’attente).
- La taille maximale de tous les CORS paramètres des règles sur la demande, à l’exclusion des balises XML, ne doit pas dépasser 2 Ko.
- La longueur d’un en-tête autorisée, en-tête exposé ou autorisées d’origine ne doit pas dépasser 256 caractères.
- En-têtes et en-têtes exposés peuvent être :
  - En-têtes littérales, où le nom d’en-tête exacte est fourni, par exemple **x-ms-meta-traitées**. Un maximum de 64 en-têtes littérales peut être spécifié dans la demande.
  - En-têtes, où un préfixe de l’en-tête est fourni, par exemple **x-ms-meta-données**de préfixe *. Spécification d’un préfixe de cette manière permet ou expose un en-tête qui commence par le préfixe spécifié. Un maximum de deux en-têtes préfixées peut être spécifié dans la demande.
- Les méthodes (ou les verbes HTTP) spécifié dans l’élément **AllowedMethods** doivent respecter les méthodes prises en charge par les API de service de stockage Azure. Méthodes prises en charge sont DELETE, GET, HEAD, fusion, POST, OPTIONS et PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Comprendre la logique d’évaluation de règle CORS

Lorsqu’un service de stockage reçoit une demande de contrôle en amont ou réelle, il évalue cette demande en fonction des règles CORS que vous avez établi pour le service via l’opération de définition des propriétés de Service appropriée. Règles CORS sont évaluées dans l’ordre dans lequel elles ont été définies dans le corps de la demande de l’opération de définition des propriétés de Service.

Règles CORS sont évalués comme suit :

1. Tout d’abord, le domaine d’origine de la demande est vérifié par rapport aux domaines répertoriés pour l’élément **AllowedOrigins** . Si le domaine d’origine est inclus dans la liste, ou tous les domaines sont autorisés avec le caractère générique ' *', puis les règles d’évaluation produit. Si le domaine d’origine n’est pas inclus, la demande échoue.

2. Ensuite, la méthode ou verbe HTTP de la demande est vérifiée par rapport aux méthodes répertoriées dans l’élément **AllowedMethods** . Si la méthode est incluse dans la liste, puis l’évaluation des règles se poursuit ; Si ce n’est pas le cas, la demande échoue.

3. Si la demande correspond à une règle dans son domaine d’origine et sa méthode, cette règle est activée pour traiter que la demande et pas plus de règles sont évaluées. Avant que la demande peut réussir, cependant, n’importe quel en-tête spécifiée sur la demande est vérifiés les en-têtes répertoriés dans l’élément **AllowedHeaders** . Si les en-têtes envoyés ne correspondent pas les en-têtes autorisés, la demande échoue.

Étant donné que les règles sont traitées dans l’ordre, qu'elles ne figurent pas dans le corps de la demande, les meilleures pratiques recommandent que vous spécifiez les règles plus restrictives en ce qui concerne les origines d’abord dans la liste, afin que celles-ci sont évaluées en premier. Spécifier des règles qui sont moins restrictives – par exemple, une règle pour autoriser toutes les origines, à la fin de la liste.

### <a name="example--cors-rules-evaluation"></a>Exemple – évaluation des règles CORS

L’exemple suivant montre un corps de demande partielle pour une opération définir les règles CORS pour les services de stockage. Pour plus d’informations sur la construction de la demande, consultez [Définir les propriétés d’objet Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [Définir les propriétés de file d’attente de Service](https://msdn.microsoft.com/library/hh452232.aspx)et [Définir les propriétés de Table Service](https://msdn.microsoft.com/library/hh452240.aspx) .

    <Cors>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>PUT,HEAD</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>*</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
        </CorsRule>
    </Cors>

Ensuite, envisagez les demandes CORS suivantes :

Demande||| Réponse||
---|---|---|---|---
**Méthode** |**Origine** |**En-têtes de demande** |**Correspondance de la règle** |**Résultat**
**PUT** | http://www.contoso.com |x-ms-blob-type de contenu | Première règle |Opération réussie
**Télécharger** | http://www.contoso.com| x-ms-blob-type de contenu | Deuxième règle |Opération réussie
**Télécharger** | http://www.contoso.com| x-ms-blob-type de contenu | Deuxième règle | Échec

La première requête correspond à la première règle – le domaine d’origine correspond à l’origine autorisée, la méthode correspond aux méthodes autorisées et l’en-tête correspond aux des en-têtes – et donc réussit.

La deuxième demande ne correspond pas à la première règle, car la méthode ne correspond pas aux méthodes autorisées. Il existe, cependant, correspondant à la deuxième règle, il réussit.

La troisième requête correspond la deuxième règle dans son domaine d’origine et de la méthode, aucune des règles additionnelles ne sont évaluées. Toutefois, l' *en-tête x-ms-client-demande-id* n’est pas autorisée par la deuxième règle, la demande échoue, malgré le fait que la sémantique de la troisième règle lui aurait permis de réussir.

>[AZURE.NOTE] Bien que cet exemple illustre une règle moins restrictive avant un plus restrictif, en général la meilleure solution consiste à répertorier en premier les règles les plus restrictives.

## <a name="understanding-how-the-vary-header-is-set"></a>Comprendre comment l’en-tête Vary est définie

L’en-tête *Vary* est un en-tête HTTP/1.1 standard consistant en un ensemble de champs d’en-tête de demande qui conseille l’agent utilisateur ou du navigateur sur les critères qui ont été sélectionnés par le serveur pour traiter la requête. L’en-tête *Vary* est principalement utilisé pour la mise en cache par les serveurs proxy, les navigateurs et CDN, ce qui permet de déterminer comment la réponse doit être mise en cache. Pour plus d’informations, consultez la spécification pour l' [en-tête Vary](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Lorsque le navigateur ou un autre utilisateur l’agent met en cache la réponse à une demande CORS, le domaine d’origine est mis en cache comme origine autorisé. Lorsqu’un second domaine envoie la même demande pour une ressource de stockage lorsque le cache est actif, l’agent utilisateur récupère le domaine d’origine mis en cache. Le deuxième domaine ne correspond pas à du domaine mis en cache, la demande échoue alors qu’il devrait normalement réussir. Dans certains cas, le stockage Azure définit l’en-tête Vary à l' **origine** pour indiquer à l’agent utilisateur pour envoyer la demande suivante de CORS au service lorsque le domaine demande diffère de l’origine de la mise en cache.

Stockage Azure définit l’en-tête *Vary* à **l’origine** pour les demandes GET par tête réels dans les cas suivants :

- Lorsque l’origine de la demande correspond exactement à l’origine autorisé défini par une règle CORS. Pour une correspondance exacte, la règle CORS ne peut pas inclure un caractère générique ' * ' caractères.

- Il n’y a aucune règle correspondant à l’origine de la demande, mais CORS est activé pour le service de stockage.

Dans le cas où une demande GET/tête correspond à une règle CORS qui permet à toutes les origines, la réponse indique que toutes les origines sont autorisés, et le cache de l’agent utilisateur autorise les demandes ultérieures à partir de n’importe quel domaine d’origine alors que le cache est actif.

Notez que pour les demandes à l’aide de méthodes autres que GET par tête, les services de stockage ne définit pas l’en-tête Vary, étant donné que les réponses à ces méthodes ne sont pas mises en cache par les agents de l’utilisateur.

Le tableau suivant indique comment Azure stockage répondra aux requêtes GET/chef selon les cas mentionnés précédemment :

Demande|Paramètre du compte et le résultat de l’évaluation de la règle|||Réponse|||
---|---|---|---|---|---|---|---|---
**En-tête d’origine présent sur demande** | **Ou plusieurs règles CORS spécifiés pour ce service** | **Règle de correspondance existe qui permet à toutes les origines (*)** | **Règle de correspondance existe pour la correspondance de l’origine exacte** | **Réponse inclut l’en-tête Vary défini à l’origine** | **Réponse inclut l’accès contrôle-autorisé-origine : «*»** | **Réponse inclut l’accès--exposé-en-têtes de contrôle**
N°|N°|N°|N°|N°|N°|N°
N°|Oui|N°|N°|Oui|N°|N°
N°|Oui|Oui|N°|N°|Oui|Oui
Oui|N°|N°|N°|N°|N°|N°
Oui|Oui|N°|Oui|Oui|N°|Oui
Oui|Oui|N°|N°|Oui|N°|N°
Oui|Oui|Oui|N°|N°|Oui|Oui


## <a name="billing-for-cors-requests"></a>Pour les demandes CORS de facturation

Les demandes de contrôle en amont réussies sont facturés si vous avez activé les CORS pour tous les services de stockage de votre compte (en appelant la [Définition des propriétés de Service Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Définir les propriétés de file d’attente Service](https://msdn.microsoft.com/library/hh452232.aspx)ou [Définir les propriétés de Service de Table](https://msdn.microsoft.com/library/hh452240.aspx)). Pour réduire les frais, envisagez de définir l’élément **MaxAgeInSeconds** dans vos règles de CORS sur une grande valeur afin que l’agent utilisateur met en cache la demande.

Les demandes de contrôle en amont : échecs ne seront pas facturées.

## <a name="next-steps"></a>Étapes suivantes

[Définir les propriétés de Service d’objet Blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Définir les propriétés du Service de file d’attente](https://msdn.microsoft.com/library/hh452232.aspx)

[Définir les propriétés de Service de Table](https://msdn.microsoft.com/library/hh452240.aspx)

[Ressource d’origine croisée W3C partage de spécification](http://www.w3.org/TR/cors/)
