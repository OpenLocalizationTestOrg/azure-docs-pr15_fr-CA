<properties 
    pageTitle="Fonctionnalités du système d’exploitation sur le Service d’application Azure" 
    description="En savoir plus sur les fonctionnalités du système d’exploitation disponibles pour les applications web, les serveurs principaux de l’application mobile et applications d’API sur le Service d’application Azure" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/01/2016" 
    ms.author="cephalin"/>

# <a name="operating-system-functionality-on-azure-app-service"></a>Fonctionnalités du système d’exploitation sur le Service d’application Azure #

Cet article décrit la fonctionnalité de système d’exploitation base commune qui est disponible pour toutes les applications en cours d’exécution sur le [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Cette fonctionnalité inclut le fichier, réseau, accès au Registre et les journaux de diagnostic et des événements. 

<a id="tiers"></a>
## <a name="app-service-plan-tiers"></a>Niveaux de plan de Service d’application

Les applications client s’exécute dans le Service d’application dans un environnement d’hébergement partagé. Les applications déployées dans les couches **libre** et **Shared** exécutent dans le processus de travail sur les machines virtuelles partagés, tandis que les applications déployées dans les couches de la **prime** et **Standard** s’exécutent sur des ordinateurs virtuels dédié spécifiquement pour les applications associées à un seul client.

Service d’application prenant en charge une expérience transparente de mise à l’échelle entre les différents niveaux, la configuration de sécurité appliquée pour les applications de Service d’application reste le même. Cela garantit que les applications ne soudainement se comportent différemment, échouer de façon inattendue, lors de la planification de Service de l’application bascule d’un niveau à un autre.

<a id="developmentframeworks"></a>
## <a name="development-frameworks"></a>Structures de développement

Niveaux de tarification de Service de l’application contrôle la quantité de ressources informatiques (processeur, disque, mémoire et sortie de réseau) disponibles pour les applications. Toutefois, la richesse des fonctionnalités de framework disponible pour les applications reste identiques pour tous les niveaux de mise à l’échelle.

Service d’application prend en charge une variété de structures de développement, y compris ASP.NET, ASP classique, node.js, PHP et les Python - qui s’exécutent en tant qu’extensions dans IIS. Pour simplifier et normaliser la configuration de la sécurité, les applications de Service d’application s’exécutent généralement les différentes infrastructures de développement avec leurs paramètres par défaut. Une approche à la configuration d’applications aurait pu être personnaliser la surface d’API et les fonctionnalités pour chaque framework de développement individuels. Service d’application est plutôt une approche plus générique en activant une référence commune de fonctionnalités de système d’exploitation, quel que soit le framework de développement d’une application.

Les sections suivantes résument les genres générales des fonctionnalités de système d’exploitation disponibles pour les applications de Service d’application.

<a id="FileAccess"></a>
##<a name="file-access"></a>Accès aux fichiers

Il existe différents lecteurs dans le Service d’application, y compris les lecteurs locaux et lecteurs réseau.

<a id="LocalDrives"></a>
### <a name="local-drives"></a>Lecteurs locaux

À la base, application de Service est un service qui s’exécute sur l’infrastructure Azure PaaS (platform en tant que service). En conséquence, les lecteurs locaux qui sont « attachés » à une machine virtuelle sont les mêmes types de lecteur disponibles pour n’importe quel rôle worker exécute dans Azure. Cela inclut un lecteur de système d’exploitation (le lecteur D:\), un lecteur d’application qui contient les fichiers de cspkg Package Azure utilisés exclusivement par le Service d’application (et inaccessibles aux clients) et un lecteur de « utilisateur » (le lecteur C:\), dont la taille varie selon la taille de la machine virtuelle.

<a id="NetworkDrives"></a>
### <a name="network-drives-aka-unc-shares"></a>Lecteurs réseau (également appelés partages UNC)

Un des aspects uniques du Service d’application qui simplifie la maintenance et le déploiement de l’application est que tous les contenus de l’utilisateur sont stockée sur un jeu de partages UNC. Ce modèle correspond parfaitement au modèle commun de stockage de contenu utilisé par le web sur site hébergeant les environnements disposant de plusieurs serveurs avec équilibrage de charge. 

Dans le Service d’application, il n’y a nombre de partages UNC créés dans chaque centre de données. Un pourcentage de la teneur de l’utilisateur pour tous les clients dans chaque centre de données est alloué à chaque partage UNC. En outre, tous le contenu de l’abonnement d’un seul client est toujours placé à la même convention UNC du fichier partagent. 

Remarquez qu’en raison du fonctionnement des services cloud, la machine virtuelle spécifique chargée d’héberger un partage UNC change au fil du temps. Il est garanti que partages UNC seront montées par différentes machines virtuelles comme ils sont mis en haut et le bas pendant le cours normal des opérations de nuage. Pour cette raison, les applications ne devraient jamais apporter codée de manière irréversible des hypothèses que les informations de l’ordinateur dans un chemin d’accès de fichier UNC reste stables dans le temps. Au lieu de cela, ils doivent utiliser la pratique *faux* chemin d’accès absolu **D:\home\site** qui fournit des services d’application. Ce chemin d’accès absolu faux fournit une méthode de portable, indépendant d’application et d’utilisateur pour faire référence à un app. À l’aide de **D:\home\site**, un peut transférer des fichiers partagés à partir d’une application à l’application sans avoir à configurer un nouveau chemin d’accès absolu pour chaque transfert.

<a id="TypesOfFileAccess"></a>
### <a name="types-of-file-access-granted-to-an-app"></a>Types d’accès aux fichiers accordé à une application

Chaque abonnement de client a une structure de répertoire réservé sur un partage UNC spécifique au sein d’un centre de données. Un client peut avoir plusieurs applications créées dans un centre de données spécifique, afin que tous les répertoires appartenant à un abonnement de client unique sont créées sur le même chemin d’UNC à partager. Le partage peut inclure les répertoires tels que ceux pour les versions antérieures de l’application créée par le contrôle de source de contenu, erreur et des journaux de diagnostic. Comme prévu, les répertoires d’application d’un client sont disponibles pour l’accès en lecture et écriture au moment de l’exécution par le code de l’application de l’application.

Sur les lecteurs locaux liés à la machine virtuelle qui s’exécute une application, application de Service se réserve une partie de l’espace sur le lecteur C:\ pour le stockage temporaire locales spécifiques à l’application. Bien qu’une application a un accès complet en lecture/écriture à son propre stockage local temporaire, que le stockage réellement n’est pas destiné à être utilisée directement par le code d’application. À la place, l’intention est pour fournir un stockage de fichier temporaire pour le web et IIS les infrastructures d’application. Service d’application limite également la quantité de stockage local temporaire disponible pour chaque application afin d’empêcher les applications de consommer de quantités excessives de stockage de fichier local.

Deux exemples d’utilisation d’application Service stockage local temporaire sont le répertoire pour les fichiers temporaires ASP.NET et le répertoire IIS dans des fichiers compressés. Le système de compilation d’ASP.NET utilise le répertoire « Temporary ASP.NET Files » comme un emplacement de cache de compilation temporaire. IIS utilise le répertoire « Fichiers compressés temporaires IIS » pour stocker la sortie de la réponse compressée. Ces deux types de fichier utilisation (ainsi que d’autres utilisateurs) sont remappés dans le Service d’application vers le stockage local temporaire de per-app. Ce remappage garantit que la fonctionnalité continue comme prévu.

Chaque application de Service de l’application s’exécute sous une identité de processus de travail de limités unique aléatoire appelée la « identité pool d’applications », décrite ici plus : [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Code d’application utilise cette identité pour base un accès en lecture sur le lecteur du système d’exploitation (le lecteur D:\). Cela signifie que le code d’application peut répertorient les structures de répertoire commun et lire des fichiers communes sur le lecteur du système d’exploitation. Bien que ceci peut sembler quelque peu à grande échelle d’accès, les mêmes répertoires et fichiers sont accessibles lorsque vous configurez un rôle worker dans une Azure service hébergé et lire le contenu du lecteur. 

<a name="multipleinstances"></a>
### <a name="file-access-across-multiple-instances"></a>Accès à un fichier sur plusieurs instances

Le répertoire contienne le contenu de l’application et le code d’application peut écrire dans celui-ci. Si une application s’exécute sur plusieurs instances, le répertoire de base est partagé entre toutes les instances afin que toutes les instances de voir le même répertoire. Ainsi, par exemple, si une application enregistre les fichiers téléchargés dans le répertoire de base, ces fichiers sont immédiatement disponibles pour toutes les instances. 

<a id="NetworkAccess"></a>
## <a name="network-access"></a>Accès au réseau
Code de l’application peut utiliser le protocole TCP/IP et protocoles basé sur UDP sortant pour réseau des connexions vers les points de terminaison accessibles de Internet qui exposent des services externes. Applications peuvent utiliser ces mêmes protocoles pour se connecter à des services Azure & #151 ; par exemple, en établissant des connexions HTTPS à la base de données de SQL.

Il existe également une capacité limitée pour les applications à établir une connexion de bouclage local et avoir une application écoute sur ce socket de bouclage locale. Cette fonctionnalité existe principalement pour activer les applications qui écoutent sur les sockets de bouclage locale dans le cadre de leurs fonctionnalités. Notez que chaque application voit une connexion loopback « privé ». l’application « A » ne peut pas écouter à une prise de bouclage locale établie par l’application « B ».

Les canaux nommés sont également pris en charge comme un mécanisme de communication interprocessus (IPC) entre les différents processus qui collectivement exécuter une application. Par exemple, le module IIS FastCGI s’appuie sur des canaux nommés pour coordonner des processus qui s’exécutent les pages PHP.


<a id="Code"></a>
## <a name="code-execution-processes-and-memory"></a>L’exécution de code, de processus et de mémoire
Comme indiqué précédemment, les applications s’exécuter dans un processus à faible privilège à l’aide d’une identité de pool d’applications aléatoire. Code de l’application a accès à l’espace de mémoire associé avec le processus de travail, ainsi que tous les processus enfants qui peuvent être générées par le processus CGI ou d’autres applications. Toutefois, une application ne peut pas accéder à la mémoire ou les données d’une autre application même s’il est sur la même machine virtuelle.

Les applications peuvent exécuter des scripts ou des pages écrites avec les structures de développement web pris en charge. Service d’application ne configurer tous les paramètres web framework aux modes plus restreints. Par exemple, les applications ASP.NET s’exécutant sur le Service de l’application s’exécute en « full » confiance par opposition à un mode de confiance plus restreint. Les infrastructures Web, y compris les ASP classique et ASP.NET, peuvent appeler des composants COM intra-processus (mais pas la sortie des composants COM de processus) comme ADO (ActiveX Data Objects) qui sont inscrits par défaut sur le système d’exploitation Windows.

Les applications peuvent générer et exécuter du code arbitraire. Il est possible pour une application d’opérations telles que la génération dynamique un interpréteur de commandes ou exécuter un script PowerShell. Toutefois, même si le processus et code arbitraire peuvent être générés à partir d’une application, les scripts et programmes exécutables sont toujours limités aux privilèges accordés au pool d’applications parent. Par exemple, une application peut créer un fichier exécutable qui effectue un appel HTTP sortant, mais que le même exécutable ne peut pas tenter de séparer l’adresse IP d’un ordinateur virtuel à partir de sa carte réseau. Un appel réseau sortant est autorisé au code de droits limités, mais essayez de reconfigurer les paramètres réseau sur un ordinateur virtuel requiert des privilèges d’administrateur.


<a id="Diagnostics"></a>
## <a name="diagnostics-logs-and-events"></a>Les événements et les journaux de diagnostic
Informations du journal sont un autre ensemble de données qui tentent d’accéder à certaines applications. Les types d’informations de journal disponibles au code exécuté dans le Service d’application inclut un Diagnostics et consigner les informations générées par une application qui est également facilement accessible à l’application. 

Par exemple, les journaux W3C HTTP générés par une application active sont disponibles dans un répertoire de journal dans l’emplacement du partage réseau créé pour l’application, ou disponibles dans le stockage blob, si un client a défini la journalisation W3C au stockage. Cette dernière option permet de grandes quantités de journaux à être collectées sans le risque de dépasser les limites de stockage de fichier associés à un partage réseau.

De la même manière, les informations de diagnostic en temps réel à partir d’applications .NET peuvent également être enregistrées à l’aide de l’infrastructure de diagnostics et de suivi de .NET avec des options pour écrire les informations de traçage pour le partage de réseau soit l’application, ou bien vers un emplacement de stockage du blob.

Zones de diagnostic journalisation et de suivi qui ne sont pas disponibles pour les applications sont des événements ETW de Windows et les journaux des événements Windows courants (par exemple système, Application et sécurité journaux des événements). Dans la mesure où les informations de traçage ETW peuvent être visible échelle de l’ordinateur (avec l’ACL de droite), les accès en lecture et écriture à des événements ETW sont bloqués. Les développeurs remarquerez peut-être que les API appelle pour lire et écrire ETW, les événements et les journaux des événements Windows courants semble fonctionner, mais c’est parce que le Service d’application est « émulant » les appels afin qu’elles apparaissent à réussir. En réalité, le code d’application n’a aucun accès aux données de cet événement.

<a id="RegistryAccess"></a>
## <a name="registry-access"></a>Accès au Registre
Les applications ont accès en lecture seule pour la plupart (mais pas tous) du Registre de la machine virtuelle qu’ils exécutent sur. Dans la pratique, cela signifie que les clés de Registre qui permettent l’accès en lecture au groupe utilisateurs locaux sont accessibles par les applications. Une zone du Registre qui n’est actuellement pas pris en charge pour l’accès en lecture ou écriture est la clé HKEY\_active\_ruche de l’utilisateur.

Accès en écriture au Registre est bloqué, y compris l’accès à des clés de Registre par utilisateur. Du point de vue de l’application, un accès en écriture au Registre doit jamais être invoquée dans l’environnement Azure dans la mesure où les applications peuvent (et faire) migrées sur différentes machines virtuelles. Le stockage accessible en écriture uniquement permanent qui peut être dépendait par une application est la structure de répertoire de contenu sur les partages UNC de Service d’application par application. 

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 
