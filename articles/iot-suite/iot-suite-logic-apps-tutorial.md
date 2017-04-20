<properties
  pageTitle="IoT Azure Suite et applications de logique | Microsoft Azure"
  description="Didacticiel sur comment raccorder la logique des applications Azure IoT Suite de processus d’entreprise."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="08/16/2016"
  ms.author="araguila"/>
  
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Didacticiel : Connexion logique d’application à votre solution Azure IoT Suite TÉLÉSURVEILLANCE préconfigurés

La [Suite de Microsoft Azure IoT] [ lnk-internetofthings] solution préconfigurée de surveillance à distance est un excellent moyen de vous familiariser rapidement avec un jeu de fonctionnalité de bout en bout qui illustre une solution IoT. Ce didacticiel vous explique comment ajouter la logique d’application à votre solution préconfigurée de surveillance à distance Microsoft Azure IoT Suite. Ces étapes expliquent comment vous pouvez prendre votre solution IoT encore plus loin en le connectant à un processus d’entreprise.

_Si vous recherchez une procédure pas à pas sur la disposition d’une zone de surveillance à distance de solution préconfigurée, consultez [didacticiel : mise en route avec les solutions IoT préconfiguré][lnk-getstarted]._

Avant de commencer ce didacticiel, vous devez :

- La surveillance à distance de fourniture préconfigurée solution dans votre abonnement Azure.

- Créer un compte SendGrid pour vous permettre d’envoyer un e-mail qui déclenche le processus de votre entreprise. Vous pouvez vous inscrire pour un compte d’essai gratuit à [SendGrid](https://sendgrid.com/) en cliquant sur **essai gratuit**. Après avoir inscrit à votre compte d’évaluation gratuit, vous devez créer une [clé d’API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) dans SendGrid qui accorde des autorisations d’envoyer du courrier. Vous avez besoin de cette clé API plus loin dans le didacticiel.

Supposant que vous avez déjà configuré votre surveillance à distance de solution préconfigurée, accédez au groupe de ressources pour cette solution dans [Azure portal][lnk-azureportal]. Le groupe de ressources a le même nom que le nom de la solution choisie lorsque vous configuré votre solution de surveillance à distance. Dans le groupe de ressources, vous pouvez voir toutes les ressources Azure mis en service pour votre solution, à l’exception de l’application Azure Active Directory que vous trouverez dans le portail classique d’Azure. La capture d’écran suivante illustre une lame de **groupe de ressources** d’exemple pour une solution préconfigurée de surveillance à distance :

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

Pour commencer, configurez l’application de la logique à utiliser avec la solution préconfigurée.

## <a name="set-up-the-logic-app"></a>Configurer l’application logique

1. Cliquez sur __Ajouter__ en haut de la blade de groupe de ressources dans le portail Azure.

2. Pour __l’Application de la logique__de recherche, sélectionnez-le et puis cliquez sur **créer**.

3. Entrez le __nom__ et utilisent le même **abonnement** et **groupe de ressources** que vous avez utilisé lors du déploiement de votre solution de surveillance à distance. Cliquez sur __créer__.

    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)

4. Une fois votre déploiement terminé, vous pouvez voir que l’application logique est répertoriée en tant que ressource dans votre groupe de ressources.

5. Cliquez sur l’application de la logique pour atteindre la lame logique App, sélectionnez le modèle **Vide logique d’application** pour ouvrir le **Concepteur d’applications logique**.

    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)

6. Sélectionnez la __demande__. Cette action indique qu’une demande HTTP entrante avec un JSON spécifique au format actes de charge utile un déclencheur.

