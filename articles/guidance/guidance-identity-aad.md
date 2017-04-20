<properties
   pageTitle="Implémentation d’ActiveDirectory Azure | Microsoft Azure"
   description="Comment faire pour implémenter une architecture de réseau sécurisée hybride utilisant Azure Active Directory."
   services="guidance,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="telmos"/>

# <a name="implementing-azure-active-directory"></a>Implémentation d’Azure Active Directory

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les meilleures pratiques pour l’intégration de forêts et domaines de Active Directory (AD) sur site avec Azure Active Directory pour fournir une authentification de l’identité basée sur le nuage.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Vous pouvez utiliser les services d’annuaire et d’identité, tels que ceux fournis par les Services d’annuaire d’Active Directory (AD DS) pour authentifier les identités. Ces identités peuvent appartenir à des utilisateurs, des ordinateurs, des applications ou d’autres ressources qui font partie d’une limite de sécurité. Vous pouvez héberger le répertoire et identité des services locaux, mais dans un scénario hybride où se trouvent les éléments d’une application dans Azure, il peut être plus efficace d’étendre ces fonctionnalités dans le nuage. Cette approche permet de réduire le temps de latence dû à l’envoi de l’authentification et les demandes d’autorisation locale à partir du cloud dans les services d’annuaire et d’identité local en cours d’exécution.

Azure fournit deux solutions de mise en oeuvre des services d’annuaire et d’identité dans le nuage :

1. Vous pouvez utiliser [Azure Active Directory (DAS)] [ azure-active-directory] pour créer un domaine Active Directory dans le nuage et le lier à un site de domaine Active Directory. DAS vous permet de configurer de session unique (SSO) pour les utilisateurs exécutant des applications accédées via le nuage. DAS utilise [Azure Connect de AD] [ azure-ad-connect] en cours d’exécution sur site pour répliquer les objets et les identités contenues dans le référentiel local dans le nuage.

    Vous pouvez également implémenter des DAS sans utiliser un répertoire local. Dans ce cas, DAS agit comme la source principale de toutes les informations d’identité plutôt que contenant des données répliquées à partir d’un répertoire local.

    Notez que le répertoire dans le nuage n’est **pas** une extension d’un répertoire local. Au lieu de cela, il s’agit d’une copie qui contient les mêmes objets et identités. Les modifications apportées à ces éléments locaux sont copiées vers le nuage, mais les modifications effectuées dans le nuage **ne sont pas** répliqués vers le domaine local.

    >[AZURE.NOTE] Éditions haut de gamme Azure Active Directory prend en charge l’écriture différée de mots de passe utilisateurs, permettant à vos utilisateurs sur site effectuer des réinitialisations de mot de passe libre-service dans le nuage. Il s’agit de la seule information qui synchronise les DAS vers le répertoire local. Pour plus d’informations sur les différentes éditions de DAS et de leurs fonctionnalités, consultez [Azure Active Directory éditions][aad-editions].

2. Vous pouvez déployer un ordinateur virtuel qui exécute les services AD DS en tant que contrôleur de domaine dans Azure, extension de votre infrastructure Active Directory existante (y compris les services AD DS et AD FS) à partir de votre centre de données sur site. Cette approche est plus courante pour les scénarios où le réseau des locaux et Azure réseau virtuel sont connectés par une connexion VPN ou ExpressRoute. Cette solution prend également en charge bidirectionnelles réplication permettant de vous apporter des modifications dans le nuage et sur site, chaque fois qu’il est plus approprié.

Cette architecture se concentre sur la solution 1. Pour plus d’informations sur la deuxième solution, reportez-vous à la section [Extension de Active Directory vers Azure][guidance-adds].

Scénarios d’utilisation classiques de cette architecture sont les suivantes :

- Fournir l’authentification unique pour les utilisateurs finaux exécutant SaaS et autres applications dans le nuage, en utilisant les mêmes informations d’identification qu’ils spécifient pour applications sur site.

- Publication sur les sites des applications web via le nuage pour fournir l’accès aux utilisateurs distants qui appartiennent à votre organisation.

- Mise en œuvre des fonctionnalités de libre-service pour l’utilisateur final, telles que la réinitialisation de leurs mots de passe et délégation de la gestion du groupe. 

    >[AZURE.NOTE] Ces fonctionnalités peuvent être contrôlées par un administrateur via la stratégie de groupe.

- Situations où les locaux réseau et Azure virtuel hébergeant des applications en nuage ne sont pas directement liés à l’aide d’un tunnel VPN ou un circuit de ExpressRoute.

Vous devez noter que DAS ne fournit pas toutes les fonctionnalités d’Active Directory. Par exemple, DAS actuellement gère uniquement l’authentification des utilisateurs plutôt que l’authentification des ordinateurs. Certaines applications et certains services, tels que SQL Server, peuvent nécessiter une authentification d’ordinateur auquel cas cette solution n’est pas appropriée. En outre, DAS est peut-être pas approprié pour les systèmes où les composants peuvent migrer au-delà de la limite de nuage/le locaux plutôt que simplement copiées.

> [AZURE.NOTE] Pour une explication détaillée du fonctionnement d’Active Directory de Azure, regardez [Expliqué de Microsoft Active Directory][aad-explained].

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le schéma suivant met en évidence les éléments importants de cette architecture. Pour plus d’informations sur les éléments de travail dans Azure, lecture [En cours d’exécution pour ordinateurs virtuels pour une architecture à N niveaux sur Azure][implementing-a-multi-tier-architecture-on-Azure]:

> [AZURE.NOTE] Pour plus de simplicité, ce diagramme illustre les connexions directement liées à un DAS uniquement et ne représente pas la redirection d’une requête web navigateur ou autre protocole relatives au trafic qui peut se produire dans le cadre du processus d’authentification et d’identité de fédération. Par exemple, un utilisateur (local ou distant) accède généralement à une application web via un navigateur, et l’application web peut rediriger de manière transparente le navigateur web pour authentifier la demande par le biais de DAS. Une fois authentifiée, la demande peut être passée à l’application web avec les informations d’identité appropriées.

[! [0]][0]

- **Les clients Active Directory de azure**. Il s’agit d’une instance de DAS créé par votre organisation. Il agit comme un service d’annuaire simple des applications en nuage (il conserve des objets copiés à partir de sur site AD) et fournit les services d’identité.

- **Sous-réseau de niveau web**. Ce sous-réseau conserve les ordinateurs virtuels qui implémentent une application personnalisée basée sur le nuage, développée par votre organisation et qui DAS peut agir comme un broker d’identité.

- **Sur site serveur AD DS**. Votre organisation susceptible de possède déjà un service d’annuaire existants tels que les services AD DS. Vous pouvez synchroniser tous les éléments dans votre répertoire de domaine Active Directory (par exemple, l’utilisateur et les informations de groupe) avec DAS, pour permettre de DAS à utiliser ces informations pour authentifier les identités.

- **Serveur de synchronisation d’azure Connect d’Active Directory**. Il s’agit d’un ordinateur local qui exécute le service de synchronisation d’Azure Connect d’Active Directory. Vous installez ce service en utilisant le logiciel Azure Connect d’Active Directory. Le service de synchronisation d’Azure AD connexion synchronise les informations (utilisateurs, groupes, Contacts, etc.) dans les locaux annonce DAS dans le nuage. Par exemple, vous pouvez provisionner ou deprovision les utilisateurs et groupes locaux, et ces modifications sont propagées à DAS. Le service de synchronisation d’Azure AD Connect transmet des informations au locataire DAS.

    >[AZURE.NOTE] Pour des raisons de sécurité, les mots de passe de l’utilisateur ne sont pas stockés directement dans un DAS. Au lieu de cela, DAS contient un hachage de chaque mot de passe. Cela est suffisant pour vérifier le mot de passe d’un utilisateur. Si un utilisateur requiert une réinitialisation de mot de passe, ce doit être effectuée sur site et le nouveau hachage envoyé à DAS. Les éditions Premium de DAS incluent des fonctionnalités permettant d’automatiser cette tâche pour permettre aux utilisateurs de réinitialiser leurs mots de passe.

