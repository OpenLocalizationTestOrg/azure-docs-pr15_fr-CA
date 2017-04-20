<properties
    pageTitle="Configurez l’enregistrement automatique des périphériques d’à un domaine Windows avec Active Directory de Azure | Microsoft Azure"
    description="Configurer vos périphériques Windows à un domaine pour enregistrer automatiquement et silencieusement avec Azure Active Directory."
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
    ms.date="10/24/2016"
    ms.author="markvi"/>



# <a name="set-up-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Définir l’enregistrement automatique de Windows à un domaine de périphériques avec Azure Active Directory

Pour utiliser [des périphériques l’accès conditionnel Azure Active Directory](active-directory-conditional-access.md), les ordinateurs à un domaine Windows doivent être enregistrés avec Azure Active Directory (AD Azure). Dans cet article, vous pouvez savoir ce que vous devez faire pour configurer l’enregistrement des périphériques à un domaine de Windows Azure publicité dans votre organisation.

À l’aide d’accès conditionnel dans Azure annonce vous offre ces avantages :

-   Expérience d’améliorée de session unique (SSO) dans les applications Azure AD via les comptes de travail ou de l’école
-   Entreprise d’itinérance des paramètres sur les périphériques
-   Accès au magasin de Windows pour les entreprises
-   Authentification renforcée et pratique à la connexion avec Windows Hello

> [AZURE.NOTE] La mise à jour de novembre de 10 Windows offre quelques-unes des expériences utilisateur améliorées dans AD Azure, mais la mise à jour de Windows 10 anniversaire prend totalement en charge des périphériques d’accès conditionnel. Pour plus d’informations sur l’accès conditionnel, consultez [accès conditionnel basé sur le périphérique de Azure Active Directory](active-directory-conditional-access.md). Pour plus d’informations sur les périphériques Windows 10 dans l’espace de travail et comment un utilisateur enregistre un périphérique Windows 10 avec AD Azure, consultez [Windows 10 pour l’entreprise : utiliser des périphériques pour les travaux](active-directory-azureadjoin-windows10-devices-overview.md).

Vous pouvez enregistrer des versions antérieures de Windows, y compris les versions :

-   8.1 de Windows
-   Windows 7

Si vous utilisez un ordinateur Windows Server sous la forme d’un ordinateur de bureau, vous pouvez enregistrer ces plates-formes :

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2


## <a name="prerequisites"></a>Conditions préalables

L’exigence principale pour l’inscription automatique à un domaine de périphériques à l’aide d’Active Directory Azure est une version à jour d’Azure Active Directory se connecter (Connect de publicité Azure).

En fonction de comment vous avez déployé Azure Connect d’Active Directory, et si vous avez utilisé une installation expresse ou personnalisée ou une mise à niveau sur place, les conditions préalables suivantes soient configurés automatiquement :

-   **Point de connexion de Service dans Active Directory sur site**. Pour la découverte des informations de locataire Azure AD par des ordinateurs qui s’inscrire de publicité Azure.

-   **Transformer des règles d’émission des Services de fédération active Directory (Active Directory Federation Services)**. Pour l’authentification de l’ordinateur lors de l’enregistrement (applicable aux configurations fédérées).

Si certains périphériques dans votre organisation ne sont pas Windows 10 à un domaine, assurez-vous que vous effectuez les tâches suivantes :

- Définir une stratégie dans Active Directory Azure afin que les utilisateurs peuvent enregistrer des périphériques
- Comme alternative à une authentification multifactorielle dans AD FS valide la valeur d’authentification Windows (intégrée)



## <a name="set-a-service-connection-point-for-discovery-of-the-azure-ad-tenant"></a>Définir un point de connexion de service pour la découverte du locataire Azure AD

Un objet point de connexion de service doit exister dans la configuration de partition d’appellation contexte de la forêt du domaine où les ordinateurs sont joints. Le point de connexion de service conserve des informations de découverte sur le locataire AD Azure dans laquelle inscrivent les ordinateurs. Dans une configuration à plusieurs forêt Active Directory, le point de connexion de service doit exister dans toutes les forêts qui ont les ordinateurs joints au domaine.

Définir le point de connexion de service à ces emplacements dans Active Directory :

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context]

> [AZURE.NOTE] Pour une forêt Active Directory nom de domaine *exemple.com*, le contexte de nommage de configuration est CN = Configuration, DC = exemple, DC = com.

