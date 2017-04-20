## <a name="create-an-iot-hub"></a>Créer un concentrateur IoT

Créer un concentrateur IoT pour votre périphérique simulé pour se connecter à. Les étapes suivantes vous montrent comment effectuer cette tâche à l’aide du portail Azure.

1. Connectez-vous au [portail Azure][lnk-portal].

2. Dans le Jumpbar, cliquez sur **Nouveau** > **Internet des objets** > **Azure IoT concentrateur**.

    ![Azure portal Jumpbar][1]

3. Dans la lame **IoT concentrateur** , choisissez la configuration de votre concentrateur IoT.

    ![Lame de concentrateur IoT][2]

    * Dans la zone **nom** , entrez un nom pour votre concentrateur IoT. Si le **nom** est valide et disponible, une coche verte s’affiche dans la zone **nom** .
    * Sélectionnez un [niveau de prix et l’échelle]de l'[lnk-pricing]. Ce didacticiel ne nécessite pas un niveau spécifique. Pour ce didacticiel, utilisez le niveau de F1 libre.
    * Dans le **groupe de ressources**, créez un nouveau groupe de ressources, ou sélectionnez-en un. Pour plus d’informations, reportez-vous à la section [utilisation des groupes de ressources à gérer vos ressources Azure][lnk-resource-groups].
    * Dans **emplacement**, sélectionnez l’emplacement pour héberger votre concentrateur IoT. Pour ce didacticiel, choisissez l’emplacement le plus proche.

4. Lorsque vous avez choisi votre concentrateur IoT d’options de configuration, cliquez sur **créer**.  Il peut prendre quelques minutes pour Azure créer votre concentrateur IoT. Pour vérifier l’état, vous pouvez surveiller la progression de la Startboard ou dans le panneau de Notifications.

    ![Nouveau statut de concentrateur IoT][3]

5. Lorsque le concentrateur IoT a été créé avec succès, cliquez sur la mosaïque pour votre concentrateur IoT dans le portail Azure pour ouvrir la lame pour le nouveau concentrateur IoT. Notez le **nom d’hôte**, puis cliquez sur **stratégies d’accès partagé**.

    ![Nouvelle lame de concentrateur IoT][4]

6. De la lame de **stratégies d’accès partagé** , cliquez sur la stratégie de **iothubowner** , puis copiez et prenez note de la chaîne de connexion dans la lame de **iothubowner** . Pour plus d’informations, consultez [contrôle d’accès] [ lnk-access-control] dans le « guide de développeur Azure IoT Hub ».

    ![Lame de stratégies d’accès partagé][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
