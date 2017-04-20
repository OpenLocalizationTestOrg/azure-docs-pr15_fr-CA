<properties
    pageTitle="Des Services de domaine Active Directory Azure : Comparer les domaine d’Azure AD Services aux contrôleurs de domaine DIY | Microsoft Azure"
    description="Comparaison d’Azure des Services de domaine Active Directory sur les contrôleurs de domaine personnalisé"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="maheshu"/>

# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Comment déterminer si les Services de domaine Active Directory de Azure est adaptée à votre cas d’utilisation
Les Services de domaine Active Directory Azure vous permet de déployer vos charges de travail dans les Services d’Infrastructure Azure, sans avoir à vous soucier de la maintenance de votre infrastructure d’identité. Ce service géré est différent d’un déploiement Active Directory de Windows Server classique que vous déployez et administrez sur votre propre. Le service est conçu pour la facilité de déploiement, le contrôle d’état automatique et mise à jour et une infrastructure d’identité simple pour le cloud. Nous sommes en constante évolution du service pour prendre en charge les scénarios de déploiement courants.

Pour décider si vous souhaitez utiliser les Services de domaine Active Directory de Azure ou de tourner et de gérer votre propre infrastructure AD (personnalisables) dans Azure :

- Consultez la liste des [fonctionnalités offertes par les Services de domaine Active Directory de Azure](active-directory-ds-features.md).

- Passez en revue les [scénarios de déploiement pour les Services de domaine Active Directory de Azure](active-directory-ds-scenarios.md)courants.

- Enfin, [comparer les Services de domaine Active Directory de Azure à une option d’annonce personnalisée](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).


## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Comparer les Services de domaine Active Directory de Azure au domaine AD DIY dans Azure
Le tableau suivant vous permet de choisir entre l’utilisation des Services de domaine Active Directory de Azure et gérer votre propre infrastructure AD dans Azure.

