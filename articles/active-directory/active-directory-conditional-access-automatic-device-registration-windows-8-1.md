<properties
    pageTitle="Configurer l’inscription automatique de périphérique pour les périphériques de joint au domaine Windows 8.1 | Microsoft Azure"
    description=" Étapes pour configurer la stratégie de groupe pour les périphériques à un domaine Windows 8.1 enregistrer automatiquement avec Azure AD. "
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
    ms.author="Markvi"/>

# <a name="configure-automatic-device-registration-for-windows-81-domain-joined-devices"></a>Configurer l’inscription automatique de périphérique pour les périphériques de joint au domaine Windows 8.1

Vous pouvez utiliser une stratégie de groupe Active Directory pour configurer vos périphériques de joint au domaine Windows 8.1 pour enregistrer automatiquement avec AD Azure. Pour configurer la stratégie de groupe, vous devez disposer d’au moins un ordinateur de Windows Server 2012 R2 ou Windows 8.1 de joints domaine avec la fonctionnalité de gestion des stratégies de groupe installée. Une fois cette stratégie de groupe est activée pour votre domaine, n’importe quel utilisateur de domaine qui se connecte à l’ordinateur sera automatiquement et silencieusement enregistré avec un objet périphérique dans Azure AD. Il y aura un objet de périphérique dans Azure AD pour chaque utilisateur enregistré du périphérique physique. Veillez à lire et les composants requis à partir de l’enregistrement automatique de périphérique avec dispositifs de Azure Active Directory for Windows Domain-Joined.

>[AZURE.NOTE]
 Pour des instructions plus récentes sur la configuration d’enregistrement de dispositif automatique, consultez [comment configurer l’enregistrement automatique du domaine Windows joint périphériques avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).



## <a name="configure-the-group-policy-for-your-windows-81-domain-joined-devices"></a>Configurer la stratégie de groupe pour vos périphériques de joint au domaine Windows 8.1

1. Ouvrez le Gestionnaire de serveur et affichez les **Outils** > **Gestion des stratégies de groupe**.
2. À partir de gestion des stratégies de groupe, naviguez vers le nœud de domaine qui correspond au domaine dans lequel vous souhaitez activer **Joindre de travail automatique**.
3. Cliquez sur **Objets de stratégie de groupe** et sélectionnez **Nouveau**. Nommez votre objet de stratégie de groupe, par exemple, **Joindre des lieux de travail automatique**. Cliquez sur **OK**.
4. Avec le bouton droit sur le nouvel objet de stratégie de groupe et sélectionnez **Modifier**.
5. Accédez au dossier **Configuration de l’ordinateur** > **stratégies** > **les modèles d’administration** > **composants Windows** > **Join de l’espace de travail**.
6. Cliquez sur automatiquement les ordinateurs clients de jointure poste de travail et sélectionnez **Modifier**.
7. Sélectionnez le bouton radio activé, puis sur Appliquer. Cliquez sur **OK**.
8. Vous pouvez maintenant lier l’objet stratégie de groupe à un emplacement de votre choix. Pour activer cette stratégie pour tous les périphériques Windows 8.1 joint au domaine de votre organisation, liez la stratégie de groupe pour le domaine.

## <a name="unregistering-your-windows-81-domain-joined-devices"></a>Annuler l’inscription de votre domaine Windows 8.1 joints périphériques

Vous pouvez choisir d’annuler l’inscription de vos périphériques de Windows 8.1 joint au domaine de la manière suivante : modifier les paramètres de stratégie de groupe de jointure poste de travail créés dans la section précédente. Définir l’automatiquement travail jointure ordinateurs stratégie client sur désactivé. Cela empêchera les nouveaux périphériques de rejoindre automatiquement de l’espace de travail.

Annuler l’inscription d’ordinateurs Windows 8.1 joint au domaine existants en suivant l’une des deux options suivantes :

* Option 1 : **périphérique à l’aide de paramètres PC Unregister un 8.1 Windows du domaine**
  1. Sur le périphérique Windows 8.1, accédez à **Paramètres PC** > **réseau** > **espace de travail**
  2. Sélectionnez **Quitter**.
Ce processus doit être répété pour chaque domaine de l’utilisateur qui est connecté à l’ordinateur et qui a été automatiquement joint un espace de travail.

* Option 2 : Annuler l’inscription d’un dispositif de joint domaine 8.1 de Windows à l’aide d’un script
    1. Ouvrez une invite de commandes sur l’ordinateur Windows 8.1 et exécutez la commande suivante :` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
Cette commande doit être exécutée dans le contexte de chaque utilisateur du domaine qui l’a signé à la machine.

##<a name="event-viewer--errors-for-windows-81-domain-joined-devices"></a>Périphériques de domaine joint de l’Observateur d’événements et erreurs pour Windows 8.1

Le journal des événements Windows sur une machine Windows 8.1 affiche des messages liés à l’enregistrement du périphérique. Vous trouverez des messages pour les événements réussis et infructueux. 

Le journal des événements vous pouvez trouver dans l’événement visionneuse sous les Services, **les journaux**des Applications et > **Microsoft** > **Windows > joindre de l’espace de travail**.

##<a name="additional-details"></a>Détails supplémentaires

La stratégie de groupe permet à une tâche planifiée sur le système qui s’exécute dans le contexte de l’utilisateur et qui est déclenché sur la connexion de l’utilisateur. La tâche inscrira silencieusement l’utilisateur et un dispositif à Azure annonce une fois que la connexion est terminée. La tâche planifiée, vous pouvez trouver sur les périphériques Windows 8.1 dans la bibliothèque du Planificateur de tâches sous **Microsoft** > **Windows** > **Join de l’espace de travail**. La tâche sera exécutée et inscrire tous les utilisateurs d’Active Directory que vous connecter à. 

## <a name="additional-topics"></a>Rubriques supplémentaires
- [Vue d’ensemble de l’enregistrement de Active Directory périphérique Azure](active-directory-conditional-access-device-registration-overview.md)
- [Enregistrement de dispositif automatique avec les périphériques à un domaine d’Azure Active Directory pour Windows 10](active-directory-conditional-access-automatic-device-registration.md)
- [Configurer l’inscription automatique pour appareils de joint au domaine Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)

