<!--author=SharS last changed: 01/15/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>Pour installer la mise à jour 1.2 à partir du portail classique Azure

1. Sur la page service de StorSimple, sélectionnez votre périphérique. Accédez à des **périphériques** > **Maintenance**.

2. En bas de la page, cliquez sur **Rechercher les mises à jour**. Un projet est créée pour rechercher les mises à jour disponibles. Vous serez averti lorsque la tâche est terminée.

3. Dans la section **Mises à jour logicielles** sur la même page, vous verrez que les nouvelles mises à jour de logiciels sont disponibles. Nous vous conseillons d’étudier les notes de version avant de l’appliquer 1.2 de mise à jour sur votre appareil.

    ![Installer les mises à jour logicielles](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)

4. En bas de la page, cliquez sur **Installer les mises à jour**.

5. Vous serez invité à confirmer. Cliquez sur **OK**.

6. Une boîte de dialogue **Installer les mises à jour** est présentée. Votre appareil doit satisfaire les contrôles répertoriés dans cette boîte de dialogue. Ces étapes ont été effectuées avant la mise à jour. Sélectionnez **je comprendre l’exigence ci-dessus et suis prêt à mettre à jour mon périphérique**. Cliquez sur l’icône de vérification.

    ![Message de confirmation](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)

7. Un ensemble de Pre-contrôles de automatiques va démarrer. Celles-ci comprennent :

    - **Contrôleur de vérifications** pour vérifier que les deux contrôleurs de périphérique sont en bonne santé et en ligne.
    
    - **Bilans de santé de composant matériel** pour vérifier que tous les composants matériels de votre périphérique StorSimple sont en bon état.
    
    - **Recherche de données 0** pour vérifier que les données 0 sont activées sur votre appareil. Si cette interface n’est pas activée, vous devez l’activer, puis réessayez.
    
    - **Données 2 et données 3 vérifie** que les interfaces de réseau de données 2 et données 3 ne sont pas activés. Si ces interfaces sont activées, puis vous devez les désactiver et essayez de mettre à jour votre périphérique. Ce contrôle est effectué uniquement si vous mettez à jour à partir d’un périphérique exécutant le logiciel GA. Les périphériques qui exécutent des versions 0.1, 0,2 et 0,3 n’ont pas cette vérification.
    
    - **Vérification de la passerelle** sur n’importe quel périphérique qui exécute une version antérieure à la mise à jour 1. Cette vérification est effectuée sur tous les périphériques exécutant le logiciel préliminaire de mise à jour 1 mais ne fonctionne pas sur les périphériques qui ont une passerelle est configurée pour une interface réseau autres que les données 0.
 
    Mise à jour 1.2 n’être appliquée que si toutes les vérifications de mise à jour préalable ci-dessus sont terminées avec succès. Vous serez averti que des vérifications préalables de mises à jour sont en cours.
  
    ![Notification de la vérification préalable](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)

    Voici un exemple dans lequel la vérification de pré-mise à niveau a échoué. Vous devez vérifier que les deux contrôleurs de périphérique sont en bonne santé et en ligne. Vous devrez également vérifier l’état des composants matériels. Dans cet exemple, le contrôleur 0 et composants de contrôleur 1 une attention. Vous devrez peut-être contacter le Support Microsoft si vous ne pouvez pas régler ces problèmes par vous-même.

     ![Échoué de la vérification préalable](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

    > [AZURE.NOTE] Après avoir appliqué 1.2 de mise à jour de votre périphérique StorSimple, contrôles de données 2 et données 3 et la vérification de la passerelle ne sera plus nécessaires pour les mises à jour futures. Autres vérifications préalables sera requises.


8. Une fois les vérifications préliminaires sont terminées avec succès, une tâche de mise à jour sera créée. Vous serez averti lorsque la tâche de mise à jour est créée avec succès.
 
    ![Mise à jour de la création d’emplois](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)

    La mise à jour sera alors appliquée sur votre périphérique.
 
9. Pour surveiller la progression de la tâche de mise à jour, cliquez sur **Afficher la tâche**. Dans la page **tâches** , vous pouvez voir la progression de la mise à jour. 

    ![Mise à jour de la progression du projet](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)

10. La mise à jour prendra quelques heures. Vous pouvez afficher les détails de la tâche à tout moment.

    ![Mettre à jour les détails de la tâche](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)

11. Une fois la tâche terminée, accédez à la page de **Maintenance** et faites défiler jusqu'à **Mises à jour logicielles**.

12. Vérifiez que votre périphérique est en cours d’exécution **StorSimple 8000 série de mise à jour 1.2 (6.3.9600.17584)**. La **dernière mise à jour de la date** doit être modifié.

    ![Page de maintenance](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)

13. Vous verrez maintenant que les mises à jour du mode de Maintenance sont disponibles. Ces mises à jour sont mises à jour à l’interruption de l’activité d’interruption du périphérique et peuvent être appliquées uniquement via l’interface Windows PowerShell de votre périphérique. Suivez les instructions dans [l’installation de mises à jour du mode de maintenance](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple) pour installer ces mises à jour via le Windows PowerShell pour StorSimple.

> [AZURE.NOTE] Dans certains cas, le message indiquant les mises à jour du mode de maintenance peut s’afficher jusqu'à 24 heures après que les mises à jour du mode de maintenance sont appliquées avec succès sur l’appareil.  


