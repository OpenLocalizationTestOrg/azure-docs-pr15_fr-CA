<properties
    pageTitle="Mise en route de solutions préconfigurées | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à déployer une solution Azure IoT Suite est préconfiguré."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Didacticiel : Mise en route avec les solutions préconfigurées

## <a name="introduction"></a>Introduction

Azure IoT Suite [solutions préconfigurées] [ lnk-preconfigured-solutions] combiner plusieurs services Azure IoT pour fournir des solutions de bout en bout qui implémentent des scénarios d’entreprise courants IoT. La solution de *surveillance à distance* préconfigurées se connecte à et surveille vos périphériques. Vous pouvez utiliser la solution à analyser le flux de données à partir de vos périphériques et pour améliorer les résultats de l’entreprise en rendant les processus à répondre automatiquement à ce flux de données.

Ce didacticiel vous montre comment mettre en service la solution préconfigurée d’analyse à distance. Il vous guide également dans les fonctionnalités de base de la solution de surveillance à distance. Vous pouvez accéder à la plupart de ces fonctionnalités via le tableau de bord de solution déploie avec la solution préconfigurée :

![Surveillance à distance préconfigurées de tableau de bord de solution][img-dashboard]

Pour terminer ce didacticiel, vous avez besoin d’un abonnement Azure.

> [AZURE.NOTE]  Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure][lnk_free_trial].

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Afficher le tableau de bord de solution

Le tableau de bord de solution vous permet de gérer la solution déployée. Par exemple, vous pouvez afficher de télémétrie ajouter des périphériques et configurer des règles de.

1.  Lorsque la mise en service est terminée et que la mosaïque pour votre solution préconfigurée indique **prêt**, cliquez sur **Démarrer** pour ouvrir votre portail de solution de surveillance à distance dans un nouvel onglet.

    ![Lancement de la solution préconfigurée][img-launch-solution]

2.  Par défaut, le portail de solution affiche le *tableau de bord de solution*. Vous pouvez sélectionner d’autres affichages à l’aide du menu de gauche.

    ![Surveillance à distance préconfigurées de tableau de bord de solution][img-dashboard]

Le tableau de bord affiche les informations suivantes :

- La carte affiche l’emplacement de chacun des périphériques connectés à la solution. Lorsque vous exécutez la solution pour la première fois, il y a quatre périphériques simulés. Les simulations de périphériques sont implémentés en tant que WebJobs d’Azure, et la solution utilise l’API Bing Maps pour tracer des informations sur la carte.
- Le panneau **Historique de télémétrie** trace télémétrie d’humidité et de température d’une unité sélectionnée de près en temps réel et affiche les données agrégées comme l’humidité moyenne, minimale et maximale.
- Le panneau **Historique d’alarme** montre les événements récents d’alarme lorsqu’une valeur de TELEMETRIE a dépassé le seuil. Vous pouvez définir vos propres alarmes en plus des exemples créés par la solution préconfigurée.

## <a name="view-the-device-list"></a>Permet d’afficher la liste des périphériques

La liste des périphériques affiche tous les périphériques inscrits dans la solution. Vous permet d’afficher et de modifiez les métadonnées de périphérique, ajoutez ou supprimez des périphériques et envoyez des commandes aux périphériques.

1.  Dans le menu de gauche pour afficher la *liste des périphériques* de cette solution, cliquez sur **périphériques** .

    ![Liste des périphériques dans le tableau de bord][img-devicelist]

2.  La liste des périphériques indique qu’il y a quatre périphériques de simulations créés par le processus de déploiement.

3.  Cliquez sur un périphérique dans la liste de périphériques pour afficher les détails du périphérique.

    ![Détails du périphérique dans le tableau de bord][img-devicedetails]

Le panneau **Informations sur le périphérique** contient trois sections :

- La section **Actions** répertorie les actions que vous pouvez effectuer sur le périphérique. Si vous désactivez le périphérique, il n’est plus autorisé à envoyer de télémétrie ou de recevoir des commandes. Si vous désactivez un périphérique, vous pouvez ensuite l’activer à nouveau. Vous pouvez ajouter une règle associée le périphérique qui déclenche une alarme lorsqu’une valeur de télémétrie dépasse un seuil. Vous pouvez également envoyer une commande à un périphérique. Lorsqu’un périphérique se connecte tout d’abord, il indique à la solution les commandes qu’il peut répondre à.
- La section **Propriétés du périphérique** répertorie les métadonnées de périphérique. Certaines de ces métadonnées est fourni à partir du périphérique lui-même (par exemple, le fabricant) et certains est généré par la solution (par exemple, l’heure de création). Vous pouvez modifier les métadonnées de périphérique à partir d’ici.
- La section de **Clés d’authentification** répertorie les clés de que l’appareil peut utiliser pour s’authentifier auprès de la solution.

