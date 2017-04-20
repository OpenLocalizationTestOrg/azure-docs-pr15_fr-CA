<properties
   pageTitle="Azure Connect de AD sync : Planificateur | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité Planificateur intégré dans Azure Connect de AD sync."
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
   ms.date="08/04/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-scheduler"></a>Azure Connect de AD sync : Planificateur
Cette rubrique décrit le Planificateur intégré dans Azure Connect de AD sync (également appelé moteur de synchronisation).

Cette fonctionnalité a été introduite avec la version 1.1.105.0 (publiée février 2016).

## <a name="overview"></a>Vue d’ensemble
Azure Connect de AD sync synchronisera les changements qui surviennent dans votre répertoire local à l’aide d’un planificateur. Il existe deux processus planificateur, une pour la synchronisation de mot de passe et l’autre pour la synchronisation de l’attribut d’objet et des tâches de maintenance. Cette rubrique couvre ce dernier.

Dans les versions antérieures, le planificateur des objets et attributs était externe au moteur de synchronisation, et le Planificateur de tâches de Windows ou un service distinct de Windows a été utilisé pour déclencher le processus de synchronisation. Le planificateur est avec les 1.1 versions intégrés à la synchronisation du moteur et n’autorisent pas les personnaliser. La nouvelle fréquence de synchronisation par défaut est 30 minutes.

Le planificateur est chargé pour exécuter deux tâches :

- Le **cycle de synchronisation**. Le processus d’importation, de synchroniser et d’exporter les modifications apportées.
- **Tâches de maintenance**. Renouveler les clés et les certificats de réinitialisation de mot de passe et dispositif d’enregistrement Service (DRS). Purger les anciennes entrées dans le journal des opérations.

Le planificateur lui-même est toujours en cours d’exécution, mais il peut être configuré pour exécuter uniquement un ou aucun de ces tâches. Par exemple si vous avez besoin d’avoir votre propre processus de cycle de synchronisation, vous pouvez désactiver cette tâche dans le planificateur, mais toujours exécuter la tâche de maintenance.

## <a name="scheduler-configuration"></a>Configuration du planificateur
Pour afficher vos paramètres de configuration en cours, accédez à PowerShell et exécuter `Get-ADSyncScheduler`. Elle indique quelque chose comme ceci :

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

Si vous voyez **la commande sync ou l’applet de commande n’est pas disponible** lors de l’exécution de cette applet de commande, le module PowerShell n’est pas chargé. Cela peut se produire si vous exécutez Azure Connect d’Active Directory sur un contrôleur de domaine ou sur un serveur avec des niveaux de restriction de PowerShell plus élevés que les paramètres par défaut. Si vous rencontrez cette erreur, puis exécutez `Import-Module ADSync` à disposition de l’applet de commande.

