<properties 
    pageTitle="Dépannage à l’aide des indicateurs du stockage Azure et journalisation, AzCopy et Analyseur de Message bout | Microsoft Azure" 
    description="Un didacticiel illustrant le dépannage de bout en bout avec Analytique du stockage Azure, AzCopy et l’Analyseur de Message de Microsoft" 
    services="storage" 
    documentationCenter="dotnet" 
    authors="robinsh" 
    manager="carmonm"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/03/2016" 
    ms.author="robinsh"/>

# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Résolution des problèmes de bout en bout à l’aide des indicateurs du stockage Azure et journalisation, AzCopy et Analyseur de Message 

[AZURE.INCLUDE [storage-selector-portal-e2e-troubleshooting](../../includes/storage-selector-portal-e2e-troubleshooting.md)]

## <a name="overview"></a>Vue d’ensemble

Diagnostic et résolution des problèmes sont essentielle pour la construction et le support des applications clientes avec Microsoft Azure Storage. En raison de la nature distribuée d’une application Azure, diagnostiquer et de résoudre des erreurs et des problèmes de performances peuvent être plus complexes que dans les environnements traditionnels.

Dans ce didacticiel, vous allez apprendre à identifier certaines erreurs qui peuvent affecter les performances et résoudre les erreurs de bout en bout pour les clients à l’aide des outils fournis par Microsoft et le stockage Azure, afin d’optimiser l’application cliente. 

Ce didacticiel fournit une exploration pragmatique d’un scénario de dépannage de bout en bout. Pour un guide détaillé des concepts dépannage d’applications de stockage Azure, voir [moniteur, diagnostiquer et de résoudre les problèmes de stockage de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md). 

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Outils de dépannage d’applications de stockage Azure

Pour résoudre les problèmes des applications clientes qui utilisent le stockage Azure Microsoft, vous pouvez utiliser une combinaison d’outils pour déterminer si un problème s’est produit, et quelle peut être la cause du problème. Ces outils incluent :

