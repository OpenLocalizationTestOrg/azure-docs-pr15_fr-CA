<properties
    pageTitle="Mise en oeuvre de la synchronisation de mot de passe avec la synchronisation Azure Connect d’annonce | Microsoft Azure"
    description="Fournit des informations sur le fonctionne de la synchronisation de mot de passe et comment l’activer."
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
    ms.date="08/24/2016"
    ms.author="markusvi;andkjell"/>


# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Mise en oeuvre de la synchronisation de mot de passe avec la synchronisation Azure Connect de publicité
Cette rubrique vous fournit les informations que vous devez synchroniser vos mots de passe utilisateur sur site Active Directory (AD) à un nuage Azure Active Directory (AD Azure).

## <a name="what-is-password-synchronization"></a>Quelle est la synchronisation de mot de passe
La probabilité que vous êtes bloqué à partir de la réalisation de votre travail en raison d’un mot de passe oublié est lié au nombre de mots de passe différents, vous devez garder à l’esprit. Les mots de passe plus que vous devez garder à l’esprit, plus la probabilité d’oublier un. Questions et appels sur les réinitialisations de mot de passe et d’autres problèmes de mot de passe demandent le plus de ressources du support technique.

Synchronisation de mot de passe est une fonctionnalité de synchronisation des mots de passe utilisateur dans Active Directory sur site un nuage Azure Active Directory (AD Azure).
Cette fonctionnalité vous permet de vous connecter à des services Azure Active Directory (par exemple, Office 365, Microsoft Intune, CRM en ligne et les Services de domaine Active Directory de Azure) en utilisant le même mot de passe que vous utilisez pour vous connecter à votre site sur Active Directory.

