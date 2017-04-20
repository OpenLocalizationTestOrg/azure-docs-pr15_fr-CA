<properties
    pageTitle="Identité ainsi que la synchronisation attribut la résilience | Microsoft Azure"
    description="Nouveau comportement de gestion des objets présentant des conflits de nom UPN ou ProxyAddress au cours de la synchronisation d’annuaire à l’aide d’Azure Connect d’Active Directory."
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
    ms.date="09/29/2016"
    ms.author="markusvi"/>



# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identité ainsi que la synchronisation attribut la résilience
Résilience d’attribut en double est une fonctionnalité dans Azure Active Directory qui permet d’éliminer par frottement dû **UserPrincipalName** et **ProxyAddress** conflits lors de l’exécution d’un des outils de synchronisation de Microsoft.

Ces deux attributs sont généralement requises pour être unique pour tout **utilisateur**, **groupe**ou **Contact** des objets dans un client Azure Active Directory donné.

> [AZURE.NOTE] Seuls les utilisateurs peuvent avoir des noms UPN.

Le nouveau comportement de cette fonctionnalité est dans la partie du nuage du pipeline de synchronisation, il est client agnostique et pertinente pour n’importe quel produit de synchronisation de Microsoft, y compris d’Azure AD Connect, synchronisation d’annuaire et MIM + connecteur. Dans ce document, le terme générique « client de synchronisation » permet de représenter l’un de ces produits.

## <a name="current-behavior"></a>Comportement actuel
S’il existe une tentative de mise en service un nouvel objet avec une valeur UPN ou ProxyAddress qui viole cette contrainte d’unicité, Azure Active Directory bloque la création de cet objet. De même, si un objet est mis à jour avec un UPN ou un ProxyAddress non unique, la mise à jour échoue. La tentative de mise en service ou de la mise à jour est une nouvelle tentative par le client de synchronisation lors de chaque cycle d’exportation et continue d’échouer jusqu'à ce que le conflit est résolu. Un e-mail de rapport d’erreur est généré lors de chaque tentative et une erreur est enregistrée par le client de synchronisation.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Problème avec la résilience d’attribut en double
Au lieu de basculer complètement de prévoir ou de mettre à jour un objet avec un attribut en double, Azure Active Directory » met en quarantaine » l’attribut en double qui viole la contrainte d’unicité. Si cet attribut est requis pour la mise en service, comme UserPrincipalName, le service attribue une valeur d’espace réservé. Le format de ces valeurs temporaires est  
"***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>. onmicrosoft.com***».  
Si l’attribut n’est pas requis, comme un **ProxyAddress**, Azure Active Directory simplement met en quarantaine de l’attribut de conflit et se poursuit avec la création de l’objet ou de la mise à jour.

Lors de la mise en quarantaine de l’attribut, les informations sur le conflit sont envoyées dans le même message de rapport d’erreur utilisé dans l’ancien comportement. Toutefois, cette information s’affiche uniquement dans le rapport d’erreurs une fois, lorsque la zone de quarantaine se produit, il ne continue pas à être enregistrés dans les prochains e-mails. En outre, puisque l’exportation de cet objet a réussi, le client de synchronisation ne consigne pas d’une erreur et ne tente pas du créer / mettre à jour l’opération lors des cycles de synchronisation suivantes.

Pour prendre en charge ce comportement, un nouvel attribut a été ajouté pour les classes d’objets utilisateur, groupe et Contact :  
**DirSyncProvisioningErrors**

Il s’agit d’un attribut à valeurs multiples qui est utilisé pour stocker les attributs en conflit qui risque de violer la contrainte d’unicité doivent leur être ajoutées normalement. Une tâche de minuterie en arrière-plan a été activée dans Azure Active Directory qui s’exécute toutes les heures pour rechercher les conflits d’attributs en double qui ont été résolus et supprime automatiquement les attributs en question à partir de la quarantaine.

