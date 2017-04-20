> [AZURE.SELECTOR]
- [C sous Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C sous Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C sur mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## <a name="scenario-overview"></a>Présentation du scénario

Dans ce scénario, vous créez un périphérique qui envoie la télémétrie suivante à la distance [solution préconfigurée]de surveillance[lnk-what-are-preconfig-solutions]:

- Température externe
- Température interne
- Humidité

Pour plus de simplicité, le code sur le périphérique génère des exemples de valeurs, mais nous vous encourageons à étendre l’exemple en connectant des capteurs réels pour votre périphérique et envoi de télémétrie réel.

Pour terminer ce didacticiel, vous avez besoin d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure][lnk-free-trial].

## <a name="before-you-start"></a>Avant de commencer

Avant d’écrire le code pour votre périphérique, vous devez configurer votre solution préconfigurée surveillance à distance et puis mettre en service un nouveau périphérique personnalisé dans cette solution.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Mettre en service votre solution préconfigurée surveillance à distance

Le périphérique que vous créez dans ce didacticiel envoie des données à une instance de la [surveillance à distance] [ lnk-remote-monitoring] solution préconfigurée. Si vous ne l’avez pas déjà déployé la surveillance à distance de la solution dans votre compte Azure préconfigurée, suivez les étapes ci-dessous :

1. Dans la page <https://www.azureiotsuite.com/> , cliquez sur **+** pour créer une nouvelle solution.

2. Cliquez sur **Sélectionner** dans le panneau de **contrôle à distance** pour créer votre nouvelle solution.

3. Sur la page de **solution de surveillance de créer à distance** , entrez le **nom de la Solution** de votre choix la **région** que vous souhaitez déployer sur et sélectionnez l’abonnement Azure à utiliser. Puis cliquez sur **créer une solution**.

4. Attendez que le processus de déploiement se termine.

> [AZURE.WARNING] Les solutions préconfigurées utilisent des services Azure facturables. Veillez à supprimer la solution préconfigurée de votre abonnement, lorsque vous avez terminé avec lui pour éviter les frais inutiles. Vous pouvez supprimer complètement une solution préconfigurée de votre abonnement, visitez la page <https://www.azureiotsuite.com/> .

Une fois le processus de déploiement pour la solution de surveillance à distance, cliquez sur **Ouvrir** pour ouvrir le tableau de bord de solution dans votre navigateur.

![][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Configurer votre périphérique dans la solution de surveillance à distance

> [AZURE.NOTE] Si vous avez déjà déployé un périphérique dans votre solution, vous pouvez ignorer cette étape. Vous devez connaître les informations d’identification de périphérique lorsque vous créez l’application cliente.

Pour un périphérique pour se connecter à la solution préconfigurée, il doit s’identifier au concentrateur IoT à l’aide des informations d’identification valides. Vous pouvez récupérer les informations d’identification du périphérique à partir du tableau de bord de solution. Vous incluez les informations d’identification de périphérique dans votre application cliente, plus loin dans ce didacticiel. 

Pour ajouter un nouveau périphérique à votre solution de surveillance à distance, procédez comme suit dans le tableau de bord de solution :

1.  Dans l’angle inférieur gauche du tableau de bord, cliquez sur **Ajouter un périphérique**.

    ![][1]

2.  Dans le panneau **Personnalisé périphérique** , cliquez sur **Ajouter nouveau**.

    ![][2]

3.  Cliquez sur **me permettre de définir votre propre ID de périphérique**, entrez un ID de périphérique, tels que **mydevice**, cliquez sur **Code de vérification** pour vérifier que ce nom n’est pas déjà en cours d’utilisation, puis cliquez sur **créer** pour configurer le périphérique.

    ![][3]

5. Notez les informations d’identification de périphérique (ID de périphérique, le nom d’hôte de IoT Hub et clé de périphérique), votre application cliente doit pouvoir se connecter à la solution de surveillance à distance. Puis cliquez sur **terminé**.

    ![][4]

6. Assurez-vous que votre périphérique s’affiche dans la section périphériques. L’état du périphérique est **en attente** jusqu'à ce que le périphérique établit une connexion à la solution de surveillance à distance.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/