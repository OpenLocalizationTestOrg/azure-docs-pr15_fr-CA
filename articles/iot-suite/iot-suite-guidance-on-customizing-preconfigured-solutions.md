<properties
    pageTitle="Personnalisation préconfiguré solutions | Microsoft Azure"
    description="Fournit des conseils sur la personnalisation de solutions Azure IoT Suite est préconfiguré."
    services=""
    suite="iot-suite"
    documentationCenter=".net"
    authors="aguilaaj"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/11/2016"
     ms.author="aguilaaj"/>

# <a name="customize-a-preconfigured-solution"></a>Personnaliser une solution préconfigurée

Les solutions préconfigurées fournies avec la Suite IoT Azure illustrent les services au sein de la suite fonctionnent ensemble pour fournir une solution de bout en bout. À partir de ce point de départ, il y a plusieurs endroits dans lesquels vous pouvez étendre et personnaliser la solution pour des scénarios spécifiques. Les sections suivantes décrivent ces points de personnalisation courants.

## <a name="finding-the-source-code"></a>Recherche le code source

Le code source pour les solutions préconfigurées est disponible sur GitHub dans les référentiels suivants :

- Surveillance à distance : [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Une Maintenance PREVENTIVE : [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

Le code source pour les solutions préconfigurées est fourni pour illustrer les modèles et les techniques utilisées pour implémenter la fonctionnalité de bout en bout d’une solution IoT avec Azure IoT Suite. Vous trouverez plus d’informations sur comment générer et déployer les solutions dans les référentiels GitHub.

## <a name="changing-the-preconfigured-rules"></a>Modification de règles préconfigurées

La solution de surveillance à distance inclut les trois projets de [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) pour implémenter la logique des informations, de télémétrie et de règles de périphérique affichée pour la solution.

Les travaux d’analytique de trois flux et leur syntaxe est décrite en détail dans la [procédure pas à pas de solution préconfigurée de surveillance à distance](iot-suite-remote-monitoring-sample-walkthrough.md). 

Vous pouvez modifier ces travaux directement pour modifier la logique, ou ajouter une logique spécifique à votre scénario. Vous pouvez rechercher les tâches de flux de données Analytique comme suit :
 
1. Accédez au [portail Azure](https://portal.azure.com).
2. Naviguez jusqu’au groupe de ressources avec le même nom que votre solution IoT. 
3. Sélectionnez la tâche Azure flux Analytique que vous souhaitez modifier. 
4. Arrêter la tâche en sélectionnant **Arrêter**dans le jeu de commandes. 
5. Modifier la requête, les entrées et les sorties.

    Une modification simple est de modifier la requête pour le travail de **règles** à utiliser un **« < »** au lieu d’un **« > »**. Le portail de solution affiche toujours **» > «** lorsque vous modifiez une règle, mais vous remarquerez que le comportement est retourné en raison de la modification de la tâche sous-jacente.

6. Démarrer le travail

> [AZURE.NOTE] Le tableau de bord de surveillance à distance dépend des données spécifiques, modifier les travaux peut provoquer le tableau de bord à échouer.

## <a name="adding-your-own-rules"></a>Ajout de vos propres règles.

En plus de modifier les tâches Azure flux Analytique préconfigurés, vous pouvez utiliser le portail Azure pour ajouter de nouveaux travaux ou ajouter de nouvelles requêtes à des projets existants.

## <a name="customizing-devices"></a>Personnalisation des périphériques

Une des activités d’extension plus courantes travaille avec des périphériques spécifiques à votre scénario. Il existe plusieurs méthodes pour travailler avec des périphériques. Ces méthodes incluent modifier un périphérique simulé afin de correspondre à votre scénario, ou à l’aide du [SDK de périphérique IoT][] pour connecter votre périphérique physique à la solution.

Pour des instructions détaillées sur l’ajout de périphériques à la solution préconfigurée surveillance à distance, voir [Périphériques de connexion Iot Suite](iot-suite-connecting-devices.md) et le [Distant surveillance exemple de kit de développement logiciel C](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring) qui est conçu pour fonctionner avec la solution de surveillance préconfigurée à distance.

### <a name="creating-your-own-simulated-device"></a>Création de votre propre simulée périphérique

Inclus dans le code de source solution surveillance à distance (cité ci-dessus), est un simulateur de .NET. Ce simulateur est celui qui est configuré en tant que partie de la solution et peut être modifié pour envoyer différentes métadonnées, télémétrie ou répondre à des commandes différentes.

Le simulator préconfiguré dans la solution préconfigurée surveillance à distance est un dispositif de refroidissement qui émet de télémétrie de température et d’humidité, vous pouvez modifier le simulateur dans le projet [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) lorsque vous avez dupliquée du référentiel de GitHub.

### <a name="available-locations-for-simulated-devices"></a>Emplacements disponibles pour les périphériques simulés

Le jeu par défaut des emplacements est Seattle Way, Redmond, Washington, États-Unis d’Amérique. Vous pouvez modifier ces emplacements dans [SampleDeviceFactory.cs][lnk-sample-device-factory].


### <a name="building-and-using-your-own-physical-device"></a>Création et utilisation de votre propre unité (physique)

Les [Kits de développement logiciel Azure IoT](https://github.com/Azure/azure-iot-sdks) fournissent des bibliothèques pour la connexion de nombreux types de périphériques (langues et systèmes d’exploitation) dans des solutions IoT.

## <a name="modifying-dashboard-limits"></a>Modification des limites de tableau de bord

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Nombre de périphériques affichés dans la liste déroulante du tableau de bord

La valeur par défaut est 200. Vous pouvez modifier ce numéro dans les [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Nombre de broches à afficher dans le contrôle de carte Bing

La valeur par défaut est 200. Vous pouvez modifier ce numéro dans les [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Période de graphe de télémétrie

La valeur par défaut est 10 minutes. Vous pouvez effectuer cette modification dans les [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-setting-up-application-roles"></a>Configurer manuellement les rôles d’application

La procédure suivante décrit comment ajouter des rôles d’application **Admin** et **en lecture seule** à une solution préconfigurée. Notez que les solutions préconfigurées déjà mis en service à partir du site azureiotsuite.com incluent les rôles **administrateur** et **en lecture seule** .

Les membres du rôle **ReadOnly** peuvent voir le tableau de bord et la liste des périphériques, mais ne sont pas autorisés à ajouter des périphériques, modifier les attributs du périphérique ou envoyer des commandes.  Les membres du rôle **administrateur** ont un accès complet à toutes les fonctionnalités de la solution.

1. Atteindre le [Azure portal classique][lnk-classic-portal].

2. Sélectionnez **Active Directory**.

3. Cliquez sur le nom du locataire DAS utilisé lors du déploiement de votre solution.

4. Cliquez sur **Applications**.

5. Cliquez sur le nom de l’application correspondant au nom de votre solution préconfigurée. Si vous ne voyez pas votre application dans la liste, sélectionnez **Ma société possède des Applications** dans l' **Afficher** de liste déroulante et cliquez sur la case à cocher.

6.  En bas de la page, cliquez sur **Gérer le manifeste** , puis **Télécharger le manifeste**.

7. Il télécharge un fichier .json sur votre ordinateur local.  Ouvrez ce fichier pour le modifier dans un éditeur de texte de votre choix.

8. Sur la troisième ligne du fichier .json, vous trouverez :

  ```
  "appRoles" : [],
  ```
  Remplacer par le texte suivant :

  ```
  "appRoles": [
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Administrator access to the application",
  "displayName": "Admin",
  "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "isEnabled": true,
  "value": "Admin"
  },
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Read only access to device information",
  "displayName": "Read Only",
  "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
  "isEnabled": true,
  "value": "ReadOnly"
  } ],
  ```

9. Enregistrez le fichier .json mis à jour (vous pouvez remplacer le fichier existant).

10.  Dans le portail de gestion Azure, au bas de la page, sélectionnez **Gérer manifeste** **Manifeste de téléchargement** pour télécharger le fichier .json que vous avez enregistré à l’étape précédente.

11. Vous avez maintenant ajouté les rôles **administrateur** et **en lecture seule** à votre application.

12. Pour affecter un de ces rôles à un utilisateur de votre répertoire, consultez [autorisations sur le site azureiotsuite.com][lnk-permissions].

## <a name="feedback"></a>Commentaires

Avoir une personnalisation vous souhaiteriez couverts dans ce document ? Veuillez ajouter des suggestions de fonctionnalités à la [Voix de l’utilisateur](https://feedback.azure.com/forums/321918-azure-iot), ou un commentaire sur cet article. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les options de personnalisation de solutions préconfigurées, voir :

- [Connexion logique d’application à votre solution Azure IoT Suite TÉLÉSURVEILLANCE préconfigurés][lnk-logicapp]
- [Utilisation de télémétrie dynamique avec l’analyse à distance de solution préconfigurée][lnk-dynamic]
- [Solution préconfigurée de métadonnées d’informations de périphérique dans la zone de surveillance à distance][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[Dispositif de IoT SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com
