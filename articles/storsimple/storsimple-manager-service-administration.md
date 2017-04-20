<properties 
   pageTitle="Administration du service Gestionnaire de StorSimple | Microsoft Azure"
   description="Apprenez à gérer votre périphérique StorSimple en utilisant le service Gestionnaire de StorSimple dans le portail classique Azure."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>Le service StorSimple Manager permet d’administrer votre périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article décrit l’interface de service Gestionnaire de StorSimple, y compris comment se connecter à, les diverses options disponibles et à des liens vers des workflows spécifiques qui peuvent être effectuées via cette interface utilisateur. Ce guide s’applique à la fois ; StorSimple physiques et le périphérique virtuel.

Après avoir lu cet article, vous apprendrez à :

- Se connecter au service Gestionnaire de StorSimple
- Naviguer dans l’interface utilisateur Gestionnaire de StorSimple
- Administrer votre périphérique StorSimple via le service Gestionnaire de StorSimple


## <a name="connect-to-storsimple-manager-service"></a>Se connecter au service Gestionnaire de StorSimple

Le service Gestionnaire de StorSimple s’exécute dans Microsoft Azure et se connecte à plusieurs périphériques StorSimple. Vous utilisez un portail classique Microsoft Azure central s’exécutant dans un navigateur pour gérer ces périphériques. Pour vous connecter au service Gestionnaire de StorSimple, effectuez les opérations suivantes.

#### <a name="to-connect-to-the-service"></a>Pour vous connecter au service

