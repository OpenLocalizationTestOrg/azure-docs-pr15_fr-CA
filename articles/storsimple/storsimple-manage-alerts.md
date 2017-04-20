<properties 
   pageTitle="Permet d’afficher et de gérer les alertes de StorSimple | Microsoft Azure"
   description="Décrit les conditions d’alerte StorSimple et gravité, comment configurer les notifications d’alerte et comment utiliser le service Gestionnaire de StorSimple pour gérer les alertes."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="anbacker" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-alerts"></a>Le service Gestionnaire de StorSimple permet d’afficher et de gérer les alertes StorSimple

## <a name="overview"></a>Vue d’ensemble

L’onglet **alertes** dans le service Gestionnaire de StorSimple permet de passer en revue et effacer StorSimple les alertes liées aux périphériques en temps réel. À partir de cet onglet, vous pouvez surveiller de façon centralisée les problèmes de santé de vos périphériques de StorSimple et de la solution Microsoft Azure StorSimple globale.

Ce didacticiel explique comment configurer les notifications d’alerte, conditions communes d’alerte et des niveaux de gravité d’alerte. En outre, il comprend des tables d’alerte de référence rapide, qui vous permettent de rapidement localiser une alerte spécifique et répondre de façon appropriée.

![Page d’alertes](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## <a name="common-alert-conditions"></a>Conditions communes d’alerte

Votre périphérique StorSimple génère des alertes en réponse à une variété de conditions. Les types les plus courants des conditions d’alerte sont les suivantes :

- **Problèmes liés au matériel** – ces alertes vous informer de l’état de santé de votre matériel. Ils vous permettent de savoir si la mise à jour est nécessaires, et si une interface réseau rencontre des problèmes, ou s’il existe un problème avec l’un de vos lecteurs de données.

- **Problèmes de connectivité** – ces alertes se produisent lorsqu’il y a des difficultés à transférer des données. Les problèmes de communication peuvent se produire lors du transfert des données vers et à partir du compte de stockage Azure ou en raison de l’absence de connectivité entre les périphériques et le service Gestionnaire de StorSimple. Problèmes de communication sont les plus difficiles à résoudre, car il existe de nombreux points de défaillance. Vous devez toujours tout d’abord vérifier que la connectivité réseau et accès à Internet sont disponibles avant de passer à la résolution des problèmes plus avancées. Pour obtenir une aide au dépannage, consultez pour [résoudre des problèmes avec l’applet de commande Test-connexion](storsimple-troubleshoot-deployment.md).

- **Problèmes de performances** – ces alertes sont provoquées lorsque votre système n’est pas optimale, notamment lorsqu’il est soumis à une charge importante.

En outre, vous pouvez voir les alertes liées à la sécurité, des mises à jour ou des échecs des tâches.

## <a name="alert-severity-levels"></a>Niveaux de gravité d’alerte

Alertes ont différents niveaux de gravité, en fonction de l’impact de la situation d’alerte et de la nécessité d’une réponse à l’alerte. Les niveaux de gravité sont les suivants :

- Il est **critique** : cette alerte en réponse à une condition qui affecte les performances réussie de votre système. Action est nécessaire pour s’assurer que le StorSimple de service n’est pas interrompue.

- **Avertissement** – cette situation pourrait devenir critique si ne pas résolu. Vous devez examiner la situation et exécuter toute action requise pour supprimer le problème.

- **Informations** – cette alerte contient des informations qui peuvent être utiles pour le suivi et la gestion de votre système.

## <a name="configure-alert-settings"></a>Configurer les paramètres d’alerte

Vous pouvez choisir si vous souhaitez être informés par e-mail de conditions d’alerte pour chacun de vos périphériques de StorSimple. En outre, vous pouvez identifier les autres destinataires de notification d’alerte en entrant leurs adresses de messagerie dans la zone **autres destinataires** , séparée par des points-virgules.

>[AZURE.NOTE] Vous pouvez entrer un maximum de 20 adresses e-mail par périphérique.

Après avoir activé la notification par courrier électronique pour un périphérique, les membres de la liste de notification recevront un message électronique chaque fois une alerte critique se produit. Les messages seront envoyés à partir de *storsimple-alerts-noreply@mail.windowsazure.com* et décrit la condition d’alerte. Destinataires cliquez sur **Annuler l’abonnement** pour se supprimer de la liste de notification de courrier électronique.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Pour activer la notification par courrier électronique des alertes pour un périphérique

1. Accédez à des **périphériques** > **configurer** le périphérique.

2. Sous **Paramètres d’alerte**, définissez les éléments suivants :

    1. Dans le champ **Envoyer la notification par e-mail** , sélectionnez **Oui**.

    2. Dans le champ **administrateurs du service de courrier électronique** , sélectionnez **Oui** si vous souhaitez que l’administrateur de service et les administrateurs de collaboration reçoivent les notifications d’alerte.

    3. Dans le champ **autres destinataires de l’e-mail** , entrez les adresses électroniques des autres destinataires qui doivent recevoir les notifications d’alerte. Entrez les noms au format *someone@somewhere.com*. Utilisez des points-virgules pour séparer les adresses de messagerie. Vous pouvez configurer un maximum de 20 adresses e-mail par périphérique. 

        ![Configuration de notification d’alertes](./media/storsimple-manage-alerts/AlertNotify.png)

3. Pour envoyer une notification par courrier électronique de test, cliquez sur la flèche en regard de **e-mail de test d’envoi**. Le service Gestionnaire de StorSimple affiche les messages d’état comme il transmet la notification test. 

4. Lorsque le message suivant s’affiche, cliquez sur **OK**. 

    ![Alertes de notification par e-mail de test](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE] Si le message de notification de test ne peut pas être envoyé, le service Gestionnaire de StorSimple affiche un message approprié. Cliquez sur **OK**, patientez quelques minutes et réessayez d’envoyer votre message de notification de test. 

## <a name="view-and-track-alerts"></a>Permet d’afficher et d’effectuer le suivi des alertes

Le tableau de bord de service Gestionnaire de StorSimple vous offre un coup de œil sur le nombre d’alertes sur vos périphériques, organisées par niveau de gravité.

![Tableau de bord alertes](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Cliquez sur le niveau de gravité pour ouvrir l’onglet **alertes** . Les résultats comprennent uniquement les alertes qui correspondent à ce niveau de gravité.

![État des alertes une portée limitée à un type d’alerte](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Cliquer sur une alerte dans la liste vous fournit des détails supplémentaires pour l’alerte, y compris la dernière fois que l’alerte a été signalée, le nombre d’occurrences de l’alerte sur le périphérique et l’action recommandée pour résoudre l’alerte. S’il s’agit d’une alerte matérielle, il identifie également le composant matériel.

![Exemple d’alerte matériel](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Vous pouvez copier les détails de l’alerte dans un fichier texte si vous avez besoin envoyer des informations au Support Microsoft. Après avoir suivi la recommandation et résoudre la condition d’alerte local, vous devez désactiver l’alerte à partir du périphérique en sélectionnant l’alerte dans l’onglet **alertes** , puis en cliquant sur **Effacer**. Pour effacer plusieurs alertes, sélectionnez chaque alerte, cliquez sur n’importe quelle colonne, à l’exception de la colonne de **l’alerte** , puis cliquez sur **Effacer** après avoir sélectionné toutes les alertes à effacer. Notez que certaines alertes sont automatiquement désactivées lorsque le problème est résolu ou lorsque le système met à jour l’alerte avec de nouvelles informations.

Lorsque vous cliquez sur **Effacer**, avoir la possibilité de fournir des commentaires sur l’alerte et les étapes que vous avez prises pour résoudre le problème. Certains événements seront effacés par le système si un autre événement est déclenché avec les nouvelles informations. Dans ce cas, vous verrez le message suivant.

![Message d’alerte effacer](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Tri et révision des alertes

Il peut s’avérer plus efficace d’exécuter des rapports sur les alertes de sorte que vous pouvez consulter et désactivez les groupes. En outre, l’onglet **alertes** peut afficher jusqu'à 250 alertes. Si vous avez dépassé le nombre correspondant d’alertes, pas toutes les alertes seront affichera dans la vue par défaut. Vous pouvez combiner les champs suivants pour personnaliser les alertes sont affichent :

- **Statut** : vous pouvez afficher les alertes **actives** ou **effacé** . Active les alertes sont toujours en cours déclenchées sur votre système, tandis que les alertes effacées ont été effacés manuellement par un administrateur ou d’effacer par programme car le système de mise à jour de la condition d’alerte avec les nouvelles informations.

- **Gravité** – vous pouvez afficher les alertes de tous les niveaux de gravité (critiques, avertissements, informations), ou simplement une certaine gravité, telles que les alertes critiques uniquement.

- **Source** – vous pouvez afficher les alertes de toutes les sources, ou limiter les alertes à ceux provoqués par le service ou un ou tous les périphériques.

- **Plage de temps** – en spécifiant les dates **** d’et **à** et les horodatages, vous pouvez regarder alertes pendant la période qui vous intéressez.

## <a name="alerts-quick-reference"></a>Référence rapide des alertes

Les tableaux suivants répertorient quelques-uns des alertes Microsoft Azure StorSimple que vous pouvez rencontrer, ainsi que des recommandations et des informations supplémentaires le cas échéant. Alertes de périphérique StorSimple entrent dans une des catégories suivantes :

- [Alertes de connectivité de nuage](#cloud-connectivity-alerts)

- [Alertes de cluster](#cluster-alerts)

- [Alertes de récupération après sinistre](#disaster-recovery-alerts)

- [Alertes de matériel](#hardware-alerts)

- [Alertes d’échec de tâche](#job-failure-alerts)

- [Alertes relatives aux volumes ajoutés localement](#locally-pinned-volume-alerts)

- [Alertes de mise en réseau](#networking-alerts)

- [Alertes de performances](#performance-alerts)

- [Alertes de sécurité](#security-alerts)

- [Prise en charge des alertes de package](#support-package-alerts)

- [Alertes de mise à jour](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertes de connectivité de nuage

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Impossible d’établir la connexion à <*nom d’informations d’identification de cloud*>.|Impossible de se connecter sur le compte de stockage.|Il semble qu’il peut y avoir un problème de connectivité avec votre périphérique. Veuillez exécuter la `Test-HcsmConnection` applet de commande à partir de l’Interface de Windows PowerShell pour StorSimple sur votre périphérique pour identifier et résoudre le problème. Si les paramètres sont corrects, le problème peut être avec les informations d’identification du compte de stockage pour lequel l’alerte a été déclenchée. Dans ce cas, utilisez le `Test-HcsStorageAccountCredential` applet de commande pour déterminer s’il existe des problèmes que vous pouvez résoudre.<ul><li>Vérifiez vos paramètres réseau.</li><li>Vérifiez vos informations d’identification du compte de stockage.</li></ul>|
|Nous n’avons pas reçu une pulsation à partir de votre périphérique pour les dernières minutes de <*nombre*>.|Impossible de se connecter au périphérique.|Il semble y avoir un problème de connectivité avec votre périphérique. Veuillez utiliser le `Test-HcsmConnection` applet de commande à partir de l’Interface de Windows PowerShell pour StorSimple sur votre périphérique pour identifier et résoudre le problème ou contactez votre administrateur réseau.|

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple comportement en cas d’échec de connectivité de nuage

Que se passe-t-il en cas d’échec de la connectivité de nuage pour mon périphérique StorSimple en production ?

En cas d’échec de la connectivité de nuage sur votre unité de production de StorSimple, puis en fonction de l’état de votre périphérique, le texte suivant peut se produire : 

- **Pour les données locales sur votre périphérique**: pendant un certain temps, il y aura sans interruption de service et de lectures continue à être pris en charge. Toutefois, comme le nombre d’e/s en attente augmente et dépasse la limite, les lectures peuvent commencer à échouer. 

    En fonction de la quantité de données sur votre périphérique, l’écrit également continuera à se produire pour les premières heures après l’interruption de la connectivité de nuage. Les écritures seront puis ralentissent et finalement commencent à échouer si la connectivité du nuage est interrompue pendant plusieurs heures. (Il existe de stockage temporaire sur le périphérique des données vers le nuage. Cette zone est vidée lorsque les données sont envoyées. En cas d’échec de la connexion, les données dans cette zone de stockage ne seront pas répercutées vers le nuage, et e/s échouera.)   

 
- **Pour les données dans le nuage**: pour la plupart des erreurs de connectivité de nuage, une erreur est renvoyée. Une fois la connectivité restaurée, les e/s sont reprises sans que l’utilisateur de devoir mettre le volume en ligne. Dans de rares cas, l’intervention de l’utilisateur peut-être être nécessaires pour ramener le volume en ligne à partir du portail classique Azure. 
 
- **Pour les instantanés de nuage en cours**: l’opération est répétée plusieurs fois au sein de 4 à 5 heures et si la connexion n’est pas restaurée, les snapshots de nuage échouera.


### <a name="cluster-alerts"></a>Alertes de cluster

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Échec du périphérique sur <*nom*>.|Périphérique est en mode maintenance.|DISPOSITIF a échoué sur entre ou sort du mode de maintenance. Ceci est normal et aucune action n’est nécessaire. Une fois que vous avez reconnu cette alerte, l’effacer à partir de la page des alertes.|
|Échec du périphérique sur <*nom*>.|Logiciel ou du microprogramme du périphérique a été mis à jour.|Il y a un basculement de cluster en raison d’une mise à jour. Ceci est normal et aucune action n’est nécessaire. Une fois que vous avez reconnu cette alerte, l’effacer à partir de la page des alertes.|
|Échec du périphérique sur <*nom*>.|Contrôleur a été arrêté ou redémarré.|DISPOSITIF a basculé, car le contrôleur actif a été arrêté ou redémarré par un administrateur. Aucune action n’est nécessaire. Une fois que vous avez reconnu cette alerte, l’effacer à partir de la page des alertes.|
|Échec du périphérique sur <*nom*>.|Basculement planifié.|Vérifiez qu’il s’agissait d’un basculement planifié. Après avoir pris les mesures appropriées, désactivez cette alerte à partir de la page des alertes.|
|Échec du périphérique sur <*nom*>.|Basculement non planifié.|StorSimple est générée afin de récupérer automatiquement d’un basculement non planifié. Si vous voyez un grand nombre de ces alertes, contactez le Support Microsoft.|
|Échec du périphérique sur <*nom*>.|Cause d’autre/inconnu.|Si vous voyez un grand nombre de ces alertes, contactez le Support Microsoft. Une fois le problème résolu, désactivez cette alerte à partir de la page des alertes.|
|Un service de périphériques critiques signale l’état comme ayant échoué.|Échec du service DataPath. |Contactez le Support Microsoft pour obtenir de l’aide.|
|Une adresse IP virtuelle pour interface réseau <*données #*> signale l’état comme ayant échoué. |Cause d’autre/inconnu. |Conditions temporaires parfois peuvent provoquer ces alertes. Si c’est le cas, puis cette alerte sera automatiquement effacée après un certain temps. Si le problème persiste, contactez le Support Microsoft.|
|Une adresse IP virtuelle pour interface réseau <*données #*> signale l’état comme ayant échoué.|Nom de l’interface : <*données #*> adresse IP <IP address> ne peut pas être mise en ligne car une adresse IP en double a été détectée sur le réseau. |Assurez-vous que l’adresse IP en double est supprimé du réseau ou de reconfigurer l’interface avec une adresse IP différente.|


### <a name="disaster-recovery-alerts"></a>Alertes de récupération après sinistre

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Les opérations de restauration n’a pas peuvent restaurer tous les paramètres de ce service. Les données de configuration de périphérique sont dans un état incohérent pour certains périphériques.|Incohérence de données détectée après la reprise après sinistre.|Les données chiffrées sur le service ne sont pas synchronisées avec qui sur le périphérique. Autoriser le périphérique <*nom_périphérique*> à partir de la fenêtre du Gestionnaire des StorSimple pour démarrer le processus de synchronisation. Permet d’exécuter l’Interface de Windows PowerShell pour StorSimple les `Restore-HcsmEncryptedServiceData` sur l’applet de commande de dispositif <*nom_périphérique*>, fournir l’ancien mot de passe en tant qu’entrée à cette applet de commande pour restaurer le profil de sécurité. Puis exécutez le `Invoke-HcsmServiceDataEncryptionKeyChange` la clé de cryptage de données de service de mise à jour de l’applet de commande. Après avoir pris les mesures appropriées, désactivez cette alerte à partir de la page des alertes.|


### <a name="hardware-alerts"></a>Alertes de matériel

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Statut des rapports matériel composant <*composant ID*> <*état*>.||Conditions temporaires parfois peuvent provoquer ces alertes. Si tel est le cas, cette alerte sera automatiquement effacée après un certain temps. Si le problème persiste, contactez le Support Microsoft.|
|Dysfonctionnement de contrôleur passif.|Contrôleur passif (secondaire) ne fonctionne pas.|Votre périphérique est opérationnel, mais un de vos contrôleurs fonctionne mal. Essayez de redémarrer ce contrôleur. Si le problème n’est pas résolu, contactez le Support Microsoft.|

### <a name="job-failure-alerts"></a>Alertes d’échec de tâche

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Échec de la sauvegarde de <*ID de groupe du volume source*>.|Échec de l’opération de sauvegarde.|Problèmes de connectivité peuvent empêcher l’opération de sauvegarde de se terminer avec succès. S’il n’y a aucun problème de connectivité, vous avez peut-être atteint le nombre maximal de sauvegardes. Supprimer toutes les sauvegardes qui ne sont plus nécessaires et recommencez l’opération. Après avoir pris les mesures appropriées, désactivez cette alerte à partir de la page des alertes.|
|Clone de <*ID d’élément de source de sauvegarde*> < des*numéros de série de volume de destination*> a échoué.|Échec du travail de clone.|Actualiser la liste de sauvegarde pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide, il est possible que les problèmes de connectivité de nuage empêchent de terminer correctement l’opération de clonage. S’il n’y a aucun problème de connectivité, vous avez peut-être atteint la limite de stockage. Supprimer toutes les sauvegardes qui ne sont plus nécessaires et recommencez l’opération. Après avoir pris les mesures appropriées pour résoudre le problème, désactivez cette alerte à partir de la page des alertes.|
|Échec de la restauration de <*ID d’élément de source de sauvegarde*>.|Restaurer l’échec de la tâche.|Actualiser la liste de sauvegarde pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide, il est possible que les problèmes de connectivité de nuage empêchent de terminer correctement l’opération de restauration. S’il n’y a aucun problème de connectivité, vous avez peut-être atteint la limite de stockage. Supprimer toutes les sauvegardes qui ne sont plus nécessaires et recommencez l’opération. Après avoir pris les mesures appropriées pour résoudre le problème, désactivez cette alerte à partir de la page des alertes.|

### <a name="locally-pinned-volume-alerts"></a>Alertes relatives aux volumes ajoutés localement

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Échoué de la création d’un volume local <*nom*>.| Le travail de création de volume a échoué. <*Message d’erreur correspondant au code d’erreur*>.|Problèmes de connectivité peuvent empêcher l’opération de création d’espace de terminer avec succès. Volumes ajoutés localement thickly mise en service et le processus de création d’espace implique de débordement hiérarchisés volumes vers le nuage. S’il n’y a aucun problème de connectivité, il pouvez que vous avez épuisé l’espace local sur le périphérique. Déterminer si un espace existe sur le périphérique avant de retenter l’opération.|
|Échec de l’expansion d’un volume local <*nom*>.|Le travail de modification du volume a échoué en raison de <*erreur message correspondant au code d’erreur*>.|Problèmes de connectivité peuvent empêcher l’opération d’extension de volume de terminer avec succès. Volumes ajoutés localement thickly mise en service et le processus d’extension de l’espace d’implique de débordement hiérarchisés volumes vers le nuage. S’il n’y a aucun problème de connectivité, il pouvez que vous avez épuisé l’espace local sur le périphérique. Déterminer si un espace existe sur le périphérique avant de retenter l’opération.|
|Échoué de la conversion du volume <*nom*>.|La tâche de conversion de volume pour convertir le type de volume à partir de localement épinglé à plusieurs niveaux a échoué.|La conversion du volume de type localement épinglé à niveaux n’a pas pu terminer. Assurez-vous qu’il n’y a aucun problème de connectivité empêche l’opération de se terminer correctement. Pour résoudre les problèmes de connectivité, passez à [résoudre les problèmes avec l’applet de commande Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Le volume local épinglé d’origine a maintenant été marqué comme un volume monté en cascade dans la mesure où certaines données à partir du volume localement épinglé a renversé vers le nuage lors de la conversion. Le volume résultant de plusieurs niveaux occupe toujours un espace local sur le périphérique qui ne peut pas être récupéré pour des volumes locaux futures.<br>Résoudre les problèmes de connectivité, désactivez l’alerte et convertissez ce volume au type de volume local épinglé d’origine afin de garantir à toutes les données rendues disponibles localement à nouveau.|
|Échoué de la conversion du volume <*nom*>.|La tâche de conversion de volume pour convertir le type de volume à partir de plusieurs niveaux localement mise en attente a échoué.|La conversion du volume de type hiérarchisé localement mise en attente n’a pu aboutir. Assurez-vous qu’il n’y a aucun problème de connectivité empêche l’opération de se terminer correctement. Pour résoudre les problèmes de connectivité, passez à [résoudre les problèmes avec l’applet de commande Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Le volume monté en cascade d’origine maintenant marqué comme un volume localement ajouté comme partie du processus de conversion continue à avoir des données résidant dans le nuage, alors que l’espace thickly mis en service sur le périphérique pour ce volume peut n’est plus récupéré pour le futurs volumes locaux.<br>Résoudre les problèmes de connectivité, désactivez l’alerte et convertissez ce volume vers le type de volume monté en cascade d’origine afin de garantir un espace thickly mis en service sur le périphérique local peut être libéré.|
|Approche de la consommation d’espace local des snapshots locaux de <*nom du groupe de volumes*>|Snapshots locaux pour la stratégie de sauvegarde peuvent manquer bientôt d’espace et être invalidées afin d’éviter les échecs d’écriture des hôtes.|Fréquentes snapshots locaux à côté d’une évolution du élevée des données dans les volumes associés à ce groupe de stratégies de sauvegarde sont à l’origine un espace local sur le périphérique à consommer rapidement. Supprimer tous les snapshots locaux qui ne sont plus nécessaires. Mettre à jour vos plannings instantané local pour cette stratégie de sauvegarde prendre des snapshots locaux moins fréquents et que les instantanés de nuage sont régulièrement pris. Si ces actions ne sont pas prises, espace local pour ces captures instantanées peut rapidement être épuisé et que le système supprime automatiquement les écritures de l’hôte de continuer à être traités avec succès.|
|Snapshots locaux pour <*nom groupe volumes*> ont été invalidées.|Les snapshots locaux pour <*nom groupe volumes*> ont été invalidés et supprimés dans la mesure où ils ont été supérieure à l’espace local sur le périphérique.|Pour vous assurer que cela ne se répète pas à l’avenir, passez en revue les planifications d’instantané local pour cette stratégie de sauvegarde et supprimer tous les snapshots locaux qui ne sont plus nécessaires. Fréquentes snapshots locaux à côté d’une évolution du élevée des données dans les volumes associés à ce groupe de la stratégie de sauvegarde peuvent provoquer un espace local sur le périphérique à consommer rapidement.|
|Échec de la restauration de <*ID d’élément de source de sauvegarde*>.|Le travail de restauration a échoué.|Si vous avez épinglé localement ou un mélange de volumes localement épinglés et hiérarchisés dans cette stratégie de sauvegarde, actualiser la liste de sauvegarde pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide, il est possible que les problèmes de connectivité de nuage empêchent de terminer correctement l’opération de restauration. Les volumes ajoutés localement qui ont été restaurées dans le cadre de ce groupe de capture instantanée n’ont pas toutes leurs données téléchargées sur le périphérique, et, si vous avez un mélange de volumes hiérarchisés et localement épinglés dans ce groupe de capture instantanée, ils ne seront pas synchronisés entre eux. Pour terminer correctement l’opération de restauration, prendre les volumes de ce groupe en mode hors connexion sur l’hôte et recommencez l’opération de restauration. Notez que les données du volume qui ont été effectuées au cours du processus de restauration de toutes les modifications seront perdues.|

### <a name="networking-alerts"></a>Alertes de mise en réseau

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|N’a pas pu démarrer les services StorSimple.|Erreur de DataPath |Si le problème persiste, contactez le Support Microsoft.|
|Adresse IP en double détecté pour « Data0 ».| |Le système a détecté un conflit d’adresse IP '10.0.0.1'. La ressource réseau « Data0 » sur le périphérique *<device1>* est hors connexion. Assurez-vous que cette adresse IP n’est pas utilisée par une autre entité dans ce réseau. Pour résoudre les problèmes de réseau, passez à la [résolution des problèmes avec l’applet de commande Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Pour tenter de résoudre ce problème, contactez votre administrateur réseau. Si le problème persiste, contactez le Support Microsoft. |
|Adresse IPv4 (ou IPv6) 'Data0' est hors connexion.| |La ressource réseau « Data0 » avec l’adresse IP '10.0.0.1.' et de préfixe de longueur '22' sur le périphérique *<device1>* est hors connexion. Assurez-vous que les ports de commutateur auquel cette interface est connectée sont opérationnels. Pour résoudre les problèmes de réseau, passez à la [résolution des problèmes avec l’applet de commande Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
 

### <a name="performance-alerts"></a>Alertes de performances

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|La charge a dépassé <*seuil*>.|Plus lent que le temps de réponse attendu.|L’utilisation de rapports de périphérique sous une lourde charge d’entrée/sortie. Ceci peut provoquer votre périphérique ne fonctionne ne pas aussi bien comme il le devrait. Passez en revue les charges de travail que vous avez connecté au périphérique et déterminez s’il y en a qui pourrait être déplacée vers un autre périphérique ou qui ne sont plus nécessaires.<br>Pour comprendre l’état actuel, passez à [utiliser le service de gestionnaire de StorSimple pour analyser votre périphérique](storsimple-monitor-device.md)|

### <a name="security-alerts"></a>Alertes de sécurité

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Session de Support de Microsoft a commencé.|Prise en charge de session d’accéder aux tiers.|Confirmez que cet accès est autorisé. Après avoir pris les mesures appropriées, désactivez cette alerte à partir de la page des alertes.|
|<*Élément*> mot de passe va expirer dans <*durée*>.|Approche de l’expiration du mot de passe.|Modifier votre mot de passe avant qu’il expire.|
|Informations de configuration de sécurité manquant pour <*ID d’élément*>.||Les volumes associés à ce conteneur de volume ne peut pas être utilisés pour répliquer votre configuration StorSimple. Pour vous assurer que vos données stockées en toute sécurité, nous vous recommandons de supprimer le conteneur de volume et de volumes associés au volume de conteneur. Après avoir pris les mesures appropriées, désactivez cette alerte à partir de la page des alertes.|
|<*nombre*> tentatives de connexion a échoué pour <*ID d’élément*>.|Tentatives de plusieurs tentatives de connexion.|Votre appareil peut être attaqué ou un utilisateur autorisé tente de se connecter avec un mot de passe incorrect.<ul><li>Contactez vos utilisateurs autorisés et vérifiez que ces tentatives étaient à partir d’une source légitime. Si vous continuez de voir un nombre important de tentatives de connexion infructueuses, pensez à désactiver la gestion à distance et de contacter votre administrateur réseau. Après avoir pris les mesures appropriées, désactivez cette alerte à partir de la page des alertes.</li><li>Vérifiez que les instances de votre gestionnaire de snapshots sont configurées avec le mot de passe. Après avoir pris les mesures appropriées, désactivez cette alerte à partir de la page des alertes.</li></ul>Pour plus d’informations, allez à [changer un mot de passe a expiré](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password).|
|Un ou plusieurs échecs s’est produite lors de la modification de la clé de cryptage de données de service.||Erreurs rencontrées lors de la modification de la clé de cryptage de données de service se sont produites. Une fois que vous avez résolu les conditions d’erreur, exécutez le `Invoke-HcsmServiceDataEncryptionKeyChange` applet de commande à partir de l’Interface de Windows PowerShell pour StorSimple sur le service de mise à jour de votre périphérique. Si ce problème persiste, contactez le support technique de Microsoft. Après avoir résolu le problème, désactivez cette alerte à partir de la page des alertes.|

### <a name="support-package-alerts"></a>Prise en charge des alertes de package

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Échec de la création du package de prise en charge.|StorSimple n’a pas pu générer le package.|Recommencez cette opération. Si le problème persiste, contactez le Support Microsoft. Une fois que vous avez résolu le problème, désactivez cette alerte à partir de la page des alertes.|

### <a name="update-alerts"></a>Alertes de mise à jour

|Texte d’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Correctif installé.|Mise à jour du logiciel ou un microprogramme terminée.|Le correctif est installé correctement sur votre appareil.|
|Mises à jour manuelles disponibles.|Notification de mises à jour disponibles.|Utilisez l’Interface de Windows PowerShell pour StorSimple sur votre périphérique pour installer ces mises à jour. <br>Pour plus d’informations, consultez Mise à [jour de votre périphérique de la gamme 8000 de StorSimple](storsimple-update-device.md).|
|Nouvelles mises à jour disponibles.|Notification de mises à jour disponibles.|Vous pouvez installer ces mises à jour à partir de la page de **Maintenance** ou à l’aide de l’Interface de Windows PowerShell pour StorSimple sur votre périphérique. <br>Pour plus d’informations, consultez Mise à [jour de votre périphérique de la gamme 8000 de StorSimple](storsimple-update-device.md).|
|Impossible d’installer les mises à jour.|Mises à jour n’ont pas été installés.|Votre système n’a pas pu installer les mises à jour. Vous pouvez installer ces mises à jour à partir de la page de **Maintenance** ou à l’aide de l’Interface de Windows PowerShell pour StorSimple sur votre périphérique. Si le problème persiste, contactez le Support Microsoft. <br>Pour plus d’informations, consultez Mise à [jour de votre périphérique de la gamme 8000 de StorSimple](storsimple-update-device.md).|
|Impossible de vérifier automatiquement les mises à jour de nouveau.|Échoué de la vérification automatique.|Vous pouvez rechercher manuellement les nouvelles mises à jour à partir de la page de **Maintenance** .|
|Nouvel agent WUA disponible.|Notification de mise à jour disponible.|Télécharger la dernière version de Windows Update Agent et l’installer à partir de l’interface de Windows PowerShell.|
|Version du firmware du composant <*ID de composant*> ne correspond pas avec le matériel.|Mises à jour du microprogramme n’ont pas été installés.|Contactez le support technique de Microsoft.|

## <a name="next-steps"></a>Étapes suivantes

Plus d’informations sur [les erreurs StorSimple et résolution des problèmes liés à une unité opérationnelle](storsimple-troubleshoot-operational-device.md).

