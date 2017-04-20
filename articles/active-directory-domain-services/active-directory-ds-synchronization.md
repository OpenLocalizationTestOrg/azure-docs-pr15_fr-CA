<properties
    pageTitle="Azure Services domaine Active Directory : Synchronisation dans les domaines gérés | Microsoft Azure"
    description="Comprendre la synchronisation dans un domaine géré Azure des Services de domaine Active Directory"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synchronisation dans un domaine géré de Services de domaine Active Directory de Azure
Le diagramme suivant illustre comment fonctionne la synchronisation dans les Services de domaine Active Directory de Azure domaines gérés.

![Topologie de synchronisation des Services de domaine Active Directory de Azure](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synchronisation à partir de votre répertoire local pour vos clients AD Azure
Azure Connect de AD sync sert à synchroniser les comptes d’utilisateur, les appartenances de groupe, et d’informations d’identification hache à vos clients AD Azure. Attributs d’utilisateur des comptes tels que le nom UPN et le SID (identificateur de sécurité) sont synchronisés sur site. Si vous utilisez les Services de domaine Active Directory de Azure, les hachages hérités d’informations d’identification requises pour l’authentification NTLM et Kerberos sont également synchronisées avec vos clients AD Azure.

Si vous configurez en écriture différée, les modifications qui se produisent dans votre répertoire AD Azure sont resynchronisées avec Active Directory sur site. Par exemple, si vous modifiez votre mot de passe à l’aide des fonctionnalités de modification de mot de passe libre-service d’Azure AD, le mot de passe modifié est mis à jour dans vos locaux de domaine Active Directory.

> [AZURE.NOTE] Utilisez toujours la version la plus récente d’Azure Connect de publicité que vous disposez des correctifs pour tous les bogues connus.


## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synchronisation à partir de votre client AD Azure pour votre domaine géré
Comptes d’utilisateur, les appartenances au groupe et les hachages de d’informations d’identification sont synchronisés à partir de votre client AD Azure pour votre domaine géré les Services de domaine Active Directory de Azure. Ce processus de synchronisation est automatique. Vous n’avez pas besoin de configurer, de contrôler ou de gérer ce processus de synchronisation. Le processus de synchronisation est également unique-way/unidirectionnelle dans la nature. Votre domaine géré est principalement en lecture seule, sauf pour les unités d’organisation personnalisées que vous créez. Par conséquent, vous ne pouvez pas modifier les attributs utilisateur, des mots de passe utilisateur ou des appartenances aux groupes dans le domaine géré. En conséquence, il n’existe aucune synchronisation inversée des modifications à partir de votre domaine géré à vos clients d’Azure AD.


## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronisation à partir d’un environnement à plusieurs forêts sur site
De nombreuses organisations ont une infrastructure d’identité local assez complexe consistant en plusieurs forêts de comptes. Azure AD Connect prend en charge la synchronisation des utilisateurs, des groupes et des hachages de d’informations d’identification à partir d’environnements à plusieurs forêts pour vos clients AD Azure.

En revanche, vos clients AD Azure est un bien plus simple et plate espace de noms. Pour permettre aux utilisateurs d’accéder de manière fiable les applications sécurisées par AD Azure, résoudre les conflits de nom UPN entre comptes d’utilisateurs dans des forêts différentes. Votre domaine géré par les Services de domaine Active Directory de Azure rapport ferme ressemblance avec vos clients AD Azure. Par conséquent, vous voyez une structure plane à l’unité d’organisation dans votre domaine géré. Tous les utilisateurs et les groupes sont stockés dans le conteneur 'AADDC ' utilisateurs, quel que soit le domaine de locaux ou de la forêt à partir de laquelle ils ont été synchronisées dans. Vous avez peut-être configuré une unité d’organisation hiérarchique de structure sur site. Toutefois, votre domaine géré a toujours une simple structure d’unités d’organisation plate.


## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusions - ce qui n’est pas synchronisé dans votre domaine géré
Les objets ou les attributs suivants ne sont pas synchronisées à votre client AD Azure ou à votre domaine géré :

- **Exclus des attributs :** Vous pouvez choisir d’exclure certains attributs de se synchroniser avec vos clients AD Azure à partir de votre domaine local à l’aide d’Azure Connect d’Active Directory. Ces attributs exclus ne sont pas disponibles dans votre domaine géré.

- **Des stratégies de groupe :** Les stratégies de groupe configurés dans votre domaine local ne sont pas synchronisées à votre domaine géré.

- **Partage SYSVOL :** De même, le contenu du partage SYSVOL sur le domaine local n’est pas synchronisé à votre domaine géré.

- **Objets ordinateur :** Les objets ordinateur pour les ordinateurs joints à votre domaine local ne sont pas synchronisées à votre domaine géré. Ces ordinateurs n’ont une relation d’approbation avec le domaine géré et appartiennent à votre domaine local uniquement. Dans votre domaine géré, vous trouvez les objets ordinateur uniquement pour les ordinateurs que vous avez explicitement à un domaine pour le domaine géré.

- **Les attributs SidHistory des utilisateurs et des groupes :** L’utilisateur principal et le groupe principal SID à partir de votre domaine local sont synchronisés avec votre domaine géré. Toutefois, les attributs SidHistory existants pour les utilisateurs et les groupes ne sont pas synchronisés à partir de votre domaine local dans votre domaine géré.

- **Les structures d’unités d’organisation (UO) :** Les unités d’organisation définies dans votre domaine local ne sont pas synchronisent à votre domaine géré. Il existe deux unités d’organisation intégrées dans votre domaine géré. Par défaut, votre domaine géré a une structure d’unités d’organisation plate. Vous pouvez cependant choisir de [créer une unité d’organisation personnalisée dans votre domaine géré](./active-directory-ds-admin-guide-create-ou.md).


## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Comment les attributs spécifiques sont synchronisées avec votre domaine géré
Le tableau suivant répertorie certains des attributs communs et explique comment ils sont synchronisés avec votre domaine géré.

| Attribut dans votre domaine géré | Source | Notes |
|:---|:---|:---|
|UPN|Attribut de nom UPN de l’utilisateur dans vos clients AD Azure|L’attribut UPN à partir de votre client AD Azure est synchronisé est à votre domaine géré. Par conséquent, la méthode la plus fiable pour vous connecter à votre domaine géré utilise votre nom UPN.|
|SAMAccountName|MailNickname de l’utilisateur, l’attribut dans vos clients AD Azure ou généré automatiquement|L’attribut SAMAccountName est alimenté à partir de l’attribut mailNickname dans vos clients AD Azure. Si plusieurs comptes d’utilisateurs ont le même attribut mailNickname, SAMAccountName est généré automatiquement. Si la valeur mailNickname ou préfixe UPN de l’utilisateur est supérieure à 20 caractères, SAMAccountName est généré automatiquement pour répondre à la limite de 20 caractères sur des attributs SAMAccountName.|
|Mots de passe|Mot de passe de l’utilisateur à partir de votre client AD Azure|Les hachages de d’informations d’identification requises pour l’authentification NTLM ou Kerberos (appelée également les informations d’identification supplémentaires) sont synchronisés à partir de votre client AD Azure. Si vos clients AD Azure est un locataire synchronisée, ces informations d’identification sont alimentées à partir de votre domaine local.|
|Utilisateur/groupe SID principal|Généré automatiquement|Le SID principal pour les comptes d’utilisateur ou le groupe est générée automatiquement dans votre domaine géré. Cet attribut ne correspond pas à l’utilisateur/SID de groupe principal de l’objet dans vos locaux sur domaine AD. Cette différence est parce que le domaine géré a un espace de noms différent de l’identificateur de sécurité à votre domaine local.|
|Historique des SID des utilisateurs et des groupes|Utilisateur principal et les SID de groupe local|L’attribut SidHistory pour les utilisateurs et les groupes dans votre domaine géré est défini pour faire correspondre l’utilisateur primaire correspondant ou le groupe SID dans le domaine local. Cette fonctionnalité permet de faciliter l’ascenseur et MAJ des applications sur site pour le domaine géré, dans la mesure où vous n’avez pas besoin de re-listes ACL des ressources.|

> [AZURE.NOTE] **Vous connecter au domaine géré à l’aide du format UPN :** L’attribut SAMAccountName peut être générée automatiquement pour certains comptes d’utilisateurs dans votre domaine géré. Si plusieurs utilisateurs ont le même attribut mailNickname ou si les utilisateurs ont les préfixes de nom UPN anormalement longues, SAMAccountName pour ces utilisateurs peut être généré automatiquement. Par conséquent, le format de SAMAccountName (par exemple, ' CONTOSO100\joeuser') n’est pas toujours un moyen fiable de se connecter au domaine. SAMAccountName généré automatiquement d’utilisateurs peut différer leur préfixe UPN. Utilisez le format UPN (par exemple, 'joeuser@contoso100.com') pour vous connecter au domaine géré correctement.


## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objets qui ne sont pas synchronisés à partir de votre domaine géré à vos clients AD Azure
Comme décrit dans la section précédente de cet article, il n’y a aucune synchronisation à partir de votre domaine géré vers vos clients AD Azure. Vous pouvez choisir de [créer une unité d’organisation personnalisée (UO)](./active-directory-ds-admin-guide-create-ou.md) dans votre domaine géré. En outre, vous pouvez créer d’autres unités d’organisation, les utilisateurs, les groupes ou les comptes de service au sein de ces unités d’organisation personnalisées. Les objets créés dans des unités d’organisation personnalisées sont synchronisées à vos clients AD Azure. Ces objets sont disponibles uniquement dans votre domaine géré. Par conséquent, ces objets ne sont pas visibles à l’aide des applets de commande PowerShell de publicité d’Azure, les API Azure AD graphique ou à l’aide de l’interface utilisateur de gestion AD Azure.


## <a name="related-content"></a>Contenu associé
- [Fonctionnalités - des Services Azure domaine d’Active Directory](active-directory-ds-features.md)

- [Scénarios de déploiement - Services de domaine Active Directory de Azure](active-directory-ds-scenarios.md)

- [Considérations relatives à la mise en réseau pour les Services de domaine Active Directory de Azure](active-directory-ds-networking.md)

- [Mise en route avec les Services de domaine Active Directory de Azure](active-directory-ds-getting-started.md)
