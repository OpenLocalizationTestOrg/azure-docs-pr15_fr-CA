<properties
    pageTitle="Azure Connect de AD sync : configurer le filtrage | Microsoft Azure"
    description="Explique comment configurer le filtrage dans Azure Connect de AD sync."
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
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure Connect de AD sync : configuration du filtrage
Grâce au filtrage, vous pouvez contrôler les objets qui doivent apparaître dans Active Directory Azure à partir de votre répertoire local. La configuration par défaut prend tous les objets dans tous les domaines des forêts configurés. En général, il s’agit de la configuration recommandée. Les utilisateurs finaux à l’aide de charges de travail Office 365, tel qu’Exchange Online et Skype pour les entreprises, bénéficient d’une liste d’adresses globale complète afin de pouvoir envoyer du courrier électronique et appeler tout le monde. La configuration par défaut, ils se seraient la même expérience qu’ils le feraient avec une mise en œuvre sur site d’Exchange ou Lync.

Dans certains cas, il est nécessaire d’apporter des modifications à la configuration par défaut. Voici quelques exemples :

- Vous envisagez d’utiliser la [topologie de AD-répertoire multiples Azure](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). Vous devez appliquer un filtre pour contrôler l’objet qui doit être synchronisé à un emplacement donné répertoire d’Azure AD.
- Vous exécutez un projet pilote pour Azure ou Office 365 et si vous souhaitez uniquement un sous-ensemble d’utilisateurs dans Active Directory Azure. Dans le projet pilote à petit, il n’est pas important de disposer d’une liste d’adresses globale complète pour illustrer la fonctionnalité.
- Vous avez plusieurs comptes de service et d’autres comptes non personnelles que vous ne voulez pas dans l’annonce d’Azure.
- Pour des raisons de respect de la réglementation, vous ne supprimez pas n’importe quel utilisateur comptes locaux. Vous les désactivez uniquement. Mais dans Azure annonce uniquement comptes actifs est présente.

Cet article explique comment configurer les différentes méthodes de filtrage.

> [AZURE.IMPORTANT]Microsoft n’autorise pas la modification ou le fonctionnement de la synchronisation d’Azure Connect de publicité en dehors de ces actions officiellement documentées. Une de ces actions peut entraîner un état incohérent ou non prise en charge d’Azure Connect de AD sync et par conséquent, Microsoft ne peut pas fournir de support technique pour ces déploiements.

## <a name="basics-and-important-notes"></a>Notions de base et remarques importantes
Dans Azure Connect de AD sync, vous pouvez activer le filtrage à tout moment. Si vous démarrez avec une configuration par défaut de la synchronisation d’annuaire puis configurez le filtrage, les objets qui sont filtrés ne sont plus synchronisées dans AD Azure. À la suite de cette modification, tous les objets dans Active Directory Azure qui ont été précédemment synchronisées mais qui ont été filtrés puis sont supprimés dans Active Directory Azure.

