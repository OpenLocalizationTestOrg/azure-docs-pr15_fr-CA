<properties 
    pageTitle="Insertion de publicités sur le côté client | Microsoft Azure" 
    description="Cette rubrique explique comment insérer des publicités sur le côté client." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="inserting-ads-on-the-client-side"></a>Insertion de publicités sur le côté client

Cette rubrique contient des informations sur la façon d’insérer divers types de publicités sur le côté client.

Pour plus d’informations sur le support de sous-titrage et ad fermé dans Live en continu de vidéos, consultez [prise en charge de sous-titrage et de normes d’Insertion de publicité](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

>[AZURE.NOTE] Azure Media Player ne prend pas actuellement en charge les annonces.

##<a id="insert_ads_into_media"></a>Insertion de publicités dans vos médias

Azure Media Services fournit la prise en charge pour l’insertion de publicité par l’intermédiaire de la plate-forme Windows Media : infrastructures de lecteur. Infrastructures de lecteur avec prise en charge d’Active Directory sont disponibles pour les périphériques Windows 8, Silverlight, Windows Phone 8 et e/s. Chaque structure de lecteur contient un exemple de code qui montre comment implémenter une application de lecteur. Il existe trois différents types de publicités que vous pouvez insérer dans votre liste : media.

- **Linéaire** – annonces plein cadre suspendre la vidéo principale.
- **Non linéaire** : annonces de superposition sont affichées, comme la lecture de la vidéo principale, généralement un logo ou une autre image statique est placé dans le lecteur.
- **Le Guide** – annonces qui s’affichent en dehors du lecteur.

Les annonces peuvent être placés à n’importe quel point dans la chronologie de la vidéo principale. Vous devez indiquer le lecteur quand jouer à la publicité et les publicités à lire. Pour cela, à l’aide d’un ensemble de fichiers XML standard : modèle de Service publicité vidéo (VAST), Digital Video plusieurs annonces sélection (VMAP), modèle de séquençage abstraite Media (mat) et Digital Video Player Ad Interface définition (VPAID). Fichiers de grandes spécifient les annonces à afficher. Fichiers VMAP spécifier à quel moment jouer différentes annonces et contient du code XML de grande. Les fichiers de mât sont un autre moyen d’annonces de séquence peut comporter elle aussi vaste XML. Les fichiers VPAID définissent une interface entre le lecteur vidéo et l’annonce ou le serveur Active Directory.

Framework de chaque lecteur fonctionne de façon différente et chacun sera traitée dans sa propre rubrique. Cette rubrique décrit les mécanismes de base utilisées pour insérer des publicités. Les applications de lecteur vidéo demander des annonces à partir d’un serveur Active Directory. Le serveur Active Directory peut répondre de plusieurs façons :

- Retourner un fichier large
- Renvoyer un fichier VMAP (avec VAST incorporé)
- Renvoyer un mât fichier (incorporé VAST)
- Retourner un fichier large avec des annonces VPAID

 
###<a name="using-a-video-ad-service-template-vast-file"></a>À l’aide d’un fichier de modèle (VAST) du Service publicité vidéo

Un vaste fichier spécifie quelle annonce ou les annonces à afficher. Le code XML suivant est un exemple d’un fichier pour une publicité linéaire de grande :
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
    
La publicité linéaire est décrite par le **<Linear>** élément. Il spécifie la durée de l’annonce, le suivi des événements, cliquez sur, cliquez sur suivi et un nombre de **<MediaFile>** éléments. Événements de suivi sont définies dans le **<TrackingEvents>** élément et permettre à un serveur Active Directory effectuer le suivi de différents événements qui se produisent lors de l’affichage de la publicité. Dans ce cas, le début, milieu, complète et développez événements suivis. L’événement de démarrage se produit lorsque la publicité est affichée. L’événement milieu se produit lorsqu’au moins 50 % de la chronologie de l’annonce a été affichée. L’événement complete se produit lorsque la publicité a été exécuté à la fin. L’événement Expand se produit lorsque l’utilisateur développe le lecteur vidéo en mode plein écran. Clics sont spécifiés avec un **<ClickThrough>** élément dans une **<VideoClicks>** élément et spécifie un URI à une ressource à afficher lorsque l’utilisateur clique sur la publicité. ClickTracking est spécifié dans une **<ClickTracking>** élément, également dans les **<VideoClicks>** élément et spécifie une ressource de suivi pour le lecteur à la demande lorsque l’utilisateur clique sur la publicité. Le **<MediaFile>** éléments spécifient les informations à propos d’un codage spécifique d’une annonce. S’il existe plus de **<MediaFile>** élément, le lecteur vidéo peut choisir le meilleur codage possible pour la plate-forme. 

Des annonces linéaires peuvent être affichées dans un ordre spécifié. Pour ce faire, ajouter d’autres <Ad> éléments à la VAST de fichiers et spécifier la commande à l’aide de l’attribut de la séquence. L’exemple suivant illustre cela :
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
    
Annonces non linéaires sont spécifiées dans un <Creative> élément ainsi. L’exemple suivant montre un <Creative> élément qui décrit une publicité non linéaire.

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>

 
Le **<NonLinearAds>** élément peut contenir un ou plusieurs **<NonLinear>** éléments, dont chacun peut décrire une publicité non linéaire. Le **<NonLinear>** élément spécifie la ressource pour la publicité non linéaire. La ressource peut être une **<StaticResouce>**, **<IFrameResource>**, ou **<HTMLResouce>**.**<StaticResource>** Décrit une ressource non HTML et définit un attribut creativeType qui spécifie le mode d’affichage de la ressource :

Image/gif, image/jpeg, image/png – la ressource est affichée dans un élément HTML **<img>** balises.

Application/x-javascript – la ressource s’affiche dans une balise HTML <**script**>.

Application/x-shockwave-flash – la ressource s’affiche dans un lecteur Flash.

**<IFrameResource>**Décrit une ressource HTML qui peut être affichée dans un IFrame. **<HTMLResource>**Décrit un fragment de code HTML qui peut être inséré dans une page web. **<TrackingEvents>**spécifier les événements de suivi et de l’URI de la demande lorsque l’événement se produit. Dans cet exemple, les événements d’acceptInvitation et de réduction sont suivies. Pour plus d’informations sur le **<NonLinearAds>** élément et ses enfants, consultez IAB.NET/VAST. Notez que la **<TrackingEvents>** élément se trouve dans le** <NonLinearAds> ** élément plutôt que de le **<NonLinear>** élément.

Annonces d’accompagnement sont définies dans un <CompanionAds> élément. Le <CompanionAds> élément peut contenir un ou plusieurs <Companion> éléments. Chaque <Companion> élément décrit une annonce le Guide et peut contenir un <StaticResource>, <IFrameResource>, ou <HTMLResource> qui sont spécifiées de la même manière qu’une publicité non linéaire. Un vaste fichier peut contenir plusieurs annonces le Guide et l’application de lecteur peut choisir la publicité la plus appropriée à afficher. Pour plus d’informations sur VAST, reportez-vous à la section [3.0 grande](http://www.iab.net/media/file/VASTv3.0.pdf).

###<a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Plusieurs fichiers de sélection (VMAP) d’Ad à l’aide d’une vidéo numérique

Un fichier VMAP vous permet de spécifier lorsque des sauts de publicité, combien de temps chaque saut est, combien d’annonces peut être affichés dans un saut et les types de publicités peuvent être affichées pendant une pause. Dans un exemple de fichier VMAP qui définit un saut unique ad suivantes :
    
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
     
Un fichier VMAP commence par une <VMAP> élément qui contient un ou plusieurs <AdBreak> éléments, chaque définition d’un arrêt de publicité. Chaque saut de publicité Spécifie le type de saut, saut identificateur, de décalage de temps. L’attribut breakType Spécifie le type de publicité qui peut être lu pendant la pause : linéaire et non linéaire, ou l’afficher. Afficher le plan de publicités pour des publicités de grande companion. Plus d’un type de publicité peut être spécifié dans une liste séparée par des virgules (sans espace). Le breakID est un identificateur facultatif pour la publicité. La timeOffset indique si la publicité doit être affichée. Il peut être spécifié dans une des manières suivantes :

1. Heure, au format hh : mm : ou.mmm où le fichier est millisecondes. La valeur de cet attribut spécifie l’heure à partir du début de la chronologie de la vidéo au début de la pause publicité.
1. Pourcentage – au format % n, où n est le pourcentage de la chronologie de la vidéo à lire avant la lecture de l’annonce
1. Début/fin – spécifie qu’une publicité doit être affichée avant ou après l’affichage de la vidéo
1. Position : Spécifie l’ordre des sauts de publicité lorsque le minutage de sauts de l’annonce est inconnu, comme dans la diffusion en continu live. L’ordre de chaque saut ad est spécifié dans le format #n où n est un entier à 1 ou supérieur. 1 indique que la publicité doit être lu à la première occasion, 2 signifie la publicité doit être lu à l’occasion du deuxième et ainsi de suite.

Dans l’élément <**AdBreak**>, il peut y avoir un élément de <**ADsource par celle**>. L’élément <**ADsource par celle**> contient les attributs suivants :

1. ID : Spécifie un identificateur pour la source ad
1. allowMultipleAds – une valeur booléenne qui indique si plusieurs annonces peuvent s’afficher pendant la pause publicité
1. followRedirects – une valeur de type Boolean facultative qui spécifie si le lecteur vidéo doit respecter redirige dans une réponse de l’annonce

L’élément <**ADsource par celle**> fournit le lecteur une réponse de publicité en ligne ou d’une référence à une réponse de l’annonce. Il peut contenir un des éléments suivants :

- <VASTAdData>Indique une réponse de grande publicité est incorporée dans le fichier VMAP
- <AdTagURI>un URI qui fait référence à une réponse d’Active Directory à partir d’un autre système
- <CustomAdData>-un arbitraire qui représente une réponse non grande chaîne

Dans cet exemple, une réponse en ligne ad est spécifiée avec un <VASTAdData> élément qui contient une réponse grande publicité. Pour plus d’informations sur les autres éléments, voir [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

L’élément <**AdBreak**> peut également contenir un élément <**TrackingEvents**>. L’élément <**TrackingEvents**> vous permet de suivre le début ou la fin d’un saut d’Active Directory ou si une erreur s’est produite pendant la pause publicité. L’élément <**TrackingEvents**> contienne un ou plusieurs <**suivi**> éléments, dont chacun spécifie un événement de suivi et de suivi URI. Les événements de suivi possibles sont les suivantes :

1. breakStart – assure le suivi au début d’un saut de publicité
1. breakEnd – le suivi de l’achèvement d’un saut de publicité
1. erreur – suit une erreur s’est produite lors de l’arrêt de l’annonce

L’exemple suivant montre un fichier VMAP qui spécifie les événements de suivi

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Pour plus d’informations sur l’élément <**TrackingEvents**> et ses enfants, consultez http://iab.org/VMAP.pdf

###<a name="using-a-media-abstract-sequencing-template-mast-file"></a>À l’aide d’un résumé des médias séquençage du fichier de modèle (mat)

Un fichier de mât vous permet de spécifier des déclencheurs qui définissent quand une annonce s’affiche. Voici un exemple de fichier de mât contenant des déclencheurs pour une annonce de rouleau antérieur, une annonce de rouleau de milieu de gamme et une publicité postérieur à rouleau.

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>

 

Un fichier de mât commence par une **<MAST>** qui contient un élément **<triggers>** élément. Le <triggers> élément contient un ou plusieurs **<trigger>** éléments qui définissent quand une annonce doit être lu. 

Le **<trigger>** élément contient un **<startConditions>** élément qui spécifient quand une annonce doit commencer à jouer. Le **<startConditions>** élément contient un ou plusieurs <condition> éléments. Lors de chaque <condition> a la valeur true, un déclencheur est lancé ou révoqués selon que la <condition> est contenue dans un **< startConditions**> ou **<endConditions>** élément respectivement. Lorsque plusieurs <condition> éléments sont présents, ils sont traités en tant qu’un ou implicite, n’importe quelle condition évaluée est true entraîne le déclencheur à lancer. <condition>les éléments peuvent être imbriqués. Lors de l’enfant <condition> éléments sont prédéfinis, ils sont traités comme un implicite et, toutes les conditions doivent avoir la valeur true pour le déclencheur lancer. Le <condition> élément contient les attributs suivants qui définissent la condition : 

1. **type** : Spécifie le type de condition, événement ou la propriété
1. **nom** : le nom de la propriété ou l’événement à utiliser lors de l’évaluation
1. **valeur** – la valeur qui sera évaluée par rapport à une propriété
1. **opérateur** – l’opération à utiliser lors de l’évaluation : EQ (égalité), NEQ (différent de), GTR (supérieur), GEQ (supérieur ou égal à), LT (inférieur à), LEQ (inférieur ou égal à), MOD (modulo)

**<endConditions>**également contenir <condition> éléments. Lorsqu’une condition a la valeur true le déclencheur est réinitialisé. Le <trigger> élément contient également un <sources> élément qui contient un ou plusieurs <source> éléments. Le <source> éléments définissent l’URI pour la réponse et le type de réponse d’Active Directory. Dans cet exemple, un URI est donné à une grande réponse. 


    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
 

###<a name="using-video-player-ad-interface-definition-vpaid"></a>À l’aide de la définition de l’Interface Ad-lecteur vidéo (VPAID)

VPAID est une API permettant d’unités de publicité exécutable communiquer avec un lecteur vidéo. Cela permet des expériences interactives. L’utilisateur peut interagir avec la publicité et la publicité peut répondre aux actions effectuées par la visionneuse. Par exemple une annonce peut afficher des boutons qui permettent à l’utilisateur d’afficher plus d’informations ou une version plus longue de la publicité. Le lecteur vidéo doit prendre en charge l’API VPAID et la publicité exécutable doit implémenter l’API. Lorsqu’un lecteur demande qu'une publicité à partir d’un serveur Active Directory le serveur peut répondre avec une grande réponse contenant une annonce VPAID.

Un exécutable ad est créé dans le code qui doit être exécutée dans un environnement d’exécution Adobe Flash™ ou de JavaScript qui peut être exécutée dans un navigateur web. Lorsqu’un serveur Active Directory renvoie une grande réponse contenant une publicité VPAID, la valeur de l’apiFramework l’attribut dans le <MediaFile> l’élément doit être « VPAID ». Cet attribut spécifie que le contenu l’annonce est une publicité exécutable VPAID. L’attribut de type doit être défini pour le type MIME du fichier exécutable, tel que « application/x-shockwave-flash » ou « application/x-javascript ». Le montre l’extrait de code XML suivant le <MediaFile> élément à partir d’une grande réponse contenant une annonce exécutable VPAID. 

    
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
 

Une annonce exécutable peut être initialisée à l’aide de la <AdParameters> élément dans le <Linear> ou <NonLinear> éléments en une grande réponse. Pour plus d’informations sur le <AdParameters> élément, reportez-vous à la section [3.0 de grande](http://www.iab.net/media/file/VASTv3.0.pdf). Pour plus d’informations sur l’API VPAID, reportez-vous à la section [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

##<a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Mise en œuvre de Windows Windows Phone 8 le lecteur avec prise en charge d’Active Directory

La plate-forme Microsoft Media : Lecteur Framework pour Windows 8 et Windows Phone 8 contient une collection d’exemples d’applications qui montrent comment implémenter une application de lecteur vidéo à l’aide de l’infrastructure. Vous pouvez télécharger le Framework de lecteur et les exemples de [lecteur Framework pour Windows 8 et Windows Phone 8](https://playerframework.codeplex.com).

Lorsque vous ouvrez la solution Microsoft.PlayerFramework.Xaml.Samples, vous verrez un certain nombre de dossiers dans le projet. Le dossier de publicité contient l’exemple de code pertinent pour la création d’un lecteur vidéo avec prise en charge d’Active Directory. À l’intérieur de la publicité dossier est un nombre de fichiers XAML/cs chaque qui montrent comment insérer des annonces d’une façon différente. La liste suivante décrit chacune :

- AdPodPage.xaml montre comment afficher un bloc d’annonces.
- AdSchedulingPage.xaml montre comment programmer des annonces.
- FreeWheelPage.xaml montre comment utiliser le plug-in FreeWheel pour programmer des annonces.
- MastPage.xaml montre comment planifier des annonces avec un fichier de mat.
- ProgrammaticAdPage.xaml montre comment planifier par programme des publicités dans une vidéo.
- ScheduleClipPage.xaml montre comment planifier une annonce sans un vaste fichier.
- VastLinearCompanionPage.xaml montre comment insérer une liste linéaire et annonces de l’Assistant.
- VastNonLinearPage.xaml montre comment insérer une publicité non linéaire.
- VmapPage.xaml montre comment spécifier des annonces avec un fichier VMAP.

Chacun de ces exemples utilise la classe de MediaPlayer définie par le cadre de lecteur. La plupart des exemples utilisent des plug-ins qui ajoutent la prise en charge de divers formats de réponse d’Active Directory. L’exemple de ProgrammaticAdPage est par programme interagit avec une instance de MediaPlayer.

###<a name="adpodpage-sample"></a>Exemple de AdPodPage

Cet exemple utilise le AdSchedulerPlugin pour définir le moment d’afficher une annonce. Dans cet exemple, une publicité de rouleau de milieu de gamme est planifiée à jouer après 5 secondes. La gousse ad (il s’agit d’un groupe d’annonces à afficher dans l’ordre) est spécifiée dans un vaste fichier retourné à partir d’un serveur Active Directory. L’URI du fichier grande est spécifié dans le <RemoteAdSource> élément.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
    
        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>
    
                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>
    
                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

Pour plus d’informations sur le AdSchedulerPlugin, reportez-vous à la section [publicité dans le cadre du lecteur sur Windows 8 et Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

###<a name="adschedulingpage"></a>AdSchedulingPage

Cet exemple utilise également la AdSchedulerPlugin. Il prévoit trois annonces, une annonce de rouleau avant, une annonce de rouleau de milieu de gamme et une publicité postérieur à rouleau. L’URI à la VAST pour chaque publicité est spécifié dans une <RemoteAdSource> élément.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>
    
                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


###<a name="freewheelpage"></a>FreeWheelPage

Cet exemple utilise le FreeWheelPlugin qui spécifie l’attribut Source qui spécifie un URI qui pointe vers un fichier SmartXML qui spécifie le contenu des annonces, ainsi que les informations de planification.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="mastpage"></a>MastPage

Cet exemple utilise le MastSchedulerPlugin qui vous permet d’utiliser un fichier de mat. L’attribut Source spécifie l’emplacement du fichier mat.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="programmaticadpage"></a>ProgrammaticAdPage

Cet exemple est par programme interagit avec le lecteur multimédia. Le fichier ProgrammaticAdPage.xaml instancie le lecteur multimédia :

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

Le fichier ProgrammaticAdPage.xaml.cs crée un AdHandlerPlugin, ajoute un TimelineMarker pour spécifier quand une annonce doit être affichée et ajoute un gestionnaire pour l’événement MarkerReached qui charge un RemoteAdSource qui spécifie un URI à un vaste fichier, puis lit la publicité.
    
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;
    
            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }
    
            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

###<a name="scheduleclippage"></a>ScheduleClipPage

Cet exemple utilise le AdSchedulerPlugin pour programmer une annonce de rouleau de milieu de gamme en spécifiant un fichier .wmv contenant la publicité.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearcompanionpage"></a>VastLinearCompanionPage

Cet exemple illustre l’utilisation de la AdSchedulerPlugin pour programmer une annonce linéaire de rouleau de milieu de gamme avec une annonce le Guide. Le <RemoteAdSource> élément spécifie l’emplacement du fichier grand.
    
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage

Cet exemple utilise le AdSchedulerPlugin pour planifier un linéaire et une publicité non linéaire. L’emplacement du fichier grande est spécifié avec la <RemoteAdSource> élément.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
                            
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vmappage"></a>VMAPPage

Les exemples utilise le VmapSchedulerPlugin pour planifier des annonces à l’aide d’un fichier VMAP. L’URI vers le fichier VMAP est spécifié dans l’attribut Source de la <VmapSchedulerPlugin> élément.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

##<a name="implementing-an-ios-video-player-with-ad-support"></a>Mise en œuvre d’un lecteur de vidéo avec prise en charge de l’annonce d’iOS


La plate-forme Microsoft Media : Cadre de lecteur pour iOS contient une collection d’exemples d’applications qui montrent comment implémenter une application de lecteur vidéo à l’aide de l’infrastructure. Vous pouvez télécharger le Framework de lecteur et les exemples [d’Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). La page github a un lien vers un Wiki qui contient des informations supplémentaires sur la structure du lecteur et une introduction à l’exemple de lecteur : [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).


###<a name="scheduling-ads-with-vmap"></a>Planifier des publicités avec VMAP

L’exemple suivant montre comment planifier des annonces à l’aide d’un fichier VMAP.

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest
    
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

###<a name="scheduling-ads-with-vast"></a>Planifier des publicités avec VAST

L’exemple suivant montre comment planifier une annonce de grande de liaison tardive.
    
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
         
   L’exemple suivant montre comment planifier une annonce de grande de liaison anticipée.
Planification d’exemple : 4 un début liaison grande ad //Download le VAST le fichier si ( ! [ framework.adResolver downloadManifest : & manifeste withURL : [NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) {[auto-logFrameworkError] ;} else {adLinearTime.startTime = 7 ; adLinearTime.duration = 0 ;
        
        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

L’exemple suivant montre comment insérer une annonce à l’aide d’ébauche Couper modification (RCE)

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

L’exemple suivant montre comment planifier un bloc d’annonces.

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;
    
    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L’exemple suivant montre comment planifier une publicité non collante de rouleau de milieu de gamme. Une publicité non permanent est uniquement lu une fois que toute recherche effectue la visionneuse.
    
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L’exemple suivant montre comment planifier un pense-bête ad de rouleau de milieu de gamme. Une annonce du pense-bête s’affiche chaque fois que le point spécifié sur la chronologie de la vidéo est atteint.

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


L’exemple suivant montre comment planifier une annonce postérieur à rouleau.

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L’exemple suivant montre comment planifier une annonce avant déploiement.
    
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L’exemple suivant montre comment planifier une annonce de superposition de rouleau de milieu de gamme.
    
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
##<a name="see-also"></a>Voir aussi

[Développer des applications de lecteur vidéo](media-services-develop-video-players.md)
