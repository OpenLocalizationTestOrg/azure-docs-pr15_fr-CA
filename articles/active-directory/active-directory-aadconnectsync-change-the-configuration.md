<properties
    pageTitle="Azure Connect de AD sync : comment faire pour apporter une modification à la configuration par défaut | Microsoft Azure"
    description="Vous explique comment apporter une modification à la configuration de la synchronisation d’Azure Connect d’AD."
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
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Azure Connect de AD sync : comment faire pour apporter une modification à la configuration par défaut
L’objectif de cette rubrique est de vous guider tout au long de comment apporter des modifications à la configuration par défaut dans Azure Connect de AD sync. Il fournit des étapes pour les scénarios courants. Fort de ces connaissances, vous devez être en mesure d’effectuer des modifications simples à votre propre configuration en fonction de vos propres règles d’entreprise.

## <a name="synchronization-rules-editor"></a>Éditeur de règles de synchronisation
L’éditeur de règles de synchronisation est utilisé pour afficher et modifier la configuration par défaut. Vous le trouverez dans le Menu Démarrer sous le groupe **Azure Connect d’Active Directory** .  
![Menu Démarrer avec l’éditeur de règle de synchronisation](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Lorsque vous l’ouvrez, vous consultez les règles d’out-of-box par défaut.

![Éditeur de règles de synchronisation](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigation dans l’éditeur
Les listes déroulantes en haut de l’éditeur vous permettent de trouver rapidement une règle particulière. Par exemple, si vous souhaitez voir les règles où l’attribut proxyAddresses est inclus, vous modifieriez les listes déroulantes pour les éléments suivants :  
![Filtrage de SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Pour redéfinir le filtrage et charger une nouvelle configuration, appuyez sur **F5** sur le clavier.

La partie supérieure droite, vous avez un bouton **Ajouter une nouvelle règle**. Ce bouton permet de créer vos propres règles personnalisées.

En bas, vous disposez des boutons d’action sur une règle de synchronisation sélectionnée. **Modifier** et **Supprimer** le faire ce que vous escomptiez. **Exporter** génère un script PowerShell pour recréer les règles de synchronisation. Cette procédure vous permet de déplacer une règle de synchronisation à partir d’un serveur à un autre.

## <a name="create-your-first-custom-rule"></a>Créer votre première règle personnalisée
Le changement le plus commun est modifié pour les flux d’attributs. Les données de votre répertoire source peut-être pas comme dans Azure AD. Dans l’exemple dans cette section, vous souhaitez vous assurer que le nom d’un utilisateur donné est toujours en **majuscules**.

### <a name="disable-the-scheduler"></a>Désactiver le planificateur
Le [Planificateur](active-directory-aadconnectsync-feature-scheduler.md) exécute toutes les 30 minutes par défaut. Vous souhaitez vous assurer qu’il ne démarre pas pendant que vous apportez des modifications et résoudre les problèmes liés à vos nouvelles règles. Pour désactiver temporairement le planificateur, démarrer PowerShell et exécuter`Set-ADSyncScheduler -SyncCycleEnabled $false`

![Désactiver le planificateur](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Création de la règle

1. Cliquez sur **Ajouter une nouvelle règle**.
2. Dans la page **Description** , entrez les informations suivantes :  
![Filtrage de la règle de trafic entrant](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
    - Nom : Donnez un nom descriptif à la règle.
    - Description : Certaines précisions afin que quelqu'un d’autre peut comprendre ce qu’est la règle pour.
    - Connexion système : le système de l’objet peut être trouvé dans. Dans ce cas, nous allons sélectionner le connecteur Active Directory.
    - Type d’objet système/métaverse connecté : Sélectionnez **utilisateur** et **personne** respectivement.
    - Type de lien : Modifier cette valeur à **joindre**.
    - Priorité : Fournir une valeur qui est unique dans le système. Une valeur numérique inférieure indique une priorité plus élevée.
    - Balise : Laissez vide. Seules les règles d’out-of-box de Microsoft doivent avoir cette case contient une valeur.
3. Sur la page de **filtre de Scoping** , entrez **givenName ISNOTNULL**.  
![Filtre de portée de règle de trafic entrant](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
Cette section permet de définir les objets de que la règle doit s’appliquer à. Si laissé vide, la règle s’appliquera à tous les objets utilisateur. Mais qui comprendrait des salles de conférence, les comptes de service et autres objets non humains utilisateur.
4. Sur les **règles de jointure**, laissez-la vide.
5. Sur la page de **Transformations** , modifier le TauxChange à **l’Expression**. Sélectionnez l' attribut cible **givenName**et entrez dans la Source de `PCase([givenName])`.
![Règle de trafic entrant transformations](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
Le moteur de synchronisation est sensible à la casse à la fois sur le nom de la fonction et le nom de l’attribut. Si vous tapez un problème, vous voyez un message d’avertissement lorsque vous ajoutez la règle. L’éditeur vous permet d’enregistrer et de continuer, donc vous devrez rouvrir la règle et corrigez la règle.
6. Cliquez sur **Ajouter** pour enregistrer la règle.

Votre nouvelle règle personnalisée doit être visible par les autres règles de synchronisation dans le système.

### <a name="verify-the-change"></a>Vérifiez la modification
Avec cette nouvelle modification, si vous souhaitez vous assurer qu’il fonctionne comme prévu et n’est pas levée des erreurs. En fonction du nombre d’objets que vous avez, il existe deux manières d’effectuer cette étape.

1. Exécuter une synchronisation complète de tous les objets
2. Exécuter un aperçu et une synchronisation complète sur un seul objet

Démarrer le **Service de synchronisation** dans le menu Démarrer. Les étapes de cette section sont tous dans cet outil.

1. **Synchronisation complète de tous les objets**  
Sélectionnez les **connecteurs** en haut. Identifier le connecteur que vous effectué une modification dans la section précédente, dans ce cas, les Services de domaine Active Directory, et sélectionnez-le. Sélectionnez **exécuter** des Actions, **Une synchronisation complète** et **OK**.
![Synchronisation complète](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
Les objets sont maintenant mises à jour dans le métaverse. Vous voulez maintenant consulter l’objet du métaverse.

2. **Aperçu et synchronisation complète sur un seul objet**  
Sélectionnez les **connecteurs** en haut. Identifier le connecteur que vous effectué une modification dans la section précédente, dans ce cas, les Services de domaine Active Directory, et sélectionnez-le. Sélectionnez **espace de connecteur de recherche**. Étendue permet de trouver un objet que vous souhaitez utiliser pour tester les modifications. Sélectionnez l’objet, cliquez sur **Aperçu**. Dans l’écran Nouveau, sélectionnez **Aperçu avant de valider**.
![Valider l’aperçu](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
La modification s’engage dans le métaverse.

**Consulter l’objet du métaverse**  
Vous voulez maintenant choisir quelques exemples d’objets pour vous assurer que la valeur attendue et que la règle est appliquée. Sélectionnez la **Recherche** à partir du haut. Ajouter un filtre, que vous devez rechercher les objets pertinents. Résultats de la recherche, ouvrir un objet. Examinez les valeurs d’attribut et vérifiez également dans la colonne **Règles de synchronisation** que la règle soit appliquée comme prévue.  
![Recherche](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  
### <a name="enable-the-scheduler"></a>Activer le planificateur
Si tout est comme prévu, vous pouvez activer de nouveau le planificateur. À partir de PowerShell, exécutez `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Les autres modifications de flux d’attribut courantes
La section précédente décrit comment apporter des modifications à un flux d’attribut. Dans cette section, des exemples supplémentaires sont fournies. Les étapes de la création de la règle de synchronisation est abrégé, mais vous trouverez la procédure complète dans la section précédente.

### <a name="use-another-attribute-than-the-default"></a>Utiliser un autre attribut à la valeur par défaut.
Chez Fabrikam, il existe une forêt où l’alphabet local est utilisé pour le prénom, nom et nom complet. La représentation sous forme de caractères Latin de ces attributs peut être trouvée dans les attributs de l’extension. Lors de la création de la liste d’adresses globale dans AD Azure et Office 365, l’organisation souhaite que ces attributs à la place.

Avec une configuration par défaut, un objet de la forêt locale ressemble à ceci :  
![Flux des attributs 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Pour créer une règle avec les flux d’autres attributs, effectuez les opérations suivantes :

- Démarrez **l’Éditeur de règle de synchronisation** à partir du menu Démarrer.
- **Entrant** toujours sélectionné vers la gauche, cliquez sur le bouton **Ajouter une nouvelle règle**.
- Attribuer un nom et une description à la règle. Sélectionnez Active Directory sur site et les types d’objet approprié.  Dans le **Type de lien**, cliquez sur **joindre**. Pour priorité, choisissez un numéro qui n’est pas utilisé par une autre règle. Les règles d’out-of-box commencent par 100, la valeur 50 peut être utilisée dans cet exemple.
![Flux des attributs 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- Laissez une étendue vide (autrement dit, doit s’appliquent à tous les objets utilisateur dans la forêt).
- Ne renseignez pas les règles de jointure (c'est-à-dire, laisser la règle out-of-box gérer toutes les jointures).
- Dans les Transformations, créez les flux suivants :  
![Flux des attributs 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- Cliquez sur **Ajouter** pour enregistrer la règle.
- Accédez au **Gestionnaire de Service de synchronisation**. Sur les **connecteurs**, sélectionnez le connecteur dans lequel nous avons ajouté la règle. Sélectionnez **exécuter**et **synchronisation complète**. Une synchronisation complète recalcule tous les objets en utilisant les règles en cours.

Voici le résultat pour le même objet avec cette règle personnalisée :  
![Flux des attributs 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Longueur des attributs
Attributs de chaîne sont par défaut défini pour être indexable et la longueur maximale est de 448 caractères. Si vous travaillez avec des attributs de chaîne qui peuvent contenir de plus, assurez-vous d’inclure les éléments suivants dans le flux des attributs :  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Modification de l’userPrincipalSuffix
L’attribut userPrincipalName dans Active Directory n’est pas toujours connu par les utilisateurs et n’est peut-être pas approprié que le nom de connexion. La connexion AD Azure Assistant installation de la synchronisation permet de choisir un attribut différent, par exemple de courrier électronique. Mais dans certains cas l’attribut doit être calculé. Par exemple, la société Contoso a deux répertoires AD Azure, un pour la production et un pour le test. Ils veulent les utilisateurs dans leurs clients de test à utiliser un autre suffixe dans l’ID de connexion.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Dans cette expression, prendre tout gauche de la première @-sign (Word) et concaténer avec une chaîne fixe.

### <a name="convert-a-multi-value-to-a-single-value"></a>Convertir des valeurs multiples à une valeur unique
Certains attributs dans Active Directory sont à valeurs multiples dans le schéma, même si leur aspect unique à valeurs dans Active Directory utilisateurs et ordinateurs. Un exemple est l’attribut de description.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Dans cette expression dans le cas où l’attribut a une valeur, nous prendre le premier élément (Item) dans l’attribut, supprimer les espaces à gauche et (Trim) et puis conserver les caractères d’abord 448 (à gauche) dans la chaîne.

### <a name="do-not-flow-an-attribute"></a>Ne passent pas d’un attribut
Pour informations sur le scénario de cette section, consultez [contrôle le processus de flux des attributs](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Il existe deux façons de ne pas un attribut de flux. Le premier est disponible dans l’Assistant d’installation et vous permet de [Supprimer les attributs sélectionnés](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Cette option fonctionne si vous n’avez jamais synchronisé l’attribut avant. Toutefois, si vous avez commencé à synchroniser cet attribut et de le supprimer plus tard avec cette fonctionnalité, le moteur de synchronisation s’arrête de gestion de l’attribut et les valeurs existantes restent dans Azure AD.

Si vous souhaitez supprimer la valeur d’un attribut et assurez-vous qu’il n’est pas transmis à l’avenir, vous devez créer une règle personnalisée à la place.

Chez Fabrikam, nous avons réalisé que certains attributs que nous synchroniser vers le nuage ne doivent pas exister. Nous souhaitons vous assurer que ces attributs sont supprimés de l’annonce Azure.  
![Attributs d’Extension incorrecte](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- Créer une nouvelle règle de synchronisation entrants et remplir la description ![Descriptions](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- Créer un flux d’attributs de type **Expression** et avec la source de **AuthoritativeNull**. littéral **AuthoritativeNull** indique que la valeur doit être vide dans le MV même si une règle de synchronisation de priorité inférieure tente de définir la valeur.
![Transformation des attributs d’Extension](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- Enregistrer la règle de la synchronisation. Démarrer le **Service de synchronisation**, trouver le connecteur, sélectionnez **exécuter**et **Une synchronisation complète**. Cette étape recalcule tous les flux d’attributs.
- Vérifiez que les modifications sont sur le point d’être exportés par la recherche de l’espace de connecteur.
![Supprimer nouvellement créé](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur le modèle de configuration de [Mise en service déclaratif de présentation](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Apprenez-en plus sur le langage des expressions dans la [Compréhension des Expressions déclaratives mise en service](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Rubriques de vue d’ensemble**

- [Azure Connect de AD sync : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