7. Dans le schéma de JSON de corps de requête, collez ce qui suit :

    ```
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
    
    > [AZURE.NOTE] Vous pouvez copier l’URL pour la publication HTTP une fois que vous enregistrez l’application logique, mais vous devez d’abord ajouter une action.

8. Cliquez sur __+ nouvelle étape__ , sous votre déclencheur manuel. Puis cliquez sur **Ajouter une action**.

    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)

9. Rechercher des **SendGrid - envoyer un e-mail** et cliquez dessus.

    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)

10. Entrez un nom pour la connexion, tel que **SendGridConnection**, entrez la **Clé d’API SendGrid** que vous avez créé lorsque vous configurez votre compte de SendGrid, cliquez sur **créer**.

    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)

11. Ajouter des adresses de messagerie que vous possédez pour à la fois les champs **** d’et **à** . Dans le champ **objet** , ajoutez **[DeviceId] d’alerte de surveillance à distance** . Dans le champ **Corps de l’E-mail** , ajoutez **[DeviceId] a signalé [measurementName] avec la valeur [measuredValue].** Vous pouvez ajouter **[DeviceId]**, **[measurementName]**et **[measuredValue]** en cliquant sur dans la section **vous pouvez insérer des données à partir des étapes précédentes** .

    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)

12. Dans le menu supérieur, cliquez sur __Enregistrer__ .

13. Cliquez sur le déclencheur de **la demande** et copiez la valeur __Http Post à cette URL__ . Vous avez besoin de cette URL plus loin dans ce didacticiel.

> [AZURE.NOTE] Applications de logique permettent d’exécuter [différents types d’action] [ lnk-logic-apps-actions] , y compris des actions dans Office 365. 

## <a name="set-up-the-eventprocessor-web-job"></a>Configurer le projet Web EventProcessor

Dans cette section, vous vous connectez à votre solution préconfigurée pour l’application logique que vous avez créé. Pour effectuer cette tâche, vous ajoutez l’URL pour déclencher l’application de la logique de l’action qui se déclenche lorsqu’une valeur de capteur de dispositif dépasse un seuil.

1. Votre client git permet de cloner la dernière version de la [azure-iot-de surveillance à distance référentiel de github][lnk-rmgithub]. Par exemple :

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. Dans Visual Studio, ouvrez le __RemoteMonitoring.sln__ à partir de la copie locale du référentiel.

3. Ouvrez le fichier __ActionRepository.cs__ dans le **Infrastructure\\référentiels** dossier.

4. Mettre à jour le dictionnaire **actionIds** avec le __Http Post vers cette URL__ vous avez noté à partir de votre application logique comme suit :

    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this UR>" },
        { "Raise Alarm", "<Http Post to this UR> }
    };
    ```

5. Enregistrer les modifications dans la solution et quittez Visual Studio.

## <a name="deploy-from-the-command-line"></a>Déployer à partir de la ligne de commande

Dans cette section, vous déployez votre version mise à jour de la solution de surveillance à distance pour remplacer la version en cours d’exécution dans Azure.

1. Suivant la [configuration de dev] [ lnk-devsetup] instructions pour configurer votre environnement pour le déploiement.

2.  Pour déployer localement, suivez le [déploiement local] [ lnk-localdeploy] instructions.

3.  Pour déployer sur le nuage et la mise à jour de votre déploiement de cloud existant, suivez le [déploiement de cloud] [ lnk-clouddeploy] instructions. Comme le nom de déploiement, utilisez le nom de votre déploiement d’origine. Pour exemple, si le déploiement d’origine a été appelé **demologicapp**, utilisez la commande suivante :

    ``
    build.cmd cloud release demologicapp
    ``
    
    Lors de l’exécution du script de compilation, veillez à utiliser le même compte Azure abonnement, région et instance Active Directory que vous avez utilisé lors du déploiement de la solution.

## <a name="see-your-logic-app-in-action"></a>Consultez votre application logique en action

La solution de surveillance préconfigurée à distance possède deux règles définies par défaut lorsque vous configurez une solution. Les deux règles sont sur le périphérique **SampleDevice001** :

* Température > 38.00
* Humidité > 48,00

La règle de température déclenche l’action de **Déclencher une alarme** et la règle d’humidité déclenche l’action **SendMessage** . En supposant que vous avez utilisé la même URL pour les actions de la classe **ActionRepository** , votre application logique déclenche une règle. Les deux règles utilisent SendGrid pour envoyer un e-mail à **l’adresse avec les détails de l’alerte** .

> [AZURE.NOTE] L’application logique continue à déclencher chaque fois que le seuil est atteint. Pour éviter les courriels inutiles, vous pouvez désactiver les règles dans votre portail de solution ou désactiver l’application logique dans [Azure portal][lnk-azureportal].

En plus de recevoir des e-mails, vous pouvez également voir lors de l’exécution de l’application de la logique dans le portail :

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez utilisé une application de logique pour connecter la solution préconfigurée pour un processus d’entreprise, vous pouvez en savoir plus sur les options de personnalisation de solutions préconfigurées :

- [Utilisation de télémétrie dynamique avec l’analyse à distance de solution préconfigurée][lnk-dynamic]
- [Solution préconfigurée de métadonnées d’informations de périphérique dans la zone de surveillance à distance][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