|**Fonctionnalité**|**Services de domaine d’Active Directory Azure**|**'Personnalisé' AD dans Azure VM**|
|---|:---:|:---:|
|[**Service géré**](active-directory-ds-comparison.md#managed-service)|**& #x 2713 ;**|**& #x 2715 ;**|
|[**Sécuriser les déploiements**](active-directory-ds-comparison.md#secure-deployments)|**& #x 2713 ;**|Administrateur doit sécuriser le déploiement.|
|[**Serveur DNS**](active-directory-ds-comparison.md#dns-server)|**& #x 2713 ;** (service géré)|**& #x 2713 ;**|
|[**Privilèges d’administrateur de domaine ou d’entreprise**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|**& #x 2715 ;**|**& #x 2713 ;**|
|[**Jonction de domaine**](active-directory-ds-comparison.md#domain-join)|**& #x 2713 ;**|**& #x 2713 ;**|
|[**Authentification de domaine à l’aide de NTLM et Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|**& #x 2713 ;**|**& #x 2713 ;**|
|[**Structure d’unité d’organisation personnalisée**](active-directory-ds-comparison.md#custom-ou-structure)|**& #x 2713 ;**|**& #x 2713 ;**|
|[**Extensions de schéma**](active-directory-ds-comparison.md#schema-extensions)|**& #x 2715 ;**|**& #x 2713 ;**|
|[**Approbations de domaine/forêt AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|**& #x 2715 ;**|**& #x 2713 ;**|
|[**Lecture LDAP**](active-directory-ds-comparison.md#ldap-read)|**& #x 2713 ;**|**& #x 2713 ;**|
|[**LDAP sécurisé (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|**& #x 2713 ;**|**& #x 2713 ;**|
|[**Écriture LDAP**](active-directory-ds-comparison.md#ldap-write)|**& #x 2715 ;**|**& #x 2713 ;**|
|[**Stratégie de groupe**](active-directory-ds-comparison.md#group-policy)|Simple|Complet|
|[**Déploiements distribués géographiquement**](active-directory-ds-comparison.md#geo-dispersed-deployments)|**& #x 2715 ;**|**& #x 2713 ;**|


#### <a name="managed-service"></a>Service géré
Les domaines des Services de domaine Active Directory Azure sont gérés par Microsoft. Vous n’avez pas à vous soucier des correctifs, mises à jour, de surveillance, les sauvegardes et garantir la disponibilité de votre domaine. Ces tâches de gestion sont proposées en tant que service par Microsoft Azure pour vos domaines gérés.

#### <a name="secure-deployments"></a>Sécuriser les déploiements
Le domaine géré est verrouillé de façon sécurisée par meilleures pratiques de sécurité de Microsoft pour les déploiements d’Active Directory. Ces meilleures pratiques proviennent des dizaines d’années de l’équipe produit AD d’expérience d’ingénierie et de prise en charge des déploiements d’Active Directory. Pour les déploiements de bricolage, vous devez prendre des étapes de déploiement spécifiques de verrouillage sécurisé vers le bas de votre déploiement.

#### <a name="dns-server"></a>Serveur DNS
Un domaine géré de Services de domaine Active Directory de Azure inclut les services DNS gérés. Les membres du groupe « Administrateurs de contrôleur de domaine DAS » peuvent gérer le système DNS sur le domaine géré. Les membres de ce groupe bénéficient des privilèges d’Administration DNS complets pour le domaine géré. Gestion du service DNS peut être effectuée à l’aide de la « console d’Administration de DNS' incluse dans le package Outils d’Administration de serveur distant (RSAT).

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilèges d’administrateur de domaine ou entreprise
Ces privilèges ne sont pas proposées sur un domaine géré DAS-DS. Applications nécessitant ces privilèges élevés pour être installé/exécuter ne peut pas être exécutées sur les domaines gérés. Un sous-ensemble plus petit de privilèges d’administration est disponible pour les membres du groupe d’administration déléguée appelée « Administrateurs de contrôleur de domaine DAS ». Ces privilèges incluent des privilèges pour configurer le service DNS, de configurer la stratégie de groupe, obtenir des privilèges d’administrateur sur les ordinateurs joints au domaine etc..

#### <a name="domain-join"></a>Jonction de domaine
Vous pouvez joindre des ordinateurs virtuels au domaine géré semblable à comment vous joignez des ordinateurs à un domaine Active Directory.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Authentification de domaine à l’aide de NTLM et Kerberos
Avec les Services de domaine Active Directory de Azure, vous pouvez utiliser vos informations d’identification d’entreprise se pour authentifier avec le domaine géré. Informations d’identification sont synchronisées avec vos clients AD Azure. Pour les locataires synchronisées, Azure Connect d’Active Directory garantit que les modifications apportées aux informations d’identification effectuées sur site sont synchronisées avec Active Directory Azure. Avec une installation de domaine personnalisé, vous devrez peut-être configurer une relation d’approbation de domaine avec une forêt de comptes locaux pour les utilisateurs s’authentifient avec leurs informations d’identification d’entreprise. Vous devrez également configurer la réplication Active Directory pour vous assurer que les mots de passe utilisateur synchronisent à vos machines virtuelles de contrôleur domaine Azure.

#### <a name="custom-ou-structure"></a>Structure d’unité d’organisation personnalisée
Les membres du groupe « Administrateurs de contrôleur de domaine DAS » peuvent créer des unités d’organisation personnalisées dans le domaine géré.

#### <a name="schema-extensions"></a>Extensions de schéma
Vous ne pouvez pas étendre le schéma de base d’un domaine géré de Services de domaine Active Directory de Azure. Par conséquent, les applications qui reposent sur les extensions de schéma d’Active Directory (par exemple, de nouveaux attributs dans l’objet utilisateur) ne peut pas être levées et déplacées vers les domaines DAS-DS.

#### <a name="ad-domain-or-forest-trusts"></a>Les approbations de forêt ou de domaine AD
Domaines gérés ne peut pas être configurés pour définir des relations d’approbation (entrant et sortant) avec d’autres domaines. Par conséquent, les scénarios de déploiements de forêt ressource ou si vous préférez ne pas à synchroniser les mots de passe Active Directory Azure ne peut pas utiliser les Services de domaine Active Directory de Azure.

#### <a name="ldap-read"></a>Lecture LDAP
Le domaine géré prend en charge les charges de travail de lecture LDAP. Par conséquent, vous pouvez déployer des applications qui effectuent des opérations de lecture LDAP contre le domaine géré.

#### <a name="secure-ldap"></a>LDAP sécurisé
Vous pouvez configurer les Services de domaine Active Directory de Azure pour sécuriser l’accès LDAP à votre domaine géré, y compris sur internet.

#### <a name="ldap-write"></a>Écriture LDAP
Le domaine géré est en lecture seule pour les objets utilisateur. Par conséquent, les applications qui effectuent des opérations d’écriture LDAP par rapport aux attributs de l’objet utilisateur ne fonctionnent pas dans un domaine géré. En outre, les mots de passe utilisateur ne peuvent pas être modifiées dans le domaine géré. Un autre exemple serait une modification des appartenances à un groupe ou les attributs d’un groupe dans le domaine géré, ce qui n’est pas autorisé. Toutefois, les modifications aux attributs de l’utilisateur ou des mots de passe dans Azure AD (via le portail de PowerShell/Azure) ou Active Directory sont synchronisés avec le domaine géré DAS-DS sur site.

#### <a name="group-policy"></a>Stratégie de groupe
Constructions de stratégie de groupe complexes ne sont pas pris en charge sur le domaine géré DAS-DS. Par exemple, vous ne peut pas créer et déployer des objets stratégie de groupe distinct pour chaque unité d’organisation personnalisée dans le domaine ou utiliser le filtrage pour le ciblage de GP WMI. Il existe un objet GPO intégré chaque pour les conteneurs 'Ordinateurs AADDC' et 'AADDC ' utilisateurs, qui peuvent être personnalisés pour configurer la stratégie de groupe.

#### <a name="geo-dispersed-deployments"></a>Déploiements de dispersés géographiquement
Azure domaines gérés de Services de domaine Active Directory sont disponibles dans un seul réseau virtuel dans Azure. Pour les scénarios qui requièrent des contrôleurs de domaine disponibles dans plusieurs régions Azure dans le monde entier, la configuration de contrôleurs de domaine dans Azure IaaS VM peut être la meilleure solution.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Options de déploiement (soi-même) AD 'Personnalisé'
Dans certains cas, utiliser-déploiement où vous devez certaines des capacités offertes par une installation de Windows Server Active Directory. Dans ce cas, envisagez l’une des options personnalisables (soi-même) suivantes :

- **Domaine de nuage autonome :** Vous pouvez définir une autonome 'domaine de nuage » à l’aide de machines virtuelles Azure qui ont été configurés comme contrôleurs de domaine. Cette infrastructure n’est pas intégré à vos locaux environnement Active Directory. Cette option nécessite un ensemble différent de « informations d’identification du nuage » à l’ouverture de session/administrer des ordinateurs virtuels dans le nuage.

- **De déploiement de ressources forêt :** Vous pouvez configurer un domaine dans la topologie de forêt ressource, à l’aide d’Azure ordinateurs virtuels configurés comme contrôleurs de domaine. Ensuite, vous pouvez configurer une relation d’approbation Active Directory avec vos locaux environnement Active Directory. Ordinateurs de domaine-jointure (Azure VM), vous pouvez à cette forêt de la ressource dans le nuage. Authentification de l’utilisateur qui produit sur une une connexion VPN/ExpressRoute dans votre répertoire local.

- **Étendre votre domaine local vers Azure :** Un réseau virtuel Azure peuvent se connecter à votre réseau local à l’aide d’une connexion VPN/ExpressRoute, afin que Azure VM peut être jointe à votre site dans Active Directory. Une autre solution est de promouvoir des contrôleurs de domaine de réplica de votre domaine local dans Azure sous la forme d’une machine virtuelle. Vous pouvez ensuite configurer pour qu’il réplique sur une connexion VPN/ExpressRoute dans votre répertoire local. Ce mode de déploiement étend efficacement votre domaine local vers Azure.

> [AZURE.NOTE] Vous pouvez déterminer qu’une option DIY est mieux adaptée pour votre déploiement utilisations. Envisager de [partager des commentaires](active-directory-ds-contact-us.md) pour nous aider à comprendre ce qui permettraient de fonctionnalités vous avez choisi à l’avenir des Services de domaine Active Directory de Azure. Vos commentaires nous permet de faire évoluer le service pour mieux répondre aux besoins de votre déploiement et cas d’usage.

Nous avons publié des [instructions pour le déploiement de Windows Server Active Directory Azure machines virtuelles en fonctionnement](https://msdn.microsoft.com/library/azure/jj156090.aspx) pour faciliter les installations DIY.


## <a name="related-content"></a>Contenu associé
- [Fonctionnalités - des Services Azure domaine d’Active Directory](active-directory-ds-features.md)

- [Scénarios de déploiement - Services de domaine Active Directory de Azure](active-directory-ds-scenarios.md)

- [Instructions pour le déploiement de Windows Server Active Directory sur les ordinateurs virtuels Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