Vous pouvez vérifier les valeurs de clients AD Azure découverte et objet en utilisant le script Windows PowerShell suivant. (Remplacez le contexte d’appellation de configuration dans l’exemple avec votre contexte de nommage de configuration.)

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

Le `$scp.Keywords` sortie affiche les informations de locataire AD Azure :

    azureADName:microsoft.com

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Si le point de connexion de service n’existe pas, créez-la en exécutant le script PowerShell suivant sur le serveur Azure Connect d’annonce :

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


> [AZURE.NOTE]Lorsque vous exécutez `$aadAdminCred = Get-Credential`, utilisez le format *user@example.com* pour le nom d’utilisateur dans la boîte de dialogue de **Get-Credential** .  
> Lorsque vous exécutez l’applet de commande Initialize-ADSyncDomainJoinedComputerSync, remplacez [*nom de compte de connecteur*] par le compte de domaine qui est utilisé dans le compte de connecteur Active Directory.  
> L’applet de commande utilise le module Active Directory PowerShell, qui s’appuie sur les Services Web de Active Directory dans un contrôleur de domaine. Services Web de Active Directory est prise en charge par les contrôleurs de domaine dans Windows Server 2008 R2 et versions ultérieures. Pour les contrôleurs de domaine dans Windows Server 2008 ou une version antérieure, utilisez l’API System.DirectoryServices via PowerShell pour créer le point de connexion de service et puis assigner les valeurs de mots clés.

## <a name="create-ad-fs-rules-for-instant-device-registration-in-federated-organizations"></a>Créer des règles de AD FS pour l’enregistrement de dispositifs instantanée dans les organisations fédérées

Dans un Azure fédéré configuration d’Active Directory, les périphériques s’appuient sur AD FS (ou sur le serveur de fédération local) pour s’authentifier sur Active Directory Azure. Puis, ils s’inscrivent sur le Service (Service d’enregistrement de dispositif de publicité Azure) pour Azure Active Directory périphérique d’enregistrement.

> [AZURE.NOTE] Dans une configuration non fédéré, hachages de mots de passe utilisateur sont synchronisés dans Azure AD, et les ordinateurs à un domaine Windows 10 et Windows serveur 2016 s’authentifier sur le Service d’enregistrement de dispositif de publicité Azure. Un utilisateur s’authentifie en utilisant les informations d’identification que l’utilisateur écrit dans leurs comptes d’ordinateur local, et qui est relayé vers Azure AD via Azure Connect d’Active Directory. Pour les non - Windows 10 et les ordinateurs Windows Server 2016 dans une configuration non fédéré, vous disposez d’options permettant de définir une autorité de certificat basé sur le périphérique de votre organisation. Pour plus d’informations, consultez la section de packages de télécharger Windows Installer pour les ordinateurs non - Windows 10 dans cet article.

Pour les ordinateurs Windows 10 et Windows serveur 2016, Azure AD Connect associe l’objet périphérique dans Azure AD avec l’objet compte d’ordinateur local. Les affirmations suivantes doivent exister lors de l’authentification pour le Service d’enregistrement de dispositif de publicité Azure terminer votre inscription et créer l’objet périphérique :

- http://schemas.Microsoft.com/ws/2012/01/AccountType contient la valeur de DJ, qui identifie l’authentificateur principal sous la forme d’un ordinateur à un domaine.
- http://schemas.Microsoft.com/Identity/Claims/onpremobjectguid contient la valeur de l’attribut **objectGUID** de compte d’ordinateur local.
- http://schemas.Microsoft.com/ws/2008/06/Identity/Claims/primarysid contient un identificateur de sécurité de l’ordinateur (SID), qui correspond à la valeur de l’attribut **objectSid** de compte d’ordinateur local.
- http://schemas.Microsoft.com/ws/2008/06/Identity/Claims/issuerid contient la valeur que AD Azure utilise pour approuver le jeton émis à partir d’AD FS ou depuis le local Service STS (Security Token). Ceci est important dans une configuration à plusieurs forêt Active Directory. Dans cette configuration, les ordinateurs peuvent être joints à une forêt différente de celle qui se connecte à Azure AD à l’AD FS ou de SharePoint Team Services sur site. Pour l’ADFS, utilisez la valeur http://< ; le*nom de domaine*>/adfs/services/approbation /, où <*nom de domaine*> est le nom de domaine validé dans Azure AD.

