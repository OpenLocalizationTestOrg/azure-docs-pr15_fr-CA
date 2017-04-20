<properties
    pageTitle="Partagés présentation générale des Signatures d’accès | Microsoft Azure"
    description="Quelles sont les Signatures de l’accès partagé, comment ils fonctionnent et comment les utiliser à partir de nœud, PHP et C#."
    services="service-bus"
    documentationCenter="na"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/02/2016"
    ms.author="darosa;sethm"/>

# <a name="shared-access-signatures"></a>Signatures d’accès partagé

*Partagé des Signatures d’accès* (SAS) sont le principal mécanisme de sécurité pour le Bus de Service, y compris les événements concentrateurs, demandé de la messagerie (files d’attente et rubriques) et de relais de messagerie. Cet article traite des Signatures de l’accès partagé, comment ils fonctionnent et comment les utiliser d’une manière indépendant de la plateforme.

## <a name="overview-of-sas"></a>Vue d’ensemble d’associations de sécurité

Signatures d’accès partagés sont un mécanisme d’authentification basé sur les hachages sécurisés SHA-256 ou URI. SAS est un mécanisme très puissant qui est utilisé par tous les services de Bus de Service. Dans une utilisation réelle, SAS a deux composants : une *Stratégie d’accès partagés* et une *Signature de l’accès partagé* (souvent appelé un *jeton*).

Vous trouverez des informations plus détaillées à propos des Signatures d’accès partagé avec le Bus de Service dans [l’authentification de Signature de l’accès partagé avec le Bus de Service](service-bus-shared-access-signature-authentication.md).

## <a name="shared-access-policy"></a>Stratégie d’accès partagé

Une chose importante à comprendre à propos des associations de sécurité est que tout commence par une stratégie. Pour chaque stratégie, vous décidez sur trois éléments d’information : **nom**, **étendue**et **autorisations**. Le **nom** n’est que cela ; un nom unique au sein de cette portée. La portée est assez facile : il s’agit de l’URI de la ressource en question. Bus de Service d’un espace de noms, la portée est le nom de domaine pleinement qualifié (FQDN), tel que `https://<yournamespace>.servicebus.windows.net/`.

Les autorisations disponibles pour une stratégie sont relativement explicites :

  + Envoyer
  + Écouter
  + Gérer les

Après avoir créé la stratégie, est associé à une *Clé primaire* et une *Clé secondaire*. Il s’agit des clés de cryptage forts. Ne pas les perdre ou perdre les - ils soient toujours disponibles dans le [portail Azure][]. Vous pouvez utiliser une des clés générées, et vous pouvez les recréer à tout moment. Toutefois, si vous régénérez ou modifiez la clé primaire dans la stratégie, des Signatures à accès partagé est créé à partir de celui-ci sont invalidés.

Lorsque vous créez un espace de noms du Bus des services, une stratégie est automatiquement créée pour l’espace de noms entier appelé **RootManageSharedAccessKey**, et cette stratégie a toutes les autorisations. Vous ne pas ouvrir une session en tant que **racine**, afin de ne pas utiliser cette stratégie sauf s’il existe une bonne raison. Vous pouvez créer des stratégies supplémentaires dans l’onglet **configurer** l’espace de noms dans le portail. Il est important de remarque qu’un seul niveau dans le Bus des services de l’arborescence (espace de noms, file d’attente, concentrateur d’événements, etc.) ne peut avoir jusqu'à 12 règles attachées.

## <a name="shared-access-signature-token"></a>Signature d’accès partagé (jeton)

La stratégie elle-même n’est pas le jeton d’accès pour le Bus de Service. Il s’agit de l’objet à partir de laquelle le jeton d’accès est généré - en utilisant soit la clé primaire ou secondaire. Le jeton est généré en soigneusement en créant une chaîne au format suivant :

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Où `signature-string` est la valeur de hachage SHA-256 de l’étendue du jeton (**étendue** comme décrit dans la section précédente) avec un CRLF ajouté et un délai d’expiration (en secondes depuis l’époque : `00:00:00 UTC` sur le 1er janvier 1970).

Le hachage ressemble à la pseudo code suivant et renvoie le 32 octets.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Les valeurs non hachée sont dans la chaîne **SharedAccessSignature** afin que le destinataire peut calculer le hachage avec les mêmes paramètres, pour vous assurer qu’il retourne le même résultat. L’URI spécifie la portée et le nom de clé identifie la stratégie à utiliser pour calculer le hachage. Ceci est important du point de vue de la sécurité. Si la signature ne correspond pas à celui qui calcule par le destinataire (Bus de Service), l’accès est refusé. À ce stade, vous pouvez être sûr que l’expéditeur a accès à la clé et doit être accordé les droits spécifiés dans la stratégie.

## <a name="generating-a-signature-from-a-policy"></a>Génération d’une signature à partir d’une stratégie

Comment réellement faire cela dans le code ? Examinons quelques-unes d'entre elles.