## <a name="send-a-command-to-a-device"></a>Envoyer une commande à un périphérique

Le volet d’informations de périphérique affiche toutes les commandes prises en charge par un périphérique spécifique et permet d’envoyer des commandes à un périphérique. Premier démarrage d’un périphérique, il envoie des informations sur les commandes qu’il prend en charge à la solution.

1.  Dans le volet d’informations de périphérique pour le périphérique sélectionné, cliquez sur **commandes** .

    ![Commandes de périphérique dans le tableau de bord][img-devicecommands]

2.  Sélectionnez **PingDevice** dans la liste de commande.

3.  Cliquez sur **Envoyer la commande**.

4.  Vous pouvez voir le statut de la commande dans l’historique des commandes.

    ![État de la commande dans le tableau de bord][img-pingcommand]

La solution effectue le suivi de l’état de chaque commande qu’il envoie. Initialement, le résultat est **en attente**. Lorsque le périphérique signale qu’il a exécuté la commande, le résultat est affecté à la **Réussite**.

## <a name="add-a-new-simulated-device"></a>Ajouter un nouveau périphérique simulé

Lorsque vous déployez la solution préconfigurée, vous configurez automatiquement les quatre dispositifs que vous pouvez voir dans la liste des périphériques. Ces périphériques sont des *simulations de périphériques* en cours d’exécution dans un WebJob d’Azure. Simulations de périphériques facilitent pour expérimenter la solution préconfigurée sans devoir déployer des périphériques physiques réels. Si vous ne souhaitez pas un véritable périphérique de se connecter à la solution, consultez la [solution préconfigurée de surveillance de connecter votre appareil au serveur distant] [ lnk-connect-rm] didacticiel.

Les étapes suivantes vous indiquent comment ajouter un périphérique simulé à la solution :

1.  Accédez à la liste des périphériques.

2.  Cliquez sur **+ Ajouter un périphérique** dans le coin inférieur gauche pour ajouter un périphérique.

    ![Ajouter un périphérique à la solution préconfigurée][img-adddevice]

3.  Cliquez sur **Ajouter** dans la fenêtre de **Dispositif de simulation** .

    ![Définir les détails du nouveau périphérique dans le tableau de bord][img-addnew]
    
    Outre la création d’un nouveau périphérique simulé, vous pouvez également ajouter un périphérique physique, si vous choisissez de créer un **Périphérique du Custom**. Pour en savoir plus sur la connexion de périphériques physiques à la solution, voir [se connecter votre périphérique à la Suite de IoT préconfiguré de solution de surveillance à distance][lnk-connect-rm].

4.  Sélectionnez **me permettre de définir votre propre ID de périphérique**et entrez un nom d’ID de périphérique unique comme **mydevice_01**.

5.  Cliquez sur **créer**.

    ![Enregistrer un nouveau périphérique][img-definedevice]

6. À l’étape 3 **d’Ajouter un périphérique simulé**, cliquez sur **terminé** pour revenir à la liste des périphériques.

7. Vous pouvez afficher votre périphérique **en cours d’exécution** dans la liste des périphériques.

    ![Nouveau périphérique de vue dans la liste des périphériques][img-runningnew]

