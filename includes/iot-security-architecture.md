# <a name="internet-of-things-security-architecture"></a>Architecture de sécurité d’Internet des objets

Lors de la conception d’un système, il est important de comprendre les menaces potentielles à ce système et ajouter les défenses en conséquence, le système est conçu et architecture. Il est particulièrement important de concevoir le produit à partir du début avec la sécurité à l’esprit, comprendre comment un intrus peut être en mesure de compromettre un système vous permet de rendre les atténuations appropriées qu’étant en place depuis le début. 

## <a name="security-starts-with-a-threat-model"></a>La sécurité commence par un modèle de menace
 
Microsoft a utilisé longtemps des modèles de menace pour ses produits et a publiquement disponible du processus de modélisation des menaces de la société. L’expérience de l’entreprise montre que la modélisation possède des avantages inattendus au-delà de comprendre quelles sont les menaces les plus immédiate concernant. Par exemple, il crée également une avenue dans un débat avec d’autres personnes en dehors de l’équipe de développement, ce qui peut conduire à nouvelles idées et des améliorations dans le produit.
  
L’objectif de la modélisation des menaces est de comprendre comment un intrus peut être en mesure de compromettre un système et vérifiez les atténuations appropriées sont en place. Forces de modélisation de menace la conception de l’équipe à prendre en compte les atténuations que le système est conçu, plutôt qu’un système est déployée. Ce fait est très important, car il est irréalisable, de rattrapage de défenses à une multitude de périphériques dans le champ sujet aux erreurs et laisse les clients exposés.

De nombreuses équipes de développement faire un excellent travail en capturant les exigences fonctionnelles pour le système dont bénéficient les clients. Toutefois, l’identifiant non évidente que quelqu'un peut détourner le système est plus complexe. La modélisation des menaces peut aider les équipes de développement à comprendre ce que peut faire un pirate et pourquoi. La modélisation des menaces est un processus structuré qui crée des décisions de conception une discussion sur la sécurité dans le système, ainsi que les modifications de la conception qui sont effectuées tout au long du processus de sécurité impact. Un modèle de menace est tout simplement un document, cette documentation représente également un moyen idéal pour assurer la continuité de la connaissance, conservation des leçons apprises, et aide nouvelle équipe intégrée rapidement. Enfin, un résultat de modélisation des menaces est de vous permettre de prendre en compte d’autres aspects de la sécurité, telles que les engagements de sécurité que vous souhaitez fournir à vos clients. Ces engagements en conjonction avec la modélisation des menaces seront informent et test de votre solution Internet des objets (IoT) du lecteur.
 

### <a name="when-to-threat-model"></a>Quand le modèle de menace