## <a name="recommendations"></a>Recommandations

Cette section résume les recommandations pour implémenter DAS comme suit.

- Connexion d’Active Directory
- Sécurité

### <a name="azure-ad-connect-sync-service-recommendations"></a>Recommandations de service de synchronisation AD Connect Azure

S’assurer que les informations d’identité aux utilisateurs dans le nuage sont cohérentes avec celle détenue dans les locaux de concerne la synchronisation. L’objectif du service de synchronisation d’Azure Connect d’Active Directory est de maintenir cette cohérence. Les points suivants résument les recommandations pour implémenter le service de synchronisation d’Azure Connect d’annonce :

- Avant d’implémenter la synchronisation d’Azure Connect de publicité, vous devez déterminer les exigences de synchronisation de votre organisation (les éléments à synchroniser, de quels domaines et la fréquence. Le [exigences en matière de synchronisation de répertoire déterminer] l’article[ aad-sync-requirements] décrit les points que vous devez prendre en compte.

- Vous pouvez exécuter le service de synchronisation d’Azure Connect d’Active Directory à l’aide d’une machine virtuelle ou un ordinateur hébergé sur site. En fonction de la volatilité des données de votre annuaire AD, une fois la synchronisation initiale avec DAS a été effectuée la charge sur le service de synchronisation d’Azure AD Connect n’est probablement pas élevé. À l’aide d’une machine virtuelle vous permet d’évoluer plus facilement le serveur (moniteur de l’activité, comme décrit dans la section [Considérations relatives à la surveillance](#monitoring-considerations) pour déterminer si cela est nécessaire).

- Si vous avez plusieurs domaines locaux dans une forêt, vous pouvez stocker et synchroniser des informations pour l’ensemble de la forêt à un seul client de DAS (c’est l’approche recommandée). Informations de filtre pour les identités qui se produisent dans plusieurs domaines, afin que chaque identité apparaît une seule fois dans DAS, plutôt que d’être dupliqué car cela peut provoquer des incohérences de lors de la synchronisation de données. Cette approche nécessite la mise en œuvre de plusieurs serveurs de synchronisation d’Azure Connect d’Active Directory. Pour plus d’informations, consultez le scénario de plusieurs DAS dans la section [Considérations relatives à la topologie](#topology-considerations) . 

- Utiliser le filtrage pour limiter les données stockées dans DAS seulement celle qui est nécessaires. Par exemple, votre organisation peut facturer des pas stocker des informations sur les comptes inactifs ou non personnelles dans DAS. Le filtrage peut être basée sur le groupe, basée sur le domaine, basé sur l’unité d’organisation ou basée sur l’attribut, et vous pouvez combiner des filtres pour générer des règles plus complexes. Par exemple, vous pouvez sélectionner pour synchroniser uniquement les objets contenus dans un domaine ayant une valeur spécifique dans un attribut sélectionné. Pour plus d’informations, consultez [Azure Connect de AD sync : configurer le filtrage de][aad-filtering].

- Pour assurer une haute disponibilité pour le service de synchronisation de connexion d’Active Directory, exécutez un serveur secondaire de transfert. Pour plus d’informations, reportez-vous à la section [Considérations relatives à la topologie](#topology-considerations) .

### <a name="security-recommendations"></a>Recommandations de sécurité

Les éléments suivants résument les recommandations de sécurité principal pour la mise en œuvre DAS, selon les besoins de votre organisation :

- Gestion des mots de passe utilisateurs.

    Si vous utilisez une édition premium de DAS, vous pouvez activer le mot de passe à écriture différée du DAS dans votre répertoire local en effectuant une installation personnalisée de Azure Connect d’annonce :

    [! [9]][9]

    Cette fonctionnalité permet aux utilisateurs de réinitialiser leurs mots de passe à partir du portail Azure, mais ne doit être activée qu’après avoir examiné la stratégie de sécurité de mot de passe de votre organisation. Par exemple, vous pouvez limiter les utilisateurs qui peuvent modifier leurs mots de passe, et vous pouvez personnaliser l’environnement de gestion de mot de passe. Pour plus d’informations, consultez [Personnalisation de gestion de mots de passe en fonction des besoins de votre organisation][aad-password-management].

- Maintenir la protection des applications sur site qui sont accessibles de l’extérieur.

    Le Proxy d’Application AD Azure permet de fournir un accès contrôlé à des applications sur site web par les utilisateurs externes par le biais de DAS. Cette approche protège vos applications en les exposant ne pas directement à Internet. Seuls les utilisateurs disposant des informations d’identification valides dans votre répertoire Azure sont en mesure d’atteindre vos applications. Pour plus d’informations, consultez l’article [Activer le Proxy d’Application dans Azure portal][aad-application-proxy].

- Surveillez le DAS pour rechercher des signes d’activité suspecte.

    Envisagez d’utiliser l’édition DAS Premium P2, ce qui inclut la Protection d’identité DAS. Protection d’identité utilise des algorithmes d’apprentissage adaptatif automatique et l’analyse heuristique pour détecter les anomalies et les risques des événements susceptibles d’indiquer qu’une identité a été compromise. Par exemple, il peut détecter une activité potentiellement anormale comme irréguliers reconnectez-vous activités, connexions provenant de sources inconnues ou à partir d’adresses IP avec une activité suspecte ou les connexions à partir de périphériques qui peuvent être infectés. Protection d’identité à l’aide de ces données, génère des rapports et des alertes qui vous permet d’analyser ces événements risque et de prendre des mesures correctives appropriées ou l’action d’atténuation. Pour plus d’informations, consultez [Protection d’identité Azure Active Directory][aad-identity-protection].

    Vous pouvez également utiliser la fonction de rapport du DAS dans le portail Azure pour surveiller suspectes et d’autres activités liées à la sécurité qui se produisent dans votre système. DAS peut générer une série de rapports de synthèse :

    [! [17]][17]

    Pour plus d’informations sur l’utilisation de ces rapports, consultez le [Guide Azure Active Directory Reporting][aad-reporting-guide].

## <a name="topology-considerations"></a>Considérations relatives à la topologie

Si vous intégrez un répertoire local avec DAS, configurer Azure Connect AD pour implémenter une topologie la plus proche des besoins de votre organisation. Azure AD Connect prend en charge les topologies suivantes :

- **Forêt unique, seul répertoire DAS**. Dans cette topologie, Azure Connect d’Active Directory vous permet de synchroniser les objets et les informations d’identité dans un ou plusieurs domaines dans une seule forêt avec un seul client de DAS sur site. Il s’agit de la topologie par défaut mis en œuvre par l’installation expresse d’Azure Connect d’Active Directory.

    [! [1]][1]

    Ne créez pas plusieurs serveurs de synchronisation Azure AD Connect pour connecter les différents domaines dans la même forêt locale au locataire DAS même, sauf si vous exécutez un serveur de synchronisation d’Azure Connect de publicité dans le mode de mise en attente (voir le serveur de la zone de transit ci-dessous).

- **Plusieurs forêts, seul répertoire DAS**. Utilisez cette topologie si vous avez plus d’une forêt sur site. Vous pouvez consolider des informations d’identité afin que chaque utilisateur unique est représenté qu’une seule fois dans le répertoire DAS, même si le même utilisateur existe dans plusieurs forêts. Toutes les forêts d’utilisent le même serveur de synchronisation d’Azure Connect d’AD. Le serveur de synchronisation d’Azure Connect d’Active Directory n’a pas à faire partie d’un domaine, mais il doit être accessible à partir de toutes les forêts :

    [! [2]][2]

    Dans cette topologie, n’utilisez pas séparée connexion AD Azure synchroniser les serveurs se connecter chaque forêt sur site pour un seul client de DAS. Cela peut entraîner des informations d’identité dupliquées dans DAS si les utilisateurs sont présents dans plusieurs forêts.

- **Plusieurs forêts, les topologies distinctes**. Cette approche vous permet de fusionner des informations d’identité à partir de forêts distinctes dans un mono-utilisateur DAS. Cette stratégie est utile si vous combinez des forêts provenant de différentes organisations (après une fusion ou d’acquisition, par exemple), les informations d’identité de chaque utilisateur sont maintenues dans une seule forêt :

    Si les listes d’adresses globales dans chaque forêt sont synchronisées, puis un utilisateur d’une forêt peut-être être présent dans un autre en tant que contact. Cela peut se produire si, par exemple, votre organisation a mis en œuvre GALSync avec le Gestionnaire d’identités de Forefront 2010 ou Microsoft Identity Manager 2016. Dans ce scénario, vous pouvez spécifier que les utilisateurs doivent être identifiés par leur attribut de *messagerie* . Vous pouvez également faire correspondre les identités à l’aide d’attributs *ObjectSID* et *msExchMasterAccountSID* . Cela est utile si vous avez une ou plusieurs forêts de ressources avec des comptes désactivés.

- Le **serveur intermédiaire**. Dans cette configuration, vous exécutez une deuxième instance du serveur de synchronisation d’Azure Connect de publicité en parallèle avec le premier. Cette structure prend en charge des scénarios tels que :

    - Une haute disponibilité.

    - Test et déploiement d’une nouvelle configuration du serveur de synchronisation d’Azure Connect d’Active Directory.

    - Introduction d’un nouveau serveur et retrait d’une ancienne configuration. 

    Dans ces scénarios, la deuxième instance s’exécute en *mode de mise en attente*. Le serveur enregistre les objets importés et les données de synchronisation dans sa base de données, mais ne passe pas les données pour le DAS. Uniquement lorsque vous désactivez le mode intermédiaire le serveur démarre l’écriture de données DAS, et également démarre performantes de mot de passe écriture-sauvegarde dans les répertoires locaux le cas échéant :

    [! [4]][4]

    Pour plus d’informations, consultez [Azure Connect de AD sync : tâches opérationnelles et considérations relatives à la][aad-connect-sync-operational-tasks].

- **DAS plusieurs répertoires**. Il est recommandé que vous créez un répertoire DAS unique pour une organisation, mais il peut y avoir des situations où vous devez aux informations de partition dans des répertoires distincts de DAS. Dans ce cas, vous devez vous assurer que chaque objet de la forêt locale apparaît uniquement dans un seul répertoire DAS, pour éviter les problèmes d’écriture différée de synchronisation et de mot de passe.

    Pour implémenter ce scénario, configurez distincte connexion AD Azure synchroniser les serveurs pour chaque répertoire DAS et utiliser le filtrage pour chaque serveur de synchronisation d’Azure Connect d’Active Directory opère sur un ensemble mutuellement exclusif d’objets : 

    [! [5]][5]

Pour plus d’informations sur ces topologies, consultez [Topologies pour Azure Connect de AD][aad-topologies].

## <a name="user-sign-in-considerations"></a>Considérations sur la connexion utilisateur

Par défaut, le service DAS suppose que les utilisateurs se connecteront en fournissant le mot de passe qu’ils utilisent sur site que le serveur de synchronisation d’Azure Connect d’Active Directory configure la synchronisation de mot de passe entre le domaine de locaux et d’un DAS. Pour de nombreuses organisations, cela est approprié, mais vous devez envisager les stratégies existantes et l’infrastructure de votre organisation. Par exemple :

- La stratégie de sécurité de votre entreprise peut interdire la synchronisation des hachages de mot de passe dans le nuage.

- Vous pouvez demander que les utilisateurs connaissent l’authentification transparente (sans invite de mot de passe supplémentaire) lors de l’accès aux ressources de cloud de domaine rejoint les ordinateurs sur le réseau d’entreprise.

- Votre entreprise possède peut-être déjà ADFS ou par un fournisseur tiers fédération déployé. Vous pouvez configurer DAS pour utiliser cette infrastructure pour implémenter l’authentification et l’authentification unique, au lieu d’utiliser les informations de mot de passe conservées dans le nuage.

Pour plus d’informations, reportez-vous à la section [Azure AD se connecter connexion utilisateur sur les options de][aad-user-sign-in].

## <a name="azure-ad-application-proxy-considerations"></a>Considérations sur le proxy application Azure AD

AD Azure permet d’accéder aux applications sur site.

- Exposer vos applications du web sur site à l’aide du proxy d’application de gérer les connecteurs par le composant proxy d’application Azure AD. Le connecteur de proxy d’application ouvre une connexion de réseau sortant vers le proxy d’application Azure AD. Demandes d’utilisateurs distants sont routés de DAS par le biais de cette connexion pour les applications web. Ce mécanisme supprime la nécessité d’ouvrir les ports d’entrée dans le pare-feu local, en réduisant la surface d’attaque exposée par votre organisation.

Pour plus d’informations, consultez [applications de publication à l’aide du proxy d’Application de AD Azure][aad-application-proxy].

## <a name="object-synchronization-considerations"></a>Considérations relatives à la synchronisation objet

La configuration par défaut d’Azure AD connexion synchronise les objets de votre annuaire AD local basé sur l’ensemble de règles spécifié dans l’article [Azure Connect de AD sync : présentation de la configuration par défaut][aad-connect-sync-default-rules]. Seuls les objets qui satisfont à ces règles sont synchronisés, d’autres sont ignorés. Par exemple, les objets de l’utilisateur doivent avoir un attribut unique *sourceAnchor* et l’attribut *accounEnabled* doit être rempli. Les objets utilisateur qui n’ont pas d’attribut *sAMAccountName* ou qui commencent par le texte *AAD_* ou *MSOL_* ne sont pas synchronisées. Azure Connect de publicité s’applique plusieurs règles pour les objets utilisateur, Contact, groupe, ForeignSecurityPrincipal et ordinateur. Si vous avez besoin de modifier l’ensemble par défaut des règles, utilisez l’éditeur de règles de synchronisation installé avec Azure AD Connect (également documentées dans [Azure Connect de AD sync : présentation de la configuration par défaut][aad-connect-sync-default-rules]).

Vous pouvez définir vos propres filtres pour limiter les objets à être synchronisé par le domaine ou l’unité d’organisation. Ou implémenter un filtrage personnalisé plus complexe, comme décrit dans [Azure Connect de AD sync : configurer le filtrage de][aad-filtering].

## <a name="security-considerations"></a>Considérations sur la sécurité

Contrôle d’accès conditionnel permet de refuser les demandes d’authentification provenant de sources inattendues :

- Déclencher [Azure plusieurs facteurs d’authentification (AMF)] [ azure-multifactor-authentication] si un utilisateur tente de se connecter à partir d’un emplacement non approuvé (tels que via Internet) au lieu d’un réseau approuvé.

- Utilisez le type de plate-forme de périphérique de l’utilisateur (iOS, Android, Windows Mobile, Windows) pour déterminer la stratégie d’accès aux applications et aux fonctionnalités.

- Enregistrer l’état activé/désactivé des périphériques des utilisateurs et les incorporer dans les vérifications de stratégie d’accès. Par exemple, si le téléphone d’un utilisateur est perdu ou volé elle doit enregistrée comme désactivé pour empêcher l’utilisation d’accéder.

- Contrôle le niveau d’accès d’un utilisateur selon l’appartenance au groupe. Utiliser les [règles d’appartenance dynamique DAS] [ aad-dynamic-membership-rules] pour simplifier l’administration de groupe. Pour un bref aperçu de comment cela fonctionne, consultez [Introduction à la dynamique des appartenances de groupes][aad-dynamic-memberships].

- Utiliser des stratégies de risque d’accès conditionnel avec Protection d’identité DAS pour fournir une protection avancée basée sur reconnectez-vous activités inhabituelles ou autres événements.

Pour plus d’informations, consultez [accès conditionnel d’Azure Active Directory][aad-conditional-access].

## <a name="scalability-considerations"></a>Évolutivité

L’évolutivité est adressée par le service DAS et la configuration du serveur de synchronisation d’Azure Connect d’annonce :

- Pour le service DAS, vous n’êtes pas obligé de configurer toutes les options pour implémenter l’évolutivité. Le service DAS prend en charge l’évolutivité en fonction des réplicas. DAS implémente un seul réplica principal, qui gère les opérations d’écriture, et plusieurs réplicas secondaires en lecture seule. DAS redirige les écritures tentatives effectuées sur les réplicas secondaires au réplica principal de façon transparente. DAS fournit la cohérence éventuelle. Toutes les modifications apportées à un réplica principal sont propagées vers les réplicas secondaires. Comme la plupart des opérations contre DAS lectures plutôt que des écritures, cette architecture est évolutif.

    Pour plus d’informations, consultez [AD Azure : les coulisses de notre annuaire nuage géo-redondants, distribuée, très disponible][aad-scalability].

- Pour le serveur de synchronisation Azure Connect d’Active Directory, vous devez déterminer combien d’objets vous pouvez synchroniser à partir de votre répertoire local. Si vous avez moins de 100 000 objets, vous pouvez utiliser le logiciel de SQL Server Express LocalDB par défaut fourni avec Azure Connect d’Active Directory. Si vous avez un grand nombre d’objets, vous devez installer une version de production de SQL Server et effectuer une installation personnalisée de la spécification d’Azure Connect de publicité qu’il doit utiliser une instance existante de SQL Server.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Comme avec les problèmes d’évolutivité, disponibilité couvre le service DAS et la configuration d’Azure Connect d’annonce :

- Le service DAS est conçu pour offrir une haute disponibilité. Il n’y a aucune option de disponibilité configurable par l’utilisateur. Il est distribué géographiquement et s’exécute dans les données de plusieurs centres de propagation dans le monde entier, avec basculement automatique sur incident. Si un centre de données devient indisponible, DAS garantit que les données de votre annuaire soient accessible à l’instance au moins deux centres de données dispersés plus.

    >[AZURE.NOTE] Le contrat SLA pour les DAS base et Premium services garanties au moins 99,9 % la disponibilité. Il n’y a pas de SLA pour la couche libre de DAS. Pour plus d’informations, consultez le [contrat SLA pour Azure Active Directory][sla-aad].

- Pour augmenter la disponibilité du serveur de synchronisation d’Azure Connect d’Active Directory, vous pouvez exécuter une deuxième instance de mode, la zone de transit comme décrit dans la section [Considérations relatives à la topologie](#topology-considerations) . 

    En outre, si vous n’utilisez pas l’instance de SQL Server Express LocalDB fourni avec Azure Connect d’Active Directory, vous devez envisager haute disponibilité pour SQL Server. Notez que la solution de disponibilité élevée uniquement pris en charge est mise en cluster de SQL. Solutions de mise en miroir et toujours sur ne sont pas pris en charge par Azure Connect d’Active Directory.

    Pour des considérations supplémentaires sur la gestion de la disponibilité du serveur synchronisation Azure Connect de publicité et comment récupérer après une panne, consultez [Azure Connect de AD sync : tâches opérationnelles et considérations - récupération d’urgence][aad-sync-disaster-recovery].

## <a name="management-considerations"></a>Considérations relatives à la gestion

Il existe deux aspects de la gestion des DAS :

1. Administration de DAS dans le nuage.

2. Mise à jour les serveurs de synchronisation d’Azure Connect d’Active Directory.

DAS fournit les options suivantes pour la gestion des domaines et des répertoires dans le nuage :

- [Azure Active Directory PowerShell Module][aad-powershell]. Utilisez ce module si vous avez besoin écrire le script AD Azure les tâches administratives courantes telles que la gestion des utilisateurs, gestion de domaine et de configuration de l’authentification unique.

- Blade de gestion AD Azure dans le portail Azure. Cette blade fournit un affichage interactif de gestion de l’annuaire et vous permet de contrôler et de configurer la plupart des aspects de DAS.

    [! [10]][10]

Azure Connect d’Active Directory installe les outils suivants qui vous permet de gérer les services de synchronisation Azure Connect d’Active Directory à partir de vos ordinateurs locaux :

- La console Microsoft Azure Active Directory se connecter. Cet outil vous permet de modifier la configuration du serveur Azure AD Sync, personnaliser comment s’effectue la synchronisation, activer ou désactiver le mode intermédiaire et basculer le mode d’ouverture de session utilisateur (vous pouvez activer la AD FS connexion à l’aide de votre infrastructure sur site).

- Le Gestionnaire de Service de synchronisation. Utilisez l’onglet *opérations* dans cet outil pour gérer le processus de synchronisation et de détecter si toutes les parties du processus ont échoué. Vous pouvez déclencher des synchronisations manuellement à l’aide de cet outil. 

    [! [12]][12]

    L’onglet *connecteurs* vous permet de contrôler les connexions pour les domaines (sur site et dans le cloud) auquel est rattaché le moteur de synchronisation :

    [! [13]][13]

-  L’éditeur de règles de synchronisation. Cet outil permet de personnaliser la façon dont les objets sont transformés lorsqu’ils sont copiés entre un répertoire des locaux et du DAS dans le nuage. Cet outil vous permet de spécifier des attributs supplémentaires et les objets de synchronisation et des filtres pour déterminer les instances doivent ou ne doivent pas être synchronisées.

    Pour plus d’informations, reportez-vous à la section de l’éditeur de règles de synchronisation dans le document [Azure Connect de AD sync : présentation de la configuration par défaut][aad-connect-sync-default-rules].

La page [Azure Connect de AD sync : meilleures pratiques pour la modification de la configuration par défaut] [ aad-sync-best-practices] contient des informations supplémentaires et des conseils de gestion Azure Connect d’Active Directory.

## <a name="monitoring-considerations"></a>Considérations relatives au contrôle

Le contrôle d’état est effectué à l’aide d’une série d’agents installés sur site :

- Azure Connect d’Active Directory installe un agent qui capture des informations sur les synchronisations. Utilisez la lame de fonctionnement Azure Active Directory se connecter dans le portail Azure pour surveiller la santé et les performances d’Azure Connect d’AD. Pour plus d’informations, reportez-vous [à l’aide de Azure AD se connecter la santé pour la synchronisation][aad-health].

- Pour surveiller l’état de santé des domaines AD DS et des répertoires à partir d’Azure, installer la santé se connecter AD Azure pour l’agent du service d’annuaire Active Directory sur un ordinateur dans le domaine local. Utilisez la lame de fonctionnement Azure Active Directory se connecter dans le portail Azure à surveiller les services AD DS. Pour plus d’informations, reportez-vous [à l’aide de Azure AD se connecter la santé avec les services AD DS][aad-health-adds]

- Installez la santé se connecter AD Azure pour agent Federation Services surveiller la santé de AD FS exécuté sur site et la lame de fonctionnement Azure Active Directory se connecter dans le portail Azure pour surveiller les services AD DS. Pour plus d’informations, consultez [AD à l’aide de Azure se connecter la santé avec AD FS][aad-health-adfs]

Pour plus d’informations sur les agents de l’état de connexion AD et leurs exigences, consultez [Azure AD connecter santé Installation de l’Agent][aad-agent-installation].

## <a name="sample-solution"></a>Solution de l’échantillon

Cette section décrit les étapes pour la création d’un exemple de solution que vous pouvez utiliser pour tester la configuration de DAS. La solution comprend les éléments suivants :

- Une application web de n-couche, suivant la structure décrite par [l’Exécution de machines virtuelles d’une architecture à N niveaux sur Azure][implementing-a-multi-tier-architecture-on-Azure].

- Un ordinateur client de test. Nous recommandons d’utiliser une autre VM pour cet ordinateur. La configuration de cet ordinateur virtuel est sans importance, tant que vous disposez des droits d’accès administrateur et il peut se connecter à l’application web de n-couche.

- Un environnement local simulée qui contient son propre domaine créé à l’aide des services AD DS.

Les scénarios illustrant ces étapes sont les suivantes :

- Permettant l’accès à l’application web de n niveaux en cours d’exécution dans le cloud pour les utilisateurs externes, avec DAS fourniture d’authentification de mot de passe.

- Permettant l’accès à l’application web de n niveaux en cours d’exécution dans le cloud pour les utilisateurs en cours d’exécution au sein de l’organisation, avec DAS fourniture d’authentification de mot de passe.

### <a name="prerequisites"></a>Conditions préalables

Les étapes qui suivent supposent que les conditions préalables suivantes :

- Vous avez un abonnement Azure existant dans lequel vous pouvez créer des groupes de ressources.

- Vous avez installé l' [Interface de ligne de commande de Azure][azure-cli].

- Vous avez téléchargé et installé la version la plus récente. Consultez [ici] [ azure-powershell-download] pour obtenir des instructions.

- Vous avez installé une copie de la [makecert] [ makecert] utilitaire sur l’ordinateur client de test.

- Vous avez accès à un ordinateur de développement avec Visual Studio installé.

### <a name="create-the-n-tier-web-application-architecture"></a>Créer l’architecture d’application à n niveaux web

1. Créez un dossier nommé `Scripts` qui contient un sous-dossier nommé `Parameters`.

2. Télécharger le [Déploiement-ReferenceArchitecture.ps1] [ solution-script] script PowerShell pour le dossier Scripts.

3. Dans le dossier Paramètres, créez un autre sous-dossier nommé Windows.

4. Téléchargez les fichiers suivants dans le dossier Paramètres/Windows :

    - [virtualNetwork.parameters.json][vnet-parameters-windows]

    - [networkSecurityGroup.parameters.json][nsg-parameters-windows]

    - [webTierParameters.json][webtier-parameters-windows]

    - [businessTierParameters.json][businesstier-parameters-windows]

    - [dataTierParameters.json][datatier-parameters-windows]

    - [managementTierParameters.json][managementtier-parameters-windows]

5. Modifiez le fichier de **Déploiement-ReferenceArchitecture.ps**1 dans le dossier Scripts et modifiez la ligne suivante pour spécifier le groupe de ressources qui doit être créé ou utilisé pour stocker l’ordinateur virtuel et les ressources créées par le script :

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-ntier-rg"
        ```

6. Modifiez les fichiers suivants dans le dossier **et les paramètres de fenêtre**et définir le `resourceGroup` valeur dans le `virtualNetworkSettings` section dans chacun de ces fichiers est le même que que vous avez spécifié dans le fichier de script de **Déploiement-ReferenceArchitecture.ps1** .

    - networkSecurityGroup.parameters.json

    - webTierParameters.json

    - businessTierParameters.json

    - dataTierParameters.json

    - managementTierParameters.json

7. Ouvrez une fenêtre PowerShell d’Azure, déplacer vers le dossier de Scripts et exécutez la commande suivante :

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows ntier
        ```

    Remplacer **<subscription id>** avec votre ID d’abonnement Azure.

    Pour **<location>**, permet de spécifier une région Azure, par exemple *eastus* ou *westus*.

8. Une fois le script terminé, utilisez le portail Azure pour obtenir l’adresse IP publique de l’équilibreur de charge du niveau web (*ra-DAS-MULTICOUCHE-web-lb*) :

    [! [18]][18]

9. Se connecter à votre client test ordinateur (ou VM) et vérifiez que vous pouvez accéder à la couche web à l’aide d’Internet Explorer pour accéder à l’adresse IP publique de l’équilibreur de charge du niveau web. La page IIS par défaut doit s’afficher.

### <a name="simulate-configuration-of-a-public-web-site"></a>Simuler la configuration d’un site web public

>[AZURE.NOTE] Les étapes suivantes de simuler la couche web associant le nom DNS www.contoso.com en modifiant le fichier hosts sur l’ordinateur client de test. En outre, les ordinateurs virtuels du niveau web sont configurés avec des certificats auto-signés et un substitut d’autorité d’hébergement. Il s’agit de test uniquement et **que vous ne devez pas utiliser ces techniques dans un environnement de production**.

1. Sur votre ordinateur client de test, modifiez le fichier **C:\Windows\System32\drivers\etc\hosts** en utilisant le bloc-notes et ajoutez une entrée qui associe le nom www.contoso.com avec l’adresse IP publique de l’équilibreur de charge du niveau web :

        ```text
        # Copyright (c) 1993-2009 Microsoft Corp.
        #
        # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
        #
        # This file contains the mappings of IP addresses to host names. Each
        # entry should be kept on an individual line. The IP address should
        # be placed in the first column followed by the corresponding host name.
        # The IP address and the host name should be separated by at least one
        # space.
        #
        # Additionally, comments (such as these) may be inserted on individual
        # lines or following the machine name denoted by a '#' symbol.
        #
        # For example:
        #
        #      102.54.94.97     rhino.acme.com          # source server
        #       38.25.63.10     x.acme.com              # x client host
        
        # localhost name resolution is handled within DNS itself.
        #   127.0.0.1       localhost
        #   ::1             localhost
        
        52.165.38.64    www.contoso.com
        ```

2. Vérifiez que vous pouvez maintenant accéder à www.contoso.com à partir de l’ordinateur client de test. La même page IIS par défaut doit apparaître comme avant.

3. Sur l’ordinateur client de test, ouvrez une invite de commande en tant qu’administrateur et utilisez l’utilitaire makecert à c
4. créer un certificat d’autorité de certification racine factice :

        ```
        makecert -sky exchange -pe -a sha256 -n "CN=MyFakeRootCertificateAuthority" -r -sv MyFakeRootCertificateAuthority.pvk MyFakeRootCertificateAuthority.cer -len 2048
        ```

    Vérifiez que les fichiers suivants sont créés :

    - MyFakeRootCertificateAuthority.cer

    - MyFakeRootCertificateAuthority.pvk

4. Exécutez la commande suivante pour installer l’autorité de certification de racine de test :

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. L’autorité de certification test permet de générer un certificat pour www.contoso.com :

        ```
        makecert -sk pkey -iv MyFakeRootCertificateAuthority.pvk -a sha256 -n "CN=www.contoso.com" -ic MyFakeRootCertificateAuthority.cer -sr localmachine -ss my -sky exchange -pe
        ```

6. Exécutez la commande de la **console mmc** et ajouter le composant logiciel enfichable Certificats pour le compte d’ordinateur pour l’ordinateur local.

7. Dans le */Certificates (ordinateur Local) / personnel/certificats/* banque, exportez le certificat www.contoso.com avec sa clé privée dans un fichier nommé www.contoso.com.pfx :

    [! [20]][20]

8. Retourner au portail Azure et se connecter à la couche de gestion VM (*ra-DAS-MULTICOUCHE-gestion-vm1*). Le nom d’utilisateur par défaut est *testuser* et le mot de passe *AweS0me@PW*:

    [! [21]][21]
    
9. À partir de la couche de gestion VM, connectez-vous à chacun de la couche web VMs à son tour avec le nom d’utilisateur *testuser* et le mot de passe *AweS0me@PW*et effectuer les tâches suivantes. Notez que les ordinateurs virtuels ont l’IP d’adresses privées 10.0.1.4, 10.0.1.5 et 10.0.1.6 :

    >[AZURE.NOTE] La couche web ordinateurs virtuels ont uniquement des adresses IP privées. Vous pouvez uniquement y connecter à l’aide de la couche de gestion VM.

    1. Copiez les fichiers *www.contoso.com.pfx* et *MyFakeRootCertificateAuthority.cer* à partir de l’ordinateur client de test.
    
    2. Ouvrez une invite de commande en tant qu’administrateur, déplacer vers le dossier contenant les fichiers que vous avez copié et exécutez les commandes suivantes :
    
        ```
        certutil.exe -privatekey -importPFX my www.contoso.com.pfx NoExport

        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

    3. Exécuter le `mmc` de commande, ajoutez le composant logiciel enfichable Certificats pour le compte d’ordinateur pour l’ordinateur local et vérifiez que les certificats suivants ont été installés :

        - \Certificates (ordinateur local) \Personal\Certificates\ www.contoso.com

        - Authorities\Certificates\MyFakeRootCertificateAuthority de Certification de racine de \Trusted \Certificates (ordinateur local)

    4. Démarrez la console Gestionnaire des Services Internet (IIS) et accédez au *Site Web de par* l’ordinateur.

    5. Dans le volet *Actions* , cliquez sur *liaisons*et ajouter une liaison https à l’aide du certificat SSL www.contoso.com :

        [! [22]][22]

10. Retournez à l’ordinateur client de test et vérifiez que vous pouvez maintenant accéder à https://www.contoso.com.

### <a name="create-an-azure-active-directory-tenant"></a>Créer un locataire Azure Active Directory

1. L’utilisation du portail Azure, créer un locataire Azure Active Directory tels que *myaadname*. onmicrosoft.com, où *myaadname* est un nom de répertoire que vous avez choisi.

2. Ajouter un utilisateur appelé *admin* avec le rôle de GlobalAdmin dans le répertoire. Prenez note du mot de passe qui vient d’être généré.

3. À l’aide d’Internet Explorer, accédez à https://account.activedirectory.windowsazure.com/ et connectez-vous en tant que admin@ *myaadname*. onmicrosoft.com. Modifier votre mot de passe lorsque vous y êtes invité.

### <a name="create-and-deploy-a-test-web-application"></a>Créer et déployer une application web de test

1. À l’aide de Visual Studio sur l’ordinateur de développement, de créer une application Web d’ASP.NET nommée ContosoWebApp1 (utiliser le.NET Framework 4.5.2) :

    [! [23]][23]

2. Sélectionnez le modèle *MVC* , modifiez l’authentification pour le *travail et l’établissement des comptes*et spécifier le nom de votre client DAS. Ne créez pas une application de Service dans le nuage.

    [! [24]][24]

3. Générez et exécutez l’application pour tester l’authentification. Dans la page de connexion, entrez le nom du compte admin@ *myaadname*. onmicrosoft.com, le mot de passe, puis cliquez sur *se connecter*:

    [! [25]][25]

4. Vérifiez que l’option DAS demande l’autorisation de vous connecter et la lecture de votre profil et puis démarre l’application web en cours d’exécution avec l’identité de l’administrateur.

5. Fermez Internet Explorer et revenir à Visual Studio.

6. Ouvrez le fichier web.config, puis, dans le `<appSettings>` section, changez la valeur de la clé de *l’ida : PostLogoutRedirectUri* à *https://www.contoso.com:443 /*. Enregistrez le fichier.

7. Dans la fenêtre de *L’Explorateur de solutions* , cliquez sur le projet ContosoWebApp1 et cliquez sur *Publier*.

8. Dans la fenêtre *Publier le site Web* , cliquez sur *personnalisé*. Créer un profil personnalisé nommé *ContosoWebApp1*.

9. Sur la page de *connexion* , définissez la *méthode de publication* pour le *Système de fichiers* et spécifier un dossier nommé *ContosoWebApp*, situé dans un emplacement approprié sur votre ordinateur de développement.

10. Dans la page *paramètres* , affectez la *Configuration* *Release*.

11. Dans la page *d’Aperçu* , cliquez sur *Publier*.

12. Se connecter à chaque serveur web à son tour (via la couche de gestion VM) et d’effectuer les tâches suivantes :

    1. Copiez le dossier *ContosoWebApp* et son contenu à partir de l’ordinateur de développement vers le dossier *C:\inetpub* .

    2. La console Gestionnaire des Services Internet (IIS), accédez au *Site Web de par* sur l’ordinateur.

    3. Dans le volet *Actions* , cliquez sur *Paramètres de la base*et modifier le chemin d’accès physique du site web de *%SystemDrive%\inetpub\ContosoWebApp*:

        [! [26]][26]

### <a name="publish-the-test-web-application-through-aad"></a>Publier l’application web de test par l’intermédiaire de DAS

1. Connectez-vous au portail Azure et accédez au répertoire DAS.

2. Dans l’onglet *Applications* , cliquez sur l’application ContosoWebApp1.

3. Vérifiez que votre application est correctement ajoutée dans le répertoire, puis cliquez sur l’onglet *configurer* .

4. Modifier l' *URL de connexion* à https://www.contoso.com:443 et de définir l' *URL de la réponse* à https://www.contoso.com:443 (la même URL).

5. Enregistrer la configuration.

6. Sur l’ordinateur client de test, accédez à https://www.contoso.com. Vérifiez que DAS vous demande vos informations d’identification, puis ouvrez une session.

>[AZURE.NOTE] C’est le point de terminaison pour le premier scénario : activation de l’accès à l’application web de n niveaux en cours d’exécution dans le cloud pour les utilisateurs externes, avec DAS fourniture d’authentification de mot de passe.

### <a name="create-a-simulated-on-premises-environment-with-active-directory"></a>Créer un environnement simulé sur site avec Active Directory

L’environnement local contient deux contrôleurs de domaine pour le `contoso.com` service de synchronisation de domaine et deux serveurs pour héberger la connexion AD Azure. Les serveurs d’hébergement Azure Connect de publicité ne sont pas à un domaine.

1. Dans l’Explorateur de fichiers, de retour dans le dossier Scripts contenant le script utilisé pour créer l’application web multicouches.

2. Dans le dossier Paramètres, ajoutez un autre dossier sub nommé `Onpremise`.

3. Téléchargez les fichiers suivants vers le dossier des paramètres/locales :

    - [Ajouter-ajoute-domaine-controller.parameters.json][add-adds-domain-controller-parameters]

    - [créer-ajoute-forêt-extension.parameters.json][create-adds-forest-extension-parameters]

    - [desmachines virtuelles-adc.parameters.json][virtualMachines-adc-parameters]

    - [desmachines virtuelles-adc-joindomain.parameters.json][virtualMachines-adc-joindomain-parameters]

    - [desmachines virtuelles-adds.parameters.json][virtualMachines-adds-parameters]

    - [virtualNetwork.parameters.json][virtualNetwork-parameters]

    - [virtualNetwork-ajoute-dns.parameters.json][virtualNetwork-adds-dns-parameters]

5. Modifiez le fichier de déploiement-ReferenceArchitecture.ps1 dans le dossier Scripts et modifiez la ligne suivante pour spécifier le groupe de ressources qui doit être créé ou utilisé pour stocker l’ordinateur virtuel et les ressources créées par le script :

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-onpremise-rg"
        ```

6. Modifiez les fichiers suivants dans le dossier Paramètres/locales et définissez la `resourceGroup` valeur dans le `virtualNetworkSettings` section dans chacun de ces fichiers est le même que que vous avez spécifié dans le fichier de script de déploiement-ReferenceArchitecture.ps1.

    - desmachines virtuelles-adds.parameters.json

    - desmachines virtuelles-adc.parameters.json

    - virtualNetwork.parameters.json

    - virtualNetwork-ajoute-dns.parameters.json

7. Ouvrez une fenêtre PowerShell d’Azure, déplacer vers le dossier de Scripts et exécutez la commande suivante :

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows onpremise
        ```

    Remplacer `<subscription id>` avec votre ID d’abonnement Azure.

    Pour `<location>`, spécifier une région Azure, tel que `eastus` ou `westus`.

### <a name="install-and-configure-the-azure-ad-connect-sync-service"></a>Installer et configurer le service de synchronisation d’Azure Connect de publicité

La configuration illustrée dans cette procédure se compose de deux instances du service de synchronisation d’Azure Connect d’Active Directory. La première est active, tandis que la seconde est configurée en mode intermédiaire pour fournir la haute disponibilité et basculement rapide en cas de défaillance du premier serveur.

1. Utilisation du portail Azure, accédez au groupe de ressources contenant les ordinateurs virtuels pour les services de synchronisation d’Azure AD Connect (*ra-DAS-locales-rg* par défaut) et se connecter à la machine virtuelle *vm1 du connecteur Active Directory locales ra DAS* . Connectez-vous en tant qu' *UtilisateurTest* avec mot de passe *AweS0me@PW*.

2. Télécharger Azure Connect d’annonce [ici][aad-connect-download].

3. Exécuter l’installateur Azure AD connecter et effectuer une installation à l’aide de l’option *Personnaliser* .

    >[AZURE.NOTE] Vous ne pouvez pas utiliser l’option *Paramètres Express* car la machine virtuelle qui héberge le service de synchronisation d’Azure Connect d’Active Directory n’est pas joint au domaine.

4. Sur la page *installer les composants requis* , rien dans les paramètres de configuration facultatifs pour accepter les options par défaut.

5. Sur la page de *connexion de l’utilisateur* , sélectionnez *Synchronisation de mot de passe*.

6. Sur la page de *connexion à Active Directory Azure* , entrez admin@ *myaadname*. onmicrosoft.com, où *myaadname* est le nom de votre client DAS. Entrez le mot de passe pour le compte d’administrateur.

7. Sur la page *se connecter vos répertoires* , spécifiez contoso.com pour la forêt (type la valeur, car il ne s’affiche pas dans la liste déroulante), entrez CONTOSO\testuser pour le nom d’utilisateur, spécifiez AweS0me@PW le mot de passe, puis cliquez sur *Ajouter un répertoire*.

8. Sur la page de *configuration de connexion AD Azure* , acceptez la valeur par défaut pour le nom d’utilisateur principal. Vérifiez *Continuer sans vérifié tous les domaines*.

    >[AZURE.NOTE] Le répertoire contoso.com sont répertoriés comme *Non vérifiée*. Pour vérifier un nom de domaine, vous devez créer un enregistrement TXT de votre registraire de nom de domaine. Dans cet exemple, le domaine local n’est pas enregistré en externe. Pour plus d’informations, consultez [Ajouter un nom de domaine personnalisé pour Azure Active Directory][aad-custom-directory].

9. Dans la page *filtrage des domaine et unité d’organisation* , sélectionnez *synchroniser tous les domaines et les unités d’organisation* (par défaut).

10. Sur la page *qui identifie de manière unique vos utilisateurs* , acceptez les valeurs par défaut.

11. Dans la page *filtrer les utilisateurs et les périphériques* , sélectionnez *synchroniser tous les utilisateurs et les périphériques* (par défaut).

12. Dans la page *Options* , sélectionnez *l’écriture différée de mot de passe*.

13. Dans la page *prêt à configurer* , sélectionnez *Démarrer le processus de synchronisation lors de la configuration est terminée*, mais laissez à *Activer le mode intermédiaire* désélectionnée.

14. Vérifiez que le processus de configuration se termine sans erreur et puis quitter le programme d’installation.

15. À partir du portail Azure, se connecter à la machine virtuelle *ra-DAS-locales-adc-ordinateur virtuel 2* . Connectez-vous en tant qu' *UtilisateurTest* avec mot de passe *AweS0me@PW*.

16. Téléchargez Azure AD Connect et puis exécutez le programme d’installation.

17. L’étape de l’Assistant et d’y répondre comme décrit dans les étapes 3 à 12.

18. Dans la page *prêt à configurer* , sélectionnez *Démarrer le processus de synchronisation lors de la configuration est terminée*et **également sélectionner** *Activer le mode de mise en attente*. Ainsi, le service de synchronisation Azure Connect de publicité à récupérer plus d’informations sur les comptes et les objets issus du domaine contoso.com et les stocker dans sa base de données, mais il ne transmet pas ces informations à vos clients de DAS.

14. Vérifiez que le processus de configuration se termine sans erreur et puis quitter le programme d’installation.

### <a name="test-the-aad-configuration"></a>Tester la configuration DAS

1. À l’aide du portail Azure, passez au répertoire DAS, ouvrir la lame Azure Active Directory, cliquez sur *utilisateurs et groupes*, puis cliquez sur *tous les utilisateurs* pour afficher la liste des utilisateurs et des groupes synchronisés avec l’annuaire. Vous devez voir les utilisateurs pour les comptes suivants :

    - administrateur (admin@ *myaadname*. onmicrosoft.com)

    - Compte de Service de synchronisation de répertoire local (Sync_ADC1_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

    - Compte de Service de synchronisation de répertoire local (Sync_ADC2_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

2. Dans le portail Azure, accédez au groupe de ressources contenant les ordinateurs virtuels pour les contrôleurs de domaine AD DS (*ra-DAS-locales-rg* par défaut) et se connecter à la machine virtuelle *ra-DAS-locales-ad-vm1* . Connectez-vous en tant qu' *UtilisateurTest* avec mot de passe *AweS0me@PW*.

3. À l’aide de la console *Active Directory utilisateurs et ordinateurs* , ajouter un nouvel utilisateur de domaine nommé Diane Tibbot, avec le nom d’ouverture de session dianet@contoso.com. Spécifiez un mot de passe de votre choix. Faire de l’utilisateur un membre du groupe Administrateurs local (il s’agit afin que vous pouvez vous connecter localement en tant que cet utilisateur ultérieurement, seuls les ordinateurs du domaine sont des contrôleurs de domaine).

4. Basculez vers l’ordinateur virtuel *vm1 du connecteur Active Directory locales ra DAS* , ouvrez une fenêtre PowerShell et exécutez les commandes suivantes :

        ```[powershell]
        Import-Module ADSync
        Start-ADSyncSyncCycle -PolicyType Delta
        ```

    Vérifiez que la commande retourne *de succès*.

    >[AZURE.NOTE] Cette étape est nécessaire car par défaut, le processus de synchronisation est planifié pour s’exécuter à des intervalles de 30 minutes. Ces commandes forcent une synchronisation ait lieu immédiatement.

5. Retourner au portail Azure, basculez vers le répertoire DAS, ouvrir la lame Azure Active Directory, cliquez sur *utilisateurs et groupes*, puis cliquez sur *tous les utilisateurs*. Vous devez maintenant voir Diane Tibbot (dianet@ *myaadname*. onmicrosoft.com) apparaissent dans la liste des utilisateurs.

6. De la lame Azure Active Directory, cliquez sur *Les Applications d’entreprise*, puis cliquez sur *toutes les applications*. Cliquez sur l’application *ContosoWebApp1* , puis cliquez sur *utilisateurs et groupes*. Dans la barre d’outils, cliquez sur *Ajouter*. Cliquez sur *utilisateurs et groupes*, puis sélectionnez les *Diane Tibbot*. Cliquez sur *attribuer*.

7. À l’aide d’Internet Explorer, naviguez vers le site https://account.activedirectory.windowsazure.com. Connectez-vous en tant que dianet@ *myaadname*. onmicrosoft.com avec le mot de passe que vous avez spécifié précédemment.

    >[AZURE.NOTE] Ne cliquez pas sur l’icône ContosoWebApp dans la liste des applications. DAS tente de trouver l’application web à l’adresse DNS répertoriée publiquement pour www.contoso.com, qui est différente de l’adresse de votre équilibreur de charge du niveau web.

8. Cliquez sur l’onglet *profil* . Les détails de l’utilisateur (si vous avez spécifié un lors de sa création) doivent s’afficher.

    >[AZURE.NOTE] Si vous cliquez sur *Modifier mot de passe*, vous n’êtes pas autorisé à effectuer cette tâche, comme cette opération doit tout d’abord être activée par un administrateur. Pour plus d’informations, consultez [mise en route avec la gestion de mot de passe][aad-password-management].

### <a name="enable-on-premises-users-to-run-the-application-by-using-authentication-through-aad"></a>Activer les utilisateurs pour exécuter l’application en utilisant l’authentification par le biais de DAS sur site

1. Retourner au contrôleur de domaine *ra-DAS-locales-ad-vm1* VM.

2. Ouvrez la console du *Gestionnaire DNS* et ajoutez un nouvel enregistrement d’hôte pour www.contoso.com. Spécifiez l’adresse IP publique de l’équilibreur de charge du niveau web.

3. Copiez le fichier *MyFakeRootCertificateAuthority.cer* à partir de l’ordinateur client de test (vous avez créé ces fichiers dans la procédure de [configuration de simulation d’un site web public](#simulate-configuration-of-a-public-web-site)
    
4. Ouvrez une invite de commande en tant qu’administrateur, déplacer vers le dossier contenant le fichier que vous venez de copier et exécutez la commande suivante :

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. À l’aide d’Internet Explorer, accédez à https://www.contoso.com. Vérifiez que la DAS-page de connexion de l’application web de ContosoWebApp1 s’affiche.

6. Connectez-vous en tant que dianet@ *myaadname*. onmicrosoft.com. L’application doit s’exécuter et vous connecter correctement.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les meilleures pratiques pour [l’extension de votre domaine local ajoute Azure][adds-extend-domain]

- Découvrez les meilleures pratiques pour la [Création d’une forêt de ressources ajoute] [ adds-resource-forest] dans Azure

<!-- links -->
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[aad-explained]: https://youtu.be/tj_0d4tR6aM
[aad-editions]: ../active-directory/active-directory-editions.md
[guidance-adds]: ./guidance-iaas-ra-secure-vnet-ad.md
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[azure-multifactor-authentication]: ../multi-factor-authentication/multi-factor-authentication.md
[aad-conditional-access]: ../active-directory//active-directory-conditional-access.md
[aad-dynamic-membership-rules]: ../active-directory/active-directory-accessmanagement-groups-with-advanced-rules.md
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-user-sign-in]: ../active-directory/active-directory-aadconnect-user-signin.md
[aad-sync-requirements]: ../active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md
[aad-topologies]: ../active-directory/active-directory-aadconnect-topologies.md
[aad-filtering]: ../active-directory/active-directory-aadconnectsync-configure-filtering.md
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-connect-sync-default-rules]: ../active-directory/active-directory-aadconnectsync-understanding-default-configuration.md
[aad-identity-protection]: ../active-directory/active-directory-identityprotection.md
[aad-password-management]: ../active-directory/active-directory-passwords-customize.md
[aad-application-proxy]: ../active-directory/active-directory-application-proxy-enable.md
[aad-connect-sync-operational-tasks]: ../active-directory/active-directory-aadconnectsync-operations.md#staging-mode
[aad-custom-domain]: ../active-directory/active-directory-add-domain.md
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-sync-disaster-recovery]: ../active-directory/active-directory-aadconnectsync-operations.md#disaster-recovery
[aad-sync-best-practices]: ../active-directory/active-directory-aadconnectsync-best-practices-changing-default-configuration.md
[aad-adfs]: ../active-directory/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs
[aad-health]: ../active-directory/active-directory-aadconnect-health-sync.md
[aad-health-adds]: ../active-directory/active-directory-aadconnect-health-adds.md
[aad-health-adfs]: ../active-directory/active-directory-aadconnect-health-adfs.md
[aad-agent-installation]: ../active-directory/active-directory-aadconnect-health-agent-install.md
[aad-reporting-guide]: ../active-directory/active-directory-reporting-guide.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-powershell-download]: ../powershell-install-configure.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/Deploy-ReferenceArchitecture.ps1
[vnet-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/webTier.parameters.json
[businesstier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/businessTier.parameters.json
[datatier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/dataTier.parameters.json
[managementtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/managementTier.parameters.json
[makecert]: https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/add-adds-domain-controller.parameters.json
[create-adds-forest-extension-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/create-adds-forest-extension.parameters.json
[virtualMachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adds.parameters.json
[virtualNetwork-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork.parameters.json
[virtualNetwork-adds-dns-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork-adds-dns.parameters.json
[virtualMachines-adc-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc.parameters.json
[virtualMachines-adc-joindomain-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc-joindomain.parameters.json
[aad-connect-download]: http://www.microsoft.com/download/details.aspx?id=47594
[aad-custom-directory]: ../active-directory/active-directory-add-domain.md
[aad-password-management]: ../active-directory/active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[0]: ./media/guidance-identity-aad/figure1.png "Architecture d’identité cloud à l’aide d’Azure Active Directory"
[1]: ./media/guidance-identity-aad/figure2.png "Une seule forêt, topologie du répertoire DAS unique"
[2]: ./media/guidance-identity-aad/figure3.png "Plusieurs forêts, seule topologie de répertoire DAS"
[4]: ./media/guidance-identity-aad/figure5.png "Topologie de serveur de mise en attente"
[5]: ./media/guidance-identity-aad/figure6.png "Topologie reposant sur plusieurs répertoires DAS"
[6]: ./media/guidance-identity-aad/figure7.png "Sélectionnant une installation personnalisée d’Azure Sync AD de se connecter à une instance spécifique de SQL Server"
[7]: ./media/guidance-identity-aad/figure8.png "Spécification de la méthode d’authentification pour la connexion de l’utilisateur"
[8]: ./media/guidance-identity-aad/figure9.png "Spécifiez le domaine et unité d’organisation options de filtrage"
[9]: ./media/guidance-identity-aad/figure10.png "L’activation différée de mot de passe"
[10]: ./media/guidance-identity-aad/figure11.png "La blade de gestion AD Azure dans le portail"
[11]: ./media/guidance-identity-aad/figure12.png "La console Azure Connect de publicité"
[12]: ./media/guidance-identity-aad/figure13.png "L’onglet opérations dans le Gestionnaire de Service de synchronisation."
[13]: ./media/guidance-identity-aad/figure14.png "L’onglet connecteurs dans le Gestionnaire de Service de synchronisation."
[14]: ./media/guidance-identity-aad/figure15.png "L’éditeur de règles de synchronisation"
[15]: ./media/guidance-identity-aad/figure16.png "La lame de fonctionnement Azure Active Directory se connecter dans le portail Azure indiquant l’état de synchronisation"
[16]: ./media/guidance-identity-aad/figure17.png "La lame de fonctionnement Azure Active Directory se connecter dans le portail Azure affichant la santé des services AD DS"
[17]: ./media/guidance-identity-aad/figure18.png "Rapports de sécurité disponibles sur le portail Azure"
[18]: ./media/guidance-identity-aad/figure19.png "Le portail Azure mise en surbrillance de l’adresse IP publique de l’équilibreur de charge du niveau web"
[19]: ./media/guidance-identity-aad/figure20.png "À l’aide d’Internet Explorer pour accéder à l’adresse IP publique de l’équilibreur de charge du niveau web"
[20]: ./media/guidance-identity-aad/figure21.png "Le composant logiciel enfichable Certificats affiche le certificat www.contoso.com"
[21]: ./media/guidance-identity-aad/figure22.png "Se connecter à la couche de gestion VM"
[22]: ./media/guidance-identity-aad/figure23.png "Création de la liaison HTTPS pour le site web par défaut"
[23]: ./media/guidance-identity-aad/figure24.png "Création de l’application web de ContosoWebApp1"
[24]: ./media/guidance-identity-aad/figure25.png "Définition des propriétés d’authentification de l’application web ContosoWebApp1"
[25]: ./media/guidance-identity-aad/figure26.png "L’ouverture de session Azure DAS à partir de l’application web ContosoWebApp1"
[26]: ./media/guidance-identity-aad/figure27.png "Modification du dossier du site web par défaut"