### <a name="enabling-duplicate-attribute-resiliency"></a>L’activation de résilience d’attribut en double
Résilience d’attribut en double sera le nouveau comportement par défaut sur tous les locataires d’Azure Active Directory. Sera activé par défaut pour tous les locataires qui a activé la synchronisation pour la première fois le 22 août 2016, ou une version ultérieure. Les locataires qui a activé la synchronisation avant cette date aura la fonctionnalité est activée dans les lots. Ce déploiement commence en septembre 2016, et une notification par courrier électronique sera envoyée au contact de notification technique de chaque client avec la date spécifique lorsque la fonctionnalité sera activée.

Une fois que la résilience d’attribut en double a été activée, il ne peut pas être désactivé.

Pour vérifier si la fonctionnalité est activée pour vos clients, vous pouvez le faire en téléchargeant la dernière version du module Azure Active Directory PowerShell et en exécutant :

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

Si vous souhaitez activer proactivement la fonctionnalité avant qu’il est activé pour vos clients, vous pouvez le faire en téléchargeant la dernière version du module Azure Active Directory PowerShell et en exécutant :

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identification des objets avec DirSyncProvisioningErrors
Il existe actuellement deux méthodes pour identifier les objets qui possèdent ces erreurs en raison de conflits de propriété en double, Azure Active Directory PowerShell et le portail d’administration d’Office 365. Il est prévu d’étendre aux autres portail rapports dans le futur.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Pour les applets de commande PowerShell dans cette rubrique, les éléments suivants sont vrais :

- Toutes les applets de commande suivantes respectent la casse.
- **ErrorCategory – PropertyConflict** doit toujours être inclus. Il n’existe actuellement aucun autre type de **ErrorCategory**, mais cela peut être étendue à l’avenir.

Tout d’abord, commencez par **MsolService de la connexion** en cours d’exécution et d’entrer des informations d’authentification pour un administrateur de clients.

Ensuite, utilisez les applets de commande et les opérateurs suivants pour afficher les erreurs de différentes manières :

