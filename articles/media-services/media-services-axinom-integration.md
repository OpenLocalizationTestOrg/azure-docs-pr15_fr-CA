<properties 
    pageTitle="L’utilisation de Axinom pour remettre les licences de Widevine pour Azure Media Services | Microsoft Azure" 
    description="Cet article décrit comment vous pouvez utiliser Azure Media Services (AMS) pour fournir un flux qui est chiffré dynamiquement par AMS PlayReady et Widevine DRMs. La licence PlayReady provient de serveur de licences PlayReady des Services de support et Widevine licence est remis par le serveur de licences de Axinom." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"   
    ms.author="willzhan;Mingfeiy;rajputam;Juliako"/>

#<a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>L’utilisation de Axinom pour remettre les licences de Widevine pour Azure Media Services  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##<a name="overview"></a>Vue d’ensemble

Azure Media Services (AMS) a ajouté la protection dynamique de Google Widevine (voir le [blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) pour plus de détails). En outre, Azure Media Player (AMP) a également ajouté la prise en charge de Widevine (voir le [document de l’AMP](http://amp.azure.net/libs/amp/latest/docs/) pour plus de détails). Il s’agit d’un succès majeur dans le tiret en continu protégé par CENC avec multi-native-DRM (PlayReady et Widevine) sur les navigateurs modernes équipées MSE et EME.

À partir de Media Services .NET SDK version 3.5.2, Media Services vous permet de configurer le modèle de licence de Widevine et d’obtenir des licences de Widevine. Vous pouvez également utiliser les partenaires AMS suivants pour vous aider à obtenir les licences de Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Cet article décrit comment intégrer et tester le serveur de licences géré par Axinom Widevine. Plus précisément, il couvre :  

- Configuration dynamique de cryptage commun avec multi-DRM (PlayReady et Widevine) avec l’URL d’acquisition de licence correspondantes ;
- Génération d’un jeton JWT afin de satisfaire les exigences de serveur de licences ;
- Développement d’application Azure Media Player qui gère l’acquisition de licences avec l’authentification par jeton JWT ;

L’ensemble du système et le flux de contenu QU'ID clé, clé, clé semences, de jeton JTW et de ses revendications peut être mieux décrit par le diagramme suivant.

![TIRET et CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##<a name="content-protection"></a>Protection du contenu

Pour configurer la protection dynamique et la stratégie de distribution de clés, veuillez consultez blog de Mingfei : [Comment faire pour configurer l’emballage Widevine avec Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Vous pouvez configurer la protection de CENC dynamique avec multi-DRM pour le tiret en continu comportant les éléments suivants :

1. Protection de PlayReady pour MS Edge et IE11, ce qui pourrait soumises à une autorisation de jeton. La stratégie de jeton restreint doit être accompagnée d’un jeton émis par un Service (jeton de sécurité), tel que Azure Active Directory ;
1. Protection de Widevine pour le Chrome, il peut demander l’authentification des jetons avec le jeton émis par un autre STS. 

Consultez [Génération de jeton JWT](media-services-axinom-integration.md#jwt-token-generation) section de pourquoi Azure Active Directory ne peut pas être utilisée comme un STS pour le serveur de licences de Widevine de Axinom.

###<a name="considerations"></a>Considérations relatives à la

1. Vous devez utiliser le Axinom spécifié clé seed (8888000000000000000000000000000000000000) et votre généré ou sélectionné clé ID pour générer la clé de contenu pour la configuration du service de distribution de clés. Axinom serveur de licences émet toutes les licences contenant les clés de contenu basées sur la même valeur de départ de clé, qui n’est valide que pour les tests et la production.
1. L’URL d’acquisition de licence Widevine pour le test : [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP et HTTS sont autorisées.

##<a name="azure-media-player-preparation"></a>Préparation du lecteur média Azure

AMP 1.4.0 prend en charge la lecture de contenu AMS dynamiquement livré avec PlayReady et Widevine DRM.
Si le serveur de licences Widevine ne nécessite pas d’authentification de jeton, il n’y a rien supplémentaires que vous devez faire pour tester un contenu de tiret protégé par Widevine. Pour obtenir un exemple, l’équipe a fournit un simple [exemple](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), où vous pouvez voir fonctionner en bord et IE11 avec PlayReady et Chrome avec Widevine.
Le serveur de licences Widevine fourni par Axinom requiert une authentification de jeton JWT. Le jeton JWT doit être envoyé avec la demande de licence via un en-tête HTTP « X-AxDRM-Message ». Pour cela, vous devez ajouter le code javascript suivant dans la page web d’hébergement AMP avant de définir la source :

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Le reste du code de l’AMP est une API AMP standard comme document d’AMP [ici](http://amp.azure.net/libs/amp/latest/docs/).

Notez que javascript pour l’en-tête d’autorisation personnalisée de paramètre ci-dessus est toujours une approche à court terme avant le fonctionnaire approche à long terme de gestion du matériel est publié.

##<a name="jwt-token-generation"></a>Génération de jeton JWT

Serveur de licence Axinom Widevine de test requiert une authentification de jeton JWT. En outre, parmi les revendications dans le jeton JWT est d’un type complexe au lieu du type de données primitif.

Malheureusement, Azure AD ne peut émettre que des jetons JWT avec des types primitifs. De la même façon, les API de.NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler et JwtPayload) vous permet uniquement d’entrée de type d’objet complexe que les créances. Cependant, les demandes sont toujours sérialisées en tant que chaîne. Par conséquent, nous ne pouvons utiliser un des deux pour générer le jeton JWT de demande de licence Widevine.


L' de John Sheehan [package JWT Nuget](https://www.nuget.org/packages/JWT) répond aux besoins afin que nous allons utiliser ce package Nuget.

Vous trouverez ci-dessous le code de génération JWT jeton avec les déclarations nécessaires comme requis par le serveur de licences de Axinom Widevine pour le test :

    
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;
    
    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
    
                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };
    
                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
    
                return token;
            }
    
            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }
    
                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }
    
                return HexAsBytes;
            }
    
        }  
    
    }  

