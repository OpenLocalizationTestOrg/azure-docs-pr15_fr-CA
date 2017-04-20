
<properties
    pageTitle="L’utilisation d’attributs pour créer des règles avancées | Microsoft Azure"
    description="Comment-de créer des règles avancées pour un groupe, y compris prises en charge pour les paramètres et les opérateurs d’expression règle."
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
    ms.date="08/15/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules"></a>L’utilisation d’attributs pour créer des règles avancées

Le portail classique Azure vous offre la possibilité de créer des règles avancées pour activer plus complexes basée sur l’attribut dynamique les appartenances de groupes Azure Active Directory (AD Azure).  

Lorsque tous les attributs d’un changement d’utilisateur, le système évalue toutes les règles de groupe dynamique dans un répertoire pour voir si la modification de l’attribut de l’utilisateur déclencheraient tout groupe ajoute ou supprime. Si un utilisateur correspond à une règle sur un groupe, ils sont ajoutés en tant que membre de ce groupe. Si elles ne répondent plus à la règle d’un groupe qu’ils sont un membre, ils sont supprimés en tant que membres de ce groupe.

## <a name="to-create-the-advanced-rule"></a>Pour créer la règle avancée

1. Dans [Azure portal classique](https://manage.windowsazure.com), sélectionnez **Active Directory**et ouvrez l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** et ouvrez le groupe que vous souhaitez modifier.

3. Sélectionnez l’onglet **configuration** , sélectionnez l’option de la **règle avancée** et puis entrez la règle avancée dans la zone de texte.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construire le corps d’une règle avancée

La règle avancée que vous pouvez créer pour les appartenances dynamiques pour les groupes est essentiellement une expression binaire qui se compose de trois parties et donne un résultat true ou false. Les trois parties sont les suivantes :

- Paramètre de gauche
- Opérateur binaire
- Constante de droite

Une règle avancée complète ressemble à ceci : (leftParameter binaryOperator « RightConstant »), où l’ouverture et la parenthèse fermante sont requis pour l’ensemble de l’expression binaire, les guillemets doubles sont requis pour la constante de droite, et la syntaxe du paramètre gauche est user.property. Une règle avancée peut se composer de plusieurs expressions binaires séparées par- et, - ou et - des opérateurs logiques pas.
Voici des exemples d’une règle avancée correctement construite :

- (« Ventes » user.department - eq)- ou de (user.department - eq « Marketing »)
- (« Ventes » user.department - eq)- et - pas (user.jobTitle-contient « SDE »)

Pour obtenir la liste complète des paramètres pris en charge et opérateurs de règle d’expressions, voir les sections ci-dessous.

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

1. Dans le portail Azure classique, cliquez sur **Active Directory**, puis cliquez sur le nom de l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** et ouvrez le groupe que vous souhaitez modifier.

3. Sélectionnez l’onglet **configuration** et sélectionnez **Avancée de règle**.

4. Tapez la règle avec la syntaxe suivante :

    Rapports directs pour les *rapports directs de {obectID_of_manager}*. Est un exemple d’une règle valide pour les collaborateurs

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    où « 62e19b97-8b3d-4d4a-a106-4ce66896a863 » est l’objectID du gestionnaire. Vous trouverez l’ID d’objet dans Active Directory Azure sous l' **onglet profil** de la page utilisateur de l’utilisateur qui est le responsable.

3. Lors de l’enregistrement de cette règle, tous les utilisateurs qui satisfont la règle va être joints en tant que membres du groupe. Il peut prendre quelques minutes pour le groupe pour le remplissage initial.


## <a name="using-attributes-to-create-rules-for-device-objects"></a>L’utilisation d’attributs pour créer des règles pour les objets de périphérique

Vous pouvez également créer une règle qui sélectionne des objets de périphérique pour l’appartenance d’un groupe. Les attributs de périphérique suivants peuvent être utilisés :

| Propriétés              | Valeurs autorisées                  | Utilisation de                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| displayName             | toute valeur de chaîne                | (device.displayName - eq « Rob Iphone »)                       |
| deviceOSType            | toute valeur de chaîne                | (device.deviceOSType - eq « E »)                             |
| deviceOSVersion         | toute valeur de chaîne                | (périphérique. OSVersion - eq « 9.1 »)                                |
| isDirSynced             | null de false true                 | (device.isDirSynced - eq « true »)                             |
| isManaged               | null de false true                 | (device.isManaged - eq « false »)                              |
| isCompliant             | null de false true                 | (device.isCompliant - eq « true »)                             |
| deviceCategory          | toute valeur de chaîne                | (device.deviceCategory - eq « »)                              |
| deviceManufacturer      | toute valeur de chaîne                | (device.deviceManufacturer - eq « Microsoft »)                 |
| deviceModel             | toute valeur de chaîne                | (« IPhone » 7 + device.deviceModel - eq)                        |
| deviceOwnership         | toute valeur de chaîne                | (device.deviceOwnership - eq « »)                             |
| nom_domaine              | toute valeur de chaîne                | (device.domainName - eq « contoso.com »)                       |
| enrollmentProfileName   | toute valeur de chaîne                | (device.enrollmentProfileName - eq « »)                       |
| isRooted                | null de false true                 | (device.deviceOSType - eq « true »)                            |
| managementType          | toute valeur de chaîne                | (device.managementType - eq « »)                              |
| unité d’organisation      | toute valeur de chaîne                | (device.organizationalUnit - eq « »)                          |
| ID de périphérique                | un identificateur valide                | (device.deviceId - eq « d4fe7726-5966-431c-b3b8-cddc8fdb717d » |

> [AZURE.NOTE]
> Ces règles de périphériques ne peut pas être créés à l’aide de la liste déroulante « règle simple » dans Azure portal classique.


## <a name="additional-information"></a>Informations supplémentaires
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Résolution des problèmes d’appartenance dynamique pour les groupes](active-directory-accessmanagement-troubleshooting.md)

* [Gestion de l’accès aux ressources avec groupes Azure Active Directory](active-directory-manage-groups.md)

* [Azure applets de commande de Active Directory pour configurer les paramètres de groupe](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)

* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