![Nouveautés d’Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

En réduisant le nombre de mots de passe de que vos utilisateurs ont besoin pour mettre à jour la seule, synchronisation de mot de passe vous aide à :

- Améliorer la productivité de vos utilisateurs
- Réduisez vos coûts de support technique  

En outre, si vous choisissez d’utiliser la [**fédération avec AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), vous pouvez activer synchronisation de mot de passe de sauvegarde en cas de défaillance de votre infrastructure ADFS.

Synchronisation de mot de passe est une extension de la fonctionnalité de synchronisation de répertoire mis en oeuvre par synchronisation d’Azure Connect d’AD. Pour utiliser la synchronisation de mot de passe dans votre environnement, vous devez :

- Installation AD Azure se connecter  
- Configurer la synchronisation d’annuaire entre vos locaux AD et Azure Active Directory
- Activer la synchronisation de mot de passe

Pour plus d’informations, consultez [intégration de vos identités sur site avec Azure Active Directory](active-directory-aadconnect.md)

> [AZURE.NOTE] Pour plus d’informations sur les Services de domaine Active Directory qui sont configurés pour la synchronisation de mot de passe et FIPS, reportez-vous à la section [synchronisation de mot de passe et FIPS](#password-synchronization-and-fips).

## <a name="how-password-synchronization-works"></a>Comment fonctionne la synchronisation de mot de passe
Le service de domaine Active Directory stocke les mots de passe sous forme d’une représentation sous forme de valeur de hachage du mot de passe réel de l’utilisateur. Une valeur de hachage est un résultat d’une fonction mathématique unidirectionnelle (le «*algorithme de hachage*»). Il n’y a aucune méthode pour rétablir le résultat d’une fonction unidirectionnelle vers la version en texte brut du mot de passe. Vous ne pouvez pas utiliser un hachage du mot de passe pour vous connecter à votre réseau local.

Pour synchroniser votre mot de passe, Azure AD connexion synchronisation extrait le hachage de mot de passe à partir d’Active Directory sur site. Traitement de sécurité supplémentaire est appliquée au hachage du mot de passe avant qu’il est synchronisé avec le service d’authentification Azure Active Directory. Mots de passe sont synchronisés sur une base par utilisateur et dans l’ordre chronologique.

Le flux réel des données du processus de synchronisation de mot de passe est similaire à la synchronisation de données utilisateur telles que le nom d’affichage ou les adresses E-mail. Toutefois, les mots de passe sont synchronisés plus souvent que la fenêtre de synchronisation d’annuaire standard pour d’autres attributs. Le processus de synchronisation de mot de passe s’exécute toutes les 2 minutes. Vous ne pouvez pas modifier la fréquence de ce processus. Lorsque vous synchronisez un mot de passe, il remplace le mot de passe existant de nuage.

La première fois, vous activez la fonctionnalité de synchronisation de mot de passe, il effectue une synchronisation initiale des mots de passe de tous les utilisateurs dans la portée. Vous ne peut pas définir explicitement un sous-ensemble de mots de passe utilisateur que vous souhaitez synchroniser.

Lorsque vous modifiez un mot de passe local, le mot de passe mis à jour est synchronisée, le plus souvent en quelques minutes.
La fonctionnalité de synchronisation de mot de passe tente automatiquement de renvoyer les tentatives de synchronisation qui a échoué. Si une erreur se produit lors d’une tentative de synchronisation de mot de passe, une erreur est enregistrée dans votre Observateur d’événements.

La synchronisation de mot de passe n’a aucun impact sur l’utilisateur actuellement connecté.
Votre session de service cloud en cours n’est pas immédiatement affectée par une modification de mot de passe synchronisés qui se produit lorsque vous êtes connecté à un service en nuage. Toutefois, lorsque le service en nuage requiert de s’authentifier à nouveau, vous devez fournir votre mot de passe.

> [AZURE.NOTE] Synchronisation de mot de passe est uniquement pris en charge pour l’utilisateur de type d’objet dans Active Directory. Il n’est pas pris en charge pour le type d’objet iNetOrgPerson.

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Comment fonctionne la synchronisation de mot de passe avec les Services de domaine Active Directory de Azure
Vous pouvez également utiliser la fonctionnalité de synchronisation de mot de passe pour synchroniser vos mots de passe locaux pour les [Services de domaine Active Directory de Azure](../active-directory-domain-services/active-directory-ds-overview.md). Ce scénario permet les Services de domaine Active Directory Azure authentifier les utilisateurs dans le nuage avec toutes les méthodes disponibles dans vos locaux sur Active Directory. L’expérience de ce scénario est semblable à l’utilisation de l’outil de Migration Active Directory (ADMT) dans un environnement sur site.

### <a name="security-considerations"></a>Considérations sur la sécurité
Lors de la synchronisation des mots de passe, la version en texte brut du mot de passe n’est pas exposée à la fonctionnalité de synchronisation de mot de passe, à AD Azure, ou à certains des services associés.

Aussi, il n’y a aucune exigence sur site Active Directory pour stocker le mot de passe dans un format chiffré réversiblement. Un résumé de la valeur de hachage du mot de passe Active Directory est utilisé pour la transmission entre les locaux AD et Azure Active Directory. Le digest du hachage de mot de passe ne peut pas être utilisé pour accéder aux ressources dans votre environnement local.

### <a name="password-policy-considerations"></a>Remarques sur les stratégies de mot de passe
Il existe deux types de stratégies de mot de passe qui sont affectés par l’activation de la synchronisation de mot de passe :

1. Stratégie de complexité de mot de passe
2. Stratégie d’Expiration de mot de passe

**Stratégie de complexité de mot de passe**  
Lorsque vous activez la synchronisation de mot de passe, les stratégies de complexité de mot de passe dans Active Directory sur site remplacent les stratégies de complexité dans le cloud pour les utilisateurs synchronisés. Vous pouvez utiliser tous les mots de passe valides de votre sur site Active Directory pour accéder aux services de publicité Azure.

> [AZURE.NOTE] Mots de passe des utilisateurs créés directement dans le nuage sont toujours soumis à des stratégies de mot de passe tel que défini dans le nuage.

**Stratégie d’expiration de mot de passe**  
Si un utilisateur est dans la portée de la synchronisation de mot de passe, le mot de passe de compte de nuage a la valeur «*N’Expire jamais*».
Vous pouvez continuer à vous connecter à vos services de cloud à l’aide d’un mot de passe synchronisé a expiré dans votre environnement local. Votre mot de passe du nuage est mis à jour la prochaine fois que vous modifiez le mot de passe dans l’environnement local.

### <a name="overwriting-synchronized-passwords"></a>Remplacement de synchroniser les mots de passe
Un administrateur peut réinitialiser manuellement votre mot de passe à l’aide de Windows PowerShell.

Dans ce cas, le nouveau mot de passe remplace votre mot de passe synchronisé et toutes les stratégies de mot de passe définis dans le nuage sont appliquées au nouveau mot de passe.

Si vous modifiez votre mot de passe local une fois encore, le nouveau mot de passe est synchronisé avec le nuage et remplace le mot de passe manuellement mis à jour.

## <a name="enabling-password-synchronization"></a>Activation de la synchronisation de mot de passe
Synchronisation de mot de passe est automatiquement activée lorsque vous installez Azure AD se connecter en utilisant les **Paramètres Express**. Pour plus d’informations, consultez [mise en route avec Azure AD se connecter en utilisant les paramètres express](./connect/active-directory-aadconnect-get-started-express.md).

Si vous utilisez des paramètres personnalisés lors de l’installation Azure Connect de publicité, vous activez la synchronisation de mot de passe sur la page de connexion utilisateur. Pour plus d’informations, consultez [installation personnalisée de Azure Connect d’Active Directory](./connect/active-directory-aadconnect-get-started-custom.md).

![Activation de la synchronisation de mot de passe](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Synchronisation de mot de passe et FIPS
Si votre serveur a été verrouillé en fonction de traitement Standard FIPS (Federal Information), MD5 a été désactivé.

**Pour activer le MD5 pour la synchronisation de mot de passe, effectuez les opérations suivantes :**

1. Accédez à **%programfiles%\Azure AD Sync\Bin**.
2. Ouvrez **miiserver.exe.config**.
3. Accédez au nœud **configuration/runtime** (à la fin du fichier).
4. Ajoutez le nœud suivant :`<enforceFIPSPolicy enabled="false"/>`
5. Enregistrez vos modifications.

Pour référence, cette capture est comment il doit ressembler à :

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Pour plus d’informations sur la sécurité et FIPS voir [conformité FIPS, le cryptage et la synchronisation de mot de passe DAS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshooting-password-synchronization"></a>Résolution des problèmes de synchronisation de mot de passe
Si les mots de passe ne se synchronisent pas comme prévu, il peut s’agir d’un sous-ensemble d’utilisateurs ou pour tous les utilisateurs.

- Si vous avez un problème avec les objets individuels, puis reportez-vous à la section [dépannage un objet qui ne se synchronise pas les mots de passe](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
- Si vous avez un problème dans lequel aucun mot de passe n’est synchronisés, consultez [résoudre les problèmes où aucun mot de passe n’est synchronisés](#troubleshoot-issues-where-no-passwords-are-synchronized).

### <a name="troubleshoot-one-object-that-is-not-synchronizing-passwords"></a>Résoudre les problèmes liés à un objet qui ne se synchronise pas les mots de passe
Vous pouvez facilement résoudre les problèmes de synchronisation de mot de passe en consultant l’état d’un objet.

Démarrer dans **utilisateurs et ordinateurs Active Directory**. Recherchez l’utilisateur et vérifiez que le **Qu'utilisateur doit changer de mot de passe à la prochaine ouverture de session** est désactivée.
![Les mots de passe productifs actives Directory](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png)  
Si elle est activée, puis demandez à l’utilisateur de se connecter et modifier le mot de passe. Mots de passe temporaires ne sont pas synchronisées dans AD Azure.

Si tout semble correct dans Active Directory, l’étape suivante consiste à suivre l’utilisateur dans le moteur de synchronisation. En suivant l’utilisateur sur site Active Directory vers Active Directory Azure, vous pouvez voir si une erreur s’est descriptive sur l’objet.

1. Démarrez le **[Gestionnaire de Service de synchronisation](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Cliquez sur **les connecteurs**.
3. Sélectionnez le **Connecteur Active Directory** , l’utilisateur se trouve dans.
4. Sélectionnez **espace de connecteur de recherche**.
5. Recherchez l’utilisateur que vous recherchez.
6. Sélectionnez l’onglet **enregistrement en ligne** et assurez-vous qu’au moins une règle de synchronisation indique la **Synchronisation de mot de passe** **a la valeur True**. Dans la configuration par défaut, le nom de la règle de synchronisation est **dans à partir d’AD - AccountEnabled de l’utilisateur**.  
    ![Informations de lignage sur un utilisateur](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)  
7. Puis [suivre l’utilisateur](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) par le biais du métaverse à l’espace d’Azure le connecteur Active Directory. L’objet espace de connecteur doit avoir une règle sortante avec la **Synchronisation de mot de passe** a la valeur **True**. Dans la configuration par défaut, le nom de la règle de synchronisation est **sorti DAS - utilisateur joindre**.  
    ![Propriétés de l’espace connecteur d’un utilisateur](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)  
8. Pour visualiser les détails de la synchronisation de mot de passe de l’objet de la semaine, cliquez sur **journal...**.  
    ![Détails du journal objet](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)  

La colonne statut peut avoir les valeurs suivantes :

État | Description
---- | -----
Opération réussie | Mot de passe a été correctement synchronisée.
FilteredByTarget | Mot de passe est défini pour **l’utilisateur doit changer de mot de passe à la prochaine ouverture de session**. Mot de passe n’a pas été synchronisé.
NoTargetConnection | Pas d’objet dans le méta-verse, ou dans l’espace de connecteur Active Directory Azure.
SourceConnectorNotPresent | Aucun objet trouvé dans l’espace du connecteur Active Directory sur site.
TargetNotExportedToDirectory | L’objet dans l’espace du connecteur Active Directory Azure n’a pas encore été exporté.
MigratedCheckDetailsForMoreInfo | Entrée de journal a été créée avant la version 1.0.9125.0 et est affichée dans son état hérité.

### <a name="troubleshoot-issues-where-no-passwords-are-synchronized"></a>Résoudre les problèmes où aucun mot de passe n’est synchronisés
Commencez par exécuter le script dans la section [obtenir l’état de la synchronisation de mot de passe](#get-the-status-of-password-sync-settings). Il vous donne une vue d’ensemble de la configuration de la synchronisation de mot de passe.  
![Sortie de script PowerShell à partir des paramètres de synchronisation de mot de passe](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png)  
Si la fonction n’est pas activée dans Azure AD ou si l’état de canal de synchronisation n’est pas activé, puis exécutez l’Assistant d’installation de se connecter. Sélectionnez **Personnaliser les options de synchronisation** , puis désactiver la synchronisation de mot de passe. Cette modification désactive temporairement la fonction. Ensuite, réexécutez l’Assistant et ré-activer la synchronisation de mot de passe. Exécutez le script à nouveau pour vérifier que la configuration est correcte.

Si le script indique qu’il n’y a aucune pulsation, puis exécutez-le le script de [déclencheur, une synchronisation complète de tous les mots de passe](#trigger-a-full-sync-of-all-passwords). Ce script peut également être utilisé pour d’autres scénarios où la configuration est correcte, mais les mots de passe ne sont pas synchronisées.

#### <a name="get-the-status-of-password-sync-settings"></a>Obtenir l’état de la synchronisation de mot de passe

```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Déclencher une synchronisation complète de tous les mots de passe
Vous pouvez déclencher une synchronisation complète de tous les mots de passe en utilisant le script suivant :

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Étapes suivantes

* [Azure Sync de connexion Active Directory : Options de personnalisation de la synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
