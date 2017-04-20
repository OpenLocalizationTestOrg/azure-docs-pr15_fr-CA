<properties
    pageTitle="Azure Connect de AD sync : le Gestionnaire de Service de synchronisation UI | Microsoft Azure"
    description="Comprendre l’onglet opérations dans le Gestionnaire de Service de synchronisation pour Azure Connect d’Active Directory."
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

![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

L’onglet opérations affiche les résultats dans les opérations de la plus récentes. Cet onglet est la clé pour comprendre et résoudre les problèmes.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Comprendre les informations visibles dans l’onglet opérations
La moitié supérieure affiche toutes les séries de chronique. Par défaut, le journal des opérations conserve les informations relatives au cours des sept derniers jours, mais ce paramètre peut être modifié avec le [Planificateur](active-directory-aadconnectsync-feature-scheduler.md). Vous souhaitez rechercher une exécution qui n’affiche pas un état de réussite. Vous pouvez modifier le tri en cliquant sur les en-têtes.

La colonne **statut** est l’information la plus importante et indique le problème plus grave pour une exécution. Voici un récapitulatif rapide des statuts par ordre de priorité pour étudier plus courants (où * indiquer plusieurs chaînes d’erreur possibles).

État | Commentaire
--- | ---
arrêté-* | L’exécution n’a pas pu se terminer. Par exemple, si le système distant est arrêté et ne peut pas être contacté.
arrêté-erreur-limite | Il n’y a plus de 5 000 erreurs. L’exécution a été automatiquement arrêtée en raison du grand nombre d’erreurs.
terminé -\*-erreurs | L’exécution est terminée, mais des erreurs (moins de 5 000) doivent être analysés.
terminé -\*-avertissements | L’exécution terminée, mais des données n’est pas dans l’état attendu. Si vous avez des erreurs, ce message est généralement uniquement un symptôme. Jusqu'à ce que vous avez résolu les erreurs, vous ne devez pas examiner avertissements.
opération réussie | Aucun problème.

Lorsque vous sélectionnez une ligne, bas met à jour pour afficher les détails de cette exécution. À l’extrémité gauche de la partie inférieure, vous pouvez avoir une liste indiquant **l’étape #**. Cette liste s’affiche uniquement si vous avez plusieurs domaines dans votre forêt où chaque domaine est représenté par une étape. Le nom de domaine sont accessibles sous l’en-tête de la **Partition**. Sous **Statistiques de synchronisation**, vous trouverez plus d’informations sur le nombre de modifications qui ont été traités. Vous pouvez cliquer sur les liens pour obtenir une liste des objets modifiés. Si vous avez des objets avec des erreurs, ces erreurs s’affichent sous les **Erreurs de synchronisation**.

## <a name="troubleshoot-errors-in-operations-tab"></a>Résoudre les erreurs dans l’onglet opérations
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
Lorsque vous avez des erreurs, à la fois l’objet erreur et l’erreur elle-même sont des liens qui fournit des informations supplémentaires.

Démarrer en cliquant sur la chaîne d’erreur (**sync-règle-erreur-fonction-déclenchée** dans l’image). Tout d’abord, vous sont présentées avec une vue d’ensemble de l’objet. Pour voir l’erreur, cliquez sur le bouton **Trace de la pile**. Cette trace fournit des informations de débogage au niveau de l’erreur.

**Conseil :** Vous pouvez avec le bouton droit dans la zone **informations de pile d’appel** , cliquez sur **Sélectionner tout**et sur la **copie**. Vous pouvez ensuite copier la pile et examiner l’erreur dans votre éditeur favori, tel que le bloc-notes.

- Si l’erreur est de **SyncRulesEngine**, les informations de pile d’appels a tout d’abord une liste de tous les attributs sur l’objet. Faites défiler la liste vers le bas jusqu'à ce que vous voyiez l’en-tête **InnerException = >**.  
![Gestionnaire de Service de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
La ligne après indique l’erreur. Dans l’image ci-dessus, l’erreur est d’un Fabrikam de règle de synchronisation personnalisé créé.

Si l’erreur elle-même ne fournit pas suffisamment d’informations, alors il est temps d’examiner les données elles-mêmes. Vous pouvez cliquer sur le lien avec l’identificateur d’objet et de [suivre un objet et ses données via le système](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la configuration [d’Azure AD connexion de synchronisation](active-directory-aadconnectsync-whatis.md) .

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
