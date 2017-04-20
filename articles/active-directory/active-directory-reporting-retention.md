<properties
    pageTitle="Stratégies de rétention de rapport Active Directory Azure | Microsoft Azure"
    description="Règles de rétention des données de rapport dans Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory rapport de rétention

*Cette documentation fait partie de l' [Azure Reporting Guide de Active Directory](active-directory-reporting-guide.md).*

Rapports d’Active Directory (AD Azure) Azure conservent les données pendant un certain nombre de jours indiqué ici.

Rapport                                                  | Description
------------------------------------------------------- | -----------
Connexions provenant de sources inconnues                           | 30 jours
Après plusieurs échecs de connexions                        | 30 jours
Connexions de plusieurs régions                      | 30 jours
Connexions provenant d’adresses IP avec une activité suspecte     | 30 jours
Connexions à partir de périphériques probablement infectés                 | 30 jours
Activité de connexion irrégulière                              | 30 jours
Utilisateurs avec l’activité de connexion anormale                   | 30 jours
Utilisateurs avec des informations d’identification perdues                           | 30 jours
Rapport d’audit                                            | 180 jours
Activité (AD Azure) de réinitialisation de mot de passe                      | 30 jours
Activité (Identity Manager) de réinitialisation de mot de passe              | 30 jours
L’activité d’enregistrement (AD Azure) de réinitialisation de mot de passe         | 30 jours
L’activité d’enregistrement (Identity Manager) de réinitialisation de mot de passe | 30 jours
Activité de groupes (Azure AD) en libre service                 | 30 jours
Activité de groupes (Gestionnaire d’identités) en libre service         | 30 jours
Utilisation de l’application                                       | 30 jours
Activité de mise en service de compte                           | 30 jours
État de survol de mot de passe                                | 30 jours
Erreurs de mise en service de compte                             | 30 jours
Utilisation de RMS                                               | 30 jours
Utilisateurs les plus actifs RMS                                   | 30 jours
Utilisation du périphérique RMS                                        | 30 jours
Utilisation des applications activées RMS                           | 30 jours