1. [Voir tous les](#see-all)

2. [Par Type de propriété](#by-property-type)

3. [Par valeur en conflit](#by-conflicting-value)

4. [À l’aide d’une recherche de chaîne](#using-a-string-search)

5. [Triés](#sorted)

6. [Dans une quantité limitée ou toutes les](#in-a-limited-quantity-or-all)


#### <a name="see-all"></a>Voir tous les
Une fois connecté, pour afficher la liste de mise en service de l’attribut générale erreurs dans le locataire exécutent :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Cela produit un résultat semblable à celui-ci :  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  


#### <a name="by-property-type"></a>Par type de propriété
Pour voir les erreurs par type de propriété, ajoutez l’indicateur **- PropertyName** avec l’argument **UserPrincipalName** ou **ProxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Par valeur en conflit
Pour afficher les erreurs relatives à une propriété spécifique ajouter l’indicateur **- PropertyValue** (**- PropertyName** doit être utilisé lors de l’ajout de cet indicateur) :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### <a name="using-a-string-search"></a>À l’aide d’une recherche de chaîne
Pour faire une recherche de chaîne large utilisez l’indicateur **- SearchString** . Cela peut être utilisée indépendamment de tous les indicateurs ci-dessus, à l’exception **ErrorCategory - PropertyConflict**, qui est toujours requis :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Dans une quantité limitée ou toutes les
1. **MaxResults <Int> ** peut être utilisé pour limiter la requête à un nombre spécifique de valeurs.

2. **Tous les** peut servir à vérifier que tous les résultats sont extraits dans le cas où il existe un grand nombre d’erreurs.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portail d’administration d’Office 365

Vous pouvez afficher les erreurs de synchronisation d’annuaire dans le centre d’administration Office 365. Le rapport dans le portail Office 365 affiche uniquement les objets **utilisateur** ayant ces erreurs. Il n’affiche pas d’informations sur les conflits entre les **groupes** et les **Contacts**.


![Utilisateurs actifs] (./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Utilisateurs actifs")

Pour obtenir des instructions sur l’affichage des erreurs de synchronisation d’annuaire dans le centre d’administration Office 365, reportez-vous à la section [identification des erreurs de synchronisation d’annuaire dans Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).


### <a name="identity-synchronization-error-report"></a>Rapport d’erreur de synchronisation de Identity
Lorsqu’un objet avec un conflit d’attribut en double est géré avec ce nouveau comportement, une notification est incluse dans l’e-mail de rapport d’erreur de synchronisation d’identité standard qui est envoyé à la Notification technique contact pour le client. Cependant, il existe des changements importants dans ce comportement. Dans le passé, des informations sur un conflit d’attribut en double apparaît dans chaque rapport d’erreur suivants jusqu'à ce que le conflit a été résolu. Ce nouveau comportement, la notification d’erreur d’un conflit donné uniquement s’affiche une fois - à la fois que l’attribut en conflit est mis en quarantaine.

Voici à quoi ressemble la notification par e-mail pour un conflit ProxyAddress :  
    ![Utilisateurs actifs](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

## <a name="resolving-conflicts"></a>Résolution des conflits
Tactiques de stratégie et la résolution de ces erreurs de résolution des problèmes ne doivent pas s’écarter de la façon dont les erreurs de l’attribut en double ont été traitées dans le passé. La seule différence est que la tâche du minuteur balayages par le locataire sur le côté service pour ajouter automatiquement l’attribut en question à l’objet approprié une fois que le conflit est résolu.

L’article suivant décrit diverses stratégies de dépannage et de résolution : [en double ou des attributs non valides empêchent la synchronisation d’annuaire dans Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problèmes connus
Aucun de ces problèmes connus provoque la dégradation de service ou de la perte de données. Plusieurs d'entre eux sont esthétiques, d’autres provoquent des erreurs attribut dupliqué est levée au lieu de la mise en quarantaine de l’attribut de conflit standard «*Pre-résilience*» et un autre entraîne certaines erreurs d’exiger correctives manuelle supplémentaire.

**Comportement de base :**

1. Objets avec des configurations spécifiques d’attribut continuent à recevoir des erreurs d’exportation au lieu du ou les attributs en double qui est mis en quarantaine.  
Par exemple :

    une barre d’outils. Nouvel utilisateur est créé dans Active Directory avec un nom UPN de **Joe@contoso.com** et ProxyAddress**smtp:Joe@contoso.com**

    b. Les propriétés de cet objet sont en conflit avec un groupe existant, où ProxyAddress est **SMTP:Joe@contoso.com**.

    c. Lors de l’exportation, une erreur de **conflit de ProxyAddress** est levée au lieu d’avoir les attributs de conflit mis en quarantaine. L’opération est réitérée lors de chaque cycle de synchronisation suivantes, comme il aurait été avant l’activation de la fonctionnalité de permanence.

2. Si deux groupes sont créés en local avec la même adresse SMTP, un échec à la disposition de la première tentative avec une erreur de **ProxyAddress** standard en double. Toutefois, la valeur dupliquée est correctement mis en quarantaine lors du prochain cycle de synchronisation.

**Rapport de portail office**:

1. Le message d’erreur détaillé pour les deux objets dans un ensemble de conflit de nom UPN est la même. Cela signifie qu’ils ont tous deux ont eu leur UPN changé / mise en quarantaine, en fait un seul d'entre eux avait toutes les données modifiées.

2. Le message d’erreur détaillé d’un conflit de nom UPN affiche displayName incorrect pour un utilisateur qui a eu leur UPN changé/mis en quarantaine. Par exemple :

    une barre d’outils. **L’utilisateur A** les synchronise en premier avec **UPN = User@contoso.com **.

    b. **Utilisateur B** est tentée d’être synchronisées suivant avec **UPN = User@contoso.com **.

    c. **L’utilisateur B** UPN est remplacée par **User1234@contoso.onmicrosoft.com** et **User@contoso.com** est ajouté à **DirSyncProvisioningErrors**.

    d. Le message d’erreur pour **l’Utilisateur B** doit indiquer que **utilisateur** déjà a **User@contoso.com** comme un UPN, mais il montre displayName propre de **L’utilisateur B** .



**Rapport d’erreur de synchronisation d’identité**:

Le lien pour *savoir comment résoudre ce problème* est incorrect :  
    ![Utilisateurs actifs](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

Il doit pointer vers [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).


## <a name="see-also"></a>Voir aussi

- [Synchronisation d’Active Directory Connect Azure](active-directory-aadconnectsync-whatis.md)

- [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)

- [Identifier les erreurs de synchronisation d’annuaire dans Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)
