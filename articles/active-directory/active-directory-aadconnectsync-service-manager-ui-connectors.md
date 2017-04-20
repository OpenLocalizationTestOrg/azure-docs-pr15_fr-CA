<properties
    pageTitle="Azure Connect de AD sync : le Gestionnaire de Service de synchronisation UI | Microsoft Azure"
    description="Comprendre l’onglet connecteurs dans le Gestionnaire de Service de synchronisation pour Azure Connect d’Active Directory."
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
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Azure Connect de AD sync : le Gestionnaire de Service de synchronisation

[Opérations](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connecteurs](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Recherche](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

L’onglet connecteurs permet de gérer tous les systèmes, à que le moteur de synchronisation est connecté.

## <a name="connector-actions"></a>Actions de connecteur

Action | Commentaire
--- | ---
Créer | N’utilisez pas. Pour la connexion à des forêts Active Directory, utilisez l’Assistant installation.
Propriétés | Utilisé pour le domaine et le filtrage de l’unité d’organisation.
[Supprimer](#delete) | Utilisé pour supprimer les données dans l’espace de connecteur ou de supprimer la connexion à une forêt.
[Configurer les profils d’exécution](#configure-run-profiles) | À l’exception du domaine filtrage, rien à configurer ici. Vous pouvez utiliser cette action pour afficher des profils d’exécution déjà configurés.
Exécuter | Permet de lancer une exécution unique d’un profil.
Arrêter | Arrête un connecteur un profil en cours d’exécution.
Connecteur d’exportation | N’utilisez pas.
Connecteur d’importation | N’utilisez pas.
Connecteur de mise à jour | N’utilisez pas.
Actualiser le schéma | Actualise le schéma de mise en cache. Il est préférable d’utiliser l’option de l’Assistant d’installation, à la place, étant donné qu’également mises à jour synchroniser les règles.
[Espace de connecteur de recherche](#search-connector-space) | Permet de rechercher des objets et de [suivre un objet et ses données à travers le système](#follow-an-object-and-its-data-through-the-system).

### <a name="delete"></a>Supprimer
L’action de suppression est utilisée pour deux choses différentes.
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

L’option **Supprimer uniquement l’espace de connecteur** supprime toutes les données, en conservant la configuration.

L’option **Supprimer le connecteur et l’espace de connecteur** supprime les données et la configuration. Cette option est utilisée lorsque vous ne souhaitez pas plus de se connecter à une forêt.

Les deux options de synchronisation de tous les objets et mettre à jour les objets du métaverse. Cette action est une opération longue.

### <a name="configure-run-profiles"></a>Configurer les profils d’exécution
Cette option vous permet de voir les profils d’exécution configurés pour un connecteur.

![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Espace de connecteur de recherche
L’action d’espace de connecteur de recherche est utile pour rechercher des objets et résoudre les problèmes de données.

![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Commencez par sélectionner une **portée**. Vous pouvez effectuer une recherche sur la base de données (RDN, DN, ancre, sous-arborescence), ou l’état de l’objet (pour toutes les autres options).  
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Si vous le faites par exemple une recherche de sous-arborescence, vous obtenez tous les objets dans une unité d’organisation.
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) à partir de cette grille, vous pouvez sélectionner un objet, sélectionnez **Propriétés**et [la faire suivre](#follow-an-object-and-its-data-through-the-system) à partir de l’espace source du connecteur, le méta-verse et à l’espace de connecteur cible.

## <a name="follow-an-object-and-its-data-through-the-system"></a>Suivre un objet et ses données via le système
Lorsque vous résolvez un problème avec les données, procédez comme un objet à partir de l’espace de connecteur source, dans le métaverse et à la cible, espace de connecteur est une procédure clée pour comprendre pourquoi les données n’ont pas les valeurs attendues.

### <a name="connector-space-object-properties"></a>Propriétés de l’objet espace connecteur
**Importation**  
Lorsque vous ouvrez un objet cs, il existe plusieurs onglets en haut. L’onglet **Importer** affiche les données qui sont mis en place après une importation.
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) la **Valeur ancienne** montre ce qui est stocké dans le système et la **Nouvelle valeur** de ce qui a été reçu à partir du système source et n’a pas encore été appliqué. Dans ce cas, dans la mesure où il existe une erreur de synchronisation, la modification ne peut pas être appliquée.

**Erreur**  
La page d’erreurs n’est visible que s’il existe un problème avec l’objet. Consultez les détails dans la page opérations pour plus d’informations sur la façon de [résoudre les erreurs de synchronisation](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab).

**Enregistrement en ligne**  
L’onglet de l’enregistrement en ligne montre comment l’objet espace de connecteur est liée à l’objet du métaverse. Vous pouvez voir le connecteur a importés dernier une modification dans le système connecté et les règles appliquées pour remplir les données dans le métaverse.
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) dans la colonne **Action** , vous pouvez voir une règle de synchronisation **entrante** avec l’action de **disposition**. Qui indique que tant que cet objet d’espace de connecteur est présent, l’objet du métaverse reste. Si la liste de règles de synchronisation affiche à la place une règle de synchronisation avec un direction **sortante** et de la **fourniture**, elle indique que cet objet est supprimé lors de la suppression de l’objet du métaverse.
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) vous pouvez également voir dans la colonne **PasswordSync** que l’espace de connecteur entrant peut y apporter des modifications au mot de passe dans la mesure où une seule règle de synchronisation a la valeur **True**. Ce mot de passe est ensuite envoyé à Azure AD par l’intermédiaire de la règle de trafic sortant.

Sous l’onglet enregistrement en ligne, vous pouvez obtenir dans le métaverse en cliquant sur [Propriétés de l’objet du métaverse](#metaverse-object-properties).

Au bas de tous les onglets se trouvent deux boutons : **Aperçu** et **journal**.

**Aperçu**  
La page d’aperçu permet de synchroniser un objet unique. Il est utile si vous dépannez certaines règles de synchronisation du client et que vous souhaitez voir l’effet d’une modification sur un objet unique. Vous pouvez choisir entre la **Synchronisation complète** et **synchronisation de Delta**. Vous pouvez également choisir entre **Générer l’aperçu**, qui permet de conserver uniquement la modification dans la mémoire et **Aperçu de Commit**, qui transfère tous les remplace par un espace de connecteur de cible.
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) vous pouvez inspecter l’objet et la règle est appliquée pour un flux d’attribut particulier.
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Journal**  
La page de journal est utilisée pour afficher l’état de la synchronisation de mot de passe et l’historique, pour plus d’informations, reportez-vous à la section [synchronisation de mot de passe de résolution des problèmes](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization) .

### <a name="metaverse-object-properties"></a>Propriétés de l’objet du métaverse
**Attributs**  
Sous l’onglet attributs, vous pouvez voir les valeurs et quel connecteur il a contribué.
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)
**liens**  
L’onglet connecteurs affiche tous les espaces de connecteur ayant une représentation de l’objet.
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) cet onglet vous permet également d’accéder à l' [objet espace de connecteur](#connector-space-object-properties).

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la configuration [d’Azure AD connexion de synchronisation](active-directory-aadconnectsync-whatis.md) .

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
