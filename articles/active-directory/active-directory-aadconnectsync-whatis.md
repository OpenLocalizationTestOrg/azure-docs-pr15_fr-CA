<properties
    pageTitle="Azure Connect de AD sync : comprendre et personnaliser la synchronisation | Microsoft Azure"
    description="Explique comment Azure Connect d’Active Directory synchronisation de works et la personnalisation."
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
    ms.date="08/29/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure Connect de AD sync : comprendre et personnaliser la synchronisation
Les services de synchronisation Azure Active Directory se connecter (Azure AD connexion synchronisation) est un composant principal d’Azure Connect d’Active Directory. Il prend en charge toutes les opérations liées à synchroniser les données d’identité entre votre environnement local et la publicité Azure. Azure Connect de AD sync est le successeur de Forefront Identity Manager avec Azure Active Directory Connector configuré, Azure AD Sync et de synchronisation d’annuaire.

Cette rubrique est la page d’accueil pour la **synchronisation d’Azure Connect de publicité** (également appelé **moteur de synchronisation**) et répertorie les liens vers toutes les autres rubriques qui lui sont associés. Les liens vers Azure Connect d’Active Directory, voir [intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).

Le service de synchronisation se compose de deux composants, le composant de **synchronisation d’Azure Connect de publicité** sur site et le côté service dans Azure AD appelé **service de synchronisation d’Azure Connect d’Active Directory**. Le service est commun pour la synchronisation d’annuaire, Azure AD Sync et Azure Connect d’Active Directory.

## <a name="azure-ad-connect-sync-topics"></a>Rubriques de synchronisation AD Connect Azure

Rubrique | Ce qu’il couvre et le moment de lire
----- | -----
**Principes de base de synchronisation AD Connect Azure** |
[Présentation de l’architecture](active-directory-aadconnectsync-understanding-architecture.md) | Pour ceux d'entre vous qui sont nouvelles dans le moteur de synchronisation et qui souhaitent en savoir plus sur l’architecture et les termes utilisés.
[Concepts techniques](active-directory-aadconnectsync-technical-concepts.md) | Une version courte de la rubrique architecture et brièvement décrit les termes utilisés.
[Topologies de publicité Azure se connecter](active-directory-aadconnect-topologies.md) | Décrit les différentes topologies et le moteur de synchronisation prend en charge des scénarios.
**Configuration personnalisée** |
[Relancer l’Assistant d’installation](active-directory-aadconnectsync-installation-wizard.md) | Explique les options que vous avez disponibles lorsque vous réexécutez l’Assistant d’installation Azure Connect d’AD.
[Présentation de provisionnement déclarative](active-directory-aadconnectsync-understanding-declarative-provisioning.md)| Décrit le modèle de configuration appelé approvisionnement déclarative.
[Comprendre la mise en service des Expressions déclaratives](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Décrit la syntaxe de la langue de l’expression utilisée dans le provisionnement déclarative.
[Présentation de la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md)| Décrit les règles d’out-of-box et la configuration par défaut. Décrit également comment les règles fonctionnent ensemble pour les scénarios d’out-of-box fonctionner.
[Compréhension des utilisateurs et contacts](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Continue dans la rubrique précédente et décrit comment la configuration pour les utilisateurs et les contacts fonctionne ensemble, en particulier dans un environnement à plusieurs forêts.
[Comment faire pour apporter une modification à la configuration par défaut](active-directory-aadconnectsync-change-the-configuration.md) | Décrit comment modifier une configuration commune pour les flux d’attributs.
[Méthodes conseillées pour la modification de la configuration par défaut](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Prise en charge des limitations et pour apporter des modifications à la configuration prête à l’emploi.
[Configurer le filtrage](active-directory-aadconnectsync-configure-filtering.md) | Décrit les différentes options pour savoir comment limiter les objets qui sont en cours synchronisées avec Active Directory Azure et étape par étape comment configurer ces options.
**Scénarios et fonctionnalités** |
[Empêcher des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Décrit la fonctionnalité *d’empêcher des suppressions accidentelles* et comment le configurer.
[Planificateur](active-directory-aadconnectsync-feature-scheduler.md) | Décrit le Planificateur intégré, ce qui est important, la synchronisation et exportation de données.
[Implémenter la synchronisation de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) | Décrit comment fonctionne la synchronisation de mot de passe, la mise en œuvre et à exploiter et à résoudre les problèmes.
[Écriture différée de périphérique](active-directory-aadconnect-feature-device-writeback.md) | Décrit le fonctionne de l’écriture différée du périphérique dans Azure Connect d’AD.
[Extensions de répertoire](active-directory-aadconnectsync-feature-directory-extensions.md) | Explique comment étendre le schéma Active Directory Azure avec vos propres attributs personnalisés.
**Service de synchronisation** |
[Fonctionnalités de service de synchronisation AD Connect Azure](active-directory-aadconnectsyncservice-features.md) | Décrit le côté service de synchronisation et comment modifier les paramètres de synchronisation dans Azure AD.
[Résilience d’attribut en double](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) | Décrit comment activer et utiliser **userPrincipalName** et **proxyAddresses** résilience de valeurs d’attribut en double.
**Opérations et interface utilisateur** |
[Gestionnaire de Service de synchronisation](active-directory-aadconnectsync-service-manager-ui.md) | Décrit le Gestionnaire de Service de synchronisation UI, y compris les [opérations](active-directory-aadconnectsync-service-manager-ui-operations.md), les [connecteurs](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)et onglets de [Recherche](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) .
[Considérations et tâches opérationnelles](active-directory-aadconnectsync-operations.md) | Décrit des problèmes opérationnels, tels que de la reprise après sinistre.
**Comment...** |
[Réinitialiser le compte Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) | Comment réinitialiser les informations d’identification du compte de service utilisé pour la connexion de synchronisation d’Azure Connect de publicité pour Azure AD.
**Plus d’informations et références** |
[Ports](active-directory-aadconnect-ports.md) | Répertorie les ports que vous devez ouvrir entre le moteur de synchronisation et que votre les répertoires locaux et Azure AD.
[Les attributs synchronisés vers Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | Répertorie tous les attributs en cours de synchronisation entre les locaux AD et AD Azure.
[Référence des fonctions](active-directory-aadconnectsync-functions-reference.md) | Répertorie toutes les fonctions disponibles dans la mise en service déclarative.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