- **Analytique de stockage azure**. [Analytique de stockage Azure](http://msdn.microsoft.com/library/azure/hh343270.aspx) fournit des mesures et enregistrement pour stockage Azure.
    - **Indicateurs de stockage** effectue le suivi des métriques de transactions et les mesures de capacité pour votre compte de stockage. À l’aide de mesures, vous pouvez déterminer comment votre application s’exécute en fonction d’une variété de mesures différentes. Pour plus d’informations sur les types de mesures de suivi Analytique de stockage, voir [Schéma de la Table Analytique métriques de stockage](http://msdn.microsoft.com/library/azure/hh343264.aspx) . 

    - **Enregistrement de stockage** de journaux chaque demande aux services de stockage Azure dans un journal côté serveur. Le journal assure le suivi des données détaillées pour chaque demande, y compris l’opération effectuée, le statut de l’opération et les informations de latence. Pour plus d’informations sur les données de demande et de réponse qui sont écrit dans les journaux par Analytique de stockage, reportez-vous à la section [Format de stockage du journal Analytique](http://msdn.microsoft.com/library/azure/hh343259.aspx) .

> [AZURE.NOTE] Des comptes de stockage avec un type de réplication de stockage redondant Zone (ZRS) n’ont pas les métriques ou la fonction de journalisation pour l’instant. 

- **Azure Portal classique**. Vous pouvez configurer des audits et journalisation pour votre compte de stockage dans le [Portail classique d’Azure](https://manage.windowsazure.com). Vous pouvez également afficher des diagrammes et des graphiques qui montrent comment votre application s’exécute dans le temps et configurer des alertes pour vous avertir si votre application effectue pas comme prévu pour une mesure spécifique. 
    
    Pour plus d’informations sur la configuration de l’analyse dans le portail classique d’Azure, voir [Moniteur d’un compte de stockage dans le portail Azure](storage-monitor-storage-account.md) .

- **AzCopy**. Journaux du serveur pour le stockage Azure sont stockées sous forme de BLOB, vous pouvez utiliser AzCopy pour copier les objets BLOB de journal dans un répertoire local pour l’analyse de l’Analyseur de Message de Microsoft. Pour plus d’informations sur AzCopy, reportez-vous à [transférer des données avec l’utilitaire de ligne de commande de AzCopy](storage-use-azcopy.md) .

- **Analyseur de Message de Microsoft**. L’Analyseur de message est un outil qui utilise des fichiers journaux et affiche les données du journal dans un format visuel qui rend facile à filtrer, rechercher et regrouper les données de journal en ensembles utiles que vous pouvez utiliser pour analyser les erreurs et les problèmes de performances. Pour plus d’informations sur l’Analyseur de Message, reportez-vous à la section [Guide d’exploitation de Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) .

## <a name="about-the-sample-scenario"></a>À propos de l’exemple de scénario

Pour ce didacticiel, nous allons examiner un scénario où les mesures de stockage Azure indique une faible pourcentage de réussite d’une application qui appelle le stockage Azure. La métrique du taux de réussite de pourcentage faible (affichée en tant que **PercentSuccess** dans le portail classique Azure et dans les tables de mesures) assure le suivi des opérations qui aboutissent, mais qui retournent un code d’état HTTP qui est supérieur à 299. Dans les fichiers journaux de stockage côté serveur, ces opérations sont enregistrées avec un statut de transaction de **ClientOtherErrors**. Pour plus d’informations sur la mesure de la réussite de pourcentage faible, voir [faible PercentSuccess afficher les mesures ou les entrées de journal d’analytique avoir des opérations avec le statut de la transaction de ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Les opérations de stockage Azure peuvent renvoyer les codes d’état HTTP supérieur à 299 dans le cadre de leur fonctionnement normal. Mais dans certains cas, elles indiquent que vous pourrez optimiser votre application cliente pour améliorer les performances. 

Dans ce scénario, nous allons étudier une faible pourcentage de réussite à quoi que ce soit inférieure à 100 %. Vous pouvez choisir un autre niveau de la mesure, cependant, en fonction de vos besoins. Nous vous recommandons que pendant le test de votre application, vous établissez une tolérance de base pour vos mesures de performances clés. Par exemple, vous pouvez déterminer, basé sur le test, que votre application doit avoir un taux de réussite de pourcentage cohérente de 90 %, ou de 85 %. Si vos données de métrique indiquent que l’application est qui s’écartent de ce numéro, puis vous pouvez examiner ce qui peut provoquer l’augmentation. 

Pour notre exemple de scénario, une fois que nous avons établi que la mesure de taux de réussite de pourcentage est inférieur à 100 %, nous allons examiner les journaux pour trouver les erreurs qui correspondent à la métrique et utilisent pour déterminer ce qui est à l’origine le taux de réussite pour cent inférieur. Nous examinerons plus particulièrement les erreurs dans la plage de 400. Ensuite, nous étudierons plus en détail les 404 erreurs (introuvable).

### <a name="some-causes-of-400-range-errors"></a>Certaines causes d’erreurs de 400-plage

Les exemples ci-dessous montre un échantillon de certaines erreurs 400-plage de requêtes contre le stockage Blob Azure et leurs causes possibles. Une de ces erreurs, ainsi que les erreurs dans la plage de 300 et la plage 500, peuvent contribuer à un taux de réussite de pourcentage faible. 

Notez que les listes ci-dessous sont loin d’être terminée. Pour plus d’informations sur les erreurs de stockage Azure générales et sur les erreurs spécifiques à chacun des services de stockage, consultez [Codes d’erreur et d’état](http://msdn.microsoft.com/library/azure/dd179382.aspx) .

**Exemples d’état Code 404 (introuvable)**

Se produit lorsqu’une opération de lecture par rapport à un conteneur ou un blob échoue car le blob ou le conteneur n’est pas trouvé.

- Se produit si un conteneur ou un blob a été supprimé par un autre client avant cette demande. 
- Se produit si vous utilisez un appel API qui crée le conteneur ou le blob après avoir vérifié si elle existe. Les APIs CreateIfNotExists appeler tête tout d’abord vérifier l’existence du conteneur ou du blob ; Si elle n’existe pas, une erreur 404 est retournée, et puis une deuxième PUT est appelé à écrire le conteneur ou le blob.

**Code d’état de 409 (conflit) Exemples**

- Se produit si vous utilisez une API de créer pour créer un nouveau conteneur ou un blob, sans vérification de l’existence de tout d’abord, un conteneur ou un objet blob avec ce nom existe déjà. 
- Se produit si un conteneur est supprimé, et que vous essayez de créer un nouveau conteneur portant le même nom, avant que l’opération de suppression est terminée.
- Se produit si vous spécifiez un bail sur un conteneur ou un blob, et il existe déjà un bail présent.
 
**Code d’état 412 (échouée de la précondition) Exemples**

- Se produit lorsque la condition spécifiée par un en-tête conditionnel n’a pas été atteint.
- Se produit lorsque l’ID de bail spécifié ne correspond pas à l’ID de bail sur le conteneur ou le blob.

## <a name="generate-log-files-for-analysis"></a>Générer des fichiers journaux pour l’analyse

Dans ce didacticiel, nous allons utiliser Analyseur de Message pour travailler avec trois types différents de fichiers journaux, bien que vous pouvez choisir de travailler avec l’un de ces :

- Le **journal du serveur**, qui est créée lorsque vous activez l’enregistrement de stockage Azure. Le journal du serveur contient des données relatives à chaque opération appelée par rapport à un des services de stockage Azure - blob, la file d’attente, table et fichier. Le journal du serveur indique l’opération qui a été appelée et que le code d’état a été renvoyés, ainsi que d’autres détails sur la demande et la réponse.
- Le **journal du client .NET**, qui est créé lorsque vous activez la journalisation de côté client dans votre application .NET. Le journal du client consacrée des informations détaillées sur la façon dont le client prépare la demande et reçoit et traite la réponse. 
- Le **journal des traces réseau HTTP**, qui collecte des données sur des données demande et réponse HTTP/HTTPS, y compris pour les opérations sur le stockage Azure. Dans ce didacticiel, nous allons générer la trace réseau via l’Analyseur de Message.

### <a name="configure-server-side-logging-and-metrics"></a>Configurer l’enregistrement de côté serveur et métriques

Tout d’abord, nous avons besoin configurer la journalisation du stockage Azure et des mesures, afin que nous avons des données à partir de l’application client à analyser. Vous pouvez configurer la journalisation et les mesures de diverses manières : via le [Portail classique d’Azure](https://manage.windowsazure.com), à l’aide de PowerShell, ou par programme. Pour plus d’informations sur la configuration de journalisation et mesures, consultez [permettant des mesures de stockage et d’affichage des données de mesures](http://msdn.microsoft.com/library/azure/dn782843.aspx) et [l’activation de la journalisation du stockage et l’accès à des données de journal](http://msdn.microsoft.com/library/azure/dn782840.aspx) .

**Via le portail classique Azure**

Pour configurer la journalisation et la métrique pour votre compte de stockage via le portail, suivez les instructions à [l’écran un compte de stockage dans le portail Azure](storage-monitor-storage-account.md).

> [AZURE.NOTE] Il n’est pas possible de définir des mesures minutes via le portail classique d’Azure. Toutefois, nous recommandons que vous ne les définissez pour les besoins de ce didacticiel et pour rechercher les problèmes de performances avec votre application. Vous pouvez définir la métrique minute à l’aide de PowerShell comme indiqué ci-dessous, ou par programme, ou via le portail classique d’Azure.
>
> Notez que le portail classique Azure ne peut pas afficher les mesures minutes, seulement les mesures de toutes les heures. 

**Via PowerShell**

Pour commencer avec PowerShell pour Azure, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

1. Pour ajouter votre compte d’utilisateur d’Azure dans la fenêtre PowerShell, utilisez l’applet de commande [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) :

    ```
    Add-AzureAccount
    ```

2. Dans la fenêtre de **connexion à Microsoft Azure** , tapez l’adresse e-mail et le mot de passe associé à votre compte. Azure authentifie et enregistre les informations d’identification et puis ferme la fenêtre.
3. Définir le compte de stockage par défaut pour le compte de stockage que vous utilisez pour le didacticiel par l’exécution de ces commandes dans la fenêtre PowerShell :

    ```
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount' 
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName 
    ```

4. Activer la journalisation de stockage pour le service d’objet Blob : 
 
    ```
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0 
    ```
5. Activer les indicateurs de stockage pour le service de Blob, en veillant à la valeur **De MetricsType -** `Minute`:

    ```
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0 
    ```

### <a name="configure-net-client-side-logging"></a>Configurer la journalisation du côté client .NET

Pour configurer l’enregistrement côté client pour une application .NET, activer les diagnostics de .NET dans le fichier de configuration de l’application (web.config ou app.config). Pour plus d’informations, consultez [ouverture de côté Client avec la bibliothèque cliente du stockage .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx) et [journalisation avec stockage Azure Microsoft SDK pour Java côté Client](http://msdn.microsoft.com/library/azure/dn782844.aspx) .

Le journal côté client inclut des informations détaillées sur la façon dont le client prépare la demande et reçoit et traite la réponse.

La bibliothèque Client de stockage stocke les données du journal du côté client à l’emplacement spécifié dans le fichier de configuration de l’application (web.config ou app.config). 

### <a name="collect-a-network-trace"></a>Collecter un suivi du réseau

Vous pouvez utiliser l’Analyseur de Message pour collecter un suivi du réseau HTTP/HTTPS pendant l’exécution de votre application cliente. L’Analyseur de message utilise [Fiddler](http://www.telerik.com/fiddler) sur le back-end. Avant de collecter la trace réseau, nous vous conseillons de configurer Fiddler pour enregistrer le trafic non chiffré de HTTPS :

1. Installation de [Fiddler](http://www.telerik.com/download/fiddler).
2. Lancement de Fiddler.
2. Sélectionnez les outils **| Options de Fiddler**.
3. Dans la boîte de dialogue Options, assurez-vous que **Capturer les CONNECTs HTTPS** et **Décrypter le trafic HTTPS** sont toutes deux activées, comme illustré ci-dessous.

![Configurer les Options de Fiddler](./media/storage-e2e-troubleshooting-classic-portal/fiddler-options-1.png)

Dans le didacticiel, collecter et enregistrer d’abord une trace réseau dans l’Analyseur de Message, puis créer une session d’analyse pour analyser la trace et les journaux. Pour collecter un suivi du réseau dans l’Analyseur de Message :

1. Dans l’Analyseur de Message, sélectionnez le fichier de **| Trace rapide | Non crypté HTTPS**.
2. La trace commence immédiatement. Cliquez sur **Arrêter** pour arrêter la trace de sorte que nous pouvons le configurer pour le trafic de stockage trace uniquement.
3. Sélectionnez **Modifier** pour modifier la session de suivi.
4. Cliquez sur le lien **configurer** à droite du fournisseur ETW **Microsoft-Pef-WebProxy** .
5. Dans la boîte de dialogue **Paramètres avancés** , cliquez sur l’onglet **fournisseur** .
6. Dans le champ de **Filtre de nom d’hôte** , spécifiez vos points de terminaison de stockage, séparés par des espaces. Par exemple, vous pouvez spécifier vos points de terminaison comme suit : modifier `storagesample` le nom de votre compte de stockage :
    
    ``` 
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net 
    ```

7. Quittez la boîte de dialogue, puis cliquez sur **redémarrer** pour commencer la collecte de la trace avec le filtre de nom d’hôte en place, afin que seul le trafic réseau de stockage Azure est inclus dans la trace.

>[AZURE.NOTE] Lorsque vous avez terminé la collecte de votre trace de réseau, nous vous recommandons vivement de rétablir les paramètres que vous avez peut-être modifié dans Fiddler pour le trafic HTTPS de décrypter. Dans la boîte de dialogue Options de Fiddler, désélectionnez les cases à cocher **Capturer les CONNECTs HTTPS** et **Décrypter le trafic HTTPS** .

Reportez-vous à la section [en utilisant les fonctionnalités de traçage réseau](http://technet.microsoft.com/library/jj674819.aspx) sur Technet pour plus de détails.

## <a name="review-metrics-data-in-the-azure-classic-portal"></a>Passez en revue les données de métrique dans le portail classique Azure

Une fois que votre application fonctionne pendant une période de temps, vous pouvez consulter les graphiques de mesures qui apparaissent dans le portail classique d’Azure pour observer la façon dont votre service a exécuté. Tout d’abord, nous allons ajouter la métrique du **Pourcentage de succès** pour la page d’analyse :

1. Accédez au tableau de bord pour votre compte de stockage dans le [Portail classique d’Azure](https://manage.windowsazure.com), puis sélectionnez **Moniteur** pour afficher la page de contrôle.
2. Cliquez sur **Ajouter des mesures** pour afficher la boîte de dialogue **Choisir les mesures** .
3. Défiler la liste pour rechercher le **Pourcentage de réussite** de groupe, développez-le, puis sélectionnez l' **agrégat**, comme illustré dans l’image ci-dessous. Cette mesure regroupe les données de pourcentage de réussite de toutes les opérations d’objet Blob.

![Choisissez métrique](./media/storage-e2e-troubleshooting-classic-portal/choose-metrics-portal-1.png)

Dans le portail classique d’Azure, vous voyez le **Pourcentage de succès** dans le plan de surveillance, ainsi que toutes autres mesures peut avoir ajouté (jusqu'à six peut être affiché sur le graphique à la fois). Dans l’image ci-dessous, vous pouvez voir que le taux de pourcentage de réussite est quelque peu inférieur à 100 %, ce qui est le scénario que nous étudierons ensuite en analysant les journaux dans l’Analyseur de Message :

![Graphique de métriques de portail](./media/storage-e2e-troubleshooting-classic-portal/portal-metrics-chart-1.png)

Pour plus d’informations sur l’ajout de mesures à la page d’analyse, consultez [Comment : ajouter des mesures à la table de mesures](storage-monitor-storage-account.md#how-to-add-metrics-to-the-metrics-table).

> [AZURE.NOTE] Il peut prendre un certain temps pour vos données métriques apparaissent dans le portail classique Azure après avoir activé les mesures de stockage. C’est pourquoi des mesures horaires pour l’heure précédente ne sont pas affichés dans le portail classique Azure jusqu'à ce que l’heure s’est écoulée. Également, les mesures de minute ne s’affichent pas dans le portail classique d’Azure. Donc selon lorsque vous activez des mesures, il peut prendre jusqu'à deux heures pour voir les données de la métrique.

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Utilisez AzCopy pour copier les journaux de serveur vers un répertoire local

Stockage Azure écrit des données de journal de serveur pour les objets BLOB, alors que les mesures sont écrites dans les tables. BLOB de journal est disponibles dans le bien connu `$logs` conteneur pour votre compte de stockage. BLOB de journal est nommés hiérarchiquement par année, mois, jour et heure, afin des retrouver facilement l’intervalle de temps que vous souhaitez examiner. Par exemple, dans le `storagesample` compte, le conteneur pour les objets BLOB de journal pour le 01/02/2015, à partir de 8-9 h, est `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Les blobs individuels dans ce conteneur sont nommés de manière séquentielle, à partir de `000000.log`.

Vous pouvez utiliser l’outil de ligne de commande AzCopy pour télécharger ces fichiers de journal côté serveur à l’emplacement de votre choix sur votre ordinateur local. Par exemple, vous pouvez utiliser la commande suivante pour télécharger les fichiers de journal pour les opérations de blob qui ont eu lieu le 2 janvier 2015, dans le dossier `C:\Temp\Logs\Server`; remplacer `<storageaccountname>` avec le nom de votre compte de stockage, et `<storageaccountkey>` avec votre clé d’accès de compte :

    AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy est disponible pour téléchargement sur la page [Téléchargements d’Azure](https://azure.microsoft.com/downloads/) . Pour plus d’informations sur l’utilisation de AzCopy, reportez-vous à [transférer des données avec l’utilitaire de ligne de commande de AzCopy](storage-use-azcopy.md).

Pour plus d’informations sur le téléchargement des journaux de côté serveur, consultez le [téléchargement d’un enregistrement de stockage des données de journal](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata). 

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Utilisez l’Analyseur de Message de Microsoft pour analyser les données de journal

Analyseur de Message de Microsoft est un outil de capture, d’affichage et d’analyse de protocole du trafic, des événements et autres messages du système ou d’application dans les scénarios de diagnostics et de résolution des problèmes de messagerie. Analyseur de message également vous permet de charger, regrouper et analyser les données de journal et enregistré des fichiers de trace. Pour plus d’informations sur l’Analyseur de Message, consultez le [Guide d’exploitation de Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx).

Analyseur de message inclut des actifs pour le stockage Azure qui vous aident à analyser les journaux de réseau, client et serveur. Dans cette section, nous verrons comment utiliser ces outils pour résoudre le problème du faible pourcentage de réussite dans les journaux de stockage.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Téléchargez et installez l’Analyseur de Message et les ressources de stockage Azure

1. Télécharger [l’Analyseur de Message](http://www.microsoft.com/download/details.aspx?id=44226) à partir du Microsoft Download Center et exécutez le programme d’installation.
2. Exécuter l’Analyseur de Message.
3. Dans le menu **Outils** , sélectionnez **Asset Manager**. Dans la boîte de dialogue **Asset Manager** , sélectionnez **téléchargements**, puis appliquer un filtre sur **Le stockage Azure**. Vous verrez les ressources de stockage Azure, comme illustré dans l’image ci-dessous.
4. Cliquez sur **Synchroniser tous les éléments affichés** pour installer les ressources de stockage Azure. Les ressources disponibles sont les suivantes :
    - **Règles de stockage azure couleur :** Règles de couleur de stockage Azure vous permettent de vous permet de définir des filtres spéciaux qui permettent de mettre en surbrillance les messages qui contiennent des informations spécifiques dans une trace de la couleur, le texte et les styles de police.
    - **Stockage azure graphiques :** Graphiques de stockage Azure sont les graphiques prédéfinis qui les données de journal de serveur du graphique. Notez que pour utiliser des graphiques de stockage Azure pour l’instant, vous pouvez uniquement charger le journal du serveur dans la grille d’analyse.
    - **Stockage azure analyseurs :** Les analyseurs de stockage Azure d’analyser le stockage Azure client, serveur et les journaux HTTP afin de les afficher dans la grille de l’analyse.
    - **Filtres de stockage azure :** Les filtres de stockage Azure sont des critères prédéfinis que vous pouvez utiliser pour interroger les données dans la grille de l’analyse.
    - **Stockage azure présentations :** Mises en page de vue stockage Azure sont des présentations colonne prédéfinies et regroupements dans la grille de l’analyse.
4. Redémarrez l’Analyseur de Message après avoir installé les actifs.

![Gestionnaire de message analyseur actif](./media/storage-e2e-troubleshooting-classic-portal/mma-start-page-1.png)

> [AZURE.NOTE] Installez tous les actifs de stockage Azure indiqués pour les besoins de ce didacticiel.

### <a name="import-your-log-files-into-message-analyzer"></a>Importer vos fichiers journaux dans l’Analyseur de Message

Vous pouvez importer tous vos fichiers journaux enregistrés (réseau, côté client et côté serveur) dans une seule session dans l’Analyseur de Message de Microsoft pour analyse.

1. Dans le menu **fichier** , dans l’Analyseur de Message de Microsoft, cliquez sur **Nouvelle Session**, puis cliquez sur **Session vide**. Dans la boîte de dialogue **Nouvelle Session** , entrez un nom pour votre session d’analyse. Dans le panneau de **Détails de la Session** , cliquez sur le bouton **fichiers** . 
1. Pour charger les données de trace réseau générées par l’Analyseur de Message, cliquez sur **Ajouter des fichiers**, accédez à l’emplacement où vous avez enregistré votre fichier .matp à partir de votre session de suivi web, sélectionnez le fichier .matp et cliquez sur **Ouvrir**. 
1. Pour charger les données du journal côté serveur, cliquez sur **Ajouter des fichiers**, accédez à l’emplacement où vous avez téléchargé vos journaux de serveur, sélectionnez les fichiers journaux de la plage de temps que vous souhaitez analyser et cliquez sur **Ouvrir**. Puis, dans le panneau de **Détails de la Session** , définir la **Configuration du journal de texte** déroulante pour chaque fichier de journal côté serveur sur **AzureStorageLog** pour vous assurer que Microsoft Message analyseur peut analyser le fichier journal correctement.
1. Pour charger les données du journal côté client, cliquez sur **Ajouter des fichiers**, accédez à l’emplacement où vous avez enregistré vos journaux côté client, sélectionnez les fichiers journaux que vous souhaitez analyser et cliquez sur **Ouvrir**. Puis, dans le panneau de **Détails de la Session** , définir la **Configuration du journal de texte** déroulante pour chaque fichier de journal côté client à **AzureStorageClientDotNetV4** pour vous assurer que Microsoft Message analyseur peut analyser le fichier journal correctement.
1. Cliquez sur **Démarrer** dans la boîte de dialogue **Nouvelle Session** pour charger et analyser les données du journal. Les données du journal s’affiche dans la grille d’analyse d’Analyseur de Message.

L’illustration ci-dessous montre un exemple de session configurée avec les fichiers journaux de suivi réseau, client et serveur.

![Configurer la Session de l’analyseur Message](./media/storage-e2e-troubleshooting-classic-portal/configure-mma-session-1.png)

Notez que Message analyseur charge les fichiers journaux en mémoire. Si vous avez un grand ensemble de données de journal, vous devez filtrer afin d’obtenir les meilleures performances de l’Analyseur de Message.

Tout d’abord, déterminez le laps de temps qui vous intéressez et conserver ce laps de temps aussi petite que possible. Dans de nombreux cas, vous souhaiterez revoir une période de plusieurs minutes ou heures au plus. Importer le plus petit ensemble de journaux qui peut répondre à vos besoins.

Si vous avez une grande quantité de données de journal, vous souhaiterez spécifier une session de filtrer vos données de journal avant de le charger. Dans la zone de **Filtre de Session** , cliquez sur le bouton de la **bibliothèque** pour choisir un filtre prédéfini ; par exemple, choisissez **filtre temps Global I** les filtres de stockage Azure filtre sur un intervalle de temps. Vous pouvez ensuite modifier les critères de filtre pour spécifier le début et fin d’horodatage pour l’intervalle que vous souhaitez afficher. Vous pouvez également filtrer sur un code de statut particulier ; par exemple, vous pouvez choisir de charger uniquement les entrées de journal dans lequel le code d’état est 404.

Pour plus d’informations sur l’importation des données du journal dans l’Analyseur de Message de Microsoft, consultez [Extraction des données de Message](http://technet.microsoft.com/library/dn772437.aspx) sur TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Utilisez l’ID de demande du client pour mettre en corrélation les données de fichier journal

La bibliothèque cliente du stockage Azure génère automatiquement un ID de demande client unique pour chaque demande. Cette valeur est écrite dans le journal du client, le journal du serveur et la trace réseau, afin que vous puissiez permettant de corréler les données entre tous les trois journaux dans l’Analyseur de Message. Consultez l' [ID de la demande Client](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) pour plus d’informations sur la demande du client ID.

Les sections ci-dessous décrivent comment utiliser les différents modes d’affichage personnalisés et préconfigurée pour mettre en corrélation et regrouper les données en fonction de l’ID de demande client.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Sélectionnez une disposition de vues à afficher dans la grille d’analyse

Les ressources de stockage pour l’Analyseur de Message d’inclure les dispositions de vue stockage Azure, qui constituent les vues préconfigurées que vous pouvez utiliser pour afficher vos données avec regroupements utiles et des colonnes pour des scénarios différents. Vous pouvez également créer des présentations personnalisées et les enregistrer pour les réutiliser. 

L’illustration ci-dessous montre le menu **Mise en page** , accessible en sélectionnant **Afficher la présentation** du ruban de la barre d’outils. Les dispositions de la vue pour le stockage Azure sont regroupées sous le nœud de **Stockage Azure** dans le menu. Vous pouvez rechercher des `Azure Storage` dans la zone de recherche pour filtrer sur le stockage Azure permet d’afficher uniquement les dispositions. Vous pouvez également sélectionner l’étoile en regard d’une disposition de vues pour rendre un favori et l’afficher en haut du menu.

![Afficher le menu mise en forme](./media/storage-e2e-troubleshooting-classic-portal/view-layout-menu.png)

Dans un premier temps, sélectionnez **Grouper par Module et ClientRequestID**. Ce groupe de mise en page vue données du journal à partir des trois journaux tout d’abord par ID de demande client, puis par fichier de journal source (ou le **Module** dans l’Analyseur de Message). Dans cette vue, vous pouvez approfondir votre ID d’une demande de client particulier et visualiser les données toutes les trois fichiers journaux pour la requête du client ID.

L’image ci-dessous illustre cette vue de la mise en page appliquée à l’exemple journal de données avec un sous-ensemble de colonnes affichées. Vous pouvez voir que pour un ID de demande client particulier, la grille d’analyse affiche les données à partir du journal client, serveur, suivi et journaux réseau.

![Mise en page du mode de stockage Azure](./media/storage-e2e-troubleshooting-classic-portal/view-layout-client-request-id-module.png)

>[AZURE.NOTE] Fichiers journaux différents ont des colonnes différentes, afin que, lorsque les données de plusieurs fichiers journaux sont affichées dans la grille de l’analyse, certaines colonnes ne peuvent pas contenir toutes les données pour une ligne donnée. Par exemple, dans l’illustration ci-dessus, les lignes de journal du client ne pas affichent toutes les données de **l’horodatage**, **TimeElapsed**, **Source**et les colonnes de **Destination** , car ces colonnes n’existent pas dans le journal du client, mais il existe dans la trace réseau. De même, la colonne **Timestamp** affiche des données d’horodatage dans le journal du serveur, mais aucune donnée n’est affichée pour les colonnes **TimeElapsed**, **Source**et de **Destination** , qui ne font pas partie du journal du serveur. 

En plus d’utiliser les mises en page de la vue stockage Azure, vous pouvez également définir et enregistrer vos propres présentations. Vous pouvez sélectionner d’autres champs de votre choix pour le regroupement des données et enregistrer le regroupement dans le cadre de votre mise en page personnalisée ainsi. 

### <a name="apply-color-rules-to-the-analysis-grid"></a>Appliquer les règles de la couleur de la grille d’analyse

Les ressources de stockage incluent également les règles de couleur, qui offrent le que moyen d’un visuel identifier les différents types d’erreurs dans la grille de l’analyse. Les règles de couleur prédéfinie s’appliquent à des erreurs HTTP, et ils s’affichent uniquement pour la trace du journal et de réseau serveur.

Pour appliquer des règles de couleur, sélectionnez **Règles de couleur** à partir du ruban de la barre d’outils. Vous verrez les stockage Azure couleur les règles dans le menu. Dans le didacticiel, sélectionnez **Erreurs de Client (StatusCode entre 400 et 499)**, comme illustré dans l’image ci-dessous.

![Mise en page du mode de stockage Azure](./media/storage-e2e-troubleshooting-classic-portal/color-rules-menu.png)

En plus d’utiliser les règles de couleur du stockage Azure, vous pouvez également définir et enregistrer vos propres règles de couleur.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Regrouper et filtrer les données de journal pour rechercher les erreurs de 400-plage

Ensuite, nous allons regrouper et filtrer les données du journal pour rechercher toutes les erreurs dans la plage de 400.

1. Localisez la colonne **StatusCode** dans la grille de l’analyse, cliquez sur l’en-tête de colonne, puis sélectionnez **groupe**.
2. Groupe suivant, la colonne **ClientRequestId** . Vous verrez que les données dans la grille d’analyse sont désormais organisées par le code d’état et par l’ID de demande client.
1. Afficher la fenêtre d’outil de filtre de la vue si elle n’est pas déjà affichée. Sur le ruban de la barre d’outils, sélectionnez **Fenêtres Outil**, puis le **Filtre d’affichage**.
2. Pour filtrer les données du journal pour afficher uniquement les erreurs 400-plage, ajouter des critères de filtre suivants dans la fenêtre **Filtre de la vue** , puis cliquez sur **Appliquer**:

        (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

L’illustration ci-dessous montre les résultats de ce filtre et de regroupement. En développant le champ **ClientRequestID** sous le regroupement pour le code d’état 409, montre, par exemple, une opération qui a entraîné ce code d’état.

![Mise en page du mode de stockage Azure](./media/storage-e2e-troubleshooting-classic-portal/400-range-errors1.png)

Après l’application de ce filtre, vous verrez que les lignes du journal de client sont exclus, comme client de journal n’inclut pas une colonne **StatusCode** . Dans un premier temps, nous allons revoir le serveur et les journaux de suivi réseau pour rechercher les 404 erreurs, et puis nous allons retourner le journal client pour examiner les opérations du client ayant conduit à leur.

>[AZURE.NOTE] Vous pouvez filtrer la colonne **StatusCode** et toujours afficher les données de tous les journaux, y compris le journal du client, si vous ajoutez une expression au filtre qui comprend des entrées de journal dans lequel le code d’état est null. Pour construire cette expression de filtre, utilisez :
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code> 
>
> Ce filtre retourne toutes les lignes de la cliente journal et seulement les lignes dans le journal du serveur et HTTP où le code d’état est supérieur à 400. Si vous l’appliquez à la disposition de l’affichage groupée par ID de demande client et module, vous pouvez rechercher ou faites défiler les entrées du journal pour trouver celles où toutes les trois journaux sont représentés par le biais de.   

### <a name="filter-log-data-to-find-404-errors"></a>Filtrer les données du journal pour rechercher des 404 erreurs

Les ressources de stockage incluent les filtres prédéfinis que vous pouvez utiliser pour affiner les données du journal pour rechercher des erreurs ou des tendances que vous recherchez. Ensuite, nous allons appliquer deux filtres prédéfinis : une qui filtre le serveur et les journaux de suivi de réseau pour les erreurs de type 404 et qui permet de filtrer les données sur une plage de temps spécifiée.

1. Afficher la fenêtre d’outil de filtre de la vue si elle n’est pas déjà affichée. Sur le ruban de la barre d’outils, sélectionnez **Fenêtres Outil**, puis le **Filtre d’affichage**.
2. Dans la fenêtre filtre de la vue, sélectionnez **bibliothèque**et effectuez une recherche sur `Azure Storage` pour trouver le stockage Azure des filtres. Sélectionnez le filtre pour **404 (introuvable) les messages dans tous les journaux**.
3. Afficher le menu de la **bibliothèque** à nouveau, recherchez et sélectionnez le **Filtre de temps Global**.
4. Modifier l’horodatage indiqué dans le filtre à la plage que vous souhaitez afficher. Cela vous permet de restreindre la plage de données à analyser.
5. Votre filtre doit apparaître comme dans l’exemple ci-dessous. Cliquez sur **Appliquer** pour appliquer le filtre à la grille d’analyse.

        ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And 
        (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Mise en page du mode de stockage Azure](./media/storage-e2e-troubleshooting-classic-portal/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analyser vos données de journal

Maintenant que vous avez groupées et filtrer vos données, vous pouvez examiner les détails des requêtes individuelles qui a généré des 404 erreurs. Dans la disposition actuelle de l’affichage, les données sont regroupées par ID de demande du client, puis par la source du journal. Dans la mesure où nous sommes le filtrage sur les demandes où le champ StatusCode contient 404, nous verrons seulement le serveur et les données de trace réseau, pas les données du journal client.

L’illustration ci-dessous montre une demande spécifique où une opération obtenir un Blob a généré une erreur 404 car le blob n’existe pas. Notez que certaines colonnes ont été supprimés de la vue standard pour afficher les données pertinentes.

![Serveur filtrée et les journaux de suivi réseau](./media/storage-e2e-troubleshooting-classic-portal/server-filtered-404-error.png)

Ensuite, nous allons établir une corrélation entre cet ID de demande du client avec les données du journal client pour connaître les actions que le client est en cours lorsque l’erreur s’est produite. Vous pouvez afficher un nouvel affichage de grille d’analyse pour cette session afficher les données du journal client, qui s’ouvre dans un deuxième onglet :

1. Tout d’abord, copiez la valeur du champ **ClientRequestId** dans le Presse-papiers. Vous pouvez le faire en sélectionnant une ligne, recherche le champ **ClientRequestId** , avec le bouton droit sur la valeur des données et cliquer sur **copier 'ClientRequestId'**. 
1. Sur le ruban de la barre d’outils, sélectionnez la **Nouvelle visionneuse**, puis sélectionnez la **Grille d’analyse** pour ouvrir un nouvel onglet. Le nouvel onglet affiche toutes les données de vos fichiers journaux, sans regroupement, de filtrage ou des règles de couleur. 
2. Sur le ruban de la barre d’outils, sélectionnez **Afficher la présentation**, puis sélectionnez **Toutes les colonnes de Client .NET** sous la section **Stockage Azure** . Cette mise en page de l’affichage affiche des données à partir du client journal ainsi que les journaux de suivi des serveurs et du réseau. Par défaut, elle est triée sur la colonne de **format** .
3. Ensuite, recherchez dans le journal de client pour l’ID de demande client. Sur le ruban de la barre d’outils, sélectionnez **Rechercher des Messages**, puis spécifier le filtre de l’ID de la demande client dans le champ **Rechercher** . Utilisez la syntaxe suivante pour le filtre, en spécifiant vos propres ID de demande client :

        *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

L’Analyseur de message localise et sélectionne la première entrée de journal lorsque les critères de recherche correspond à l’ID de demande client. Dans le journal du client, il existe plusieurs entrées pour chaque ID de demande client, donc vous pouvez les regrouper sur le champ **ClientRequestId** pour faciliter les visualiser tous ensemble. L’image ci-dessous affiche tous les messages dans le journal de client pour le client spécifié ID de demande. 

![Affichage du journal client des 404 erreurs](./media/storage-e2e-troubleshooting-classic-portal/client-log-analysis-grid1.png)

Les données affichées dans les mises en page de l’affichage de ces deux onglets, vous pouvez analyser les données de demande pour déterminer ce qui peut avoir provoqué l’erreur. Vous pouvez également consulter les demandes qui ont précédé le pour voir si un événement précédent peut ont entraîné l’erreur 404. Par exemple, vous pouvez consulter les entrées du journal client précédant cet ID de demande client pour déterminer si le blob a peut-être été supprimé ou si l’erreur est due à l’application cliente appelant une API CreateIfNotExists sur un conteneur ou un objet blob. Dans le journal du client, vous pouvez trouver les adresses de l’objet blob dans le champ **Description** . dans le serveur et les journaux de suivi réseau, cette information s’affiche dans le champ **Résumé** .

Une fois que vous connaissez l’adresse de l’objet blob qui a généré l’erreur 404, vous pouvez approfondir. Si vous recherchez les entrées du journal pour les autres messages associés aux opérations sur le même objet blob, vous pouvez vérifier si le client a précédemment supprimé l’entité. 

## <a name="analyze-other-types-of-storage-errors"></a>Analyser d’autres types d’erreurs de stockage

Maintenant que vous êtes familiarisé avec l’utilisation de Message analyseur pour analyser vos données de journal, vous pouvez analyser d’autres types d’erreurs à l’aide de la vue de mises en page, les règles de la couleur et la recherche/filtrage. Le tableau ci-dessous répertorie certains problèmes que vous pouvez rencontrer et les critères de filtre que vous pouvez utiliser pour localiser les. Pour plus d’informations sur la création de filtres et l’Analyseur de Message filtrage de langage, consultez [Filtrage des données de Message](http://technet.microsoft.com/library/jj819365.aspx).

|    Rechercher...                                                                                               |    Utiliser l’Expression de filtre...                                                                                                                                                                                                                                        |    Expression qui s’applique au journal (Client, serveur, réseau, tous les)    |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
|    Retards inattendus dans la remise des messages dans une file d’attente                                                              |    AzureStorageClientDotNetV4.Description contient le « Nouvel essai Échec de l’opération. »                                                                                                                                                                                |    Client                                                        |
|    Augmentation PercentThrottlingError HTTP                                                                       |    HTTP. Response.StatusCode == 500 & #124 ; & #124 ; HTTP. Response.StatusCode == 503                                                                                                                                                                                          |    Réseau                                                       |
|    Augmentation de la PercentTimeoutError                                                                               |    HTTP. Response.StatusCode == 500                                                                                                                                                                                                                             |    Réseau                                                       |
|    Augmentation de la PercentTimeoutError (tous)                                                                         |    * StatusCode == 500                                                                                                                                                                                                                                          |    Tous les                                                           |
|    Augmentation de la PercentNetworkError                                                                               |    AzureStorageClientDotNetV4.EventLogEntry.Level < 2                                                                                                                                                                                                          |    Client                                                        |
|    Messages HTTP 403 (refusé)                                                                                 |    HTTP. Response.StatusCode == 403                                                                                                                                                                                                                             |    Réseau                                                       |
|    HTTP 404 (introuvable) messages                                                                                 |    HTTP. Response.StatusCode == 404                                                                                                                                                                                                                             |    Réseau                                                       |
|    404 (tous)                                                                                                     |    * StatusCode == 404                                                                                                                                                                                                                                          |    Tous les                                                           |
|    Partagé le problème de l’autorisation d’accès Signature (SAS)                                                             |    AzureStorageLog.RequestStatus == « SASAuthorizationError »                                                                                                                                                                                                     |    Réseau                                                       |
|    HTTP 409 (conflit) messages                                                                                  |    HTTP. Response.StatusCode == 409                                                                                                                                                                                                                             |    Réseau                                                       |
|    409 (tous)                                                                                                     |    * StatusCode == 409                                                                                                                                                                                                                                          |    Tous les                                                           |
|    Les entrées de journal faible PercentSuccess ou analytique avoir des opérations avec le statut de la transaction de ClientOtherErrors    |    AzureStorageLog.RequestStatus == « ClientOtherError »                                                                                                                                                                                                         |    Serveur                                                        |
|    Avertissement de Nagle                                                                                               |    ((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1,5)) et (AzureStorageLog.RequestPacketSize < 1460) et (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200)        |    Serveur                                                        |
|    Intervalle de temps dans les journaux de serveur et de réseau                                                                    |    #Horodatage > = 2014-10-20T16:36:38 et #Timestamp < = 2014-10-20T16:36:39                                                                                                                                                                                     |    Serveur, réseau                                               |
|    Intervalle de temps dans les journaux du serveur                                                                                |    AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 et AzureStorageLog.Timestamp < = 2014-10-20T16:36:39                                                                                                                                                     |    Serveur                                                        |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les scénarios de bout en bout au dépannage dans le stockage Azure, consultez ces ressources :

- [Contrôler, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)
- [Analytique de stockage](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [Surveiller un compte de stockage dans le portail Azure](storage-monitor-storage-account.md)
- [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
- [Guide de fonctionnement Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx)
 
 