Avant de commencer à effectuer des modifications pour filtrage, assurez-vous que vous [désactiver la tâche planifiée](#disable-scheduled-task) afin vous n’exportez pas accidentellement les modifications que vous n’avez pas encore vérifiées pour être correcte.

Dans la mesure où le filtrage supprimer plusieurs objets en même temps, vous souhaitez vous assurer que vos nouveaux filtres sont correctes avant de vous lancer l’exportation de toutes les modifications apportées à Active Directory Azure. Après avoir terminé les étapes de configuration, il est fortement recommandé de suivre les [étapes de vérification](#apply-and-verify-changes) avant d’exporter et apporter des modifications à Active Directory Azure.

Pour vous protéger contre la suppression de nombreux objets par inadvertance, la fonctionnalité [empêcher des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) est activée par défaut. Si vous supprimez plusieurs objets en raison du filtrage (500 par défaut), vous devez suivre les étapes décrites dans cet article afin de permettre la supprime atteindre AD Azure.

Si vous utilisez une génération avant novembre 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), apportez une modification à la configuration de filtre et que vous utilisez la synchronisation de mot de passe, vous devez déclencher une synchronisation complète de tous les mots de passe une fois que vous avez terminé la configuration. Pour savoir comment déclencher une synchronisation complète du mot de passe, consultez [déclenchement d’une synchronisation complète de tous les mots de passe](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Si vous êtes 1.0.9125 ou une version ultérieure, puis l’action régulière de **synchronisation complète** calcule également si les mots de passe doivent être synchronisés et cette étape supplémentaire n’est plus nécessaire.

Si les objets **utilisateur** supprimés par inadvertance dans Azure AD en raison d’une erreur de filtre, vous pouvez recréer les objets utilisateur dans Active Directory Azure en supprimant vos configurations de filtrage et puis synchroniser à nouveau vos répertoires. Cette action rétablit les utilisateurs à partir de la Corbeille dans Azure AD. Toutefois, vous ne peut pas restaurer les autres types d’objet. Par exemple, si vous supprimez accidentellement un groupe de sécurité et il a été utilisé pour les ACL une ressource, le groupe et son ACL ne peut pas être récupérés.

Azure AD Connect supprime uniquement les objets, il est considéré comme une seule fois pour être dans la portée. S’il existe des objets dans Active Directory Azure qui ont été créés par un autre moteur de synchronisation, et ces objets ne sont pas dans la portée, ajout de filtrage ne les supprimez pas. Par exemple, si vous démarrez avec un serveur de synchronisation d’annuaire et il créé une copie complète de votre répertoire entier dans Azure AD et vous installez un nouveau serveur de synchronisation d’Azure Connect de publicité en parallèle avec le filtrage est activé à partir du début, elle ne supprime pas les objets supplémentaires créés par la synchronisation d’annuaire.

La configuration de filtrage est conservée lors de l’installation ou la mise à niveau vers une version plus récente d’Azure Connect d’Active Directory. Il est toujours conseillé de vérifier que la configuration a été pas par inadvertance changée après une mise à niveau vers une version plus récente avant d’exécuter la première synchronisation de cycle.

Si vous avez plusieurs forêts, les configurations de filtrage décrites dans cette rubrique doivent être appliquées à chaque forêt (en supposant que vous souhaitez que la même configuration pour toutes les).

### <a name="disable-scheduled-task"></a>Désactiver les tâches planifiées
Pour désactiver le Planificateur intégré qui déclenche un cycle de synchronisation toutes les 30 minutes, procédez comme suit :

1. Atteindre un PowerShell prompt.
2. Exécutez `Set-ADSyncScheduler -SyncCycleEnabled $False` pour désactiver le planificateur.
3. Apportez les modifications décrites dans cette rubrique.
4. Exécution de `Set-ADSyncScheduler -SyncCycleEnabled $True` à nouveau activer le planificateur.

**Si vous utilisez une version Azure Connect de publicité avant de 1.1.105.0**  
Pour désactiver la tâche planifiée qui déclenche un cycle de synchronisation toutes les 3 heures, procédez comme suit :

1. Démarrer **Le Planificateur de tâches** dans le menu Démarrer.
2. Directement dans la **Bibliothèque du Planificateur de tâches**, rechercher la tâche nommée **Azure AD Sync planificateur**, cliquez du bouton droit et sélectionnez **désactiver**.  
![Planificateur de tâches](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Vous pouvez maintenant apporter des modifications de configuration et exécuter le moteur de synchronisation de manuellement à partir de la console du **Gestionnaire de service de synchronisation** .

Une fois que vous avez terminé toutes vos modifications de filtrage, n’oubliez pas de venir arrière et **Activer** la tâche à nouveau.

## <a name="filtering-options"></a>Options de filtrage
Les types de configuration de filtrage suivantes peuvent être appliquées à l’outil de synchronisation d’annuaire :

- [**Groupe de base**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): filtrage basé sur un groupe unique ne peut être configuré que sur une installation initiale à l’aide de l’Assistant d’installation. Il n’est pas encore couvert dans cette rubrique.

- [**Domaine**](#domain-based-filtering): cette option vous permet de sélectionner quels domaines synchroniser vers Azure AD. Il vous permet également d’ajouter et de supprimer les domaines de la configuration de moteur de synchronisation si vous apportez des modifications à votre infrastructure sur site après l’installation de la synchronisation d’Azure Connect d’AD.

- [**Basée sur une unité d’organisation**](#organizational-unitbased-filtering): cette option de filtrage vous permet de sélectionner lequel synchroniser les unités d’organisation à Active Directory Azure. Cette option est pour tous les types d’objets dans des unités d’organisation sélectionnées.

- [**Basée sur l’attribut**](#attribute-based-filtering): cette option vous permet de filtrer les objets basés sur les valeurs d’attribut sur les objets. Vous pouvez également avoir des filtres différents pour différents types d’objets.

Vous pouvez utiliser plusieurs options de filtrage en même temps. Par exemple, vous pouvez utiliser le filtrage basé sur l’unité d’organisation pour inclure uniquement les objets dans une unité d’organisation et à la même heure attribut filtrage pour filtrer les objets. Lorsque vous utilisez plusieurs méthodes de filtrage, les filtres utilisent un et logique entre les filtres.

## <a name="domain-based-filtering"></a>Filtrage basé sur le domaine
Cette section fournit les étapes pour configurer le filtre de votre domaine. Si vous avez ajouté ou supprimé des domaines de votre forêt après avoir installé Azure Connect d’Active Directory, vous devez également mettre à jour la configuration de filtrage.

Il est le meilleur moyen de modifier le filtrage basé sur le domaine en exécutant l’installation Assistant et le changement [domaine et des unités d’organisation de filtrage](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L’Assistant installation de l’automatisation de toutes les tâches décrites dans cette rubrique.

Vous suivre ces étapes uniquement si pour une raison quelconque ne pas exécuter l’Assistant installation.

Configuration de filtrage basé sur le domaine se compose des étapes suivantes :

- [Sélectionnez les domaines](#select-domains-to-be-synchronized) qui doivent être inclus dans la synchronisation.
- Pour chaque domaine ajouté et supprimé, ajuster les [profils d’exécution](#update-run-profiles).
- [Appliquer et vérifier les modifications](#apply-and-verify-changes).

### <a name="select-domains-to-be-synchronized"></a>Sélectionnez les domaines à synchroniser
**Pour définir le filtre de domaine, procédez comme suit :**

1. Ouvrez une session sur le serveur qui exécute Azure Connect de AD sync en utilisant un compte qui est membre du groupe de sécurité **ADSyncAdmins** .
2. Démarrer le **Service de synchronisation** dans le menu Démarrer.
3. Sélectionnez les **connecteurs** et dans la liste de **liens** , sélectionnez le lien avec le type de **Services de domaine Active Directory**. À partir des **Actions**, sélectionnez **Propriétés**.  
![Propriétés du connecteur](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Cliquez sur **configurer des Partitions de l’annuaire**.
5. Dans la liste **Sélectionner les partitions de l’annuaire** , sélectionnez et désélectionnez les domaines en fonction des besoins. Vérifiez que l’option uniquement les partitions que vous voulez synchroniser sont sélectionnées.  
![Partitions](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
Si vous avez modifié vos locaux infrastructure AD et ajoutés ou supprimés des domaines de la forêt, puis cliquez sur le bouton **Actualiser** pour obtenir une liste mise à jour. Lors de l’actualisation, vous demande des informations d’identification. Fournir des informations d’identification avec un accès en lecture à Active Directory sur site. Il n’a pas à être l’utilisateur qui est prédéfinie dans la boîte de dialogue.  
![Actualisation requise](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Lorsque vous avez terminé, fermez la boîte de dialogue **Propriétés** en cliquant sur **OK**. Si vous avez supprimé les domaines de la forêt, un menu contextuel de message indiquant qu'un domaine a été supprimé et que la configuration seront nettoyés.
7. Continuez à modifier les [profils d’exécution](#update-run-profiles).

### <a name="update-run-profiles"></a>Mettre à jour les profils d’exécution
Si vous avez mis à jour le filtre de votre domaine, vous devez également mettre à jour les profils d’exécution.

1. Dans la liste des **connecteurs** , assurez-vous que le connecteur que vous avez modifié à l’étape précédente est sélectionné. À partir des **Actions**, sélectionnez **Configuration des profils de s’exécuter**.  
![Connecteur de profils d’exécution](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

Vous devez ajuster les profils suivants :

- Importation intégrale
- Synchronisation complète
- Importation d’écart
- Synchronisation delta
- Exporter

Pour chacun des cinq profils, effectuez les étapes suivantes pour chaque domaine **ajouté** :

1. Sélectionnez le profil d’exécution, puis cliquez sur la **Nouvelle étape**.
2. Dans la page **Configurer l’étape** , dans la liste déroulante **Type** , sélectionnez le type d’étape portant le même nom que le profil que vous configurez. Puis cliquez sur **suivant**.  
![Connecteur de profils d’exécution](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. Sur la page de **Configuration du connecteur** , dans le menu déroulant **Partition** , sélectionnez le nom du domaine que vous avez ajouté à votre filtre de domaine.  
![Connecteur de profils d’exécution](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. Pour fermer la boîte de dialogue **Configurer le profil de s’exécuter** , cliquez sur **Terminer**.

Pour chacun des cinq profils, effectuez les étapes suivantes pour chaque domaine **supprimé** :

1. Sélectionnez le profil d’exécution.
2. Si la **valeur** de l’attribut de **Partition** est un GUID, sélectionnez l’étape d’exécution et cliquez sur **Supprimer l’étape**.  
![Connecteur de profils d’exécution](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

Le résultat devrait être que chaque domaine que vous souhaitez synchroniser doit figurer en tant qu’étape dans chaque profil d’exécution.

Pour fermer la boîte de dialogue de **Configuration des profils de s’exécuter** , cliquez sur **OK**.

- Pour terminer la configuration, [appliquer et vérifier les modifications](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrage basé sur une unité d’organisation
La meilleure façon de modifier le filtrage basé sur l’unité d’organisation est en exécutant l’installation Assistant et le changement [domaine et des unités d’organisation de filtrage](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L’Assistant installation de l’automatisation de toutes les tâches décrites dans cette rubrique.

Vous suivre ces étapes uniquement si pour une raison quelconque ne pas exécuter l’Assistant installation.

**Pour configurer le filtrage d’organisation par-unité, procédez comme suit :**

1. Ouvrez une session sur le serveur qui exécute Azure Connect de AD sync en utilisant un compte qui est membre du groupe de sécurité **ADSyncAdmins** .
2. Démarrer le **Service de synchronisation** dans le menu Démarrer.
3. Sélectionnez les **connecteurs** et dans la liste de **liens** , sélectionnez le lien avec le type de **Services de domaine Active Directory**. À partir des **Actions**, sélectionnez **Propriétés**.  
![Propriétés du connecteur](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Cliquez sur **Configurer des Partitions de répertoire**, sélectionnez le domaine que vous souhaitez configurer, puis cliquez sur **les conteneurs**.
5. Lorsque vous y êtes invité, fournissez des informations d’identification avec accès en lecture à Active Directory sur site. Il n’a pas à être l’utilisateur qui est prédéfinie dans la boîte de dialogue.
6. Dans la boîte de dialogue **Sélectionnez les conteneurs** , désactivez les unités d’organisation que vous ne souhaitez pas synchroniser avec l’annuaire de nuage, puis cliquez sur **OK**.  
![UNITÉ D’ORGANISATION](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - Le conteneur **d’ordinateurs** doit être sélectionné pour vos ordinateurs Windows 10 être synchronisé avec succès avec AD Azure. Si votre domaine des ordinateurs situés dans d’autres unités d’organisation, vérifiez que ceux-ci sont sélectionnés.
  - Le conteneur **ForeignSecurityPrincipals** doit être sélectionné si vous avez plusieurs forêts avec approbations. Ce conteneur permet l’appartenance au groupe de sécurité entre les forêts être résolu.
  - L’unité d’organisation **RegisteredDevices** doit être sélectionnée si vous avez activé l’option d’écriture différée. Si vous utilisez une autre fonctionnalité d’écriture différée, telles que l’écriture différée du groupe, assurez-vous que ces emplacements sont sélectionnés.
  - Sélectionnez une autre unité d’organisation où se trouvent les utilisateurs, les iNetOrgPersons, les groupes, les Contacts et les ordinateurs. Dans l’image, tous ces se trouvent dans l’OU ManagedObjects.
7. Lorsque vous avez terminé, fermez la boîte de dialogue **Propriétés** en cliquant sur **OK**.
8. Pour terminer la configuration, [appliquer et vérifier les modifications](#apply-and-verify-changes).

## <a name="attribute-based-filtering"></a>Filtrage basé sur l’attribut
Assurez-vous que vous êtes le 2015 novembre ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) ou générez ultérieurement pour que cette procédure fonctionne.

Attribut en fonction de filtrage est le moyen le plus souple pour filtrer des objets. Vous pouvez utiliser la puissance de [provisionnement déclaratives](active-directory-aadconnectsync-understanding-declarative-provisioning.md) pour contrôler pratiquement tous les aspects de lorsqu’un objet doit être synchronisé sur Azure AD.

Peut être appliqué sur [entrant](#inbound-filtering) à partir d’Active Directory dans le métaverse et [sortant](#outbound-filtering) du métaverse à AD Azure. Il est recommandé d’appliquer le filtrage sur entrant, puisque c’est plus facile de mettre à jour. Filtrage sortant doit être utilisé uniquement si cela est nécessaire pour joindre des objets à partir de plus d’une forêt que l’évaluation puisse avoir lieu.

### <a name="inbound-filtering"></a>Filtrage entrant
Le filtrage entrant en fonction utilise la configuration par défaut où les objets AD Azure va doivent avoir le cloudFiltered d’attribut du métaverse ne pas affecter une valeur à synchroniser. Si la valeur de cet attribut a la valeur **true**, l’objet n’est pas synchronisé. Il ne doit pas être défini **false** par conception. Pour vous assurer que les autres règles ont la possibilité de contribuer d’une valeur, cet attribut est seulement censée pour avoir les valeurs **True** ou **NULL** (manquante).

Dans le filtrage entrant, la puissance de **l’étendue** vous permet de déterminer quels objets doivent ou ne doivent pas être synchronisées. Il s’agit d’où vous effectuez des ajustements pour répondre à des besoins de votre propre organisation. Le module de la portée a **groupe** et **clause** pour déterminer si une règle de synchronisation doit être dans la portée. Un **groupe** contient un ou plusieurs **clause**. Il existe un et logique entre plusieurs clauses et un opérateur logique ou entre plusieurs groupes.

Examinons un exemple :  
![Portée](./media/active-directory-aadconnectsync-configure-filtering/scope.png) cela doit être lu comme **(département = informatique) ou (département = ventes et c = US)**.

Dans les exemples et les étapes ci-dessous, vous utilisez l’objet utilisateur à titre d’exemple, mais vous pouvez l’utiliser pour tous les types d’objet.

Dans les exemples ci-dessous, la valeur de priorité commencent à 500. Cette valeur permet à que ces règles sont évaluées après les règles d’out-of-box (de priorité plus faible, valeur numérique plus élevée).

#### <a name="negative-filtering-do-not-sync-these"></a>Négatif « ne ne pas synchroniser ces » filtrage
Dans l’exemple suivant, vous filtrez (pas synchroniser) où **extensionAttribute15** ont la valeur **NoSync**de tous les utilisateurs.

1. Ouvrez une session sur le serveur qui exécute Azure Connect de AD sync en utilisant un compte qui est membre du groupe de sécurité **ADSyncAdmins** .
2. Démarrez **l’Éditeur de règles de synchronisation** à partir du menu Démarrer.
3. Assurez-vous que **entrant** est activée, puis cliquez sur **Ajouter une nouvelle règle**.
4. Attribuer à la règle un nom descriptif, tel que «*dans à partir d’Active Directory – utilisateur DoNotSyncFilter*». Sélectionnez le **type d’objet MV**la forêt appropriée, l' **utilisateur** comme **type d’objet CS**et **personne** . En tant que **Type de lien**, cliquez sur **joindre** et ont la priorité, tapez une valeur non utilisée actuellement par une autre règle de synchronisation (par exemple 500) puis cliquez sur **suivant**.  
![1 description de trafic entrant](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. **Scoping filtre**, cliquez sur **Ajouter un groupe**, cliquez sur **Ajouter une Clause**et dans un attribut, sélectionnez **ExtensionAttribute15**. Assurez-vous que l’opérateur est défini sur **égal** et tapez la valeur **NoSync** dans la zone valeur. Cliquez sur **suivant**.  
![2 étendue de trafic entrant](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Ne renseignez pas les règles de **jointure** , puis cliquez sur **suivant**.
7. Cliquez sur **Ajouter une Transformation**, sélectionnez l' **TauxChange** à la **constante**, sélectionnez l' attribut cible **cloudFiltered** et dans la zone de texte Source, tapez **Vrai**. Cliquez sur **Ajouter** pour enregistrer la règle.  
![Entrée de transformation 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Pour terminer la configuration, [appliquer et vérifier les modifications](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positif, filtrage, « synchroniser uniquement ces »
Expression de filtrage positif peut être plus difficile car vous devez également prendre en compte les objets qui ne sont pas évidentes à synchroniser, tels que les salles de conférence.

L’option de filtrage positive nécessite deux règles de synchronisation. Un (ou plusieurs) à la portée correcte des objets à synchroniser et le second la synchronisation passe-partout la règle ce filtre à tous les objets qui n’ont pas encore été identifiées en tant qu’objet qui doit être synchronisé.

Dans l’exemple suivant, vous ne synchroniser que les objets utilisateur dans lequel cet attribut a la valeur de **vente**.

1. Ouvrez une session sur le serveur qui exécute Azure Connect de AD sync en utilisant un compte qui est membre du groupe de sécurité **ADSyncAdmins** .
2. Démarrez **l’Éditeur de règles de synchronisation** à partir du menu Démarrer.
3. Assurez-vous que **entrant** est activée, puis cliquez sur **Ajouter une nouvelle règle**.
4. Attribuer à la règle un nom descriptif, tel que «*à partir d’Active Directory – utilisateur ventes synchronisés*». Sélectionnez le **type d’objet MV**la forêt appropriée, l' **utilisateur** comme **type d’objet CS**et **personne** . En tant que **Type de lien**, cliquez sur **joindre** et ont la priorité, tapez une valeur non utilisée actuellement par une autre règle de synchronisation (par exemple 501) puis cliquez sur **suivant**.  
![4 description de trafic entrant](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. **Scoping filtre**, cliquez sur **Ajouter un groupe**, cliquez sur **Ajouter une Clause**et dans un attribut, sélectionnez **département**. Assurez-vous que l’opérateur est défini sur **égal** et tapez la valeur de **vente** dans la zone valeur. Cliquez sur **suivant**.  
![Entrant portée 5](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Ne renseignez pas les règles de **jointure** , puis cliquez sur **suivant**.
7. Cliquez sur **Ajouter une Transformation**, sélectionnez l' **TauxChange** à la **constante**, sélectionnez l' attribut cible **cloudFiltered** et dans la zone de texte Source, tapez la **valeur False**. Cliquez sur **Ajouter** pour enregistrer la règle.  
![Entrée de transformation 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
Il s’agit d’un cas particulier où vous définissez cloudFiltered explicitement sur False.

    Nous devons maintenant créer la règle de collecte sync.

8. Attribuer à la règle un nom descriptif, tel que «*dans à partir d’Active Directory – filtre de l’utilisateur pour tout*». Sélectionnez le **type d’objet MV**la forêt appropriée, l' **utilisateur** comme **type d’objet CS**et **personne** . **Type de lien**, cliquez sur **joindre** et ont la priorité, tapez une valeur non utilisée actuellement par une autre règle de synchronisation (par exemple 600). Vous avez sélectionné une priorité valeur priorité plus élevée (inférieure) que la règle de la synchronisation précédente mais également à gauche place afin que nous pouvons ajouter plusieurs règles de filtrage de synchronisation ultérieurement lorsque vous souhaitez démarrer la synchronisation des services supplémentaires. Cliquez sur **suivant**.  
![Entrant description 7](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Laissez **Scoping filtre** vide et cliquez sur **suivant**. Un filtre vide indique que la règle doit être appliquée à tous les objets.
10. Ne renseignez pas les règles de **jointure** , puis cliquez sur **suivant**.
11. Cliquez sur **Ajouter une Transformation**, sélectionnez l' **TauxChange** à la **constante**, sélectionnez l' attribut cible **cloudFiltered** et dans la zone de texte Source, tapez **Vrai**. Cliquez sur **Ajouter** pour enregistrer la règle.  
![Entrée de transformation 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Pour terminer la configuration, [appliquer et vérifier les modifications](#apply-and-verify-changes).

Si vous le souhaitez, vous pouvez créer plusieurs règles du premier type où vous incluez plusieurs objets dans notre synchronisation.

### <a name="outbound-filtering"></a>Filtrage sortant
Dans certains cas, il est nécessaire d’effectuer le filtrage uniquement lorsque les objets se sont engagés dans le métaverse. Il pourrait, par exemple, être nécessaire d’examiner l’attribut mail à partir de la forêt de ressources et de l’attribut userPrincipalName à partir de la forêt de compte pour déterminer si un objet doit être synchronisé. Dans ces cas, vous créez le filtrage de la règle de trafic sortant.

Dans cet exemple, vous modifiez les utilisateurs de conséquent que filtrage où les messages et userPrincipalName terminent avec @contoso.com sont synchronisées :

1. Ouvrez une session sur le serveur qui exécute Azure Connect de AD sync en utilisant un compte qui est membre du groupe de sécurité **ADSyncAdmins** .
2. Démarrez **l’Éditeur de règles de synchronisation** à partir du menu Démarrer.
3. Sous **Type de règles**, cliquez sur **sortie**.
4. Rechercher la règle **les DAS – SOAInAD Join d’utilisateur**. Cliquez sur **Modifier**.
5. Dans le menu contextuel, répondez **Oui** pour créer une copie de la règle.
6. Sur la page **Description** , modifier le niveau de priorité à une valeur non utilisée, par exemple 50.
7. Dans la barre de navigation gauche, cliquez sur **filtre de Scoping** . Cliquez sur **Ajouter la clause**, dans l’attribut, sélectionnez **courrier**, opérateur, sélectionnez **ENDSWITH**et type valeur **@contoso.com**. Cliquez sur **Ajouter la clause**, dans sélection d’attribut **userPrincipalName**, opérateur, sélectionnez **ENDSWITH**et type valeur **@contoso.com**.
8. Cliquez sur **Enregistrer**.
9. Pour terminer la configuration, [appliquer et vérifier les modifications](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Appliquer et vérifier les modifications
Une fois que vous avez modifié votre configuration, celles-ci doivent être appliquées aux objets déjà présents dans le système. Elle peut également être des objets n’est pas actuellement dans le moteur de synchronisation doivent être traités que le moteur de synchronisation doit lire le système source à nouveau pour vérifier son contenu.

Si vous avez modifié la configuration à l’aide de **domaine** ou **l’unité d’organisation** de filtrage, vous devez effectuer **une importation complète** suivie de **synchronisation Delta**.

Si vous avez modifié la configuration à l’aide du filtrage d' **attribut** , vous devez effectuer **une synchronisation complète**.

Effectuez les étapes suivantes :

1. Démarrer le **Service de synchronisation** dans le menu Démarrer.
2. Sélectionnez les **connecteurs** et dans la liste de **liens** , sélectionnez le lien où vous modifié la configuration antérieure. **Actions**, sélectionnez **exécuter**.  
![Connecteur à exécuter](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. Dans les **profils d’exécution**, sélectionnez l’opération mentionnée dans la section précédente. Si vous avez besoin exécuter les deux actions, exécutez la deuxième après le premier d'entre eux est terminée (la colonne de **l’état** est **inactif** pour le lien sélectionné).

Après la synchronisation, toutes les modifications sont transférées pour pouvoir être exporté. Avant les modifications dans Active Directory Azure, vous voulez vérifier que toutes ces modifications sont correctes.

1. Démarrez une invite de commandes et accédez à`%Program Files%\Microsoft Azure AD Sync\bin`
2. Exécuter :`csexport "Name of Connector" %temp%\export.xml /f:x`  
Vous trouverez le nom du connecteur de Service de synchronisation. Il a un nom similaire à « contoso.com – DAS » pour Azure AD.
3. Exécuter :`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Vous disposez maintenant d’un fichier dans % temp % nommé export.csv qui peut être examiné dans Microsoft Excel. Ce fichier contient toutes les modifications qui doivent être exportées.
5. Apportez les modifications nécessaires à la configuration ou de données et d’exécuter ces étapes à nouveau (importation, synchroniser et vérifier) jusqu'à ce que les modifications qui sont sur le point d’être exportées sont prévues.

Lorsque vous êtes satisfait, exporter les modifications apportées à AD Azure.

1. Sélectionnez **les connecteurs** puis dans la liste des **connecteurs** , le connecteur Active Directory Azure. **Actions**, sélectionnez **exécuter**.
2. Dans les **profils d’exécution**, sélectionnez **Exporter**.
3. Si les modifications de votre configuration supprimez plusieurs objets, vous verrez une erreur à l’exportation lorsque le nombre est supérieur au seuil configuré (par défaut 500). Si vous rencontrez cette erreur, vous devez désactiver temporairement la fonctionnalité, [pas de suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Il est maintenant temps de réactiver le planificateur.

1. Démarrer **Le Planificateur de tâches** dans le menu Démarrer.
2. Directement dans la **Bibliothèque du Planificateur de tâches**, rechercher la tâche nommée **Azure AD Sync planificateur**, cliquez du bouton droit et sélectionnez **Activer**.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la configuration [d’Azure AD connexion de synchronisation](active-directory-aadconnectsync-whatis.md) .

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
