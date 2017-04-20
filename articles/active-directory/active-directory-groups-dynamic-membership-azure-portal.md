
<properties
    pageTitle="L’utilisation d’attributs pour créer des règles avancées pour l’appartenance au groupe dans l’aperçu d’Azure Active Directory | Microsoft Azure"
    description="Comment créer des règles avancées pour l’appartenance de groupe dynamique, y compris prise en charge des opérateurs d’expression règle et des paramètres."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules-for-group-membership-in-azure-active-directory-preview"></a>L’utilisation d’attributs pour créer des règles avancées pour l’appartenance au groupe dans l’aperçu d’Azure Active Directory

Le portail Azure vous offre la possibilité de créer des règles avancées pour activer plus complexes basée sur l’attribut dynamique les appartenances de groupes d’aperçu Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Cet article détaille les attributs de règle et la syntaxe pour créer ces règles avancées.

## <a name="to-create-the-advanced-rule"></a>Pour créer la règle avancée

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2.  Sélectionnez des **services supplémentaires**, entrez les **utilisateurs et les groupes** dans la zone de texte et puis sélectionnez **entrée**.

  ![Gestion des utilisateurs ouverture](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)

3.  Sur la blade **d’utilisateurs et des groupes** , sélectionnez **tous les groupes**.

  ![Ouverture de la lame de groupes](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)

4. Sur la lame de **utilisateurs et groupes : tous les groupes** , sélectionnez la commande **Ajouter** .

  ![Ajouter un nouveau groupe](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)

