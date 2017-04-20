<properties
 pageTitle="Guide du développeur - contrôle de l’accès à IoT concentrateur | Microsoft Azure"
 description="Guide du développeur de concentrateur de IoT Azure - comment contrôler l’accès à IoT concentrateur et de gestion de la sécurité"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="control-access-to-iot-hub"></a>Contrôle de l’accès à IoT concentrateur

## <a name="overview"></a>Vue d’ensemble

Cet article décrit les options permettant de sécuriser votre concentrateur IoT. IoT Hub utilise des *autorisations* pour accorder l’accès aux points de terminaison de chaque concentrateur IoT. Autorisations de limitent l’accès à un concentrateur IoT basé sur la fonctionnalité.

Cet article décrit :

- Les différentes autorisations que vous pouvez accorder à une application de périphérique ou back-end pour accéder à votre concentrateur IoT.
- Le processus d’authentification et les jetons qu’il utilise pour vérifier les autorisations.
- Comment les informations d’identification de l’étendue pour limiter l’accès à des ressources spécifiques.
- Concentrateur de IoT prise en charge des certificats X.509.
- Mécanismes d’authentification périphérique personnalisés qui utilisent des registres d’identité périphérique existant ou schémas d’authentification.

### <a name="when-to-use"></a>Quand utiliser

Vous devez disposer des autorisations appropriées pour accéder à l’un points de terminaison IoT concentrateur. Par exemple, un périphérique doit inclure un jeton contenant les informations d’identification de sécurité ainsi que tous les messages qu’il envoie à IoT concentrateur.

## <a name="access-control-and-permissions"></a>Autorisations et contrôle d’accès