Pour créer manuellement des ces règles dans AD FS, utilisez le script PowerShell suivant dans une session qui est connectée à votre serveur. Remplacer la première ligne par le nom de domaine de votre organisation validé Azure AD.

> [AZURE.NOTE] Si vous n’utilisez pas AD FS pour votre serveur de fédération local, suivez les instructions de votre fournisseur pour créer les règles qui émettent de ces demandes.

    $validatedDomain = "example.com"      # Replace example.com with your organization's validated domain name in Azure AD

    $rule1 = '@RuleName = "Issue object GUID"

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain-joined computers"

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

> [AZURE.NOTE] Utiliser uniquement les trois premières règles si votre environnement local est une forêt unique. Si vos ordinateurs sont dans une autre forêt que celle de la synchronisation avec Active Directory Azure, ou si vous utilisez d’autres noms à celles de la configuration de la synchronisation, vous devez également inclure les règles restantes.

    $rule3 = '@RuleName = "Pass through primary SID"

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(claim = c2);'

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account"

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"])

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");'

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID"

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));'

    $rule6 = '@RuleName = "Update issuer for DJ computer auth"

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");'

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

> [AZURE.NOTE] Les ordinateurs à un domaine Windows 10 et Windows serveur 2016 authentifient à l’aide de IWA à un point de terminaison WS-Trust active qui est hébergé par AD FS. Assurez-vous que le point de terminaison est active. Si vous utilisez le Proxy d’Application Web, veillez à ce que ce point de terminaison est publiée via le serveur proxy. Le point de terminaison est adfs/services/approbation/13/windowstransport. Pour vérifier si elle est active, dans la console de gestion AD FS atteindre le **Service** > **points de terminaison**. Si vous n’utilisez pas AD FS pour votre serveur de fédération local, suivez les instructions de votre fournisseur pour vous assurer que le point de terminaison correspondant est actif.



## <a name="set-up-ad-fs-for-authentication-of-device-registration"></a>Configuration d’ADFS pour l’authentification de l’enregistrement du périphérique

Assurez-vous que l’option IWA est défini comme une alternative valide à plusieurs facteurs d’authentification pour l’inscription de périphérique dans AD FS. Pour ce faire, vous devez avoir une émission de transformer la règle qui passe par le biais de la méthode d’authentification.

1. Dans la console de gestion AD FS, accédez à **AD FS** > **Relations d’approbation** > **Approbations des parties de confiance**.

2. Cliquez sur l’objet d’approbation partie utilisatrice plate-forme d’identité Microsoft Office 365, puis sélectionnez **Modifier les règles de revendication**.

3.  Sous l’onglet **Règles de transformation d’émission** , sélectionnez **Ajouter une règle**.

4.  Dans la liste modèle de **règle** , sélectionnez **Envoyer des demandes à l’aide d’une règle personnalisée**.

5.  Cliquez sur **suivant**.

6.  Dans la zone **nom de règle de revendication** , entrez **Règle méthode Auth**.

