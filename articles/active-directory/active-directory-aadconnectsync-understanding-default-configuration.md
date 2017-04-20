<properties
    pageTitle="Azure Connect de AD sync : présentation de la configuration par défaut | Microsoft Azure"
    description="Cet article décrit la configuration par défaut dans Azure Connect de AD sync."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure Connect de AD sync : présentation de la configuration par défaut
Cet article décrit les règles de configuration d’out-of-box. Elle documente les règles et l’impact de ces règles sur la configuration. Il vous guide également dans la configuration par défaut d’Azure Connect de AD sync. L’objectif est que le lecteur comprenne comment fonctionne le modèle de configuration nommé déclarative de provisionnement, dans un exemple réel. Cet article suppose que vous avez déjà installé et que vous configurez Azure Connect d’AD à l’aide de l’Assistant installation de la synchronisation.

Pour comprendre les détails du modèle de configuration, lire la [Présentation de la mise en service déclarative](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Règles de out-of-box sur site vers Active Directory Azure
Les expressions suivantes sont accessibles dans la configuration prête à l’emploi.

### <a name="user-out-of-box-rules"></a>Règles d’utilisateur out-of-box
Ces règles s’appliquent également au type d’objet iNetOrgPerson.

Un objet utilisateur doit satisfaire les éléments suivants à synchroniser :

- Doit être un sourceAnchor.
- Une fois que l’objet a été créé dans Active Directory Azure, sourceAnchor ne pouvez pas modifier. Si la valeur est modifié sur place, l’objet arrête la synchronisation jusqu'à ce que le sourceAnchor est modifié à sa valeur précédente.
- Doit avoir l’attribut accountEnabled (userAccountControl) rempli. Avec Active Directory sur site, cet attribut est toujours présente et rempli.

Les objets d’utilisateur suivants ne sont **pas** synchronisées dans AD Azure :

- `IsPresent([isCriticalSystemObject])`. S’assurer de nombreux objets d’out-of-box dans Active Directory, tels que le compte administrateur intégré, ne sont pas synchronisées.
- `IsPresent([sAMAccountName]) = False`. Assurez-vous que les objets utilisateur sans attribut sAMAccountName ne sont pas synchronisées. Ce cas se produit uniquement pratiquement dans un domaine mis à niveau à partir de NT4.
- `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Ne pas synchroniser le compte de service utilisé par la synchronisation d’Azure Connect de publicité et de ses versions antérieures.
- Ne pas synchroniser les comptes Exchange qui ne fonctionnent pas dans Exchange en ligne.
    - `[sAMAccountName] = "SUPPORT_388945a0"`
    - `Left([mailNickname], 14) = "SystemMailbox{"`
    - `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
    - `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
- Ne synchronisez pas les objets qui ne fonctionnerait pas dans Exchange en ligne.
`CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
Ce masque de bits (& H21C07000) peut filtrer les objets suivants :
    - Dossier Public à extension messagerie
    - Boîte aux lettres du service Surveillance du système
    - Boîte aux lettres de base de données de boîtes aux lettres (boîte aux lettres système)
    - Groupe universel de sécurité (ne s’appliquent pas pour un utilisateur, mais n’est présent pour des raisons d’héritage)
    - Groupe non universel (ne s’appliquent pas pour un utilisateur, mais n’est présent pour des raisons d’héritage)
    - Plan de la boîte aux lettres
    - Boîte aux lettres de découverte
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne pas synchroniser les objets cible de réplication.

Les règles d’attribut suivantes s’appliquent :

- `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. L’attribut sourceAnchor n’est pas contribué à partir d’une boîte aux lettres liée. Il est supposé que si une boîte aux lettres liée a été trouvé, le compte réel est joint ultérieurement.
- Liés à Exchange, les attributs sont synchronisés uniquement si l' attribut **mailNickName** a une valeur.
- Lorsqu’il existe plusieurs forêts, les attributs sont utilisés dans l’ordre suivant :
    1. Attributs liés à la connexion (par exemple userPrincipalName) sont fournis à partir de la forêt avec un compte activé.
    2. Les attributs qui se trouvent dans une liste d’adresses globale Exchange (liste d’adresses globale) sont fournis à partir de la forêt avec une boîte aux lettres Exchange.
    3. Si aucune boîte aux lettres ne peut être trouvé, ces attributs peuvent provenir de toute la forêt.
    4. Lié à Exchange avec attributs (attributs techniques non visibles dans la liste d’adresses globale) sont fournis à partir de la forêt où `mailNickname ISNOTNULL`.
    5. S’il existe plusieurs forêts qui pourraient satisfaire à une de ces règles, l’ordre de création (date/heure) des connecteurs (forêts) est utilisée pour déterminer quelle forêt fournit les attributs.

### <a name="contact-out-of-box-rules"></a>Contactez les règles out-of-box
Un objet contact doit satisfaire les éléments suivants à synchroniser :

- Le contact doit être à extension messagerie. Il est vérifié par les règles suivantes :
    - `IsPresent([proxyAddresses]) = True)`. L’attribut proxyAddresses doit être rempli.
    - Vous trouverez une adresse de messagerie principale dans l’attribut proxyAddresses ou l’attribut de messagerie. La présence d’une @ est utilisée pour vérifier que le contenu est une adresse de messagerie. Une de ces deux règles doit être évaluée à True.
        - `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existe-t-il une entrée avec « SMTP : » et s’il existe, un @ se trouve dans la chaîne ?
        - `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Est l’attribut mail rempli et si c’est le cas, peut un @ se trouve dans la chaîne ?

Les objets de contact suivants ne sont **pas** synchronisées dans AD Azure :

- `IsPresent([isCriticalSystemObject])`. Assurez-vous qu’aucuns marquées comme critiques d’objets contacts ne sont synchronisées. Ne doit pas être avec une configuration par défaut.
- `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
- `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Ces objets ne fonctionnerait pas dans Exchange en ligne.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne pas synchroniser les objets cible de réplication.

### <a name="group-out-of-box-rules"></a>Groupe de règles d’out-of-box
Un objet groupe doit satisfaire les éléments suivants à synchroniser :

- Doit être inférieur à 50 000 membres. Ce nombre est le nombre de membres du groupe local.
    - Si elle contient plus de membres avant la synchronisation démarre la première fois, le groupe n’est pas synchronisé.
    - Si le nombre de membres augmente à partir de laquelle il a été créé, puis lorsqu’elle atteint 50 000 membres, il arrête la synchronisation jusqu'à ce que le nombre de membres est inférieur à 50 000 à nouveau.
    - Remarque : Le nombre de 50 000 membres est également appliqué par AD Azure. Vous n’êtes pas en mesure de synchroniser les groupes contenant davantage de membres même si vous modifiez ou supprimez cette règle.
- Si le groupe est un **Groupe de Distribution**, il doit également être activé pour la messagerie. Reportez-vous à la section [règles de prêts à l’emploi de Contact](#contact-out-of-box-rules) de cette règle est appliquée.

Les objets de groupe suivantes ne sont **pas** synchronisées dans AD Azure :

- `IsPresent([isCriticalSystemObject])`. S’assurer de nombreux objets d’out-of-box dans Active Directory, tel que le groupe Administrateurs intégrés, ne sont pas synchronisées.
- `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Groupe hérité utilisé par la synchronisation d’annuaire.
- `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Groupe de rôles.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne pas synchroniser les objets cible de réplication.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Règles d’out-of-box ForeignSecurityPrincipal
FSP appartiennent à « quelconque » (\*) objet dans le méta-verse. En réalité, cette jointure se produit uniquement pour les utilisateurs et les groupes de sécurité. Cette configuration garantit que les appartenances entre forêts sont résolus et représentées correctement dans Azure AD.

### <a name="computer-out-of-box-rules"></a>Règles ordinateur out-of-box
Un objet ordinateur doit remplir les éléments suivants à synchroniser :

- `userCertificate ISNOTNULL`. Seuls les ordinateurs Windows 10 remplissent cet attribut. Tous les objets d’ordinateur avec une valeur de cet attribut sont synchronisés.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Comprendre le scénario de règles d’out-of-box
Dans cet exemple, nous utilisons un déploiement avec une seule forêt compte (A), une forêt de ressources (R) et un répertoire AD Azure.

![Image avec description de scénario](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

Dans cette configuration, il est supposé qu'est un compte activé dans la forêt de compte et un compte désactivé dans la forêt de ressources avec une boîte aux lettres liée.

L’objectif de la configuration par défaut est :

- Attributs liés à la connexion sont synchronisés à partir de la forêt et le compte activé.
- Les attributs que vous trouverez dans la liste d’adresses globale (liste d’adresses globale) sont synchronisés à partir de la forêt avec la boîte aux lettres. Si aucune boîte aux lettres ne peut être trouvé, aucune autre forêt est utilisé.
- Si une boîte aux lettres liée est trouvée, le compte activé lié doit être trouvé pour l’objet à exporter vers Azure AD.

### <a name="synchronization-rule-editor"></a>Éditeur de règles de synchronisation
La configuration peut être affichée et modifiée à l’aide de l’outil Éditeur de règles de synchronisation (SRE) et son raccourci se trouve dans le menu Démarrer.

![Icône d’éditeur de règles de synchronisation](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

Le SRE est un outil du kit de ressources, et il est installé avec la synchronisation Azure Connect d’Active Directory. Pour être en mesure de le démarrer, vous devez être un membre du groupe ADSyncAdmins. Lorsqu’il démarre, vous voyez quelque chose comme ceci :

![Règles de synchronisation entrants](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Dans ce volet, vous voyez toutes les règles de synchronisation créées pour votre configuration. Chaque ligne de la table est une règle de synchronisation. À gauche, sous Types de règles, les deux types sont répertoriés : entrant et sortant. Entrant et sortant est à partir de la vue du méta-verse. Vous allez surtout de se concentrer sur les règles de trafic entrant dans cette vue d’ensemble. La liste des règles de synchronisation dépend du schéma détecté dans Active Directory. Dans l’illustration ci-dessus, la forêt de comptes (fabrikamonline.com) ne dispose pas de tous les services, comme Exchange et Lync, et aucune règle de synchronisation n’ont été créés pour ces services. Toutefois, dans la forêt de ressources (res.fabrikamonline.com), vous trouvez les règles de synchronisation pour ces services. Le contenu des règles est différent selon la version détectée. Par exemple, dans un déploiement Exchange 2013, il existe plusieurs flux d’attributs configurés que dans Exchange 2010/2007.

### <a name="synchronization-rule"></a>Règles de synchronisation
Une règle de synchronisation est un objet de configuration avec un jeu d’attributs qui circulent lorsqu’une condition est satisfaite. Il est également utilisé pour décrire comment un objet dans un espace de connecteur est lié à un objet dans le méta-verse, appelé **jointure** ou **faire correspondre**. Les règles de synchronisation ont une valeur de priorité indiquant la façon dont ils sont associés entre eux. Une règle de synchronisation avec une valeur numérique inférieure a une priorité plus élevée, et dans un conflit de flux d’attribut, une priorité plus élevée gagne la résolution de conflit.

Par exemple, regardez la règle de synchronisation **dans à partir d’Active Directory – utilisateur AccountEnabled**. Marquer cette ligne dans le SRE et sélectionnez **Modifier**.

Dans la mesure où cette règle est une règle d’out-of-box, vous recevez un message d’avertissement lorsque vous ouvrez la règle. Vous ne devez pas vous toutes les [modifications apportées aux règles de prêts à l’emploi](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), afin que le système vous demande quelles sont vos intentions. Dans ce cas, vous souhaitez uniquement afficher la règle. Cliquez sur **non**.

![Synchronisation règles d’avertissement](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Une règle de synchronisation comprend quatre sections de configuration : Description, filtre, règles de jointure et les Transformations de portée.

#### <a name="description"></a>Description
La première section fournit des informations de base telles que le nom et la description.

![Description onglet Éditeur de règles de synchronisation ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Vous trouverez des informations concernant le système connecté cette règle est associée, dont le type d’objet dans le système connecté, à qu'il s’applique et le type d’objet du métaverse. Le type d’objet du métaverse est toujours sans tenir compte de la personne lorsque le type d’objet source est un utilisateur, iNetOrgPerson ou contact. Le type d’objet du métaverse ne doit jamais changer de sorte qu’il est créé sous la forme d’un type générique. Le Type de lien peut être défini à la jointure, de StickyJoin ou de disposition. Ce paramètre fonctionne conjointement avec la section règles de jointure et est traité ultérieurement.

Vous pouvez également voir que cette règle de synchronisation est utilisée pour la synchronisation de mot de passe. Si un utilisateur est dans la portée de cette règle de synchronisation, le mot de passe est synchronisé sur site au nuage (en supposant que vous avez activé la fonctionnalité de synchronisation de mot de passe).

#### <a name="scoping-filter"></a>Filtre d’étendue
La section de la portée d’un filtre permet de configurer lors d’une synchronisation de règle doit s’appliquer. Dans la mesure où le nom de la règle de synchronisation que vous examinez indique qu’il doit être appliqué uniquement pour les utilisateurs activés, l’étendue est configurée pour l' attribut de publicité **userAccountControl** ne doit pas avoir le bit 2 définie. Lorsque le moteur de synchronisation détecte un utilisateur dans Active Directory, il s’applique cette règle de synchronisation lorsque **userAccountControl** est définie à la valeur décimale 512 (utilisateur normal activé). Il ne s’applique pas la règle lorsque l’utilisateur a **userAccountControl** définie sur 514 (utilisateur normal désactivé).

![Onglet étendue dans l’éditeur de règle de synchronisation ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

La portée du filtre a les Clauses qui peuvent être imbriqués et les groupes. Toutes les clauses à l’intérieur d’un groupe doivent être satisfaites pour une règle de synchronisation à appliquer. Lorsque plusieurs groupes sont définis, au moins un groupe doit être remplie pour appliquer la règle. C'est-à-dire un ou logique est évalué entre les groupes et une logique et est évalué à l’intérieur d’un groupe. Vous trouverez un exemple de cette configuration dans de synchronisation règle sortante **à DAS – Group Join**. Il existe plusieurs groupes de filtre de synchronisation, par exemple, une pour les groupes de sécurité (`securityEnabled EQUAL True`) et l’autre pour les groupes de distribution (`securityEnabled EQUAL False`).

![Onglet étendue dans l’éditeur de règle de synchronisation ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Cette règle est utilisée pour définir les groupes doivent être provisionnées sur Azure AD. Les groupes de distribution doivent être activé pour être synchronisé avec l’annonce Azure pour la messagerie, mais pour les groupes de sécurité, un message électronique n’est pas obligatoire.

#### <a name="join-rules"></a>Les règles de jointure
La troisième section permet de configurer comment les objets dans l’espace de connecteur sont liées aux objets du métaverse. La règle que vous avez étudié précédemment n’a pas toutes les configurations pour les règles de jointure, au lieu de cela vous vous apprêtez à regarder **dans à partir d’Active Directory – utilisateur joindre**.

![Joindre l’onglet règles dans l’éditeur de règle de synchronisation ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Le contenu de la règle de jointure dépend de l’option correspondante dans l’Assistant d’installation. Pour une règle de trafic entrant, l’évaluation commence par un objet dans l’espace de connecteur de source et de chaque groupe dans les règles de jointure est évaluée dans l’ordre. Si un objet source est évalué pour le correspond à exactement un objet dans le méta-verse, à l’aide de l’une des règles de jointure, les objets sont jointes. Si toutes les règles ont été évaluées, et il n’y a aucune correspondance, le Type de lien sur la page de description est utilisé. Si cette configuration est à la **disposition**, un nouvel objet est créé dans la cible, le métaverse. Pour mettre en service un nouvel objet dans le métaverse est également connu sous le nom de **projet** un objet dans le métaverse.

Les règles de jointure ne sont évaluées qu’une seule fois. Lorsqu’un objet espace de connecteur et un objet du méta-verse sont jointes, elles restent jointes tant que la portée de la règle de synchronisation est toujours satisfaite.

Lors de l’évaluation des règles de synchronisation, une seule règle de synchronisation avec les règles de jointure définies doit être dans la portée. Si plusieurs règles de synchronisation avec les règles de jointure est trouvées pour un objet, une erreur est levée. Pour cette raison, la meilleure solution consiste à n'avoir qu’une seule règle de synchronisation avec la jointure définie lorsque plusieurs règles de synchronisation sont dans la portée d’un objet. Dans la configuration d’out-of-box pour la synchronisation Azure Connect d’Active Directory, ces règles peuvent être trouvée en recherchant le nom et trouver les avec le mot à **joindre** à la fin du nom. Une règle de synchronisation sans aucune règle de jointure défini s’applique à l’attribut de flux lorsqu’une autre règle de synchronisation de joindre les objets ou mis en service d’un nouvel objet dans la cible.

Si vous examinez l’illustration ci-dessus, vous pouvez voir que la règle tente de joindre **objectSID** avec **msExchMasterAccountSid** (Exchange) et **msRTCSIP-OriginatorSid** (Lync), qui est ce que nous attendre dans une topologie à forêt compte-ressource. Vous trouvez la même règle sur toutes les forêts. Il est supposé que chaque forêt peut être soit un compte ou une ressource de forêt. Cette configuration fonctionne également si vous avez des comptes qui résident dans une forêt unique et ne doivent pas être jointes.

#### <a name="transformations"></a>Transformations
La section transformation définit tous les flux d’attributs qui s’appliquent à l’objet cible lorsque les objets sont joints et que l’étendue du filtre est satisfait. Pour revenir à la **dans à partir d’Active Directory – utilisateur AccountEnabled** règle de synchronisation, vous trouvez les transformations suivantes :

![Transformations onglet Éditeur de règles de synchronisation ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Pour mettre cette configuration dans un contexte, dans un déploiement de forêt compte-ressource, il est prévu pour rechercher un compte activé dans la forêt de compte et un compte désactivé dans la forêt de ressources avec les paramètres Exchange et Lync. La règle de synchronisation que vous regardez contient les attributs requis pour la connexion et ces attributs doivent s’écouler depuis la forêt où il existe un compte activé. Tous les flux de ces attributs sont réunis dans une seule règle de synchronisation.

Une transformation peut avoir différents types : constante, Direct et Expression.

- Un flux constant passe toujours une valeur codée en dur. Dans ce cas, il définit toujours la valeur **True** dans le métaverse attribut nommé **accountEnabled**.
- Un flux direct est toujours transmise la valeur de l’attribut de la source à l’attribut cible en tant que-est.
- Le troisième type de flux est Expression, et qu’il permet des configurations plus avancées.

Le langage des expressions est de VBA (Visual Basic pour Applications), ainsi, les personnes ayant une expérience de Microsoft Office ou VBScript reconnaît le format. Les attributs sont placés entre crochets, [Nom_attribut]. Les noms d’attributs et les noms de fonction respectent la casse, mais l’éditeur de règles de synchronisation évalue les expressions et fournir un avertissement si l’expression n’est pas valide. Toutes les expressions sont exprimées sur une seule ligne avec des fonctions imbriquées. Pour afficher la puissance de la langue de configuration, voici le flux pour pwdLastSet, mais avec des commentaires supplémentaires insérés :

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Pour plus d’informations sur le langage des expressions pour les flux d’attributs, consultez [Présentation des Expressions déclaratives mise en service](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

### <a name="precedence"></a>Priorité
Vous avez maintenant recherché dans certaines règles de synchronisation individuelles, mais les règles fonctionnent ensemble dans la configuration. Dans certains cas, une valeur d’attribut est contribuée à partir de plusieurs règles de synchronisation pour le même attribut cible. Dans ce cas, la priorité des attributs est utilisée pour déterminer quel attribut wins. Par exemple, examinez l’attribut sourceAnchor. Cet attribut est un attribut important d’être en mesure de se connecter à Active Directory Azure. Vous trouverez un flux d’attribut de cet attribut dans deux règles de synchronisation différents, **dans à partir d’Active Directory – utilisateur AccountEnabled** et **dans à partir d’Active Directory – utilisateur courant**. En raison de la priorité des règles de synchronisation, l’attribut sourceAnchor est contribué à partir de la forêt avec un compte activé tout d’abord lorsqu’il y a plusieurs objets liés à l’objet du métaverse. Si aucun compte activé, alors que le moteur de synchronisation utilise la règle de synchronisation à tout **dans à partir d’Active Directory – utilisateur courant**. Cette configuration garantit que même pour les comptes qui sont désactivés, il existe toujours un sourceAnchor.

![Règles de synchronisation entrants](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

La priorité des règles de synchronisation est définie dans les groupes par l’Assistant d’installation. Toutes les règles d’un groupe ont le même nom, mais ils sont connectés à différents annuaires connectés. L’Assistant d’installation donne à la règle **dans à partir d’Active Directory – utilisateur joindre** plus haute priorité et il effectue une itération sur tous les connecté les répertoires AD. Ensuite, il continue avec les groupes suivant des règles dans un ordre prédéfini. À l’intérieur d’un groupe, les règles sont ajoutées dans l’ordre que les connecteurs ont été ajoutées dans l’Assistant. Si un autre connecteur est ajouté par le biais de l’Assistant, les règles de synchronisation sont réorganisées et les règles du nouveau connecteur sont insérées en dernier dans chaque groupe.

### <a name="putting-it-all-together"></a>Vue d’ensemble
Nous savons maintenant suffisamment sur les règles de synchronisation pour être en mesure de comprendre le fonctionne de la configuration avec les différentes règles de synchronisation. Si vous regardez un utilisateur et les attributs qui sont fournis dans le métaverse, les règles sont appliquées dans l’ordre suivant :

Nom | Commentaire
:------------- | :-------------
Dans à partir d’Active Directory – la jointure de l’utilisateur | Règle d’assemblage des objets espace de connecteur avec le métaverse.
Dans à partir d’Active Directory – Compte_utilisateur activé | Attributs obligatoires pour la connexion à Active Directory Azure et Office 365. Nous voulons que ces attributs à partir du compte activé.
Dans à partir d’Active Directory – commun d’utilisateur à partir d’Exchange | Attributs figurant dans la liste d’adresses globale. Nous supposons que la qualité des données est préférable dans la forêt où nous avons trouvé des boîtes aux lettres de l’utilisateur.
Dans à partir d’Active Directory – utilisateur commun | Attributs figurant dans la liste d’adresses globale. Dans le cas où nous n’avons trouvé une boîte aux lettres, n’importe quel autre objet joint peut contribuer à la valeur de l’attribut.
Dans à partir d’Active Directory – Exchange de l’utilisateur | N’existe que si Exchange a été détecté. Il passe tous les attributs Exchange d’infrastructure.
Dans à partir d’Active Directory – utilisateur Lync | N’existe que si Lync a été détecté. Il passe tous les attributs de Lync d’infrastructure.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur le modèle de configuration de [Mise en service déclaratif de présentation](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Apprenez-en plus sur le langage des expressions dans la [Compréhension des Expressions déclaratives mise en service](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Poursuivre la lecture de fonctionne de la configuration prête à l’emploi dans la [compréhension des utilisateurs et des Contacts](active-directory-aadconnectsync-understanding-users-and-contacts.md)
- Voir comment apporter une modification pratique à l’aide de la mise en service de manière déclarative comment [apporter une modification à la configuration par défaut](active-directory-aadconnectsync-change-the-configuration.md).

**Rubriques de vue d’ensemble**

- [Azure Connect de AD sync : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
