<properties 
    pageTitle="Notes de version de Media Services | Microsoft Azure" 
    description="Notes de publication de Services multimédia" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="media" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>

# <a name="azure-media-services-release-notes"></a>Notes de version Azure Media Services

Ces notes de version résument les modifications depuis les versions précédentes et les problèmes connus.

>[AZURE.NOTE] Nous souhaitons vous entendre de nos clients et de se concentrer sur la résolution des problèmes qui vous concernent. Pour signaler un problème ou poser des questions, Veuillez publier sur le [Forum MSDN de Azure Media Services].


##<a id="issues"></a>Problèmes connus

### <a id="general_issues"></a>Problèmes généraux liés à Media Services

Problème|Description
---|---
Plusieurs en-têtes HTTP courants ne sont pas fournis dans l’API REST.|Si vous développez des applications de Services de support à l’aide de l’API REST, vous constatez que certains champs d’en-tête HTTP courantes (y compris CLIENT-demande-ID, ID de la demande et retour-CLIENT-demande-ID) ne sont pas pris en charge. Les en-têtes seront ajoutées dans une future mise à jour.
Codage de pourcentage n’est pas autorisée.|Les Services de support utilise la valeur de la propriété IAssetFile.Name lors de la génération d’URL pour le contenu de transmission en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Pour cette raison, codage de pourcentage n’est pas autorisée. La valeur de la propriété **Name** ne peut comporter aucun des [caractères %-codage-réservés](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)suivants : !*'();:@&=+$,/?%#[]". En outre, il ne peut exister un '.' pour l’extension de nom de fichier.
La méthode ListBlobs qui fait partie de la version 3.x non acceptée SDK du stockage Azure.|Media Services génère des URL de SAS en fonction de la version [2012-02-12](http://msdn.microsoft.com/library/azure/dn592123.aspx) . Si vous souhaitez le Kit de développement de stockage Azure permet de BLOB de liste dans un conteneur d’objet blob, utilisez la méthode [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) qui fait partie de la version du Kit de développement de stockage Azure 2.x. La méthode ListBlobs qui fait partie de la version du Kit de développement de stockage Azure 3.x échouera.
Mécanisme d’accélération de Media Services restreint l’utilisation des ressources pour les applications qui font une demande excessive au service. Le service peut renvoyer le code d’état HTTP de Service non disponible (503).|Pour plus d’informations, voir la description du code d’état HTTP 503 dans la rubrique [Codes d’erreur Azure Media Services](http://msdn.microsoft.com/library/azure/dn168949.aspx) .
Lors de l’interrogation des entités, il existe une limite de 1000 entités retournées en une seule fois car public reste v2 limite les résultats de la requête de résultats de 1000. | Vous devez utiliser les **Ignorer** et **prendre** (.NET) / **haut** (reste) comme indiqué dans [Cet exemple .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) et [exemple de cette API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
Certains clients peuvent rencontrer un problème de répétition de balise dans le manifeste de diffusion en continu lisse.|Pour plus d’informations, consultez [cette](media-services-deliver-content-overview.md#known-issues) section.
Les objets Media Services .NET SDK Azure ne peut pas être sérialisés et par conséquent ne fonctionnent pas avec la mise en cache d’Azure.|Si vous essayez de sérialiser l’objet AssetCollection du Kit de développement logiciel pour l’ajouter à la mise en cache d’Azure, une exception est levée.
Tâches de codage échouent avec une chaîne de message « étape : DownloadFile. Code : System.NullReferenceException ».|Le flux de travail de codage par défaut est de télécharger des fichiers vidéo d’entrée à un élément d’entrée et soumettre une ou plusieurs tâches de codage pour cet actif d’entrée, sans modification de plus que l’entrée de ressource. Toutefois, si vous modifiez l’élément d’entrée (par exemple par ajout/suppression/modification du nom des fichiers au sein de l’actif), puis les travaux ultérieurs peuvent échouer avec une erreur DownloadFile. La solution de contournement consiste à supprimer l’élément d’entrée et téléchargez à nouveau les fichiers d’entrée à une nouvelle immobilisation. 

##<a id="rest_version_history"></a>Historique de Version de l’API reste

Pour plus d’informations sur l’historique de version de Media Services reste API, consultez [Référence des API reste Azure Media Services].

##<a id="july_changes16"></a>Version de juillet 2016

###<a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Mises à jour de fichier de manifeste (*. ISM) générés par les tâches de codage

Lorsqu’une tâche est soumise à Media Encoder Standard ou Azure Media Encoder, la tâche génère un [fichier de manifeste de transmission en continu](media-services-deliver-content-overview.md) (* .ism) le fichier dans la sortie actif. Avec la dernière version de service, la syntaxe de ce fichier manifeste en continu a été mis à jour.

>[AZURE.NOTE]La syntaxe du fichier de manifeste (.ism) en flux continu est réservée à un usage interne et est susceptible d’être modifiée dans les futures versions. Veuillez ne pas modifier ou manipuler le contenu de ce fichier.

###<a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Un nouveau manifeste de client (*. Fichier ISMC) est généré dans la sortie actif lorsqu’une tâche de codage génère un ou plusieurs fichiers MP4

Démarrer avec la dernière version de service, après l’achèvement d’une tâche de codage qui génère un plus de fichiers MP4, la sortie actif contient également un fichier de manifeste (*.ismc) client en continu. Le fichier .ismc, vous aide à améliorer les performances de flux dynamique. 

>[AZURE.NOTE]La syntaxe du fichier de manifeste (.ismc) du client est réservée à un usage interne et est susceptible d’être modifiée dans les futures versions. Veuillez ne pas modifier ou manipuler le contenu de ce fichier.

Pour plus d’informations, consultez [le](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blog.

### <a name="known-issues"></a>Problèmes connus

Certains clients peuvent provenir de son adaptabilité à un problème de répétition de balise dans le manifeste de diffusion en continu lisse. Pour plus d’informations, consultez [cette](media-services-deliver-content-overview.md#known-issues) section.

##<a id="apr_changes16"></a>Version d’avril 2016

### <a name="azure-media-analytics"></a>Azure Media Analytique

Azure Media Servces introduit Azure Media Analytique pour les puissantes informations vidéo. Pour plus d’informations, consultez [Vue d’ensemble du Analytique Azure Media Services](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (aperçu)

Azure Media Services vous permet désormais de dynamiquement crypter votre HTTP Live en continu (TLS) contenu avec FairPlay d’Apple. Vous pouvez également utiliser le service de livraison de licence AMS pour fournir des licences de FairPlay aux clients. Pour plus d’informations, consultez [utilisation d’Azure Media Services pour diffuser votre TLS du contenu protégé avec Apple FairPlay ](media-services-protect-hls-with-fairplay.md).
  
##<a id="feb_changes16"></a>Version de février 2016

La dernière version de Azure Media Services SDK pour .NET (3.5.3) contient un correctif de bogue Widevine connexe. Le problème était : AssetDeliveryPolicy n’a pas pu être réutilisé pour plusieurs immobilisations cryptées avec Widevine. Dans le cadre de ce correctif de bogue, la propriété suivante a été ajoutée pour le Kit de développement logiciel : **WidevineBaseLicenseAcquisitionUrl**.
    
    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
        
    };

##<a id="jan_changes_16"></a>Version de janvier 2016

Codage d’unités réservées renommés pour éviter la confusion avec les noms de codeur.

Les unités réservées codage Basic, Standard et Premium sont renommées à S1, S2, et S3 réservé des unités, respectivement.  Clients utilisant aujourd'hui les RUs de codage base verrez S1 comme étiquette dans Azure Portal (et, dans la nomenclature), tout en Standard et Premium verront les étiquettes S2 et S3 respectivement. 

##<a id="dec_changes_15"></a>Version de décembre 2015

L’équipe Azure SDK publié une nouvelle version du package qui contient les mises à jour et nouvelles fonctionnalités pour les Services de support Microsoft Azure [Azure SDK pour PHP](http://github.com/Azure/azure-sdk-for-php) . En particulier, le SDK des Services Azure Media pour PHP prend désormais en charge les dernières fonctionnalités de [protection de contenu](media-services-content-protection-overview.md) : cryptage dynamique avec AES et DRM (PlayReady et Widevine), avec et sans restriction de jeton. Il prend également en charge mise à l’échelle [d’Unités de codage](media-services-dotnet-encoding-units.md).

Pour plus d’informations, voir :

- Le blog du [SDK Microsoft Azure Media Services pour PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) .
- Ci-dessous [des exemples de code](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) pour vous aider à démarrer rapidement :
    - **vodworkflow_aes.php**: il s’agit d’un fichier PHP qui montre comment utiliser le Service de distribution de clés et de cryptage dynamique de AES-128. Il est basé sur l’exemple .NET expliqué dans [cet](media-services-protect-with-aes128.md) article.
    - **vodworkflow_aes.php**: il s’agit d’un fichier PHP qui montre comment utiliser le Service de livraison de licence et de chiffrement dynamiques de PlayReady. Il est basé sur l’exemple .NET expliqué dans [cet](media-services-protect-with-drm.md) article.
    - **scale_encoding_units.php**: il s’agit d’un fichier PHP qui montre comment mettre à l’échelle de codage unité réservée.


##<a id="nov_changes_15"></a>Version de novembre 2015

Azure Media Services propose désormais un service de livraison de licence Google Widevine dans le nuage. Pour plus d’informations, reportez-vous au [blog de cette annonce](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Consultez également [ce didacticiel](media-services-protect-with-drm.md) et [référentiel de GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Notez que les services de livraison de licence Widevine fournies par Azure Media Services est en mode Aperçu. Pour plus d’informations, consultez [le blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

##<a id="oct_changes_15"></a>Version d’octobre 2015

Azure Media Services (AMS) est désormais disponible dans les centres de données suivant : sud du Brésil, ouest de l’Inde, du Sud de l’Inde et de l’Inde centrale. Vous pouvez maintenant utiliser le portail classique d’Azure pour [créer des comptes de Service de support](media-services-portal-create-account.md) et effectuer différentes tâches décrites [ici](https://azure.microsoft.com/documentation/services/media-services/). Toutefois, l ' encodage Live n’est pas activé dans ces centres de données. En outre, pas tous les types d’unités réservées de codage sont disponibles dans ces centres de données.

- Sud du Brésil : Seuls Standard et codage réservé des unités de base sont disponibles
- Ouest de l’Inde, du Sud de l’Inde et de l’Inde centrale : uniquement codage réservé des unités de base sont disponibles


##<a id="september_changes_15"></a>Version de septembre 2015 

- AMS offre désormais la possibilité de protéger les vidéos à la demande (VOD) et les flux en direct avec la technologie DRM modulaire de Widevine. Vous pouvez utiliser les partenaires de services de livraison suivants pour vous aider à obtenir les licences de Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Pour plus d’informations, consultez [le blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

    Vous pouvez utiliser [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (en commençant par la version 3.5.1) ou autres API pour configurer votre AssetDeliveryConfiguration pour utiliser Widevine.  

- AMS désormais en charge les vidéos Apple ProRes. Vous pouvez maintenant télécharger vos fichiers de vidéos QuickTime source qui utilisent Apple ProRes ou autres codecs. Pour plus d’informations, consultez [le blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- Vous pouvez désormais utiliser Media Encoder Standard pour effectuer l’extraction d’archives découpage secondaire et en direct. Pour plus d’informations, consultez [le blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- Les mises à jour de filtrage suivantes ont été apportées : 

    - Vous pouvez désormais utiliser le format de Apple HTTP Live en continu (TLS) avec filtre audio uniquement. Cette mise à jour permet de supprimer la piste audio uniquement en spécifiant (audio uniquement = false) dans l’URL.
    - Lorsque vous définissez des filtres pour vos ressources, vous avez maintenant la possibilité de combiner plusieurs filtres (jusqu'à 3) dans une URL.

    Pour plus d’informations, consultez [le](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

- AMS prend désormais en charge des trames I dans TLS v4. Prise en charge d’I-Frame optimise l’avance rapide et le rembobinage des opérations. Par défaut, toutes les sorties de v4 TLS incluent la sélection d’I-Frame (EXT-X-I-FRAME-STREAM-INF).
 
    Pour plus d’informations, consultez [le](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

##<a id="august_changes_15"></a>Version d’août 2015

- Azure Media Services SDK pour Java V0.8.0 release et de nouveaux exemples sont désormais disponibles. Pour plus d’informations, voir :

    - [Billet de blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
    - [Référentiel des exemples Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Mise à jour de Media Player Azure avec prise en charge des flux de données audio multiples. Pour plus d’informations, voir :
    - [Billet de blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Version de juillet 2015

- Annonce de la disponibilité générale de Media Encoder Standard. Pour plus d’informations, consultez [ce billet de blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

    Media Encoder Standard utilise les paramètres prédéfinis décrits dans [cette](http://go.microsoft.com/fwlink/?LinkId=618336) section. Notez que lorsque vous à l’aide d’un paramètre prédéfini de 4 Ko de code, vous devez obtenir le type d’unité de **prime** réservé. Pour plus d’informations, voir [procédure de codage de l’échelle](media-services-scale-media-processing-overview.md).
- Live légendes en temps réel avec Azure Media Services et le lecteur. Pour plus d’informations, consultez [ce billet de blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###<a name="media-services-net-sdk-updates"></a>Mises à jour du Kit de développement .NET de Services multimédia

Kit de développement .NET Azure Media Services est désormais la version 3.4.0.0. Les fonctionnalités suivantes a été ajoutée dans cette version :  

- Mise en œuvre de la prise en charge pour une archive en ligne. Notez que vous ne peut pas télécharger un actif contenant une archive en ligne.
- Mise en œuvre de la prise en charge pour les filtres dynamiques.
- Fonctionnalité implémentée qui permet aux utilisateurs de conserver le récipient de stockage lors de la suppression d’actifs.
- Correctifs de bogues liés aux tentatives de stratégies dans les canaux.
- Activé le **Workflow de prime Media Encoder**.

##<a id="june_changes_15"></a>Version de juin 2015

###<a name="media-services-net-sdk-updates"></a>Mises à jour du Kit de développement .NET de Services multimédia

Kit de développement .NET Azure Media Services est désormais la version 3.3.0.0. Les fonctionnalités suivantes a été ajoutée dans cette version :  

- prise en charge des spécifications de la découverte de connexion OpenId,
- prise en charge pour le traitement de substitution de clés sur le côté de fournisseur d’identité. 

Si vous utilisez un fournisseur d’identité qui expose le document de découverte OpenID se connecter (comme le font les fournisseurs suivants : Active Directory Azure, Google, la force de vente), vous pouvez demander à Azure Media Services pour obtenir les clés de signature pour la validation du jeton JWT de OpenID de se connecter à des spécifications de découverte. 

Pour plus d’informations, reportez-vous [à l’aide des touches de Web Json OpenID connecter des spécifications de découverte pour fonctionner avec l’authentification de jeton JWT dans Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Version de mai 2015

Annonce les nouvelles fonctionnalités suivantes :

- [Un aperçu de l ' encodage Live avec Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Manifeste dynamique](media-services-dynamic-manifest-overview.md)
- [Un aperçu du processeur de média Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Version d’avril 2015

 ###<a name="general-media-services-updates"></a>Mises à jour des Services multimédia pour général

- [Annonce d’Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- Commençant par 2.10 reste de Media Services, les canaux sont configurés pour l’acquisition d’un protocole RTMP, sont créés avec primaire et secondaire d’acquisition URL. Pour plus d’informations, consultez [configurations d’acquisition de canal](media-services-live-streaming-with-onprem-encoders.md#channel_input)
- Mises à jour de Indexer de Media Azure
- Prise en charge de la langue espagnole
- Nouveau format de configuration xml

Pour plus d’informations, consultez [le blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###<a name="media-services-net-sdk-updates"></a>Mises à jour du Kit de développement .NET de Services multimédia

Kit de développement .NET Azure Media Services est désormais la version 3.2.0.0.

Voici du client vis-à-vis des mises à jour :

- **Modification avec rupture**: modifié **TokenRestrictionTemplate.Issuer** et **TokenRestrictionTemplate.Audience** pour être de type chaîne.
- Mises à jour relatives à la création personnalisée réessayer de stratégies.
- Correctifs liés au chargement/téléchargement de fichiers.
- La classe **MediaServicesCredentials** accepte à présent point final de contrôle de l’accès principal et secondaire pour s’authentifier.



##<a id="march_changes_15"></a>Version de mars 2015

### <a name="general-media-services-updates"></a>Mises à jour des Services multimédia pour général

- Media Services maintenant l’intégration Azure CDN. Pour prendre en charge l’intégration, la propriété **CdnEnabled** a été ajoutée à **StreamingEndpoint**.  **CdnEnabled** peut être utilisé avec les API reste depuis la version 2.9 (pour plus d’informations, voir [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)).  **CdnEnabled** peut être utilisé avec le Kit de développement .NET depuis la version 3.1.0.2 (pour plus d’informations, voir [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Annonce du **flux de travail Media Encoder Premium**. Pour plus d’informations, consultez [Présentation de prime codage dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).
 


##<a id="february_changes_15"></a>Version de février 2015

### <a name="general-media-services-updates"></a>Mises à jour des Services multimédia pour général

API de reste de Media Services est désormais la version 2.9. À partir de cette version, vous pouvez activer l’intégration Azure CDN avec la diffusion en continu de points de terminaison. Pour plus d’informations, consultez [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Version de janvier 2015

### <a name="general-media-services-updates"></a>Mises à jour des Services multimédia pour général

Annonce de disponibilité générale de protection de contenu avec cryptage dynamique. Pour plus d’informations, reportez-vous à la section [Azure Media Services améliore la sécurité en continu grâce à la technologie de la disponibilité générale de DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

###<a name="media-services-net-sdk-updates"></a>Mises à jour du Kit de développement .NET de Services multimédia

Kit de développement .NET Azure Media Services est désormais la version 3.1.0.1.

Cette version marqué le constructeur de Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate par défaut comme étant obsolète. Le nouveau constructeur prend TokenType comme argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Version de décembre 2014

###<a name="general-media-services-updates"></a>Mises à jour des Services multimédia pour général

- Certaines mises à jour et nouvelles fonctionnalités ont été ajoutées au processeur Azure indexeur Media. Pour plus d’informations, consultez [Les Notes de Version de Indexer de Media Azure 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Ajouté une nouvelle API de repos qui vous permet de mettre à jour le codage réservé unités : [EncodingReservedUnitType avec un reste](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- CORS ajoutés prend en charge pour le service de distribution de clés.
- Améliorations des performances d’interrogation des options de stratégie d’autorisation ont été effectuées.
- Dans le centre de données de Chine, [Clé de l’URL de remise](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) est désormais par client (comme dans les autres centres de données).
- Durée de cible ajoutée TLS auto. Lors de la diffusion en continu live, les TLS est toujours empaquetées dynamiquement. Par défaut, Media Services calcule automatiquement un rapport d’emballage segment TLS (FragmentsPerSegment) en fonction de l’intervalle d’image clé (KeyFrameInterval), également appelé groupe d’images – groupe d’images, qui est reçu à partir du codeur Live. Pour plus d’informations, consultez [utilisation de Azure Media Services Live en continu].
 
###<a name="media-services-net-sdk-updates"></a>Mises à jour du Kit de développement .NET de Services multimédia

- [Kit de développement .NET Azure Media Services](http://www.nuget.org/packages/windowsazure.mediaservices/) est désormais la version 3.1.0.0.
- Mise à niveau de la dépendance du Kit de développement .net 4.5 de .NET Framework.
- Ajouter une nouvelle API qui vous permet de mettre à jour le codage unités réservées. Pour plus d’informations, consultez [mise à jour de Type d’unité réservé et augmentant le RUs de codage à l’aide de .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Ajouté JWT (JSON Web Token) prise en charge de l’authentification des jetons. Pour plus d’informations, consultez [authentification de jeton JWT dans Azure Media Services et le cryptage dynamique](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Décalages relatifs ajoutés pour BeginDate et ExpirationDate dans le modèle de licence PlayReady.


##<a id="november_changes_14"></a>Version de novembre 2014

- Les Services de médias vous permet désormais d’acquisition d’un contenu de diffusion en continu lisse (FMP4) sur une connexion SSL. Pour réceptionner sur SSL, assurez-vous de mettre l’URL d’acquisition pour HTTPS.  Pour plus d’informations sur live en continu, consultez [utilisation des Azure Media Services Live en continu].
- Notez qu’actuellement, vous ne pouvez pas réceptionner un flux RTMP sur une connexion SSL.
- Vous pouvez aussi diffuser votre contenu sur une connexion SSL. Pour ce faire, assurez-vous que votre URL en continu commencent par HTTPS.
- Notez que vous pouvez diffuser uniquement sur SSL si le point de terminaison en continu à partir de laquelle vous livrez votre contenu a été créé après le 10 septembre 2014. Si votre URL de diffusion en continu est basés sur les points de terminaison en continu créés après le 10 septembre, l’URL contient « streaming.mediaservices.windows.net » (le nouveau format). En continu les URL qui contiennent « origin.mediaservices.windows.net » (l’ancien format) ne supportent pas SSL. Si votre URL est dans l’ancien format et que vous souhaitez être en mesure de diffuser en continu via le protocole SSL, [créez un nouveau point de terminaison en continu](media-services-portal-manage-streaming-endpoints.md). URL créés à partir du nouveau point de terminaison en continu permet de diffuser votre contenu sur SSL.

##<a id="october_changes_14"></a>Version d’octobre 2014

### <a id="new_encoder_release"></a>Version de codeur de Services multimédia

Annonce de la nouvelle version de Media Services Azure Media Encoder. Avec la dernière Azure Media Encoder vous sont facturés uniquement pour sortie Go, mais dans le cas contraire l’encodeur nouvelle fonctionnalité compatible avec l’encodeur précédent. Pour plus d’informations [Détails de tarification de Services média]).

### <a id="oct_sdk"></a>Services de support .NET SDK 

Media Services SDK pour les Extensions .NET est désormais la version 2.0.0.3.

Media Services SDK pour .NET est désormais la version 3.0.0.8.

Les modifications suivantes ont été apportées :

- Refactorisation dans des classes de stratégie de nouvelle tentative.
- Ajout de chaîne d’agent utilisateur aux en-têtes de requête http.
- Ajouter l’étape de génération de restauration nuget.
- Tests de scénario pour utiliser des x509 de fixation cert à partir du référentiel.
- Validation des paramètres lors de la mise à jour du canal et diffusion en continu de fin.
 

### <a name="new-github-repository-to-host-media-services-samples"></a>Nouveau référentiel GitHub à des exemples de Services de support hôte

Exemples se trouvent dans le [référentiel de GitHub d’échantillons Azure Media Services](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>Version de septembre 2014

Métadonnées de Media Services reste sont désormais la version 2.7. Pour plus d’informations sur les dernières mises à jour de repos, voir [Référence de l’API Azure Media Services reste].

Media Services SDK pour .NET est désormais la version 3.0.0.7
 
### <a id="sept_14_breaking_changes"></a>Modifications avec rupture

* **Origine** a été renommé [StreamingEndpoint].
* Une modification du comportement par défaut lors de l’utilisation du **Portail classique d’Azure** de coder et de publier les fichiers MP4.

Auparavant, lorsque à l’aide du portail classique Azure pour publier un élément vidéo de fichier unique MP4 une URL d’associations de sécurité serait créé (SAS URL vous autorise à télécharger la vidéo à partir d’un stockage d’objets blob). Actuellement, lorsque vous utilisez le portail classique Azure de coder et de publier un élément vidéo MP4 de fichier unique, l’URL générée pointe vers un point de terminaison de la diffusion en continu de Azure Media Services.  Cette modification n’affecte pas les vidéos MP4 directement téléchargés vers les Services de support et publiées sans encodé par Azure Media Services.

Actuellement, vous disposez des deux options suivantes pour résoudre le problème.

* Unités de transmission en continu et emballage dynamique de flux .mp4 bien sous la forme d’une présentation de diffusion en continu lisse.

* Créer une url SAS pour télécharger (ou progressivement lire) le .mp4. Pour plus d’informations sur la création d’un localisateur SAS, voir [Fourniture du contenu].


### <a id="sept_14_GA_changes"></a>Nouvelles fonctionnalités/scénarios qui font partie de la version de GA

* **Processeur de média d’indexeur**. Pour plus d’informations, consultez [L’indexation des fichiers multimédias à Indexer de Media Azure].

* L’entité [StreamingEndpoint] maintenant vous permet d’ajouter des noms de domaine personnalisé (hôte).

    Pour un nom de domaine personnalisé à utiliser comme le nom de point de terminaison des Services de support en continu, vous devez ajouter des noms d’hôte personnalisé pour votre point de terminaison en continu. Pour ajouter des noms d’hôte personnalisé, utilisez le Media Services reste API ou le Kit de développement .NET.
    
    Les considérations suivantes s’appliquent :
    
    * Vous devez avoir la propriété du nom de domaine personnalisé.
    
    * La propriété du nom de domaine doit être validée par Azure Media Services. Pour valider le domaine, créer un enregistrement CName qui mappe les <MediaServicesAccountId>.<parent domain> pour verifydns. < zone de dns mediaservices >. 
    
    * Vous devez créer un autre CName qui mappe le nom de l’hôte personnalisé (par exemple, sports.contoso.com) au nom d’hôte de votre StreamingEndpont de Services média (par exemple, amstest.streaming.mediaservices.windows.net).


    Pour plus d’informations, consultez la propriété **CustomHostNames** dans la rubrique [StreamingEndpoint] .

### <a id="sept_14_preview_changes"></a>Nouvelles fonctionnalités/scénarios qui font partie de la version

* Aperçu de diffusion en direct. Pour plus d’informations, consultez [utilisation de Azure Media Services Live en continu].

* Service de distribution de clés. Pour plus d’informations, reportez-vous [à l’aide de cryptage dynamique AES-128 et Service de distribution de clés].

* Cryptage AES dynamique. Pour plus d’informations, reportez-vous [à l’aide de cryptage dynamique AES-128 et Service de distribution de clés].

* Service de livraison de licences PlayReady. Pour plus d’informations, reportez-vous [à l’aide de cryptage dynamique PlayReady et Service de livraison de licence].

* Cryptage des dynamiques de PlayReady. Pour plus d’informations, reportez-vous [à l’aide de cryptage dynamique PlayReady et Service de livraison de licence].

* Modèle de licence PlayReady de Services multimédia. Pour plus d’informations, consultez [Vue d’ensemble du modèle de licence PlayReady Media Services].

* Diffusion en continu de stockage crypté actifs. Pour plus d’informations, consultez [Streaming stockage crypté de contenu].

##<a id="august_changes_14"></a>Version d’août 2014

Lorsque vous codez un actif, une immobilisation de sortie est générée à la fin de la tâche de codage. Avant cette version, le codeur Azure Media Services produit les métadonnées sur les éléments de sortie. À partir de cette version, l’encodeur génère aussi des métadonnées sur les éléments d’entrée. Pour plus d’informations, consultez les rubriques de [Sortie] et les [Métadonnées de l’entrée] .


##<a id="july_changes_14"></a>Version de juillet 2014

Les correctifs suivants ont été prises pour le Gestionnaire de package de Services Azure Media et chiffreur :

* Seul le son est lu lorsque transmuxing un actif d’une archive en ligne pour la lecture en continu Live HTTP – Ceci a été résolu et maintenant à la fois audio et vidéo sont lus.

* Lors de la compression HTTP Live en continu et le cryptage AES 128 bits enveloppe un actif, les flux de packages ne jouent pas sur des appareils Android – ce bogue a été résolu et le flux de package est lu sur des appareils Android prenant en charge HTTP Live en continu.

##<a id="may_changes_14"></a>Version de mai 2014

### <a id="may_14_changes"></a>Mises à jour des Services multimédia pour général

Vous pouvez maintenant utiliser [l’Emballage dynamique] à un flux de diffusion en continu Live HTTP (TLS) v3. Flux TLS v3, ajouter au format suivant pour le chemin de recherche d’origine : * .ism/manifest(format=m3u8-aapl-v3). Pour plus d’informations, reportez-vous à la section [Blog de Nick Drouin].

Emballage dynamique maintenant également prend en charge la fourniture TLS (v3 et v4) cryptée avec PlayReady basée sur la diffusion en continu lisse statiquement crypté avec PlayReady. Pour plus d’informations sur la façon de crypter la diffusion en continu lisse avec PlayReady, consultez [protection de flux fluide avec PlayReady].

### <a name="may_14_donnet_changes"></a>Mises à jour du Kit de développement .NET de Services multimédia

Les améliorations suivantes sont incluses dans la version de Media Services .NET SDK 3.0.0.5 :

* Plus de vitesse et de résilience pour le téléchargement/chargement des ressources multimédias.

* Améliorations de la nouvelle tentative logique et transitoires la gestion des exceptions : 

    * Logique de détection et de réessayer erreur temporaire ont été améliorées pour les exceptions qui sont provoquées par l’interrogation, l’enregistrement des modifications, chargement ou téléchargement de fichiers. 
    
    * Lors de l’obtention des Exceptions Web (par exemple, lors d’une demande de jeton ACS), vous remarquerez que les erreurs irrécupérables sont défectueux plus rapidement maintenant.

Pour plus d’informations, consultez [Logique de nouvelle tentative dans le Kit de développement de Services de support pour .NET].

##<a id="april_changes_14"></a>Version de codeur avril 2014

### <a name="april_14_enocer_changes"></a>Mises à jour de l’encodeur de Services multimédia

* Prise en charge ajoutée pour traiter les fichiers AVI créés à l’aide de l’éditeur d’herbe Valley fonction non linéaire, où la vidéo est légèrement compressé à l’aide du codec d’herbe Valley HQ/HQX. Pour plus d’informations, consultez [Herbe Valley annonce la fonction 7 en continu via le nuage].

* Prise en charge supplémentaire pour la spécification de la convention d’affectation de noms pour les fichiers générés par le codeur multimédia. Pour plus d’informations, consultez [Contrôle Media Service codeur sortie les noms de fichiers].

* Prise en charge supplémentaire pour les superpositions vidéo et/ou audio. Pour plus d’informations, consultez [Création de superpositions].

* Prise en charge ajoutée pour assembler plusieurs segments vidéo. Pour plus d’informations, reportez-vous à la section [Combinaison des Segments vidéo].

* Corrige un bogue lié au transcodage MP4s où l’audio a été codée avec Audio MPEG-1 layer 3 (alias MP3).


##<a id="jan_feb_changes_14"></a>Versions de janvier/février 2014

### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services SDK .NET 3.0.0.1, 3.0.0.2 et 3.0.0.3

Les modifications apportées à 3.0.0.1 et 3.0.0.2 sont les suivantes :

* Problèmes résolus relatifs à l’utilisation de requêtes LINQ avec des instructions de OrderBy.

* Solutions d’essai Split dans [GitHub] dans les tests par unité et basée sur un scénario de tests.

Pour plus d’informations sur les modifications apportées, consultez : [Azure Media Services SDK .NET 3.0.0.1 et 3.0.0.2 mises à jour].

Les modifications suivantes ont été apportées dans 3.0.0.3 :

* Dépendances de stockage Azure mis à niveau pour utiliser la version 3.0.3.0. 

* Problème de compatibilité ascendante fixe pour 3.0. *.* mises à jour. 


##<a id="december_changes_13"></a>Version de décembre 2013

### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0

>[AZURE.NOTE] 3.0.x.x versions ne sont pas compatibles avec les versions 2.4.x.x.

La dernière version du SDK Media Services est désormais 3.0.0.0. Vous pouvez télécharger le dernier package de Nuget ou obtenir les bits à partir de [GitHub].

En commençant par la version 3.0.0.0 de Media Services SDK, vous pouvez réutiliser les jetons [Azure Active Directory Access Control Service (ACS)] . Pour plus d’informations, consultez la section « Réutilisation des jetons accès contrôle Service » dans la rubrique [connexion à des Services multimédias avec le Kit de développement de Services de support pour .NET] .

### <a name="dec_13_donnet_ext_changes"></a>Extensions du Kit de développement .NET 2.0.0.0 des Services multimédia pour Azure

Les Extensions de Azure Media Services .NET SDK est un ensemble de méthodes d’extension et des fonctions d’assistance qui va simplifier votre code et le rendre plus facile à développer avec Azure Media Services. Vous pouvez obtenir les derniers bits à partir [d’Extensions de Azure Media Services .NET SDK].

##<a id="november_changes_13"></a>Version de novembre 2013

### <a name="nov_13_donnet_changes"></a>Modifications du Kit de développement .NET des Services multimédia pour Azure

À partir de cette version, le Kit de développement de Services de support pour .NET gère les erreurs transitoires qui peuvent se produire lors d’appels à la couche Media Services reste API.

##<a id="august_changes_13"></a>Version d’août 2013

### <a name="aug_13_powershell_changes"></a>Applets de commande PowerShell de Services média inclus dans les outils du Kit de développement logiciel Azure

Les applets de commande PowerShell de Services de support suivantes sont désormais incluses dans les [Outils du Kit de développement logiciel d’azure].

* Get-AzureMediaServices 

    Par exemple, `Get-AzureMediaServicesAccount`.

* Nouvelle-AzureMediaServicesAccount 

    Par exemple, `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* Nouvelle-AzureMediaServicesKey 

    Par exemple, `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Supprimer-AzureMediaServicesAccount 

    Par exemple, `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Version de juin 2013

### <a name="june_13_general_changes"></a>Modifications de Media Services Azure

Les modifications mentionnées dans cette section sont mises à jour incluses dans les versions de juin 2013 Media Services.

* Possibilité de lier le stockage de plusieurs comptes pour un compte de Service de support. 

    StorageAccount
    
    Asset.StorageAccountName et Asset.StorageAccount

* Possibilité de mise à jour Job.Priority. 

* Notification concernant les entités et les propriétés : 

    JobNotificationSubscription
    
    NotificationEndPoint
    
    Travail

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Modifications de Media Services .NET SDK Azure

Les modifications suivantes sont incluses dans juin 2013 mises à jour de Media Services SDK. La dernière version de Media Services SDK est disponible sur GitHub.

* À partir de la version 2.3.0.0, la prise en charge de Media Services SDK liant le stockage de plusieurs comptes pour un compte de Services de support. Cette fonctionnalité prend en charge les API suivantes :
    
    Le type de IStorageAccount.
    
    La propriété Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
    
    La propriété StorageAccount.
    
    La propriété StorageAccountName.
    
    Pour plus d’informations, consultez [Gestion des ressources de Services multimédias entre plusieurs comptes de stockage].

* Notification des API associées. À partir de la version 2.2.0.0, que vous avez la possibilité d’écouter les notifications de stockage Azure file. Pour plus d’informations, consultez [Notifications relatives aux tâches de gestion des Media Services].
    
    La propriété Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
    
    Le type de Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
    
    Le type de Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
    
    Le type de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
    
    Le type de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
    
    Le type de Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dépendance sur le Client de stockage Azure SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dépendance sur OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Version de décembre 2012

### <a name="dec_12_dotnet_changes"></a>Modifications de Media Services .NET SDK Azure

* IntelliSense : Ajouté la documentation manquante de Intellisense pour de nombreux types.

* Microsoft.Practices.TransientFaultHandling.Core : Corrige un problème où le Kit de développement était une dépendance à une ancienne version de cet assembly. Le Kit de développement logiciel fait désormais référence à la version 5.1.1209.1 de cet assembly.

Correctifs pour les problèmes rencontrés dans le Kit de développement logiciel de novembre 2012 :

* IAsset.Locators.Count : Ce nombre est désormais correctement signalé sur les nouvelles interfaces IAsset après la suppression de tous les repères.

* IAssetFile.ContentFileSize : Cette valeur est désormais correctement définie après un téléchargement par IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize : Cette propriété peut maintenant être définie lors de la création d’un fichier de ressources. Il était déjà en lecture seule.

* IAssetFile.Upload(filepath) : Corrige un problème où cette méthode de téléchargement synchrone a été levée à l’erreur suivante lors du téléchargement de plusieurs fichiers à l’actif. L’erreur était « Server n’a pas pu authentifier la demande. Assurez-vous que la valeur de l’en-tête d’autorisation est formée correctement, y compris de la signature. »

* IAssetFile.UploadAsync : Corrige un problème où des fichiers ne dépassant pas 5 peuvent être chargés simultanément.

* IAssetFile.UploadProgressChanged : Cet événement est maintenant fourni par le Kit de développement logiciel.

* IAssetFile.DownloadAsync (chaîne, BlobTransferClient, ILocator, CancellationToken) : cette surcharge de méthode est maintenant fournie.

* IAssetFile.DownloadAsync : Corrige un problème où pas plus de 5 fichiers peuvent être téléchargés simultanément.

* IAssetFile.Delete() : Résolution d’un problème dans lequel l’appel de delete peut lever une exception si aucun fichier n’a été chargé pour le IAssetFile.

* Travaux : Corrige un problème où un « MP4 pour la tâche de flux de fluide » avec une « tâche de Protection de PlayReady » à l’aide d’un modèle de tâche de chaînage pas créerait des tâches à tout.

* EncryptionUtils.GetCertificateFromStore() : Cette méthode lève n’est plus une exception de référence null à une recherche le certificat basé sur les problèmes de configuration de certificat des défaillances.

##<a id="november_changes_12"></a>Version de novembre 2012

Les modifications mentionnées dans cette section ont été mises à jour incluses dans le Kit de développement novembre 2012 (version 2.0.0.0) SDK. Ces modifications peuvent exiger tout code écrit pour l’aperçu de juin 2012 SDK version pour être modifié ou réécrite.

* Actifs
    
    IAsset.Create(assetName) est la fonction de création de ressource uniquement. IAsset.Create n’est plus télécharge les fichiers dans le cadre de l’appel de méthode. Utilisez IAssetFile pour le téléchargement.
    
    La méthode IAsset.Publish et la valeur d’énumération AssetState.Publish ont été supprimés dans le Kit de développement de Services. Tout code qui dépend de cette valeur doit être réécrit.

* FileInfo

    Cette classe a été supprimée et remplacée par IAssetFile.

    IAssetFiles

    IAssetFile remplace FileInfo et a un comportement différent. Pour l’utiliser, instancier l’objet IAssetFiles, suivi d’un téléchargement de fichiers à l’aide de la Media Services SDK ou le Kit de développement de stockage Azure. IAssetFile.Upload les surcharges suivantes peuvent être utilisées :

    * IAssetFile.Upload(filePath) : Une méthode synchrone bloque le thread et est recommandée uniquement lors du chargement d’un fichier unique.
    
    * IAssetFile.UploadAsync (CheminFichier, blobTransferClient, locator, cancellationToken) : une méthode asynchrone. C’est le mécanisme de téléchargement par défaut. 

    Bogue connu : à l’aide de cancellationToken en effet annule le téléchargement ; Toutefois, l’état de l’annulation des tâches peut être un certain nombre d’états. Vous devez correctement d’intercepter et de gérer les exceptions.

* Localisateurs
    
    Les versions spécifiques d’origine ont été supprimées. Le contexte spécifique du SAS. Locators.CreateSasLocator (actif, accessPolicy) sont marquées obsolètes ou supprimés par GA. Voir la section repères sous de nouvelles fonctionnalités pour le comportement de mise à jour.


##<a id="june_changes_12"></a>Version d’évaluation de juin 2012

Les fonctionnalités suivantes sont apparues dans la version de novembre du Kit de développement.

* La suppression des entités

    IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey objets sont maintenant supprimés au niveau de l’objet, par exemple, IObject.Delete(), plutôt que de demander une suppression de la Collection, qui est cloudMediaContext.ObjCollection.Delete(objInstance).

* Localisateurs

    Localisateurs doivent maintenant être créés à l’aide de la méthode CreateLocator et utilisent les valeurs enum LocatorType.SAS ou LocatorType.OnDemandOrigin sous la forme d’un argument pour le type de localisateur que vous souhaitez créer.

    Nouvelles propriétés ont été ajoutées aux localisateurs pour faciliter leur obtenir l’URI utilisable pour votre contenu. Cette nouvelle conception de localisateurs devait offrent plus de souplesse pour la future extensibilité de tiers et d’augmenter la facilité d’utilisation pour les applications clientes de media.

* Prise en charge de la méthode asynchrone

    Prise en charge asynchrone a été ajouté à toutes les méthodes.


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Forum MSDN Azure Media Services]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Référence de l’API reste des Services multimédia pour Azure]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Détails de la tarification des Services multimédia]: http://azure.microsoft.com/pricing/details/media-services/
[Entrer des métadonnées]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Métadonnées de sortie]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Fourniture de contenu]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[L’indexation des fichiers multimédias à Indexer de Media Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Utilisation des Services Azure Media Live en continu]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[À l’aide du Service de distribution de clés et de cryptage dynamique de AES-128]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[À l’aide du cryptage des dynamiques de PlayReady et Service de livraison de licence]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Vue d’ensemble des modèles de licences PlayReady de Services multimédia]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Diffusion en continu de stockage du contenu chiffré]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[Emballage dynamique]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protection de flux fluide avec PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Nouvelle tentative de logique dans les Services de support SDK pour .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Annonce la fonction 7 grass Valley en continu via le nuage]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Contrôle Media Service de noms de fichiers de sortie codeur]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Créer des superpositions]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[La combinaison de séquences vidéo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Versions Azure Media Services .NET SDK 3.0.0.1 et 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory Access Control Service (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Connexion aux Services de support avec les Services de support SDK pour .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services Extensions du Kit de développement .NET]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[outils du Kit de développement logiciel d’Azure]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[La gestion des supports de Services actifs dans plusieurs comptes de stockage]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Notifications de travail des Services de gestion des médias]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 
