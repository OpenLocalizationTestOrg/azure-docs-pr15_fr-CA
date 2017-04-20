<properties
    pageTitle="Annonce Azure se connecter à l’historique de Version de la santé"
    description="Ce document décrit les versions de la santé se connecter Azure AD et ce qui a été inclus dans ces versions."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-version-release-history"></a>AD Azure se connecter la santé : Historique de publication de Version

L’équipe Azure Active Directory met régulièrement à jour la santé Azure AD se connecter avec de nouvelles fonctionnalités. Cet article répertorie les versions et les fonctionnalités qui ont été publiées.

## <a name="october-2016"></a>Octobre 2016
**Mise à jour de l’agent :**
- Agent de santé de connexion AD Azure pour AD FS \(version 2.6.408.0\)
    1. Améliorations dans la détection des adresses IP des clients dans les demandes d’authentification
    2. Correctifs de bogues liés aux alertes
- Agent de santé de connexion AD Azure pour AD DS (version 2.6.408.0)
    1. Correctifs liés aux alertes.
- Agent de santé de connexion AD Azure pour la synchronisation (version 2.6.353.0) fournie avec Azure AD Connect version 1.1.281.0
    1. Fournir les données requises pour les rapports d’erreur de synchronisation
    2. Correctifs de bogues liés aux alertes

**Nouvelles fonctionnalités d’aperçu :**
- Rapports d’erreurs de synchronisation pour Active Directory Azure se connecter

**Nouvelles fonctionnalités :**
- Azure santé AD se connecter pour AD FS - champ adresse IP n’est disponible dans le rapport sur les 50 utilisateurs avec nom d’utilisateur/mot de passe incorrect.

## <a name="july-2016"></a>Juillet 2016

**Nouvelles fonctionnalités d’aperçu :**

- [Annonce azure se connecter la santé pour les services AD DS](active-directory-aadconnect-health-adds.md).


## <a name="january-2016"></a>Janvier 2016


**Mise à jour de l’agent :**

- Agent de santé de connexion AD Azure pour AD FS (version 2.6.91.1512)


**Nouvelles fonctionnalités :**

- [Outil de test de connectivité pour Azure AD connecter les Agents d’intégrité](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## <a name="november-2015"></a>Novembre 2015


**Nouvelles fonctionnalités :**

- Prise en charge de [contrôle d’accès basée sur les rôles](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**Nouvelles fonctionnalités d’aperçu :**

- [Azure AD connecter la santé pour la synchronisation](active-directory-aadconnect-health-sync.md).

**Problèmes résolus :**

- Correctifs pour les erreurs rencontrées pendant les enregistrements de l’agent.

## <a name="september-2015"></a>Septembre 2015

**Nouvelles fonctionnalités :**

- Faux rapport de mot de passe nom d’utilisateur pour AD FS
- Prise en charge pour configurer les Proxy HTTP non authentifié
- Prise en charge de la configuration de l’agent sur Server core
- Améliorations apportées aux alertes pour AD FS
- Améliorations dans Azure Agent de santé se connecter AD pour AD FS pour la connectivité et les données de téléchargement.


**Problèmes résolus :**

- Correctifs de bogues dans les perspectives d’utilisation pour les types d’erreur de AD FS.


## <a name="june-2015"></a>Juin 2015

**Version initiale de la santé se connecter Azure AD pour AD FS et AD FS Proxy.**

**Nouvelles fonctionnalités :**

- Alertes de surveillance des serveurs ADFS et AD FS Proxy avec les notifications par courrier électronique.
- Accès facile à la topologie d’ADFS et les modèles dans les compteurs de Performance AD FS.
- Tendances des demandes de jeton réussie sur les serveurs ADFS regroupés par les Applications, les méthodes d’authentification, etc. d’emplacement réseau demande.
- L’évolution des demandes qui ont échoué sur des serveurs ADFS regroupés par des Applications, etc. de Types d’erreur.
- Déploiement de l’Agent à l’aide des informations d’identification d’un administrateur Global AD Azure plus simple.  




## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les [services d’infrastructure et de la synchronisation d’identité dans le nuage moniteur vos locaux](active-directory-aadconnect-health.md).
