<properties 
    pageTitle="Vue d’ensemble des modèles de licence Widevine | Microsoft Azure" 
    description="Cette rubrique fournit une vue d’ensemble d’un modèle de licence de Widevine qui permet de configurer les licences de Widevine." 
    authors="juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="widevine-license-template-overview"></a>Vue d’ensemble des modèles de licence Widevine

##<a name="overview"></a>Vue d’ensemble

Azure Media Services maintenant vous permet de configurer et de demander des licences de Widevine. Lorsque le lecteur de l’utilisateur final essaie de lire votre contenu protégé de Widevine, une demande est envoyée au service de livraison de licence pour obtenir une licence. Si le service de licences approuve la demande, il émet la licence qui est envoyée au client et peut être utilisée pour décrypter et en lire le contenu spécifié.

Widevine demande de licence est mis en forme comme un message JSON.  

Notez que vous pouvez choisir de créer un message vide sans valeurs simplement « {} » et un modèle de licence sera créé avec les paramètres par défaut.  

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

##<a name="json-message"></a>Message JSON

Nom | Valeur | Description
---|---|---
charge utile |Chaînes codées en Base64 |La demande de licence envoyée par un client. 
content_id | Chaînes codées en Base64|Identificateur utilisé pour dériver les clés de contenu et de KeyId(s) pour chaque content_key_specs.track_type.
fournisseur |chaîne |Utilisé pour rechercher les stratégies et les clés de contenu. Obligatoire.
nom_stratégie | chaîne |Nom d’une stratégie précédemment enregistrée. Facultatif
allowed_track_types | enum  | SD_ONLY ou SD_HD. Les contrôles dont le content des clés doivent être inclus dans une licence
content_key_specs | tableau de JSON structures, consultez **Les spécifications de clé de contenu** suivantes | Une plus grande ampleur contrôle sur le contenu des touches pour revenir. Pour plus d’informations, consultez Spécification de clé de contenu ci-dessous.  Seul des allowed_track_types et content_key_specs peut être spécifié. 
use_policy_overrides_exclusively | valeur booléenne. valeur True ou false | Utilisation des attributs de stratégie spécifié par policy_overrides et omettre toutes les stratégies stockées précédemment.
policy_overrides | JSON de structure, consultez la **Stratégie remplace** ci-dessous | Paramètres de stratégie pour cette licence.  En cas de cet actif a une stratégie prédéfinie, ces valeurs spécifiés seront utilisés. 
session_init | JSON de structure, consultez **Initialisation de Session** ci-dessous | Facultatif de données passé à licence.
parse_only | valeur booléenne. valeur True ou false | La demande de licence est analysée, mais aucune licence n’est émise. Toutefois, les valeurs de formulaire à la demande de licence sont retournés dans la réponse.  

##<a name="content-key-specs"></a>Spécifications de la clé de contenu 

En présence d’une stratégie existante, il n’est pas nécessaire de spécifier des valeurs dans la spécification de clé de contenu.  La stratégie préexistante associée à ce contenu permet de déterminer la protection de la sortie des CGMS HDCP.  Si une stratégie existante n’est pas enregistrée avec le serveur de licence Widevine, le fournisseur de contenu permet d’injecter les valeurs dans la demande de licence.   


Chaque content_key_specs doit être spécifié pour toutes les pistes, quel que soit l’option use_policy_overrides_exclusively. 


Nom | Valeur | Description
---|---|---
content_key_specs. track_type | chaîne | Un nom de type de suivi. Si content_key_specs est spécifié dans la demande de licence, assurez-vous de spécifier que tous les types de suivi explicitement. Faire provoque un échec de lecture dernières secondes de 10. 
content_key_specs  <br/> security_level | UInt32 | Définit les exigences de fiabilité de client pour la lecture. <br/> 1 - basé sur logiciel de boîte blanche crypto est requis. <br/> 2 - chiffrement d’un logiciel et un décodeur masqué est requis. <br/> 3 - le matériel de clé et les opérations de chiffrement doivent être exécutées dans un environnement d’exécution de confiance sauvegardés de matériel. <br/> 4 - le chiffrement et le décodage de contenu doivent être exécutées dans un environnement d’exécution de confiance sauvegardés de matériel.  <br/> 5 - le chiffrement, décodage et tous les gestion des médias (compressée et non compressée) doivent être gérées dans un environnement de sauvegarde exécution approuvé de matériel.  
content_key_specs <br/> required_output_protection.HDC | chaîne - un des : HDCP_NONE, HDCP_V1, HDCP_V2 | Indique si la protection HDCP est nécessaire.
content_key_specs <br/>clé | En base 64 <br/>chaîne codée|Clé de contenu à utiliser pour cette piste. Si spécifié, le track_type ou le key_id est requis.  Cette option permet au fournisseur de contenu injecter la clé pour cette piste au lieu de laisser le serveur de licences Widevine générer ou de recherche d’une clé de contenu.
content_key_specs.key_ID| Crypté en Base64 chaîne binaire de 16 octets | Identificateur unique de la clé. 


