<properties
   pageTitle="Azure Connect de AD sync : empêcher des suppressions accidentelles | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité empêcher des suppressions accidentelles (prévention des suppressions accidentelles) Azure Connect d’AD."
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
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure Connect de AD sync : empêcher des suppressions accidentelles
Cette rubrique décrit la fonctionnalité empêcher des suppressions accidentelles (prévention des suppressions accidentelles) Azure Connect d’AD.

Lorsque l’installation Azure AD Connect, empêche accidentelle suppressions est activé par défaut et configuré pour ne pas autoriser une exportation avec plus de 500 suppressions. Cette fonctionnalité est conçue pour vous protéger contre les modifications accidentelles de configuration et les modifications dans votre répertoire local qui affecte de nombreux utilisateurs et autres objets.

Scénarios courants lorsque vous voyez des suppressions nombreuses incluent :

- Modifications apportées au [filtrage](active-directory-aadconnectsync-configure-filtering.md) où une toute [unité d’organisation](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) ou un [domaine](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) n’est pas cochée.
- Tous les objets dans une unité d’organisation sont supprimés.
- Une unité d’organisation est renommée pour tous les objets qu’il contient sont considérés comme hors de portée pour la synchronisation.

La valeur par défaut de 500 objets peut être modifiée avec PowerShell à l’aide de `Enable-ADSyncExportDeletionThreshold`. Vous devez configurer cette valeur en fonction de la taille de votre organisation. Étant donné que le Planificateur de synchronisation s’exécute toutes les 30 minutes, la valeur est le nombre de suppressions apparaît dans les 30 minutes.

S’il y a trop de suppressions transférées pour être exporté vers Azure AD, l’exportation s’arrête et vous recevez un e-mail comme suit :

![Empêcher des suppressions accidentelles e-mail](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Bonjour (contact technique). À (heure), le service de synchronisation d’identité détecté que le nombre de suppressions a dépassé le seuil de suppression configuré pour (nom de l’organisation). Un ensemble de (nombre) des objets ont été envoyés pour la suppression dans cette synchronisation d’identité s’exécutée. Cela atteint ou dépassé le seuil configuré de suppression d’objets de (nombre). Nous devons fournir une confirmation que ces suppressions doivent être traitées avant de se poursuivre. Consultez les suppressions accidentelles de prévention pour plus d’informations sur l’erreur indiquée dans ce message électronique.*

Vous pouvez également afficher le statut `stopped-deletion-threshold-exceeded` lorsque vous les consultez dans le **Gestionnaire de Service de synchronisation** de l’interface utilisateur pour le profil d’exportation.
![Pas de gestionnaire de Service de synchronisation UI de suppressions accidentelles](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Si c’était inattendu, puis examiner et prendre des mesures correctives. Pour voir quels objets sont sur le point d’être supprimé, effectuez les opérations suivantes :

1. Démarrer le **Service de synchronisation** dans le Menu Démarrer.
2. Accédez à **connecteurs**.
3. Sélectionnez le connecteur avec type **Azure Active Directory**.
4. Sous **Actions** à droite, sélectionnez **l’Espace de connecteur de recherche**.
5. Dans le menu contextuel sous **étendue**, sélectionnez **Déconnectée car** , puis choisissez une heure dans le passé. Cliquez sur **Rechercher**. Cette page fournit une vue de tous les objets sur le point d’être supprimé. En cliquant sur chaque élément, vous pouvez obtenir plus d’informations sur l’objet. Vous pouvez également cliquer sur pour ajouter des attributs supplémentaires pour être visible dans la grille de **Définition de colonne** .

![Espace de connecteur de recherche](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Si toutes les suppressions sont souhaitées, puis effectuez les opérations suivantes :

1. Désactiver cette protection temporaire et de laisser ces suppressions passent, exécutez l’applet de commande PowerShell : `Disable-ADSyncExportDeletionThreshold`. Fournir un compte d’administrateur Global des annonces Azure et d’un mot de passe.
![Informations d’identification](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Avec Azure connecteur Active Directory étant toujours sélectionné, sélectionnez l’action à **exécuter** et sélectionnez **Exporter**.
3. Pour réactiver la protection, exécutez l’applet de commande PowerShell : `Enable-ADSyncExportDeletionThreshold`.

## <a name="next-steps"></a>Étapes suivantes

**Rubriques de vue d’ensemble**

- [Azure Connect de AD sync : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
