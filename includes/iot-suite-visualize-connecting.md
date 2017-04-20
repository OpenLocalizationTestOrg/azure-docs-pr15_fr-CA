## <a name="view-device-telemetry-in-the-dashboard"></a>Télémesure pour dispositif de vue dans le tableau de bord

Le tableau de bord dans la solution de surveillance à distance vous permet d’afficher la télémétrie d’envoyer vos périphériques au concentrateur de IoT.

1. Dans votre navigateur, revenir à la console de solution de surveillance à distance, cliquez sur **périphériques** dans le panneau de gauche pour accéder à la **liste des périphériques**.

2. Dans la **liste des périphériques**, vous devez voir que l’état de votre périphérique est maintenant **en cours d’exécution**.

    ![][18]

3. Cliquez sur le **tableau de bord** pour revenir au tableau de bord, sélectionnez votre périphérique dans le **périphérique d’affichage** vers le bas pour afficher sa télémétrie. La télémétrie à partir de l’exemple d’application est de 50 unités de température interne, 55 unités pour la température externe et 50 unités de l’humidité. Notez que par défaut, le tableau de bord affiche uniquement les valeurs de température et d’humidité.

    ![][img-telemetry]

## <a name="send-a-command-to-your-device"></a>Envoyer une commande à votre périphérique

Le tableau de bord dans la solution de surveillance à distance vous permet d’envoyer des commandes aux périphériques par l’intermédiaire de IoT concentrateur. Par exemple, dans la solution de surveillance à distance, vous pouvez envoyer une commande à la valeur de la température interne d’un périphérique.

1. Dans la console solution de surveillance à distance, cliquez sur **périphériques** dans le panneau de gauche pour accéder à la **liste des périphériques**.

2. Cliquez sur **ID de périphérique** pour votre périphérique dans la **liste des périphériques**.

3. Dans le panneau **informations sur le périphérique** , cliquez sur **commandes**.

    ![][13]

4. Dans la liste déroulante **commande** , sélectionnez **SetTemperature**et entrez une nouvelle valeur de température de **température** . Cliquez sur la **commande Send** pour envoyer la commande au périphérique.

    ![][14]

    > [AZURE.NOTE] L’historique des commandes affiche initialement l’état de la commande en **attente**. Lorsque le périphérique reconnaît la commande, le statut passe à **succès**.

5. Sur le tableau de bord, vérifiez que le périphérique envoie désormais 75 comme nouvelle valeur de température.

## <a name="next-steps"></a>Étapes suivantes

L’article [personnalisation préconfiguré de solutions] [ lnk-customize] décrit des méthodes que vous pouvez étendre cet exemple. Extensions possibles incluent l’utilisation de capteurs réels et de mise en œuvre des commandes supplémentaires.

Pour en savoir plus sur les [autorisations sur le site azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
