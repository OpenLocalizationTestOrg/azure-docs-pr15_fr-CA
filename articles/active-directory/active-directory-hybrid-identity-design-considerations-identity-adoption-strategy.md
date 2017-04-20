<properties
    pageTitle="Azure Active Directory hybride identité considérations de conception - définir une stratégie pour adopter identité hybride | Microsoft Azure"
    description="Avec contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Lorsque ces conditions sont réunies, l’utilisateur est authentifié et autorisé à accéder à l’application."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-a-hybrid-identity-adoption-strategy"></a>Définir une stratégie d’adoption identité hybride

Dans cette tâche, vous allez définir la stratégie d’adoption identité hybride pour votre solution d’identité hybride répondre aux besoins de l’entreprise qui ont été abordés dans :

- [Déterminer les besoins de l’entreprise](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [Déterminer les besoins de synchronisation d’annuaire](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [Déterminer les besoins d’authentification à plusieurs facteurs](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Définition de la stratégie de besoins professionnels
Les adresses de tâche première détermination de l’activité des entreprises a besoin.  Cela peut être très large et escalade de l’étendue peut se produire si vous n’êtes pas prudent.  Dans début simplicité, mais n’oubliez pas de prévoir une conception qui va accueillir et faciliter les changer à l’avenir.  Qu’il s’agisse d’une conception simple ou un extrêmement complexe, Azure Active Directory est la plate-forme de Microsoft Identity qui prend en charge les applications prenant en charge Office 365 et de Microsoft Online Services de nuage.

## <a name="define-an-integration-strategy"></a>Définir une stratégie d’intégration
Microsoft a trois scénarios d’intégration principal qui sont les identités de nuage, synchronisation des identités et des identités fédérées.  Vous devez planifier en adoptant une de ces stratégies d’intégration.  La stratégie que vous choisissez peut varier et les décisions en choisissant l’une peut inclure, quel type d’expérience utilisateur que vous souhaitez fournir, vous avez partie de l’infrastructure existante déjà en place, et ce qui est le plus économique.  
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Les scénarios définis dans la figure ci-dessus sont :

- **Identités du nuage**: il s’agit des identités qui existent uniquement dans le nuage.  Dans le cas d’Azure AD, ils résident spécifiquement dans votre répertoire AD Azure.
- **Synchronisé**: il s’agit des identités qui existent sur site et dans le cloud.  À l’aide d’Azure Connect d’Active Directory, ces utilisateurs créés ou rejoint avec les comptes Active Directory Azure existants.  Hachage de mot de passe de l’utilisateur est synchronisé à partir de l’environnement local dans le nuage dans ce qu’on appelle un hachage du mot de passe.  Lors de l’utilisation de synchronisation l’inconvénient est que si un utilisateur est désactivé dans l’environnement local, il peut prendre jusqu'à 3 heures pour ce statut de compte s’affiche dans l’annonce d’Azure.  C’est en raison de l’intervalle de synchronisation.
- **Fédéré**: ces identités existent à la fois sur site et dans le cloud.  À l’aide d’Azure Connect d’Active Directory, ces utilisateurs créés ou rejoint avec les comptes Active Directory Azure existants.  
 
>[AZURE.NOTE]
Pour plus d’informations sur la synchronisation options lire [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).

Le tableau suivant vous aide à déterminer les avantages et les inconvénients de chacune des stratégies suivantes :

| Stratégie de         | Avantages                                                                                                                                                                                                                                                  | Inconvénients                                                                                                                                                                                                                                                                                                                                                  |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Identités de nuage** | Plus facile à gérer pour une petite organisation. <br> Rien à installer non locaux de matériel supplémentaire nécessaire<br>Facilement désactivé si l’utilisateur quitte la société                                                                                                   | Les utilisateurs devront se connecter lorsqu’ils accèdent à des charges de travail dans le cloud <br> Mots de passe peuvent ou ne peuvent pas être le même pour les identités du nuage et sur site                                                                                                                                                                                                                     |
| **Synchronisé**     | Mot de passe local s’authentifier à la fois sur site et répertoires en nuage <br>Plus facile à gérer pour les entreprises de petites, moyennes ou grandes <br>Les utilisateurs peuvent disposer de session unique (SSO) pour certaines ressources <br> Méthode par défaut de Microsoft pour la synchronisation <br> Plus facile à gérer | Certains clients peuvent être réticents à synchroniser leurs répertoires avec le nuage d’échéance police de société spécifique                                                                                                                                                                                                                                                  |
| **Fédéré**        | Les utilisateurs peuvent disposer de session unique (SSO) <br>Si un utilisateur s’arrête ou quitte, le compte peut être désactivé immédiatement et que l’accès est révoqué,<br> Prend en charge des scénarios avancés qui ne peuvent pas être, effectuée avec synchronisation                                           | Installation et configuration en plusieurs étapes <br> Maintenance plus élevé <br> Peut nécessiter du matériel supplémentaire pour l’infrastructure SharePoint Team Services <br> Peut nécessiter du matériel supplémentaire pour installer le serveur de fédération. Logiciel supplémentaire n’est requis si AD FS est utilisé. <br> Nécessitent une installation complète pour l’authentification unique <br> Point de critique de la panne si le serveur de fédération est arrêté, les utilisateurs ne pourront s’authentifier |

### <a name="client-experience"></a>Expérience client
La stratégie que vous utilisez détermine l’expérience de connexion utilisateur.  Les tableaux suivants vous fournissent des informations sur ce que les utilisateurs doivent s’attendre à leur expérience de connexion à.  Notez que tous les fournisseurs d’identité fédérés prennent en charge l’authentification unique dans tous les scénarios.

**Applications réseau jointe au domaine et privées**:
 

|                              | Identité synchronisée      | Identité fédérée                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Navigateurs Web                 | L’authentification basée sur les formulaires | ouverture de session unique, parfois requis pour fournir l’ID de l’organisation |
| Outlook                      | Demander les informations d’identification     | Demander les informations d’identification                                       |
| Skype pour les entreprises (Lync)    | Demander les informations d’identification     | connexion unique pour Lync, l’invite d’informations d’identification pour Exchange   |
| SkyDrive Pro                 | Demander les informations d’identification     | ouverture de session unique                                               |
| Office Pro Plus d’abonnement | Demander les informations d’identification     | ouverture de session unique                                               |

**Externe ou non approuvé de sources de**:

|                              | Identité synchronisée      | Identité fédérée                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Navigateurs Web                 | L’authentification basée sur les formulaires |  L’authentification basée sur les formulaires |
| Outlook, Skype pour les entreprises (Lync), Skydrive Pro, abonnement Office| Demander les informations d’identification     | Demander les informations d’identification                                       |
| Exchange ActiveSync    | Demander les informations d’identification     | connexion unique pour Lync, l’invite d’informations d’identification pour Exchange   |
| Applications mobiles                 | Demander les informations d’identification     | Demander les informations d’identification                                               |

Si vous avez déterminé à partir de la tâche 1 que vous avez un 3ème partie IdP ou sont des cours pour utiliser un pour fournir la fédération avec AD Azure, vous devez connaître les fonctions prises en charge suivantes :
- N’importe quel fournisseur de SAML 2.0 qui est conforme pour le profil SP-Lite peut de prendre en charge l’authentification AD Azure et applications associées
- Prend en charge l’authentification passive, ce qui facilite l’authentification OWA, simulé, etc..
- Les clients Exchange Online peuvent être pris en charge via le SAML 2.0 amélioré Client profil (ECP)

Vous devez être également connaître quelles sont les fonctionnalités ne seront pas disponibles :

- Sans prise en charge de WS-approbation/Federation, rompt tous les autres clients actives
 - Cela ne signifie aucun client Lync, OneDrive client, abonnement Office, Office Mobile avant Office 2016
- Transition d’Office pour authentification passive permettra pour prendre en charge les IdPs de 2.0 SAML pur, mais la prise en charge sera toujours sur une base client par client


>[AZURE.NOTE]
Pour obtenir la liste la plus à jour, lisez l’article http://aka.ms/ssoproviders.

## <a name="define-synchronization-strategy"></a>Définition de la stratégie de synchronisation
Dans cette tâche, vous allez définir les outils qui seront utilisés pour synchroniser l’organisation locale données au nuage et topologie, vous devez utiliser.  Car, la plupart des organisations utilisent Active Directory, les informations sur l’utilisation d’Azure Connect d’Active Directory pour répondre aux questions ci-dessus sont fournies en détail.  Pour les environnements qui n’ont pas d’Active Directory, il existe d’informations sur l’utilisation de FIM 2010 R2 ou MIM 2016 afin de planifier cette stratégie.  Toutefois, les versions futures de Azure AD Connect prend en charge les annuaires LDAP, par conséquent, en fonction de votre barre de planning, ces informations peuvent être en mesure de vous aider.

###<a name="synchronization-tools"></a>Outils de synchronisation
Au fil des années, plusieurs outils de synchronisation ont existé et utilisés pour différents scénarios.  Actuellement, la connexion AD Azure est l’aller à l’outil de choix pour tous les scénarios pris en charge.  Synchronisation de DAS et de synchronisation d’annuaire sont également toujours autour et peuvent même être présents dans votre environnement maintenant. 

>[AZURE.NOTE]
Pour les dernières informations concernant les fonctionnalités prises en charge de chaque outil, lisez l’article de [comparaison des outils Directory integration](active-directory-hybrid-identity-design-considerations-tools-comparison.md) .  

### <a name="supported-topologies"></a>Topologies prises en charge
Lors de la définition d’une stratégie de synchronisation, la topologie utilisée doit être déterminée. En fonction des informations qui a été déterminées à l’étape 2, vous pouvez déterminer quelle topologie est l’outil à utiliser. La forêt unique, la seule topologie AD Azure est le plus souvent et se compose d’une seule forêt Active Directory et une seule instance d’Azure AD.  Il doit être utilisé dans la majorité des scénarios et la topologie attendue lors de l’utilisation d’installation AD Azure se connecter Express comme indiqué dans la figure ci-dessous.
 
![](./media/hybrid-id-design-considerations/single-forest.png)Scénario de forêt unique qu’il est très courant que même les petites et grandes entreprises possèdent plusieurs forêts, comme illustré à la Figure 5.

>[AZURE.NOTE]
Pour plus d’informations sur les différents sur site et les topologies AD Azure avec Azure Connect de AD sync lire l’article [Topologies pour Azure Connect d’Active Directory](active-directory-aadconnect-topologies.md).


![](./media/hybrid-id-design-considerations/multi-forest.png) 

Scénario de forêts multiples

Si ce le cas, la topologie multi-forest-unique AD Azure doit être pris en considération si les conditions suivantes sont respectées :

- Les utilisateurs ont uniquement 1 identité dans toutes les forêts : l’identifiant de manière unique les utilisateurs ci-dessous décrit cela plus en détail.
- L’utilisateur s’authentifie à la forêt dans lequel se trouve leur identité
- UPN et le point d’ancrage de la Source (id immuable) proviendront de cette forêt.
- Toutes les forêts sont accessibles par Azure Connect de publicité – cela signifie de que ne pas devoir être domaine rejoint et peut être placé dans une zone DMZ si cela facilite cela.
- Les utilisateurs ont uniquement une boîte aux lettres
- La forêt qui héberge la boîte aux lettres de l’utilisateur a la meilleure qualité de données pour les attributs visibles dans la liste d’adresses globale Exchange (GAL)
- S’il n’existe aucune boîte aux lettres de l’utilisateur, une forêt peut ensuite être utilisée pour ces valeurs de contribute
- Si vous avez une boîte aux lettres liée, puis il existe également un autre compte dans une autre forêt utilisée pour vous connecter.

>[AZURE.NOTE]
Les objets qui existent dans les locaux et le nuage sont « connectés » via un identificateur unique. Dans le contexte de synchronisation d’annuaire, cet identificateur unique est appelé la SourceAnchor. Dans le contexte de session unique, ceci est appelé la ImmutableId. [Les concepts Azure Connect d’annonce](active-directory-aadconnect-design-concepts.md#sourceanchor) pour plus de considérations relatives à l’utilisation de SourceAnchor.

Si vous avez plus d’un compte d’actif ou de plusieurs boîtes aux lettres ci-dessus ne sont pas remplies, Azure Connect de publicité sera choisir un et ignorer l’autre.  Si vous avez lié des boîtes aux lettres, mais pas d’autre compte, ces comptes ne seront pas exportées vers Active Directory Azure et cet utilisateur ne sera pas un membre d’un groupe.  Cela est différent de comment il a été par le passé avec la synchronisation d’annuaire et est intentionnel pour une meilleure prise en charge de ces scénarios de plusieurs forêts. Un scénario de forêt multiples est illustré dans la figure ci-dessous.
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 
 
**Plusieurs forêts plusieurs scénario d’Azure AD**

Il est recommandé d’avoir qu’un seul répertoire dans Azure AD pour une organisation mais il est pris en charge qu’il reste une relation 1:1 entre un serveur de synchronisation d’Azure Connect de publicité et d’un annuaire AD d’Azure.  Pour chaque instance d’AD Azure, vous devez une installation d’Azure Connect d’Active Directory.  En outre, AD Azure, par conception est isolé et utilisateurs dans une instance d’Azure annonce ne sera pas en mesure de voir les utilisateurs dans une autre instance.

Il est possible et pris en charge pour connecter une instance locale d’Active Directory à plusieurs annuaires AD Azure comme illustré dans la figure ci-dessous :

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 
 

**Scénario de filtrage de forêt unique**

Pour ce faire les informations suivantes doit être remplies :

- Serveurs de synchronisation Azure Connect d’Active Directory doivent être configurés pour le filtrage et ils ont chacun un ensemble mutuellement exclusif d’objets.  Cela fait, par exemple, par la portée de chaque serveur dans un domaine particulier ou d’une unité d’organisation.
- Un serveur DNS ne peut être inscrite que dans un seul répertoire d’Azure AD donc l’UPN des utilisateurs dans les locaux AD doit utiliser des espaces de noms distincts
- Les utilisateurs dans une instance d’Azure annonce sera uniquement en mesure de voir les utilisateurs de leur instance.  Ils ne pourront pas voir les utilisateurs dans les autres cas
- Seul l’un des répertoires AD Azure peut activer Exchange hybride avec sur site AD
- Exclusivité mutuelle s’applique également pour l’écriture différée.  Ainsi, certaines fonctionnalités d’écriture différée ne pas pris en charge avec cette topologie puisqu’elles supposent une configuration unique sur site.  Cela inclut :
 - Groupe d’écriture différée avec la configuration par défaut
 - DISPOSITIF différée


Sachez que les éléments suivants ne sont pas pris en charge et ne doivent pas être choisies comme une implémentation :

- Il n’est pas pris en charge pour plusieurs serveurs de synchronisation Azure AD connexion se connecter dans le même répertoire AD Azure même s’ils sont configurés pour synchroniser un jeu mutuellement exclusif d’objet
- Il est pris en charge pour synchroniser le même utilisateur sur plusieurs annuaires AD Azure. 
- Il est également pris en charge pour modifier une configuration pour que les utilisateurs dans une annonce en tant que contacts dans un autre répertoire AD Azure Azure. 
- Il est également non pris en charge pour modifier la synchronisation Azure AD Connect pour vous connecter à plusieurs annuaires AD Azure.
- Les répertoires AD Azure sont par conception isolée. Il est non pris en charge pour modifier la configuration de la synchronisation d’Azure AD Connect pour lire des données à partir d’un autre répertoire AD Azure lors d’une tentative de créer une liste d’adresses globale commune et unifiée entre les annuaires. Il est également non pris en charge pour exporter les utilisateurs en tant que contacts vers un autre sur site AD à l’aide de la synchronisation d’Azure Connect d’AD.


>[AZURE.NOTE]
Si votre organisation limite les ordinateurs de votre réseau de se connecter à Internet, cet article répertorie les points de terminaison (noms de domaine complets, IPv4 et IPv6 de plages d’adresses) que vous devez inclure dans votre sortants permettent aux listes et la Zone des Sites de confiance Internet Explorer du client ordinateurs pour vous assurer que vos ordinateurs peuvent avec succès Office 365. Pour plus d’informations, lisez [les URL d’Office 365 et de plages d’adresses IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).

## <a name="define-multi-factor-authentication-strategy"></a>Définition de la stratégie d’authentification à plusieurs facteurs
Dans cette tâche, vous allez définir la stratégie d’authentification à plusieurs facteurs à utiliser.  Authentification à plusieurs facteurs Azure est disponible en deux versions différentes.  Un est un nuage et l’autre en fonction de l’utilisation du serveur d’AMF Azure sur site.  En fonction de l’évaluation vous ci-dessus, vous pouvez déterminer quelle solution est correcte pour votre stratégie.  Utilisez le tableau ci-dessous pour déterminer quelle option de conception mieux satisfaire les exigences de sécurité de votre entreprise :

Options de création de plusieurs facteurs :

| Pour sécuriser les actifs                                               | AMF dans le nuage | AMF sur site |
|---------------------------------------------------------------|------------------|----------------|
| Applications Microsoft                                                | Oui              | Oui            |
| Applications SaaS dans la galerie d’application                                  | Oui              | Oui            |
| Applications IIS publiées via un Proxy d’application Azure AD         | Oui              | Oui            |
| Applications IIS ne pas publiées par le Proxy d’application de AD Azure | aucune               | Oui            |
| Accès distant VPN, distance                                     | aucune               | Oui            |

Même si vous pouvez avoir réglé sur une solution pour votre stratégie, vous devez toujours utiliser l’évaluation ci-dessus sur où se trouvent vos utilisateurs.  Cela peut entraîner la solution à modifier.  Utilisez le tableau ci-dessous pour vous aider à déterminer ce :

| Emplacement de l’utilisateur                                                       | Option de conception par défaut                 |
|---------------------------------------------------------------------|-----------------------------------------|
| Azure Active Directory                                              | Multi-FactorAuthentication dans le nuage |
| Annonce Azure et annonce à l’aide de la fédération avec AD FS sur site             | Les deux                                    |
| Annonce Azure et annonce à l’aide d’Active Directory Azure aucune synchronisation de mot de passe de connecter en local | Les deux                                    |
| Annonce Azure et sur site à l’aide d’Azure Connect d’Active Directory avec la synchronisation de mot de passe  | Les deux                                    |
| Publicité sur site                                                      | Serveur d’authentification à plusieurs facteurs      |

>[AZURE.NOTE]
Vous devez également vous assurer que l’option Création de plusieurs facteurs d’authentification que vous avez sélectionné prend en charge les fonctionnalités requises pour votre conception.  Pour plus d’informations, consultez [Choisir la solution de sécurité à facteurs multiples pour vous](../multi-factor-authentication-get-started.md#what-am-i-trying-to-secure).

## <a name="multi-factor-auth-provider"></a>Fournisseur d’authentification selon plusieurs facteurs
Plusieurs facteurs d’authentification par défaut est disponible pour les administrateurs globaux qui ont un locataire Azure Active Directory. Toutefois, si vous souhaitez étendre l’authentification multifactorielle pour tous vos utilisateurs ou souhaitez un administrateur global pour pouvoir tirer parti des fonctionnalités le portail de gestion, d’accueil personnalisé, des rapports, vous devez acheter et configurer le fournisseur d’authentification selon plusieurs facteurs.

>[AZURE.NOTE]
Vous devez également vous assurer que l’option Création de plusieurs facteurs d’authentification que vous avez sélectionné prend en charge les fonctionnalités requises pour votre conception. 

##<a name="next-steps"></a>Étapes suivantes
[Déterminer les exigences de protection de données](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
