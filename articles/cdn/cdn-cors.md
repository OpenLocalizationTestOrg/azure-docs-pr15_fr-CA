<properties
    pageTitle="Avec Azure CDN CORS | Microsoft Azure"
    description="Apprenez à utiliser l’Azure réseau CDN (Content Delivery) à avec le partage de ressources Cross-Origin (CORS)."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="casoper"/>
    
# <a name="using-azure-cdn-with-cors"></a>À l’aide d’Azure CDN avec CORS     

## <a name="what-is-cors"></a>Nouveautés CORS

CORS (Cross origine partage des ressources) est une fonctionnalité HTTP qui permet à une application web en cours d’exécution dans un domaine d’accéder aux ressources dans un autre domaine. Afin de réduire le risque d’attaques de script entre sites, tous les navigateurs web modernes implémentent une restriction de sécurité appelée [stratégie de même origine](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Ainsi, une page web à partir de l’appel d’API dans un autre domaine.  CORS offre un moyen sécurisé pour permettre à un domaine (le domaine d’origine) appeler des API dans un autre domaine.
 
## <a name="how-it-works"></a>Mode de fonctionnement
1.  Le navigateur envoie la demande d’OPTIONS avec un en-tête d' **origine** HTTP. La valeur de cet en-tête est le domaine pris en charge la page parente. Lorsqu’une page à partir de https://www.contoso.com tente d’accéder aux données d’un utilisateur dans le domaine fabrikam.com, l’en-tête de requête suivante sera envoyée au fabrikam.com : 
    
    `Origin: https://www.contoso.com`
 
2.  Le serveur peut répondre par le texte suivant :
    - Un en-tête **d’Accès contrôle-autoriser-origine** dans sa réponse indiquant les sites d’origine sont autorisées. Par exemple :
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - Une page d’erreur si le serveur n’autorise pas la demande d’origine croisée
    - Un en-tête **d’Accès contrôle-autoriser-origine** avec un caractère générique qui permet à tous les domaines :
        
        `Access-Control-Allow-Origin: *`
 
Pour les demandes HTTP complexes, est une demande « en amont » faite le premier pour déterminer si elle a l’autorisation avant d’envoyer la totalité de la demande.
 
## <a name="wildcard-or-single-origin-scenarios"></a>Scénarios d’origine unique ou de caractères génériques

Lorsque la valeur de l’en-tête **d’Accès contrôle-autoriser-origine** à caractère générique (*) ou d’une seule origine, CORS sur Azure CDN fonctionnent automatiquement sans aucune configuration supplémentaire.  Le CDN va mettre en cache la première réponse et les demandes ultérieures utiliseront le même en-tête.
 
Si les demandes ont déjà été apportées au CDN avant CORS sur l’origine du lot, vous devez vider le contenu sur le contenu de votre point de terminaison à recharger le contenu avec l’en-tête **d’Accès contrôle-autoriser-origine** .
 
## <a name="multiple-origin-scenarios"></a>Plusieurs scénarios d’origine

Si vous avez besoin permettre à une liste spécifique d’origines pour être autorisée pour les CORS, les choses deviennent un peu plus complexes. Le problème se produit lorsque le CDN met en cache l’en-tête **Accès contrôle-autoriser-origine** pour l’origine CORS premier.  Une origine différente de CORS lorsqu’une demande ultérieure, le CDN est pris en charge l’en-tête **d’Accès contrôle-autoriser-origine** mis en cache, qui ne correspondre pas.  Il existe plusieurs façons de corriger ce problème.
 
### <a name="azure-cdn-premium-from-verizon"></a>Prime d’Azure CDN de Verizon

La meilleure façon de procéder consiste à utiliser des **Premium du CDN Azure auprès de Verizon**, qui expose des fonctionnalités avancées. 
 
Vous devez [créer une règle](cdn-rules-engine.md) vérifier l’en-tête de la demande **d’origine** .  S’il s’agit d’une origine valide, votre règle définit l’en-tête **d’Accès contrôle-autoriser-origine** avec l’origine fournie dans la demande.  Si l’origine spécifiée dans l’en-tête **d’origine** n’est pas autorisé, votre règle doit omettre l’en-tête **d’Accès contrôle-autoriser-origine** qui provoquera le navigateur de rejeter la demande. 
 
Il existe deux façons de le faire avec le moteur de règles.  Dans les deux cas, l’en-tête **d’Accès contrôle-autoriser-origine** à partir du serveur d’origine du fichier est complètement ignorée, moteur de règles de du CDN gère totalement les origines CORS autorisées.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Une expression régulière avec toutes les origines valides
 
Dans ce cas, vous allez créer une expression régulière qui inclut toutes les origines que vous souhaitez autoriser : 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] **Azure CDN de Verizon** utilise des [Expressions régulières de Perl Compatible](http://pcre.org/) comme son moteur des expressions régulières.  Vous pouvez utiliser un outil tel que [101 d’Expressions régulières](https://regex101.com/) pour valider votre expression régulière.  Notez que le caractère « / » est valide dans une expression régulière et ne doit pas nécessairement être échappés, toutefois, ce caractère d’échappement est considéré comme une meilleure pratique et attendu par certains validateurs d’expression régulière.

Si l’expression régulière correspond, votre règle remplace l’en-tête de **l’Accès contrôle-autoriser-origine** (le cas échéant) à partir de l’origine avec l’origine qui a envoyé la demande.  Vous pouvez également ajouter des en-têtes CORS supplémentaires, telles que **Access--autoriser-méthodes de contrôle**.

![Exemple de règles avec l’expression régulière](./media/cdn-cors/cdn-cors-regex.png)
 
#### <a name="request-header-rule-for-each-origin"></a>Règle d’en-tête de demande pour chaque origine.

Au lieu d’expressions régulières, vous pouvez à la place créer une règle distincte pour chaque origine, que vous souhaitez autoriser à l’aide de la **Demande des génériques en-tête** [correspond à la condition](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). Comme avec la méthode d’expression régulière, le moteur de règles seul définit les en-têtes CORS. 
  
![Exemple de règles sans expression régulière](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] Dans l’exemple ci-dessus, l’utilisation du caractère générique * indique au moteur de règles pour le correspond à la fois HTTP et HTTPS.
 
### <a name="azure-cdn-standard"></a>Azure CDN Standard

Sur les profils d’Azure CDN Standard, le seul mécanisme permettant à plusieurs origines sans l’utilisation de l’origine des génériques est d’utiliser [la mise en cache de chaîne de requête](cdn-query-string.md).  Vous devez activer le paramètre de chaîne de requête pour le point de terminaison CDN et puis utiliser une chaîne de requête unique pour les demandes de chaque domaine autorisé. Cela entraînera le CDN mise en cache d’un objet distinct pour chaque chaîne de requête unique. Cette approche n’est pas idéale, cependant, car il se traduira par plusieurs copies du même fichier mis en cache sur le CDN.  

