<properties
    pageTitle="# Configurer l’inscription automatique pour appareils de joint au domaine Windows 7 | Microsoft Azure"
    description="Étapes à suivre pour configurer votre domaine Windows 7 rejoint les périphériques pour enregistrer automatiquement avec Azure AD. et les étapes nécessaires pour déployer le package de logiciel de périphérique d’enregistrement à votre domaine Windows 7 joint des périphériques à l’aide d’un système de distribution de logiciels tels que System Center Configuration Manager."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="MarkVi"/>

# <a name="configure-automatic-device-registration-for-windows-7-domain-joined-devices"></a>Configurer l’inscription automatique pour appareils de joint au domaine Windows 7

Un administrateur informatique, vous pouvez configurer vos périphériques de joint au domaine Windows 7 pour enregistrer automatiquement avec AD Azure. Pour ce faire, vous devez déployer le package de logiciel de périphérique d’enregistrement à votre domaine Windows 7 joints périphériques à l’aide d’un système de distribution de logiciels tels que System Center Configuration Manager. Veillez à lire et les composants requis répertoriés dans l’enregistrement automatique de périphérique avec les périphériques d’Azure Active Directory pour Windows à un domaine.

>[AZURE.NOTE]
 Pour des instructions plus récentes sur la configuration d’enregistrement de dispositif automatique, consultez [comment configurer l’enregistrement automatique du domaine Windows joint périphériques avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

##<a name="installing-the-device-registration-software-package-on-windows-7-domain-joined-devices"></a>Installez le package de logiciel de périphérique d’enregistrement sous Windows 7 domaine périphériques

Enregistrement de dispositif pour Windows 7 est disponible en tant que [package téléchargeable de MSI](https://connect.microsoft.com/site1164). Le package doit être installé sur les ordinateurs Windows 7 qui sont rattachés à un domaine Active Directory. Vous devez déployer le package à l’aide d’un système de distribution de logiciels tels que System Center Configuration Manager. Le package MSI prend en charge les options standard d’installation silencieuse à l’aide de /quiet paramètre.
Le logiciel est disponible pour téléchargement sur le [site Web Microsoft Connect](https://connect.microsoft.com/site1164). Ici, vous pouvez sélectionner et télécharger puis de jointure poste de travail pour Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## <a name="workplace-join-with-azure-active-directory"></a>Jointure poste de travail avec Azure Active Directory
Inscription de périphérique pour les périphériques de joint au domaine Windows 7 ne requièrent ni incluent une interface utilisateur. Une fois installé sur l’ordinateur, n’importe quel utilisateur de domaine qui se connecte à l’ordinateur sera automatiquement et silencieusement enregistré avec un objet périphérique dans Azure AD. Il y aura un objet de périphérique dans Azure AD pour chaque utilisateur enregistré du périphérique physique.

Le programme d’installation crée une tâche planifiée sur le système qui s’exécute dans le contexte de l’utilisateur et est déclenché sur la connexion de l’utilisateur. La tâche enregistre silencieusement l’utilisateur et dispositif avec AD Azure après les signes de l’utilisateur est terminée.
La tâche planifiée peut être trouvée dans la bibliothèque du Planificateur de tâches sous **Microsoft** > **Join de l’espace de travail**.
La tâche sera exécutée et inscrire tous les utilisateurs Active Directory que vous ouvrez une session dans l’ordinateur.
L’illustration suivante affiche le processus pas à pas pour l’enregistrement de dispositifs automatiques.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. Un utilisateur (travailleur de l’information) ouvre une session sur un ordinateur client de Windows 7 à l’aide des informations d’identification de domaine Active Directory.
1. La jointure de travail tâche planifiée est exécutée.
1. L’utilisateur est authentifié en mode silencieux avec AD FS à l’aide de l’authentification intégrée de Windows.
1. Le PC Windows 7 est inscrit à l’utilisateur dans Active Directory Azure.
1. Un objet périphérique et un certificat est créé dans AD Azure. L’objet représente le user@device.
1. Le certificat de la jointure de l’espace de travail est stocké sur l’ordinateur.

## <a name="unregistering-your-windows-7-domain-joined-devices"></a>Annuler l’inscription de votre domaine Windows 7 joints périphériques

Vous pouvez choisir d’annuler l’inscription de vos périphériques de Windows 7 joint au domaine de la manière suivante : Désinstallez la jointure de l’espace de travail logiciel à partir de votre domaine Windows 7 joints périphériques à l’aide d’un système de distribution de logiciels tels que System Center Configuration Manager.

Ensuite, ouvrez une invite de commandes sur l’ordinateur Windows 7 et exécutez la commande suivante pour annuler l’inscription de l’appareil :

    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
>Cette commande doit être exécutée dans le contexte de chaque utilisateur du domaine qui l’a signé à la machine.
Périphériques domaine joint de l’Observateur d’événements et erreurs pour Windows 7.

Le journal des événements Windows sur l’ordinateur Windows 7 affiche les messages liés à la jointure de l’espace de travail. Vous pouvez rechercher des messages pour les événements de joindre du poste de travail à la fois les réussites et les échecs. Le journal des événements vous pouvez trouver dans l’événement visionneuse sous journaux des Applications et Services > joindre de Microsoft-lieu de travail.

## <a name="additional-topics"></a>Rubriques supplémentaires

- [Vue d’ensemble de l’enregistrement de Active Directory périphérique Azure](active-directory-conditional-access-device-registration-overview.md)
- [Enregistrement de dispositif automatique avec les périphériques Azure Active Directory for Windows Domain-Joined](active-directory-conditional-access-automatic-device-registration.md)
- [Configurer l’inscription automatique de périphérique pour les périphériques de joint au domaine Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Enregistrement de dispositif automatique avec les périphériques à un domaine d’Azure Active Directory pour Windows 10](active-directory-azureadjoin-devices-group-policy.md)
