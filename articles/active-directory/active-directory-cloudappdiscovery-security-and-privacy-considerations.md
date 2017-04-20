<properties
    pageTitle="Considérations relatives à la confidentialité et sécurité de la découverte de l’application en nuage | Microsoft Azure"
    description="Cette rubrique décrit les considérations de sécurité et de confidentialité liées à la découverte des applications de Cloud."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Considérations relatives à la confidentialité et sécurité de la découverte de l’application en nuage

Microsoft s’engage à protéger votre confidentialité et à la sécurisation de vos données, tout en proposant des logiciels et des services qui vous aident à gérer la sécurité de votre organisation. <br>
Nous reconnaissons que lorsque vous confier vos données à d’autres personnes, qu’approbation nécessite des investissements ingénierie de sécurité rigoureux et l’expertise pour la restaurer.
Microsoft se conforme à la conformité et les consignes de sécurité à partir de pratiques de cycle de vie de développement de logiciels sécurisés à l’exploitation d’un service. <br>
Sécurisation et de protection des données sont une priorité de Microsoft.

Cette rubrique explique comment les données sont collectées, traitées et sécurisées au sein de la recherche d’application Azure Active Directory nuage




##<a name="overview"></a>Vue d’ensemble

Découverte d’application cloud est une fonctionnalité de publicité Azure et il est hébergée dans Microsoft Azure. <br>
L’agent de point de terminaison de découverte d’application Cloud est utilisé pour collecter des données de découverte d’application à partir d’ordinateurs d’informatiques gérés. <br>
Les données collectées sont envoyées en toute sécurité via un canal crypté pour le service de découverte d’application Azure AD Cloud. <br>
Les données de découverte d’application Cloud pour une organisation, puis seront visibles dans le portail Azure. <br>


