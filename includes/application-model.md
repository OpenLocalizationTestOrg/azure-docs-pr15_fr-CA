# <a name="compute"></a>Calculer

Azure vous permet de déployer et d’analyser votre code d’application en cours d’exécution à l’intérieur d’un centre de données de Microsoft. Lorsque vous créez une application et l’exécuter sur Azure, le code et l’ensemble de la configuration est appelée un Azure service est hébergé. Les services hébergés sont faciles à gérer, mettre à l’échelle verticalement, reconfigurer et mettre à jour avec les nouvelles versions de code de votre application. Cet article se concentre sur l’Azure hébergé modèle d’application de service.<a id="compare" name="compare"></a>

## Table des matières<a id="_GoBack" name="_GoBack"></a>

-   [Avantages du modèle Application Azure][]
-   [Principaux concepts relatifs au Service hébergé][]
-   [Considérations de conception de Service hébergé][]
-   [Conception de votre Application pour échelle][]
-   [Définition de Service hébergé et Configuration][]
-   [Le fichier de définition de Service][]
-   [Le fichier de Configuration de Service][]
-   [Création et déploiement d’un Service hébergé][]
-   [Références][]

## <a id="benefits"> </a>Avantages de modèle d’Application azure

Lorsque vous déployez votre application comme un service hébergé, Azure crée une ou plusieurs machines virtuelles (VM qui contiennent le code de votre application) et démarre les ordinateurs virtuels sur des ordinateurs physiques résidant dans un des centres de données Azure. Comme les demandes des clients vers votre application hébergée entrent le centre de données, un équilibreur de charge répartit ces demandes équitablement sur les ordinateurs virtuels. Pendant que votre application est hébergée dans Azure, il obtient trois avantages clés :

-   **Une haute disponibilité.** La haute disponibilité signifie Qu'azure permet de s’assurer que votre application est en cours d’exécution que possible et qu’il est en mesure de répondre aux demandes des clients. Si votre application se termine (en raison d’une non gérée, par exemple), puis Azure permettra de le détecter et il sera automatiquement redémarrer votre application. Si l’ordinateur de votre application s’exécute sur l’expérience de certains types de défaillance matérielle, puis Azure sera également détecter et automatiquement créer une machine virtuelle sur une autre machine physique travail et exécutez votre code à partir de là. Remarque : Pour que votre application obtenir le contrat de niveau de Service de Microsoft de 99,95 % disponibles, vous devez disposer d’au moins deux des ordinateurs virtuels exécutant votre code d’application. Cela permet à un ordinateur virtuel traiter les demandes de client alors qu’Azure déplace votre code à partir d’un ordinateur virtuel en échec à une VM de nouveau, une bonne.

-   **Évolutivité.** Azure vous permet de facilement et modifier de façon dynamique le nombre d’ordinateurs virtuels exécutant votre code d’application pour gérer la charge réelle placée sur votre application. Cela vous permet d’ajuster votre application à la charge de travail que vos clients sont placer dessus tout en protégeant votre uniquement pour les ordinateurs virtuels que vous avez besoin lorsque vous en avez besoin. Lorsque vous souhaitez modifier le nombre de machines virtuelles, Azure répond quelques minutes, ce qui permet de modifier dynamiquement le nombre de machines virtuelles en cours d’exécution aussi souvent que vous le souhaitez.

-   **Facilité de gestion.** Car Azure est une plateforme en tant que Service (PaaS) offre, il gère l’infrastructure nécessaire pour conserver ces machines en cours d’exécution (matériel, électricité et mise en réseau). Azure gère également la plate-forme, assurer un système d’exploitation à jour avec tous les bons correctifs et mises à jour de sécurité, ainsi que mises à jour des composants tels que le.NET Framework et Internet Information Server. Étant donné que tous les ordinateurs virtuels sont exécutent Windows Server 2008, Azure fournit des fonctionnalités supplémentaires telles que le suivi de diagnostic, prise en charge du Bureau à distance, pare-feu et configuration de magasin de certificats. Toutes ces fonctionnalités sont fournies au n° coût supplémentaire. En fait, lorsque vous exécutez votre application dans Azure, la licence de système d’exploitation (OS) de Windows Server 2008 est incluse. Dans la mesure où tous les ordinateurs virtuels exécutent Windows Server 2008, tout code qui s’exécute sur Windows Server 2008 fonctionne correctement lors de l’exécution dans Azure.

