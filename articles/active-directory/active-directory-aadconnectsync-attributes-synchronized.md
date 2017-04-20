<properties
    pageTitle="Azure Connect de AD sync : attributs synchronisés vers Azure Active Directory | Microsoft Azure"
    description="Répertorie les attributs qui sont synchronisées avec Azure Active Directory."
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
    ms.date="09/13/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure Connect de AD sync : attributs synchronisés vers Azure Active Directory
Cette rubrique répertorie les attributs qui sont synchronisés par synchronisation d’Azure Connect d’AD.  
Les attributs sont regroupés par la Azure connexe AD app.

## <a name="attributes-to-synchronize"></a>Pour synchroniser les attributs
Une question courante est *qu’est la liste des attributs minimum à synchroniser*. Par défaut et l’approche recommandée consiste à conserver les attributs par défaut pour une liste d’adresses globale complète (liste d’adresses globale) peut être construit dans le nuage et pour obtenir toutes les fonctionnalités dans Office 365 les charges de travail. Dans certains cas, il y a des attributs de votre organisation ne souhaite pas synchronisées vers le nuage dans la mesure où ces attributs contiennent sensible ou des données personnelles (informations d’identification personnelle), comme dans cet exemple :  
![attributs incorrects](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

Dans ce cas, démarrer avec la liste d’attributs dans cette rubrique et d’identifier les attributs qui contiendraient des données sensibles ou personnelles et ne peut pas être synchronisés. Désactivez ces attributs lors de l’installation à l’aide [d’Azure AD app et le filtrage de l’attribut](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

>[AZURE.WARNING] Lorsque vous désélectionnez attributs, soyez prudent et désélectionnez l’option uniquement les attributs absolument pas possibles de synchroniser. Désélection d’autres attributs susceptibles d’avoir un impact négatif sur les fonctionnalités.

## <a name="office-365-proplus"></a>Office 365 ProPlus

| Nom de l’attribut| Utilisateur| Commentaire |
| --- | :-: | --- |
| accountEnabled| X| Définit si un compte est activé.|
| NC| X|  |
| displayName| X|  |
| objectSID| X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et AD.|
| pwdLastSet| X| propriété mécanique. Permet de savoir quand à invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et de fédération.|
| sourceAnchor| X| propriété mécanique. Identificateur immuable pour conserver la relation entre ajoute et Azure AD.|
| usageLocation| X| propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X| Nom UPN est l’ID de connexion de l’utilisateur. La plupart du temps la valeur identique au [mail].|

## <a name="exchange-online"></a>Exchange en ligne

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| Assistant| X| X|  |  |
| authOrig| X| X| X|  |
| c| X| X|  |  |
| NC| X|  | X|  |
| CO| X| X|  |  |
| société| X| X|  |  |
| countryCode| X| X|  |  |
| département| X| X|  |  |
| Description| X| X| X|  |
| displayName| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| homePhone| X| X|  |  |
| Info| X| X| X| Cet attribut n’est actuellement pas utilisé pour les groupes.|
| Initiales| X| X|  |  |
| l| X| X|  |  |
| legacyExchangeDN| X| X| X|  |
| mailNickname| X| X| X|  |
| mangedBy|  |  | X|  |
| Gestionnaire de| X| X|  |  |
| membre|  |  | X|  |
| Mobile| X| X|  |  |
| msDS-HABSeniorityIndex| X| X| X|  |
| msDS-PhoneticDisplayName| X| X| X|  |
| msExchArchiveGUID| X|  |  |  |
| msExchArchiveName| X|  |  |  |
| forme msExchAssistantName| X| X|  |  |
| msExchAuditAdmin| X|  |  |  |
| msExchAuditDelegate| X|  |  |  |
| msExchAuditDelegateAdmin| X|  |  |  |
| msExchAuditOwner| X|  |  |  |
| msExchBlockedSendersHash| X| X|  |  |
| msExchBypassAudit| X|  |  |  |
| msExchCoManagedByLink|  |  | X|  |
| msExchDelegateListLink| X|  |  |  |
| msExchELCExpirySuspensionEnd| X|  |  |  |
| msExchELCExpirySuspensionStart| X|  |  |  |
| msExchELCMailboxFlags| X|  |  |  |
| msExchEnableModeration| X|  | X|  |
| msExchExtensionCustomAttribute1| X| X| X| Cet attribut n’est pas actuellement utilisé par Exchange en ligne. |
| msExchExtensionCustomAttribute2| X| X| X| Cet attribut n’est pas actuellement utilisé par Exchange en ligne. |
| msExchExtensionCustomAttribute3| X| X| X| Cet attribut n’est pas actuellement utilisé par Exchange en ligne. |
| msExchExtensionCustomAttribute4| X| X| X| Cet attribut n’est pas actuellement utilisé par Exchange en ligne. |
| msExchExtensionCustomAttribute5| X| X| X| Cet attribut n’est pas actuellement utilisé par Exchange en ligne. |
| msExchHideFromAddressLists| X| X| X|  |
| msExchImmutableID| X|  |  |  |
| msExchLitigationHoldDate| X| X| X|  |
| msExchLitigationHoldOwner| X| X| X|  |
| msExchMailboxAuditEnable| X|  |  |  |
| msExchMailboxAuditLogAgeLimit| X|  |  |  |
| msExchMailboxGuid| X|  |  |  |
| msExchModeratedByLink| X| X| X|  |
| msExchModerationFlags| X| X| X|  |
| msExchRecipientDisplayType| X| X| X|  |
| msExchRecipientTypeDetails| X| X| X|  |
| msExchRemoteRecipientType| X|  |  |  |
| msExchRequireAuthToSendTo| X| X| X|  |
| msExchResourceCapacity| X|  |  |  |
| msExchResourceDisplay| X|  |  |  |
| msExchResourceMetaData| X|  |  |  |
| msExchResourceSearchProperties| X|  |  |  |
| msExchRetentionComment| X| X| X|  |
| msExchRetentionURL| X| X| X|  |
| msExchSafeRecipientsHash| X| X|  |  |
| msExchSafeSendersHash| X| X|  |  |
| msExchSenderHintTranslations| X| X| X|  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| msExchUserHoldPolicies| X|  |  |  |
| msOrg-IsOrganizational|  |  | X|  |
| objectSID| X|  | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherTelephone| X| X|  |  |
| récepteur de radiomessagerie| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| code postal| X| X|  |  |
| proxyAddresses| X| X| X|  |
| publicDelegates| X| X| X|  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand à invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et de fédération.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Dérivé de groupType|
| n° de série| X| X|  |  |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour conserver la relation entre ajoute et Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| numéro de téléphone| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| titre| X| X|  |  |
| unauthOrig| X| X| X|  |
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userCertificate| X| X|  |  |
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion de l’utilisateur. La plupart du temps la valeur identique au [mail].|
| userSMIMECertificates| X| X|  |  |
| wWWHomePage| X| X|  |  |

## <a name="sharepoint-online"></a>SharePoint Online

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| authOrig| X| X| X|  |
| c| X| X|  |  |
| NC| X|  | X|  |
| CO| X| X|  |  |
| société| X| X|  |  |
| countryCode| X| X|  |  |
| département| X| X|  |  |
| Description| X| X| X|  |
| displayName| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| hideDLMembership|  |  | X|  |
| HomePhone| X| X|  |  |
| Info| X| X| X|  |
| initiales| X| X|  |  |
| ipPhone de l'| X| X|  |  |
| l| X| X|  |  |
| courrier| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Gestionnaire de| X| X|  |  |
| membre|  |  | X|  |
| middleName| X| X|  |  |
| Mobile| X| X|  |  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointLinkedBy| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| objectSID| X|  | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherIpPhone| X| X|  |  |
| otherMobile| X| X|  |  |
| otherPager| X| X|  |  |
| otherTelephone| X| X|  |  |
| récepteur de radiomessagerie| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| code postal| X| X|  |  |
| postOfficeBox| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand à invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et de fédération.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Dérivé de groupType|
| n° de série| X| X|  |  |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour conserver la relation entre ajoute et Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| numéro de téléphone| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| titre| X| X|  |  |
| unauthOrig| X| X| X|  |
| URL| X| X|  |  |
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion de l’utilisateur. La plupart du temps la valeur identique au [mail].|
| wWWHomePage| X| X|  |  |

## <a name="lync-online"></a>Lync Online

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| c| X| X|  |  |
| NC| X|  | X|  |
| CO| X| X|  |  |
| société| X| X|  |  |
| département| X| X|  |  |
| Description| X| X| X|  |
| displayName| X| X| X|  |
| facsimiletelephonenumber| X| X| X|  |
| givenName| X| X|  |  |
| HomePhone| X| X|  |  |
| ipPhone de l'| X| X|  |  |
| l| X| X|  |  |
| courrier| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Gestionnaire de| X| X|  |  |
| membre|  |  | X|  |
| Mobile| X| X|  |  |
| msExchHideFromAddressLists| X| X| X|  |
| msRTCSIP-ApplicationOptions| X|  |  |  |
| msRTCSIP-DeploymentLocator| X| X|  |  |
| msRTCSIP-Line| X| X|  |  |
| msRTCSIP-OptionFlags| X| X|  |  |
| msRTCSIP-OwnerUrn| X|  |  |  |
| msRTCSIP-PrimaryUserAddress| X| X|  |  |
| msRTCSIP-UserEnabled| X| X|  |  |
| objectSID| X|  | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et AD.|
| otherTelephone| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| code postal| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand à invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et de fédération.|
| securityEnabled|  |  | X| Dérivé de groupType|
| n° de série| X| X|  |  |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour conserver la relation entre ajoute et Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| numéro de téléphone| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| titre| X| X|  |  |
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion de l’utilisateur. La plupart du temps la valeur identique au [mail].|
| wWWHomePage| X| X|  |  |

## <a name="azure-rms"></a>RMS Azure

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| NC| X|  | X| Nom commun ou alias. La plupart du temps, le préfixe de la valeur de [mail].|
| displayName| X| X| X| Une chaîne qui représente le nom affiché souvent comme nom convivial (nom prénom).|
| courrier| X| X| X| adresse de messagerie complète.|
| membre|  |  | X|  |
| objectSID| X|  | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et AD.|
| proxyAddresses| X| X| X| propriété mécanique. Utilisé par Active Directory Azure. Contient toutes les adresses de messagerie secondaires pour l’utilisateur.|
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand à invalider les jetons déjà émis.|
| securityEnabled|  |  | X| Dérivé de groupType.|
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour conserver la relation entre ajoute et Azure AD.|
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X|  |  | Cet UPN est l’ID de connexion de l’utilisateur. La plupart du temps la valeur identique au [mail].|

## <a name="intune"></a>Intune

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| c| X| X|  |  |
| NC| X|  | X|  |
| Description| X| X| X|  |
| displayName| X| X| X|  |
| courrier| X| X| X|  |
| mailNickname| X| X| X|  |
| membre|  |  | X|  |
| objectSID| X|  | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand à invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et de fédération.|
| securityEnabled|  |  | X| Dérivé de groupType|
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour conserver la relation entre ajoute et Azure AD.|
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion de l’utilisateur. La plupart du temps la valeur identique au [mail].|

## <a name="dynamics-crm"></a>Dynamics CRM

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| c| X| X|  |  |
| NC| X|  | X|  |
| CO| X| X|  |  |
| société| X| X|  |  |
| countryCode| X| X|  |  |
| Description| X| X| X|  |
| displayName| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| l| X| X|  |  |
| managedBy|  |  | X|  |
| Gestionnaire de| X| X|  |  |
| membre|  |  | X|  |
| Mobile| X| X|  |  |
| objectSID| X|  | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et AD.|
| physicalDeliveryOfficeName| X| X|  |  |
| code postal| X| X|  |  |
| preferredLanguage| X|  |  |  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand à invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et de fédération.|
| securityEnabled|  |  | X| Dérivé de groupType|
| n° de série| X| X|  |  |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour conserver la relation entre ajoute et Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| numéro de téléphone| X| X|  |  |
| titre| X| X|  |  |
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion de l’utilisateur. La plupart du temps la valeur identique au [mail].|

## <a name="3rd-party-applications"></a>applications 3ème partie
Ce groupe est un ensemble d’attributs utilisés comme les attributs minimes nécessaires pour une application ou une charge de travail générique. Il peut être utilisé pour une charge de travail ne figurant ne pas dans une autre section ou d’une application non Microsoft. Il est utilisé explicitement pour les éléments suivants :

- Yammer (un seul utilisateur est consommée)
- [Scénarios de collaboration hybride Business-to-Business (B2B) entre à l’organisation proposées par les ressources, telles que SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Ce groupe est un ensemble d’attributs qui peut être utilisé si le répertoire AD Azure n’est pas utilisé pour prendre en charge d’Office 365, Dynamics ou Intune. Il a un petit ensemble d’attributs de base.

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| NC| X|  | X|  |
| displayName| X| X| X|  |
| givenName| X| X|  |  |
| courrier| X|  | X|  |
| managedBy|  |  | X|  |
| mailNickName| X| X| X|  |
| membre|  |  | X|  |
| objectSID| X|  |  | propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand à invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et de fédération.|
| n° de série| X| X|  |  |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour conserver la relation entre ajoute et Azure AD.|
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion de l’utilisateur. La plupart du temps la valeur identique au [mail].|

## <a name="windows-10"></a>Windows 10
Un computer(device) à un domaine de Windows 10 synchronise certains attributs à Active Directory Azure. Pour plus d’informations sur les scénarios, voir [se connecter à un domaine périphériques AD Azure pour Windows 10 rencontre](active-directory-azureadjoin-devices-group-policy.md). Ces attributs sont toujours synchroniser et 10 de Windows n’apparaît pas comme une application, que vous pouvez désélectionner. Un ordinateur à un domaine de Windows 10 est identifié en ayant l’userCertificate attribut rempli.

| Nom de l’attribut| DISPOSITIF| Commentaire |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Valeur codée en dur pour les ordinateurs joints au domaine. |
| displayName | X| |
| MS-DS-CreatorSID | X| Également appelé registeredOwnerReference.|
| GUID d’objet | X| Également appelé ID de périphérique.|
| objectSID | X| Également appelé onPremisesSecurityIdentifier.|
| système d’exploitation | X| Également appelé deviceOSType.|
| operatingSystemVersion | X| Également appelé deviceOSVersion.|
| userCertificate | X| |

Ces attributs de **l’utilisateur** sont en plus des autres applications que vous avez sélectionné.  

| Nom de l’attribut| Utilisateur| Commentaire |
| --- | :-: | --- |
| domainFQDN| X| Également appelé Nom_Domaine_Dns. Par exemple, contoso.com.|
| domainNetBios| X| Également appelé Nom_netbios. Par exemple, CONTOSO.|

## <a name="exchange-hybrid-writeback"></a>Écriture différée de Exchange hybride
Ces attributs sont écrites depuis Active Directory Azure sur site Active Directory lorsque vous sélectionnez Activer **Exchange hybride**. En fonction de votre version de Exchange, moins d’attributs peuvent être synchronisés.

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObjectID| X|  |  | Dérivé de cloudAnchor dans Azure AD. Cet attribut est une nouveauté dans Exchange 2016.|
| msExchArchiveStatus| X|  |  | Archivage en ligne : Permet aux clients d’archiver les messages.|
| msExchBlockedSendersHash| X|  |  | Filtrage : Réécrit le filtrage sur les sites et les données des expéditeurs sûrs et bloqués en ligne à partir de clients.|
| msExchSafeRecipientsHash| X|  |  | Filtrage : Réécrit le filtrage sur les sites et les données des expéditeurs sûrs et bloqués en ligne à partir de clients.|
| msExchSafeSendersHash| X|  |  | Filtrage : Réécrit le filtrage sur les sites et les données des expéditeurs sûrs et bloqués en ligne à partir de clients.|
| msExchUCVoiceMailSettings| X|  |  | Activer la messagerie unifiée (MU) - messagerie vocale en ligne : utilisée par Microsoft Lync Server integration pour indiquer à Lync Server local que l’utilisateur dispose de la messagerie vocale dans les services en ligne.|
| msExchUserHoldPolicies| X|  |  | Gel : Active les services en nuage pour déterminer les utilisateurs qui se trouvent sous Litigation Hold.|
| proxyAddresses| X| X| X| Uniquement les x500 adresse à partir d’Exchange en ligne est insérée.|

## <a name="device-writeback"></a>Écriture différée de périphérique
Les objets du périphérique sont créés dans Active Directory. Ces objets peuvent être des périphériques liés à AD Azure ou à un domaine Windows 10.

| Nom de l’attribut| DISPOSITIF| Commentaire |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| displayName | X| |
| nom de domaine | X| |
| msDS-CloudAnchor | X| |
| msDS-DeviceID | X| |
| msDS-DeviceObjectVersion | X| |
| msDS-DeviceOSType | X| |
| msDS-DeviceOSVersion | X| |
| msDS-DevicePhysicalIDs | X| |
| msDS-KeyCredentialLink | X| Uniquement avec le schéma Active Directory de Windows Server 2016 |
| msDS-IsCompliant | X| |
| msDS-IsEnabled | X| |
| msDS-IsManaged | X| |
| msDS-RegisteredOwner | X| |


## <a name="notes"></a>Notes

- Lorsque vous utilisez un autre ID, attribut userPrincipalName local est synchronisé avec l’onPremisesUserPrincipalName d’attribut AD Azure. L’attribut autre ID, pour le courrier de l’exemple, est synchronisé avec l’attribut userPrincipalName AD Azure.
- Dans la liste ci-dessus, le type d’objet **utilisateur** s’applique également à de type objet **iNetOrgPerson**.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la configuration [d’Azure AD connexion de synchronisation](active-directory-aadconnectsync-whatis.md) .

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