[La modélisation des menaces](http://www.microsoft.com/security/sdl/adopt/threatmodeling.aspx) d’offre la plus grande valeur si elle est incorporée dans la phase de conception. Lors de la conception, vous avez la plus grande flexibilité pour apporter des modifications afin d’éliminer les menaces. Élimination des menaces en conception est le résultat souhaité. Il est beaucoup plus simple que l’ajout de mesures d’atténuation, leur test et de s’assurer qu’ils restent en cours et, en outre, cette élimination n’est pas toujours possible. Il devient plus difficile à éliminer les menaces comme un produit devient le plus mature et tour finalement nécessitera plus de travail et les compromis beaucoup plus difficiles que la modélisation des menaces très tôt dans le développement.

### <a name="what-to-threat-model"></a>Que se passe-t-il pour le modèle de menace

Vous devez la solution dans son ensemble de modèle de thread et également concentrer vos efforts dans les domaines suivants :

- Fonctionnalités de sécurité et de confidentialité
- Les fonctionnalités dont les défaillances sont concernant la sécurité
- Les fonctionnalités qui touchent une limite de confiance 

### <a name="who-threat-models"></a>Modèles qui la menace

La modélisation des menaces est un processus semblable aux autres.  Il est conseillé de traiter le document du modèle de menace comme tout autre composant de la solution et de le valider. De nombreuses équipes de développement faire un excellent travail en capturant les exigences fonctionnelles pour le système dont bénéficient les clients. Toutefois, l’identifiant non évidente que quelqu'un peut détourner le système est plus complexe. La modélisation des menaces peut aider les équipes de développement à comprendre ce que peut faire un pirate et pourquoi.

### <a name="how-to-threat-model"></a>Comment le modèle de menace

Processus de modélisation des menaces sont composé de quatre étapes ; les étapes sont les suivantes :

- L’application du modèle
- Énumérer les menaces
- Atténuer les menaces
- Valider les atténuations

#### <a name="the-process-steps"></a>Les étapes du processus

Trois règles générales à prendre en compte lors de la création d’un modèle de menace :

1. Créer un diagramme de l’architecture de référence. 
2. Démarrer la largeur d’abord. Obtenir une vue d’ensemble et comprendre le système dans son ensemble, avant de plonger de profondeur.  Cela permet de garantir que vous dirigeons aux bons endroits.
3. Piloter le processus, ne laissez pas le processus de lecteur vous. Si vous détectez un problème lors de la phase de modélisation et que vous souhaitez Explorer, n’hésitez pas !  Ne vous sentez pas besoin de suivre ces étapes aveuglément.  

#### <a name="threats"></a>Menaces

Les quatre principaux éléments d’un modèle de menace sont :

- Processus (services web, les services Win32, * nix démons, etc.. Notez que certaines entités complexes (par exemple, les capteurs et les passerelles de champ) peuvent être abstrait comme un processus lors d’un zoom technique dans ces domaines n’est pas possible.
- Stocke les données (anywhere données sont stockées, par exemple une base de données ou un fichier de configuration)
- Flux de données (où données se déplacent entre les autres éléments de l’application)
- Des entités externes (tout ce qui interagit avec le système, mais n’est pas sous le contrôle de l’application, exemples incluent utilisateurs et satellite des flux)

Tous les éléments dans le schéma de l’architecture sont soumises à différentes menaces ; Nous allons utiliser le mnémonique STRIDE. Lire de [Menace de modélisation à nouveau, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) en savoir plus sur les éléments du modèle STRIDE.

Différents éléments de diagramme d’application sont soumis à certaines menaces STRIDE :

- Les processus sont soumis à STRIDE
- Flux de données sont à TID
- Banques de données sont à TID et, parfois, R, si les banques de données sont des fichiers journaux.
- Les entités externes sont soumis à SRD

## <a name="security-in-iot"></a>Sécurité dans IoT

Équipements spéciaux ont un nombre significatif d’éventuelles zones de surface d’interaction et les modèles d’interaction, qui doivent être considérées comme offrant une structure pour la sécurisation de l’accès numérique à ces périphériques. Le terme « accès numérique » est utilisé ici pour distinguer de toutes les opérations qui sont effectuées par interaction de périphérique direct où la sécurité d’accès est fournie via le contrôle d’accès physique. Par exemple, placer le périphérique dans une salle équipée d’un verrou sur la porte. Lors de l’accès physique ne peut être refusée à l’aide des logiciels et matériels, des mesures puissent être prises pour empêcher l’accès physique à partir de pointe aux interférences de système. 

Nous parcourons les modèles interaction, nous allons étudier « pilotage » et de « données de périphérique » avec le même niveau d’attention. « Pilotage » peut être classé comme toute information fournie à un périphérique par un tiers dans le but de modifier ou d’influencer son comportement vers son état ou de l’état de son environnement. « Données de périphérique » peuvent être classées en tant que toutes les informations qui émet d’un périphérique à une autre partie sur son état et l’état observée de son environnement.
   
Afin d’optimiser les meilleures pratiques de sécurité, il est recommandé qu’une architecture IoT classique être divisée en plusieurs zones/de composant dans le cadre de l’exercice de modélisation des menaces. Ces zones sont décrits en détail dans cette section et incluent :

-   DISPOSITIF,
-   Passerelle de champ,
-   Nuage de passerelles, et
-   Services.

Les zones sont étendues à segmenter une solution ; souvent, chaque zone possède ses propres exigences en matière de données et d’authentification et d’autorisation. Zones peuvent également être utilisé pour les dommages d’isolation et limiter l’impact des zones de niveau de confiance faible dans les zones de confiance plus élevés.

Chaque zone est séparé par une limite de confiance, qui est indiqué sous la ligne rouge en pointillés dans le diagramme ci-dessous. Il représente une transition de données provenant d’une source à un autre. Au cours de cette transition, les données peut être soumis à l’usurpation d’identité, falsification, répudiation, divulgation d’informations, déni de Service et élévation de privilège (le pas).

![Les Zones de sécurité IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Les composants représentés au sein de chaque frontière sont également soumis à STRIDE, permettant une complète 360 aperçu de la solution de modélisation de la menace. Les sections ci-dessous est élaboré sur chacun des composants et problèmes de sécurité spécifiques et des solutions qui doivent être mises en place.

Les sections qui suit aborderons des composants standard que se trouvés généralement dans ces zones.

### <a name="the-device-zone"></a>La Zone périphérique

L’environnement du périphérique est l’espace physique immédiate autour de l’appareil où l’accès et/ou de « réseau local » peer-to-peer des accès numérique pour le périphérique ne sont possible. Un « réseau local » est supposé pour être un réseau distinct et isolé à partir de – mais potentiellement affectés d’un pont à – Internet public, et inclut toute technologie des radio sans fil à courte portée qui permet une communication peer-to-peer de périphériques. C’est *pas* inclure n’importe quelle technologie de virtualisation réseau créer l’illusion d’un tel réseau local et ne sont également pas réseaux opérateur public qui nécessitent deux périphériques de communiquer à travers l’espace de réseau public s’il s’agissait d’entrer une relation de communication d’homologue à homologue.

### <a name="the-field-gateway-zone"></a>La Zone de passerelle de champ

Passerelle de champ est un périphérique/matériel ou des logiciels serveur à vocation généraliste qui agit en tant qu’activateur de communication et, éventuellement, sous la forme d’un système de contrôle de périphérique et d’un concentrateur de traitement des données de périphérique. La zone de passerelle de champ inclut la passerelle champ lui-même et tous les périphériques qui sont reliés. Comme son nom l’indique, passerelles de champ agissent de fonctions de traitement de données dédié à l’extérieur, sont généralement emplacement lié, peuvent être soumis à une intrusion physique et cette procédure limitera redondance fonctionnelle. Tout pour dire qu’une passerelle de champ est en général une chose un peut toucher et saboter lors de savoir quelle est sa fonction. 

Une passerelle de champ est différente d’un routeur de trafic simple dans la mesure où il y a eu un rôle actif dans la gestion de l’accès et du flux d’informations, ce qui signifie qu’il est une application adressé entité et connexion réseau ou une session Terminal Server. Un périphérique NAT ou un pare-feu, en revanche, ne constituent pas des passerelles de champ dans la mesure où ils ne sont pas connexion explicite ou terminaux de session, mais plutôt un itinéraire (ou bloquer) connexions ou sessions effectuées par leur intermédiaire. La passerelle de champ a deux zones distinctes. Une face de périphériques qui sont reliés et représente l’intérieur de la zone, et l’autre est confronté à toutes les parties externes et le bord de la zone.   

### <a name="the-cloud-gateway-zone"></a>La zone de passerelle de nuage

Passerelle de cloud est un système qui permet les communications à distance à partir d’et vers les périphériques ou les passerelles de champ à partir de plusieurs sites au sein de l’espace du réseau public, généralement à un contrôle basé sur le cloud et le système d’analyse de données, une fédération de ces systèmes. Dans certains cas, une passerelle de nuage peut faciliter immédiatement l’accès à des appareils à usage spécifique à partir de terminaux, comme des tablettes ou téléphones. Dans le contexte abordé ici, « nuage » est censée faire référence à un système de traitement de données dédié qui n’est pas lié au même site que le périphérique relié ou de passerelles de champ. Également dans une Zone de nuage, les mesures opérationnelles empêcher tout accès physique ciblée et n’est pas nécessairement exposée à une infrastructure « nuage public ».  

Une passerelle de nuage peut potentiellement être mappée dans une superposition de virtualisation réseau pour isoler la passerelle de nuage et de l’ensemble de ses périphériques connectés ou les passerelles de champ à partir de tout autre trafic réseau. La passerelle de nuage lui-même est ni un système de contrôle de périphérique ni un traitement ou un support de stockage des données de périphérique ; Ces installations d’interface avec la passerelle de nuage. La zone de la passerelle nuage inclut la passerelle du nuage ainsi que toutes les passerelles de champ et les périphériques directement ou indirectement. Le bord de la zone est une zone de surface distincte où toutes les parties externes communiquent par le biais de.

### <a name="the-services-zone"></a>La zone de services

Un « service » est défini pour ce contexte comme n’importe quel composant logiciel ou le module qui est une activité d’interface avec des périphériques via une passerelle champ - ou nuage collecte et analyse des données, ainsi que pour la commande et de contrôle.  Les services sont médiateurs. Ils agissent sous leur identité vers les passerelles et les autres sous-systèmes, stockent et analyser des données, autonome envoyer des commandes aux périphériques basées sur les analyses de données ou des planifications et exposent les informations et le contrôlent des fonctionnalités pour les utilisateurs finaux autorisés.

### <a name="information-devices-vs-special-purpose-devices"></a>Informations-périphériques et appareils à usage spécifique

PC, des téléphones et des tablettes sont principalement des dispositifs d’information interactif. Téléphones et des tablettes sont explicitement optimisées pour optimiser la durée de vie de batterie. Ils préférence désactivent partiellement lors de le n'interaction pas immédiatement avec une personne, ou lorsque vous ne fournit ne pas de services tels que la lecture de musique ou de guidage de leur propriétaire à un emplacement particulier. Du point de vue des systèmes, ces périphériques de technologie d’information sont principalement agissant en tant que proxy vers les personnes. Ils sont « actionneurs de personnes « suggérant des actions et « capteurs de personnes « collecte d’entrées de. 

Appareils à usage spécifique, de capteurs de température simple aux lignes de production fabrique complexes avec des milliers de composants à l’intérieur, sont différentes. Ces périphériques sont beaucoup plus étendues dans le but, et même si elles fournissent une interface utilisateur, ils portent principalement à assurer l’interface avec ou être intégrés dans des actifs dans le monde physique. Ils mesurent et signalent des conditions d’environnement, activer des vannes, servos de contrôle, son alarmes, basculer les lumières et effectuer de nombreuses autres tâches. Ils aident pour effectuer le travail pour lequel un dispositif d’information est trop générique, trop coûteux, trop grande, soit trop fragile. L’objectif de béton dicte immédiatement leur conception technique comme bien le budget disponible monétaire pour leur production et le fonctionnement de la durée de vie prévue. La combinaison de ces deux facteurs clés contraint le disponible calcul de budget de l’énergie, encombrement et donc d’espace de stockage disponible, opérationnelle et des fonctionnalités de sécurité.  

Si quelque chose « est redirigé incorrect » avec les périphériques contrôlables automatisées ou à distance, par exemple, des défauts physiques ou logique de contrôle défauts manipulation et intrusions grossières. Les lots de production peuvent être détruits, bâtiments peuvent être vidés ou gravés vers le bas et personnes peuvent être blessé ou même de matrice. Il s’agit, bien entendu, une classe entièrement différente des dommages qu’une autre personne réclament la limite d’une carte de crédit volées. La barre de sécurité pour les périphériques qui rendent les éléments à déplacer et également pour les données de capteur aboutir à des commandes qui génèrent les éléments à déplacer, doit être supérieure à n’importe quel commerce électronique ou d’un scénario bancaire. 


### <a name="device-control-and-device-data-interactions"></a>Contrôle de périphérique et les interactions des données de périphérique

Équipements spéciaux ont un nombre significatif d’éventuelles zones de surface d’interaction et les modèles d’interaction, qui doivent être considérées comme offrant une structure pour la sécurisation de l’accès numérique à ces périphériques. Le terme « accès numérique » est utilisé ici pour distinguer de toutes les opérations qui sont effectuées par interaction de périphérique direct où la sécurité d’accès est fournie via le contrôle d’accès physique. Par exemple, placer le périphérique dans une salle équipée d’un verrou sur la porte. Lors de l’accès physique ne peut être refusée à l’aide des logiciels et matériels, des mesures puissent être prises pour empêcher l’accès physique à partir de pointe aux interférences de système. 
 
Nous parcourons les modèles interaction, nous allons étudier « pilotage » et de « données de périphérique » avec le même niveau d’attention lors de la modélisation des menaces. « Pilotage » peut être classé comme toute information fournie à un périphérique par un tiers dans le but de modifier ou d’influencer son comportement vers son état ou de l’état de son environnement. « Données de périphérique » peuvent être classées en tant que toutes les informations qui émet d’un périphérique à une autre partie sur son état et l’état observée de son environnement. 

## <a name="threat-modeling-the-azure-iot-reference-architecture"></a>L’architecture de référence Azure IoT de modélisation des menaces

Microsoft utilise l’infrastructure décrite ci-dessus pour pour Azure IoT de modélisation des menaces. Dans la section ci-dessous, nous utilisons donc l’exemple concret d’Architecture de référence IoT Azure pour montrer comment penser IoT de modélisation des menaces et à contrer les menaces identifiées. Dans notre cas, nous avons identifié quatre domaines principaux :

-   Les Sources de données et les périphériques
-   Transport des données,
-   Dispositif et traitement de l’événement, et
-   Présentation

![Utilisez-le pour Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png) 

Le schéma ci-dessous donne une vue simplifiée de l’Architecture de Microsoft IoT à l’aide d’un modèle de diagramme de flux de données qui est utilisé par l’outil de modélisation des menaces de Microsoft :

![Utilisez-le pour IoT Azure à l’aide d’outil de modélisation de menace MS](media/iot-security-architecture/iot-security-architecture-fig3.png)

Il est important de noter que l’architecture sépare les capacités du périphérique et de la passerelle. Cela permet à l’utilisateur de tirer parti des périphériques de passerelle qui sont plus sécurisés : ils sont capables de communiquer avec la passerelle nuage à l’aide de protocoles sécurisés, ce qui nécessite généralement une plus grande charge de traitement qu’un périphérique natif - comme un thermostat - pourrait fournir sur son propre. Dans la zone services Azure, nous partons du principe que la passerelle de nuage est représentée par le service Azure IoT concentrateur.

### <a name="device-and-data-sourcesdata-transport"></a>Données sources de données et dispositif de transport

Cette section étudie l’architecture décrite ci-dessus à travers l’objectif de la modélisation des menaces et donne une vue d’ensemble de la façon dont nous la corrigeons certains des problèmes inhérents. Nous allons nous concentrer sur les éléments fondamentaux d’un modèle de menace :

- Processus (ceux de notre contrôle et les éléments externes)
- Communication (également appelée flux de données)
- Stockage (également appelée banques de données)

#### <a name="processes"></a>Processus

Dans chacune des catégories présentées dans l’architecture IoT d’Azure, nous essayons de limiter un certain nombre de menaces différentes entre les différentes étapes/informations de données existe dans : processus, de communication et de stockage. Ci-dessous, nous donne une vue d’ensemble des causes plus courantes pour la catégorie « processus », suivie d’un aperçu de comment il pourraient être mieux atténués : 

**L’usurpation d’identité (S)**: un attaquant peut extraire la clé cryptographique à partir d’un périphérique, que ce soit au niveau du logiciel ou du matériel, et par la suite le système avec un autre périphérique physique ou virtuel sous l’identité de l’équipement, le matériel de clé d’accès a été prise à partir de. Une bonne illustration est le contrôle à distance qui peuvent activer n’importe quel téléviseur et qui sont des outils de prankster les plus courants.

**Déni de Service (D)**: un périphérique peut être rendu dans l’incapacité de fonctionnement ou de communication par interférence avec des fréquences radio ou des fils de coupe. Par exemple, une caméra de surveillance qui avait sa connexion d’alimentation ou réseau intentionnellement masquée ne signale pas à tous les données.

**Falsification (T)**: un attaquant peut partiellement ou totalement remplacer le logiciel en cours d’exécution sur le périphérique, ce qui pourrait permettre le logiciel remplacé tirer parti de l’identité véritable de l’appareil si le matériel de clé ou les fonctions de chiffrement contenant les principaux documents étaient disponibles pour le programme illicite. Par exemple, un attaquant peut exploiter extrait matériel de clé pour intercepter et supprimer les données de l’appareil sur le chemin de communication et remplace par false qui est authentifié avec le matériel de clé volé.

**Divulgation d’informations (I)**: si le périphérique est en cours d’exécution manipulé de logiciel, ces logiciels manipulé pourrait potentiellement avoir une fuite des données à des personnes non autorisées. Par exemple, un attaquant peut exploiter extrait matériel de clé pour injecter lui-même dans le chemin de communication entre le périphérique et un contrôleur ou champ de passerelle ou une passerelle de nuage permit d’informations.

**Élévation de privilège (E)**: un périphérique qui exécute une fonction spécifique peut être obligé de faire autre chose. Par exemple, une vanne est programmée pour ouvrir la moitié peut être amené à son insue pour ouvrir complètement.

| **Composant** | **Menace** | **Réduction des risques**                                                                                                                                                | **Risques**                                                                                                                                                                                                    | **Mise en œuvre**                                                                                                                                                                                                                                                                                                                                     |
|---------------|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DISPOSITIF        | S          | Affectation d’identité sur le périphérique et le périphérique d’authentification                                                                                                | Remplacement de périphérique ou une partie de l’équipement avec un autre périphérique. Comment savons-nous qu'impliquent à l’appareil approprié ?                                                                                           | Authentifier le périphérique, à l’aide de la sécurité TLS (Transport Layer) ou IPSec. Infrastructure doit prendre en charge à l’aide d’une clé prépartagée (PSK) sur les périphériques qui ne peuvent pas gérer la cryptographie asymétrique complète. Exploiter AD Azure, [OAuth](http://www.rfc-editor.org/in-notes/internet-drafts/draft-ietf-ace-oauth-authz-01.txt)                             |
|               | TRID       | Appliquer par exemple inviolable des mécanismes pour le périphérique en le rendant très difficile à impossible d’extraire les clés et autres matériels de chiffrement à partir du périphérique. | Le risque est que si une personne est falsification du périphérique (toute interférence physique). Comment sommes-nous bien sûr, ce périphérique n’a pas altéré.                                                                                 | L’atténuation plus efficace est une fonctionnalité de module (TPM) de plateforme sécurisée qui permet le stockage de clés dans un circuit spécial sur puce à partir de laquelle les clés ne peut pas être lu, mais seulement peuvent être utilisés pour les opérations qui utilisent la clé mais ne divulguent jamais la clé de chiffrement. Cryptage de la mémoire du périphérique. Gestion des clés pour le périphérique. Signature du code. |
|               | E          | Ayant le contrôle d’accès du périphérique. Schéma d’autorisation.                                                                                                    | Si le périphérique permet les actions spécifiques à effectuer en fonction des commandes à partir d’une source externe ou des capteurs de même compromises, il autorise l’attaque effectuer des opérations non accessible. | Ayant le schéma d’autorisation pour le périphérique                                                                                                                                                                                                                                                                                                             |
| Passerelle de champ | S          | L’authentification de la passerelle de champ à la passerelle de nuage (cert en fonction, PSK, demande de remboursement,...)                                                                           | Si une personne peut usurper l’identité de passerelle de champ, puis qu’il peut être affichée comme n’importe quel périphérique.                                                                                                                               | RSA/PSK, IPSe, TLS [RFC 4279](https://tools.ietf.org/html/rfc4279). Les mêmes principales préoccupations de stockage et d’attestation des périphériques en général – meilleur des cas est utiliser TPM. Extension de 6LowPAN pour IPSec prendre en charge des réseaux de capteur sans fil (WSN).                                                                                                              |
|               | TRID       | Protéger la passerelle champ contre la falsification (TPM) ?                                                                                                            | Usurpation qui trompent le raisonnement de passerelle de nuage qu'il parle de passerelle de champ risque de divulgation d’informations et la falsification des données                                                             | De mémoire cryptage, le module de plateforme sécurisée, l’authentification.                                                                                                                                                                                                                                                                                                              |
|               | E          | Mécanisme de contrôle d’accès pour le champ passerelle                                                                                                                    |                                                                                                                                                                                                             |                                                                                                                                                                                                                                                                                                                                                        |

Voici quelques exemples de menaces de cette catégorie :

L’usurpation d’identité : Un attaquant peut extraire clé cryptographique à partir d’un périphérique, que ce soit au logiciel ou au niveau du matériel et par la suite le système avec un autre périphérique physique ou virtuel sous l’identité de l’équipement, le matériel de clé a été pris à partir de l’accès.

**Déni de Service**: un périphérique peut être rendu dans l’incapacité de fonctionnement ou de communication par interférence avec des fréquences radio ou des fils de coupe. Par exemple, une caméra de surveillance qui avait sa connexion d’alimentation ou réseau intentionnellement masquée ne signale pas à tous les données.

**Falsification**: un attaquant peut partiellement ou totalement remplacer le logiciel en cours d’exécution sur le périphérique, ce qui pourrait permettre le logiciel remplacé tirer parti de l’identité véritable de l’appareil si le matériel de clé ou les fonctions de chiffrement contenant les principaux documents étaient disponibles pour le programme illicite.

**Falsification**: une caméra de surveillance qui affiche une image du spectre visible d’un couloir vide peut viser à une photographie d’un couloir de ce type. Un détecteur de fumée ou d’incendie peut être reporting détenteur d’un briquet qu’il contient. Dans les deux cas, le périphérique peut être techniquement entièrement digne de confiance envers le système, mais il signale les informations à manipuler.

**Falsification**: un attaquant peut exploiter les extrait matériel de clé pour intercepter et supprimer les données de l’appareil sur le chemin de communication et remplace par false qui est authentifié avec le matériel de clé volé.

**Falsification**: un attaquant peut partiellement ou complètement remplacer le logiciel en cours d’exécution sur le périphérique, ce qui pourrait permettre le logiciel remplacé tirer parti de l’identité véritable de l’appareil si le matériel de clé ou les fonctions de chiffrement contenant les principaux documents étaient disponibles pour le programme illicite.
   
**Divulgation d’informations**: si le périphérique est en cours d’exécution manipulé de logiciel, ces logiciels manipulé pourrait potentiellement avoir une fuite des données à des personnes non autorisées.

**Divulgation d’informations**: un attaquant peut exploiter les extrait matériel de clé pour injecter lui-même dans le chemin de communication entre la passerelle de champ et un contrôleur ou un périphérique ou nuage permit informations.

**Déni de Service**: le périphérique pouvant être désactivé ou activé dans un mode où la communication n’est pas possible (ce qui est intentionnel de nombreuses machines industrielles).

**Falsification**: le périphérique peut être reconfiguré pour fonctionner dans un état inconnu (en dehors des paramètres d’étalonnage connus), le système de contrôle et de fournir ainsi des données qui peuvent être mal interprétées

**Une élévation de privilèges**: un périphérique qui exécute une fonction spécifique peut être obligé de faire autre chose. Par exemple, une vanne est programmée pour ouvrir la moitié peut être amené à son insue pour ouvrir complètement.

**Déni de Service**: le périphérique peut être transformé en un état dans lequel la communication n’est pas possible.

**Falsification**: le périphérique peut être reconfiguré pour fonctionner dans un état inconnu (en dehors des paramètres d’étalonnage connus), le système de contrôle et de fournir ainsi des données qui peuvent être mal interprétées.
 
**L’usurpation d’identité/falsification/reniement**: si non sécurisés (qui est rarement le cas avec les télécommandes consommateur) un attaquant peut manipuler l’état d’un périphérique anonymement. Une bonne illustration est le contrôle à distance qui peuvent activer n’importe quel téléviseur et qui sont des outils de prankster les plus courants.

#### <a name="communication"></a>Communication

Menaces autour de chemin de communication entre les périphériques, les périphériques et passerelles de champ et passerelle de périphérique et le nuage. Le tableau ci-dessous présente quelques conseils autour de sockets ouverts sur le périphériques/le réseau privé virtuel :

| **Composant**               | **Menace** | **Réduction des risques**                                      | **Risques**                                                                                                      | **Mise en œuvre**                                                                                                                                                                                                                                                                                                                                                               |
|-----------------------------|------------|-----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DISPOSITIF IoT concentrateur              | TID        | (D) TLS (PSK/RSA) pour crypter le trafic             | Écoute clandestine ou entraver la communication entre le périphérique et de la passerelle                             | Sécurité au niveau du protocole. Avec les protocoles personnalisés, nous devons comprendre comment les protéger. Dans la plupart des cas, la communication s’effectue à partir du périphérique sur le concentrateur IoT (le périphérique établit la connexion).                                                                                                                                                                 |
| Périphérique               | TID        | (D) TLS (PSK/RSA) pour crypter le trafic.            | Lecture des données en transit entre des périphériques. Falsification de données. La surcharge de l’appareil avec nouvelles connexions | Sécurité au niveau du protocole (MQTT/AMQP/HTTP/CoAP. Avec les protocoles personnalisés, nous devons comprendre comment les protéger. L’atténuation de la menace de déni de service consiste aux périphériques via une passerelle de champ ou de nuage d’homologues et demandez-leur d’agir uniquement en tant que clients sur le réseau. L’homologation peut entraîner une connexion directe entre les homologues après avoir été demandée par la passerelle |
| Dispositif d’entité externe      | TID        | Couplage fort de l’entité externe sur le périphérique | Écoute clandestine de la connexion au périphérique. Interférence de la communication avec le périphérique                     | Appariement de l’entité externe au CNC/Bluetooth LE périphérique en toute sécurité. Contrôle le panneau opérationnel de l’unité (physique)                                                                                                                                                                                                                                                  |
| Champ passerelle passerelle de nuage | TID        | TLS (PSK/RSA) pour crypter le trafic.               | Écoute clandestine ou entraver la communication entre le périphérique et de la passerelle                             | Sécurité au niveau du protocole (MQTT/AMQP/HTTP/CoAP). Avec les protocoles personnalisés, nous devons comprendre comment les protéger.                                                                                                                                                                                                                                                       |
| Passerelle de nuage de périphérique        | TID        | TLS (PSK/RSA) pour crypter le trafic.               | Écoute clandestine ou entraver la communication entre le périphérique et de la passerelle                             | Sécurité au niveau du protocole (MQTT/AMQP/HTTP/CoAP). Avec les protocoles personnalisés, nous devons comprendre comment les protéger.                                                                                                                                                                                                                                                       |

Voici quelques exemples de menaces de cette catégorie :

**Déni de Service**: périphériques contraintes sont généralement sous menaces de déni de service lorsqu’ils activement d’écouter les connexions entrantes ou non sollicité sur un réseau, car un attaquant peut ouvrir de connexions en parallèle et pas leur servent ou leur servent très lentement, ou le périphérique peut être envahi par le trafic non sollicité. Dans les deux cas, le périphérique peut effectivement être rendu inutilisable sur le réseau.

**L’usurpation d’identité, la divulgation d’informations**: contrainte et les dispositifs spéciaux ont souvent des fonctions de sécurité d’un-pour-tous comme mot de passe ou de protection de code confidentiel ou ils reposent entièrement sur l’approbation du réseau, c'est-à-dire leur accorde l’accès à des informations lorsqu’un périphérique est sur le même réseau, et ce réseau est souvent uniquement protégé par une clé partagée. Cela signifie que lorsque le secret partagé sur le réseau ou de périphérique est indiqué, il est possible de contrôler le périphérique ou observer les données provenant du périphérique.  

**L’usurpation d’identité**: un intrus peut intercepter ou partiellement substituer la diffusion et usurper l’identité de l’expéditeur (attaque de l’intercepteur)

**Falsification**: un intrus peut intercepter ou partiellement substituer la diffusion et envoyer de fausses informations 

**Divulgation d’informations :** un intrus peut espionner une diffusion et obtenir des informations sans autorisation **par déni de Service :** attaquant peut le signal de diffusion de bourrage papier et refuser la distribution d’informations

#### <a name="storage"></a>Stockage

Passerelle de chaque périphérique et le champ a une forme quelconque de stockage (temporary pour queuing les données, le stockage d’images du système d’exploitation).

| **Composant**                            | **Menace** | **Réduction des risques**                       | **Risques**                                                                                                                                                                                                                                                                                                                | **Mise en œuvre**                                                                                                                                                     |
|------------------------------------------|------------|--------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stockage de l’appareil                           | TRID       | Cryptage de stockage, les journaux de signature | Lecture de données à partir du stockage (données personnelles), falsification des données de télémétrie. Falsification de la file d’attente ou mis en cache de données de contrôle de commande. Falsification des packages de mise à jour de microprogramme ou de configuration lors de la mise en cache ou la file d’attente localement peut conduire à la compromission des composants du système d’exploitation et/ou le système                                         | Cryptage, code d’authentification de message (MAC) ou la signature numérique. Où le contrôle d’accès possible, renforcé par le biais de l’accès aux ressources contrôle les listes ou les autorisations. |
| Image de système d’exploitation périphérique                          | TRID       |                                      | Falsification de système d’exploitation / de remplacer les composants du système d’exploitation                                                                                                                                                                                                                                                                         | Partition de système d’exploitation en lecture seule, signés image de système d’exploitation, cryptage                                                                                                                    |
| Stockage de champ passerelle (queuing les données) | TRID       | Cryptage de stockage, les journaux de signature | Lecture de données à partir du stockage (données personnelles), falsification de données télémétriques, falsification de la file d’attente ou mis en cache de données de contrôle de commande. Falsification des packages de mise à jour de microprogramme ou de configuration (destinés à périphériques ou une passerelle de champ) lors de la mise en cache ou la file d’attente localement peut conduire à la compromission des composants du système d’exploitation et/ou le système | BitLocker                                                                                                                                                              |
| Image de l’OS de passerelle de champ                   | TRID       |                                      | Falsification de système d’exploitation / de remplacer les composants du système d’exploitation                                                                                                                                                                                                                                                                          | Partition de système d’exploitation en lecture seule, signés image de système d’exploitation, cryptage                                                                                                                    |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Événements et dispositif de traitement/nuage zone de passerelle

Une passerelle de cloud est un système qui permet les communications à distance à partir d’et vers les périphériques ou les passerelles de champ à partir de plusieurs sites au sein de l’espace du réseau public, généralement à un contrôle basé sur le cloud et le système d’analyse de données, une fédération de ces systèmes. Dans certains cas, une passerelle de nuage peut faciliter immédiatement l’accès à des appareils à usage spécifique à partir de terminaux, comme des tablettes ou téléphones. Dans le contexte abordé ici, « nuage » est conçu pour faire référence à un système de traitement de données dédié qui n’est pas lié au même site que le périphérique relié ou de passerelles de champ, et où les mesures opérationnelles empêchent ciblé accès physique, mais n’est pas nécessairement une infrastructure « nuage public ».  Une passerelle de nuage peut potentiellement être mappée dans une superposition de virtualisation réseau pour isoler la passerelle de nuage et de l’ensemble de ses périphériques connectés ou les passerelles de champ à partir de tout autre trafic réseau. La passerelle de nuage lui-même est ni un système de contrôle de périphérique ni un traitement ou un support de stockage des données de périphérique ; Ces installations d’interface avec la passerelle de nuage. La zone de la passerelle nuage inclut la passerelle du nuage ainsi que toutes les passerelles de champ et les périphériques directement ou indirectement.

Passerelle de nuage est essentiellement personnalisé intégré du logiciel s’exécutant en tant que service, avec des points de terminaison exposés à laquelle se connectent les périphériques et la passerelle du champ. En tant que tel, il doit être conçu avec la sécurité à l’esprit. Suivez le processus [SDL](http://www.microsoft.com/sdl) pour la conception et la création de ce service. 

#### <a name="services-zone"></a>Zone de services

Un système de contrôle (ou un contrôleur) est une solution logicielle qui sert d’interface avec un périphérique, ou une passerelle de champ ou une passerelle de nuage afin de contrôler un ou plusieurs périphériques et/ou pour collecter, stocker ou analyser les données du périphérique de la présentation, ou à des fins de contrôle suivantes. Systèmes de contrôle sont les seules entités dans le cadre de cette discussion qui peut immédiatement facilitent l’interaction avec des personnes. L’exception sont les surfaces intermédiaires contrôle physiques sur les périphériques, comme un commutateur qui permet de désactiver le périphérique ou modifier d’autres propriétés, et pour lesquelles n’existe pas d’équivalent fonctionnel accessibles numériquement. 

Surfaces de contrôle physiques intermédiaires sont celles où toute sorte de régissant la logique contraint la fonction de la surface de contrôle physique tel qu’une fonction équivalente peut être lancée à distance ou des conflits d’entrée avec l’entrée à distance peuvent être évités – ces surfaces de contrôle intermediated sont conceptuellement reliés à un système de commande locale qui exploite la même fonctionnalité sous-jacente comme tout autre système de contrôle à distance du périphérique peut être connecté à en parallèle. Principales menaces pour le cloud computing peut être lu dans la page de [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/research/top-threats/) .


## <a name="additional-resources"></a>Ressources supplémentaires

Les articles suivants pour plus d’informations, reportez-vous à la rubrique :

- [Outil de modélisation des menaces SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
- [Architecture de référence Microsoft Azure IoT](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
 
