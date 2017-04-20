<properties
    pageTitle="Connecter des périphériques d’à un domaine à AD Azure pour des expériences Windows 10 | Microsoft Azure"
    description="Explique comment les administrateurs peuvent configurer la stratégie de groupe pour activer les périphériques être associés à un domaine pour le réseau d’entreprise."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Connecter des périphériques d’à un domaine à AD Azure pour des expériences Windows 10

Jonction de domaine est que les organisations de manière standard ont des équipements de travail au cours des 15 dernières années et bien plus encore. Il a activé les utilisateurs à se connecter à leurs périphériques à l’aide de leur travail Windows Server Active Directory (Active Directory) ou d’établissement des comptes et informatique entièrement gérer ces périphériques. Organisations s’appuient généralement sur les méthodes d’acquisition d’images à la disposition des périphériques pour les utilisateurs et généralement utilisent System Center Configuration Manager (SCCM) ou stratégie de groupe pour les gérer.

Jonction de domaine dans Windows 10 fournit les avantages suivants après avoir connecté les périphériques à Azure Active Directory (AD Azure) :

- Session unique (SSO) aux ressources AD Azure depuis n’importe où
- Accès à l’entreprise Windows Store à l’aide de comptes de travail ou de l’école (aucun compte Microsoft requis)
- Compatible entreprise itinérant des paramètres utilisateur sur les périphériques à l’aide de comptes de travail ou de l’école (aucun compte Microsoft requis)
- Authentification forte et pratique à la connexion pour les comptes de travail ou à l’école avec Microsoft Passport et Windows Hello
- Possibilité de limiter l’accès uniquement aux périphériques conformes aux paramètres de stratégie de groupe unité d’organisation

## <a name="prerequisites"></a>Conditions préalables

Jonction de domaine continue à être utile. Toutefois, pour obtenir les avantages Azure AD de l’authentification unique, itinérance de paramètres avec le travail ou les comptes de l’établissement et l’accès à Windows Store avec les comptes de travail ou à l’école, vous aurez besoin de ce qui suit :

- Abonnement de publicité Azure
- Azure Connect AD pour étendre le répertoire local pour Azure AD
- Stratégie qui a défini pour connecter à un domaine des périphériques pour Azure AD
- Génération de Windows 10 (version 10551 ou plus récente) pour les périphériques

Pour permettre à Microsoft Passport pour le travail et Windows Hello, vous devez également les éléments suivants :