### <a name="nodejs"></a>NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### <a name="java"></a>Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### <a name="php"></a>PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();  
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
return $token; 
}
```
 
### <a name="c35"></a>C & #35 ;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="using-the-shared-access-signature-at-http-level"></a>À l’aide de la Signature d’accès partagé (au niveau HTTP)
 
Maintenant que vous savez comment créer des Signatures de l’accès partagé pour toutes les entités dans le Bus des services, vous êtes prêt à effectuer une publication HTTP :

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
    
N’oubliez pas que cette méthode fonctionne pour tous les éléments. Vous pouvez créer des associations de sécurité pour la file d’attente, rubrique, abonnement, concentrateur d’événements ou relais. Si vous utilisez l’identité par l’éditeur pour les concentrateurs d’événement, vous ajoutez simplement `/publishers/< publisherid>`.

Si vous donnez un expéditeur ou un client, un jeton SAS, ils n’ont pas directement la clé, et qu’ils ne peuvent pas inverser le hachage pour les obtenir. En tant que tel, vous contrôlez sur ce qu’ils peuvent accéder à et de la durée. Une chose importante à retenir est que si vous modifiez la clé primaire dans la stratégie, des Signatures à accès partagé est créé à partir de celui-ci seront invalidées.

## <a name="using-the-shared-access-signature-at-amqp-level"></a>À l’aide de la Signature d’accès partagé (au niveau AMQP)

Dans la section précédente, vous avez appris à utiliser le jeton d’associations de sécurité avec une demande HTTP POST pour envoyer des données pour le Bus de Service. Comme vous le savez, vous pouvez accéder à l’aide de l’avancée Message Queuing Protocol (AMQP) qui est le protocole par défaut à utiliser pour des raisons de performances, dans de nombreux scénarios de Bus de Service. L’utilisation de jetons de SAS avec AMQP est décrite dans le document [AMQP Claim-Based sécurité Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) qui se trouve dans un brouillon de travail aujourd'hui depuis 2013 mais bien pris en charge par Azure.

Avant de commencer à envoyer des données à un Bus de Service, l’éditeur doit envoyer le jeton SAS à l’intérieur d’un message AMQP à un nœud AMQP bien défini, nommé **$cbs** (vous pouvez le voir sous la forme d’une file d’attente « spéciale » utilisé par le service d’acquérir et de valider tous les jetons de SAS). L’éditeur doit spécifier le champ **ReplyTo** à l’intérieur du message AMQP ; C’est le nœud dans lequel le service répond à l’éditeur avec le résultat de la validation de jeton (un modèle demande/réponse simple entre l’éditeur et de service). Création de ce nœud de réponse « à la volée, » parler « création dynamique de nœud distant » conformément à la spécification AMQP 1.0. Après avoir vérifié que le jeton SAS est valide, l’éditeur peut avancer et commencer à envoyer des données vers le service.

Les étapes suivantes indiquent comment envoyer le jeton SAS avec protocole AMQP à l’aide de la bibliothèque [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) . Cela est utile si vous ne pouvez pas utiliser le SDK de Bus de Service officiel (par exemple sur WinRT, .net Compact Framework, .net Micro Framework et Mono) développement de C\#. Bien entendu, cette bibliothèque est utile pour aider à comprendre la sécurité basée sur les revendications fonctionne au niveau de la AMQP, comme vous avez vu comment il fonctionne au niveau HTTP (avec une demande HTTP POST et le jeton SAS envoyée dans l’en-tête « Autorisation »). Si vous n’avez pas besoin des connaissances approfondies sur AMQP, vous pouvez utiliser le SDK de Bus de Service officiel avec .net applications Framework, ce qui le fera pour vous.

### <a name="c35"></a>C & #35 ;

```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

Le `PutCbsToken()` méthode reçoit la *connexion* (instance de classe AMQP connexion sous la forme fournie par la [bibliothèque AMQP .NET Lite](https://github.com/Azure/amqpnetlite)) qui représente la connexion TCP pour le service et le paramètre *sasToken* est le jeton SAS à envoyer. 

> [AZURE.NOTE] Il est important que la connexion est créée avec le **mécanisme d’authentification SASL valeur externe** (et non le brut par défaut avec un nom d’utilisateur et le mot de passe utilisé lorsque vous n’êtes pas obligé d’envoyer le jeton SAS).

Ensuite, l’éditeur crée deux liens AMQP pour envoyer le jeton SAS et la réception de la réponse (le résultat de la validation de jeton) à partir du service.

Le message AMQP contient un ensemble de propriétés et d’informations plus qu’un simple message. Le jeton SAS est le corps du message (à l’aide de son constructeur). La propriété **« ReplyTo »** est définie pour le nom du nœud pour la réception du résultat de la validation sur la liaison de récepteur (vous pouvez modifier son nom si vous voulez, et il sera créé dynamiquement par le service). Les propriétés d’application/custom trois derniers sont utilisées par le service pour indiquer quel type d’opération qu’il doit s’exécuter. Comme décrit par la spécification de projet CBS, ils doivent être le **nom de l’opération** (« put-jeton »), le **type de jeton** (dans ce cas, un « servicebus.windows.net:sastoken ») et le **« nom » de l’audience** à laquelle le jeton s’applique (l’entité entière).

Après avoir envoyé le jeton SAS sur le lien de l’expéditeur, l’éditeur doit lire la réponse sur le lien du récepteur. La réponse est un simple message d’AMQP avec une propriété de l’application nommé **« code d’état »** qui peut contenir les mêmes valeurs que le code d’état HTTP. 

## <a name="next-steps"></a>Étapes suivantes

Consultez la [référence de l’API REST de Bus de Service](https://msdn.microsoft.com/library/azure/hh780717.aspx) pour plus d’informations sur ce que vous pouvez faire avec ces jetons SAS.

Pour plus d’informations sur l’authentification de Bus de Service, voir [Bus de Service d’authentification et autorisation](service-bus-authentication-and-authorization.md). 

Obtenir d’autres exemples d’associations de sécurité dans C# et Java Script sont dans [ce billet de blog](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx).

[Azure portal]: https://portal.azure.com