<properties 
   pageTitle="Web de tableau virtuel de StorSimple administration de l’interface utilisateur | Microsoft Azure"
   description="Décrit comment effectuer des tâches d’administration de base des périphériques par l’intermédiaire de l’interface utilisateur du web StorSimple Virtual Array."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Utilisez l’interface utilisateur Web pour administrer votre tableau virtuel StorSimple

![flux du processus d’installation](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Vue d’ensemble

Les didacticiels dans cet article s’appliquent à la Microsoft Azure StorSimple tableau virtuel (également connu comme le StorSimple sur site périphérique virtuel) qui exécutent la version de mise à disposition générale (GA) mars 2016. Cet article décrit certains des flux de travail complexes et des tâches de gestion qui peuvent être effectuées sur le tableau virtuel StorSimple. Vous pouvez gérer le tableau virtuel de StorSimple l’aide du Gestionnaire de StorSimple service d’interface utilisateur (dénommé le portail de l’interface utilisateur) et l’interface utilisateur web local pour le périphérique. Cet article se concentre sur les tâches que vous pouvez effectuer à l’aide de l’interface utilisateur web.

Cet article comprend les didacticiels suivants :

- Obtenir la clé de cryptage de données de service
- Résoudre les erreurs d’installation de l’interface utilisateur web
- Générer un package de journaux
- Arrêter ou redémarrer votre périphérique

## <a name="get-the-service-data-encryption-key"></a>Obtenir la clé de cryptage de données de service

Une clé de cryptage de données de service est générée lorsque vous enregistrez votre premier périphérique avec le service Gestionnaire de StorSimple. Cette clé est obligatoire avec la clé d’inscription de service pour enregistrer des périphériques supplémentaires avec le service Gestionnaire de StorSimple.

Si vous avez égaré votre clé de chiffrement de données de service et de la nécessité de le récupérer, procédez comme suit les étapes dans l’interface utilisateur du périphérique local web inscrit avec votre service.

#### <a name="to-get-the-service-data-encryption-key"></a>Pour obtenir la clé de cryptage de données de service

1. Se connecter à l’interface utilisateur de web local. Accédez à **Configuration** > **les paramètres de nuage**.
  

2. En bas de la page, cliquez sur **obtenir la clé de cryptage de données de service**. Une clé s’affiche. Copiez et enregistrez cette clé.
    
    ![obtenir la clé de cryptage de données de service 1](./media/storsimple-ova-web-ui-admin/image27.png)
   


## <a name="troubleshoot-web-ui-setup-errors"></a>Résoudre les erreurs d’installation de l’interface utilisateur web

Dans certains cas lorsque vous configurez le périphérique via le web local de l’interface utilisateur, vous pouvez rencontrer des erreurs. Pour diagnostiquer et résoudre de telles erreurs, vous pouvez exécuter les tests de diagnostic.

#### <a name="to-run-the-diagnostic-tests"></a>Pour exécuter les tests de diagnostic

1. Dans l’interface utilisateur web local, accédez à la **résolution des problèmes** > **les tests de Diagnostic**.

    ![Exécutez les diagnostics 1](./media/storsimple-ova-web-ui-admin/image29.png)

2. En bas de la page, cliquez sur **Exécuter les Tests de Diagnostic**. Ceci va démarrer les tests pour diagnostiquer les problèmes possibles dans votre réseau, le périphérique, le proxy web, heure, ou les paramètres de nuage. Vous serez averti que le périphérique est en cours d’exécution des tests.

3. Une fois les tests terminés, les résultats s’affichent. L’exemple suivant montre les résultats de tests de diagnostic. Notez que les paramètres du proxy web n’ont pas configurés sur ce périphérique, et par conséquent, le test de proxy web n’a pas été exécuté. Tous les autres tests pour les paramètres de réseau, serveur DNS et les délais ont réussi.

    ![exécuter les tests de diagnostic 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Générer un package de journaux

Un package de journaux est constitué de tous les journaux pertinents qui peuvent aider le dépannage des problèmes de périphérique de Support de Microsoft. Dans cette version, un package de journaux peut être généré via le web local de l’interface utilisateur.

#### <a name="to-generate-the-log-package"></a>Pour générer le package de journaux

1. Dans l’interface utilisateur web local, accédez à la **résolution des problèmes** > **les journaux système**.

    ![générer un lot de journal 1](./media/storsimple-ova-web-ui-admin/image31.png)

2. En bas de la page, cliquez sur **créer le lot de journal**. Un package de journaux du système sera créé. Cela prendra quelques minutes.

    ![générer le package de journaux 2](./media/storsimple-ova-web-ui-admin/image32.png)

    Vous serez averti une fois le package créé, et que la page va être mis à jour pour indiquer l’heure et la date de création du package.

    ![générer un lot de journal 3](./media/storsimple-ova-web-ui-admin/image33.png)

3. Cliquez sur **Télécharger le journal**. Un package compressé sera téléchargé sur votre système.

    ![générer le package de journaux 4](./media/storsimple-ova-web-ui-admin/image34.png)

4. Vous pouvez décompresser le package téléchargé journal et afficher les fichiers journaux système.

## <a name="shut-down-and-restart-your-device"></a>Arrêtez et redémarrez le périphérique

Vous pouvez arrêter ou redémarrer votre périphérique virtuel à l’aide de l’interface utilisateur de web local. Nous avons recommandé avant de redémarrer, prenez les volumes ou les partages en mode hors connexion sur l’hôte, puis sur le périphérique. Cela permet de minimiser tout risque de corruption des données. 

#### <a name="to-shut-down-your-virtual-device"></a>Pour arrêter votre périphérique virtuel

1. Dans l’interface utilisateur web local, accédez à **gestion** > **paramètres d’alimentation**.

2. En bas de la page, cliquez sur **Arrêter**.

    ![arrêt du périphérique 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. Un message d’avertissement s’affiche pour indiquer qu’un arrêt du périphérique va interrompre tout d’e/s qui étaient en cours, ce qui entraîne un temps d’arrêt. Cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Avertissement d’arrêt de périphérique](./media/storsimple-ova-web-ui-admin/image37.png)

    Vous serez averti que la fermeture a été initiée.

    ![arrêt du périphérique démarré](./media/storsimple-ova-web-ui-admin/image38.png)

    L’appareil va s’arrêter. Si vous souhaitez démarrer votre périphérique, vous devrez le faire via le Gestionnaire Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Pour redémarrer votre périphérique virtuel

1. Dans l’interface utilisateur web local, accédez à **gestion** > **paramètres d’alimentation**.

2. En bas de la page, cliquez sur **redémarrer**.

    ![redémarrage du périphérique](./media/storsimple-ova-web-ui-admin/image36.png)

3. Un message d’avertissement s’affiche indiquant que le redémarrage du périphérique va interrompre tout IOs qui étaient en cours, ce qui entraîne un temps d’arrêt. Cliquez sur l’icône de vérification ![Vérifiez l’icône](./media/storsimple-ova-web-ui-admin/image3.png).

    ![avertissement relatif au redémarrage](./media/storsimple-ova-web-ui-admin/image37.png)

    Vous serez averti que le redémarrage a été initié.

    ![redémarrage](./media/storsimple-ova-web-ui-admin/image39.png)

    Lorsque le redémarrage est en cours, vous perdez la connexion à l’interface utilisateur. Vous pouvez surveiller le redémarrage en actualisant régulièrement de l’interface utilisateur. Sinon, vous pouvez surveiller l’état de redémarrage du périphérique via le Gestionnaire Hyper-V.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [utiliser le service de gestionnaire de StorSimple pour gérer le périphérique](storsimple-manager-service-administration.md).
