<properties 
    pageTitle="Les Services BizTalk à l’aide de journaux des opérations de dépannage | Microsoft Azure" 
    description="Résoudre les problèmes des Services BizTalk à l’aide de journaux des opérations. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>


# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>Les Services BizTalk : Résolution des problèmes liés à l’aide de journaux des opérations

## <a name="what-are-the-operation-logs"></a>Que sont les journaux
Journaux d’opération est une fonctionnalité de Services de gestion disponible dans Azure portal classique qui vous permet d’afficher l’historique des journaux des opérations effectuées sur vos services Azure, y compris les Services BizTalk. Cela vous permet de consulter les données historiques associées aux opérations de gestion de votre abonnement de BizTalk Service jusqu'à 180 jours.

> [AZURE.NOTE]Cette fonctionnalité capture uniquement les journaux pour les opérations de gestion sur les Services BizTalk, par exemple lorsque le démarrage du service, de secours, et ainsi de suite. Ces opérations sont suivies indépendamment de si elles sont exécutées à partir du portail classique Azure, ou à l’aide de l' [API du Service de BizTalk reste](http://msdn.microsoft.com/library/azure/dn232347.aspx). Pour obtenir une liste complète des opérations qui sont suivies à l’aide des Services de gestion, consultez [Opérations de suivi à l’aide de gestion des Services Azure](#bizops).<br/><br/>
Il ne capture pas les journaux d’activités liées à l’exécution du BizTalk Service (par exemple, les messages traités par des ponts, etc.).. Pour afficher ces fichiers journaux, utilisez le mode de suivi à partir du portail de Services de BizTalk. Pour plus d’informations, reportez-vous à la section [De suivi des Messages](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## <a name="view-biztalk-services-operation-logs"></a>Affichage des journaux de fonctionnement des Services BizTalk
1. Dans le portail Azure classique, sélectionnez les **Services de gestion**et puis sélectionnez l’onglet **Journaux** .
2. Vous pouvez filtrer les journaux en fonction de différents paramètres comme abonnement, plage de dates, le type de service (par exemple, les Services BizTalk), nom du service ou l’état de l’opération (réussite, échec).
3. Activez la case à cocher pour afficher la liste filtrée. L’image suivante montre les activités liées à la testbiztalkservice :  ![afficher les journaux d’opération][ViewLogs] 
4. Pour visualiser davantage de données sur une opération spécifique, sélectionnez la ligne et cliquez sur **Détails** dans la barre des tâches en bas.


## <a name="bizops"></a>Opérations suivies à l’aide des Services de gestion d’Azure
Le tableau suivant répertorie les opérations suivies en utilisant les Services de gestion d’Azure :

Nom de l’opération | Tâche
--- | ---
CreateBizTalkService | Opération de création d’un nouveau BizTalk Service
DeleteBizTalkService | Opération de suppression d’un BizTalk Service
RestartBizTalkService | Opération de redémarrage d’un BizTalk Service
StartBizTalkService | Opération pour démarrer un BizTalk Service
StopBizTalkService | Opération d’arrêt d’un BizTalk Service
DisableBizTalkService | Opération pour désactiver un BizTalk Service
EnableBizTalkService | Opération pour activer un BizTalk Service
BackupBizTalkService | Opération de sauvegarde d’un BizTalk Service
RestoreBizTalkService | Opération de restauration d’un BizTalk Service à partir de la sauvegarde spécifiée
SuspendBizTalkService | Opération de suspension d’un BizTalk Service
ResumeBizTalkService | Opération pour reprendre un BizTalk Service
ScaleBizTalkService | Opération de mise à l’échelle d’un BizTalk Service vers le haut ou vers le bas
ConfigUpdateBizTalkService | Opération de mise à jour de la configuration d’un BizTalk Service
ServiceUpdateBizTalkService | Opération de mise à niveau ou déclasser un BizTalk Service à une version différente
PurgeBackupBizTalkService | Opération de purge du BizTalk Service en dehors de la période de rétention des sauvegardes


## <a name="see-also"></a>Voir aussi
- [Service de sauvegarde de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurer le Service BizTalk à partir de la sauvegarde](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Les Services BizTalk : Développeur, basique, Standard et Premium éditions graphique](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Les Services BizTalk : Mise en service de portail de classique à l’aide de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Services de BizTalk : Graphique de l’état de mise en service](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Services de BizTalk : Onglets de tableau de bord, du moniteur et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Les Services BizTalk : limitation](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Les Services BizTalk : Nom de l’émetteur et la clé de l’émetteur](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Comment faire démarrer à l’aide du SDK des Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 
