<properties 
   pageTitle="Permet d’afficher et de gérer les alertes de tableau virtuel de StorSimple | Microsoft Azure"
   description="Décrit les conditions d’alerte tableau virtuel de StorSimple et de la gravité et comment utiliser le service Gestionnaire de StorSimple pour gérer les alertes."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-alerts-for-the-storsimple-virtual-array"></a>Le service Gestionnaire de StorSimple permet d’afficher et de gérer les alertes pour le tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

L’onglet **alertes** dans le service Gestionnaire de StorSimple permet de passer en revue et désactivez les alertes liées tableau virtuel de StorSimple en temps réel. À partir de cet onglet, vous pouvez surveiller de façon centralisée les problèmes de santé de vos baies virtuel de StorSimple (également connu sous le nom StorSimple local périphériques virtuels) et la solution Microsoft Azure StorSimple globale.

Ce didacticiel explique comment configurer les notifications d’alerte, des conditions d’alerte courantes, les niveaux de gravité d’alerte et afficher et de suivi des alertes. En outre, il comprend des tables d’alerte de référence rapide, qui vous permettent de rapidement localiser une alerte spécifique et répondre de façon appropriée.

![Page d’alertes](./media/storsimple-ova-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurer les paramètres d’alerte

Vous pouvez choisir si vous souhaitez être averti par courrier électronique des conditions d’alerte pour chacun de vos périphériques virtuels StorSimple. En outre, vous pouvez identifier les autres destinataires de notification d’alerte en entrant leurs adresses de messagerie dans la zone **autres destinataires** , séparée par des points-virgules.

>[AZURE.NOTE] Vous pouvez entrer un maximum de 20 adresses e-mail par périphérique virtuel.

Après avoir activé la notification par e-mail pour un périphérique virtuel, les membres de la liste de notification recevront un message électronique chaque fois une alerte critique se produit. Les messages seront envoyés à partir de *storsimple-alerts-noreply@mail.windowsazure.com* et décrit la condition d’alerte. Destinataires cliquez sur **Annuler l’abonnement** pour se supprimer de la liste de notification de courrier électronique.

#### <a name="to-enable-email-notification-of-alerts-for-a-virtual-device"></a>Pour activer la notification par courrier électronique des alertes pour un périphérique virtuel

1. Accédez à des **périphériques** > **Configuration** du périphérique virtuel. Passez à la section **paramètres d’alerte** .

    ![paramètres d’alerte](./media/storsimple-ova-manage-alerts/alerts2.png)

2. Sous **paramètres d’alerte**, définissez les éléments suivants :

    1. Dans le champ **Envoyer la notification par e-mail** , sélectionnez **Oui**.

    2. Dans le champ **administrateurs du service de courrier électronique** , sélectionnez **Oui** si vous souhaitez que l’administrateur de service et les administrateurs de collaboration reçoivent les notifications d’alerte.

    3. Dans le champ **autres destinataires de l’e-mail** , entrez les adresses électroniques des autres destinataires qui doivent recevoir les notifications d’alerte. Entrez les noms au format *someone@somewhere.com*. Utilisez des points-virgules pour séparer les adresses de messagerie. Vous pouvez configurer un maximum de 20 adresses e-mail par périphérique virtuel. 

        ![configuration de notification d’alertes](./media/storsimple-ova-manage-alerts/alerts3.png)

3. En bas de la page, cliquez sur **Enregistrer** pour enregistrer votre configuration.

4. Pour envoyer une notification par courrier électronique de test, cliquez sur la flèche en regard de **e-mail de test d’envoi**. Le service Gestionnaire de StorSimple affiche les messages d’état comme il transmet la notification test. 

5. Lorsque le message suivant s’affiche, cliquez sur **OK**. 

    ![Alertes de notification par e-mail de test](./media/storsimple-ova-manage-alerts/alerts4.png)

    >[AZURE.NOTE] Si le message de notification de test ne peut pas être envoyé, le service Gestionnaire de StorSimple affiche un message approprié. Cliquez sur **OK**, patientez quelques minutes et réessayez d’envoyer votre message de notification de test. 

    Le message de notification de test sera semblable à la suivante.

    ![Exemple de courriel alertes test](./media/storsimple-ova-manage-alerts/alerts5.png)

## <a name="common-alert-conditions"></a>Conditions communes d’alerte

Votre tableau virtuel StorSimple génère des alertes en réponse à une variété de conditions. Les types les plus courants des conditions d’alerte sont les suivantes :

- **Problèmes de connectivité** – ces alertes se produisent lorsqu’il y a des difficultés à transférer des données. Les problèmes de communication peuvent se produire lors du transfert des données vers et à partir du compte de stockage Azure ou en raison de l’absence de connectivité entre les périphériques virtuels et le service Gestionnaire de StorSimple. Problèmes de communication sont les plus difficiles à résoudre, car il existe de nombreux points de défaillance. Vous devez toujours tout d’abord vérifier que la connectivité réseau et accès à Internet sont disponibles avant de passer à la résolution des problèmes plus avancées. Pour plus d’informations sur les ports et les paramètres de pare-feu, accédez à [StorSimple Virtual Array Configuration requise](storsimple-ova-system-requirements.md). Pour obtenir une aide au dépannage, consultez pour [résoudre des problèmes avec l’applet de commande Test-connexion](storsimple-troubleshoot-deployment.md).

- **Problèmes de performances** – ces alertes sont provoquées lorsque votre système n’est pas optimale, notamment lorsqu’il est soumis à une charge importante.

En outre, vous pouvez voir les alertes liées à la sécurité, des mises à jour ou des échecs des tâches.

## <a name="alert-severity-levels"></a>Niveaux de gravité d’alerte

Alertes ont différents niveaux de gravité, en fonction de l’impact de la situation d’alerte et de la nécessité d’une réponse à l’alerte. Les niveaux de gravité sont les suivants :

- Il est **critique** : cette alerte en réponse à une condition qui affecte les performances réussie de votre système. Action est nécessaire pour s’assurer que le StorSimple de service n’est pas interrompue.

- **Avertissement** – cette situation pourrait devenir critique si ne pas résolu. Vous devez examiner la situation et exécuter toute action requise pour supprimer le problème.

- **Informations** – cette alerte contient des informations qui peuvent être utiles pour le suivi et la gestion de votre système.

## <a name="view-and-track-alerts"></a>Permet d’afficher et d’effectuer le suivi des alertes

Le tableau de bord de service Gestionnaire de StorSimple vous offre un coup de œil sur le nombre d’alertes sur vos périphériques virtuels, organisées par niveau de gravité.

![Tableau de bord alertes](./media/storsimple-ova-manage-alerts/alerts6.png)

Cliquez sur le niveau de gravité pour ouvrir l’onglet **alertes** . Les résultats comprennent uniquement les alertes qui correspondent à ce niveau de gravité.

![État des alertes une portée limitée à un type d’alerte](./media/storsimple-ova-manage-alerts/alerts7.png)

Cliquer sur une alerte dans la liste vous fournit des détails supplémentaires pour l’alerte, y compris la dernière fois que l’alerte a été signalée, le nombre d’occurrences de l’alerte sur le périphérique et l’action recommandée pour résoudre l’alerte.

Vous pouvez copier les détails de l’alerte dans un fichier texte si vous avez besoin envoyer des informations au Support Microsoft. Après avoir suivi la recommandation et résoudre la condition d’alerte local, vous devez désactiver l’alerte à partir de l’alerte dans l’onglet **alertes** , en cliquant sur **Effacer**. Pour effacer plusieurs alertes, sélectionnez chaque alerte, cliquez sur n’importe quelle colonne, à l’exception de la colonne de **l’alerte** , puis cliquez sur **Effacer** après avoir sélectionné toutes les alertes à effacer. Notez que certaines alertes sont automatiquement désactivées lorsque le problème est résolu ou lorsque le système met à jour l’alerte avec de nouvelles informations.

Lorsque vous cliquez sur **Effacer**, avoir la possibilité de fournir des commentaires sur l’alerte et les étapes que vous avez prises pour résoudre le problème. 

![commentaires de l’alertes](./media/storsimple-ova-manage-alerts/clear-alert.png)

Cliquez sur l’icône de vérification ![icône de la vérification](./media/storsimple-ova-manage-alerts/check-icon.png) Pour enregistrer vos commentaires.

Certains événements seront effacés par le système si un autre événement est déclenché avec les nouvelles informations. Dans ce cas, vous verrez le message suivant.

![Message d’alerte effacer](./media/storsimple-ova-manage-alerts/alerts8.png)

## <a name="sort-and-review-alerts"></a>Tri et révision des alertes

L’onglet **alertes** peut afficher jusqu'à 250 alertes. Si vous avez dépassé le nombre correspondant d’alertes, pas toutes les alertes seront affichera dans la vue par défaut. Vous pouvez combiner les champs suivants pour personnaliser les alertes sont affichent :

- **Statut** : vous pouvez afficher les alertes **actives** ou **effacé** . Active les alertes sont toujours en cours déclenchées sur votre système, tandis que les alertes effacées ont été effacés manuellement par un administrateur ou d’effacer par programme car le système de mise à jour de la condition d’alerte avec les nouvelles informations.

- **Gravité** – vous pouvez afficher les alertes de tous les niveaux de gravité (critiques, avertissements, informations), ou simplement une certaine gravité, telles que les alertes critiques uniquement.

- **Source** – vous pouvez afficher les alertes de toutes les sources, ou limiter les alertes à ceux provoqués par le service ou d’un ou de tous les périphériques virtuels.

- **Plage de temps** – en spécifiant les dates **** d’et **à** et les horodatages, vous pouvez regarder alertes pendant la période qui vous intéressez.

## <a name="alerts-quick-reference"></a>Référence rapide des alertes

Les tableaux suivants répertorient quelques-uns des alertes Microsoft Azure StorSimple que vous pouvez rencontrer, ainsi que des recommandations et des informations supplémentaires le cas échéant. Alertes de périphérique virtuel StorSimple entrent dans une des catégories suivantes :

- [Alertes de connectivité de nuage](#cloud-connectivity-alerts)

- [Alertes de configuration](#configuration-alerts)

- [Alertes d’échec de tâche](#job-failure-alerts)

- [Alertes de performances](#performance-alerts)

- [Alertes de sécurité](#security-alerts)

- [Alertes de mise à jour](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertes de connectivité de nuage

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|DISPOSITIF *<device name>* n’est pas connecté au nuage.|Le périphérique nommé ne peut pas se connecter au nuage. |N’a pas pu se connecter au nuage. Cela peut être dû à l’une des opérations suivantes :<ul><li>Il peut y avoir un problème avec les paramètres réseau de votre périphérique.</li><li>Il peut y avoir un problème avec les informations d’identification du compte de stockage.</li></ul>Pour plus d’informations sur la résolution des problèmes de connectivité, accédez à l' [interface utilisateur web de local](storsimple-ova-web-ui-admin.md) du périphérique.|


### <a name="configuration-alerts"></a>Alertes de configuration

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Configuration de périphérique virtuel local non pris en charge.|Ralentissement des performances.|La configuration actuelle peut entraîner une dégradation des performances. Assurez-vous que votre serveur répond à la configuration minimale requise. Pour plus d’informations, accédez aux [Exigences de tableau virtuel StorSimple](storsimple-ova-system-requirements.md). 
|Vous exécutez manque d’espace disque configuré sur <*nom*>.|Avertissement d’espace disque.|Vous manquez d’espace disque mis en service. Pour libérer de l’espace, envisagez de déplacer des charges de travail sur un autre volume ou partage ou suppression de données.

### <a name="job-failure-alerts"></a>Alertes d’échec de tâche

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Sauvegarde de <*nom*> n’a pas pu être terminée.|Échec de l’opération de sauvegarde.|N’a pas pu créer une sauvegarde. Envisagez l’une des opérations suivantes :<ul><li>Problèmes de connectivité peuvent empêcher l’opération de sauvegarde de se terminer avec succès. Assurez-vous qu’il n’y a aucun problème de connectivité. Pour plus d’informations sur la résolution des problèmes de connectivité, accédez à l' [interface utilisateur web de local](storsimple-ova-web-ui-admin.md) pour votre périphérique virtuel.</li><li>Vous avez atteint la limite de stockage disponible. Pour libérer de l’espace, supprimez toutes les sauvegardes qui ne sont plus nécessaires.</li></ul> Résoudre les problèmes, désactivez l’alerte et recommencez l’opération.|
|Restauration de <*nom*> n’a pu être terminée.|Restaurer une tâche a échoué.|N’a pas pu restaurer à partir de la sauvegarde. Envisagez l’une des opérations suivantes :<ul><li>Sauvegarde de la liste n’est peut-être pas valide. Actualiser la liste pour vérifier qu’il est toujours valide.</li><li>Problèmes de connectivité peuvent empêcher l’opération de restauration de terminer avec succès. Assurez-vous qu’il n’y a aucun problème de connectivité.</li><li>Vous avez atteint la limite de stockage disponible. Pour libérer de l’espace, supprimez toutes les sauvegardes qui ne sont plus nécessaires.</li></ul>Résoudre les problèmes, désactivez l’alerte et recommencez l’opération de restauration.|
|Clone de <*nom*> n’a pu être terminée.|Dupliquer la tâche a échoué.|N’a pas pu créer un clone. Envisagez l’une des opérations suivantes :<ul><li>Sauvegarde de la liste n’est peut-être pas valide. Actualiser la liste pour vérifier qu’il est toujours valide.</li><li>Problèmes de connectivité peuvent empêcher l’opération de clonage de terminer avec succès. Assurez-vous qu’il n’y a aucun problème de connectivité.</li><li>Vous avez atteint la limite de stockage disponible. Pour libérer de l’espace, supprimez toutes les sauvegardes qui ne sont plus nécessaires.</li></ul>Résoudre les problèmes, désactivez l’alerte et recommencez l’opération.|

### <a name="performance-alerts"></a>Alertes de performances

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Vous rencontrez des retards inattendus dans le transfert de données.|Transfert de données lentes.|Limitation des erreurs se produisent lorsque vous dépassez les cibles de l’évolutivité d’un service de stockage. Le service de stockage pour cela pour vous assurer qu’aucun client unique ou le locataire ne peut utiliser le service au détriment des autres. Pour plus d’informations sur le dépannage de votre compte de stockage Azure, accédez à [surveiller, diagnostiquer et de résoudre les problèmes de stockage de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).
|Vous manquez d’espace disque local réservation <*nom_périphérique*>.|Temps de réponse lents.|10 % de la taille totale et mis en service pour <*nom_périphérique*> est réservée sur le périphérique local et est désormais insuffisante sur l’espace réservé. La charge de travail <*nom de périphérique*> génère un taux plus élevé d’évolution du ou peut avoir récemment fait migrer une grande quantité de données. Cela peut entraîner une baisse des performances. Envisagez l’une des actions suivantes pour résoudre ce problème :<ul><li>Augmenter la bande passante du nuage pour ce périphérique.</li><li>Réduisez ou déplacer les charges de travail sur un autre volume ou partage.</li></ul>

### <a name="security-alerts"></a>Alertes de sécurité

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|<*Nom_périphérique*> mot de passe va expirer dans <*nombre*> jours.|Avertissement du mot de passe.| Votre mot de passe va expirer dans < nombre < jours. Envisagez de changer votre mot de passe. Pour plus d’informations, allez à [changer le mot de passe administrateur de périphérique tableau virtuel de StorSimple](storsimple-ova-change-device-admin-password.md).

### <a name="update-alerts"></a>Alertes de mise à jour

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Nouvelles mises à jour sont disponibles pour votre périphérique.|Le tableau virtuel StorSimple mises à jour sont disponibles.|Vous pouvez installer les nouvelles mises à jour à partir de la page de **Maintenance** .|
|N’a pu analyser de nouvelles mises à jour sur <*nom*>.|Mise à jour de la défaillance. |Une erreur s’est produite lors de l’installation de nouvelles mises à jour. Vous pouvez installer manuellement les mises à jour. Si le problème persiste, contactez le [Support technique de Microsoft](storsimple-contact-microsoft-support.md).|


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le tableau virtuel StorSimple](storsimple-ova-overview.md).


