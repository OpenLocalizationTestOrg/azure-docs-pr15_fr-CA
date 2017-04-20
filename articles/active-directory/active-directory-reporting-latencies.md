<properties
   pageTitle="Azure Active Directory latences de création de rapports | Microsoft Azure"
   description="Quantité de temps que nécessaire pour les rapports d’événements s’afficheront dans Azure Active Directory"
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

# <a name="azure-active-directory-report-latencies"></a>Latences de rapport Azure Active Directory

*Cette documentation fait partie de l' [Azure Reporting Guide de Active Directory](active-directory-reporting-guide.md).*

Rapport                                                  | Minimum  | Moyenne    | Maximum
------------------------------------------------------- | -------- | ---------- | ----------
**Rapports de sécurité**                                    |          |            |
Activité de connexion irrégulière                              | 2 heures  | 4 heures    | 8 heures
Connexions provenant de sources inconnues                           | 2 heures  | 4 heures    | 8 heures
Après plusieurs échecs de connexions                        | 2 heures  | 4 heures    | 8 heures
Connexions de plusieurs régions                      | 2 heures  | 4 heures    | 8 heures
Connexions provenant d’adresses IP avec une activité suspecte     | 2 heures  | 4 heures    | 8 heures
Connexions à partir de périphériques probablement infectés                 | 2 heures  | 4 heures    | 8 heures
Utilisateurs avec l’activité de connexion anormale                   | 2 heures  | 4 heures    | 8 heures
Utilisateurs avec des informations d’identification perdues                           | 2 heures  | 4 heures    | 8 heures
**Rapports de l’application**                                 |          |            |
Activité de mise en service de compte                           | 2 heures  | 4 heures    | 8 heures
Erreurs de mise en service de compte                             | 2 heures  | 4 heures    | 8 heures
Utilisation de l’application                                       | 2 heures  | 4 heures    | 8 heures
État de survol de mot de passe                                | 2 heures  | 4 heures    | 8 heures
**Audit et rapports sur les activités**                            |          |            |
Rapport d’audit                                            | 1 minute | 15 minutes | 30 minutes
Activité (AD Azure) de réinitialisation de mot de passe                      | 2 heures  | 4 heures    | 8 heures
Activité (Identity Manager) de réinitialisation de mot de passe              | 2 heures  | 4 heures    | 8 heures
L’activité d’enregistrement (AD Azure) de réinitialisation de mot de passe         | 2 heures  | 4 heures    | 8 heures
L’activité d’enregistrement (Identity Manager) de réinitialisation de mot de passe | 2 heures  | 4 heures    | 8 heures
Activité de groupes (Azure AD) en libre service                 | 2 heures  | 4 heures    | 8 heures
Activité de groupes (Gestionnaire d’identités) en libre service         | 2 heures  | 4 heures    | 8 heures
**Rapports de RMS**                                         |          |            |
Utilisateurs les plus actifs RMS                                   | 2 heures  | 4 heures    | 8 heures
Utilisation de RMS                                               | 2 heures  | 4 heures    | 8 heures
Utilisation du périphérique RMS                                        | 2 heures  | 4 heures    | 8 heures
Utilisation des applications activées RMS                           | 2 heures  | 4 heures    | 8 heures
**Rapports d’aperçu privé**                             |          |            |
Toute activité de connexion de l’utilisateur                               | 2 heures  | 4 heures    | 8 heures
