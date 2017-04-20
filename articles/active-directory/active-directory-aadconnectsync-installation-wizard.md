<properties
    pageTitle="Azure Connect de AD sync : exécution de l’Assistant d’installation une deuxième fois | Microsoft Azure"
    description="Explique le fonctionne de l’Assistant installation de la deuxième fois que vous exécutez."
    keywords="L’Assistant d’installation Azure Connect d’Active Directory vous permet de configurer les paramètres de maintenance la deuxième fois que vous l’exécutez"
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


# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure Connect de AD sync : exécution de l’Assistant d’installation une deuxième fois
La première fois que vous exécutez l’Assistant installation Azure Connect d’Active Directory, il vous guide dans la configuration de votre installation. Si vous réexécutez l’Assistant d’installation, il offre des options pour la maintenance.

Vous pouvez trouver l’Assistant d’installation dans le menu Démarrer nommé **Azure Connect d’Active Directory**.

![Menu Démarrer](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Lorsque vous démarrez l’Assistant d’installation, vous voyez une page avec ces options :

![Page avec une liste des tâches supplémentaires](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Si vous avez installé ADFS avec Azure Connect d’Active Directory, vous avez plus d’options. Les options supplémentaires que vous avez pour ADFS sont documentées dans la [Gestion d’ADFS](active-directory-aadconnect-federation-management.md#ad-fs-management).

Sélectionnez une des tâches, puis cliquez sur **suivant** pour continuer.

> [AZURE.IMPORTANT] Tant que l’Assistant installation ouvert, toutes les opérations dans le moteur de synchronisation sont suspendues. Assurez-vous que vous fermez l’Assistant installation, dès que vous avez terminé vos modifications de configuration.

## <a name="view-current-configuration"></a>Afficher la configuration en cours
Cette option vous donne un aperçu rapide de vos options actuellement configurées.

![Page avec une liste de toutes les options et leur état](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Cliquez sur **précédent** pour revenir en arrière. Si vous sélectionnez **Quitter**, vous fermez l’Assistant d’installation.

## <a name="customize-synchronization-options"></a>Personnaliser les options de synchronisation
Cette option est utilisée pour apporter des modifications à la configuration de la synchronisation. Vous consultez un sous-ensemble des options à partir du chemin d’installation de configuration personnalisée. Vous voyez cette option même si vous avez utilisé une installation expresse initialement.

- [Ajouter plus de répertoires](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Pour la suppression d’un répertoire, voir [suppression d’un connecteur](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
- [Changer de domaine et le filtrage de l’unité d’organisation](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
- Supprimer le groupe de filtrage.
- [Options de modification](active-directory-aadconnect-get-started-custom.md#optional-features).

Les autres options à partir de l’installation initiale ne sont pas modifiables et ne sont pas disponibles. Ces options sont les suivantes :

- Modifiez l’attribut à utiliser pour userPrincipalName et sourceAnchor.
- Modifier la méthode de jonction pour les objets à partir de l’autre forêt.
- Activer le filtrage basé sur le groupe.

## <a name="refresh-directory-schema"></a>Actualiser le schéma de l’annuaire
Cette option est utilisée si vous avez modifié le schéma dans un de vos locaux sur les forêts AD DS. Par exemple, vous avez peut-être installé Exchange ou mis à niveau vers un schéma de Windows Server 2012 avec les objets du périphérique. Dans ce cas, vous devez indiquer à Azure Connect annonce à nouveau de lire le schéma AD DS et de mettre à jour son cache. Cette opération régénère également les règles de synchronisation. Si vous ajoutez le schéma Exchange, par exemple, les règles de synchronisation pour Exchange sont ajoutés à la configuration.

Lorsque vous sélectionnez cette option, tous les répertoires de votre configuration sont répertoriés. Vous pouvez conserver le paramètre par défaut et actualiser toutes les forêts ou désélectionner certains d'entre eux.

![Page avec une liste de tous les répertoires dans l’environnement](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurer le mode de mise en attente
Cette option vous permet d’activer et de désactiver le mode de mise en attente sur le serveur. Vous trouverez plus d’informations sur la mise en attente du mode et la manière dont il est utilisé dans les [opérations](active-directory-aadconnectsync-operations.md#staging-mode).

L’option indique si la mise en attente est actuellement activé ou désactivé :  
![Option qui affiche également l’état actuel du mode de mise en attente](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Pour modifier l’état, sélectionnez cette option et sélectionnez ou désélectionnez la case à cocher.  
![Option qui affiche également l’état actuel du mode de mise en attente](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Modifier la connexion de l’utilisateur
Cette option vous permet de modifier à partir de la synchronisation de mot de passe pour la fédération ou l’inverse. Vous ne pouvez pas modifier à **n’est pas configuré**.

Pour plus d’informations sur cette option, reportez-vous à la section [authentification de l’utilisateur](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le modèle de configuration utilisé par synchronisation Azure Connect de publicité dans la [Compréhension de la mise en service déclarative](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Rubriques de vue d’ensemble**

- [Azure Connect de AD sync : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
