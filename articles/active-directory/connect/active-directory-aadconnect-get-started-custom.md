<properties
    pageTitle="Azure AD Connect : Installation personnalisée | Microsoft Azure"
    description="Ce document détaille les options d’installation personnalisée pour Azure Connect d’Active Directory. Utilisez ces instructions pour installer Active Directory via Azure Connect d’Active Directory."
    services="active-directory"
    keywords="Nouveautés Azure Connect d’Active Directory, installez Active Directory, les composants requis pour Azure AD"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="billmath"/>

# <a name="custom-installation-of-azure-ad-connect"></a>Installation personnalisée de Azure Connect de publicité
De connexion AD Azure **paramètres personnalisés** est utilisée lorsque vous souhaitez davantage d’options pour l’installation. Il est utilisé si vous avez plusieurs forêts, ou si vous souhaitez configurer les fonctionnalités facultatives non couvertes dans l’installation express. Il est utilisé dans tous les cas où l’option [**d’installation expresse**](active-directory-aadconnect-get-started-express.md) ne satisfait pas votre topologie ou déploiement.

Avant de commencer l’installation Azure Connect d’Active Directory, assurez-vous que [Télécharger Azure Connect de publicité](http://go.microsoft.com/fwlink/?LinkId=615771) et les étapes préalable complète [Azure AD Connect : matériel et conditions préalables](../active-directory-aadconnect-prerequisites.md). Assurez-vous également que vous avez requis comptes disponibles, comme indiqué dans [les comptes Azure AD Connect et des autorisations](active-directory-aadconnect-accounts-permissions.md).

Si des paramètres personnalisés ne correspond pas à votre topologie, par exemple pour la mise à niveau de la synchronisation d’annuaire, consultez la [documentation associée](#related-documentation) pour d’autres scénarios.

## <a name="custom-settings-installation-of-azure-ad-connect"></a>Installation des paramètres personnalisés d’Azure Connect de publicité

### <a name="express-settings"></a>Paramètres Express
Sur cette page, cliquez sur **Personnaliser** pour démarrer une installation de paramètres personnalisés.

### <a name="install-required-components"></a>Installer les composants requis
Lorsque vous installez les services de synchronisation, vous pouvez laisser la section de configuration facultatives non contrôlé et Azure Connect d’Active Directory définit tous les éléments automatiquement. Il configure une instance de SQL Server 2012 Express LocalDB, créez les groupes appropriés et attribuer des autorisations. Si vous souhaitez modifier les paramètres par défaut, vous pouvez utiliser le tableau suivant pour comprendre les options de configuration facultatives sont disponibles.

![Composants requis](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)

Configuration facultative  | Description
------------- | -------------
Utilisez un SQL Server existant | Vous permet de spécifier le nom de SQL Server et le nom de l’instance. Choisissez cette option si vous avez déjà un serveur de base de données que vous souhaitez utiliser. Entrez le nom d’instance suivi d’un numéro de port et les virgules dans le **Nom de l’Instance** si votre SQL Server n’a pas activé la navigation.
Utiliser un compte de service existant | Par défaut, Azure Connect d’Active Directory crée un compte de service local pour les services de synchronisation à utiliser. Le mot de passe est généré automatiquement et inconnues à la personne qui installe Azure Connect d’Active Directory. Si vous utilisez un serveur SQL distant ou un serveur proxy qui requiert une authentification, vous avez besoin d’un service dans le domaine de compte et de connaître le mot de passe. Dans ce cas, entrez le compte de service à utiliser. Assurez-vous que l’utilisateur qui exécute l’installation est une association de sécurité dans SQL pour une connexion d’accès pour le compte de service peut être créée. Reportez-vous à la section [comptes d’Azure AD Connect et des autorisations](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation)
Spécifier les groupes de synchronisation personnalisés | Par défaut Azure Connect d’Active Directory crée quatre groupes locaux sur le serveur lorsque les services de synchronisation sont installés. Ces groupes sont : groupe Administrateurs, opérateurs groupe, groupe de navigation et le groupe de réinitialisation de mot de passe. Vous pouvez spécifier vos propres groupes ici. Les groupes doivent être locales sur le serveur et ne peut pas être situés dans le domaine.

### <a name="user-sign-in"></a>Connexion de l’utilisateur
Après avoir installé les composants requis, vous êtes invité à sélectionner vos utilisateurs unique méthode d’authentification. Le tableau suivant fournit une brève description des options disponibles. Pour une description complète des méthodes de connexion, reportez-vous à la section [authentification de l’utilisateur](../active-directory-aadconnect-user-signin.md).

![Ouverture de session utilisateur](./media/active-directory-aadconnect-get-started-custom/usersignin.png)

Option d’ouverture de session unique | Description
------------- | -------------
Synchronisation de mot de passe | Les utilisateurs peuvent se connecter aux services de cloud de Microsoft, comme Office 365, en utilisant le même mot de passe qu’ils utilisent dans leur réseau local. Les mots de passe des utilisateurs sont synchronisés à AD Azure sous la forme d’un hachage de mot de passe et l’authentification s’effectue dans le nuage. Pour plus d’informations, reportez-vous à la section [synchronisation de mot de passe](../active-directory-aadconnectsync-implement-password-synchronization.md) .
Fédération avec AD FS | Les utilisateurs peuvent se connecter aux services de cloud de Microsoft, comme Office 365, en utilisant le même mot de passe qu’ils utilisent dans leur réseau local.  Les utilisateurs sont redirigés vers leur site AD FS instance pour vous connecter et l’authentification s’effectue en local.
Ne configurez pas | Ni la fonctionnalité est installée et configurée. Choisissez cette option si vous avez déjà un serveur de fédération 3ème partie ou une autre solution existante en place.

### <a name="connect-to-azure-ad"></a>Connexion à Active Directory Azure
Sur la connexion à l’écran d’annonce d’Azure, entrez un compte d’administrateur global et d’un mot de passe. Si vous avez sélectionné la **fédération avec AD FS** à la page précédente, ne vous connectez pas avec un compte dans un domaine que vous souhaitez activer pour la fédération. Une recommandation est d’utiliser un compte dans le domaine par défaut **onmicrosoft.com** , qui est fourni avec votre annuaire AD Azure.

Ce compte est utilisé uniquement pour créer un compte de service dans Active Directory Azure et n’est pas utilisé une fois l’Assistant terminé.  
![Ouverture de session utilisateur](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

Si votre compte d’administrateur global a AMF activé, vous devez fournir le mot de passe à nouveau dans la fenêtre connexion contextuelle et terminer le défi de l’AMF. Le défi pourrait être un fournissant un code de vérification ou d’un appel téléphonique.  
![Connexion utilisateur AMF](./media/active-directory-aadconnect-get-started-custom/connectaadmfa.png)

Le compte de l’administrateur global peut également avoir [Privilégié la gestion des identités](../active-directory-privileged-identity-management-getting-started.md) est activée.

Si vous recevez un message d’erreur et que vous avez des problèmes de connectivité, consultez [résoudre les problèmes de connectivité](../active-directory-aadconnect-troubleshoot-connectivity.md).

## <a name="pages-under-the-section-sync"></a>Pages dans la section synchronisation

### <a name="connect-your-directories"></a>Connecter vos répertoires
Pour vous connecter à votre Service de domaine Active Directory, Azure Connect de publicité doit les informations d’identification d’un compte disposant des autorisations suffisantes. Vous pouvez saisir la partie domaine au format NetBios ou nom de domaine complet, c'est-à-dire que FABRIKAM\syncuser ou fabrikam.com\syncuser. Ce compte peut être un compte d’utilisateur normal, car il doit uniquement les autorisations de lecture par défaut. Toutefois, en fonction de votre scénario, vous devrez peut-être plus d’autorisations. Pour plus d’informations, voir [comptes de connexion AD Azure et des autorisations](../active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account)

![Connecter l’annuaire](./media/active-directory-aadconnect-get-started-custom/connectdir.png)

### <a name="azure-ad-sign-in-configuration"></a>Configuration d’authentification AD Azure
Cette page vous permet de consulter l’UPN de domaines existants dans les locaux AD DS et qui ont été vérifiés dans Azure AD. Cette page vous permet également de configurer l’attribut à utiliser pour l’userPrincipalName.

![Domaines non vérifiés](./media/active-directory-aadconnect-get-started-custom/aadsigninconfig.png)  
Passez en revue chaque domaine marquée **N’a pas ajouté** et **N’a pas vérifié**. Vérifiez que ces domaines que vous utilisez ont été vérifiés dans Azure AD. Lorsque vous avez vérifié vos domaines, cliquez sur le symbole d’actualisation. Pour plus d’informations, consultez la rubrique [Ajouter et le domaine](../active-directory-add-domain.md)

**UserPrincipalName** - l’attribut userPrincipalName est que les utilisateurs de l’attribut utilisent lorsqu’ils se connectent à Active Directory Azure et Office 365. Les domaines utilisés, également connu sous le nom-suffixe UPN, doivent être vérifiées dans Azure annonce avant que les utilisateurs sont synchronisés. Microsoft vous recommande de conserver l’attribut userPrincipalName par défaut. Si cet attribut n’est pas routable et ne peut pas être vérifié, il est possible de sélectionner un autre attribut. Vous pouvez par exemple sélectionner courrier électronique comme l’attribut contenant l’ID de connexion. **Autre ID**à l’aide d’un autre attribut à userPrincipalName est appelée. La valeur de l’attribut autre ID doit suivre la norme RFC822. Autre ID utilisable avec la synchronisation de mot de passe et de fédération.

>[AZURE.WARNING]
À l’aide d’un autre ID n’est pas compatible avec toutes les charges de travail Office 365. Pour plus d’informations, reportez-vous à [Configuration d’un autre ID de connexion](https://technet.microsoft.com/library/dn659436.aspx).

### <a name="domain-and-ou-filtering"></a>Filtrage de l’unité d’organisation et de domaine
Par défaut, tous les domaines et les unités d’organisation sont synchronisées. S’il existe certains domaines ou des unités d’organisation que vous ne souhaitez pas synchroniser à AD Azure, vous pouvez désélectionner ces domaines et les unités d’organisation.  
![Filtrage de DomainOU](./media/active-directory-aadconnect-get-started-custom/domainoufiltering.png) cette page de l’Assistant consiste à configurer le filtrage basé sur le domaine. Pour plus d’informations, consultez [filtrage basé sur le domaine](../active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering).

Il est également possible que certains domaines ne sont pas accessibles en raison de restrictions de pare-feu. Ces domaines sont sélectionnés par défaut et ont un avertissement.  
![Domaines inaccessibles](./media/active-directory-aadconnect-get-started-custom/unreachable.png)  
Si vous voyez cet avertissement, assurez-vous que ces domaines sont en effet inaccessibles et l’avertissement est normal.

### <a name="uniquely-identifying-your-users"></a>Identifiant de manière unique vos utilisateurs
La mise en correspondance entre la fonctionnalité des forêts vous permet de définir comment les utilisateurs de vos forêts AD DS sont représentés dans Active Directory Azure. Un utilisateur peut soit être représenté qu’une seule fois dans toutes les forêts, ou une combinaison de comptes activés et désactivés. L’utilisateur peut également être représenté en tant que contact dans des forêts.

![Unique](./media/active-directory-aadconnect-get-started-custom/unique.png)

Paramètre | Description
------------- | -------------
[Les utilisateurs sont représentés uniquement de fois dans toutes les forêts](../active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) | Tous les utilisateurs sont créés en tant qu’objets individuels dans Azure AD. Les objets ne sont pas jointes dans le métaverse.
[Attribut de messagerie](../active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) | Cette option relie les utilisateurs et les contacts si l’attribut mail a la même valeur dans des forêts différentes. Utilisez cette option lorsque vos contacts ont été créés à l’aide de GALSync.
[ObjectSID et msExchangeMasterAccountSID / msRTCSIP-OriginatorSid](../active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest) | Cette option permet de joindre un utilisateur activé dans une forêt de compte avec un utilisateur désactivé dans une forêt de ressources. Dans Exchange, cette configuration est connue sous la forme d’une boîte aux lettres liée. Cette option peut également être utilisée si vous n’utilisez Lync et Exchange n’est pas présent dans la forêt de ressources.
sAMAccountName et le MailNickName | Cette option de jointures sur les attributs, où il est prévu de que l’ID d’ouverture de session de l’utilisateur sont accessibles.
Un attribut spécifique | Cette option vous permet de sélectionner votre propre attribut. **Limite :** Veillez à choisir un attribut qui sont déjà disponibles dans le métaverse. Si vous sélectionnez un attribut personnalisé (et non dans le métaverse), il est impossible de terminer l’Assistant.

**Ancre de source** - sourceAnchor de l’attribut est un attribut qui est immuable pendant la durée de vie d’un objet utilisateur. Il s’agit de la clé primaire, la liaison de l’utilisateur local de l’utilisateur dans Active Directory Azure. Étant donné que l’attribut ne peut pas être modifié, vous devez prévoir un bon attribut à utiliser. Un bon candidat est l’objectGUID. Cet attribut n’est pas modifié, à moins que le compte d’utilisateur est déplacé entre les forêts/domaines. Dans un environnement à plusieurs forêts où vous déplacez des comptes entre les forêts, un autre attribut doit être utilisé, par exemple un attribut dont le n° employé. Évitez les attributs changer lorsqu’une personne phares ou modifier les affectations. Vous ne pouvez pas utiliser des attributs avec un @-sign, pour e-mail et userPrincipalName ne peut pas être utilisés. L’attribut respecte la casse lorsque vous déplacez un objet entre des forêts, assurez-vous donc de conserver les majuscules/minuscules. Attributs binaires sont codées en base64, mais les autres types d’attribut restent dans son état non codée. Dans les scénarios de fédération et certaines interfaces AD Azure, cet attribut est également connu sous le nom immutableID. Vous trouverez plus d’informations sur le point d’ancrage de la source dans les [concepts de conception](../active-directory-aadconnect-design-concepts.md#sourceAnchor).

### <a name="sync-filtering-based-on-groups"></a>Filtrage de synchronisation basé sur les groupes
Le filtrage sur la fonctionnalité des groupes vous permet de synchroniser uniquement un petit sous-ensemble des objets pour un projet pilote. Pour utiliser cette fonctionnalité, créez un groupe pour ce rôle dans Active Directory sur site. Puis ajouter les utilisateurs et les groupes qui doivent être synchronisées pour Azure AD en tant que membres directs. Vous pouvez ensuite ajouter et supprimer des utilisateurs à ce groupe pour gérer la liste des objets qui doivent être présents dans Azure AD. Tous les objets que vous souhaitez synchroniser doivent être un membre du groupe direct. Utilisateurs, groupes, contacts et ordinateurs/périphériques doivent être des membres directs. Appartenance à un groupe imbriqué n’est pas résolu. Lorsque vous ajoutez un groupe comme un membre, seul le groupe lui-même est ajouté et non à ses membres.

![Filtrage de la synchronisation](./media/active-directory-aadconnect-get-started-custom/filter2.png)

>[AZURE.WARNING]
Cette fonctionnalité est uniquement destinée à prendre en charge un déploiement pilote. Ne l’utilisez pas dans un déploiement de production complet.

Dans un déploiement de production complet, il va être difficile de maintenir un groupe unique avec tous les objets à synchroniser. Vous devez plutôt utiliser une des méthodes dans [configurer le filtrage](../active-directory-aadconnectsync-configure-filtering.md).

### <a name="optional-features"></a>Fonctionnalités facultatives
Cet écran vous permet de sélectionner les fonctionnalités facultatives à vos scénarios spécifiques.

![Fonctionnalités facultatives](./media/active-directory-aadconnect-get-started-custom/optional.png)

>[AZURE.WARNING]
Si vous avez actuellement la synchronisation d’annuaire ou Azure AD Sync active, n’activez pas les fonctionnalités d’écriture différée dans Azure Connect d’Active Directory.

Fonctionnalités facultatives | Description
------------------- | -------------
Déploiement hybride d’Exchange | La fonctionnalité de déploiement hybride d’Exchange permet la coexistence de boîtes aux lettres Exchange à la fois sur site et dans Office 365. Azure AD connexion synchronise un ensemble spécifique d' [attributs](../active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) d’Active Directory Azure dans votre répertoire local.
Application de publicité Azure et le filtrage de l’attribut | En activant Azure AD app et le filtrage de l’attribut, l’ensemble des attributs synchronisés peut être adapté. Cette option ajoute deux pages de configuration supplémentaires à l’Assistant. Pour plus d’informations, consultez [Azure AD app et le filtrage de l’attribut](#azure-ad-app-and-attribute-filtering).
Synchronisation de mot de passe | Si vous avez sélectionné la fédération que la solution d’ouverture de session, vous pouvez activer cette option. Synchronisation de mot de passe peut ensuite être utilisée comme une option de sauvegarde. Pour plus d’informations, consultez [synchronisation de mot de passe](../active-directory-aadconnectsync-implement-password-synchronization.md).
Écriture différée de mot de passe | En activant l’écriture différée de mot de passe, les modifications de mot de passe qui proviennent AD Azure sont réécrites dans votre répertoire local. Pour plus d’informations, consultez [mise en route avec la gestion de mot de passe](../active-directory-passwords-getting-started.md).
Écriture différée du groupe | Si vous utilisez la fonctionnalité de **Groupes d’Office 365** , vous pouvez avoir ces groupes représentés dans Active Directory sur site. Cette option n’est disponible que si vous avez Exchange présents dans vos locaux d’Active Directory. Pour plus d’informations, consultez [écriture différée du groupe](../active-directory-aadconnect-feature-preview.md#group-writeback).
Écriture différée de périphérique | Permet aux objets de périphérique de d’écriture différée dans Azure annonce sur site Active Directory pour les scénarios d’accès conditionnel. Pour plus d’informations, consultez [activation écriture différée de dispositif dans Azure Connect d’AD](../active-directory-aadconnect-feature-device-writeback.md).
Synchronisation d’annuaire extension attribut | En activant la synchronisation d’annuaire extensions attribut, attributs spécifiés sont synchronisées dans AD Azure. Pour plus d’informations, consultez [extensions du répertoire](../active-directory-aadconnectsync-feature-directory-extensions.md).

### <a name="azure-ad-app-and-attribute-filtering"></a>Application de publicité Azure et le filtrage de l’attribut
Si vous souhaitez limiter les attributs à synchroniser pour Azure AD, puis démarrer en sélectionnant les services que vous utilisez. Si vous apportez des modifications de la configuration de cette page, un nouveau service doit être activée explicitement en relançant l’Assistant d’installation.

![Applications de fonctionnalités facultatives](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

Basé sur les services, sélectionnés à l’étape précédente, cette page affiche tous les attributs qui sont synchronisés. Cette liste est une combinaison de tous les types d’objet en cours de synchronisation. S’il y a certains attributs particuliers, que vous avez besoin de ne pas synchroniser, vous pouvez désélectionner les attributs.

![Attributs de fonctionnalités facultatives](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

>[AZURE.WARNING]
Suppression d’attributs peut avoir un impact sur la fonctionnalité. Pour les meilleures pratiques et des recommandations, consultez [attributs synchronisés](../active-directory-aadconnectsync-attributes-synchronized.md#attributes-to-synchronize).

### <a name="directory-extension-attribute-sync"></a>Synchronisation d’annuaire Extension attribut
Vous pouvez étendre le schéma dans Active Directory Azure avec des attributs personnalisés ajoutés par votre organisation ou d’autres attributs dans Active Directory. Pour utiliser cette fonctionnalité, sélectionnez **synchronisation d’attribut Extension de répertoire** sur la page **Fonctionnalités facultatives** . Vous pouvez sélectionner plusieurs attributs à synchroniser sur cette page.

![Extensions de répertoire](./media/active-directory-aadconnect-get-started-custom/extension2.png)

Pour plus d’informations, consultez [extensions du répertoire](../active-directory-aadconnectsync-feature-directory-extensions.md).

## <a name="configuring-federation-with-ad-fs"></a>Configurer la fédération avec AD FS
Configuration d’ADFS avec Azure Connect de publicité est simple, avec seulement quelques clics. Le texte suivant est requise avant la configuration.

- Un serveur Windows Server 2012 R2 pour le serveur de fédération avec la gestion à distance activé
- Un serveur Windows Server 2012 R2 pour le serveur Proxy de l’Application Web avec la gestion à distance activé
- Un certificat SSL pour le nom de service de fédération que vous projetez d’utiliser (par exemple, sts.contoso.com)

### <a name="ad-fs-configuration-pre-requisites"></a>AD FS configuration conditions préalables
Pour configurer votre batterie de serveurs AD FS à l’aide d’Azure Connect d’Active Directory, assurez-vous que WinRM est activé sur les serveurs distants. De plus, passez en revue l’exigence de ports répertorié dans le [tableau 3 - Azure Connect d’Active Directory et les serveurs de fédération/WAP](../active-directory-aadconnect-ports.md#table-3---azure-ad-connect-and-federation-serverswap).

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Créer une nouvelle batterie de serveurs ADFS ou utiliser une batterie de serveurs existante AD FS
Vous pouvez utiliser une batterie AD FS existante, ou vous pouvez choisir de créer une nouvelle batterie de serveurs ADFS. Si vous choisissez de créer un nouveau, vous devez fournir le certificat SSL. Si le certificat SSL est protégé par un mot de passe, vous êtes invité pour le mot de passe.

![Batterie de serveurs AD FS](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

Si vous choisissez d’utiliser une batterie de serveurs existante AD FS, vous accédez directement à la configuration de la relation d’approbation entre un écran AD FS et Azure AD.

### <a name="specify-the-ad-fs-servers"></a>Spécifiez les serveurs ADFS
Entrez les serveurs que vous souhaitez installer AD FS sur. Vous pouvez ajouter un ou plusieurs serveurs en fonction des besoins de planification de la capacité. Joindre à tous les serveurs Active Directory avant d’effectuer cette configuration. Microsoft vous recommande d’installer un serveur ADFS unique pour les déploiements pilote et test. Ajoutez ensuite et déployer des serveurs supplémentaires afin de répondre à vos besoins de mise à l’échelle en exécutant Azure Connect d’Active Directory après la configuration initiale.

>[AZURE.NOTE]
Assurez-vous que tous vos serveurs sont joints à un domaine Active Directory avant de procéder à cette configuration.

![Serveurs AD FS](./media/active-directory-aadconnect-get-started-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Spécifiez les serveurs Proxy de l’Application Web
Entrez les serveurs que vous souhaitez que vos serveurs de proxy d’Application Web. Le serveur de proxy d’application web est déployé dans votre zone DMZ (accès extranet) et prend en charge les demandes d’authentification à partir de l’extranet. Vous pouvez ajouter un ou plusieurs serveurs en fonction des besoins de planification de la capacité. Microsoft vous recommande d’installer un serveur de proxy Web application unique pour les déploiements pilote et test. Ajoutez ensuite et déployer des serveurs supplémentaires afin de répondre à vos besoins de mise à l’échelle en exécutant Azure Connect d’Active Directory après la configuration initiale. Nous vous recommandons d’avoir un nombre équivalent de serveurs proxy pour répondre à l’authentification à partir de l’intranet.

>[AZURE.NOTE]
<li> Si le compte que vous utilisez n’est pas un administrateur local sur les serveurs ADFS, vous êtes invité pour les informations d’identification d’administration.</li>
<li> Vérifiez qu’il existe une connectivité HTTP/HTTPS entre le serveur Azure Connect d’Active Directory et le serveur Proxy de l’Application Web avant d’exécuter cette étape.</li>
<li> Vérifiez qu’il existe une connectivité HTTP/HTTPS entre le serveur d’Application Web et le serveur AD FS pour permettre le flux par le biais des demandes d’authentification.</li>

![Application Web](./media/active-directory-aadconnect-get-started-custom/adfs3.png)

Vous êtes invité à entrer les informations d’identification afin que le serveur d’application web peut établir une connexion sécurisée avec le serveur AD FS. Ces informations d’identification doivent être un administrateur local sur le serveur AD FS.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Spécifier le compte de service pour le service AD FS
Le service AD FS requiert un compte de service de domaine pour authentifier les utilisateurs et les informations utilisateur de recherche dans Active Directory. Il prend en charge deux types de comptes de service :

- **Groupe géré le compte de Service** - introduites dans les Services de domaine Active Directory avec Windows Server 2012. Ce type de compte fournit des services, tels que AD FS, un seul compte sans avoir besoin de mettre à jour le mot de passe régulièrement. Utilisez cette option si vous disposez déjà de contrôleurs de domaine Windows Server 2012 dans le domaine auquel appartiennent vos serveurs ADFS.
- **Compte d’utilisateur de domaine** - ce type de compte nécessite vous permet de fournir un mot de passe et mettre à jour le mot de passe lorsque le mot de passe change ou arrive à expiration. Utilisez cette option uniquement lorsque vous n’avez pas de contrôleurs de domaine Windows Server 2012 dans le domaine auquel appartiennent vos serveurs ADFS.

Si vous avez sélectionné groupe de compte de Service administré et que cette fonctionnalité n’a jamais été utilisée dans Active Directory, vous êtes invité pour les informations d’identification de l’administrateur de l’entreprise. Ces informations sont utilisées pour initialiser la banque de clés et d’activer la fonctionnalité dans Active Directory.

![Compte de Service AD FS](./media/active-directory-aadconnect-get-started-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-wish-to-federate"></a>Sélectionnez le domaine AD Azure à se fédérer
Cette configuration est utilisée pour configurer la relation de fédération entre AD FS et Azure AD. Il configure AD FS pour le problème des jetons de sécurité à Active Directory Azure ainsi Azure AD pour approuver les jetons à partir de cette instance d’AD FS spécifique. Cette page vous permet uniquement de configurer un seul domaine dans l’installation initiale. Vous pouvez configurer plusieurs domaines ultérieurement en exécutant de nouveau Azure Connect d’Active Directory.

![Domaines Active Directory Azure](./media/active-directory-aadconnect-get-started-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Vérifiez le domaine AD Azure sélectionné pour la fédération
Lorsque vous sélectionnez le domaine à être fédéré, Azure AD Connect vous fournit les informations nécessaires pour vérifier un domaine non vérifié. Consultez [Ajouter et vérifiez le domaine](../active-directory-add-domain.md) pour l’utilisation de ces informations.

![Domaines Active Directory Azure](./media/active-directory-aadconnect-get-started-custom/verifyfeddomain.png)

>[AZURE.NOTE]
AD Connect tente de vérifier le domaine lors de l’étape de configuration. Si vous continuez de configurer sans ajout d’enregistrements DNS nécessaires, l’Assistant n’est pas en mesure de terminer la configuration.

## <a name="configure-and-verify-pages"></a>Configurer et de vérifier les pages
La configuration se produit sur cette page.

>[AZURE.NOTE]
Avant de continuer l’installation et si vous avez configuré la fédération, vérifiez que vous avez configuré la [résolution de noms pour les serveurs de fédération](../active-directory-aadconnect-prerequisites.md#name-resolution-for-federation-servers).

![Prêt à configurer](./media/active-directory-aadconnect-get-started-custom/readytoconfigure2.png)

### <a name="staging-mode"></a>Mode de mise en attente
Il est possible de configurer un nouveau serveur de synchronisation en parallèle avec le mode de mise en attente. Il est uniquement pris en charge pour disposer d’un serveur de synchronisation exportant vers un répertoire dans le nuage. Mais si vous voulez déplacer à partir d’un autre serveur, par exemple une synchronisation d’annuaire en cours d’exécution, vous pouvez activer Azure Connect AD dans le mode de mise en attente. Lorsqu’il est activé, le moteur de synchronisation importer et synchroniser les données comme d’habitude, mais il n’exporte pas quoi que ce soit vers Azure Active Directory ou Active Directory. Le fonctionnalités mot de passe mot de passe et de la synchronisation en écriture différée sont désactivées lorsque vous êtes en mode de mise en attente.

![Mode de mise en attente](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)

En mode de mise en attente, il est possible apporter les modifications requises pour le moteur de synchronisation et passer en revue ce qui doit être exporté. Lorsque la configuration est bonne, relancez l’Assistant installation et désactiver le mode intermédiaire. Données sont maintenant exportées vers Azure annonce à partir de ce serveur. Veillez à désactiver l’autre serveur en même temps donc qu’un seul serveur consiste à exporter activement.

Pour plus d’informations, voir [mode de mise en attente](../active-directory-aadconnectsync-operations.md#staging-mode).

### <a name="verify-your-federation-configuration"></a>Vérification de la configuration de la fédération
Azure AD connexion vérifie les paramètres DNS pour vous lorsque vous cliquez sur le bouton vérifier.

![Terminer](./media/active-directory-aadconnect-get-started-custom/completed.png)

![Vérifier](./media/active-directory-aadconnect-get-started-custom/adfs7.png)

En outre, effectuez les vérifications suivantes :

- Valider que vous pouvez vous connecter à partir d’un navigateur à partir d’un ordinateur joint de domaine sur l’intranet : se connecter à https://myapps.microsoft.com et vérifiez la connexion à l’aide de votre compte connecté. Le compte d’administrateur AD DS n’est pas synchronisé et ne peut pas être utilisé pour la vérification.
- Vérifiez que vous pouvez vous connecter à partir d’un périphérique à partir de l’extranet. Sur un ordinateur personnel ou un périphérique mobile, connectez-vous à https://myapps.microsoft.com et fournir vos informations d’identification.
- Valider la connexion de client riche. Se connecter à https://testconnectivity.microsoft.com, cliquez sur l’onglet **d’Office 365** et choisi l' **Office 365 session Test unique**.

## <a name="next-steps"></a>Étapes suivantes
Une fois l’installation terminée, déconnectez-vous et reconnectez-vous à Windows avant d’utiliser le Gestionnaire de Service de synchronisation ou l’éditeur de règles de synchronisation.

Maintenant que vous avez Azure Connect Active Directory installé, vous pouvez [vérifier l’installation et l’attribution de licences](../active-directory-aadconnect-whats-next.md).

En savoir plus sur ces fonctionnalités, qui ont été activées lors de l’installation : [pas accidentelle de suppression](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) et de [La santé se connecter Azure AD](../active-directory-aadconnect-health-sync.md).

En savoir plus sur ces sujets courants : [Planificateur et le mode de déclenchement de la synchronisation](../active-directory-aadconnectsync-feature-scheduler.md).

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](../active-directory-aadconnect.md).

## <a name="related-documentation"></a>Documentation connexe

Rubrique |  
--------- | ---------
Vue d’ensemble de connexion de publicité Azure | [Intégration des identités sur site à Azure Active Directory](../active-directory-aadconnect.md)
L’installation en utilisant les paramètres Express | [Installation rapide d’Azure Connect de publicité](active-directory-aadconnect-get-started-express.md)
Mise à niveau à partir de la synchronisation d’annuaire | [Mise à niveau à partir de l’outil de synchronisation AD Azure (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Comptes utilisés pour l’installation | [En savoir plus sur les autorisations et les comptes d’Azure Connect de publicité](active-directory-aadconnect-accounts-permissions.md)