5. Sur la lame du **groupe** , entrez un nom et une description pour le nouveau groupe. Sélectionnez un **type d’appartenance** de **Utilisateur dynamique** ou **Dispositif dynamique**, selon que vous souhaitez créer une règle pour les utilisateurs ou les périphériques, puis sélectionnez **Ajouter une requête dynamique**. Pour les attributs utilisés pour les règles de périphérique, consultez [à l’aide des attributs pour créer des règles pour les objets du périphérique](#using-attributes-to-create-rules-for-device-objects).

  ![Ajouter une règle d’appartenance dynamique](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

6. Sur la lame de **règles d’appartenance dynamique** , entrez votre règle dans la zone **Ajouter une règle d’appartenance dynamique avancée** et appuyez sur ENTRÉE puis sélectionnez **créer** au bas de la lame.

7. Sélectionnez **créer** sur la lame de **groupe** pour créer le groupe.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construire le corps d’une règle avancée

La règle avancée que vous pouvez créer pour les appartenances dynamiques pour les groupes est essentiellement une expression binaire qui se compose de trois parties et donne un résultat true ou false. Les trois parties sont les suivantes :

- Paramètre de gauche
- Opérateur binaire
- Constante de droite

Une règle avancée complète ressemble à ceci : (leftParameter binaryOperator « RightConstant »), où l’ouverture et la parenthèse fermante sont requis pour l’ensemble de l’expression binaire, les guillemets doubles sont requis pour la constante de droite, et la syntaxe du paramètre gauche est user.property. Une règle avancée peut se composer de plusieurs expressions binaires séparées par- et, - ou et - des opérateurs logiques pas.

Voici des exemples d’une règle avancée correctement construite :

- (« Ventes » user.department - eq)- ou de (user.department - eq « Marketing »)
- (« Ventes » user.department - eq)- et - pas (user.jobTitle-contient « SDE »)

Pour obtenir la liste complète des paramètres pris en charge et opérateurs de règle d’expressions, voir les sections ci-dessous. Pour les attributs utilisés pour les règles de périphérique, consultez [à l’aide des attributs pour créer des règles pour les objets du périphérique](#using-attributes-to-create-rules-for-device-objects).

La longueur totale du corps de votre règle avancée ne peut pas dépasser 2048 caractères.

> [AZURE.NOTE]
>Opérations de chaîne et les expressions régulières sont la casse. Vous pouvez également effectuer des vérifications de valeur Null, à l’aide de $null sous la forme d’une constante, par exemple, l’user.department - eq $null.
Les chaînes contenant des guillemets » doit être une séquence d’échappement à l’aide de ' caractère, par exemple, user.department - eq \`« Ventes ».

## <a name="supported-expression-rule-operators"></a>Opérateur de règle d’expression pris en charge
Le tableau suivant répertorie tous les opérateurs de règle prise en charge d’expression et leur syntaxe à utiliser dans le corps de la règle avancée :

| Opérateur        | Syntaxe         |
|-----------------|----------------|
| Pas égal à      | -ne            |
| Est égal à          | -eq            |
| Ne commence pas par | -notStartsWith |
| Commence par     | startsWith-    |
| Ne contient pas    | -notContains   |
| Contient        | -contient      |
| Pas de correspondance       | -notMatch      |
| Correspondance           | -correspond à         |


## <a name="query-error-remediation"></a>Correction d’erreur de requête
Le tableau suivant répertorie les erreurs potentielles et comment les corriger si elles se produisent

| Erreur d’analyse de requête     | Utilisation de l’erreur       | Utilisation corrigée             |
|-----------------------|-------------------|-----------------------------|
| Erreur : Attribut non pris en charge.                                      | (user.invalidProperty - eq « Valeur »)       | (user.department - eq « valeur »)<br/>Propriété doit correspondre à l’une à partir de la [liste des propriétés de prise en charge](#supported-properties).                          |
| Erreur : Opérateur n’est pas pris en charge sur l’attribut.                       | (user.accountEnabled-a la valeur True)                                                                               | (true - eq de user.accountEnabled)<br/>Propriété est de type booléen. Utilisez les opérateurs pris en charge (-eq ou - ne) de type boolean à partir de la liste ci-dessus.                                                                                                                                   |
| Erreur : Erreur de compilation de requête.                                      | (« Ventes » user.department - eq)- et (user.department - eq « Marketing »)(user.userPrincipalName-match"*@domain.ext") | (« Ventes » user.department - eq)-(user.department - eq « Marketing ») et<br/>Opérateur logique doit correspondre à l’une à partir de la liste des propriétés prises en charge ci-dessus. (user.userPrincipalName-correspond à ".*@domain.ext")or(user.userPrincipalName -correspond à "@domain.ext$")Error dans l’expression régulière. |
| Erreur : L’expression binaire n’est pas dans le format approprié.                     | (user.department – eq « Ventes ») (user.department - eq « Ventes ») (user.department-eq « Ventes »)                             | (valeur de true user.accountEnabled - eq)- et (user.userPrincipalName-contient"alias@domain")<br/>Requête comporte plusieurs erreurs. Parenthèse pas à l’emplacement approprié.                                                                                                                            |
| Erreur : Une erreur inconnue s’est produite lors de la configuration dynamiques appartenances. | (user.accountEnabled - eq « True » et user.userPrincipalName-contient"alias@domain")                               | (valeur de true user.accountEnabled - eq)- et (user.userPrincipalName-contient"alias@domain")<br/>Requête comporte plusieurs erreurs. Parenthèse pas à l’emplacement approprié.                                                                                                                            |

## <a name="supported-properties"></a>Propriétés prises en charge
Toutes les propriétés de l’utilisateur que vous pouvez utiliser dans votre règle avancée sont les suivantes :

### <a name="properties-of-type-boolean"></a>Propriétés de type booléen

Opérateurs autorisés

* -eq


* -ne


| Propriétés     | Valeurs autorisées  | Utilisation de                          |
|----------------|-----------------|--------------------------------|
| accountEnabled | Vrai faux      | Vrai - eq de user.accountEnabled)  |
| dirSyncEnabled | null de false true | (true - eq de user.dirSyncEnabled) |

### <a name="properties-of-type-string"></a>Propriétés de type chaîne

Opérateurs autorisés

* -eq


* -ne


* -notStartsWith


* StartsWith-


* -contient


* -notContains


* -correspond à


* -notMatch

| Propriétés                 | Valeurs autorisées                                                                                        | Utilisation de                                                     |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Ville                       | $Null ni valeur de chaîne                                                                           | (user.city - eq « valeur »)                                   |
| pays                    | $Null ni valeur de chaîne                                                                            | (user.country - eq « valeur »)                                |
| département                 | $Null ni valeur de chaîne                                                                          | (user.department - eq « valeur »)                             |
| displayName                | Toute valeur de chaîne                                                                                 | (user.displayName - eq « valeur »)                            |
| facsimileTelephoneNumber   | $Null ni valeur de chaîne                                                                           | (user.facsimileTelephoneNumber - eq « valeur »)               |
| givenName                  | $Null ni valeur de chaîne                                                                           | (user.givenName - eq « valeur »)                              |
| jobTitle                   | $Null ni valeur de chaîne                                                                           | (user.jobTitle - eq « valeur »)                               |
| courrier                       | Toute valeur de chaîne ou les $null (adresse SMTP de l’utilisateur)                                                  | (user.mail - eq « valeur »)                                   |
| mailNickName               | Toute valeur de chaîne (alias de messagerie de l’utilisateur)                                                            | (user.mailNickName - eq « valeur »)                           |
| Mobile                     | $Null ni valeur de chaîne                                                                           | (user.mobile - eq « valeur »)                                 |
| objectId                   | GUID de l’objet utilisateur                                                                            | (user.objectId - eq « 1111111-1111-1111-1111-111111111111 ») |
| passwordPolicies           | Aucun DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |   (user.passwordPolicies - eq « DisableStrongPassword »)                                                      |
| physicalDeliveryOfficeName | $Null ni valeur de chaîne                                                                            | (user.physicalDeliveryOfficeName - eq « valeur »)             |
| code postal                 | $Null ni valeur de chaîne                                                                            | (user.postalCode - eq « valeur »)                             |
| preferredLanguage          | Code ISO 639-1.                                                                                        | (user.preferredLanguage - eq « en-US »)                      |
| sipProxyAddress            | $Null ni valeur de chaîne                                                                            | (user.sipProxyAddress - eq « valeur »)                        |
| état                      | $Null ni valeur de chaîne                                                                            | (user.state - eq « valeur »)                                  |
| streetAddress              | $Null ni valeur de chaîne                                                                            | (user.streetAddress - eq « valeur »)                          |
| nom de famille                    | $Null ni valeur de chaîne                                                                            | (user.surname - eq « valeur »)                                |
| numéro de téléphone            | $Null ni valeur de chaîne                                                                            | (user.telephoneNumber - eq « valeur »)                        |
| usageLocation              | Code de deux pays symbolisée                                                                           | (user.usageLocation - eq « US »)                             |
| userPrincipalName          | Toute valeur de chaîne                                                                                     | (user.userPrincipalName - eq"alias@domain")               |
| userType                   | invité de membre $null                                                                                    | (user.userType - eq « Member »)                              |

### <a name="properties-of-type-string-collection"></a>Propriétés de type chaîne collection

Opérateurs autorisés

* -contient


* -notContains

| Poperties      | Valeurs autorisées                        | Utilisation de                                                |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails     | Toute valeur de chaîne                      | (user.otherMails-contient"alias@domain")           |
| proxyAddresses | SMTP : alias@domain smtp :alias@domain | (user.proxyAddresses-contient « SMTP :alias@domain") |

## <a name="extension-attributes-and-custom-attributes"></a>Les attributs personnalisés et les attributs d’extension
Attributs d’extension et les attributs personnalisés sont pris en charge dans les règles d’appartenance dynamique.

Attributs d’extension sont synchronisées à partir de site Active Directory du serveur de fenêtre et prennent le format de « ExtensionAttributeX », où X est égal à 1 à 15.
Un exemple d’une règle qui utilise un attribut d’extension

(user.extensionAttribute15 - eq « commercialisation »)

Attributs personnalisés sont synchronisées à partir de site Active Directory du serveur Windows ou à partir d’une application connectée de SaaS et le format de « user.extension_[GUID]\__ [Attribute] », où [GUID] est l’identificateur unique dans le DAS pour l’application qui a créé l’attribut dans DAS et [Attribute] est le nom de l’attribut tel qu’il a été créé.
Est un exemple d’une règle qui utilise un attribut personnalisé

User.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

Le nom de l’attribut personnalisé peut être trouvé dans le répertoire en interrogeant un utilisateur de l’attribut à l’aide du graphique de l’Explorateur et la recherche pour le nom de l’attribut.

## <a name="direct-reports-rule"></a>Règle de rapports directs
Vous pouvez maintenant remplir les membres dans un groupe basé sur l’attribut de gestionnaire d’un utilisateur.

**Pour configurer un groupe comme un groupe de « Gestionnaire »**

1. Suivez les étapes 1 à 5 pour [créer la règle avancée](#to-create-the-advanced-rule), puis sélectionnez un **type d’appartenance** **d’Utilisateur dynamique**.

2. Sur la lame de **règles d’appartenance dynamique** , permet d’entrer la règle avec la syntaxe suivante :

    Rapports directs pour les *rapports directs de {obectID_of_manager}*. Est un exemple d’une règle valide pour les collaborateurs

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    où « 62e19b97-8b3d-4d4a-a106-4ce66896a863 » est l’objectID du gestionnaire. Vous trouverez l’ID d’objet dans Active Directory Azure sous l' **onglet profil** de la page utilisateur de l’utilisateur qui est le responsable.

3. Lors de l’enregistrement de cette règle, tous les utilisateurs qui satisfont la règle va être joints en tant que membres du groupe. Il peut prendre quelques minutes pour le groupe pour le remplissage initial.


## <a name="using-attributes-to-create-rules-for-device-objects"></a>L’utilisation d’attributs pour créer des règles pour les objets de périphérique

Vous pouvez également créer une règle qui sélectionne des objets de périphérique pour l’appartenance d’un groupe. Les attributs de périphérique suivants peuvent être utilisés :

| Propriétés           | Valeurs autorisées                  | Utilisation de                                                |
|----------------------|---------------------------------|------------------------------------------------------|
| displayName          | toute valeur de chaîne                | (device.displayName - eq « Rob Iphone »)                 |
| deviceOSType         | toute valeur de chaîne                | (device.deviceOSType - eq « E »)                      |
| deviceOSVersion      | toute valeur de chaîne                | (périphérique. OSVersion - eq « 9.1 »)                         |
| isDirSynced          | null de false true                 | (device.isDirSynced - eq « true »)                      |
| isManaged            | null de false true                 | (device.isManaged - eq « false »)                       |
| isCompliant          | null de false true                 | (device.isCompliant - eq « true »)                      |


## <a name="additional-information"></a>Informations supplémentaires
Ces articles fournissent des informations supplémentaires sur les groupes dans Active Directory de Azure.

* [Consultez les groupes existants](active-directory-groups-view-azure-portal.md)
* [Créer un nouveau groupe et ajout de membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les appartenances d’un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs d’un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
