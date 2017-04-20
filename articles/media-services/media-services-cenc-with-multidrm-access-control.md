<properties 
    pageTitle="CENC avec Multi-DRM et de contrôle d’accès : une conception de référence et la mise en œuvre sur Azure et d’Azure Media Services | Microsoft Azure" 
    description="Obtenir des informations sur le Kit Microsoft® lisse en continu Client portage de licence." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan"  
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="willzhan;kilroyh;yanmf;juliako"/>

#<a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC avec Multi-DRM et de contrôle d’accès : une conception de référence et la mise en œuvre sur Azure et d’Azure Media Services

##<a name="key-words"></a>Mots clés
 
Azure livraison de licence Azure Media Player, cryptage dynamique, Active Directory, Azure Media Services, PlayReady, Widevine, FairPlay, Encryption(CENC) commune, Multi-DRM, Axinom, tiret, EME, MSE, JSON Web jeton (JWT), créances, les navigateurs modernes, clé de substitution, clé symétrique, clé asymétrique, se connecter OpenID, X509 certificat. 

##<a name="in-this-article"></a>Dans cet article

Les rubriques suivantes sont traitées dans cet article :

- [Introduction](media-services-cenc-with-multidrm-access-control.md#introduction)
    - [Vue d’ensemble de cet article](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [Une conception de référence](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [Mappage de conception à la technologie de mise en oeuvre](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [Mise en œuvre](media-services-cenc-with-multidrm-access-control.md#implementation)
    - [Procédures de mise en œuvre](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
    - [Des problèmes de mise en œuvre](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [Rubriques supplémentaires de mise en oeuvre](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
    - [HTTP ou HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
    - [Azure Active Directory substitution de la clé de signature](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
    - [Où se trouve le jeton d’accès ?](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
    - [Qu’en est-il de Live en continu ?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
    - [Que se passe-t-il pour les serveurs de licences en dehors d’Azure Media Services ?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
    - [Que se passe-t-il si je souhaite utiliser un STS personnalisé ?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [Le test et le système terminés](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
    - [Connexion de l’utilisateur](media-services-cenc-with-multidrm-access-control.md#user-login)
    - [À l’aide des Extensions Media crypté pour PlayReady](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
    - [L’utilisation de EME pour Widevine](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
    - [Utilisateurs non autorisés.](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
    - [Secure Service jeton personnalisé en cours d’exécution](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [Résumé](media-services-cenc-with-multidrm-access-control.md#summary)

##<a name="introduction"></a>Introduction

Il est bien connu qu’il s’agit d’une tâche complexe à concevoir et à construire un sous-système DRM pour un OTT ou en ligne en flux continu de la solution. Et c’est une pratique courante pour les fournisseurs de vidéos/en ligne les opérateurs d’externaliser cette partie aux prestataires de services spécialisés DRM. L’objectif de ce document est de présenter une conception de référence et la mise en oeuvre du sous-système DRM de bout en bout dans OTT ou solution de diffusion en ligne.

Les lecteurs ciblés de ce document sont des ingénieurs travaillant dans le sous-système DRM de OTT ou de solutions en ligne en continu/multi-screen ou les lecteurs intéressés par le sous-système DRM. Il est supposé que les lecteurs sont familiarisés avec au moins une des technologies DRM sur le marché, tels que PlayReady, Widevine, FairPlay ou accès d’Adobe.

Par DRM, nous avons également inclure CENC (cryptage courants) avec multi-DRM. Une tendance majeure dans l’industrie OTT et de la diffusion en ligne consiste à utiliser CENC avec multi-native-DRM sur différentes plates-formes de clients, un travail d’équipe à partir de la tendance précédente de l’utilisation d’un seul DRM et son client SDK pour différentes plateformes clientes. Lorsque vous utilisez CENC avec multiples native-DRM, PlayReady et Widevine sont cryptés par la spécification de [Chiffrement commune (CENC ISO/IEC 23001-7)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Les avantages de la CENC avec multi-DRM sont les suivantes :

1. Réduit de cryptage, dans la mesure où un traitement de chiffrement est utilisé, ciblant différentes plateformes avec ses DRMs natifs ;
1. Réduit le coût de la gestion des ressources cryptées car une seule copie des actifs cryptés est nécessaire ;
1. Élimine le client DRM coût de licence depuis le client DRM natif est généralement libre sur leur plate-forme native.

Microsoft a été un promoteur actif de tiret et CENC avec certains acteurs majeurs de l’industrie. Microsoft Azure Media Services propose la prise en charge de tiret et CENC. Pour les annonces récentes, veuillez consulter les blogs de Mingfei : [services de livraison de licence Widevine de Google annonce dans Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)et [Azure Media Services ajoute emballage Widevine de Google pour fournir un flux continu de multi-DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Vue d’ensemble de cet article

L’objectif de cet article comprend les éléments suivants :

1. Fournit un modèle de référence du sous-système DRM avec CENC multi-DRM ;
1. Fournit une implémentation de référence sur la plate-forme de Microsoft Azure/Azure Media Services ;
1. Traite de certaines rubriques de conception et d’implémentation.

Dans l’article, « multi-DRM » couvre les sujets suivants :

1. Microsoft PlayReady
1. Widevine de Google
1. FairPlay d’Apple (pas encore pris en charge par les Services de support Azure)

Le tableau suivant résume la plate-forme/native natif app et navigateurs pris en charge par chaque DRM.

**Plate-forme de client**|**Prise en charge native de DRM**|**Navigateur/App**|**Formats de transmission en continu**
----|------|----|----
**Télévisions intelligentes, opérateur décodeurs, les décodeurs de OTT**|PlayReady principalement, Widevine, ou autre|Linux, Opera, WebKit, autres|Divers formats
**Périphériques Windows 10 (PC Windows, tablettes de Windows, Windows Phone, Xbox)**|PlayReady|MS bord/IE11/EME<br/><br/><br/>UWP|TIRET (pour TLS, PlayReady n'est pas pris en charge)<br/><br/>TIRET, de la diffusion en continu lisse (pour TLS, PlayReady n'est pas pris en charge) 
**Appareils Android (téléphone, tablette, TV)**|Widevine|Chrome/EME|TIRET
**iOS (iPhone, iPad), les clients OS X et Apple TV**|FairPlay|Safari 8 / EME|TLS
**Plug-in : Emploi d’Adobe**|Accès d’emploi|Plug-in de navigateur|HDS, TLS

Compte tenu de l’état en cours de déploiement pour chaque DRM, un service souhaitez généralement 2 ou 3 DRMs pour vous assurer que vous adressez à tous les types de points de terminaison de la meilleure manière de mettre en œuvre.

Il existe un compromis entre la complexité de la logique de service et de la complexité du côté client pour atteindre un certain niveau d’expérience de l’utilisateur sur les clients différents.

Pour faire votre sélection, gardez à l’esprit ces faits :

- PlayReady est implémentée en mode natif dans chaque périphérique Windows, sur certains appareils Android et disponibles par le biais de kits de développement logiciel sur pratiquement n’importe quelle plate-forme
- Widevine est en mode natif implémentée dans chaque appareil Android, chrome et dans certains autres périphériques
- FairPlay est disponible uniquement sur les e/s et les clients Mac OS ou via iTunes.

Par conséquent, un multi-DRM typique serait :

- Option 1 : PlayReady et Widevine
- Option 2 : PlayReady, Widevine et FairPlay


## <a name="a-reference-design"></a>Une conception de référence

Dans cette section, nous présenterons une conception de référence qui est agnostique quant aux technologies utilisées pour l’implémenter.

Un sous-système DRM peut contenir les éléments suivants :

1. Gestion des clés
1. Emballage de DRM
1. Livraison de licence DRM
1. Vérification des droits
1. Authentification/autorisation
1. Lecteur
1. Origine/CDN

Le diagramme suivant illustre l’interaction de haut niveau entre les composants d’un sous-système DRM.

![Sous-système DRM avec CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
Il existe trois base « couches » de la conception :

1. Couche de back-office (en noir) qui ne sont pas exposées en externe ;
1. Couche de « DMZ » (bleu) contenant tous les points de terminaison face au public ;
1. Couche de Internet public (bleu clair) contenant CDN et joueurs avec le trafic sur le réseau Internet public.
 
Il doit y avoir un outil de gestion de contenu pour contrôler la protection DRM, quel que soit le cryptage statique ou dynamique. Les entrées pour le cryptage de DRM doivent inclure :

1. Contenu vidéo de MBR ;
1. Clé de contenu ;
1. URL d’acquisition de licence.

Au cours de la durée de lecture, le flux de haut niveau est :

1. Utilisateur est authentifié ;
1. Jeton d’autorisation est créée pour l’utilisateur.
1. Le contenu protégé par DRM (manifeste) est téléchargé vers le lecteur ;
1. Le lecteur envoie la demande d’acquisition de licence pour les serveurs de licences ainsi que l’ID de clé et l’autorisation jeton.

Avant de passer à la rubrique suivante, quelques mots sur la conception de la gestion des clés.

**ContentKey – à des ressources**|**Scénario**
------|---------------------------
– 1 à 1|Le cas le plus simple. Il offre un contrôle plus fin. Mais cela se traduit généralement par les frais de livraison de licence plus élevés. Au minimum licence une seule demande est requise pour chaque ressource protégée.
1-à-plusieurs|Vous pouvez utiliser la même clé de contenu pour plusieurs immobilisations. Par exemple, tous les capitaux d’un groupe logique un genre ou d’un sous-ensemble de genre (ou de Gene de film), vous pouvez utiliser une seule clé de contenu.
1-à-plusieurs|Plusieurs clés de contenu sont nécessaires pour chaque immobilisation. <br/><br/>Par exemple, si vous avez besoin d’appliquer une protection CENC dynamique avec multi-DRM pour MPEG-tiret et le chiffrement AES-128 dynamique pour TLS, vous avez besoin de deux clés de contenu distincts, chacun avec sa propre ContentKeyType. (Pour la clé de contenu dynamique de protection de CENC, ContentKeyType.CommonEncryption doit être utilisé, tandis que pour la clé de contenu utilisée pour le chiffrement AES-128 dynamique, ContentKeyType.EnvelopeEncryption doit être utilisé.)<br/><br/>Un autre exemple de protection des CENC de contenu tiret, en théorie, une clé de contenu peut être utilisée pour protéger les flux vidéo et une autre clé de contenu pour protéger des flux de données audio. 
Plusieurs-à - plusieurs|Combinaison des deux scénarios ci-dessus : un jeu de clés de contenu sont utilisés pour plusieurs éléments de l’actif même « groupe ».


Un autre facteur important à considérer est l’utilisation de licences persistants et non persistants.

Pourquoi les ces considérations sont importantes ? 

Ils ont un impact direct sur les frais de livraison de licence si vous utilisez le cloud public pour la remise de la licence. Prenons les deux cas suivants de conception différentes pour illustrer :



1. Abonnement mensuel : utiliser une licence permanente et le mappage de touche-à-actif contenu 1-à-plusieurs. Par exemple pour les films enfants, nous utilisons une seule clé pour le cryptage de contenu. Dans ce cas : 

    Nombre total de licences # demandés pour tous les enfants de films/périphérique = 1

1. Abonnement mensuel : utilisez la licence non persistante et mappage 1 à 1 entre les actifs et de la clé de contenu. Dans ce cas :

    Nombre total de licences # demandés pour tous les enfants de films/périphérique = [surveillés de films #] x [sessions #]

Comme vous pouvez le voir, les deux conceptions différentes entraînent dans les modèles de demande de licence très différentes, par conséquent, livraison de licence coût si le service de livraison de licence est fourni par un cloud public telles que Azure Media Services.

## <a name="mapping-design-to-technology-for-implementation"></a>Mappage de conception à la technologie de mise en oeuvre

Ensuite, nous allons associer notre conception générique aux technologies de plate-forme de Microsoft Azure/Azure Media Services, en spécifiant la technologie à utiliser pour chaque bloc de construction.

Le tableau suivant montre le mappage :

**Bloc de construction**|**Technologie**
------|-------
**Lecteur**|[Lecteur Media Azure](https://azure.microsoft.com/services/media-services/media-player/)
**Fournisseur d’identité (IDP)**|Azure Active Directory
**Service de jeton de sécurité (STS)**|Azure Active Directory
**Flux de travail de Protection DRM**|Protection dynamique des Services multimédia pour Azure
**Livraison de licence DRM**|1. azure Media Services de livraison de licence (PlayReady, Widevine, FairPlay), ou <br/>2. serveur de licences de Axinom <br/>3. serveur de licences PlayReady personnalisé
**Origine**|Transmission en continu de point de terminaison des Services multimédia pour Azure
**Gestion des clés**|Pas nécessaire pour l’implémentation de référence
**Gestion de contenu**|Une application de console C#

En d’autres termes, à la fois fournisseur d’identité (IDP) et Secure Token Services (STS) sera AD Azure. Pour le lecteur, nous allons utiliser [l’API Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). À la fois Azure Media Services et Azure Media Player prend en charge tiret et CENC avec multi-DRM.

Le diagramme suivant montre la structure globale et du flux avec le mappage de technologie ci-dessus.

![CENC sur AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Pour configurer le cryptage dynamique de CENC, l’outil de gestion de contenu utilisera les entrées suivantes :

1. Contenu de l’ouvrir ;
1. Clé de contenu à partir de la clé de génération et de gestion ;
1. URL d’acquisition de licence ;
1. Une liste d’informations à partir d’AD Azure.

La sortie de l’outil de gestion de contenu sera :

1. ContentKeyAuthorizationPolicy, contenant la spécification de la livraison de licence vérifie un jeton JWT et spécifications de licence DRM ;
1. AssetDeliveryPolicy contenant des spécifications en continu au format, protection DRM et URL d’acquisition de licence.

Lors de l’exécution, le flux est comme ci-dessous :

1. Lors de l’authentification des utilisateurs, un jeton JWT est généré ;
1. Les revendications contenues dans le jeton JWT est demande de « groupes » qui contient l’ID d’objet de groupe de « EntitledUserGroup ». Cette demande sera utilisée pour le passage de « vérification du droit ».
1. Lecteur téléchargements client manifeste d’un CENC le contenu protégé et « voit » le texte suivant :
    1. ID de clé 
    1. le contenu est protégé, de CENC
    1. URL d’acquisition de licence.

1. Le lecteur effectue une demande de d’acquisition de licence basée sur le navigateur/DRM pris en charge. ID de clé dans la demande d’acquisition de licence, et le jeton JWT sera également envoyé. Service de livraison de licence vérifie le jeton JWT et les revendications contenues avant la délivrance de la licence requise.

##<a name="implementation"></a>Mise en œuvre

###<a name="implementation-procedures"></a>Procédures de mise en œuvre

La mise en œuvre comprend les étapes suivantes :

1. Préparer les tests actifs : colis encoder une vidéo test multi-bitrate fragmenté MP4 dans Azure Media Services. Cet actif n’est pas protégé par DRM. Protection DRM sera effectuée par la protection dynamique ultérieurement.
1. Créer la clé ID et un contenu clé (éventuellement à partir de semences de clé). Dans notre cas, le système de gestion de clés n’est pas nécessaire dans la mesure où nous avons affaire à un seul ensemble de clés d’ID et la clé de contenu pour deux ressources de test ;
1. AMS API permet de configurer les services de livraison de licence multi-DRM pour l’élément de test. Si vous utilisez des serveurs de licence personnalisée par les fournisseurs de votre société au lieu des services de licence dans Azure Media Services ou de votre société, vous pouvez ignorer cette étape et spécifier l’URL d’acquisition de licence lors de l’étape de configuration de la remise de la licence. AMS API est nécessaire pour spécifier les que configurations, par exemple la restriction de la stratégie d’autorisation, les modèles de réponse de licence pour les différents services de licence DRM, etc. détaillé. À ce stade, le portail Azure ne fournit pas encore l’interface utilisateur nécessaire pour cette configuration. Vous pouvez trouver des informations de niveau API et exemple de code document de Julia Kornich : [à l’aide de PlayReady et/ou Widevine de cryptage commun dynamique](media-services-protect-with-drm.md). 
1. Utiliser les API de l’AMS pour configurer la stratégie de livraison d’immobilisation pour l’immobilisation de test. Vous pouvez trouver des informations de niveau API et exemple de code document de Julia Kornich : [à l’aide de PlayReady et/ou Widevine de cryptage commun dynamique](media-services-protect-with-drm.md).
1. Créer et configurer un locataire Azure Active Directory dans Azure ;
1. Créer plusieurs comptes d’utilisateurs et des groupes dans votre client Azure Active Directory : vous devez créer au moins « EntitledUser » de groupe et d’ajouter un utilisateur à ce groupe. Les utilisateurs de ce groupe passera à cocher le droit d’acquisition de licence et les utilisateurs pas dans ce groupe ne pourra pas passer la vérification de l’authentification et ne sera pas en mesure d’acquérir une licence. Un membre de ce groupe de « EntitledUser » est une revendication requis « groupes » dans le jeton JWT délivré par AD Azure. Cette exigence de réclamation doit être spécifiée dans la configuration d’étape de services de livraison de licence multi-DRM.
1. Créer une application ASP.NET MVC qui hébergera votre lecteur vidéo. Cette application ASP.NET sera protégée par l’authentification des utilisateurs contre le locataire Azure Active Directory. Des déclarations appropriées seront incluses dans le jeton d’accès obtenu après authentification de l’utilisateur. API de connexion OpenID est recommandé pour cette étape. Vous devez installer les packages NuGet suivants :
    - Package d’installation Microsoft.Azure.ActiveDirectory.GraphClient
    - Package d’installation Microsoft.Owin.Security.OpenIdConnect
    - Package d’installation Microsoft.Owin.Security.Cookies
    - Package d’installation Microsoft.Owin.Host.SystemWeb
    - Package d’installation Microsoft.IdentityModel.Clients.ActiveDirectory
1. Créer un lecteur à l’aide des [API d’Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). [Azure le lecteur ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) vous permet de spécifier la technologie DRM à utiliser sur une autre plate-forme DRM.
1. Matrice de test :

**DRM**|**Navigateur**|**Résultat pour le droit utilisateur**|**Résultat pour un utilisateur non autorisé**
---|---|-----|---------
**PlayReady**|Bord de MS ou IE11 sous Windows, 10|Réussir|Échec
**Widevine**|Chrome sur Windows 10|Réussir|Échec
**FairPlay** |À DÉFINIR||

George Trifonov d’Azure Media Services équipe a écrit un blog fournit la procédure détaillée de la configuration d’Azure Active Directory pour une application de lecteur d’ASP.NET MVC : [intégrer Azure Media Services OWIN MVC en fonction de l’application avec Azure Active Directory et limiter la remise de clés contenu basé sur les revendications JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

George a également écrit un blog sur [l’authentification des jetons JWT dans Azure Media Services et le cryptage dynamique](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). Et Voici son [exemple sur l’intégration de publicités Azure avec distribution de clé Azure Media Services](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Pour plus d’informations sur Azure Active Directory :

- Vous pouvez trouver des informations destinées aux développeurs dans [Azure Active Directory Guide du développeur de](../active-directory/active-directory-developers-guide.md).
- Vous pouvez trouver des informations sur l’administrateur dans [Administrer Your Directory Azure AD](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Des problèmes de mise en œuvre

Il existe certains « pièges » dans la mise en oeuvre. Nous espérons que la liste des « pièges » ci-dessous peut vous aider si vous rencontrez des problèmes de dépannage.

1. **Émetteur** URL doit se terminer par **« / »**.  

    **Public** doit être l’ID client d’application lecteur et vous devez également ajouter **« / »** à la fin de l’URL de l’émetteur.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

    Dans un [Décodeur de JWT](http://jwt.calebb.net/), vous devez voir **aud** et **iss** comme ci-dessous dans le jeton de JWT :
    
    ![Piège n 1er](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. Ajouter des autorisations à l’application de DAS (sur l’onglet de configuration de l’application). Ceci est nécessaire pour chaque application (les versions locales et déployées).

    ![Piège n 2](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. Utilisez l’émetteur de droite dans la configuration de la protection de CENC dynamique :

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    
    Les éléments suivants ne fonctionnent pas :
    
        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    
    Le GUID est l’ID du client DAS. Le GUID sont accessibles dans le menu contextuel de points de terminaison dans Azure portal.

4. L’appartenance au groupe de subvention de revendications des privilèges. Vérifiez dans le fichier manifeste d’application DAS, nous avons le suivant

    « groupMembershipClaims » : « Toutes », (la valeur par défaut est null.)

5. Définition de TokenType approprié lors de la création de conditions de restriction.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Depuis l’ajout de la prise en charge de JWT (DAS) en plus des SWT (ACS), la valeur par défaut de TokenType est TokenType.JWT. Si vous utilisez SWT/ACS, vous devez définir à TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Rubriques supplémentaires de mise en oeuvre
Ensuite nous sera disque uss certaines rubriques supplémentaires de notre conception et d’implémentation.

###<a name="http-or-https"></a>HTTP ou HTTPS ?

L’application de lecteur ASP.NET MVC que nous avons créé doit prendre en charge les éléments suivants :

1. Authentification des utilisateurs par le biais de publicité Azure qui doit être sous HTTPS ;
1. Exchange de jeton JWT entre le client et Azure AD qui doit être sous HTTPS ;
1. Acquisition de licence DRM par le client qui doit être sous HTTPS si la livraison de licence fourni par Azure Media Services. Bien entendu, suite de produits de PlayReady ne force pas HTTPS pour la remise de la licence. Si votre serveur de licences PlayReady est en dehors d’Azure Media Services, HTTP ou HTTPS peut être utilisé.

Par conséquent, l’application de lecteur ASP.NET utilise HTTPS comme une meilleure pratique. Cela signifie que le lecteur de Media Azure sera sur une page sous HTTPS. Toutefois, pour la diffusion en continu nous préférons HTTP, par conséquent, nous devons prendre en compte les problèmes liés au contenu mixte.

1. Navigateur n’autorise pas de contenu mixte. Mais les plug-ins tels que les plug-in Silverlight et OSMF pour lisser et le tiret. Le contenu mixte est un problème de sécurité - cela est dû à la menace de la capacité à injecter JS malveillant qui peut entraîner les données du client être exposés.  Navigateurs bloquent ce paramètre par défaut et la seule façon de le contourner est côté serveur (origine), pour permettre à tous les domaines (quel que soit le https ou http). Ce n’est probablement pas soit une bonne idée.
1. Nous devons éviter un contenu mixte : les deux utilisent le protocole HTTP, soit tous les deux utilisent le protocole HTTPS. Lors de la lecture d’un contenu mixte, silverlightSS tech requiert la suppression d’un avertissement sur le contenu mixte. technique de flashSS gère le contenu mixte sans avertissement de contenu mixte.
1. Si votre point de terminaison en continu a été créé avant août 2014, il ne prendra pas en charge HTTPS. Dans ce cas, créez et utilisez un nouveau point de terminaison en continu pour le protocole HTTPS.

Dans l’implémentation de référence, pour le contenu protégé par DRM, application et la diffusion en continu se sera sous HTTTPS. Pour le contenu ouvert, le lecteur ne doit pas l’authentification ou la licence, afin d’avoir la liberté d’utiliser HTTP ou HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory substitution de la clé de signature

Il s’agit d’un point important à prendre en considération de votre implémentation. Si vous considérez ceci dans votre implémentation, le système terminé sera finalement cesser de fonctionner complètement dans 6 semaines au plus.

Annonce Azure utilise la norme industrielle pour établir l’approbation entre lui-même et les applications à l’aide d’Active Directory Azure. Plus précisément, AD Azure utilise une clé de signature qui se compose d’une paire de clés publique et privée. Lorsque l’annonce Azure crée un jeton de sécurité qui contient des informations sur l’utilisateur, ce jeton est signé par Azure AD à l’aide de sa clé privée avant d’être envoyé à l’application. Pour vérifier que le jeton est valide et effectivement origines d’Azure AD, l’application doit valider signature du jeton à l’aide de la clé publique exposée par AD Azure qui est contenue dans le document de métadonnées de fédération du locataire. Cette clé publique et la clé de signature à partir de laquelle elle dérive, sont le même que celui utilisé pour tous les locataires dans Azure AD.

Vous trouverez des informations détaillées sur la substitution de la clé AD Azure dans le document : [Informations importantes concernant la signature de clé survol dans Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Entre la [paire de clés publique / privée](https://login.windows.net/common/discovery/keys/), 

- La clé privée est utilisée par Azure Active Directory pour générer un jeton JWT ;
- La clé publique est utilisée par une application, tels que les Services de livraison de licence DRM dans AMS afin de vérifier le jeton JWT ;
 
À des fins de sécurité, Azure Active Directory fait pivoter ce certificat périodiquement (toutes les 6 semaines). En cas de violations de la sécurité, la substitution de la clé peut survenir à tout moment. Les services de livraison de licence dans AMS doivent mettre à jour la clé publique utilisée comme AD Azure fait pivoter la paire de clés, sinon l’authentification des jetons dans AMS échoue et aucune licence n’est émis. 

Cela s’effectue en définissant des TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument lors de la configuration des services de livraison de licence DRM.

Le flux de jeton JWT est comme ci-dessous :

1.  Annonce Azure émet le jeton JWT avec la clé privée en cours pour un utilisateur authentifié ;
2.  Lorsqu’un lecteur voit un CENC avec du contenu multi-DRM protégé, il d’abord localiser le jeton JWT délivré par AD Azure.
3.  Le lecteur envoie la demande d’acquisition de licence avec le jeton JWT aux services de livraison de licence dans AMS ;
4.  Les services de livraison de licence dans AMS utilise la clé publique en cours/valides à partir d’AD Azure afin de vérifier le jeton JWT, avant de délivrer des licences.

Services de livraison de licence DRM seront toujours chargé de vérifier pour la clé publique en cours/valides à partir d’AD Azure. La clé publique présentée par AD Azure sera la clé utilisée pour vérifier un jeton JWT délivré par AD Azure.

Que se passe-t-il si la substitution de la clé se produit après que DAS génère un jeton JWT mais avant du JWT le jeton est envoyé par les joueurs aux services de livraison licence DRM dans AMS pour la vérification ? 

Dans la mesure où une clé peut être annulée à tout moment, il est toujours plus d’une clé publique valide disponible dans le document de métadonnées de fédération. Livraison de licence Azure Media Services peut utiliser chacune des touches spécifiées dans le document, dans la mesure où une clé peut être annulée plus rapidement, un autre peut être son remplacement et ainsi de suite.

### <a name="where-is-the-access-token"></a>Où se trouve le jeton d’accès ?

Si vous observez comment une application web appelle une application API sous [l’Identité de l’Application des droits de d’informations d’identification Client OAuth 2.0](active-directory-authentication-scenarios.md#web-application-to-web-api), le flux d’authentification est comme ci-dessous :

1.  Un utilisateur est connecté à une annonce Azure dans l’application web (voir le [Navigateur Web à une Application Web](active-directory-authentication-scenarios.md#web-browser-to-web-application).
2.  Le point de terminaison d’autorisation AD Azure redirige l’agent utilisateur à l’application client avec un code d’autorisation. L’agent utilisateur renvoie un code d’autorisation pour l’URI de redirection de l’application client.
3.  L’application web doit acquérir un jeton d’accès afin qu’il peut s’authentifier auprès de l’API web et récupérer la ressource souhaitée. Il fait une demande au point de terminaison jeton Azure annonce, fournissant les informations d’identification, l’ID de client et ID URI d’application que l’API de web. Il présente le code d’autorisation pour prouver que l’utilisateur a accepté.
4.  Annonce Azure authentifie l’application et retourne un jeton d’accès JWT qui est utilisé pour appeler l’API web.
5.  Via HTTPS, l’application web utilise le jeton d’accès JWT retourné pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête de la demande d’autorisation de l’API web. L’API de web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.

Dans ce flux de le « identité d’application », l’API web approuve que l’utilisateur est authentifié par l’application web. Pour cette raison, ce modèle est appelé un sous-système approuvé. Le [diagramme de cette page](http://msdn.microsoft.com/library/azure/dn645542.aspx/) décrit comment accorder à works de flux code d’autorisation.

Dans l’acquisition de licence avec restriction de jeton, nous continuons au même modèle de sous-système approuvé. Et le service de remise de licence dans Azure Media Services API ressource, le « back-end » une application web doit accéder à Internet. Par conséquent, où se trouve le jeton d’accès ?

En effet, nous sommes obtention de jeton d’accès à partir d’AD Azure. Une fois l’authentification réussie d’un utilisateur, code d’autorisation est retourné. Le code d’autorisation sert ensuite, ainsi que de la clé ID et l’application client à exchange pour le jeton d’accès. Et le jeton d’accès pour accéder à une application de « pointeur » en pointant ou représentant le service de livraison de licence Azure Media Services.

Nous avons besoin inscrire et configurer l’application de « pointeur » dans Azure annonce en suivant les étapes ci-dessous :

1.  Dans les clients AD Azure

    - Ajouter une application (ressource) avec ouverture de session d’URL : 

    https://[resource_name].azurewebsites.NET/ et 

    - URL de l’ID d’application : 
    
    [https://[aad_tenant_name].onmicrosoft.com/[resource_name] ; 
2.  Ajouter une nouvelle clé pour l’application de ressource ;
3.  Mettre à jour le fichier de manifeste d’application afin que la propriété groupMembershipClaims a la valeur suivante : « groupMembershipClaims » : « Toutes »,  
4.  Dans l’application Azure AD pointant vers l’application web de lecteur, dans la section « autorisations à d’autres applications », ajouter l’application de la ressource qui a été ajoutée à l’étape 1 ci-dessus. Sous « délégué des autorisations » vérifier les coches « Accès [nom_ressource] ». Ainsi, l’autorisation d’application web pour créer des jetons d’accès pour accéder à l’application de ressource. Vous devez procéder pour à la fois locale et déployée la version de l’application web si vous développez avec Visual Studio et Azure application de web.
    
Par conséquent, le jeton JWT délivré par AD Azure est en effet le jeton d’accès pour accéder à cette ressource de « pointeur ».

### <a name="what-about-live-streaming"></a>Qu’en est-il de Live en continu ?

Dans l’exemple ci-dessus, notre discussion a été en mettant l’accent sur les ressources à la demande. Qu’en est-il live en continu ?

La bonne nouvelle est que vous pouvez utiliser exactement la même conception et mise en œuvre pour la protection en continu dans Azure Media Services, en traitant l’actif associé à un programme en tant que « VOD actifs ».

En particulier, il est connu que pour faire live en continu dans Azure Media Services, vous devez créer un canal, puis un programme sous le canal. Pour créer le programme, vous devez créer une immobilisation qui contiendra l’archive en ligne du programme. Pour fournir CENC protection multi-DRM du contenu dynamique, vous devez faire, consiste à appliquer le même paramétrage/traitement à l’actif comme s’il était un actif VOD « » avant de démarrer le programme.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>Que se passe-t-il pour les serveurs de licences en dehors d’Azure Media Services ?

Souvent, les clients peuvent ont investi dans la licence de serveurs dans leurs propres données centrer ou hébergés par les fournisseurs de service DRM. Heureusement, Protection du contenu Azure Media Services vous permet de fonctionner en mode hybride : contenu hébergé et protégé dynamiquement dans Azure Media Services, tandis que les licences DRM sont fournis par les serveurs en dehors d’Azure Media Services. Dans ce cas, il existe des considérations suivantes de modifications :

1. Le Service de jeton sécurisé doit émettre des jetons qui sont acceptables et peuvent être vérifiées à la batterie de serveurs de licences. Par exemple, les serveurs de licences Widevine fournies par Axinom requiert un jeton JWT spécifique qui contient « message droit ». Par conséquent, vous devez avoir un STS à émettre ce jeton JWT. Les auteurs ont effectué une telle implémentation et vous trouverez les détails dans le document suivant dans le [Centre de Documentation Azure](https://azure.microsoft.com/documentation/): [À l’aide de Axinom pour fournir des Widevine de licences pour Azure Media Services](media-services-axinom-integration.md). 
1. Vous n’avez plus besoin de configurer un service de livraison de licence (ContentKeyAuthorizationPolicy) dans Azure Media Services. Ce que vous devez faire est de fournir des URL d’acquisition de la licence (pour PlayReady, Widevine et FairPlay) lors de la configuration AssetDeliveryPolicy de configuration CENC avec multi-DRM.
 
### <a name="what-if-i-want-to-use-a-custom-sts"></a>Que se passe-t-il si je souhaite utiliser un STS personnalisé ?

Il peut exister plusieurs raisons que le client peut choisir d’utiliser un service STS (Secure Token Service) personnalisé pour fournir des jetons JWT. Certains d'entre eux sont les suivants :

1.  Le fournisseur identité (IDP) utilisé par le client n’accepte pas de SharePoint Team Services. Dans ce cas un STS personnalisé peut être une option.
2.  Le client ait besoin de contrôle plus souple ou plus étroit dans l’intégration de SharePoint Team Services avec un abonné du client système de facturation. Par exemple, un opérateur MVPD peut offrir plusieurs packages d’abonné OTT comme prime, base, sports, etc.. L’opérateur peut appliquer les revendications dans un jeton avec le package de l’abonné afin que seul le contenu de l’ensemble est mis à disposition. Dans ce cas, un STS personnalisé offre la souplesse nécessaire et le contrôle.

Deux modifications doivent être apportées lors de l’utilisation d’un STS personnalisé :

1.  Lorsque vous configurez le service de livraison de licence pour un capital, vous devez spécifier la clé de sécurité utilisée pour la vérification par le STS personnalisé (plus de détails ci-dessous) au lieu de la clé actuelle d’Azure Active Directory.
2.  Lorsqu’un jeton JTW est généré, une clé de sécurité est spécifiée au lieu de la clé privée de l’actuel X509 certificat dans Azure Active Directory.

Il existe deux types de clés de sécurité :

1.  Clé symétrique : la même clé est utilisée pour générer et vérifier un jeton JWT ;
2.  Clé asymétrique : une paire de clés publique / privée dans un certificat est utilisé avec une clé privée pour le cryptage/générer un jeton JWT et la clé publique pour vérifier le jeton de X509.

####<a name="tech-note"></a>Note technique

Si vous utilisez.NET Framework / C# en tant que plate-forme de développement, la X509 certificat utilisé pour la clé de sécurité asymétrique doit avoir au moins 2048 octets la longueur de clé. Il s’agit d’une exigence de la classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey dans.NET Framework. Dans le cas contraire, l’exception suivante est levée :

IDX10630 : 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' pour la signature ne peut pas être inférieure à « 2048 » bits. 

## <a name="the-completed-system-and-test"></a>Le test et le système terminés

Nous étudierons quelques scénarios dans le système de bout en bout terminé afin que les lecteurs puissent avoir une « image » du comportement du basic avant d’obtenir un compte de connexion.

L’application web du lecteur et sa connexion d’accès, vous pouvez trouver [ici](https://openidconnectweb.azurewebsites.net/).

Si ce dont vous avez besoin est non-« intégrée » de scénario : éléments vidéo hébergés dans Azure Media Services qui sont protégés, ou DRM protégé mais sans l’authentification des jetons (délivrance d’une licence à quiconque demande), vous pouvez le tester sans connexion (par commutation à HTTP si votre vidéo en flux continu est sur HTTP).

Si ce dont vous avez besoin est scénario intégrée de bout en bout : éléments vidéo est sous protection DRM dynamique dans Azure Media Services, et d’authentification de jeton jeton JWT généré par AD Azure, vous devez vous connecter.

### <a name="user-login"></a>Connexion de l’utilisateur

Pour tester le système DRM intégré de bout en bout, vous devez disposer d’un « compte » créé ou ajouté. 

Quel compte ?

Mais à l’origine, Azure autorise uniquement l’accès à des utilisateurs de comptes Microsoft, il permet désormais l’accès par les utilisateurs des deux systèmes. Ceci, en demandant à tous l’approbation des propriétés Azure Azure AD pour l’authentification, ayant AD Azure à authentifier les utilisateurs de l’organisation et en créant une relation de fédération où AD Azure approuve le système d’identité Microsoft compte consommateur pour authentifier les utilisateurs du consommateur. Par conséquent, Azure AD est en mesure d’authentifier les comptes « invité » de Microsoft ainsi que les comptes AD Azure « natifs ».

Dans la mesure où Active Directory Azure approuve le domaine de compte MSA (Microsoft), vous pouvez ajouter tous les comptes à partir d’un des domaines suivants l’annonce Azure personnalisée client et utilisent le compte d’ouverture de session :

**Nom de domaine**|**Domaine**
-----------|----------
**Domaine du locataire personnalisé AD Azure**|somename.onmicrosoft.com
**Domaine de l’entreprise**|Microsoft.com
**Domaine de compte MSA (Microsoft)**|Outlook.com, live.com, hotmail.com

Vous pouvez contacter un des auteurs d’avoir un compte créé ou ajouté pour vous. 

Voici les captures d’écran des pages de connexion différents utilisés par les différents comptes de domaine.

**Azure personnalisée compte de domaine clients AD**: dans ce cas, vous consultez la page de connexion personnalisé personnalisé domaine des clients AD Azure.

![Compte de domaine personnalisé AD Azure locataire](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Compte de domaine Microsoft avec carte à puce**: dans ce cas, vous consultez la page de connexion personnalisée par Microsoft corporate IT avec l’authentification à deux facteurs.

![Compte de domaine personnalisé AD Azure locataire](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Compte MSA (Microsoft)**: dans ce cas, vous consultez la page de connexion de Microsoft Account pour les consommateurs.

![Compte de domaine personnalisé AD Azure locataire](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>À l’aide des Extensions Media crypté pour PlayReady

Sur un navigateur moderne avec Extensions Media crypté (EME) pour PlayReady support, comme 11 d’Internet Explorer sur Windows 8.1 et vers le haut et le navigateur de Microsoft Edge sur Windows 10, PlayReady sera la DRM sous-jacent pour EME.

![L’utilisation de EME pour PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

La zone du lecteur foncé est dû au fait que PlayReady protection empêche une d’effectuer des captures d’écran de la vidéo protégé. 

L’écran suivant montre les plug-ins du lecteur et la prise en charge MSE/EME.

![L’utilisation de EME pour PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME de Microsoft Edge et 11 d’Internet Explorer sur Windows 10 permet l’appel de [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) sur les périphériques Windows 10 qui prennent en charge. PlayReady SL3000 déverrouille le flux de contenu de qualité supérieure amélioré (4K, HDR, etc.) et de nouveaux modèles de fourniture de contenu (fenêtre au plus tôt pour un contenu optimisé).

Se concentrer sur les périphériques Windows : PlayReady est la seule DRM dans le matériel disponible sur les périphériques Windows (PlayReady SL3000). Un service de diffusion en continu PlayReady via EME ou via une application UWP et d’offrir une qualité vidéo supérieure à l’aide de PlayReady SL3000 que DRM d’un autre. En règle générale, les contenus de 2K transitera Chrome ou Firefox et contenu de 4 Ko par le biais de Microsoft Edge/IE11 ou une application UWP sur le même périphérique (en fonction des paramètres de service et la mise en œuvre).


#### <a name="using-eme-for-widevine"></a>L’utilisation de EME pour Widevine

Sur un navigateur moderne avec EME/Widevine prise en charge, telle que Chrome 41 + sur Windows 10, 8.1 de Windows, Mac OSX Yosemite et Chrome sur 4.4.4 Android, Google Widevine est l’application DRM derrière le EME.

![L’utilisation de EME pour Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Notez que Widevine n’empêche pas une à partir de la fabrication de capture d’écran de la vidéo protégé.

![L’utilisation de EME pour Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Utilisateurs non autorisés.

Si un utilisateur n’est pas un membre du groupe « Droit des utilisateurs », l’utilisateur ne sera pas en mesure de passer de « vérification du droit » et le service de licence multi-DRM refuse de délivrer la licence demandée, comme indiqué ci-dessous. La description détaillée est « acquisition de licence a échoué », qui est conçu.

![Utilisateurs non autorisés.](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Secure Service jeton personnalisé en cours d’exécution

Pour le scénario d’exécution personnalisées Service (jeton de sécurité), le jeton JWT sera délivré par le STS personnalisé à l’aide de la clé symétrique ou asymétrique. 

Le cas de l’utilisation d’une clé symétrique (à l’aide de Chrome) :

![STS personnalisés en cours d’exécution](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Le cas de l’utilisation d’une clé asymétrique via un X509 du certificat (à l’aide du navigateur moderne de Microsoft).

![STS personnalisés en cours d’exécution](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Dans les deux cas ci-dessus, authentification de l’utilisateur reste le même : via AD Azure. La seule différence est que les jetons JWT sont émises par le STS personnalisé au lieu d’Azure AD. Bien entendu, lors de la configuration de la protection de CENC dynamique, la restriction du service de livraison de licence spécifie le type de jeton JWT, clé symétrique ou asymétrique.

## <a name="summary"></a>Résumé

Dans ce document, nous avons parlé CENC avec multiples native-DRM et contrôle d’accès via l’authentification de jeton : sa conception et sa mise en oeuvre à l’aide d’Azure, Azure Media Services et Azure Media Player.

- Une conception de référence s’affiche qui contient tous les composants nécessaires dans un sous-système DRM/CENC ;
- Une implémentation de référence sur Azure, Azure Media Services et Azure Media Player.
- Certaines rubriques directement impliqués dans la conception et l’implémentation sont également abordées.


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Accusés de réception 

William Zhang, Mingfei Yan, Roland Le Franc, Kilroy Hughes, Julia Kornich
