## <a name="create-a-device-management-enabled-iot-hub"></a>Créer une unité de gestion activée IoT concentrateur

Gestion des périphériques IoT concentrateur étant dans l’aperçu, vous devez créer un périphérique activé IoT véritable tour de contrôle. Lors de la gestion des périphériques IoT concentrateur atteint la disponibilité générale, ce didacticiel sera mis à jour. Les étapes suivantes vous montrent comment effectuer cette tâche à l’aide du portail Azure.

1.  Connectez-vous au [portail Azure].
2.  Dans le Jumpbar, cliquez sur **Nouveau**, puis cliquez sur **Internet des objets**, puis cliquez sur **Azure IoT concentrateur**.

    ![][img-new-hub]

3.  Dans la lame **IoT concentrateur** , choisissez la configuration de votre concentrateur IoT.

    ![][img-configure-hub]

  -   Dans la zone **nom** , entrez un nom pour votre concentrateur IoT. Si le **nom** est valide et disponible, une coche verte s’affiche dans la zone **nom** .
  -   Sélectionnez un **niveau de tarification et d’échelle**. Ce didacticiel ne nécessite pas un niveau spécifique.
  -   Dans le **groupe de ressources**, créez un nouveau groupe de ressources, ou sélectionnez-en un. Pour plus d’informations, consultez [utilisation des groupes de ressources à gérer vos ressources Azure].
  -   Cochez la case pour **Activer la gestion des périphériques - aperçu**.
  -   Dans **emplacement**, sélectionnez l’emplacement pour héberger votre concentrateur IoT. Gestion des périphériques IoT Hub n’est disponible dans les États-Unis, l’Europe du Nord et Asie de l’est au cours de la présentation publique.

    > [AZURE.NOTE]Si vous ne cochez pas cette case pour **Activer la gestion des périphériques**, les exemples ne fonctionnent pas.<br/>En vérifiant les **Activer la gestion des périphériques**, vous créez un aperçu IoT concentrateur pris en charge uniquement par les États-Unis, l’Europe du Nord et Asie de l’est et non destinés à des scénarios de production. Périphériques ne peut pas migrer d’et vers des concentrateurs de gestion activée de périphérique.

4.  Lorsque vous avez choisi votre concentrateur IoT d’options de configuration, cliquez sur **créer**. Il peut prendre quelques minutes pour Azure créer votre concentrateur IoT. Pour vérifier l’état, vous pouvez surveiller la progression de la **Startboard** ou dans le panneau de **Notifications** .

    ![][img-monitor]

5.  Lorsque le concentrateur IoT a été créé avec succès, la lame pour votre concentrateur s’ouvre automatiquement. Notez le **nom d’hôte**, puis cliquez sur **stratégies d’accès partagé**.

    ![][img-keys]

6.  Cliquez sur la stratégie de **iothubowner** , puis copier et prenez note de la chaîne de connexion dans la lame de **iothubowner** . Copier dans un emplacement que vous pouvez accéder à une version ultérieure, car vous en avez besoin pour compléter ce didacticiel.

    > [AZURE.NOTE] Dans les scénarios de production, assurez-vous de s’abstenir d’utiliser les informations d’identification de **iothubowner** .

    ![][img-connection]

Vous avez maintenant créé un dispositif de gestion activée IoT concentrateur. Vous avez besoin pour terminer ce didacticiel, la chaîne de connexion.

## <a name="create-a-device-identity"></a>Création d’une identité de périphérique

Dans cette section, vous utilisez un outil de nœud [IoT concentrateur] Explorateur[ iot-hub-explorer] pour créer une identité de périphérique pour ce didacticiel.

1. Dans votre environnement de ligne de commande, exécutez la procédure suivante :

    npm installation -giothub-explorer@latest

2. Ensuite, exécutez la commande suivante pour vous connecter à votre concentrateur, n’oubliez pas de remplacer `{service connection string}` avec la chaîne de connexion IoT Hub que vous avez copié précédemment :

    connexion d’iothub-explorer « {chaîne de connexion service} »

3. Enfin, créez une nouvelle identité de périphérique appelée `myDeviceId` avec la commande :

    iothub-explorer créer myDeviceId--chaîne de connexion

Prenez note de la chaîne de connexion de périphérique à partir du résultat. Cette chaîne de connexion est utilisée par l’application de périphérique pour se connecter à votre concentrateur IoT en tant que périphérique.

![][img-identity]

Reportez-vous à la [mise en route avec IoT concentrateur] [ lnk-getstarted] pour un moyen de créer par programme des identités des appareils.

<!-- images and links -->
[img-new-hub]: media/iot-hub-get-started-create-hub-pp/image1.png
[img-configure-hub]: media/iot-hub-get-started-create-hub-pp/image2.png
[img-monitor]: media/iot-hub-get-started-create-hub-pp/image3.png
[img-keys]: media/iot-hub-get-started-create-hub-pp/image4.png
[img-connection]: media/iot-hub-get-started-create-hub-pp/image5.png
[img-identity]: media/iot-hub-get-started-create-hub-pp/devidentity.png

[Azure portal]: https://portal.azure.com/
[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
[À l’aide de groupes de ressources à gérer vos ressources Azure]: ../articles/azure-portal/resource-group-portal.md