1. Accédez à [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

1. À l’aide de vos informations d’identification de compte Microsoft, connectez-vous au portail Microsoft Azure classique (situé en haut à droite du volet).

1. Faites défiler le volet de navigation de gauche pour accéder au service Gestionnaire de StorSimple.


## <a name="navigate-storsimple-manager-service-ui"></a>Gestionnaire de StorSimple service interface utilisateur de navigation

La hiérarchie de navigation pour le service Gestionnaire de StorSimple l’interface utilisateur est indiqué dans le tableau suivant.

- Page d’accueil **StorSimple Manager** permet d’atteindre les pages de niveau de service de l’interface utilisateur applicables à tous les périphériques au sein d’un service.

- Page **de périphériques** permet d’atteindre les pages de l’interface utilisateur au niveau du périphérique applicables à un périphérique spécifique.

- Page de **Conteneurs de volume** vous amène à la page de volume qui affiche tous les volumes associés à un périphérique.


#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Hiérarchie de navigation de service Gestionnaire de StorSimple

|Page d’accueil|Pages de niveau de service|Pages au niveau du périphérique|Pages au niveau du périphérique|
|---|---|---|---|
|Service Gestionnaire de StorSimple|Tableau de bord de service|Tableau de bord de périphérique||
||Périphériques →|Moniteur|
||Catalogue de sauvegarde|Volume containers→|Volumes|
||Configurer (Service)|Stratégies de sauvegarde||
||Travaux|Configurer (périphérique)|
||Alertes|Gestion des|

![Vidéo disponible](./media/storsimple-manager-service-administration/Video_icon.png) **vidéo disponible**

Pour regarder une vidéo qui vous guide tout au long de l’interface utilisateur du service Gestionnaire de StorSimple, cliquez [ici](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>Administrer le dispositif de StorSimple à l’aide du service Gestionnaire de StorSimple

Le tableau suivant affiche un résumé de toutes les tâches de gestion courantes et des workflows complexes qui peuvent être effectuées dans le service de gestionnaire de StorSimple l’interface utilisateur. Ces tâches sont classées selon les pages de l’interface utilisateur sur lequel elles sont lancées.

Pour plus d’informations sur chaque flux de travail, cliquez sur la procédure appropriée dans le tableau.

#### <a name="storsimple-manager-workflows"></a>StorSimple Gestionnaire de flux de travail

|Si vous souhaitez le faire...|Accédez à cette page de l’interface utilisateur...|Utilisez cette procédure.|
|---|---|---|
|Création d’un service</br>Supprimer un service</br>Obtenir la clé d’enregistrement de service</br>Régénérer la clé d’enregistrement de service|Service Gestionnaire de StorSimple|[Déployer un service Gestionnaire de StorSimple](storsimple-manage-service.md)
|Modifier la clé de cryptage de données de service</br>Afficher les journaux d’opération|Tableau de bord StorSimple Manager service →|[Utilisez le tableau de bord de service Gestionnaire de StorSimple](storsimple-service-dashboard.md)|
|Désactiver un périphérique</br>Supprimer un périphérique|Gestionnaire de StorSimple service → périphériques|[Désactiver ou supprimer un périphérique](storsimple-deactivate-and-delete-device.md)|
|Informez-vous sur le basculement de périphérique et de la récupération après sinistre</br>Basculement vers un périphérique physique</br>Basculement vers un périphérique virtuel</br>Continuité d’activité reprise (BCDR)|Gestionnaire de StorSimple service → périphériques|[Basculement et reprise après sinistre de votre périphérique StorSimple](storsimple-device-failover-disaster-recovery.md)|
|Liste des sauvegardes d’un volume</br>Sélectionnez un jeu de sauvegarde</br>Supprimer un jeu de sauvegarde|Catalogue de sauvegarde StorSimple Manager service →|[Gestion des sauvegardes](storsimple-manage-backup-catalog.md)|
|Clonage d’un volume|Catalogue de sauvegarde StorSimple Manager service →|[Clonage d’un volume](storsimple-clone-volume.md)|
|Restauration d’un jeu de sauvegarde|Catalogue de sauvegarde StorSimple Manager service →|[Restauration d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md)|
|À propos des comptes de stockage</br>Ajouter un compte de stockage</br>Modifier un compte de stockage</br>Supprimer un compte de stockage</br>Rotation des clés des comptes de stockage|Gestionnaire de StorSimple service → configurer|[Gérer les comptes de stockage](storsimple-manage-storage-accounts.md)|
|À propos des modèles de la bande passante</br>Ajouter un modèle de la bande passante</br>Modifier un modèle de la bande passante</br>Supprimer un modèle de la bande passante</br>Utiliser un modèle de la bande passante par défaut</br>Créer un modèle de la bande passante sur une journée entière qui démarre à une heure spécifiée|Gestionnaire de StorSimple service → configurer|[Gérer les modèles de la bande passante](storsimple-manage-bandwidth-templates.md)|
|À propos des enregistrements de contrôle d’accès</br>Créer un enregistrement de contrôle d’accès</br>Modification d’un enregistrement de contrôle d’accès</br>Supprimer un enregistrement de contrôle d’accès|Gestionnaire de StorSimple service → configurer|[Gérer les enregistrements de contrôle d’accès](storsimple-manage-acrs.md)|
|Afficher les détails du projet</br>Annulation d’une tâche|Gestionnaire de StorSimple service → travaux|[Gérer les travaux](storsimple-manage-jobs.md)
|Recevoir des notifications d’alerte</br>Gérer les alertes</br>Consulter les alertes|Gestionnaire de StorSimple service → alertes|[Permet d’afficher et de gérer les alertes de StorSimple](storsimple-manage-alerts.md)
|Afficher des initiateurs connectés</br>Rechercher le numéro de série du périphérique</br>Rechercher la nom qualifié de la cible|Gestionnaire de StorSimple service → périphériques → tableau de bord|[Utilisez le panneau de commandes de périphérique StorSimple](storsimple-device-dashboard.md)|
|Créer des graphiques d’analyse|Gestionnaire de StorSimple service → périphériques → surveiller|[Analyser votre périphérique StorSimple](storsimple-monitor-device.md)|
|Ajouter un conteneur de volume</br>Modifier un conteneur de volume</br>Supprimer un conteneur de volume|StorSimple Manager service → périphériques → Volume conteneurs|[Gérer des conteneurs de volume](storsimple-manage-volume-containers.md)|
|Ajouter un volume</br>Modifier un volume</br>Prélever un volume hors connexion</br>Supprimer un volume</br>Analyser un volume|Volumes StorSimple Manager service → périphériques → Volume conteneurs →|[Gérer les volumes](storsimple-manage-volumes.md)|
|Modifier les paramètres de périphérique</br>Modifier les paramètres de temps</br>Modifier les paramètres de DNS.md</br>Configurer les interfaces réseau|Gestionnaire de StorSimple service → périphériques → configurer|[Modifier la configuration des périphériques de votre périphérique StorSimple](storsimple-modify-device-config.md)|
|Afficher les paramètres de proxy web|Gestionnaire de StorSimple service → périphériques → configurer|[Configurer le proxy web pour votre périphérique](storsimple-configure-web-proxy.md)|
|Modifier le mot de passe administrateur</br>Modifier le mot de passe StorSimple Gestionnaire de snapshots|Gestionnaire de StorSimple service → périphériques → configurer|[Modifier les mots de passe StorSimple](storsimple-change-passwords.md)|
|Configurer la gestion à distance|Gestionnaire de StorSimple service → périphériques → configurer|[Se connecter à distance à votre périphérique de StorSimple](storsimple-remote-connect.md)|
|Configurer les paramètres d’alerte|Gestionnaire de StorSimple service → périphériques → configurer|[Permet d’afficher et de gérer les alertes de StorSimple](storsimple-manage-alerts.md)|
|Configurer la sécurité CHAP pour votre périphérique StorSimple|Gestionnaire de StorSimple service → périphériques → configurer|[Configurer la sécurité CHAP pour votre périphérique StorSimple](storsimple-configure-chap.md)|
|Ajouter une stratégie de sauvegarde</br>Ajouter ou modifier une planification</br>Supprimer une stratégie de sauvegarde</br>Effectuer une sauvegarde manuelle</br>Créer une stratégie de sauvegarde personnalisée avec plusieurs volumes et les planifications|Stratégies de sauvegarde de gestionnaire de StorSimple service → périphériques →|[Gérer les stratégies de sauvegarde](storsimple-manage-backup-policies.md)|
|Arrêter les contrôleurs de périphérique</br>Redémarrez les contrôleurs de périphérique</br>Arrêter les contrôleurs de périphérique</br>Réinitialisez le périphérique par défaut</br>(Ci-dessus sont pour appareil sur site uniquement)|Gestionnaire de StorSimple service → périphériques → Maintenance|[Gérer le contrôleur de périphérique StorSimple](storsimple-manage-device-controller.md)|
|Obtenir des informations sur les composants matériels de StorSimple</br>Surveillance de l’état matériel</br>(Ci-dessus sont pour appareil sur site uniquement)|Gestionnaire de StorSimple service → périphériques → Maintenance|[Composants matériels du moniteur](storsimple-monitor-hardware-status.md)|
|Créer un package de prise en charge|Gestionnaire de StorSimple service → périphériques → Maintenance|[Créer et gérer un package de prise en charge](storsimple-create-manage-support-package.md)|
|Installer les mises à jour logicielles|Gestionnaire de StorSimple service → périphériques → Maintenance|[Mise à jour de votre périphérique](storsimple-update-device.md)|


##<a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes avec le fonctionnement quotidien de votre périphérique StorSimple ou un de ses composants matériels, reportez-vous à :

- [Résoudre les problèmes liés à un dispositif opérationnel](storsimple-troubleshoot-operational-device.md)
- [Utilisez les voyants de surveillance de StorSimple](storsimple-monitoring-indicators.md)

Si vous ne pouvez pas résoudre les problèmes et vous devez créer une demande de service, voir [contacter](storsimple-contact-microsoft-support.md)le support technique Microsoft.
