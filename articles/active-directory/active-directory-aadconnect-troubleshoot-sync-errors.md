<properties
    pageTitle="Connexion de publicité Azure : Dépannage des erreurs lors de la synchronisation | Microsoft Azure"
    description="Explique comment résoudre les erreurs rencontrées lors de la synchronisation avec Azure Connect d’Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="troubleshooting-errors-during-synchronization"></a>Dépannage des erreurs lors de la synchronisation
Erreurs peuvent se produire lors de la synchronisation des données d’identité à partir de Windows Server Active Directory (AD DS) pour Azure Active Directory (AD Azure). Cet article fournit une vue d’ensemble des différents types d’erreurs de synchronisation, certains des scénarios qui provoquent ces erreurs et les méthodes pour corriger les erreurs. Cet article inclut les types d’erreur courants et ne peut pas couvrir toutes les erreurs possibles.

 Cet article suppose que le lecteur est familiarisé avec sous-jacent [concevoir des concepts de publicité Azure et d’Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Avec la dernière version de Azure Connect de AD \(août 2016 ou supérieur\), un rapport d’erreurs de synchronisation est disponible sur le [Portail Azure](https://aka.ms/aadconnecthealth) dans le cadre d’Azure AD connecter la santé pour la synchronisation.


Commençant le 1 septembre 2016 Azure Active Directory dupliquer attribut fonctionnalité de [Permanence](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) sera activée par défaut pour tous les *nouveaux* Azure Active Directory locataires. Cette fonction sera automatiquement activée aux locataires existants dans les mois à venir.

Azure Connect d’Active Directory effectue des 3 types d’opérations à partir des répertoires qu’il reste synchronisée : synchronisation, importation et exportation. Erreurs peuvent avoir lieu dans toutes les opérations. Cet article se concentre principalement sur les erreurs lors de l’exportation vers Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Erreurs lors de l’exportation vers Azure AD
Après section décrit les différents types d’erreurs de synchronisation qui peuvent se produire au cours de l’opération d’exportation pour Azure AD en utilisant le connecteur Active Directory Azure. Ce connecteur peut être identifié par le format du nom est « contoso. *onmicrosoft.com*».
Erreurs lors de l’exportation vers Azure AD indiquent que l’opération de \(ajouter, mettre à jour, supprimer etc.\) tenté d’Azure Connect de AD \(moteur de synchronisation de\) sur Azure Active Directory a échoué.

![Présentation des erreurs de l’exportation](.\media\active-directory-aadconnect-troubleshoot-sync-errors\Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Erreurs d’incompatibilité de données
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Description
- Lorsque Active Directory Azure se connecter \(moteur de synchronisation\) indique à Azure Active Directory pour ajouter ou mettre à jour les objets, AD Azure correspond à l’objet entrant à l’aide de l’attribut **sourceAnchor** à l’attribut **immutableId** des objets dans Active Directory Azure. Cette correspondance est appelée une **Correspondance de disque dur**.
- Lorsque l’annonce Azure **ne trouve pas de** n’importe quel objet qui correspond à **immutableId** d’attribut avec l’attribut **sourceAnchor** de l’objet entrant, avant la mise en service d’un nouvel objet, il se replie pour utiliser les attributs ProxyAddresses et UserPrincipalName pour trouver une correspondance. Cette correspondance est appelée une **Correspondance souple**. Le logiciel correspond à est conçu pour faire correspondre des objets déjà présents dans Active Directory Azure (qui sont générés dans Azure AD) avec les nouveaux objets sont ajoutés ou mis à jour lors de la synchronisation qui représentent la même entité (utilisateurs, groupes) dans les locaux.
- **InvalidSoftMatch** erreur lors de la correspondance exacte ne trouve pas de n’importe quel objet correspondant **et** soft correspond à recherche un objet correspondant, mais cet objet a une valeur différente *immutableId* à *SourceAnchor*, suggérant que l’objet correspondant a été synchronisé avec un autre objet à partir de site Active Directory l’objet entrant.

En d’autres termes, la correspondance logicielle fonctionne, l’objet doit être mis en correspondance souple avec ne doit pas avoir n’importe quelle valeur pour *immutableId*. Si la valeur de n’importe quel objet avec *immutableId* avec une valeur est Échec de la correspondance-dur mais répondant à des critères des soft-correspondance, que l’opération aurait pour résultat une erreur de synchronisation InvalidSoftMatch.

Schéma d’Active Directory Azure n’autorise pas deux ou plusieurs objets ont la même valeur des attributs suivants. \(Il ne s’agit pas d’une liste exhaustive.\)

- ProxyAddresses
- UserPrincipalName
- onPremisesSecurityIdentifier
- ObjectId

>[AZURE.NOTE] Fonctionnalité de [résilience d’attribut Azure AD attribut dupliqué](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) est également déployée comme comportement par défaut d’Azure Active Directory.  Cela réduira le nombre d’erreurs de synchronisation vu par Azure AD Connect (ainsi que d’autres clients de synchronisation) en rendant AD Azure plus souple dans son mode de que gestion dupliqués attributs ProxyAddresses et UserPrincipalName présents dans les environnements locaux AD. Cette fonctionnalité ne corrige pas les erreurs de duplication. Les données doivent donc être résolu. Mais elle permet la mise en service de nouveaux objets qui sont bloqués dans le cas contraire est mis en service en raison des valeurs dupliquées dans Azure AD. Cela réduira également le nombre d’erreurs de synchronisation retournée vers le client de synchronisation.
Si cette fonctionnalité est activée pour vos clients, vous ne verrez pas les erreurs de synchronisation InvalidSoftMatch vus au cours de la mise en service de nouveaux objets.


#### <a name="example-scenarios-for-invalidsoftmatch"></a>Scénarios d’exemple pour InvalidSoftMatch
1. Il existe dans deux ou plusieurs objets ayant la même valeur de l’attribut ProxyAddresses, dans les locaux de Active Directory. Seul l’obtention configuré dans Azure AD.
2. Il existe dans deux ou plusieurs objets ayant la même valeur d’userPrincipalName dans les locaux de Active Directory. Seul l’obtention configuré dans Azure AD.
3. Un objet a été ajouté dans les locaux de Active Directory avec la même valeur de l’attribut ProxyAddresses, comme celle d’un objet existant dans Active Directory de Azure. L’objet ajouté dans les locaux n’est pas mise en route configuré dans Azure Active Directory.
4. Un objet a été ajouté dans les locaux de Active Directory avec la même valeur de l’attribut userPrincipalName que celui d’un compte dans Active Directory de Azure. L’objet n’est pas mise en route configuré dans Azure Active Directory.
5. Un compte synchronisé a été déplacé à partir de la forêt A à la forêt B. Azure AD Connect (moteur de synchronisation) pour calculer le SourceAnchor a été à l’aide d’attribut ObjectGUID. Après le déplacement de la forêt, la valeur de la SourceAnchor est différente. Le nouvel objet (à partir de forêt B) ne peut pas synchroniser avec l’objet existant dans Active Directory Azure.
6. Un objet synchronisé a été accidentellement supprimé dans les locaux de Active Directory et un nouvel objet a été créé dans Active Directory pour la même entité (utilisateur) sans supprimer le compte dans Azure Active Directory. Le nouveau compte ne parvient pas à synchroniser avec l’objet AD Azure existante.
7. Azure AD Connect a été désinstallé et réinstallé. Au cours de la réinstallation, un attribut différent a été choisi comme le SourceAnchor. Tous les objets qui avaient précédemment synchronisées arrêté la synchronisation avec l’erreur de InvalidSoftMatch.

#### <a name="example-case"></a>Exemple de cas :
1. **Bob Smith** est un utilisateur synchronisé dans Azure Active Directory sur le site Active Directory de *contoso.com*
2. De Bob Smith **UserPrincipalName** est défini en tant que **bobs@contoso.com**.
3. **« abcdefghijklmnopqrstuv == »** est la **SourceAnchor** calculé par Azure Connect AD à l’aide de Bob Smith **objectGUID** sous locaux Active Directory, qui est l' **immutableId** de Bob Smith dans Azure Active Directory.
4. Bob a également suivant les valeurs de l’attribut **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
5.  Un nouvel utilisateur, **Bob Taylor**, est ajouté à l’entreprise dans Active Directory.
6. de Bob Taylor **UserPrincipalName** est défini en tant que **bobt@contoso.com**.
7. **« abcdefghijkl0123456789 == " »** est la **sourceAnchor** calculé par Azure AD se connecter en utilisant de Bob Taylor **objectGUID** sous locaux Active Directory. Objet de Bob Taylor n’est pas encore synchronisée à Azure Active Directory.
8. Bob Taylor a les valeurs suivantes pour l’attribut proxyAddresses
    - smtp:bobt@contoso.com
    - smtp:bob.taylor@contoso.com
    - **smtp:bob@contoso.com**
9. Lors de la synchronisation, Azure AD Connect reconnaît l’ajout de les Bob Taylor dans sur le site Active Directory et AD Azure pour apporter la même modification de demander.
10. Annonce Azure effectue tout d’abord correspondance permanente. Autrement dit, il recherche si n’importe quel objet avec du immutableId est égal à « abcdefghijkl0123456789 == ». Correspondance exacte échouera car aucun autre objet dans Active Directory Azure ne porte ce immutableId.
11. Annonce Azure tente alors de Bob Taylor soft match. Autrement dit, il recherche si n’importe quel objet avec proxyAddresses est égale à trois valeurs, y comprissmtp:bob@contoso.com
12. Annonce Azure trouverez objet Bob Smith répond aux critères de soft-match. Mais cet objet a la valeur immutableId = « abcdefghijklmnopqrstuv == ». qui indique cet objet a été synchronisé à partir d’un autre objet à partir de site Active Directory. Par conséquent, Azure AD ne peut pas soft match ces objets et provoquera une erreur de synchronisation **InvalidSoftMatch** .

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Comment corriger l’erreur de InvalidSoftMatch
La raison la plus courante pour l’erreur InvalidSoftMatch est deux objets différent SourceAnchor \(immutableId\) ont la même valeur pour les attributs ProxyAddresses et/ou UserPrincipalName, qui sont utilisés pendant le processus soft-match sur Azure AD. Pour corriger la correspondance logicielle non valide

1.  Identifier les adresses proxyAddresses dupliqué, userPrincipalName ou autre valeur d’attribut qui est à l’origine de l’erreur. Également identifier les deux \(ou plus\) objets impliqués dans le conflit. Le rapport généré par [Azure AD connecter la santé pour la synchronisation](https://aka.ms/aadchsyncerrors) peut vous aider à identifier les deux objets.
2. Identifier l’objet qui doit continuer à avoir la valeur dupliquée et l’objet ne doit pas.
3. Supprimez la valeur dupliquée à partir de l’objet ne doit pas avoir cette valeur. Notez que la modification à effectuer dans le répertoire dans lequel l’objet est alimenté à partir de. Dans certains cas, vous devrez peut-être supprimer un des objets en conflit.
4. Si vous avez apporté la modification dans les locaux sur Active Directory, permettent de Azure AD Connect synchroniser la modification.

Notez que le rapport d’erreurs de synchronisation dans Azure AD connecter la santé pour la synchronisation de mise à jour toutes les 30 minutes et inclut les erreurs de la dernière tentative de synchronisation.

>[AZURE.NOTE] ImmutableId, par définition, ne doit pas changer dans la durée de vie de l’objet. Si Azure Connect d’Active Directory n’a pas été configuré avec certains des scénarios en tenant compte de la liste ci-dessus, vous pouvez vous retrouver dans une situation où Azure AD Connect calcule une valeur différente de la SourceAnchor de l’objet Active Directory qui représente la même entité (même groupe/utilisateur/contact etc.) qui a un objet AD Azure existant que vous souhaitez continuer à utiliser.

#### <a name="related-articles"></a>Articles connexes
- [Attributs en double ou non valides empêchent la synchronisation d’annuaire dans Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Description
Lorsqu’AD Azure tente soft correspondent aux deux objets, il est possible que deux objets de différentes « type d’objet » (par exemple, utilisateur, groupe, Contact etc.) ont les mêmes valeurs pour les attributs utilisés pour effectuer la correspondance souple. Comme la duplication de ces attributs n’est pas autorisée dans Active Directory Azure, l’opération peut entraîner dans l’erreur de synchronisation de « ObjectTypeMismatch ».

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Exemples de scénarios d’erreur de ObjectTypeMismatch
- Création d’un groupe de sécurité de messagerie dans Office 365. Administrateur ajoute un nouvel utilisateur ou un contact dans les locaux d’Active Directory (qui n’est pas synchronisé avec AD Azure encore) avec la même valeur pour l’attribut ProxyAddresses que celle du groupe d’Office 365.

#### <a name="example-case"></a>Exemple de cas

1. Admin crée un nouveau groupe de sécurité de messagerie dans Office 365 pour le département de la taxe et fournit une adresse de messagerie en tant que tax@contoso.com. Cela affecte l’attribut ProxyAddresses pour ce groupe avec la valeur de**smtp:tax@contoso.com**
2. Un nouvel utilisateur rejoint Contoso.com et un compte est créé pour l’utilisateur dans des installations possédant le proxyAddress en tant que**smtp:tax@contoso.com**
3. Lorsque Azure AD Connect est une synchronisation avec le nouveau compte d’utilisateur, il obtiendra l’erreur « ObjectTypeMismatch ».

#### <a name="how-to-fix-objecttypemismatch-error"></a>Comment corriger l’erreur de ObjectTypeMismatch
La raison la plus courante pour l’erreur ObjectTypeMismatch est deux objets d’un type différent (utilisateur, groupe, Contact etc.) ont la même valeur pour l’attribut ProxyAddresses. Pour corriger le ObjectTypeMismatch :

1.  Identifier les adresses proxyAddresses dupliqué (ou un autre attribut) valeur qui est à l’origine de l’erreur. Également identifier les deux \(ou plus\) objets impliqués dans le conflit. Le rapport généré par [Azure AD connecter la santé pour la synchronisation](https://aka.ms/aadchsyncerrors) peut vous aider à identifier les deux objets.
2. Identifier l’objet qui doit continuer à avoir la valeur dupliquée et l’objet ne doit pas.
3. Supprimez la valeur dupliquée à partir de l’objet ne doit pas avoir cette valeur. Notez que la modification à effectuer dans le répertoire dans lequel l’objet est alimenté à partir de. Dans certains cas, vous devrez peut-être supprimer un des objets en conflit.
4. Si vous avez apporté la modification dans les locaux sur Active Directory, permettent de Azure AD Connect synchroniser la modification. Rapport d’erreurs de synchronisation dans Azure AD connecter la santé pour la synchronisation de mise à jour toutes les 30 minutes et inclut les erreurs de la dernière tentative de synchronisation.


## <a name="duplicate-attributes"></a>Attributs dupliqués
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Description
Schéma d’Active Directory Azure n’autorise pas deux ou plusieurs objets ont la même valeur des attributs suivants. C’est à que chaque objet dans Active Directory Azure est obligé d’avoir une valeur unique de ces attributs dans une instance donnée.

- ProxyAddresses
- UserPrincipalName

Si Azure Connect d’Active Directory tente d’ajouter un nouvel objet ou de mettre à jour un objet existant avec une valeur pour les attributs ci-dessus qui est déjà affectée à un autre objet dans Azure Active Directory, l’opération entraînera dans l’erreur de synchronisation de « AttributeValueMustBeUnique ».
#### <a name="possible-scenarios"></a>Scénarios possibles :
1. Valeur en double est assignée à un objet déjà synchronisé, ce qui est en conflit avec un autre objet synchronisé.

#### <a name="example-case"></a>Exemple de cas :
1. **Bob Smith** est un utilisateur synchronisé dans Azure Active Directory sur le site Active Directory de contoso.com
2. De Bob Smith **UserPrincipalName** dans locaux est défini en tant que **bobs@contoso.com**.
3. Bob a également suivant les valeurs de l’attribut **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
4. Un nouvel utilisateur, **Bob Taylor**, est ajouté à l’entreprise dans Active Directory.
5. de Bob Taylor **UserPrincipalName** est défini en tant que **bobt@contoso.com**.
6. **Bob Taylor** a les valeurs suivantes pour l’attribut **ProxyAddresses** i. smtp:bobt@contoso.comII. smtp:bob.taylor@contoso.com
7. Synchronisation de l’objet de Bob Taylor avec AD Azure avec succès.
8. Admin a décidé de mettre à jour l’attribut **ProxyAddresses** de Bob Taylor avec la valeur suivante : i. **smtp:bob@contoso.com**
9. Annonce Azure va tenter de mettre à jour les objets de Bob Taylor dans Azure AD avec la valeur ci-dessus, mais cette opération échoue car que ProxyAddresses valeur est déjà assignée à Bob Smith, aboutissant à un erreur de « AttributeValueMustBeUnique ».

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Comment corriger l’erreur de AttributeValueMustBeUnique
La raison la plus courante pour l’erreur AttributeValueMustBeUnique est deux objets différent SourceAnchor \(immutableId\) ont la même valeur pour les attributs ProxyAddresses et/ou UserPrincipalName. Pour corriger l’erreur de AttributeValueMustBeUnique

1.  Identifier les adresses proxyAddresses dupliqué, userPrincipalName ou autre valeur d’attribut qui est à l’origine de l’erreur. Également identifier les deux \(ou plus\) objets impliqués dans le conflit. Le rapport généré par [Azure AD connecter la santé pour la synchronisation](https://aka.ms/aadchsyncerrors) peut vous aider à identifier les deux objets.
2. Identifier l’objet qui doit continuer à avoir la valeur dupliquée et l’objet ne doit pas.
3. Supprimez la valeur dupliquée à partir de l’objet ne doit pas avoir cette valeur. Notez que la modification à effectuer dans le répertoire dans lequel l’objet est alimenté à partir de. Dans certains cas, vous devrez peut-être supprimer un des objets en conflit.
4. Si vous avez apporté la modification dans les locaux sur Active Directory, permettent de Azure AD Connect synchroniser la modification de l’erreur pour le résoudre.

#### <a name="related-articles"></a>Articles connexes
-[Attributs en double ou non valides empêchent la synchronisation d’annuaire dans Office 365](https://support.microsoft.com/en-us/kb/2647098)


## <a name="data-validation-failures"></a>Échecs de Validation de données
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Description
Azure Active Directory met en œuvre diverses restrictions sur les données avant que les données à écrire dans le répertoire. Il s’agit afin de garantir que les utilisateurs finaux les meilleures expériences possibles lors de l’utilisation des applications qui dépendent de ces données.
#### <a name="scenarios"></a>Scénarios
une barre d’outils. La valeur de l’attribut UserPrincipalName est non valide/non prise en charge des caractères.
b. L’attribut UserPrincipalName ne suit pas le format requis.
#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Comment corriger l’erreur de IdentityDataValidationFailed

une barre d’outils. Assurez-vous que l’attribut userPrincipalName est prise en charge des caractères et le format requis.

#### <a name="related-articles"></a>Articles connexes
- [Préparer la configuration des utilisateurs par le biais de la synchronisation d’annuaire vers Office 365]( https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)


### <a name="datavalidationfailed"></a>DataValidationFailed
#### <a name="description"></a>Description
Il s’agit d’un cas très spécifique qui provoque une erreur de synchronisation de **« DataValidationFailed »** lorsque le suffixe de UserPrincipalName l’utilisateur est modifié à partir d’un domaine fédéré à un autre domaine fédéré.

#### <a name="scenarios"></a>Scénarios
Pour un utilisateur de synchronisation, le suffixe UserPrincipalName a été modifié à partir d’un domaine fédéré à un autre domaine fédéré locaux. Par exemple, *UserPrincipalName = bob@contoso.com * a été modifié en *UserPrincipalName = bob@fabrikam.com *.

#### <a name="example"></a>Exemple
1. Bob Smith, un compte pour Contoso.com, est ajouté sous la forme d’un nouvel utilisateur dans Active Directory avec UserPrincipalNamebob@contoso.com
2. Bob se déplace vers une autre division contoso.com appelé Fabrikam.com et son UserPrincipalName est modifiébob@fabrikam.com
3. Les domaines contoso.com et fabrikam.com sont des domaines fédérés avec Azure Active Directory.
4. UserPrincipalName de Bob ne pas mis à jour et génère une erreur de synchronisation de « DataValidationFailed ».

#### <a name="how-to-fix"></a>Comment résoudre le problème
Si le suffixe de UserPrincipalName d’un utilisateur a été mis à jour à partir de bob@ **contoso.com** à bob@ **fabrikam.com**, où les forêts **contoso.com** et **fabrikam.com** sont des **domaines fédérés**, puis suivez les opérations suivantes pour corriger l’erreur de synchronisation

1. Mettre à jour UserPrincipalName l’utilisateur dans Azure annonce à partir de bob@contoso.com à bob@contoso.onmicrosoft.com. Vous pouvez utiliser la commande PowerShell suivante avec le Module PowerShell de publicité Azure :`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`

2. Autoriser la tentative de synchronisation du prochain cycle de synchronisation. Cette synchronisation de l’heure sera réussie et il mettra à jour le UserPrincipalName de Robert à bob@fabrikam.com comme prévu.


## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Description
Lorsqu’un attribut dépasse la limite de taille autorisée, la limite de longueur ou le nombre maximal défini par le schéma Active Directory de Azure, l’opération de synchronisation entraîne l’erreur **LargeObject** ou **ExceededAllowedLength** . Cette erreur se produit généralement pour les attributs suivants

- userCertificate
- thumbnailPhoto
- proxyAddresses

### <a name="possible-scenarios"></a>Scénarios possibles
1. Attribut userCertificate de Bob stocke trop de certificats sont affectés à Bob. Il peut s’agir de certificats plus anciennes, a expiré.
2. ThmubnailPhoto de Bob défini dans Active Directory est trop volumineux pour être synchronisées dans AD Azure.
3. Pendant le remplissage automatique de l’attribut ProxyAddresses d’Active Directory, un objet est assigné > 500 ProxyAddresses.

### <a name="how-to-fix"></a>Comment résoudre le problème

1. Assurez-vous que l’attribut à l’origine de l’erreur est dans la limite autorisée.

## <a name="related-links"></a>Liens connexes
- [Rechercher des objets Active Directory dans Active Directory Administrative Center] (https://technet.microsoft.com/library/dd560661.aspx)
- [Comment faire pour interroger Active Directory de Azure pour un objet à l’aide d’Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