- **AllowedSyncCycleInterval**. Le plus souvent AD Azure permettra de synchronisations. Vous ne pouvez pas synchroniser plus fréquemment et toujours être pris en charge.
- **CurrentlyEffectiveSyncCycleInterval**. La planification actuellement en vigueur. Il aura la même valeur que CustomizedSyncInterval (si défini) si elle n’est pas plus fréquente que AllowedSyncInterval. Si vous modifiez CustomizedSyncCycleInterval, cela prendra effet après le prochain cycle de synchronisation.
- **CustomizedSyncCycleInterval**. Si vous souhaitez que le planificateur doit s’exécuter à une fréquence autre que celle par défaut de 30 minutes, vous allez configurer ce paramètre. Dans l’image ci-dessus le planificateur a été défini pour s’exécuter à la place de toutes les heures. Si vous choisissez une valeur inférieure à AllowedSyncInterval, ce dernier sera utilisé.
- **NextSyncCyclePolicyType**. Delta ou initiale. Définit si la prochaine exécution doit uniquement les modifications de delta de processus, ou si la prochaine exécution doit faire un intégral à importer et synchroniser, qui serait également traiter à nouveau les règles nouvelles ou modifiées.
- **NextSyncCycleStartTimeInUTC**. Le planificateur commence lors de votre prochaine du prochain cycle de synchronisation.
- **PurgeRunHistoryInterval**. L’heure de journaux doivent être conservés. Elles peuvent être consultées dans le Gestionnaire de service de synchronisation. La valeur par défaut est de conserver ces pendant 7 jours.
- **SyncCycleEnabled**. Indique si le planificateur est en cours d’exécution l’importation, la synchronisation et les processus d’exportation dans le cadre de son fonctionnement.
- **MaintenanceEnabled**. Indique si le processus de maintenance est activé. Il met à jour les certificats/clés et purger le journal des opérations.
- **IsStagingModeEnabled**. Indique si le [mode de mise en attente](active-directory-aadconnectsync-operations.md#staging-mode) est activée.

Vous pouvez modifier certains de ces paramètres avec `Set-ADSyncScheduler`. Vous pouvez modifier les paramètres suivants :

- CustomizedSyncCycleInterval
- NextSyncCyclePolicyType
- PurgeRunHistoryInterval
- SyncCycleEnabled
- MaintenanceEnabled

La configuration du planificateur est stockée dans Active Directory Azure. Si vous avez un serveur intermédiaire, toute modification sur le serveur principal affecteront également le serveur intermédiaire (à l’exception de IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntaxe :`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - jours, HH - heures, mm - minutes, ss - secondes

Exemple :`Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Change au planificateur d’exécuter toutes les 3 heures.

Exemple :`Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Change au planificateur d’exécuter quotidiennement.

## <a name="start-the-scheduler"></a>Démarrer le planificateur
Le planificateur exécutera par défaut toutes les 30 minutes. Dans certains cas, vous souhaiterez exécuter un cycle de synchronisation entre les cycles planifiées ou vous avez besoin exécuter un type différent.

**Cycle de synchronisation delta**  
Un cycle de synchronisation delta comprend les étapes suivantes :

- Importation de delta sur tous les connecteurs
- Synchronisation delta sur tous les connecteurs
- Exportation de tous les connecteurs

Il est possible d’avoir une urgence modifier qui doivent être synchronisées immédiatement c’est pourquoi vous devez exécuter manuellement un cycle. Si vous devez exécuter manuellement un cycle, puis à partir de PowerShell exécuter `Start-ADSyncSyncCycle -PolicyType Delta`.

**Cycle de synchronisation complète**  
Si vous avez effectué une des modifications de configuration suivantes, vous devez exécuter un cycle de synchronisation complet (également appelé Initial) :

- Ajouter des objets ou des attributs à importer à partir d’un répertoire source plus
- Apporté des modifications aux règles de synchronisation
- Modifié le [filtrage](active-directory-aadconnectsync-configure-filtering.md) pour un nombre différent d’objets doit être inclus

Si vous avez effectué une de ces modifications, vous devez ensuite exécuter un cycle de synchronisation complet afin que le moteur de synchronisation a la possibilité de reconsolider les espaces du connecteur. Un cycle de synchronisation complète comprend les étapes suivantes :

- Importation complète de tous les connecteurs
- Synchronisation complète de tous les connecteurs
- Exportation de tous les connecteurs

Pour initier un cycle de synchronisation complète, exécutez `Start-ADSyncSyncCycle -PolicyType Initial` à partir d’une invite de commandes PowerShell. Ceci démarrera un cycle de synchronisation complète.

## <a name="stop-the-scheduler"></a>Arrêter le planificateur
Si le planificateur est en cours d’exécution un cycle de synchronisation, vous devrez peut-être pour l’arrêter. Par exemple, si vous démarrez l’Assistant installation et que vous obtenez cette erreur :

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Lorsqu’un cycle de synchronisation est en cours d’exécution, vous ne pouvez pas modifier la configuration. Vous pourriez attendre jusqu'à ce que le planificateur a terminé le processus, mais vous pouvez également l’arrêter pour vous permettre de vos modifications immédiatement. Arrêter le cycle en cours n’est pas dangereux et des modifications ne pas encore traitées seront traitées avec la prochaine exécution.

1. Commencez par indiquer le planificateur pour arrêter son cycle en cours avec l’applet de commande PowerShell `Stop-ADSyncSyncCycle`.
2. Arrêter le planificateur n’arrêtera pas le connecteur en cours à partir de sa tâche en cours. Pour forcer le connecteur à arrêter, effectuez les actions suivantes : ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Démarrez le **Service de synchronisation** dans le menu Démarrer. Accédez à **connecteurs**, le connecteur avec l’état **en cours d’exécution** de mettre en surbrillance et sélectionnez **Arrêter** dans les Actions.

Le planificateur est toujours actif et redémarre à la prochaine occasion.

## <a name="custom-scheduler"></a>Planificateur personnalisé
Les applets de commande décrites dans cette section sont uniquement disponibles dans la version [1.1.130.0](active-directory-aadconnect-version-history.md#111300) ou ultérieure.

Si le Planificateur intégré ne satisfait pas vos besoins, vous pouvez planifier les connecteurs à l’aide de PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Appel de code non-ADSyncRunProfile
Vous pouvez démarrer un profil pour un connecteur de cette façon :

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Vous trouverez les noms à utiliser pour les [noms de connecteur](active-directory-aadconnectsync-service-manager-ui-connectors.md) et [exécution](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) de profil dans l' [Interface utilisateur du Gestionnaire de Service de synchronisation](active-directory-aadconnectsync-service-manager-ui.md).

![Appeler le profil d’exécution](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

Le `Invoke-ADSyncRunProfile` applet de commande est synchrone, par exemple, elle ne renverra pas contrôle jusqu'à ce que le connecteur a terminé l’opération, avec succès ou avec une erreur.

Lorsque vous planifiez vos connecteurs, il est recommandé de les planifier dans l’ordre suivant :

1. (Complet/Delta) Importer à partir de répertoires locaux, tels que Active Directory
2. (Complet/Delta) Importation depuis Active Directory Azure
3. (Complet/Delta) Synchronisation de répertoires locaux, tels que Active Directory
4. (Complet/Delta) Synchronisation d’Active Directory Azure
5. Exporter vers Azure AD
6. Exporter vers les répertoires locaux, tels que Active Directory

Si vous examinez le Planificateur intégré, il s’agit de l’ordre que les connecteurs seront exécutera.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Vous pouvez également analyser le moteur de synchronisation pour voir si elle est occupée ou inactive. Cette applet de commande renvoie un résultat vide si le moteur de synchronisation est inactif et qu’il n’exécute pas un connecteur. Si un connecteur est en cours d’exécution, il retourne le nom du connecteur.

```
Get-ADSyncConnectorRunStatus
```

![État d’exécution du connecteur](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
Dans l’illustration ci-dessus, la première ligne est dans un état où le moteur de synchronisation est inactif. La deuxième ligne à partir de quand le Azure le connecteur Active Directory est en cours d’exécution.

## <a name="scheduler-and-installation-wizard"></a>Assistant Planificateur et installation
Si vous démarrez l’Assistant d’installation, puis le planificateur est temporairement suspendu. C’est pourquoi il est supposé vous pouvez apporter des modifications de configuration et il ne peut pas être appliquées si le moteur de synchronisation est en cours d’exécution. Pour cette raison, ne laissez pas l’Assistant installation ouvert dans la mesure où il s’arrête le moteur de synchronisation d’effectuer toutes les actions de synchronisation.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la configuration [d’Azure AD connexion de synchronisation](active-directory-aadconnectsync-whatis.md) .

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