<center>![Fonctionnement de la découverte d’application Cloud](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center> <br>


Les sections suivantes, suivre le flux des informations et décrivent comment il est sécurisé lorsqu’il se déplace à partir de votre organisation pour le service de découverte d’application Cloud et enfin sur le portail de découverte d’application Cloud.



## <a name="collecting-data-from-your-organization"></a>Collecte de données de votre organisation.

Pour utiliser la fonctionnalité de découverte du nuage application Azure d’Active Directory pour obtenir des informations sur les applications utilisées par les employés de votre organisation, vous devez tout d’abord déployer l’agent de point de terminaison de découverte d’application Azure AD nuage aux ordinateurs de votre organisation.

Administrateurs du locataire Azure Active Directory (ou son délégué) peuvent télécharger le package d’installation de l’agent à partir du portail Azure. L’agent peut être manuellement installé ou installé sur plusieurs ordinateurs de l’organisation à l’aide de SCCM ou la stratégie de groupe.

Pour plus d’informations sur les options de déploiement, consultez le [Guide de déploiement de stratégie de groupe nuage App découverte](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).
<br>

### <a name="data-collected-by-the-agent"></a>Données collectées par l’agent

Les informations présentées dans la liste ci-dessous sont collectées par l’agent lorsqu’une connexion est établie à une application Web. Les informations sont collectées uniquement pour les applications que l’administrateur a configuré pour la découverte. <br>
Vous pouvez modifier la liste des applications de cloud qui surveille de l’agent par le biais de la lame de découverte d’application Cloud dans le Microsoft [Azure portal](https://portal.azure.com/), sous **paramètres**->**La collecte des données**->**liste de Collection de l’application**. Pour plus d’informations, consultez [Mise en route avec nuage App découverte](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
<br>
**Catégorie d’information**: informations utilisateur <br>
**Description**: <br>
Le nom d’utilisateur Windows du processus qui a fait une demande à l’application Web de cible (par exemple : domaine\nom d’utilisateur), ainsi que l’identificateur de sécurité Windows (SID) de l’utilisateur.


**Catégorie d’information**: traitement des informations <br>
**Description**: <br>
Le nom du processus qui a fait la demande à l’application Web de cible (par exemple : « iexplore.exe »)

**Catégorie d’information**: informations sur l’ordinateur <br>
**Description**: <br>
Le nom NetBIOS d’ordinateur sur lequel l’agent est installé.

**Catégorie d’information**: informations de trafic d’application <br>
**Description**: <br>

Les informations de connexion suivantes :

- La source (ordinateur local) et les adresses IP de destination et les numéros de port

- L’adresse IP publique de l’organisation par le biais duquel la demande est transmis.

- L’heure de la demande

- Le volume de trafic envoyé et reçu

- La version IP (4 ou 6)

- Pour les connexions TLS uniquement : le nom d’hôte cible à partir de l’extension de l’Indication de nom de serveur ou le certificat de serveur.

Les informations HTTP suivantes :

- Méthode (GET, POST, etc.).

- Protocole (HTTP/1.1, etc.).

- Chaîne d’agent utilisateur

- Nom d’hôte

- Cible URI (à l’exclusion des chaîne de requête)

- Informations de type de contenu

- Informations d’URL de point d’accès (à l’exclusion de la chaîne de requête)



> [AZURE.NOTE] Les informations HTTP ci-dessus sont collectées pour toutes les connexions non cryptées.
Pour les connexions TLS, cette information est capturée uniquement lorsque le paramètre « Inspection approfondie » est activé dans le portail. Le paramètre est 'ON' par défaut.
Pour plus d’informations, voir ci-dessous et [Mise en route avec nuage App découverte](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)


En plus des données que l’agent collecte sur l’activité du réseau, il collecte également des informations anonymes sur le logiciel et configuration matérielle, les rapports d’erreurs et les informations sur l’utilisation de l’agent.

<br><br>
### <a name="how-the-agent-works"></a>Fonctionne de l’agent

Installation de l’agent comprend deux composants :

- Un composant en mode utilisateur

- Un composant de pilote en mode noyau (pilote plateforme de filtrage Windows)



Lorsque l’agent est installé pour la première fois il stocke un certificat approuvé spécifique à l’ordinateur sur l’ordinateur qu’il utilise ensuite pour établir une connexion sécurisée avec le service de découverte d’application Cloud. <br>
L’agent récupère périodiquement les configuration de la stratégie à partir du service de découverte d’application Cloud via cette connexion sécurisée. <br>
La stratégie consacrée des informations sur les applications en nuage à surveiller et si la mise à jour automatique doit être activée, entre autres choses.

Comme le trafic Web est envoyé et reçu sur l’ordinateur à partir d’Internet Explorer et Chrome, l’agent de découverte d’application Cloud analyse le trafic et extrait les métadonnées pertinentes (voir la section de **données collectées par l’agent** ci-dessus). <br>
Toutes les minutes, l’agent télécharge les métadonnées recueillies au service de découverte d’application Cloud via un canal crypté.

Le composant de pilote intercepte le trafic crypté et introduit dans le flux de données chiffré. Plus de détails dans la section des **données de modèle Intercepting à partir des connexions chiffrées (inspection approfondie)** ci-dessous.


### <a name="respecting-user-privacy"></a>En respectant la confidentialité de l’utilisateur

Notre objectif est de fournir aux administrateurs les outils nécessaires pour régler l’équilibre entre optique détaillée en application l’utilisation et utilisateur la confidentialité en fonction de leur organisation. À cette fin, nous fournissons les boutons suivants dans la page Paramètres du portail :

- **La collecte des données**: les administrateurs peuvent choisir de spécifier les applications ou les catégories d’application ils souhaitent obtenir des données de découverte sur.

- **Inspection approfondie**: les administrateurs peuvent choisir pour spécifier si l’agent recueille le trafic HTTP pour les connexions SSL/TLS (également appelé **« Inspection approfondie »**). Plus d’informations sur cette option dans la section suivante.

- **Options de consentement**: les administrateurs peuvent utiliser le portail de découverte d’application Cloud choisir d’avertir les utilisateurs de la collecte de données par l’agent, et si nécessaire l’utilisateur consentement avant le démarrage de l’agent collecte des données de l’utilisateur.

L’agent de point de terminaison de nuage App découverte collecte uniquement les informations décrites dans la section de **données collectées par l’agent** ci-dessus.


### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Intercepter les données à partir des connexions chiffrées (inspection approfondie)
Comme mentionné précédemment, les administrateurs peuvent configurer l’agent pour analyser les données à partir des connexions chiffrées (« inspection approfondie »). TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) est un des protocoles plus courants en cours d’utilisation sur Internet aujourd'hui. Grâce au cryptage des communications avec TLS, un client peut établir un canal de communication sécurisé et privé sur un serveur web ; TLS offre la protection essentielle pour passer des informations d’authentification et empêcher la divulgation d’informations sensibles.

Alors que le canal crypté sécurisé bout à bout fourni par TLS permet d’importantes de la sécurité et de protection de la confidentialité, le protocole est souvent utilisés abusivement à des fins malveillantes ou nuisibles. Bien ainsi, en fait, que TLS est souvent appelé « protocole de contournement de pare-feu universel ». La racine du problème est que la plupart des pare-feu ne peuvent pas inspecter les communications TLS car les données de la couche application sont cryptées avec SSL. Sachant cela, les attaquants exploitent fréquemment TLS pour fournir des charges actives malveillantes à un utilisateur sûr que même les pare-feu de couche d’application plus intelligent sont complètement invisible sur TLS et doivent simplement relayer les communications TLS entre les hôtes. Les utilisateurs finaux exploiter fréquemment TLS pour contourner les contrôles d’accès appliquées par leur pare-feu d’entreprise et les serveurs proxy de l’utiliser pour vous connecter à des serveurs proxy publics et pour le tunneling de protocoles non-TLS à travers le pare-feu peut être bloqué par la stratégie.

Inspection approfondie permet à l’agent de découverte d’application Cloud agir comme un approuvé man-in-the-middle. Lorsqu’un client est demandé pour accéder à une ressource protégée de HTTPS, le pilote de l’Agent de point de terminaison intercepte la connexion et établit qu'une nouvelle connexion au serveur de destination à récupère son certificat SSL au nom du client. L’agent vérifie que le certificat peut être approuvé (en vérifiant qu’il n’était pas révoqué et effectuer d’autres vérifications de certificat), et si ces passe, l’Agent de point de terminaison puis copie les informations du certificat du serveur et crée son propre certificat de serveur--connu comme certificat d’interception--à l’aide de ces informations. Le certificat d’interception est signé à la volée par l’agent de point de terminaison avec un certificat racine, qui est installé dans le magasin de certificats de confiance Windows. Ce certificat racine auto-signé est marqué comme non exportable et est ACL a dû aux administrateurs. Elle est destinée à ne jamais laisser l’ordinateur sur lequel il a été créé. Lorsque l’application cliente de l’utilisateur final reçoit le certificat d’interception, il sera l’approbation, car il peut valider avec succès de la chaîne de certificats jusqu’au certificat racine. Ce processus est transparent à partir de point de vue d’un utilisateur avec quelques inconvénients, comme décrit ci-dessous.

En activant l’inspection approfondie, l’Agent de point de terminaison de nuage App découverte décrypter et inspecter les communications TLS cryptée, autorisant le service réduire le bruit et fournir des conseils sur l’utilisation des applications de cloud crypté.

#### <a name="a-word-of-caution"></a>Un mot de prudence
Avant d’activer l’inspection approfondie, il est fortement recommandé de vous communiquer vos intentions à votre service juridique et les départements RH et obtenir leur consentement. Inspection des communications cryptées privée de l’utilisateur final peut être pas un sujet sensible, pour des raisons évidentes. Avant une production sortie d’inspection approfondie, assurez-vous que la sécurité de votre entreprise et les stratégies du bon usage ont été mis à jour pour indiquer que la communication cryptée doit être examinée. Notification de l’utilisateur et l’exonération de sites considérés comme sensibles (par exemple, bancaires et médicaux sites) peuvent également être nécessaires si vous configurez la découverte d’application Cloud pour surveiller les. Comme mentionné ci-dessus, les administrateurs peuvent utiliser le portail de découverte d’application nuage permet de choisir si vous souhaitez avertir les utilisateurs de la collecte de données par l’agent et si vous souhaitez exiger le consentement de l’utilisateur avant le démarrage de l’agent collecte des données de l’utilisateur.

### <a name="known-issues-and-drawbacks"></a>Inconvénients et les problèmes connus
Il existe quelques cas où interception de TLS peut avoir un impact sur l’expérience de l’utilisateur final :

- Étendue certificats EV (Validation) rendent la barre d’adresse du navigateur web vert pour agir comme un indice visuel que vous visitez un site web de confiance. Inspection de TLS ne peut pas dupliquer EV dans le certificat qu’elle délivre au client, pour les sites web qui utilisent des certificats EV fonctionne normalement, mais la barre d’adresses n’affichera pas vert.  

- Public clé l’épinglage (également appelée certificat épinglage) sont conçus pour aider à protéger les utilisateurs contre les attaques de man-in-the-middle et non fiables d’autorités de certification. Lorsque le certificat racine d’un site épinglé ne correspond pas à une des connus bonne l’autorité de certification, le navigateur refuse la connexion avec une erreur. Étant donné que l’interception de TLS est, en fait, un man-in-the-middle, ces connexions échouent.

- Si les utilisateurs cliquent sur l’icône de verrou dans le navigateur de barre d’adresse pour inspecter les informations de site, ils ne verront pas une chaîne se terminant par l’autorité de certificat utilisée pour signer le certificat de site Web, mais à la place une chaîne de certificat se terminant par le Windows approuvé le magasin de certificats.

Pour réduire les occurrences de ces problèmes, nous avons effectuer le suivi des services en nuage et les applications clientes connues pour utiliser une validation étendue ou l’épinglage de clé publique et de demander à l’Agent de point de terminaison afin d’éviter l’interception des connexions d’impactés. Même dans ce cas, toutefois, vous continuerez à recevoir des rapports de l’utilisation de ces applications de cloud et le volume de données transféré, mais puisqu’ils ne sont pas complètes inspectés, aucun détail concernant la façon dont les applications ont été utilisées ne seront disponibles.


## <a name="sending-data-to-cloud-app-discovery"></a>Envoi de données à la découverte des applications de Cloud

Une fois que les métadonnées ont été collectées par l’agent, il est mis en cache sur l’ordinateur pendant une minute ou jusqu'à ce que les données mises en cache atteint une taille de 5 Mo. Il est compressé, puis envoyée sur une connexion sécurisée avec le service de découverte d’application Cloud.

Si l’agent ne peut pas communiquer avec le service de découverte d’application Cloud pour une raison quelconque, les métadonnées collectées sont stockées dans un cache de fichiers local qui est accessible par des utilisateurs privilégiés sur la machine (par exemple, le groupe Administrateurs). <br>
L’agent tente automatiquement de renvoyer les métadonnées mises en cache jusqu'à ce qu’il a été correctement reçue par le service de découverte d’application Cloud.



## <a name="receiving-the-data-at-the-service-end"></a>Reçoit les données à la fin du service

Les agents de s’authentifier à la découverte d’application Cloud service en utilisant le certificat d’authentification ordinateur client spécifique mentionnée ci-dessus et transfère les données via un canal crypté. <br>
Pipeline d’analytique du service de découverte d’application Cloud traite des métadonnées pour chaque client séparément par le partitionnement logique il à travers toutes les étapes du pipeline analytique.
Les métadonnées analysée les lecteurs différents rapports sur le portail.

Les métadonnées non transformées et les métadonnées analysées sont stockés pendant 180 jours. En outre, clients la possibilité de capturer les métadonnées analysée dans un compte de stockage blob Azure de leur choix.
Cela est utile pour l’analyse hors connexion de métadonnées ainsi que la conservation à long terme des données.

## <a name="accessing-the-data-using-the-azure-portal"></a>Accès aux données à l’aide du portail Azure

Dans le but de protéger les métadonnées recueillies, par défaut seuls les administrateurs globaux du locataire ont accès à la fonctionnalité de découverte d’application Cloud dans le portail Azure. <br>
Toutefois, les administrateurs peuvent choisir de déléguer cet accès aux autres utilisateurs ou groupes.



> [AZURE.NOTE] Pour plus d’informations, consultez [Mise en route avec nuage App découverte](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

<br>
Tout utilisateur accédant aux données dans le portail, doit être dotée d’une licence d’Azure AD Premium.



##<a name="additional-resources"></a>Ressources supplémentaires


* [Comment puis-je connaître les applications cloud non sanctionnée utilisés au sein de mon organisation](active-directory-cloudappdiscovery-whatis.md)
* [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