Serveur de licence Axinom Widevine

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###<a name="considerations"></a>Considérations relatives à la

1.  Même si le service de livraison de licence AMS PlayReady requiert « PORTEUR = » précédant un jeton d’authentification, Axinom Widevine serveur de licences ne l’utilise pas.
2.  La clé de communication Axinom est utilisée en tant que clé de signature. Notez que la clé est une chaîne hexadécimale, mais il doit être traité comme une série d’octets n’est pas une chaîne lors de l’encodage. Pour ce faire, la méthode ConvertHexStringToByteArray.

##<a name="retrieving-key-id"></a>Identificateur de la clé de récupération

Vous avez peut-être remarqué que dans le code pour la génération d’un JWT ID de jeton, la clé est requise. Dans la mesure où les besoins de jeton JWT prête avant chargement AMP lecteur, clé ID doit être récupérées, afin de générer un jeton JWT.

Évidemment, il existe plusieurs manières de s’emparer de clé de code. Par exemple, un peut stocker les ID de clé ainsi que des métadonnées dans une base de données de contenu. Vous pouvez également extraire l’ID de fichier de tiret MPD (Description de la présentation multimédia) de clé. Le code ci-dessous correspond à ce dernier.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }
    
        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();
    
        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
    
        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }
    
        return key_id;
    }

##<a name="summary"></a>Résumé

Dernier-né de prise en charge de Widevine Protection du contenu Azure Media Services et Azure Media Player, nous sommes en mesure d’implémenter la transmission en continu de tiret + Multi-native-DRM (PlayReady + Widevine) avec les deux service licence PlayReady AMS et Widevine serveur de licences à partir de Axinom pour les navigateurs modernes suivants :

- Chrome
- Bord de Microsoft sur Windows 10
- IE 11 sur Windows 8.1 et Windows 10
- Firefox (bureau) et Safari sur Mac (pas d’iOS) sont également pris en charge via Silverlight et la même URL avec Azure Media Player

Les paramètres suivants sont requis dans le serveur de licences de Axinom Widevine en exploitant une solution mini. À l’exception de clé ID, le reste des paramètres sont fournis par Axinom en fonction de leur programme d’installation du serveur Widevine.


Paramètre|Comment elle est utilisée
---|---
ID clé de communication|Doit être inclus en tant que valeur de la demande « com_key_id » dans le jeton de JWT (voir [cette](media-services-axinom-integration.md#jwt-token-generation) section).
Clé de communication|Doit être utilisé comme clé de signature de jeton JWT (voir [cette](media-services-axinom-integration.md#jwt-token-generation) section).
Semences de clé|Doit être utilisé pour générer la clé de contenu dont le contenu donné ID de clé (voir [cette](media-services-axinom-integration.md#content-protection) section).
URL d’acquisition de licence de Widevine|Doit être utilisé dans la configuration de stratégie de remise d’actifs pour le tiret en continu (voir [cette](media-services-axinom-integration.md#content-protection) section).
ID de clé de contenu|Doit être inclus dans le cadre de la valeur de revendication du droit Message du jeton JWT (voir [cette](media-services-axinom-integration.md#jwt-token-generation) section). 


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Accusés de réception 

Nous aimerions remercier les personnes suivantes qui a contribué à la création de ce document : Amit Rajput Kristjan Jõgi de Axinom et Mingfei Yan.