7.  Dans la zone de la **règle** , entrez cette commande :

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
    => issue(claim = c);`.

8. Sur votre serveur de fédération, entrez cette commande PowerShell :

    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

<*RPObjectName*> est le nom de l’objet partie utilisatrice pour votre objet AD Azure approbation de tiers de confiance. Cet objet est généralement nommé de *Plate-forme d’identité Microsoft Office 365*.



## <a name="deployment-and-rollout"></a>Et le déploiement

Lorsque les ordinateurs joints au domaine les connaissances préalables, ils sont prêts à vous inscrire avec AD Azure.

Les ordinateurs à un domaine Windows 10 anniversaire Update et de Windows Server 2016 enregistrement automatiquement avec AD Azure lors du prochain redémarrage de l’appareil ou lorsqu’un utilisateur se connecte à Windows. Nouveaux ordinateurs qui sont joints au domaine enregistrement avec AD Azure lors du redémarrage de celui-ci après l’opération de jonction de domaine.

> [AZURE.NOTE] Les ordinateurs à un domaine Windows 10 s’enregistre automatiquement avec Azure annonce uniquement si la valeur de l’objet de stratégie de groupe de déploiement.

Vous pouvez utiliser un objet de stratégie de groupe pour contrôler la mise en œuvre de l’enregistrement automatique de Windows 10 et les ordinateurs à un domaine Windows Server 2016. Pour déployer l’enregistrement automatique des ordinateurs à un domaine de 10 non Windows, vous pouvez déployer un package Windows Installer sur les ordinateurs que vous sélectionnez.

> [AZURE.NOTE] La stratégie de groupe pour contrôle de déploiement déclenche également l’enregistrement des ordinateurs à un domaine de Windows 8.1. Vous pouvez utiliser la stratégie pour l’inscription d’ordinateurs à un domaine de Windows 8.1. Ou bien, si vous avez un mélange de versions de Windows, y compris les versions de Windows 7 ou de Windows Server, vous pouvez enregistrer tous vos 10 non - Windows et les ordinateurs Windows Server 2016 à l’aide d’un package Windows Installer.

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Créer un objet de stratégie de groupe pour contrôler la mise en œuvre de l’enregistrement automatique

Pour contrôler la mise en œuvre l’enregistrement automatique des ordinateurs joints au domaine avec AD Azure, vous pouvez déployer la stratégie de groupe **inscrire les ordinateurs joints au domaine en tant que périphériques** sur les ordinateurs que vous souhaitez enregistrer. Par exemple, vous pouvez déployer la stratégie à une unité d’organisation ou à un groupe de sécurité.

Pour définir la stratégie, procédez comme suit :

1. Ouvrez le Gestionnaire de serveur et puis cliquez sur **Outils** > **Gestion des stratégies de groupe**.

2. Atteindre le nœud du domaine qui correspond au domaine dans lequel vous voulez activer l’inscription automatique d’ordinateurs Windows 10 ou Windows serveur 2016.

3. Cliquez sur **Objets de stratégie de groupe**et sélectionnez **Nouveau**.

4. Entrez un nom pour votre objet de stratégie de groupe. Par exemple, *L’enregistrement automatique dans AD Azure*. Sélectionnez **OK**.

4. Avec le bouton droit de votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.

5. Accédez à **Configuration de l’ordinateur** > **stratégies** > **les modèles d’administration** > **composants Windows** > **l’enregistrement du périphérique**. Cliquez droit sur le **domaine de Registre des ordinateurs en tant que périphériques**et sélectionnez **Modifier**.

    > [AZURE.NOTE] Ce modèle de stratégie de groupe a été renommé à partir de versions antérieures de la console Gestion de stratégie de groupe. Si vous utilisez une version antérieure de la console, accédez à **Configuration de l’ordinateur** > **stratégies** > **Les modèles d’administration** > **Composants Windows** > **Join de l’espace de travail** > **automatiquement les ordinateurs de clients jointure poste de travail**.

6. Sélectionnez **activé**, puis sélectionnez **Appliquer**.

7. Sélectionnez **OK**.

8. Lier l’objet stratégie de groupe à un emplacement de votre choix. Par exemple, vous pouvez le lier à une unité d’organisation spécifique. Vous également pourriez lier à un groupe de sécurité spécifique d’ordinateurs qui inscrivent automatiquement avec AD Azure. Pour définir cette stratégie pour tous les ordinateurs joints au domaine Windows 10 et Windows serveur 2016 dans votre organisation, liez l’objet stratégie de groupe pour le domaine.

### <a name="download-windows-installer-packages-for-non-windows-10-computers"></a>Téléchargez les packages de Windows Installer pour les ordinateurs non - Windows 10  

Pour enregistrer des ordinateurs à un domaine Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 en cours d’exécution, vous pouvez télécharger et installer ces fichiers de package (.msi) Windows Installer :

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Déployer le package à l’aide d’un système de distribution de logiciels tels que System Center Configuration Manager. Le package prend en charge les options d’installation en mode silencieux standard avec le paramètre *silencieux* . System Center Configuration Manager 2016 offre des avantages supplémentaires à partir de versions antérieures, comme la possibilité de suivre les enregistrements terminés. Pour plus d’informations, reportez-vous à la section [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center).

Le programme d’installation crée une tâche planifiée sur le système qui s’exécute dans le contexte de l’utilisateur. La tâche est déclenchée lorsque l’utilisateur se connecte à Windows. La tâche enregistre silencieusement le périphérique avec AD Azure avec les informations d’identification de l’utilisateur après l’authentification par le biais de IWA. Pour voir la tâche planifiée, cliquez sur **Microsoft** > **Join de l’espace de travail**, puis revenez à la bibliothèque du Planificateur de tâches.



## <a name="next-steps"></a>Étapes suivantes

- [Azure accès conditionnel de Active Directory](active-directory-conditional-access.md)
