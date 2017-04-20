<properties
    pageTitle="Hybride, identité : Intégration d’annuaire outils de comparaison | Microsoft Azure"
    description="Cette page fournit une table complète qui compare les différents outils d’intégration de répertoire qui peuvent être utilisés pour l’intégration d’annuaire."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Comparaison des outils d’intégration hybride identité directory

Au fil des années les outils d’intégration de répertoire ont développé et a évolué.  Ce document est d’aider à fournir une vue consolidée de ces outils et une comparaison des fonctionnalités qui sont disponibles dans chacun.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Azure Connect de publicité incorpore les composants publiés précédemment sous forme de synchronisation d’annuaire et de synchronisation des DAS. Ces outils n’est plus mis sur le marché individuellement, et toutes les futures améliorations figureront dans les mises à jour Azure Connect d’Active Directory, afin que vous sachiez toujours où obtenir les plus récentes fonctionnalités.
>
>Synchronisation d’annuaire et Azure AD Sync sont déconseillées. Vous trouverez plus d’informations dans [ici](active-directory-aadconnect-dirsync-deprecated.md).


Utilisez la clé suivante pour chacune des tables.

● = Disponible maintenant  
FR = version Future  
PP = version d’évaluation  

## <a name="on-premises-to-cloud-synchronization"></a>Sur le site vers le Cloud de synchronisation

| Fonctionnalité  | Azure Active Directory se connecter  | Azure Active Directory synchronisation Services (synchronisation DAS) | Outil de synchronisation d’annuaire Azure (DirSync)| Forefront Identity Manager 2010 R2 (FIM) |Gestionnaire d’identité Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Se connecter à la même forêt Active Directory sur site | ● | ● | ● | ● |● |
| Se connecter à plusieurs forêts d’Active Directory sur site |●  | ● |  | ● |● |
| Se connecter à plusieurs Orgs de Exchange sur site | ● |  |  |  | |
| Se connecter au service d’annuaire LDAP sur site unique | FR |  |  | ● |● |
| Se connecter à plusieurs annuaires sur site |FR  |  |  | ● |● |
| Se connecter à Active Directory sur site et annuaires LDAP sur site |FR  |  |  | ● |● |
| Se connecter à des systèmes personnalisés (par exemple, SQL, Oracle, MySQL, etc.) | FR |  |  | ● |● |
| Synchroniser les attributs définies par le client (extensions de répertoire) | ● |  |  |  |  |
|Se connecter à local HR (par ex., SAP, Oracle e-Business, PeopleSoft)| FR |  |  | ● |● |
|Prend en charge les règles de synchronisation FIM et connecteurs pour la mise en service sur les systèmes locaux.|  |  |  | ● |● |

## <a name="cloud-to-on-premises-synchronization"></a>Nuage pour la synchronisation locale

| Fonctionnalité  | Azure Active Directory se connecter  | Services de synchronisation d’annuaire Azure | Outil de synchronisation d’annuaire Azure (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Gestionnaire d’identité Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Écriture différée de périphériques | ● |  | ● |  ||
| Attribut d’écriture différée (pour le déploiement hybride d’Exchange) | ● | ● | ● | ● |● |
| Écriture différée des objets utilisateurs et groupes |  ●|  | |  ||
| Écriture différée des mots de passe (à partir de réinitialisation de mot de passe libre-service (SSPR) et le mot de passe) |  ● | ● |  |  ||



## <a name="authentication-feature-support"></a>Prise en charge de la fonctionnalité d’authentification

| Fonctionnalité  | Azure Active Directory se connecter | Services de synchronisation d’annuaire Azure | Outil de synchronisation d’annuaire Azure (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Gestionnaire d’identité Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Synchronisation de mot de passe pour une seule forêt Active Directory sur site | ● | ● | ● |  ||
| Synchronisation de mot de passe pour plusieurs forêts d’Active Directory sur site |  ●| ● |  |  ||
| Ouverture de session unique avec la fédération | ● | ● | ● | ● |● |
| Écriture différée des mots de passe (de modification SSPR et mot de passe) |●  | ● |  |  ||



## <a name="set-up-and-installation"></a>Configuration et Installation

| Fonctionnalité  | Azure Active Directory se connecter  | Services de synchronisation d’annuaire Azure | Outil de synchronisation d’annuaire Azure (DirSync) | Gestionnaire d’identité Microsoft 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| En charge l’installation sur un contrôleur de domaine | ● | ● | ● |  |
| En charge l’installation à l’aide de SQL Express | ● | ● | ● |  |
| Mise à niveau à partir de la synchronisation d’annuaire |● |  |  |  |
| Localisation de Admin UX pour les langues de Windows Server | ● | ● | ● |  |
|Localisation de l’utilisateur final UX pour les langues de Windows Server| |  |  |● |
| Prise en charge de Windows Server 2008 et Windows Server 2008 R2 | ● pour la synchronisation, non pour la fédération| ● | ●  | ● |
| Prise en charge de Windows Server 2012 et Windows Server 2012 R2 | ● | ● | ● | ● |

## <a name="filtering-and-configuration"></a>Filtrage et Configuration

Fonctionnalité  | Azure Active Directory se connecter | Services de synchronisation d’annuaire Azure | Outil de synchronisation d’annuaire Azure (DirSync) | Forefront Identity Manager 2010 R2 (FIM)| Gestionnaire d’identité Microsoft 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Filtrer sur les domaines et les unités d’organisation | ● | ● | ● | ●  | ●
Filtrer sur les valeurs d’attribut des objets | ● | ● | ● | ●| ●
Autoriser un ensemble minimal d’attributs à synchroniser (MinSync) | ● | ● |  ||
Autoriser les modèles de service différent à appliquer pour les flux d’attributs |●  | ● |  ||
Autoriser la suppression des attributs de transiter depuis Active Directory vers Azure AD | ● | ● |  |  |
Autoriser la personnalisation avancée de flux d’attributs | ● | ● |  | ●  | ●

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