## <a id="concepts"> </a>Hébergé des principaux concepts relatifs au Service

Lorsque votre application est déployée comme un service hébergé dans Azure, il s’exécute une ou plusieurs *rôles.* Un *rôle* fait simplement référence à la configuration et les fichiers de l’application. Vous pouvez définir un ou plusieurs rôles de votre application, chacune avec son propre jeu de fichiers d’application et de configuration. Pour chaque rôle dans votre application, vous pouvez spécifier le nombre d’ordinateurs virtuels ou les *instances de rôles*, pour s’exécuter. La figure ci-dessous affiche deux exemples simples d’une application modelée comme un service hébergé à l’aide de rôles et les instances de rôle.

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>Figure 1 : Un seul rôle avec trois instances (VMs) en cours d’exécution dans un centre de données Azure

![image][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>Figure 2 : Deux rôles, chacun avec deux instances (VMs), en cours d’exécution dans un centre de données Azure

![image][1]

Instances de rôle généralement traitent Internet client entrant le centre de données par le biais de ce que l'on appelle un *point de terminaison d’entrée*. Un seul rôle peut avoir 0 ou plus d’entrée des points de terminaison. Chaque point de terminaison indique un protocole (HTTP, HTTPS ou TCP) et un port. Il est courant de configurer un rôle pour que les deux points de terminaison d’entrée : HTTP à l’écoute sur le port 80 et HTTPS sur le port 443. La figure ci-dessous montre un exemple de deux rôles différents de diriger les demandes des clients vers les différents points de terminaison d’entrée.

![image][2]

Lorsque vous créez un service hébergé dans Azure, il est attribué une adresse IP publique adressable que les clients peuvent utiliser pour y accéder. Lors de la création du service hébergé, vous devez également sélectionner un préfixe de l’URL qui est mappé à l’adresse IP. Ce préfixe doit être unique que vous réservez essentiellement le *préfixe*. cloudapp.net URL afin que personne d’autre ne peut faire. Les clients communiquent avec vos instances de rôles à l’aide de l’URL. En règle générale, ne pas distribuer ou publier le *préfixe*d' Azure. cloudapp.net URL. Au lieu de cela, vous achetez un nom DNS à partir de votre registraire DNS de choix et configurer votre nom DNS pour rediriger les requêtes de client vers l’URL d’Azure. Pour plus d’informations, consultez [configuration d’un nom de domaine personnalisé dans Azure][].

## <a id="considerations"> </a>Hébergé les considérations de conception de Service

Lorsque vous créez une application de s’exécuter dans un environnement de cloud, plusieurs aspects sont à considérer comme la latence, haute disponibilité et d’évolutivité.

Choix de l’emplacement rechercher le code de votre application d’est une considération importante lors de l’exécution d’un service hébergé dans Azure. Il est courant de déployer votre application sur les centres de données les plus proches de vos clients pour réduire la latence et d’obtenir les meilleures performances possibles.
Toutefois, vous pouvez choisir un centre de données proche de votre entreprise ou de vos données si vous avez des problèmes de compétence ou juridiques sur vos données et où il réside. Il existe six centres de données partout dans le monde capable d’héberger votre code d’application. Le tableau ci-dessous indique les emplacements disponibles :

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**Pays/région**

</td>
<td style="width: 200px;">
**Sous-régions**

</td>
</tr>
<tr>
<td>
États-Unis

</td>
<td>
Centre Sud & Centre Nord

</td>
</tr>
<tr>
<td>
Europe

</td>
<td>
Du Nord et Ouest

</td>
</tr>
<tr>
<td>
Asie

</td>
<td>
Sud-est & Moyen-Orient

</td>
</tr>
</tbody>
</table>
Lors de la création d’un service hébergé, vous sélectionnez une sous-région indiquant l’emplacement dans lequel vous souhaitez exécuter votre code.

Pour parvenir à l’évolutivité et la haute disponibilité, il est essentiel que les données de votre application est conservé dans un référentiel central accessible à plusieurs instances de rôle. Pour faciliter cela, Azure offre plusieurs options de stockage telles que les objets BLOB, les tables et de base de données SQL. Veuillez consulter les [Offres de stockage de données dans Azure][] pour plus d’informations sur ces technologies de stockage. La figure ci-dessous illustre la façon dont l’équilibreur de charge à l’intérieur du centre de données Azure distribue les demandes des clients vers les instances de rôles différents qui ont accès au même stockage de données.

![image][3]

En règle générale, vous souhaitez localiser votre code d’application et de vos données dans le même centre de données pour un faible temps de latence (performances) lorsque le code de votre application accède aux données. En outre, vous ne seront pas imputés pour la bande passante lorsque les données sont déplacées au sein du même centre de données.

## <a id="scale"> </a>Conception de votre Application pour échelle

Vous pouvez être amené à prendre une application unique (par exemple, un site web simple) et l’avez hébergé dans Azure. Mais bien souvent, votre application peut être composé de plusieurs rôles qui collaborent. Par exemple, dans la figure ci-dessous, il y a deux instances du rôle de site Web, trois instances du rôle de traitement des commandes et une instance du rôle de générateur de rapports. Ces rôles sont tous travailler ensemble et le code pour tous les peut être regroupé et déployé comme une unité unique à Azure.

![image][4]

La principale raison pour fractionner une application dans des rôles différents à chacun d’eux exécutant sur son propre ensemble d’instances de rôle (c'est-à-dire VMs) est mise à l’échelle de manière indépendante les rôles. Par exemple, pendant les vacances, de nombreux clients peuvent être achète des produits de votre société, vous pouvez donc augmenter le nombre d’instances de rôle qui exécute le rôle de votre site Web ainsi que le nombre d’instances de rôle exécute votre rôle de traitement des commandes. Après les vacances de Noël, vous pouvez obtenir un grand nombre de produits retournés, et vous devez toujours un grand nombre d’instances de site Web, mais moins de traitement des cas. Pendant le reste de l’année, vous devez uniquement quelques sites Web et traitement des instances de. Tout au long de tout cela, vous devrez peut-être qu’une seule instance du Générateur de rapports. La flexibilité de basé sur le rôle des déploiements dans Azure vous permet de facilement adapter votre application pour les besoins de votre entreprise.

Il est fréquent d’avoir le rôle d’instances dans votre service hébergé de communiquent entre eux. Par exemple, le rôle de site Web accepte une commande client mais puis il décharge l’ordre de traitement pour les instances de rôle de traitement des commandes. Le meilleur moyen de transmettre le formulaire de travail un ensemble d’instances de rôle à un autre ensemble d’instances à l’aide de la technologie de files d’attente fournie par Azure, soit le Service de file d’attente ou les files d’attente de Bus de Service. L’utilisation d’une file d’attente est un élément essentiel de l’histoire ici. La file d’attente permet au service hébergé à l’échelle de ses rôles indépendamment ce qui permet d’équilibrer la charge de travail et coût. Si le nombre de messages dans la file d’attente augmente au fil du temps, vous pouvez adapter le nombre d’instances de rôle de traitement des commandes. Si le nombre de messages dans la file d’attente diminue au fil du temps, vous pouvez adapter le numéro d’ordre de traitement des instances de rôle. De cette manière, vous seulement payez pour les instances requises pour gérer la charge de travail réelle.

La file d’attente assure également la fiabilité. Lors de la mise à l’échelle vers le bas le numéro d’ordre de traitement des instances de rôle, Azure décide les instances à terminer. Il peut décider de mettre fin à une instance qui est en cours de traitement d’un message de la file d’attente. Cependant, étant donné que le traitement de message ne se termine pas avec succès, le message devient visible à nouveau à un autre traitement des commandes d’instance de rôle qui il récupère et traite. En raison de la visibilité du message de file d’attente, les messages sont assurés de finalement traités. La file d’attente agit également comme un équilibreur de charge en répartissant les efficacement ses messages vers les instances de tous les rôles qui demandent des messages à partir de celui-ci.

Pour les instances de rôle de site Web, vous pouvez surveiller le trafic entrant sur les et décidez de mettre à l’échelle le nombre d'entre eux vers le haut ou vers le bas ainsi. La file d’attente vous permet de mettre à l’échelle le nombre d’instances de rôle de site Web indépendamment des instances de rôle de traitement des commandes. Cela est très puissante et vous offre une grande souplesse. Bien entendu, si votre application comprend des rôles supplémentaires, vous pouvez ajouter des files d’attente supplémentaires comme le canal d’échange pour communiquer entre eux afin de tirer parti de la même échelle et coût des avantages.

## <a id="defandcfg"> </a>Hébergé de Configuration et définition de Service

Déploiement d’un service hébergé sur Azure nécessite également avoir un fichier de définition de service et un fichier de configuration de service. Ces deux fichiers sont des fichiers XML, et ils permettent de spécifier des options de déploiement pour votre service hébergé de manière déclarative. Le fichier de définition de service décrit tous les rôles qui composent votre service hébergé et comment ils communiquent. Le fichier de configuration de service décrit le nombre d’instances de chaque rôle et de paramètres permet de configurer chaque instance du rôle.

## <a id="def"> </a>Le fichier de définition de Service

Comme mentionné plus haut, la définition du service (CSDEF) le fichier est un fichier XML qui décrit les différents rôles qui composent votre application. Le schéma complet pour le fichier XML se trouve ici : [] de [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx].
Le fichier CSDEF contient un élément WebRole ou WorkerRole pour chaque rôle que vous souhaitez dans votre application. Déploiement d’un rôle comme un rôle web (à l’aide de l’élément WebRole) signifie que le code s’exécute sur une instance de rôle contenant Windows Server 2008 et Internet Information Server (IIS).
Déploiement d’un rôle comme un rôle worker (à l’aide de l’élément WorkerRole) signifie que l’instance de rôle Windows Server 2008 sur celui-ci (IIS ne sera pas installé).

Vous pouvez certainement créer et déployer un rôle worker qui utilise d’autres mécanismes pour écouter les demandes web entrantes (par exemple, votre code peut créer et utiliser un HttpListener .NET). Étant donné que les instances de rôle exécutent tous Windows Server 2008, votre code peut exécuter toutes les opérations qui sont normalement disponibles pour une application s’exécutant sur Windows Server
2008.

Pour chaque rôle, vous indiquez la taille de mémoire virtuelle souhaitée que les instances de ce rôle doivent utiliser. Le tableau ci-dessous présente les différentes tailles de mémoire virtuelle disponibles sur le marché et les attributs de chaque :

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Taille de mémoire virtuelle**

</td>
<td>
**UNITÉ CENTRALE**

</td>
<td>
**MÉMOIRE VIVE**

</td>
<td>
**Disque**

</td>
<td>
**E/s de réseau de pointe**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Très petite**

</td>
<td>
1 x 1.0 GHz

</td>
<td>
768 MO

</td>
<td>
20 GO

</td>
<td>
\~5 Mbits/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Petite taille**

</td>
<td>
1 x 1,6 GHz

</td>
<td>
1,75 GO

</td>
<td>
225 GO

</td>
<td>
\~100 Mbits/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Support**

</td>
<td>
2 x 1,6 GHz

</td>
<td>
3,5 GO

</td>
<td>
490 GO

</td>
<td>
\~200 Mbits/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande taille**

</td>
<td>
4 x 1,6 GHz

</td>
<td>
7 GO

</td>
<td>
1 TO

</td>
<td>
\~400 Mb/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Très grande**

</td>
<td>
8 x 1,6 GHz

</td>
<td>
14 GO

</td>
<td>
2 TO

</td>
<td>
\~800 Mbit/s

</td>
</tr>
</tbody>
</table>
Vous êtes chargé de toutes les heures pour chaque machine virtuelle vous servir d’une instance de rôle et vous êtes également chargé de toutes les données que vos instances de rôles envoient à l’extérieur du centre de données. Vous ne sont pas facturés pour les données d’entrée du centre de données. Pour plus d’informations, reportez-vous à la section [prix Azure] de []. En général, il est conseillé d’utiliser de nombreuses instances de rôle limité au lieu de quelques instances de grande taille afin que votre application est plus résistante aux pannes. Après tout, les instances de rôle moins vous avez, une défaillance de l’un d’eux le plus désastreux votre application globale. En outre, comme mentionné précédemment, vous devez déployer au moins deux instances pour chaque rôle afin d’obtenir l’accord de niveau de service de 99,95 % Microsoft fournit.

Le fichier de définition (CSDEF) de service est également où vous spécifiez de nombreux attributs sur chaque rôle dans votre application. Voici quelques-uns des éléments plus utiles à votre disposition :

-   **Certificats**. Vous utilisez des certificats pour le cryptage des données ou si votre service web prend en charge SSL. Tous les certificats doivent être téléchargées vers Azure. Pour plus d’informations, consultez [Gestion des certificats dans Azure][]. Ce paramètre XML installe téléchargés précédemment les certificats dans le magasin de certificats de l’instance de rôle afin qu’ils soient utilisables par votre code d’application.

-   **Les noms de paramètre de configuration**. Pour les valeurs que vous souhaitez que vos applications à lire lors de l’exécution sur une instance de rôle. La valeur réelle des paramètres de configuration est définie dans le fichier de configuration (CSCFG) de service qui peut être mis à jour à tout moment sans avoir à redéployer votre code. En fait, vous pouvez coder vos applications de manière à détecter les valeurs de configuration modifiée sans subir d’interruption de service.

-   **Entrée des points de terminaison**. Ici, vous spécifiez les HTTP, HTTPS ou TCP points de terminaison (avec ports) que vous souhaitez exposer au monde extérieur par le biais de votre *préfixe*. cloadapp.net URL. Lorsque Azure déploie votre rôle, il va automatiquement configurer le pare-feu sur l’instance de rôle.

-   **Les points de terminaison internes**. Ici, vous spécifiez les HTTP ou TCP points de terminaison que vous souhaitez exposés à d’autres instances de rôle qui sont déployés dans le cadre de votre application. Les points de terminaison internes permettent de toutes les instances de rôle dans votre application pour communiquer entre eux mais ne sont pas accessibles à toutes les instances de rôles qui sont en dehors de votre application.

-   **Modules d’importation**. Ces éventuellement installer des composants utiles sur vos instances de rôles. Composants existent pour la surveillance de diagnostic, Bureau à distance et Azure Connect (qui permet à votre instance de rôle accéder aux ressources de locaux via un canal sécurisé).

-   **Stockage local**. Il alloue un sous-répertoire sur l’instance de rôle de votre application à utiliser. Il est décrit plus en détail dans l’article [d’Offres de stockage de données dans Azure][] .

-   **Tâches de démarrage**. Tâches de démarrage vous permettent d’installer les composants requis sur une instance de rôle qu’il démarre. Les tâches peuvent s’exécuter en tant qu’administrateur si nécessaire.

## <a id="cfg"> </a>Le fichier de Configuration de Service

Le fichier de configuration (CSCFG) de service est un fichier XML qui décrit les paramètres qui peuvent être modifiés sans le redéploiement de votre application. Le schéma complet pour le fichier XML se trouve ici : [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][].
Le fichier CSCFG contient un élément de rôle pour chaque rôle dans votre application. Voici certains des éléments que vous pouvez spécifier dans le fichier CSCFG :

-   **Version du système d’exploitation**. Cet attribut vous permet de sélectionner la version de système d’exploitation (OS) souhaitée utilisée pour toutes les instances de rôle votre code d’application en cours d’exécution. Ce système d’exploitation est connu en tant que *système d’exploitation invité*et chaque nouvelle version inclut les derniers correctifs de sécurité et les mises à jour au moment où que la système d’exploitation invité est libérée. Si vous affectez la valeur de l’attribut osVersion "\*«, puis Azure met automatiquement à jour la système d’exploitation invité sur chacun de vos instances de rôles que de nouvelles versions de système d’exploitation invité deviennent disponibles. Toutefois, vous pouvez vous désinscrire mises à jour automatiques en sélectionnant un version de système d’exploitation d’invité spécifique. Par exemple, en définissant l’attribut osVersion à une valeur de « WA-invité-OS-2,8\_201109-01 » entraîne tous vos instances de rôles obtenir ce qui est décrit dans cette page web : [http://msdn.microsoft.com/library/hh560567.aspx][]. Pour plus d’informations sur les versions de système d’exploitation invité, consultez [Gestion des mises à jour pour le système d’exploitation invités de Azure].

-   **Instances**. La valeur de cet élément indique le nombre d’instances de rôle que vous souhaitez provisionné exécute le code pour un rôle particulier. Dans la mesure où vous pouvez télécharger un nouveau fichier CSCFG vers Azure (sans le redéploiement de votre application), il est simplement simple modifier la valeur de cet élément et télécharger un nouveau fichier CSCFG pour augmenter ou diminuer le nombre d’instances de rôle qui exécute votre code d’application dynamique. Cela vous permet de faire évoluer facilement votre application vers le haut ou vers le bas pour répondre aux charges de travail réel des demandes tout en contrôlant les également combien vous sont facturés pour l’exécution des instances de rôle.

-   **Valeurs de paramètre de configuration**. Cet élément indique des valeurs pour les paramètres (comme défini dans le fichier CSDEF). Votre rôle peut lire ces valeurs en cours d’exécution. Ces valeurs de paramètres de configuration sont généralement utilisés pour les chaînes de connexion de base de données SQL ou stockage Azure, mais ils peuvent être utilisés à des fins que vous le souhaitez.

## <a id="hostedservices"> </a>Création et le déploiement d’un Service hébergé

Création d’un service hébergé requiert que vous accédez d’abord sur le [Portail de gestion Azure] et configurer un service hébergé en spécifiant un préfixe DNS et le centre de données finalement que le code en cours d’exécution. Puis dans votre environnement de développement, vous créez votre fichier de définition (CSDEF) service, générez votre code d’application et de tous les fichiers de package (zip) dans un fichier de package (CSPKG) du service. Vous devez également préparer votre fichier de configuration (CSCFG) du service. Pour déployer votre rôle, vous téléchargez les fichiers CSPKG et CSCFG avec l’API de gestion de Service Azure. Une fois déployé, Azure, sera instances de rôle de disposition dans le centre de données (basées sur les données de configuration), extraire le code de votre application à partir du package, copiez-le dans les instances de rôle et démarrer les instances. Maintenant, votre code est en cours d’exécution.

La figure ci-dessous illustre les fichiers CSPKG et CSCFG que vous créez sur votre ordinateur de développement. Le fichier CSPKG contient le fichier CSDEF et le code de deux rôles. Après avoir téléchargé les fichiers CSPKG et CSCFG avec l’API de gestion de Service Azure, Azure crée des instances de rôle dans le centre de données. Dans cet exemple, le fichier CSCFG a indiqué que Azure doit créer trois instances de rôle \#1 et deux instances de rôle \#2.

![image][5]

Pour plus d’informations sur le déploiement, mise à niveau et la reconfiguration des rôles, consultez l’article de [déploiement et mise à jour des Applications Azure][] .<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Références

-   [Création d’un Service hébergé pour Azure][]

-   [Gestion des Services hébergés dans Azure][]

-   [Migration des Applications vers Azure][]

-   [Configuration d’une Application Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Écrit par Jeffrey Richter (Wintellect)</p>

</div>

  [Avantages du modèle Application Azure]: #benefits
  [Principaux concepts relatifs au Service hébergé]: #concepts
  [Considérations de conception de Service hébergé]: #considerations
  [Conception de votre Application pour échelle]: #scale
  [Définition de Service hébergé et Configuration]: #defandcfg
  [Le fichier de définition de Service]: #def
  [Le fichier de Configuration de Service]: #cfg
  [Création et déploiement d’un Service hébergé]: #hostedservices
  [Références]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuration d’un nom de domaine personnalisé dans Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Offres de stockage de données dans Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
  [Gestion des certificats dans Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.Microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.Microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Gestion des mises à niveau du système d’exploitation invités Azure]: http://msdn.microsoft.com/library/ee924680.aspx
  [Portail de gestion Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Déploiement et mise à jour des Applications Azure]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [Création d’un Service hébergé pour Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Gestion des Services hébergés dans Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migration des Applications vers Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configuration d’une Application Azure]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
