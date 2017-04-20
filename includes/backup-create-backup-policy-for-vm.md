## <a name="defining-a-backup-policy"></a>Définition d’une stratégie de sauvegarde

Une stratégie de sauvegarde définit une matrice de lorsque les données des instantanés et la durée pendant laquelle les snapshots sont conservés. Lors de la définition d’une stratégie de sauvegarde d’un ordinateur virtuel, vous pouvez déclencher un travail de sauvegarde *une fois par jour*. Lorsque vous créez une nouvelle stratégie, il est appliqué à la chambre forte. L’interface de stratégie de sauvegarde se présente comme suit :

![Stratégie de sauvegarde](./media/backup-create-policy-for-vms/backup-policy.png)

Pour créer une stratégie :

1. Entrez un nom pour le **nom de la stratégie**.

2. Instantanés de vos données peuvent être effectuées à des intervalles quotidienne ou hebdomadaire. Utilisez le menu déroulant **Fréquence de sauvegarde** pour choisir si données des instantanés quotidienne ou hebdomadaire.

    - Si vous choisissez un intervalle quotidien, utilisez le contrôle en surbrillance pour sélectionner l’heure du jour de la capture instantanée. Pour changer l’heure, annuler la sélection de l’heure, sélectionnez la nouvelle heure.

    ![Stratégie de sauvegarde quotidienne](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>

    - Si vous choisissez un intervalle hebdomadaire, utilisez les contrôles mis en surbrillance pour sélectionner l’ou les jours de la semaine et l’heure de la journée à prendre le snapshot. Dans le menu de la journée, sélectionnez un ou plusieurs jours. Dans le menu de l’heure, sélectionnez une heure. Pour changer l’heure, désélectionnez l’heure sélectionnée, sélectionnez la nouvelle heure.

    ![Stratégie de sauvegarde hebdomadaire](./media/backup-create-policy-for-vms/backup-policy-weekly.png)

3. Par défaut, toutes les options de **Durée de rétention** sont sélectionnées. Décochez la case aucune limite de plage de rétention que vous ne souhaitez pas utiliser. Ensuite, spécifiez l’interval(s) à utiliser.

    Mensuelle et annuelle de plages de rétention permettent de spécifier les instantanés selon un intervalle hebdomadaire ou quotidien.

    >[AZURE.NOTE] Lorsque vous protégez une machine virtuelle, une opération de sauvegarde s’exécute une fois par jour. L’exécution lorsque la sauvegarde est le même pour chaque plage de rétention.

4. Après avoir défini toutes les options de la stratégie, cliquez sur **Enregistrer**en haut de la lame.

    La nouvelle stratégie est immédiatement appliquée à la chambre forte.
