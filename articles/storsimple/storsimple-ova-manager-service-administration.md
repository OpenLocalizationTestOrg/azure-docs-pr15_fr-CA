<properties 
   pageTitle="Administration du Gestionnaire de StorSimple virtuelle tableau | Microsoft Azure"
   description="Apprenez à gérer votre tableau virtuel local de StorSimple en utilisant le service Gestionnaire de StorSimple dans le portail classique Azure."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-virtual-array"></a>Le service StorSimple Manager permet d’administrer votre tableau virtuel StorSimple

![flux du processus d’installation](./media/storsimple-ova-manager-service-administration/manage4.png)

## <a name="overview"></a>Vue d’ensemble

Cet article décrit l’interface de service Gestionnaire de StorSimple, y compris comment connecter et les différentes options disponibles et fournit des liens vers les flux de travail spécifiques qui peut être effectuées via cette interface. 

Après avoir lu cet article, vous saurez comment :

- Se connecter au service Gestionnaire de StorSimple
- Naviguer dans l’interface utilisateur Gestionnaire de StorSimple
- Administrer votre tableau virtuel de StorSimple via le service Gestionnaire de StorSimple

> [AZURE.NOTE] Pour afficher les options de gestion disponibles pour le périphérique de la gamme 8000 de StorSimple, accédez à [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

## <a name="connect-to-the-storsimple-manager-service"></a>Se connecter au service Gestionnaire de StorSimple

Le service Gestionnaire de StorSimple s’exécute dans Microsoft Azure et se connecte à plusieurs baies virtuel StorSimple. Vous utilisez un portail classique Microsoft Azure central s’exécutant dans un navigateur pour gérer ces périphériques. Pour vous connecter au service Gestionnaire de StorSimple, effectuez les opérations suivantes.

#### <a name="to-connect-to-the-service"></a>Pour vous connecter au service

1. Accédez à [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. À l’aide de vos informations d’identification de compte Microsoft, connectez-vous au portail Microsoft Azure classique (situé en haut à droite du volet).

3. Faites défiler le volet de navigation de gauche pour accéder au service Gestionnaire de StorSimple.

    ![Accédez à un service](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## <a name="navigate-the-storsimple-manager-service-ui"></a>Recherchez le service de gestionnaire de StorSimple l’interface utilisateur

La hiérarchie de navigation pour le service Gestionnaire de StorSimple l’interface utilisateur est indiqué dans le tableau suivant.

- La page de lancement du **Gestionnaire de StorSimple** permet d’atteindre les pages de niveau de service de l’interface utilisateur applicables à tous les groupes virtuels au sein d’un service.

- La page **périphériques** permet d’atteindre les pages de l’interface utilisateur au niveau du périphérique applicables à un tableau virtuel spécifique.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Hiérarchie de navigation de service Gestionnaire de StorSimple

|Page d’accueil|Pages de niveau de service|Pages au niveau du périphérique|
|---|---|---|
|Service Gestionnaire de StorSimple|Tableau de bord (service)|Tableau de bord (périphérique)|
||Périphériques →|Moniteur|
||Catalogue de sauvegarde|Partages (serveur de fichiers) ou </br>Volumes (serveur iSCSI)|
||Configurer (service)|Configurer (périphérique)|
||Travaux|Gestion des|
||Alertes|

## <a name="use-the-storsimple-manager-service-to-perform-management-tasks"></a>Le service Gestionnaire de StorSimple permet d’effectuer des tâches de gestion

Le tableau suivant affiche un résumé de toutes les tâches de gestion courantes et des workflows complexes qui peuvent être effectuées dans le service de gestionnaire de StorSimple l’interface utilisateur. Ces tâches sont classées selon les pages de l’interface utilisateur sur lequel elles sont lancées.

Pour plus d’informations sur chaque flux de travail, cliquez sur la procédure appropriée dans le tableau.

#### <a name="storsimple-manager-workflows"></a>StorSimple Gestionnaire de flux de travail

|Si vous souhaitez le faire...|Accédez à cette page de l’interface utilisateur...|Utilisez cette procédure|
|---|---|---|
|Création d’un service</br>Supprimer un service</br>Obtention de la clé d’inscription de service</br>Régénérer la clé d’inscription de service|Service Gestionnaire de StorSimple|[Déployer le service Gestionnaire de StorSimple](storsimple-ova-manage-service.md)|
|Modifier la clé de cryptage de données de service</br>Afficher les journaux des opérations|Tableau de bord StorSimple Manager service →|[Utilisez le tableau de bord de service StorSimple](storsimple-ova-service-dashboard.md)|
|Désactiver un tableau virtuel</br>Supprimer un tableau virtuel|Gestionnaire de StorSimple service → périphériques|[Désactiver ou supprimer un tableau virtuel](storsimple-ova-deactivate-and-delete-device.md)|
|Basculement de périphérique et de la récupération après sinistre</br>Conditions préalables de basculement</br>Basculement vers un périphérique virtuel</br>Continuité d’activité reprise (BCDR)</br>Erreurs lors de la reprise après sinistre|Gestionnaire de StorSimple service → périphériques|[Dispositif et la récupération des basculements sur incident pour votre tableau virtuel StorSimple](storsimple-ova-failover-dr.md)|
|Sauvegarder les partages et volumes</br>Effectuer une sauvegarde manuelle</br>Modifier la planification de sauvegarde</br>Afficher les sauvegardes existantes|Catalogue de sauvegarde de gestionnaire de StorSimple service →|[Sauvegarder votre tableau virtuel StorSimple](storsimple-ova-backup.md)|
|Restauration des partages à partir d’un jeu de sauvegarde</br>Restauration des volumes à partir d’un jeu de sauvegarde</br>Au niveau de l’élément de récupération (serveur de fichiers uniquement)|Catalogue de sauvegarde StorSimple Manager service →|[Restaurer à partir d’une sauvegarde de votre tableau virtuel StorSimple](storsimple-ova-restore.md)|
|À propos des comptes de stockage</br>Ajouter un compte de stockage</br>Modifier un compte de stockage</br>Supprimer un compte de stockage|Gestionnaire de StorSimple service → configurer|[Gérer les comptes de stockage pour le tableau virtuel StorSimple](storsimple-ova-manage-storage-accounts.md)|
|À propos des enregistrements de contrôle d’accès</br>Ajouter ou modifier un enregistrement de contrôle d’accès </br>Supprimer un enregistrement de contrôle d’accès|Gestionnaire de StorSimple service → configurer|[Gérer les enregistrements de contrôle d’accès pour le tableau virtuel StorSimple](storsimple-ova-manage-acrs.md)|
|Afficher les détails du projet|Gestionnaire de StorSimple service → travaux| [Gérer les travaux de tableau virtuel de StorSimple](storsimple-ova-manage-jobs.md)|
|Configurer les paramètres d’alerte</br>Recevoir des notifications d’alerte</br>Gérer les alertes</br>Consulter les alertes|Gestionnaire de StorSimple service → alertes|[Permet d’afficher et de gérer les alertes pour le tableau virtuel StorSimple](storsimple-ova-manage-alerts.md)|
|Modifier le mot de passe administrateur|Gestionnaire de StorSimple service → périphériques → configurer|[Modifier le mot de passe administrateur de périphérique tableau virtuel de StorSimple](storsimple-ova-change-device-admin-password.md)|
|Installer les mises à jour logicielles|Gestionnaire de StorSimple service → périphériques → Maintenance|[Mettre à jour votre tableau virtuel](storsimple-ova-install-update-01.md)|

>[AZURE.NOTE] Vous devez utiliser l' [interface utilisateur web de local](storsimple-ova-web-ui-admin.md) pour les tâches suivantes :
>
>- [Récupérer la clé de cryptage de données de service](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Créer un package de prise en charge](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Arrêter et redémarrer un tableau virtuel](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##<a name="next-steps"></a>Étapes suivantes
Pour informations sur l’interface utilisateur web et comment l’utiliser, accédez à [utiliser l’interface utilisateur pour administrer votre tableau virtuel StorSimple web StorSimple](storsimple-ova-web-ui-admin.md).
