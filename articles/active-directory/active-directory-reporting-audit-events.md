<properties
   pageTitle="Événements de rapports d’audit Active Directory Azure | Microsoft Azure"
   description="Auditer les événements qui sont disponibles pour l’affichage et le téléchargement à partir d’Active Directory Azure"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/19/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-audit-report-events"></a>Azure événements de rapport d’audit Active Directory

*Cette documentation fait partie de la [Azure Reporting Guide de Active Directory] (active-directory-reporting-guide.md).*

Le rapport de vérification Azure Active Directory aide les clients à identifier les actions privilégiées qui s’est produite dans leur Azure d’Active Directory. Actions privilégiées incluent élévation (par exemple, création de rôle ou les réinitialisations de mot de passe), évolution des configurations de stratégie (par exemple les stratégies de mot de passe) ou les modifications apportées à la configuration de l’annuaire (par exemple, les modifications apportées aux paramètres de fédération de domaine). Les rapports fournissent l’enregistrement d’audit pour le nom de l’événement, l’acteur ayant effectué l’action, la ressource cible affectée par la modification, la date et l’heure (UTC). Les clients sont en mesure de récupérer la liste des événements d’audit pour leurs Azure d’Active Directory via le [Portail Azure](https://portal.azure.com/), comme indiqué dans [l’affichage des journaux d’Audit](active-directory-reporting-azure-portal.md).


## <a name="list-of-audit-report-events"></a>Liste des événements du rapport d’Audit
<!--- audit event descriptions should be in the past tense --->

Événements                               | Description de l’événement
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Événements utilisateur**                      |
Ajouter utilisateur                             | Permet d’ajouter un utilisateur au répertoire.
Supprimer l’utilisateur                          | Un utilisateur est supprimé de l’annuaire.
Définition des propriétés de licence               | Définissez les propriétés de la licence d’un utilisateur dans l’annuaire.
Réinitialiser le mot de passe utilisateur                  | Réinitialiser le mot de passe d’un utilisateur dans l’annuaire.
Modification mot de passe utilisateur                 | Permet de modifier le mot de passe d’un utilisateur dans l’annuaire.
Licence de changement d’utilisateur                  | Permet de modifier la licence attribuée à un utilisateur dans l’annuaire. Pour voir quelles licences ont été mis à jour, consultez les propriétés [d’utilisateur de mise à jour](#update-user-attributes) ci-dessous
Utilisateur de mise à jour                          | Mise à jour d’un utilisateur dans l’annuaire. [Voir ci-dessous](#update-user-attributes) pour les attributs qui peuvent être mis à jour.
Jeu de force modification utilisateur mot de passe       | Définissez la propriété qui force l’utilisateur à modifier son mot de passe de connexion.
Mettre à jour les informations d’identification de l’utilisateur        | Utilisateur a changé le mot de passe  
**Événements de groupe**                     |
Ajouter un groupe                            | Créer un groupe dans le répertoire.
Groupe de mise à jour                         | Mise à jour d’un groupe dans le répertoire. Pour afficher les propriétés de groupe ont été mis à jour, reportez-vous à [Auditer des propriétés de groupe](#update-group-attributes) de la section ci-dessous
Supprimer le groupe                         | Supprimer un groupe à partir du répertoire.
Ajouter des membres au groupe            | Ajouter un membre à un groupe dans le répertoire.
Supprimer des membres du groupe         | Supprimer un membre d’un groupe dans le répertoire.
CreateGroupSettings              | Paramètres de groupe
UpdateGroupSettings          | Mise à jour des paramètres de groupe. Pour afficher les paramètres de groupe ont été mis à jour, reportez-vous à [Auditer des propriétés de groupe](#update-group-attributes) de la section ci-dessous
DeleteGroupSettings            | Paramètres de groupe supprimé
SetGroupLicense                  | Licence de groupe ensemble.
SetGroupManagedBy              | Définir le groupe d’être géré par l’utilisateur
AddGroupMember               | Membres ajoutés au groupe
RemoveGroupMember            | Supprimer des membres du groupe
AddGroupOwner                | Propriétaire ajouté au groupe
RemoveGroupOwner                 | Propriétaire supprimé du groupe
**Événements d’application**               |
Ajouter principal du service                | Ajouté un principal de service dans le répertoire.
Supprimer l’entité de service             | Supprimer une entité de service à partir du répertoire.
Ajouter des informations d’identification principal de service    | Informations d’identification ajoutées à une entité de service.
Supprimer les informations d’identification principal de service | Informations d’identification supprimées à partir d’un service principal.
Ajoutez l’entrée de délégation                 | Créé un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) dans le répertoire.
Entrée de délégation de jeu                 | Mise à jour d’une [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) dans le répertoire.
Supprimer l’entrée de délégation              | Permet de supprimer un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) dans le répertoire.
**Événements de rôle**                      |
Ajouter des membres de rôle au rôle              | Permet d’ajouter un utilisateur à un rôle de répertoire.
Supprimer des membres de rôle du rôle         | Supprimer un utilisateur d’un rôle de répertoire.
Définir les informations de contact société      | Définir les préférences de contact au niveau de l’entreprise. Cela inclut les adresses de messagerie pour les notifications de marketing, ainsi que les techniques à propos de Microsoft Online Services.
Ajoutez l’entrée de délégation                 | Créé un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) dans le répertoire.
Entrée de délégation de jeu                 | Mise à jour d’une [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) dans le répertoire.
Supprimer l’entrée de délégation              | Permet de supprimer un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) dans le répertoire.
AddSevicePrincipalOwner          | Propriétaire ajouté à l’entité de sécurité de service.
RemoveSevicePrincipalOwner   | Supprimé le propriétaire principal du service.
AddApplication  | Ajouter une application.
UpdateApplication | Mise à jour d’application. Pour savoir quels paramètres d’application ont été mis à jour, reportez-vous à [Auditer des propriétés d’Application](#update-application-attributes) dans la section ci-dessous
DeleteApplication | Supprimer l’application.
RestoreApplication|Restauration d’application.
AddApplicationOwner   |     Ajoutez le propriétaire de l’application.
RemoveApplicationOwner    |     Supprimer le propriétaire de l’application.
**Événements de rôle**                         |
Ajouter des membres de rôle au rôle                 | Permet d’ajouter un utilisateur à un rôle de répertoire.
Supprimer des membres de rôle du rôle            | Supprimer un utilisateur d’un rôle de répertoire.
AddRoleDefinition               | Définition du rôle ajouté.
UpdateRoleDefinition                | Mise à jour de la définition de rôle. Pour afficher les paramètres de rôle ont été mis à jour, reportez-vous à la section [Audit de propriétés de définition de rôle](#update-role-definition-attributes) dans la section ci-dessous
DeleteRoleDefinition                | Supprimer la définition de rôle.
AddRoleAssignmentToRoleDefinition | Affectation de rôle ajouté à la définition de rôle.
RemoveRoleAssignmentFromRoleDefinition | Affectation de rôle supprimé à partir de la définition de rôle.
AddRoleFromTemplate     | Rôle ajouté à partir du modèle.
UpdateRole  | Rôle mis à jour.
AddRoleScopeMemberToRole    | Membre de portée ajouté au rôle.
RemoveRoleScopedMemberFromRole  | Supprimé étendue membre de rôle.
**Événements de périphériques (tous les nouveaux événements)**                    |
Ajout de périphérique               | Périphérique ajouté.
UpdateDevice            | Périphérique mis à jour. Pour afficher les propriétés du périphérique ont été mis à jour, reportez-vous aux [Propriétés d’un périphérique contrôlé](#update-device-attributes) dans la section ci-dessous
DeleteDevice            | Appareil de communication supprimé.
AddDeviceConfiguration      | Configuration du périphérique.
UpdateDeviceConfiguration   | Configuration de périphérique mis à jour. Pour afficher les propriétés de configuration du périphérique ont été mis à jour, consultez [Propriétés de configuration de périphérique contrôlé](#update-device-configuration-attributes) dans la section ci-dessous
DeleteDeviceConfiguration   | Configuration de l’appareil de communication supprimé.
AddRegisteredOwner     | Propriétaire inscrit ajouté au périphérique.
AddRegisteredUsers     | Utilisateurs inscrits pour le périphérique.
RemoveRegisteredOwner    | Supprimer le propriétaire inscrit de périphérique.
RemoveRegisteredUsers    | Supprimer les utilisateurs enregistrés de périphérique.
RemoveDeviceCredentials  | Supprimer les informations d’identification de périphérique.
**Événements B2B**                       |
Les invitations de lot téléchargement.              | Un administrateur a téléchargé un fichier contenant les invitations à envoyer aux utilisateurs partenaire.
Invitations par lots traitées.             | Un fichier contenant des invitations aux utilisateurs partenaire a été traité.
Inviter un utilisateur externe.                | Un utilisateur externe a été invité à l’annuaire.
Échanger les invitation utilisateur externe.         | Un utilisateur externe a échangé leur invitation au répertoire.
Ajouter un utilisateur externe au groupe.          | Un utilisateur externe a été affecté à l’appartenance à un groupe dans le répertoire.
Affecter l’utilisateur externe à l’application. | Un utilisateur externe a été affecté à un accès direct à une application.
Création de clients virale.               | Un nouveau locataire a été créé dans Active Directory Azure par le remboursement de l’invitation.
Création de l’utilisateur virale.                 | Un utilisateur a été créé dans un client existant dans Azure AD par le remboursement de l’invitation.
**Unités administratives (tous les nouveaux événements)**             |
AddAdministrativeUnit   |   Ajouter une unité d’administration.
UpdateAdministrativeUnit|   Unité administrative de la mise à jour. Pour afficher les propriétés d’une unité d’administration ont été mis à jour, reportez-vous à [Auditer des propriétés d’une unité d’administration](#update-administrative-unit-attributes) dans la section ci-dessous
DeleteAdministrativeUnit|   Supprimer une unité d’administration.
AddMemberToAdministrativeUnit|  Ajouter des membres à une unité d’administration.
RemoveMemberFromAdministrativeUnit|     Supprimer des membres d’unité administrative.
**Événements d’annuaire**                 |
Ajouter un partenaire de la société               | Ajouter un partenaire dans le répertoire.
Supprimer un partenaire de la société          | Supprimer un partenaire à partir du répertoire.
DemotePartner   |   Rétrograder le partenaire.
Ajouter le domaine de la société                | Ajouter un domaine à l’annuaire.
Supprimer le domaine de la société           | Supprimé un domaine de l’annuaire.
Mettre à jour le domaine                        | Mise à jour d’un domaine sur le répertoire. Pour afficher les propriétés du domaine ont été mis à jour, reportez-vous à [Propriétés de domaine contrôlés](#update-domain-attributes) dans la section ci-dessous
Définir l’authentification de domaine            | Permet de modifier le paramètre de domaine par défaut pour la société.
Définir les informations de contact société      | Définir les préférences de contact au niveau de l’entreprise. Cela inclut les adresses de messagerie pour les notifications de marketing, ainsi que les techniques à propos de Microsoft Online Services.
Définir les paramètres de fédération de domaine    | Mettre à jour les paramètres de fédération d’un domaine.
Vérifiez le domaine                        | Permet de vérifier un domaine sur le répertoire.
Vérifiez le domaine de messagerie vérifié         | Permet de vérifier un domaine sur le répertoire à l’aide de la vérification du courrier électronique.
Indicateur de DirSyncEnabled jeu de société   | Définissez la propriété qui permet à un répertoire pour Azure la synchronisation Active Directory.
Définir la stratégie de mot de passe                  | Définir les contraintes de longueur et les caractères des mots de passe utilisateur.
Informations de jeu de société              | Mettre à jour les informations au niveau de l’entreprise. Consultez l’applet de commande [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) PowerShell pour plus de détails.
SetCompanyAllowedDataLocation  |    Jeu de société autorisé l’emplacement des données.
SetCompanyDirSyncEnabled    |   Définir l’indicateur de DirSyncEnabled.
SetCompanyDirSyncFeature |  Définir la fonctionnalité de synchronisation d’annuaire.
SetCompanyInformation |   Informations sur la société de l’ensemble.
SetCompanyMultiNationalEnabled    |     Définir société multinationale fonctionnalité est activée.
SetDirectoryFeatureOnTenant   |     Définir la fonctionnalité de répertoire sur les clients.
SetTenantLicenseProperties  |   Définir les propriétés des licences clients.
CreateCompanySettings   |   Créer des paramètres de la société
UpdateCompanySettings   |   Mettre à jour les paramètres de la société. Pour afficher les propriétés de la société ont été mis à jour, reportez-vous à [Auditer des propriétés de la société](#update-company-attributes) dans la section ci-dessous
DeleteCompanySettings   |   Supprimer les paramètres de la société
SetAccidentalDeletionThreshold   |  Définir le seuil de suppression accidentelle.
SetRightsManagementProperties   |   Permet de définir des propriétés de gestion de droits.
PurgeRightsManagementProperties     |   Purger les propriétés de gestion de droits.
UpdateExternalSecrets   |   Mettre à jour les secrets externes
**Événements de stratégie (tous les nouveaux événements)**           |
AddPolicy   |   Ajouter une stratégie.
UpdatePolicy    |   Mise à jour de la stratégie.
DeletePolicy    |   Supprimer la stratégie.
AddDefaultPolicyApplication     |   Ajouter la stratégie d’application.
AddDefaultPolicyServicePrincipal    |   Ajouter la stratégie à principal du service.
RemoveDefaultPolicyApplication  |   Supprimer la stratégie d’application.
RemoveDefaultPolicyServicePrincipal     |   Supprimer la stratégie à partir de principal du service.
RemovePolicyCredentials     |   Supprimer les informations d’identification de la stratégie.

## <a name="audit-report-retention"></a>Rétention du rapport d’audit
Dans le rapport d’Audit de publicité Azure, les événements sont conservés pendant 180 jours. Pour plus d’informations sur la rétention sur les rapports, voir [Stratégies de rétention de rapport Azure pour Active Directory](active-directory-reporting-retention.md).

Aux clients qui souhaitent stocker leurs événements d’audit pour les périodes de rétention, l’API de création de rapports peut être destiné à régulièrement les événements d’audit dans un magasin de données distinct. Pour plus d’informations, consultez [Mise en route avec l’API de création de rapports](active-directory-reporting-api-getting-started.md) .

## <a name="properties-included-with-each-audit-event"></a>Propriétés incluses dans chaque événement d’audit

Propriété      | Description
------------- | --------------------------------------------------------------
Date et heure | La date et l’heure de l’événement d’audit
Acteur         | L’utilisateur ou le principal de service qui a effectué l’action
Action        | L’action a été effectuée
Cible        | L’utilisateur ou le principal de service que l’action a été exécutée sur


## <a name="update-user-attributes"></a>Attributs de « Mise à jour d’utilisateur »
L’événement d’audit « Utilisateur de mise à jour » comprend des informations supplémentaires sur les attributs utilisateur ont été mis à jour. Pour chaque attribut, la valeur précédente et la nouvelle valeur est inclus.

Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled                      | L’utilisateur peut se connecter.
AssignedLicense                     | Toutes les licences qui ont été affectées à l’utilisateur.
AssignedPlan                        | Plans de service résultant de licences affectées à l’utilisateur.
LicenseAssignmentDetail             | Plus d’informations sur les licences attribuées à l’utilisateur. Par exemple, si basée sur le groupe de gestion de licences a été impliquée, cela inclut le groupe qui a accordé la licence.
Mobile                              | Téléphone mobile de l’utilisateur.
OtherMail                           | Adresse de messagerie alternative de l’utilisateur.
OtherMobile                         | Téléphone mobile de la substitution de l’utilisateur.
StrongAuthenticationMethod          | Une liste de méthodes de vérification configuré par l’utilisateur pour l’authentification selon plusieurs facteurs, comme appeler, SMS ou vérification du code à partir d’une application mobile.
StrongAuthenticationRequirement     | Si une authentification multifactorielle est appliquée, activé ou désactivé pour cet utilisateur.
StrongAuthenticationUserDetails     | Numéro de téléphone de l’utilisateur, autre téléphone numéro et l’adresse e-mail utilisée pour la vérification de réinitialisation de mot de passe et d’authentification à plusieurs facteurs.
StrongAuthenticationPhoneAppDetail  | Applications de phone forof détail enregistrées pour effectuer l’ouverture de session 2FA
Numéro de téléphone                     | Numéro de téléphone de l’utilisateur.
AlternativeSecurityId               | Un ID de sécurité de remplacement pour l’objet.
CreationType                        |Méthode de création de l’utilisateur (soit par adjudication ou virale).
InviteTicket                        |Liste des tickets d’invitation pour l’utilisateur.
InviteReplyUrl                      |Liste des URL de répondre après l’acceptation de l’invitation.
InviteResources                     |Liste des ressources auxquelles l’utilisateur a été invité.
LastDirSyncTime                     |Dernière fois que l’objet a été mis à jour en raison de la synchronisation à partir de l’autorité répertoire (client, sur site).
MSExchRemoteRecipientType           |Mappages de types de destinataires MSO. Reportez-vous à https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx [types de destinataires MSO] pour les types de destinataires
PreferredDataLocation               |L’emplacement par défaut pour l’utilisateur, du groupe, du contact, du dossier public, ou les données du périphérique.
ProxyAddresses                      |L’adresse à laquelle un objet de destinataire Exchange Server est reconnu dans un système de messagerie étranger.
StsRefreshTokensValidFrom           |Ballon d’actualiser les informations de révocation token - des jetons d’actualisation STS émis avant cette heure sont considérés comme expiré.
UserPrincipalName                   |L’UPN est un nom d’ouverture de session de type Internet pour un utilisateur.
UserState                           |Statut de l’utilisateur approbation en cours/PendingAcceptance/accepté/PendingVerification.
UserStateChangedOn                  |Horodatage de la dernière modification apportée à UserState. Permet de déclencher des flux de travail du cycle de vie.
UserType                            |Type d’utilisateur. Membres (0), l’invité (1), Viral(2).

## <a name="update-group-attributes"></a>Attributs « Update Group »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Classification            | La classification d’un groupe unifiée (IEA, IMA, etc.).
Description               | Phrases descriptives explicite sur l’objet.  
DisplayName               |Le nom complet d’un objet
DirSyncEnabled            |Indique si la synchronisation intervient à partir d’une autorité répertoire (client, sur site).
GroupLicenseAssignment    |Affectation de licence d’un groupe
GroupType                 |Type de groupe, unifiée (0)
IsMembershipRuleLocked    |Indique que la propriété MembershipRule est définie par le service de gestion libre-service de groupe et ne peut pas être modifiée par les utilisateurs. Applicable uniquement aux groupes où GroupType inclut GroupType.DynamicMembership
IsPublic                  |Indicateur pour indiquer si le groupe est publique/privée.
LastDirSyncTime           |Dernière fois que l’objet a été mis à jour suite à la synchronisation à partir de l’autorité répertoire de (client, sur site).
Courrier                      |Adresse de messagerie principale
MailEnabled               |Indique si ce groupe a fonction de courrier électronique.
MailNickname              |Moniker pour un objet de carnet d’adresses, en général la partie de son nom de messagerie précédent le "@" symbole.   
MembershipRule            |Une chaîne exprimant les critères utilisés par le service de gestion libre-service de groupe pour déterminer quels membres doivent appartenir à ce groupe. Voir aussi IsMembershipRuleLocked. Applicable uniquement aux groupes où GroupType inclut GroupType.DynamicMembership.
MembershipRuleProcessingState| Valeur enum définie par le service de gestion libre-service de groupe définissant l’état d’appartenance pour ce groupe. Applicable uniquement aux groupes où GroupType inclut GroupType.DynamicMembership.
ProxyAddresses            |L’adresse à laquelle un objet de destinataire Exchange Server est reconnu dans un système de messagerie étranger.
RenewedDateTime           |Enregistrement d’horodatage de lorsqu’un groupe a été récemment renouvelé.   
SecurityEnabled           |Indique si l’appartenance du groupe peut-être influencer les décisions d’autorisation.
WellKnownObject           |Un objet d’annuaire, désigner comme partie d’un ensemble prédéfini des étiquettes.

## <a name="update-device-attributes"></a>Attributs de « Mise à jour de périphérique »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | Indique si une entité de sécurité peut s’authentifier.
CloudAccountEnabled | Indique si une entité de sécurité peut s’authentifier. Écrit par InTune lorsque le périphérique est gravé sur le site.
CloudDeviceOSType | Type de périphérique basé sur le système d’exploitation, par exemple Windows RT, e/s. Lorsque la valeur par un service cloud (par exemple, Intune), cet attribut devient faisant autorité dans le répertoire de DeviceOSType.
CloudDeviceOSVersion | Version du système d’exploitation. Lorsque la valeur par un service cloud (par exemple, Intune), cet attribut devient faisant autorité dans le répertoire de DeviceOSVersion.
CloudDisplayName | Valeur de l’attribut LDAP displayName. Lorsque la valeur par un service cloud (par exemple, Intune), cet attribut devient faisant autorité dans le répertoire de displayName.
CloudCreated |Indique si l’objet a été créé par les services en nuage.
CompliantUntil | Jusqu'à quel périphérique est considéré comme conforme.
DeviceMetadata |Métadonnées personnalisées pour le périphérique
DeviceObjectVersion | Cet attribut est utilisé pour identifier la version du schéma de l’équipement.
DeviceOSType |Type de périphérique basé sur le système d’exploitation, par exemple Windows RT, e/s. Écrit par le Service d’enregistrement et destinés à être mis à jour par le MDM de service de gestion ou de SharePoint Team Services lumière service Gestion des services.
DeviceOSVersion |Version du système d’exploitation. Écrit par le Service d’enregistrement et destinés à être mis à jour par le MDM de service de gestion ou de SharePoint Team Services lumière service Gestion des services.
DevicePhysicalIds |Attribut à valeurs multiples destiné à stocker les identificateurs de l’unité physique. Il peut s’agir du BIOS ID, les empreintes de module de plateforme sécurisée, matériel ID spécifiques, etc..
DirSyncEnabled | Indique si la synchronisation intervient à partir d’un autorité (client, sur site) répertoire.
DisplayName |Le nom complet d’un objet
IsCompliant | Cet attribut est utilisé pour gérer l’état de gestion de périphérique mobile du périphérique.
IsManaged |Cet attribut est utilisé pour indiquer que le périphérique est géré par un nuage MDM.
LastDirSyncTime |Dernière fois que l’objet a été mis à jour en raison de la synchronisation à partir de l’autorité répertoire de (client, sur site).

## <a name="update-device-configuration-attributes"></a>Attributs de « Mise à jour de la Configuration de périphérique »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
MaximumRegistrationInactivityPeriod |Le nombre maximal de jours de qu'un volume peut être inactif avant qu’il est pris en compte pour la suppression.
RegistrationQuota   |Stratégie utilisée pour limiter le nombre d’enregistrements du périphérique autorisé pour un seul utilisateur.

## <a name="update-service-principal-configuration-attributes"></a>Attributs de « Mise à jour de la Configuration du Service principale »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled |Indique si une entité de sécurité peut s’authentifier.
AppPrincipalId | Identité externe, définie par l’application pour une entité de sécurité.
DisplayName |Le nom complet d’un objet
Nom principal de service    | Un nom principal de service, contenant « nom/autorité » où nom spécifie une valeur de classe d’application et autorité contienne au moins nom d’hôte [ : port] ou « nom » qui spécifie un identificateur de l’entité de sécurité du service.

## <a name="update-app-attributes"></a>Attributs de « Mise à jour d’application »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |L’ensemble des adresses (URL de redirection) qui sont affectées à un principal de service.
AppId                               |ID de l’application de l’application   
AppIdentifierUri | Application URI, qui identifie l’application.  Il est généralement l’URL d’application access.
AppLogoUrl |L’url de l’image du logo application stocké dans un réseau CDN.
AvailableToOtherTenants | True l’application est partagée (par exemple, peut être utilisé par les autres locataires).
DisplayName | Le nom complet d’un nom d’Application
Droit |Liste des droits de l’application.
ExternalUserAccountDelegationsAllowed |Indicateur signalant si application de ressource est approuvé et peut créer des entrées de délégation pour les comptes d’utilisateur externe.
GroupMembershipClaims |La stratégie de créances de l’appartenance au groupe.
PublicClient | True si le client ne peut pas garder secret (c'est-à-dire non confidentielle client du OAuth2.0)
RecordConsentConditions | Types de conditions de consentement, tel que défini par les termes du contrat : aucun (0), SilentConsentForPartnerManagedApp(1). Cette valeur sera exposée dans le schéma de l’API de graphique et ne peut être un ensemble/modifiés par les administrateurs de clients.
RequiredResourceAccess |Contenu XML d’une valeur de la propriété RequiredResourceAccess.
WebApp |Si la valeur est true, indique que cette application est une application web.
WwwHomepage |La page Web principale.

## <a name="update-role-attributes"></a>Attributs de « Mise à jour du rôle »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |L’ensemble des adresses (URL de redirection) qui sont affectées à un principal de service.
BelongsToFirstLoginObjectSet |Si la valeur est true, indique que cet objet appartient à l’ensemble des objets requis pour activer la connexion de l’administrateur premier de nouveau locataire.
BuiltIn |Indique si la durée de vie d’un objet est détenue par le système.
Description | Phrases descriptives explicite sur l’objet.  
DisplayName |Le nom complet d’un objet
MailNickname | Moniker pour un objet de carnet d’adresses, en général la partie de son nom de messagerie précédent le "@" symbole.
RoleDisabled |Indique si le rôle doit être ignoré à des fins de contrôle d’accès.
RoleTemplateId | Identité du modèle de rôle.
ServiceInfo |Service de mise en service des informations spécifiques qui peuvent être consommées par MOAC et/ou d’autres instances de service (des types de service identiques ou différents).
TaskSetScopeReference |Identifie une tâcheDéfinir et un ensemble de zones associées à un rôle ou à RoleTemplate.
ValidationError |Informations publiées par un service fédéré décrivant une erreur non transitoires et spécifiques au service en ce qui concerne les propriétés ou le lien à partir d’une action de l’administrateur objet à résoudre.
WellKnownObject |Un objet d’annuaire, désigner comme partie d’un ensemble prédéfini des étiquettes.

## <a name="update-role-definition-attributes"></a>Attributs de « Mise à jour de la définition de rôle »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AssignableScopes    |Collection d’étendues d’autorisation qui peuvent être référencés lors de l’affectation de cette RoleDefinition à une entité de sécurité.
DisplayName     |Le nom complet d’un objet
GrantedPermissions  |Autorisations accordées par cette RoleDefinition.


## <a name="update-administrative-unit-attributes"></a>Attributs de « Mise à jour d’unité Administrative »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Description |   Cette propriété est mise à jour lorsque vous modifiez la description d’une unité d’administration.
DisplayName |   Cette propriété est mise à jour lorsque vous modifiez le nom d’une unité d’administration.

## <a name="update-company-attributes"></a>Attributs « Société de mise à jour »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AllowedDataLocation     | Un emplacement dans lequel les utilisateurs de l’entreprise sont autorisés à être mis en service.
AuthorizedServiceInstance   | Noms d’instances de service pour laquelle un plan peut être déployé.
DirSyncEnabled |Indique si la synchronisation intervient à partir d’un autorité (client, sur site) répertoire.
DirSyncStatus |Indique si la synchronisation des objets de carnet d’adresse dans ce contexte de clients se produit à partir d’un autorité (client, sur site) répertoire ; une expansion de la propriété DirSyncEnabled sur les objets de la société.
DirSyncFeatures |Bit indicateur pour effectuer le suivi de l’ensemble des fonctionnalités de synchronisation d’annuaire activés et désactivés pour les clients.
DirectoryFeatures |Fonctions activées/désactivées directory.
DirSyncConfiguration |Contient toutes les configuration de synchronisation d’annuaire spécifique au locataire actuel.
DisplayName |Le nom complet d’un objet
IsMnc |Un indicateur booléen la valeur iff « true », que la société est activée pour la fonctionnalité de société multinationale.
ObjectSettings | Un ensemble de paramètres applicables à la portée de l’objet.
PartnerCommerceUrl |URL du site du partenaire commerce.
PartnerHelpUrl |URL du site du partenaire aide.
PartnerSupportEmail | URL de l’e-mail de support du partenaire.
PartnerSupportTelephone |URL de téléphone du support technique du partenaire.
PartnerSupportUrl |URL vers le site de support du partenaire.
StrongAuthenticationDetails |Détails relatifs aux StrongAuthentication.
StrongAuthenticationPolicy |Stratégie d’authentification forte de la société.
TechnicalNotificationMail |Adresse de messagerie pour notifier les problèmes techniques relatives à une société.
Numéro de téléphone |Numéros de téléphone qui respectent l’ITU Recommendation E.123.
TenantType |Le type d’un client. Si cette valeur n’est pas spécifiée, le client est une société. Dans le cas contraire, les valeurs possibles sont : MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) (3) de BreadthPartnerDelegatedAdmin ValueAddedResellerPartnerDelegatedAdmin de ResellerPartnerDelegatedAdmin (4) (5).
VerifiedDomain |Un ensemble de noms de domaine DNS est lié à une société.

## <a name="update-domain-attributes"></a>Attributs de « Mise à jour de domaine »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Fonctionnalités    |Bits indicateurs qui décrivent les fonctionnalités du domaine, le cas échéant.
Par défaut |Indique si le domaine est la valeur par défaut ; par exemple, le suffixe par défaut UserPrincipalName lorsqu’un administrateur crée un nouvel utilisateur dans MOAC.
Initiale |Indique si le domaine est le domaine initial de la société, comme allouée par OCP. Le domaine initial est un sous-domaine unique d’un domaine Microsoft Online ; e.g.contoso3.microsoftonline.com.
LiveType    |Type de Windows Live espace de noms correspondant, le cas échéant.
Nom    | Identificateur du point de terminaison.
PasswordNotificationWindowDays  |Le nombre de jours avant l’expiration de mot de passe l’utilisateur est averti.
PasswordValidityPeriodDays  | Le nombre de jours d’un mot de passe pour avant qu’elle doit être modifiée.

Les enregistrements d’audit sont un contrôle requis pour de nombreuses réglementations. Pour les clients à l’aide de l’état de l’Audit de répertoire Azure Active pour répondre à leurs obligations réglementaires, il est recommandé que le client soumettre une copie de cette rubrique d’aide avec la copie du rapport d’audit exporté du client permettent de décrire les détails du rapport. Si vous souhaitez comprendre les respect des réglementations répondant Azure actuellement l’auditeur, diriger l’auditeur à la [page de mise en conformité](https://azure.microsoft.com/support/trust-center/compliance/) de la Microsoft Azure Trust Center.