Vous pouvez accorder des [autorisations](#iot-hub-permissions) dans l’une des manières suivantes :

* **Au niveau du concentrateur partagé des stratégies d’accès**. Les stratégies d’accès partagé peuvent accorder à n’importe quelle combinaison [d’autorisations](#iot-hub-permissions). Vous pouvez définir des stratégies dans [Azure portal][lnk-management-portal], ou par programme en utilisant le [fournisseur de ressources IoT concentrateur reste API][lnk-resource-provider-apis]. Un concentrateur IoT nouvellement créé possède les stratégies par défaut suivantes :

    - **iothubowner**: stratégie avec toutes les autorisations.
    - **service**: stratégie avec l’autorisation de ServiceConnect.
    - **périphérique**: stratégie avec l’autorisation de DeviceConnect.
    - **registryRead**: la stratégie avec l’autorisation de RegistryRead.
    - **registryReadWrite**: la stratégie avec les autorisations de RegistryRead et RegistryWrite.


* **Informations d’identification de sécurité par périphérique**. Chaque concentrateur IoT contient un [Registre d’identité de périphérique][lnk-identity-registry]. Pour chaque périphérique dans cette clé de Registre, vous pouvez configurer des informations d’identification de sécurité qui accorde les autorisations de **DeviceConnect** étendues pour les points de terminaison de périphérique correspondant.

Par exemple, dans une solution IoT classique :

- Le composant de gestion de périphérique utilise la stratégie de *registryReadWrite* .
- Le composant de traitement d’événement utilise la stratégie de *service* .
- Le composant de la logique métier runtime périphérique utilise la stratégie de *service* .
- Des périphériques de se connectent à l’aide des informations d’identification stockées dans le Registre d’identité du concentrateur IoT.

## <a name="authentication"></a>Authentification

Azure IoT concentrateur accorde l’accès aux points de terminaison en vérifiant un jeton contre les stratégies d’accès partagé et les références de sécurité de périphérique identité du Registre.

Informations d’identification de sécurité, telles que les clés symétriques ne sont jamais envoyées sur le réseau.

> [AZURE.NOTE] Le fournisseur de ressources Azure IoT concentrateur est sécurisé par le biais de votre abonnement Azure, comme tous les fournisseurs dans le [Gestionnaire de ressources Azure][lnk-azure-resource-manager].

Pour plus d’informations sur la façon de construire et d’utiliser des jetons de sécurité, consultez [les jetons de sécurité IoT concentrateur][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Détails de protocole

Chaque protocole pris en charge, tels que MQTT, AMQP et HTTP, les transports de jetons de différentes manières.

Lorsque vous utilisez MQTT, le paquet de connexion a l’ID de périphérique comme ClientId, {iothubhostname} / {ID de périphérique} dans le champ nom d’utilisateur et un jeton SAS dans le champ mot de passe. {iothubhostname} doit être le nom complet canonique du concentrateur IoT (par exemple, contoso.azure-devices.net).

Lors de l’utilisation de [AMQP][lnk-amqp], IoT Hub prend en charge [SASL brut] [ lnk-sasl-plain] [AMQP revendications basées-sécurité]et[lnk-cbs].

Si vous utilisez AMQP revendications--sécurité, la norme spécifie comment transmettre ces jetons.

Pour SASL brut, **nom d’utilisateur** peut être :

* `{policyName}@sas.root.{iothubName}`Si vous utilisez les jetons au niveau du concentrateur.
* `{deviceId}@sas.{iothubname}`Si vous utilisez un périphérique à portée de jetons.

Dans les deux cas, le champ de mot de passe contient le jeton, comme décrit dans [les jetons de sécurité IoT concentrateur][lnk-sas-tokens].

HTTP met en œuvre l’authentification en incluant un jeton valide dans l’en-tête de la demande **d’autorisation** .

#### <a name="example"></a>Exemple

Nom d’utilisateur (ID de périphérique est sensible à la casse) :`iothubname.azure-devices.net/DeviceId`

Mot de passe (générer une séquence SAS avec dispositif Explorer) :`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] Les [Kits de développement logiciel Azure IoT concentrateur] [ lnk-sdks] générer automatiquement des jetons lors de la connexion au service. Dans certains cas, les kits de développement logiciel ne supportent pas tous les protocoles ou toutes les méthodes d’authentification.

### <a name="special-considerations-for-sasl-plain"></a>Considérations spéciales pour SASL brut

Lors de l’utilisation de SASL brut avec AMQP, un client qui se connecte à un concentrateur IoT peut utiliser un jeton unique pour chaque connexion TCP. Lorsque le jeton expire, la connexion TCP déconnecté du service et déclenche une reconnexion. Ce comportement, bien que pas problématique pour un composant back-end de l’application, est très dommageable pour une application de périphérique pour les raisons suivantes :

*  Les passerelles se connectent au nom de nombreux périphériques. Lorsque vous utilisez SASL brut, ils doivent créer une connexion TCP distincte pour chaque périphérique qui se connecte à un concentrateur IoT. Ce scénario est considérablement augmente la consommation de puissance et de ressources réseau et augmente la latence de chaque connexion de périphérique.
* Périphériques à ressources limitées sont affectées par l’utilisation accrue des ressources se reconnecter après chaque expiration du jeton.

## <a name="scope-hub-level-credentials"></a>Informations d’identification de portée au niveau du concentrateur

Vous pouvez limiter l’étendue des stratégies de sécurité au niveau du concentrateur en créant des jetons avec un URI de ressource restreinte. Par exemple, le point de terminaison pour envoyer des messages de périphérique-nuage à partir d’un périphérique est **/devices/ {deviceId} / messages/événements**. Vous pouvez également utiliser une stratégie au niveau du concentrateur un accès partagé avec les autorisations de **DeviceConnect** pour signer un jeton dont ResourceURI des services Web sont **/devices/ {deviceId}**. Cette approche crée un jeton qui n’est plus utilisable pour envoyer des messages au nom de périphérique, **ID de périphérique**.

Ce mécanisme est similaire à la [stratégie de l’éditeur concentrateurs d’événement][lnk-event-hubs-publisher-policy]et vous permet d’implémenter des méthodes d’authentification personnalisé.

## <a name="security-tokens"></a>Jetons de sécurité

IoT Hub utilise les jetons de sécurité pour authentifier les périphériques et les services afin d’éviter l’envoi de clés sur le réseau. En outre, les jetons de sécurité sont limités dans la portée et de la période de validité. [Azure SDK de concentrateur IoT] [ lnk-sdks] générer automatiquement des jetons sans configuration spéciale. Certains scénarios, toutefois, exigent de l’utilisateur de générer et d’utiliser directement les jetons de sécurité. Celles-ci incluent l’utilisation directe des surfaces MQTT, AMQP ou HTTP, ou la mise en oeuvre du modèle service de jeton, comme expliqué dans [l’authentification de périphérique personnalisée][lnk-custom-auth].

IoT Hub permet également de périphériques s’authentifier avec IoT concentrateur à l’aide de [certificats X.509][lnk-x509]. 

### <a name="security-token-structure"></a>Structure de jeton de sécurité
Les jetons de sécurité vous permet d’accorder des limitées à la fois l’accès aux équipements et services à une fonctionnalité spécifique dans IoT concentrateur. Pour vous assurer que seuls les services et les périphériques autorisés peuvent se connecter, les jetons de sécurité doivent être signés avec une clé de stratégie d’accès partagé ou une clé symétrique stockée avec une identité de périphérique dans le Registre de l’identité.

Un jeton est signé avec un accès clé accorde un accès partagé à toutes les fonctionnalités associées aux autorisations de stratégie d’accès partagé. D’autre part, un jeton signé avec une clé symétrique de l’identité d’un dispositif n’accorde l’autorisation **DeviceConnect** pour l’identité du périphérique associé.

Le jeton de sécurité a le format suivant :

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Voici les valeurs attendues :

| Valeur | Description |
| ----- | ----------- |
| signature de {} | Une chaîne de signature de code HMAC-SHA256 du formulaire : `{URL-encoded-resourceURI} + "\n" + expiry`. **Important**: la clé est décodée à partir en base 64 et utilisée comme clé pour effectuer le calcul du code HMAC-SHA256. |
| {ResourceURI} des services Web | Préfixe URI (par segment) des points de terminaison qui peut être accédée avec ce jeton, en commençant par le nom d’hôte du concentrateur IoT (aucun protocole). Par exemple,`myHub.azure-devices.net/devices/device1` |
| {expiration} | Chaînes UTF8 pour le nombre de secondes depuis l’UTC de 00:00:00 époque sur le 1er janvier 1970. |
| {URL-encoded-ResourceURI services Web} | Cas inférieur le codage URL de l’URI de la ressource bas de casse |
| {policyName} | Le nom de la stratégie d’accès partagé auquel se réfère ce jeton. Absent dans le cas de jetons se référant aux informations d’identification de périphérique du Registre. |

**Note sur le préfixe**: préfixe de l’URI est calculée par segment et non par caractères. Par exemple `/a/b` est un préfixe `/a/b/c` mais pas pour les `/a/bc`.

L’extrait de Node.js suivant montre une fonction appelée **generateSasToken** qui calcule le jeton à partir d’entrées `resourceUri, signingKey, policyName, expiresInMins`. Les sections suivantes détaillent la manière d’initialiser les entrées différentes pour les cas d’utilisation de jetons différents.

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // Use crypto
        var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
        hmac.update(toSign);
        var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

        // Construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        return token;
    };

Comparaison, le code Python équivalent pour générer un jeton de sécurité est la suivante :

    from base64 import b64encode, b64decode
    from hashlib import sha256
    from time import time
    from urllib import quote_plus, urlencode
    from hmac import HMAC

    def generate_sas_token(uri, key, policy_name, expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
        print sign_key
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

        rawtoken = {
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl))
        }

        if policy_name is not None:
            rawtoken['skn'] = policy_name

        return 'SharedAccessSignature ' + urlencode(rawtoken)

> [AZURE.NOTE] Dans la mesure où la validité du jeton est validée sur les ordinateurs IoT Hub, il est important que la dérive de l’horloge de l’ordinateur qui génère le jeton soit minimal.

### <a name="use-sas-tokens-in-a-device-client"></a>Utilisation des jetons SAS dans un client de périphérique

Il existe deux méthodes pour obtenir les autorisations de **DeviceConnect** avec IoT Hub avec des jetons de sécurité : utilisation d’une [clé symétrique de périphérique à partir du Registre d’identité de périphérique](#use-a-symmetric-key-in-the-identity-registry), ou utiliser une [clé de stratégie d’accès partagé](#use-a-shared-access-policy).

Gardez à l’esprit que toutes les fonctionnalités accessibles à partir de périphériques sont exposée par la conception de points de terminaison avec le préfixe `/devices/{deviceId}`.

> [AZURE.IMPORTANT] La seule façon de que IoT concentrateur authentifie un périphérique spécifique à l’aide de la clé symétrique identité du périphérique. Dans le cas lorsqu’une stratégie d’accès partagé est utilisée pour accéder aux fonctionnalités de périphérique, la solution doit prendre en compte le composant émettant le jeton de sécurité sous la forme d’un sous-composant d’un niveau de confiance.

Les points de terminaison côté périphérique sont (indépendamment du protocole) :

| Point de terminaison | Fonctionnalités |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Envoyer des messages de périphérique-nuage. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Recevoir des messages du nuage vers le périphérique. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Utiliser une clé symétrique dans le Registre de l’identité

Lors de l’utilisation de la clé symétrique de l’identité d’un dispositif pour générer un jeton de la stratégie (`skn`) élément du jeton est omis.

Par exemple, un jeton créé pour accéder à toutes les fonctionnalités de périphérique doit avoir les paramètres suivants :

* URI de la ressource : `{IoT hub name}.azure-devices.net/devices/{device id}`,
* la clé de signature : une clé symétrique pour le `{device id}` identité,
* Aucun nom de stratégie,
* un délai d’expiration.

Un exemple d’utilisation de la fonction de nœud ci-dessus serait :

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

Le résultat, qui accorde l’accès à toutes les fonctionnalités d’appareil1, serait :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] Il est possible de générer un jeton de sécurité à l’aide de l’outil .NET [Explorateur de périphérique][lnk-device-explorer].

### <a name="use-a-shared-access-policy"></a>Utiliser une stratégie d’accès partagé

Lors de la création d’un jeton d’une stratégie d’accès partagé, la stratégie de nom de champ `skn` doit être définie sur le nom de la stratégie utilisée. Il est également nécessaire que la stratégie accorde l’autorisation de **DeviceConnect** .

Les deux principaux scénarios pour l’utilisation de stratégies d’accès partagé pour accéder aux fonctionnalités du périphérique sont les suivantes :

* [les passerelles de protocole en nuage][lnk-endpoints],
* [jeton de services] [ lnk-custom-auth] utilisé pour implémenter des schémas d’authentification personnalisés.

Dans la mesure où la stratégie d’accès partagé peut potentiellement accorder l’accès pour se connecter en tant que n’importe quel périphérique, il est important d’utiliser l’URI de la ressource correcte lors de la création de jetons de sécurité. Ceci est particulièrement important pour les services de jetons, qui doivent étendre le jeton à un périphérique spécifique, à l’aide de l’URI de la ressource. Ce point est moins utile pour les passerelles de protocole comme ils sont médiation déjà le trafic pour tous les périphériques.

Par exemple, un service de jetons en utilisant la stratégie précréés accès partagé appelée **périphérique** créerait un jeton avec les paramètres suivants :

* URI de la ressource : `{IoT hub name}.azure-devices.net/devices/{device id}`,
* la clé de signature : une des clés de la `device` stratégie,
* nom de la stratégie : `device`,
* un délai d’expiration.

Un exemple d’utilisation de la fonction de nœud ci-dessus serait :

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Le résultat, qui accorde l’accès à toutes les fonctionnalités d’appareil1, serait :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Une passerelle de protocole peut utiliser le même jeton pour tous les périphériques simplement en définissant l’URI de la ressource à `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Utilisation des jetons de sécurité à partir de composants de service

Composants de service ne peuvent générer que des jetons de sécurité à l’aide de stratégies d’accès partagé accorder les autorisations appropriées, comme expliqué précédemment.

Ce sont les fonctions de service exposées sur les points de terminaison :

| Point de terminaison | Fonctionnalités |
| ----- | ----------- |
| `{iot hub host name}/devices` | Créer, mettre à jour, extraire et supprimer des identités des appareils. |
| `{iot hub host name}/messages/events` | Recevoir des messages de périphérique-nuage. |
| `{iot hub host name}/servicebound/feedback` | Recevoir des commentaires pour les messages du nuage vers le périphérique. |
| `{iot hub host name}/devicebound` | Envoyer des messages du nuage vers le périphérique. |

Par exemple, un service de génération à l’aide de la stratégie précréés accès partagé appelée **registryRead** créerait un jeton avec les paramètres suivants :

* URI de la ressource : `{IoT hub name}.azure-devices.net/devices`,
* la clé de signature : une des clés de la `registryRead` stratégie,
* nom de la stratégie : `registryRead`,
* un délai d’expiration.

    point de terminaison var = « myhub.azure-devices.net/devices » ;   var policyName = 'unité' ;   var policyKey = '...' ;

    jeton de var = generateSasToken (point de terminaison, policyKey, policyName, 60) ;

Le résultat, qui peut accorder l’accès pour toutes les identités du périphérique de lecture, serait :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x509-certificates"></a>Prise en charge des certificats X.509

Vous pouvez utiliser des certificats X.509 pour authentifier un dispositif avec IoT concentrateur. Cela inclut :

-   **Un certificat X.509 existant**. Un périphérique est peut-être déjà un certificat X.509 lui est associé. Le périphérique peut utiliser ce certificat pour authentifier avec IoT concentrateur.

-   **Un certificat de X-509 généré automatiquement et auto-signé**. Un fabricant de périphérique ou le responsable du déploiement en interne peut générer ces certificats et stocker la clé privée correspondante (et le certificat) sur le périphérique. Vous pouvez utiliser des outils tels que [OpenSSL] [ lnk-openssl] et [Windows SelfSignedCertificate] [ lnk-selfsigned] utilitaire à cet effet.

-   **Certificat X.509 de signature d’autorité de certification**. Vous pouvez également utiliser un certificat X.509 généré et signé par une autorité de Certification (CA) pour identifier un périphérique et authentifier un périphérique avec IoT concentrateur.

Un périphérique peut utiliser un certificat X.509 ou un jeton de sécurité pour l’authentification, mais pas les deux.

### <a name="register-an-x509-client-certificate-for-a-device"></a>Enregistrer un certificat de client X.509 pour un périphérique

Le [SDK de Service IoT Azure pour C#] [ lnk-service-sdk] (version 1.0.8+) prend en charge l’enregistrement d’un dispositif qui utilise un certificat client X.509 pour l’authentification. Autres API comme importation/exportation de périphériques prennent également en charge les certificats de client X.509.

### <a name="c-support"></a>C\# prise en charge

La classe **RegistryManager** permet une programmation inscrire un appareil. En particulier, les méthodes **AddDeviceAsync** et **UpdateDeviceAsync** permettent à un utilisateur d’enregistrer et de mettre à jour un périphérique dans le Registre d’identité de périphérique Iot concentrateur. Ces deux méthodes prennent une instance de **périphérique** comme entrée. La classe de **périphérique** inclut une propriété **d’authentification** qui permet à l’utilisateur de spécifier les empreintes de certificat X.509 principales et secondaires. L’empreinte numérique représente un hachage SHA-1 du certificat X.509 (stocké en utilisant le codage DER binaire). Les utilisateurs ont la possibilité de spécifier une empreinte principale ou une empreinte secondaire ou les deux. Les empreintes primaires et secondaires sont pris en charge pour gérer les scénarios de survol de certificat.

> [AZURE.NOTE] IoT concentrateur ne requièrent ni stocker le certificat client X.509 tout entier, uniquement l’empreinte numérique.

Voici un exemple de C\# enregistrer un périphérique à l’aide d’un certificat client X.509 extrait de code :

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-client-certificate-during-runtime-operations"></a>Utiliser un certificat client X.509 au cours des opérations d’exécution

Le [dispositif d’Azure IoT SDK du .NET] [ lnk-client-sdk] (version 1.0.11+) prend en charge l’utilisation de certificats client X.509.

### <a name="c-support"></a>C\# prise en charge

La classe **DeviceAuthenticationWithX509Certificate** prend en charge la création d’instances de  **DeviceClient** à l’aide d’un certificat client X.509.

Voici un extrait de code exemple :

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Authentification de périphérique personnalisé

Vous pouvez utiliser le Hub d’IoT [Registre d’identité de périphérique] [ lnk-identity-registry] pour configurer les informations d’identification de sécurité de périphérique et de contrôle à l’aide des [jetons]d’accès[lnk-sas-tokens]. Toutefois, si une solution IoT a déjà consenti un investissement significatif dans un mécanisme du Registre et d’authentification d’identité périphérique personnalisé, vous pouvez intégrer cette infrastructure existante IoT concentrateur par la création d’un *service de jeton*. De cette façon, vous pouvez utiliser d’autres fonctionnalités IoT dans votre solution.

Un service de jetons est un service en nuage personnalisé. Il utilise une *stratégie d’accès partagé* de IoT Hub avec les autorisations de **DeviceConnect** pour créer des jetons de *dispositif de portée* . Ces jetons activer un périphérique pour se connecter à votre concentrateur IoT.

  ![Étapes du modèle de service de jeton][img-tokenservice]

Voici les principales étapes du modèle de service de jeton :

1. Créer une stratégie d’accès concentrateur IoT partagé avec les autorisations de **DeviceConnect** pour votre concentrateur IoT. Vous pouvez créer cette stratégie dans [Azure portal] [ lnk-management-portal] ou par programme. Le service de jeton utilise cette stratégie pour signer les jetons qu’il crée.
2. Lorsqu’un périphérique doit accéder votre concentrateur IoT, elle demande un jeton signé à partir de votre service de jeton. Le périphérique peut s’authentifier avec votre jeu de Registre/authentification d’identité périphérique personnalisé pour déterminer l’identité du périphérique par le service de jeton pour créer le jeton.
3. Le service de jeton retourne un jeton. Le jeton est créé à l’aide de `/devices/{deviceId}` en tant que `resourceURI`, avec `deviceId` comme le périphérique en cours d’authentification. Le service de jeton utilise la stratégie d’accès partagé pour construire le jeton.
4. Le périphérique utilise le jeton directement avec le concentrateur IoT.

> [AZURE.NOTE] Vous pouvez utiliser la classe .NET [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] ou de la classe Java [IotHubServiceSasToken] [ lnk-java-sas] pour créer un jeton dans votre service de jeton.

Le service de jeton peut définir l’expiration du jeton comme vous le souhaitez. Le jeton d’expiration, le concentrateur IoT rompt la connexion du périphérique. Ensuite, le périphérique doit demander un nouveau jeton à partir du service de jeton. Si vous utilisez un délai court, cela augmente la charge sur le périphérique et le service de jeton.

Pour un périphérique pour se connecter à un concentrateur, vous devez l’ajouter encore dans le Registre d’identité de périphérique IoT Hub, même si le périphérique utilise un jeton et pas une clé de périphérique pour se connecter. Par conséquent, vous pouvez continuer à utiliser le contrôle d’accès par périphérique en activant ou désactivant les identités de périphérique dans le [Registre d’identité IoT concentrateur] [ lnk-identity-registry] lorsque le périphérique s’authentifie avec un jeton. Cela permet d’atténuer les risques liés à l’aide des jetons avec des délais d’expiration longues.

### <a name="comparison-with-a-custom-gateway"></a>Comparaison avec une passerelle personnalisée

Le modèle de service de jetons est la méthode recommandée pour implémenter un schéma de Registre/authentification d’identité personnalisée avec IoT concentrateur. Il est recommandé parce que IoT concentrateur continue de gérer la plupart du trafic de la solution. Toutefois, il existe des cas où le schéma d’authentification personnalisé est ainsi étroitement avec le protocole qu’un service de traitement de tout le trafic (*passerelle personnalisée*) est requis. Un exemple de cela est la [sécurité TLS (Transport Layer) et les clés prépartagées (PSKs)][lnk-tls-psk]. Pour plus d’informations, consultez la [passerelle de protocole] [ lnk-protocols] rubrique.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes vous fournissent plus d’informations sur le contrôle de l’accès à votre concentrateur IoT.

## <a name="iot-hub-permissions"></a>Autorisations de concentrateur de IoT

Le tableau suivant répertorie les autorisations que vous pouvez utiliser pour contrôler l’accès à votre concentrateur IoT.

| Autorisation            | Notes |
| --------------------- | ----- |
| **RegistryRead**      | Aides à l’accès en lecture sur le Registre d’identité de périphérique. Pour plus d’informations, consultez le [Registre d’identité de périphérique][lnk-identity-registry]. |
| **RegistryReadWrite** | Lire des subventions et l’accès en écriture sur le Registre d’identité de périphérique. Pour plus d’informations, consultez le [Registre d’identité de périphérique][lnk-identity-registry]. |
| **ServiceConnect**    | Accorde l’accès à la communication et surveillance des points de terminaison orientés vers le service en nuage. Par exemple, il accorde l’autorisation pour les services en nuage de back-end pour recevoir des messages de périphérique-nuage, envoyer des messages du nuage vers le périphérique et récupérer les accusés de réception de livraison correspondante. |
| **DeviceConnect**     | Accorde l’accès aux points de terminaison de communication orientés vers le périphérique. Par exemple, il accorde l’autorisation d’envoyer des messages de périphérique-nuage et de recevoir des messages de nuage vers le périphérique. Cette autorisation est utilisée par les périphériques. |

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

D’autres rubriques de référence dans le Guide du développeur incluent :

- [Les points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison qui expose de chaque concentrateur IoT pour les opérations de gestion et d’exécution.
- [Les quotas et la régulation] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT Hub et le comportement d’accélération s’attendre lorsque vous utilisez le service.
- [SDK de périphérique et service IoT concentrateur] [ lnk-sdks] répertorie la langue différents kits de développement logiciel vous une utilisation lorsque vous développez des services et des périphériques à la fois les applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, procédés et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations du concentrateur de IoT sur vos jumeaux de périphérique, les méthodes et les tâches.
- [Prise en charge de IoT concentrateur MQTT] [ lnk-devguide-mqtt] fournit plus d’informations sur la prise en charge de IoT Hub pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

Maintenant vous avez appris comment contrôler l’accès IoT concentrateur, peut vous intéresser dans les rubriques du Guide de développement suivantes :

- [Des jumeaux de périphérique permet de synchroniser l’état et les configurations][lnk-devguide-device-twins]
- [Appeler une méthode directe sur un périphérique][lnk-devguide-directmethods]
- [Planifier des tâches sur plusieurs périphériques][lnk-devguide-jobs]

Si vous souhaitez essayer certaines des concepts décrits dans cet article, vous intéresser dans les didacticiels IoT Hub suivants :

- [Mise en route avec Azure IoT concentrateur][lnk-getstarted-tutorial]
- [Comment envoyer des messages de nuage-dispositif avec IoT concentrateur][lnk-c2d-tutorial]
- [Comment traiter les messages de périphérique-nuage IoT concentrateur][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