##<a name="policy-overrides"></a>Remplacements de la stratégie 

Nom | Valeur | Description
---|---|---
policy_overrides. can_play | valeur booléenne. valeur True ou false | Indique que la lecture du contenu est autorisée. Valeur par défaut est false.
policy_overrides. can_persist | valeur booléenne. valeur True ou false |Indique que la licence peut-être être conservée dans le stockage non volatile pour une utilisation en mode hors connexion. Valeur par défaut est false.
policy_overrides. can_renew | valeur booléenne true ou false |Indique que le renouvellement de la licence est autorisé. Si la valeur est true, la durée de la licence peut être prolongée de pulsation. Valeur par défaut est false. 
policy_overrides. license_duration_seconds | Int64 | Indique la durée de validité de cette licence spécifique. Une valeur de 0 indique qu’il n’y a aucune limite à la durée. Valeur par défaut est 0. 
policy_overrides. rental_duration_seconds | Int64 | Indique la fenêtre de temps que la lecture est autorisée. Une valeur de 0 indique qu’il n’y a aucune limite à la durée. Valeur par défaut est 0. 
policy_overrides. playback_duration_seconds | Int64 | La fenêtre d’affichage de temps après le démarrage de la lecture dans la durée de la licence. Une valeur de 0 indique qu’il n’y a aucune limite à la durée. Valeur par défaut est 0. 
policy_overrides. renewal_server_url |chaîne | Toutes les demandes de pulsation (renouvellement) de cette licence doivent être dirigés vers l’URL spécifiée. Ce champ est uniquement utilisé si can_renew a la valeur true.
policy_overrides. renewal_delay_seconds |Int64 |Le nombre de secondes après license_start_time, avant le renouvellement est tentée en premier. Ce champ est uniquement utilisé si can_renew a la valeur true. Valeur par défaut est 0 
policy_overrides. renewal_retry_interval_seconds | Int64 | Spécifie le délai en secondes entre les demandes de renouvellement de licence suivantes, en cas de défaillance. Ce champ est uniquement utilisé si can_renew a la valeur true. 
policy_overrides. renewal_recovery_duration_seconds | Int64 | La fenêtre de temps, dans lequel la lecture peut se poursuivre pendant que le renouvellement est tentée, mais échec dû à des problèmes de back-end avec le serveur de licences. Une valeur de 0 indique qu’il n’y a aucune limite à la durée. Ce champ est uniquement utilisé si can_renew a la valeur true.
policy_overrides. renew_with_usage | valeur booléenne true ou false |Indique que la licence est envoyée pour le renouvellement lorsque l’utilisation est démarrée. Ce champ est uniquement utilisé si can_renew a la valeur true. 

##<a name="session-initialization"></a>Initialisation de la session

Nom | Valeur | Description
---|---|---
provider_session_token | Chaînes codées en Base64 |Ce jeton de session est repassée dans la licence et se trouvent dans les renouvellements suivants.  Le jeton de session n’est pas conservées au-delà des sessions. 
provider_client_token | Chaînes codées en Base64 | Jeton de client à envoyer dans la réponse de la licence.  Si la demande de licence contient un jeton client, cette valeur est ignorée. Le jeton du client est conservées au-delà de sessions de la licence.
override_provider_client_token | valeur booléenne. valeur True ou false |Si la valeur false et à la demande de licence contient un jeton client, utilisez le jeton à partir de la demande même si un jeton du client a été spécifié dans cette structure.  Si la valeur est true, utilisez toujours le jeton spécifié dans cette structure.

##<a name="configure-your-widevine-licenses-using-net-types"></a>Configurer vos licences de Widevine à l’aide des types .NET

Media Services fournit les API .NET qui vous permettent de configurer vos licences Widevine. 

###<a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classes définies dans le Kit de développement .NET de Services multimédia

Voici les définitions de ces types.

    public class WidevineMessage
    {
        public WidevineMessage();
    
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###<a name="example"></a>Exemple

L’exemple suivant montre comment utiliser l’API .NET pour configurer une licence Widevine simple.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Voir aussi

[En utilisant le cryptage commun PlayReady et/ou Widevine dynamique](media-services-protect-with-drm.md)
