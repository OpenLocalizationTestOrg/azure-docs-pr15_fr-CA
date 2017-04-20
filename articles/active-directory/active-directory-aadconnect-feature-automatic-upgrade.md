<properties
   pageTitle="Connexion de publicité Azure : Mise à niveau automatique | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité de mise à niveau automatique intégrée dans Azure Connect de AD sync."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/24/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-automatic-upgrade"></a>Connexion de publicité Azure : Mise à niveau automatique
Cette fonctionnalité a été introduite avec la version 1.1.105.0 (publiée février 2016).

## <a name="overview"></a>Vue d’ensemble
S’assurer que votre installation Azure Connect d’annonce est toujours à jour n’a jamais été plus facile grâce à la fonctionnalité de **mise à niveau automatique** . Cette fonctionnalité est activée par défaut pour les installations express et mises à niveau de la synchronisation d’annuaire. Lorsqu’une nouvelle version est publiée, l’installation est automatiquement mis à niveau.

Mise à jour automatique est activée par défaut pour les éléments suivants :

- Express installation des paramètres et des mises à niveau de la synchronisation d’annuaire.
- À l’aide de SQL Express LocalDB, qui est ce qui utilisent toujours les paramètres Express. Synchronisation d’annuaire avec SQL Express utilisent également LocalDB.
- Le compte Active Directory est le compte MSOL_ par défaut créé par les paramètres Express et la synchronisation d’annuaire.
- Avoir moins de 100 000 objets dans le métaverse.

L’état actuel de mise à niveau automatique peut être affiché avec l’applet de commande PowerShell `Get-ADSyncAutoUpgrade`. Il comporte les états suivants :

État | Commentaire
---- | ----
Activé | Mise à jour automatique est activée.
Suspendu | Défini par le système uniquement. Le système n’est plus autorisé à recevoir des mises à niveau automatiques.
Désactivé | Mise à jour automatique est désactivée.

Vous pouvez modifier entre **activé** et **désactivé** à `Set-ADSyncAutoUpgrade`. Que le système doit définir l’état **suspendu**.