- Infrastructure à clé publique (PKI) pour la délivrance de certificats d’utilisateur.
- Version 1509 pour la version bêta de System Center Configuration Manager. Pour plus d’informations, consultez [Microsoft système Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) et le [Blog de l’équipe System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Ceci est nécessaire pour déployer des certificats d’utilisateur en fonction des clés de Microsoft Passport.

Comme alternative à l’impératif de déploiement d’infrastructure à clé publique, vous pouvez effectuer les opérations suivantes :

- Disposer de plusieurs contrôleurs de domaine avec les Services de domaine Active Directory de Windows Server 2016.

Pour activer l’accès conditionnel, vous pouvez créer des paramètres de stratégie de groupe qui permettent d’accéder à des périphériques aucun déploiement supplémentaire à un domaine. Pour gérer le contrôle d’accès basé sur la conformité de l’équipement, vous devrez les éléments suivants :

- System Center Configuration Manager version 1509 Technical Preview pour les scénarios de Passport

## <a name="deployment-instructions"></a>Instructions de déploiement



### <a name="step-1-deploy-azure-active-directory-connect"></a>Étape 1 : Déployer Azure Active Directory se connecter

Azure Connect d’Active Directory vous permettra de provisionner des ordinateurs locaux en tant qu’objets de périphérique dans le nuage. Pour déployer Azure AD Connect, reportez-vous à « Installation AD Azure se connecter » dans l’article [d’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md#install-azure-ad-connect).

 - Si vous avez suivi une [installation personnalisée pour Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) (pas l’installation Express), puis suivez la procédure de **Création d’une connexion de service point dans sur site Active Directory**, plus loin dans cette étape.
 - Si vous avez une configuration fédérée avec AD Azure avant d’installer Azure AD se connecter (par exemple, si vous avez déployé Active Directory Federation Services (ADFS) avant), puis suivez la procédure de **configuration AD FS des règles de revendication** , plus loin dans cette étape.

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>Créer un point de connexion de service dans les locaux de Active Directory

Les périphériques associés à un domaine utilise le point de connexion de service pour découvrir des informations de locataire Azure AD au moment de l’inscription automatique avec le service d’enregistrement de dispositif Azure.

Sur le serveur Azure Connect d’Active Directory, exécutez les commandes PowerShell suivantes :

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Lors de l’exécution de l’applet de commande $aadAdminCred = Get-Credential, utilisez le format *user@example.com* pour le nom d’utilisateur de l’information d’identification qui est entrée lorsque la fenêtre contextuelle de Get-Credential s’affiche.

Lorsque vous exécutez la cmdlet Initialize-ADSyncDomainJoinedComputerSync, remplacez [*nom de compte de connecteur*] par le compte de domaine qui est utilisé comme compte de connecteur Active Directory.

#### <a name="configure-ad-fs-claim-rules"></a>Configurer les règles de revendications AD FS
Configurer les règles de revendications AD FS permet d’inscription instantanée d’un ordinateur avec le service d’enregistrement de dispositif Azure en permettant aux ordinateurs d’authentifier à l’aide de Kerberos/NTLM via AD FS. Sans cette étape, ordinateurs obtiendra pour Azure AD d’une manière différée (soumis à la fois de synchronisation Azure Connect de publicité).

>[AZURE.NOTE]
Si vous n’avez pas AD FS en tant que serveur de fédération sur les sites, suivez les instructions de votre fournisseur pour créer les règles de la demande.

Sur le serveur AD FS (ou sur une session connectée au serveur ADFS), exécutez les commandes PowerShell suivantes :

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Windows 10 ordinateurs seront authentifient à l’aide de l’authentification intégrée de Windows pour un point de terminaison WS-Trust actif hébergé par AD FS. Vérifiez que ce point de terminaison est activée. Si vous utilisez le serveur de Proxy Web d’authentification, aussi vous assurer que ce point de terminaison est publiée via le serveur proxy. Pour cela, en vérifiant l’adfs/services/approbation/13/windowstransport. Il doit indiquer que l’activé dans la console de gestion AD FS sous **Service** > **points de terminaison**.


### <a name="step-2-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>Étape 2 : Configurer l’inscription automatique de périphérique via la stratégie de groupe dans Active Directory

Vous pouvez utiliser la stratégie de groupe dans Active Directory pour configurer vos périphériques à un domaine de Windows 10 pour enregistrer automatiquement avec Azure AD.

> [AZURE.NOTE]
> Pour des instructions plus récentes sur la configuration d’enregistrement de dispositif automatique, consultez [comment configurer l’enregistrement automatique du domaine Windows joint périphériques avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
>
> Ce modèle de stratégie de groupe a été renommé dans Windows 10. Si vous exécutez l’outil de stratégie de groupe à partir d’un ordinateur Windows 10, la stratégie s’affiche sous la forme : <br>
> **Enregistrer des ordinateurs rattachés au domaine en tant que périphériques**<br>
> La stratégie est à l’emplacement suivant :<br>
> ***Inscription de composants/périphériques ordinateur modèles de Configuration/stratégies/administration/Windows***


## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour l’entreprise : méthodes d’utilisation de périphériques pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du nuage pour périphériques Windows 10 par le biais de joindre de Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour joindre des annonces d’Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des périphériques d’à un domaine à AD Azure pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Paramétrage de joindre des annonces d’Azure](active-directory-azureadjoin-setup.md)