8. Vous pouvez également afficher la télémétrie simulée à partir de votre nouveau périphérique sur le tableau de bord :

    ![Télémétrie d’affichage à partir du nouveau périphérique][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>Modifier les métadonnées de périphérique

Lorsqu’un périphérique se connecte d’abord à la solution, il envoie ses métadonnées à la solution. Lorsque vous modifiez les métadonnées de périphérique via le tableau de bord de solution, il envoie les nouvelles valeurs des métadonnées sur le périphérique et enregistre les nouvelles valeurs dans la base de DocumentDB de solution. Pour plus d’informations, consultez le [Registre d’identité de périphérique et de DocumentDB][lnk-devicemetadata].

1.  Accédez à la liste des périphériques.

2.  Sélectionnez votre nouveau périphérique dans la **Liste de périphériques**et puis cliquez sur **Modifier** pour modifier les **Propriétés d’un périphérique**:

    ![Modifier les métadonnées de périphérique][img-editdevice]

3. Faites défiler vers le bas et modifiez les valeurs de latitude et de longitude. Cliquez ensuite sur **Enregistrer les modifications dans le Registre de périphérique**.

    ![Modifier les métadonnées de périphérique][img-editdevice2]

4. Naviguer en arrière vers le tableau de bord, l’emplacement du périphérique a été modifié sur la carte :

    ![Modifier les métadonnées de périphérique][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>Ajouter une règle pour le nouveau périphérique

Il n’existe aucune règle pour le nouveau périphérique que vous venez d’ajouter. Dans cette section, vous ajoutez une règle qui déclenche une alarme lorsque la température indiquée par le nouveau périphérique supérieure à 47 degrés. Avant de commencer, notez que l’historique de télémétrie pour le nouveau périphérique sur le tableau de bord affiche que la température du périphérique ne dépasse jamais 45 degrés.

1.  Accédez à la liste des périphériques.

2.  Sélectionnez votre nouveau périphérique dans la **Liste des périphériques**, puis cliquez sur **Ajouter une règle** pour ajouter une règle pour le périphérique.

3. Créer une règle qui utilise la **température** que le champ de données et utilise **AlarmTemp** comme sortie lorsque la température dépasse 47 degrés :

    ![Ajouter une règle pour les périphériques][img-adddevicerule]

4. Cliquez sur **Enregistrer et afficher les règles** pour enregistrer vos modifications.

5.  Dans le volet d’informations de périphérique pour le nouveau périphérique, cliquez sur **commandes** .

    ![Ajouter une règle pour les périphériques][img-adddevicerule2]

6.  Sélectionnez **ChangeSetPointTemp** à partir de la liste de commandes et valeur **SetPointTemp** 45. Puis cliquez sur **Envoyer la commande**:

    ![Ajouter une règle pour les périphériques][img-adddevicerule3]

7.  Naviguer en arrière vers le bord de la solution. Après un bref moment, vous verrez une nouvelle entrée dans le volet **Historique d’alarme** lorsque la température indiquée par votre nouveau périphérique dépasse le seuil de 47 degrés :

    ![Ajouter une règle pour les périphériques][img-adddevicerule4]

8. Vous pouvez consulter et modifier toutes les règles sur la page **règles** du tableau de bord :

    ![Liste des règles de périphériques][img-rules]

9. Vous pouvez consulter et modifier toutes les actions qui peuvent être prises en réponse à une règle dans la page **Actions** du tableau de bord :

    ![Liste des actions sur les périphériques][img-actions]

> [AZURE.NOTE] Il est possible de définir les actions que peuvent envoyer un message électronique ou un SMS en réponse à une règle ou à intégrer dans un système de métier via une [Application de logique][lnk-logic-apps]. Pour plus d’informations, consultez la [solution préconfigurée de connecter les applications logique votre Azure IoT Suite au contrôle à distance][lnk-logicapptutorial].

## <a name="other-features"></a>Autres fonctionnalités

À l’aide du portail de solution que vous pouvez rechercher pour les périphériques avec des caractéristiques spécifiques telles qu’un numéro de modèle :

![Recherche d’un périphérique][img-search]

Vous pouvez désactiver un périphérique, et une fois qu’il est désactivé, vous pouvez supprimer il :

![Désactiver et supprimer un périphérique][img-disable]

## <a name="behind-the-scenes"></a>Dans les coulisses

Lorsque vous déployez une solution préconfigurée, le processus de déploiement crée plusieurs ressources dans l’abonnement Azure que vous avez sélectionné. Vous pouvez afficher ces ressources dans Azure [portal][lnk-portal]. Le processus de déploiement crée un **groupe de ressources** avec un nom basé sur le nom que vous choisissez pour votre solution préconfigurée :

![Solution préconfigurée dans le portail Azure][img-portal]

Vous pouvez afficher les paramètres de chaque ressource en la sélectionnant dans la liste des ressources dans le groupe de ressources.

Vous pouvez également afficher le code source de la solution préconfigurée. Le code source de solution préconfigurée surveillance à distance est dans [azure-iot-de surveillance à distance] [ lnk-rmgithub] GitHub référentiel :

- Le dossier **DeviceAdministration** contient le code source pour le tableau de bord.
- Le dossier **Simulator** contient le code source du périphérique simulé.
- Le dossier **EventProcessor** contient le code source pour le processus de back-end qui gère la télémesure entrant.

Lorsque vous avez terminé, vous pouvez supprimer la solution préconfigurée votre abonnement Azure sur [azureiotsuite.com] [ lnk-azureiotsuite] site. Ce site vous permet de facilement supprimer toutes les ressources qui ont été mis en service lorsque vous avez créé la solution préconfigurée.

> [AZURE.NOTE] Pour vous assurer que vous supprimez tout ce qui concerne la solution préconfigurée, supprimer dans les [azureiotsuite.com] [ lnk-azureiotsuite] de site et ne supprimez pas simplement le groupe de ressources dans le portail.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé une solution de travail préconfiguré, vous pouvez continuer la mise en route avec IoT Suite, lisez les articles suivants :

- [Surveillance à distance préconfigurées de procédure pas à pas de solution][lnk-rm-walkthrough]
- [Connectez votre périphérique à la solution de surveillance préconfigurée à distance][lnk-connect-rm]
- [Autorisations sur le site azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
