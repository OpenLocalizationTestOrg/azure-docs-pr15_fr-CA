## <a name="create-an-event-hub"></a>Créer un concentrateur d’événements

1. Ouvrez une session sur le [portail Azure][]et cliquez sur **Nouveau** dans la partie supérieure gauche de l’écran.

2. Cliquez sur **données + Analytique**, puis cliquez sur **Événement concentrateurs**.

    ![](./media/event-hubs-create-event-hub/create-event-hub9.png)

3. Dans la lame de **l’espace de noms de créer** , entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.

    ![](./media/event-hubs-create-event-hub/create-event-hub1.png)

4. Une fois que le nom de l’espace de noms est disponible, choisissez le niveau de prix (de base ou Standard). En outre, choisissez un abonnement Azure, le groupe de ressources et l’emplacement dans lequel créer la ressource. 

2. Cliquez sur **créer** pour créer l’espace de noms.

6. Dans la liste espace de noms de concentrateurs de l’événement, cliquez sur l’espace de noms qui vient d’être créé.      

    ![](./media/event-hubs-create-event-hub/create-event-hub2.png)

7. Dans l’espace de noms « lame », cliquez sur **Événement concentrateurs**.

    ![](./media/event-hubs-create-event-hub/create-event-hub3.png)

8. En haut de la lame, cliquez sur **Ajouter un concentrateur événement**.

    ![](./media/event-hubs-create-event-hub/create-event-hub4.png)

3. Tapez un nom pour votre concentrateur d’événements, puis cliquez sur **créer**.

    ![](./media/event-hubs-create-event-hub/create-event-hub5.png)

4. Dans la liste des événements, cliquez sur le nom de concentrateur d’événements qui vient d’être créé. 

    ![](./media/event-hubs-create-event-hub/create-event-hub6.png)

5. Dans l’espace de noms blade (pas la lame concentrateur d’événements spécifique), cliquez sur **stratégies d’accès partagé**, puis cliquez sur **RootManageSharedAccessKey**.

    ![](./media/event-hubs-create-event-hub/create-event-hub7.png)

5. Cliquez sur le bouton Copier pour copier la chaîne de connexion **RootManageSharedAccessKey** dans le Presse-papiers. Enregistrer cette chaîne de connexion à utiliser, plus loin dans le didacticiel.

    ![](./media/event-hubs-create-event-hub/create-event-hub8.png)

Votre concentrateur d’événements est créé et que les chaînes de connexion que vous avez besoin d’envoyer et de recevoir des événements.

[Azure portal]: https://portal.azure.com/