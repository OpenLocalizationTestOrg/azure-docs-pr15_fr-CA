<properties 
    pageTitle="Restauration d’une application dans Azure" 
    description="Découvrez comment restaurer votre application à partir d’une sauvegarde." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/06/2016" 
    ms.author="cephalin"/>

# <a name="restore-an-app-in-azure"></a>Restauration d’une application dans Azure

Cet article vous explique comment restaurer une application dans [Azure Application Service](../app-service/app-service-value-prop-what-is.md) que vous avez précédemment sauvegardé (voir la section [sauvegarder votre application dans Azure](web-sites-backup.md)). Vous pouvez restaurer votre application avec ses bases de données liées (de la base de données SQL ou MySQL) à la demande à un état antérieur, ou créer une nouvelle application à partir de la sauvegarde d’origine de votre application. Création d’une nouvelle application qui s’exécute en parallèle avec la version la plus récente peut être utile pour un tests de B.

La restauration à partir de sauvegardes est disponible pour les applications en cours d’exécution dans la couche **Standard** et **Premium** . Pour plus d’informations sur l’évolution verticale de votre application, reportez-vous à la section [mise à l’échelle d’une application dans Azure](web-sites-scale.md). Niveau de **prime** permet un plus grand nombre de sauvegardes quotidiennes à exécuter à un niveau **Standard** .

<a name="PreviousBackup"></a>
## <a name="restore-an-app-from-an-existing-backup"></a>Restauration d’une application à partir d’une sauvegarde existante

1. Sur la lame de **paramètres** de votre application dans Azure Portal, cliquez sur **sauvegarde** pour afficher la lame de **sauvegardes** . Puis cliquez sur **Restaurer maintenant** dans la barre de commandes. 
    
    ![Choisissez Restaurer maintenant][ChooseRestoreNow]

3. À la lame de **restauration** , sélectionnez la source de sauvegarde. 

    ![](./media/web-sites-restore/021ChooseSource.png)
    
    L’option de **sauvegarde d’application** affiche toutes les sauvegardes existantes de l’application en cours, et vous pouvez facilement sélectionner un. 
    L’option de **stockage** vous permet de sélectionner un fichier ZIP de sauvegarde à partir de n’importe quel compte de stockage Azure et un conteneur dans votre abonnement existant. 
    Si vous essayez de restaurer une sauvegarde d’une autre application, utilisez l’option de **stockage** .

4. Ensuite, spécifiez la destination de la restauration de l’application dans la **destination de restauration**.

    ![](./media/web-sites-restore/022ChooseDestination.png)
    
    >[AZURE.WARNING] Si vous choisissez **Remplacer**, toutes les données existantes dans votre application en cours seront effacées. Avant de cliquer sur **OK**, vérifiez qu’il est exactement ce que vous voulez faire.
    
    Vous pouvez sélectionner **Une application existante** pour restaurer la sauvegarde de l’application à une autre application dans le même groupe de ressource. Avant d’utiliser cette option, vous auriez déjà dû créer une application dans votre groupe de ressources avec mise en miroir de base de données configuration à celui défini dans la sauvegarde de l’application. 
    
5. Cliquez sur **OK**.

<a name="StorageAccount"></a>
## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Télécharger ou supprimer une sauvegarde à partir d’un compte de stockage
    
1. À partir de la main **Parcourir** la lame du portail Azure, sélectionnez **comptes de stockage**.
    
    Une liste de vos comptes de stockage existant s’affiche. 
    
2. Sélectionnez le compte de stockage contenant la sauvegarde que vous souhaitez télécharger ou supprimer.
    
    La lame pour le compte de stockage s’affiche.

3. De la lame d’accountn de stockage, sélectionnez le conteneur
    
    ![Afficher les conteneurs][ViewContainers]

4. Sélectionnez le fichier de sauvegarde à télécharger ou à supprimer.

    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)

5. Cliquez sur **Télécharger** ou **Supprimer** , selon ce que vous voulez faire.  

<a name="OperationLogs"></a>
## <a name="monitor-a-restore-operation"></a>Surveiller une opération de restauration
    
1. Pour plus de détails sur la réussite ou l’échec de l’opération de restauration d’application, accédez à la lame de **Journal d’Audit** dans le portail Azure. 
    
    La lame de **journaux d’Audit** affiche toutes vos opérations, ainsi que le niveau, l’état, ressources et détails du temps.
    
2. Faites défiler la liste pour trouver, l’opération de restauration souhaité puis cliquez sur pour le sélectionner.

La lame de détails affiche les informations disponibles relatives à l’opération de restauration.
    
## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également de sauvegarde et de restauration des applications de Service d’application à l’aide des API REST (voir [Reste utiliser pour sauvegarder et restaurer des applications de Service d’application](websites-csm-backup.md)).

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
 
