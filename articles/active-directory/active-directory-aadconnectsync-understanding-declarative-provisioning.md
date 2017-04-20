<properties
    pageTitle="Azure Connect de AD sync : présentation de la mise en service déclarative | Microsoft Azure"
    description="Explique le modèle de configuration de mise en service déclaratif dans Azure Connect d’Active Directory."
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
    ms.date="08/29/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure Connect de AD sync : présentation de la mise en service déclarative
Cette rubrique explique le modèle de configuration dans Azure Connect d’Active Directory. Le modèle est appelé mise en service déclarative et permet de modifier une configuration en toute simplicité. Beaucoup de choses décrites dans cette rubrique est avancés et n’est nécessaire pour la plupart des scénarios de client.

## <a name="overview"></a>Vue d’ensemble
Provisionnement déclarative est le traitement des objets provenant d’un annuaire connecté source et détermine comment l’objet et les attributs doivent être transformées à partir d’une source vers une cible. Traitement d’un objet dans un pipeline de synchronisation et le tuyau est la même pour des règles entrantes et sortantes. Une règle de trafic entrant provient d’un espace de connecteur dans le métaverse et une règle sortante est du métaverse à un espace de connecteur.

![Pipeline de synchronisation](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

Le pipeline a plusieurs modules. Chacun d’eux est responsable pour un concept de la synchronisation de l’objet.

![Pipeline de synchronisation](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

- Source de l’objet source
- [Portée](#scope), recherche toutes les règles de synchronisation qui sont dans la portée
- [Joindre](#join), détermine les relations entre l’espace de connecteur et méta-verse
- [Transformer](#transform), calcule comment les attributs doivent être transformées et flux
- [Priorité](#precedence), résout les contributions d’attribut en conflit
- Cible, l’objet cible

## <a name="scope"></a>Champ d’application
Le module de portée évalue un objet et détermine les règles qui sont dans la portée et doivent être inclus dans le traitement. Selon les valeurs d’attributs sur l’objet, les règles de synchronisation différents sont évaluées pour être dans la portée. Par exemple, un utilisateur désactivé avec aucune boîte aux lettres Exchange possède des règles différentes à un utilisateur activé avec une boîte aux lettres.  
![Champ d’application](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

La portée est définie comme les clauses et les groupes. Les clauses sont à l’intérieur d’un groupe. Un et logique est utilisé entre toutes les clauses dans un groupe. Par exemple, (département = informatique et pays = Danemark). Un opérateur logique OR est utilisé entre les groupes.

![Champ d’application](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
La portée de cette image doit être lu en tant que (department = informatique et pays = Danemark) ou (pays = Suède). Si le groupe 1 ou du groupe 2 est évalué à true, alors que la règle est dans la portée.

Le module de portée prend en charge les opérations suivantes.

Opération | Description
--- | ---
ÉGAL, NOTEQUAL | Une comparaison de chaînes qui évalue si la valeur est égale à la valeur de l’attribut. Pour les attributs à valeurs multiples, consultez ISIN et ISNOTIN.
LESSTHAN, LESSTHAN_OR_EQUAL | Une comparaison de chaînes qui évalue si la valeur est inférieure à la valeur dans l’attribut.
CONTIENT, NOTCONTAINS | Une comparaison de chaînes qui évalue si la valeur peut être trouvée quelque part dans la valeur de l’attribut.
STARTSWITH, NOTSTARTSWITH | Une comparaison de chaînes qui évalue si la valeur est au début de la valeur de l’attribut.
ENDSWITH, NOTENDSWITH | Une comparaison de chaînes qui évalue si la valeur est à la fin de la valeur de l’attribut.
GREATERTHAN, GREATERTHAN_OR_EQUAL | Une comparaison de chaînes qui évalue si la valeur est supérieure à la valeur de l’attribut.
ISNULL, ISNOTNULL | Évalue si l’attribut est absent de l’objet. Si l’attribut n’est pas présente et donc null, la règle est dans la portée.
ISIN, ISNOTIN | Évalue si la valeur est présente dans l’attribut défini. Cette opération est la variation à valeurs multiples d’égal et NOTEQUAL. L’attribut est censé pour être un attribut à valeurs multiples et si la valeur peut être trouvée dans une des valeurs d’attributs, puis la règle est dans la portée.
ISBITSET, ISNOTBITSET | Évalue si un bit précis est défini. Par exemple, peut être utilisé pour évaluer les bits dans userAccountControl pour voir si un utilisateur est activé ou désactivé.
ISMEMBEROF, ISNOTMEMBEROF | La valeur doit contenir un nom de domaine à un groupe dans l’espace de connecteur. Si l’objet est un membre du groupe spécifié, la règle est dans la portée.

## <a name="join"></a>Jointure
Le module de jointure dans le pipeline de synchronisation est responsable de la recherche de la relation entre l’objet de la source et un objet de la cible. Dans une règle de trafic entrant, cette relation est un objet dans un espace de connecteur recherche une relation à un objet dans le méta-verse.  
![Jointure entre cs et mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
L’objectif est de voir que si un objet est déjà dans le méta-verse, créé par un autre connecteur, il doit être associé. Par exemple, dans une forêt de ressources-compte l’utilisateur à partir de la forêt de comptes doit être jointes à l’utilisateur de la forêt ressource.

Les jointures sont utilisés principalement sur les règles de trafic entrant pour joindre les objets espace de connecteur pour le même objet du méta-verse.

Les jointures sont définies sous la forme d’un ou plusieurs groupes. À l’intérieur d’un groupe, vous avez des clauses. Un et logique est utilisé entre toutes les clauses dans un groupe. Un opérateur logique OR est utilisé entre les groupes. Les groupes sont traités dans l’ordre de haut en bas. Lorsqu’un groupe a trouvé exactement une correspondance avec un objet de la cible, aucune autre règle de jointure n’est évaluées. Si zéro ou plus d’un objet est trouvé, le traitement se poursuit au groupe de règles. Pour cette raison, les règles doivent être créées dans l’ordre premier plus explicite et plus flou à la fin.  
![Définition de jointure](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Les jointures dans cette image sont traités de haut en bas. Tout d’abord le pipeline de synchronisation détecte s’il existe une correspondance sur employeeID. Si ce n’est pas le cas, la deuxième règle détecte si le nom de compte peut être utilisé pour joindre les objets. Si tel n’est pas soit une correspondance, la règle de la troisième et dernière correspond plus floue en utilisant le nom de l’utilisateur.

Si toutes les règles de jointure ont été évalués et il n’est pas exactement une correspondance, le **Type de lien** sur la page de **Description** est utilisé. Si cette option est définie à la **disposition**, un nouvel objet de la cible est créé.  
![Mise à disposition ou la jointure](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Un objet doit uniquement avoir une seule synchronisation règle avec les règles de jointure dans la portée. S’il existe plusieurs règles de synchronisation pour laquelle la jointure est définie, une erreur se produit. Priorité n’est pas utilisée pour résoudre les conflits de jointure. Un objet doit avoir une règle de jointure dans la portée pour les attributs de flux avec le même sens entrant et sortant. Si vous avez besoin pour les attributs de flux entrant et sortant au même objet, vous devez disposer à la fois entrante et une règle de synchronisation sortants avec une jointure.

Jointure sortant a un comportement spécial lorsqu’il essaie de mettre en service un objet à un espace de connecteur de cible. L’attribut de nom unique est utilisé d’abord une jointure inverse. Si un objet existe déjà dans l’espace de connecteur cible avec le même nom de domaine, les objets sont jointes.

Le module de jointure n’est évalué qu’une fois lorsqu’une nouvelle règle de synchronisation est fourni dans la portée. Lorsqu’un objet est joint, il n’est pas disjonction même si les critères de jointure n’est plus remplie. Si vous souhaitez disjoindre un objet, la règle de synchronisation qui joint les objets doit hors de portée.

### <a name="metaverse-delete"></a>Suppression du métaverse
Un objet du méta-verse reste en tant qu’est une règle de synchronisation dans la portée avec le **Type de lien** la valeur mise à **disposition** ou **StickyJoin**. Un StickyJoin est utilisé lorsqu’un connecteur n’est pas autorisé à mettre en service un nouvel objet dans le métaverse, mais lorsqu’il est attaché, il doit être supprimé de la source avant la suppression de l’objet du métaverse.

Lorsqu’un objet du méta-verse est supprimé, tous les objets associés à une règle de synchronisation sortants marquée pour la **disposition** sont marqués pour suppression.

## <a name="transformations"></a>Transformations
Les transformations sont utilisées pour définir le doivent flux des attributs à partir de la source vers la cible. Les flux peuvent avoir un des **types de flux**suivants : Direct, constante ou Expression. Une valeur d’attribut en tant que flux d’un flux direct,-est sans transformations supplémentaires. Une valeur de constante définit la valeur spécifiée. Une expression utilise le langage d’expression de mise en service déclarative pour exprimer la façon dont la transformation doit être. Vous trouverez les détails pour le langage des expressions dans la rubrique de [langage d’expression provisionnement déclarative compréhension](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

![Mise à disposition ou la jointure](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

La case à cocher **appliquer une fois** définit l’attribut doit être défini uniquement lorsque l’objet est créé initialement. Par exemple, cette configuration peut être utilisée pour définir un mot de passe initial d’un nouvel objet utilisateur.

### <a name="merging-attribute-values"></a>Fusion de valeurs d’attribut
Dans les flux d’attributs, il existe un paramètre afin de déterminer si les attributs à valeurs multiples doivent être fusionnées à partir de plusieurs connecteurs de différents. La valeur par défaut est **mise à jour**, qui indique que la règle de la synchronisation avec la priorité la plus élevée doit gagner.

![Types de fusion](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Il est également **Fusionner** et **MergeCaseInsensitive**. Ces options vous permettent de fusionner les valeurs à partir de différentes sources. Par exemple, il peut être utilisé pour fusionner l’attribut proxyAddresses ou de membres à partir de plusieurs forêts différentes. Lorsque vous utilisez cette option, toutes les règles de synchronisation dans la portée d’un objet doivent utiliser le même type de fusion. Vous ne pouvez pas définir **mise à jour** à partir d’un connecteur et la **fusion** à partir d’un autre. Si vous essayez, vous recevez une erreur.

La différence entre **Fusionner** et **MergeCaseInsensitive** est le traitement des valeurs d’attribut en double. Le moteur de synchronisation permet de s’assurer que des valeurs en double ne sont pas insérées dans l’attribut cible. Avec **MergeCaseInsensitive**, des valeurs en double avec seulement une différence dans le cas où ne vont ne pas être présents. Par exemple, vous ne devriez pas voir les deux "SMTP:bob@contoso.com" et "smtp:bob@contoso.com" dans l’attribut cible. **Fusionner** uniquement examine les valeurs exactes et de plusieurs valeurs où existe uniquement une différence de casse peut être présente.

L’option **Remplacer** est le même que la **mise à jour**, mais il n’est pas utilisé.

### <a name="control-the-attribute-flow-process"></a>Contrôle le processus de flux des attributs
Lorsque plusieurs règles de synchronisation entrantes sont configurées pour contribuer à l’attribut du métaverse même, priorité est utilisée pour déterminer le vainqueur. La règle de la synchronisation avec la priorité la plus élevée (valeur numérique la plus basse) va contribuer la valeur. Le même se produit pour les règles de trafic sortant. La synchronisation avec wins de priorité le plus élevés de la règle et contribute de la valeur à l’annuaire connecté.

Dans certains cas, au lieu de contribuer d’une valeur, la règle de synchronisation doit déterminer le comportement des autres règles. Il existe certains littéraux spéciales utilisés pour ce cas.

Pour les règles de synchronisation entrante, le **NULL** littéral peut servir à indiquer que le flux n’a aucune valeur de contribuer. Une autre règle de priorité inférieure peut contribuer à une valeur. Si aucune règle n’a contribué à une valeur, l’attribut du métaverse est supprimé. Pour une règle sortante, si **NULL** est la valeur finale après le traitement de toutes les règles de synchronisation, la valeur est alors supprimée de l’annuaire connecté.

littéral **AuthoritativeNull** est similaire, mais à la différence qu’aucune règle de priorité inférieure ne peut contribuer à une valeur **null** .

Un flux d’attribut permet également de **IgnoreThisFlow**. Il est similaire à la valeur NULL dans le sens où il indique rien à contribuer. La différence est qu’il ne supprime pas une valeur déjà existante dans la cible. C’est comme le flux des attributs n’a jamais été il.

Voici un exemple :

Vous pouvez trouver le flux suivant dans *les annonces - hybride d’Exchange de l’utilisateur* :  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Cette expression doit être lu comme : si la boîte aux lettres de l’utilisateur se trouve dans Active Directory Azure, puis transmettre l’attribut Azure publicité pour la publicité. Si ce n’est pas le cas, ne passent pas quoi que ce soit à Active Directory. Dans ce cas, il conserve la valeur existante dans Active Directory.

### <a name="importedvalue"></a>ImportedValue
La fonction ImportedValue est différente de celle de toutes les autres fonctions, dans la mesure où le nom d’attribut doit être placé entre les guillemets au lieu de crochets :  
`ImportedValue("proxyAddresses")`.

Généralement lors de la synchronisation un attribut utilise la valeur attendue, même si elle n’a pas encore été exporté ou si une erreur a été reçue lors de l’exportation (« en haut de la tour »). Une synchronisation entrante suppose qu’un attribut qui n’a pas encore atteint un annuaire connecté éventuellement atteint. Dans certains cas, il est important de ne synchroniser qu’une valeur a été confirmée par l’annuaire connecté (« hologramme et delta importer tour).

Vous trouverez un exemple de cette fonction dans la règle de synchronisation out-of-box *dans à partir d’Active Directory – utilisateur courantes à partir d’Exchange*. En échange de l’hybride, la valeur ajoutée par Exchange en ligne doit être synchronisée uniquement lorsqu’il a été confirmé que la valeur a été exportée avec succès :  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Priorité
Lorsque plusieurs règles de synchronisation essaie de contribute de la même valeur d’attribut à la cible, la valeur de priorité est utilisée pour déterminer le vainqueur. La règle de priorité la plus élevée, plus petite valeur numérique, va contribuer l’attribut dans un conflit.

![Types de fusion](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Cette commande permet de définir plus précisément les flux d’attributs pour un petit sous-ensemble des objets. Par exemple, out-de-zone-rules Assurez-vous que les attributs d’un compte activé (**Utilisateur AccountEnabled**) ont une priorité d’autres comptes.

Priorité peut être définie entre des connecteurs. Qui autorise les connecteurs avec données mieux contribuer tout d’abord les valeurs.

### <a name="multiple-objects-from-the-same-connector-space"></a>Plusieurs objets dans le même espace de connecteur
Si vous avez plusieurs objets dans le même espace de connecteur lié au même objet du méta-verse, la priorité doit être ajustée. Si plusieurs objets sont dans la portée de la même règle de synchronisation, le moteur de synchronisation n’est pas en mesure de déterminer la priorité. Il est ambigu de l’objet source doit contribuer à la valeur dans le métaverse. Cette configuration est signalée comme étant ambigu, même si les attributs de la source ont la même valeur.  
![Plusieurs objets liés au même objet mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

Dans ce scénario, vous devez modifier la portée des règles de synchronisation pour les objets de la source ont des règles de synchronisation différent dans la portée. Qui vous permet de définir la priorité de différente.  
![Plusieurs objets liés au même objet mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur le langage des expressions dans la [Compréhension des Expressions déclaratives mise en service](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Voir comment déclarative le provisionnement est utilisé out-of-box pour [comprendre la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md).
- Voir comment apporter une modification pratique à l’aide de la mise en service de manière déclarative comment [apporter une modification à la configuration par défaut](active-directory-aadconnectsync-change-the-configuration.md).
- Continuer à lire le fonctionnement des utilisateurs et contacts dans la [compréhension des utilisateurs et des Contacts](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Rubriques de vue d’ensemble**

- [Azure Connect de AD sync : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)

**Rubriques de référence**

- [Azure Connect de AD sync : référence des fonctions](active-directory-aadconnectsync-functions-reference.md)