Mise à niveau automatique utilise AD Azure se connecter la santé de l’infrastructure de mise à niveau. Pour la mise à niveau automatique fonctionne, assurez-vous que vous avez ouvert les URL de votre serveur proxy pour **La santé se connecter Azure AD** comme indiqué dans [les URL d’Office 365 et de plages d’adresses IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Si le **Gestionnaire de Service de synchronisation** de l’interface utilisateur est en cours d’exécution sur le serveur, la mise à niveau est suspendue jusqu'à la fermeture de l’interface utilisateur.

## <a name="troubleshooting"></a>Résolution des problèmes
Si votre installation de se connecter ne met pas niveau lui-même comme prévu, puis procédez comme suit pour savoir ce que peut être incorrect.

Tout d’abord, vous ne devriez pas être tentée le premier jour qu'une nouvelle version est publiée la mise à niveau automatique. Il existe un caractère aléatoire intentionnelle avant la tentative de mise à niveau afin de ne vous inquiétez pas si votre installation n’est pas mis à niveau immédiatement.

Si vous pensez que quelque chose ne convient pas, puis d’abord exécuter `Get-ADSyncAutoUpgrade` afin de garantir la mise à niveau automatique est activée.

Vérifiez que vous avez ouvert les URL requises dans votre proxy ou votre pare-feu. Mise à jour automatique utilise AD Azure se connecter la santé comme décrit dans la [vue d’ensemble](#overview). Si vous utilisez un serveur proxy, assurez-vous que la santé a été configurée pour utiliser un [serveur proxy](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Également tester la [connectivité de santé](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) à AD Azure.

Avec la connectivité à AD Azure vérifié, il est temps de rechercher dans les journaux d’événements. Démarrer l’Observateur d’événements et recherchez dans le journal des événements **Application** . Ajouter un filtre de journal des événements de la source **Azure AD connecter une mise à niveau** et l’id d’événement plage **300-399**.  
![Filtre de journal d’événements de mise à niveau automatique](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Vous pouvez maintenant voir les journaux d’événements associés à l’état de mise à niveau automatique.  
![Filtre de journal d’événements de mise à niveau automatique](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

Le code de résultat a un préfixe, une vue d’ensemble de l’état.

Préfixe du code de résultat | Description
--- | ---
Opération réussie | L’installation a été correctement mis à niveau.
UpgradeAborted | Un problème temporaire arrêté la mise à niveau. Il sera tentée à nouveau et la s’attend à ce qu’il réussit ultérieurement.
UpgradeNotSupported | Le système a une configuration qui bloque le système à partir de la mise à niveau automatiquement. Il sera tentée à nouveau pour voir si la modification de l’état, mais le but est que le système doit être mis à niveau manuellement.

Voici une liste de messages les plus courants que vous recherchez. Il ne répertorie pas tous, mais le message de résultat doit être claire à ce que le problème est.

Message de résultat | Description
--- | ---
**UpgradeAborted** |
UpgradeAbortedCouldNotSetUpgradeMarker | N’a pas pu écrire dans le Registre.
UpgradeAbortedInsufficientDatabasePermissions | Le groupe Administrateurs intégrés n’a pas les autorisations pour la base de données. Mettre à niveau manuellement vers la dernière version d’Azure Connect AD pour résoudre ce problème.
UpgradeAbortedInsufficientDiskSpace | Il n’est pas suffisamment d’espace disque pour prendre en charge d’une mise à niveau.
UpgradeAbortedSecurityGroupsNotPresent | Impossible de trouver et de résoudre tous les groupes de sécurité utilisés par le moteur de synchronisation.
UpgradeAbortedServiceCanNotBeStarted | Impossible de démarrer le Service NT **Microsoft Azure AD Sync** .
UpgradeAbortedServiceCanNotBeStopped | Impossible d’arrêter le Service NT **Microsoft Azure AD Sync** .
UpgradeAbortedServiceIsNotRunning | Le Service NT **Microsoft Azure AD Sync** ne fonctionne pas.
UpgradeAbortedSyncCycleDisabled | L’option SyncCycle dans le [Planificateur](active-directory-aadconnectsync-feature-scheduler.md) a été désactivée.
UpgradeAbortedSyncExeInUse | Le [Gestionnaire de service de synchronisation l’interface utilisateur](active-directory-aadconnectsync-service-manager-ui.md) est ouverte sur le serveur.
UpgradeAbortedSyncOrConfigurationInProgress | L’Assistant d’installation est en cours d’exécution ou une synchronisation a été planifiée en dehors du planificateur.
**UpgradeNotSupported** |
UpgradeNotSupportedCustomizedSyncRules | Vous avez ajouté vos propres règles personnalisées pour la configuration.
UpgradeNotSupportedDeviceWritebackEnabled | Vous avez activé la fonctionnalité [d’écriture différée du périphérique](active-directory-aadconnect-feature-device-writeback.md) .
UpgradeNotSupportedGroupWritebackEnabled | Vous avez activé la fonctionnalité [d’écriture différée du groupe](active-directory-aadconnect-feature-preview.md#group-writeback) .
UpgradeNotSupportedInvalidPersistedState | L’installation n’est pas des paramètres Express ou une mise à niveau de la synchronisation d’annuaire.
UpgradeNotSupportedMetaverseSizeExceeeded | Vous avez plus de 100 000 objets du métaverse.
UpgradeNotSupportedMultiForestSetup | Vous vous connectez à plus d’une forêt. Le programme d’installation Express se connecte uniquement à une seule forêt.
UpgradeNotSupportedNonLocalDbInstall | Vous n’utilisez pas une base de données SQL Server Express LocalDB.
UpgradeNotSupportedNonMsolAccount | Le [compte de connecteur Active Directory](active-directory-aadconnect-accounts-permissions.md#active-directory-account) n’est pas plus le compte MSOL_ par défaut.
UpgradeNotSupportedStagingModeEnabled | Le serveur est défini en [mode de mise en attente](active-directory-aadconnectsync-operations.md#staging-mode).
UpgradeNotSupportedUserWritebackEnabled | Vous avez activé la fonctionnalité [d’écriture en différé de l’utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback) .

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
