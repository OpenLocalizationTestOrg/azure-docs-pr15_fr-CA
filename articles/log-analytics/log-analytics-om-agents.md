<properties
    pageTitle="Operations Manager de se connecter au journal Analytique | Microsoft Azure"
    description="Pour conserver votre investissement existant dans System Center Operations Manager et utilisez les capacités étendues avec journal Analytique, vous pouvez intégrer des Operations Manager avec votre espace de travail de l’OMS."
    services="log-analytics"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="magoedte"/>

# <a name="connect-operations-manager-to-log-analytics"></a>Connecter des Operations Manager pour journal Analytique

Pour conserver votre investissement existant dans System Center Operations Manager et utilisez les capacités étendues avec journal Analytique, vous pouvez intégrer des Operations Manager avec votre espace de travail de l’OMS.  Ainsi, que vous exploitez les opportunités d’OMS tout en continuant à utiliser le Gestionnaire d’opérations pour :

- Continuer à surveiller la santé de vos services informatiques avec Operations Manager
- Gestion de l’intégration avec vos solutions ITSM prise en charge de la gestion des incidente et de problèmes
- Gérer le cycle de vie des agents déployés sur site et les ordinateurs virtuels cloud public IaaS que vous surveillez avec Operations Manager

Intégration avec System Center Operations Manager enrichit votre stratégie d’opérations de service grâce à la vitesse et l’efficacité de l’OMS de collecte, de stockage et d’analyse de données d’Operations Manager.  OMS vous aide à mettre en corrélation et de parvenir à identifier les défaillances de problèmes et de surfaces de reoccurrences à l’appui de votre processus de gestion des problèmes existant.   La souplesse du moteur de recherche pour examiner les performances, les événements et les données d’alerte, avec des tableaux de bord riches et des fonctions de reporting afin d’exposer ces données de façon significative, illustre la puissance QU'OMS apporte élargie Operations Manager.

Les agents rendant compte au groupe d’administration Operations Manager collectent des données à partir de vos serveurs basées sur les sources de données Analytique de journal et les solutions que vous avez activé votre abonnement OMS.  En fonction de la solution que vous avez activé, les données à partir de ces solutions sont soit envoyé directement à partir d’un serveur d’administration Operations Manager pour le service web OMS, ou en raison du volume de données collectées sur le système géré par agent, sont envoyés directement à partir de l’agent au service du web OMS. Le serveur de gestion transmet directement les données de l’OMS pour le service web OMS, il n’est jamais écrite sur la base de données d’Operations Manager ou OperationsManagerDW.  Lorsqu’un serveur d’administration perd la connectivité avec le service web OMS, il met en cache les données localement jusqu'à ce que la communication est rétablie avec l’OMS.  Si le serveur d’administration est en mode hors connexion en raison d’une maintenance planifiée ou d’interruption non planifiée, un autre serveur d’administration du groupe d’administration va reprendre la connectivité avec l’OMS.  

Le diagramme suivant représente la connexion entre les serveurs d’administration et les agents dans un groupe d’administration System Center Operations Manager et l’OMS, y compris la direction et les ports.   

![diagramme du integration manager OMS opérations](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

## <a name="system-requirements"></a>Configuration système requise
Avant de commencer, vérifiez les détails suivants pour vérifier les que connaissances préalables nécessaires.

- OMS prend uniquement en charge les Operations Manager 2012 SP1 UR6 et ultérieure et Operations Manager 2012 R2 UR2 et supérieur.  Prise en charge du proxy a été ajouté dans l’exclusion de UR7 Operations Manager 2012 SP1 et Operations Manager 2012 R2 UR3.
- Tous les agents d’Operations Manager doivent répondre aux exigences en matière de prise en charge minimale. Assurez-vous que les agents sont à la mise à jour minimale, sinon le trafic de l’agent Windows échouera et de nombreuses erreurs peuvent remplir le journal des événements Operations Manager.
- Un abonnement OMS.  Pour plus d’informations, consultez [mise en route de journal Analytique](log-analytics-get-started.md).

## <a name="connecting-operations-manager-to-oms"></a>Connexion Operations Manager pour OMS
Effectuer la série d’étapes à suivre pour configurer votre groupe d’administration Operations Manager pour vous connecter à l’un de vos espaces de travail OMS suivante.

1. Dans la console Operations Manager, sélectionnez l’espace de travail **d’Administration** .
2. Développez le nœud de la Suite de gestion des opérations et cliquez sur **connexion**.
3. Cliquez sur le lien **d’inscription à la Suite de gestion des opérations** .
4. Sur le **Assistant d’intégration Operations Management Suite : l’authentification** de page, entrez l’adresse de messagerie ou le numéro de téléphone et le mot de passe du compte administrateur associé à votre abonnement OMS et cliquez sur **connexion**.
5. Une fois que vous êtes correctement authentifié, sur le **Assistant d’intégration Operations Management Suite : sélectionnez un espace de travail** de page, vous serez invité à sélectionner votre espace de travail de l’OMS.  Si vous avez plus d’un espace de travail, sélectionnez l’espace que vous souhaitez enregistrer avec le groupe d’administration Operations Manager dans la liste déroulante, puis cliquez sur **suivant**.

    >[AZURE.NOTE] Operations Manager prend en charge uniquement un espace de travail OMS à la fois. La connexion et les ordinateurs qui ont été inscrits pour OMS avec l’espace de travail précédent sont supprimés de l’OMS.

6. Sur le **Assistant d’intégration Operations Management Suite : résumé** de page, vérifiez vos paramètres et si elles sont correctes, cliquez sur **créer**.
7. Sur le **Assistant d’intégration Operations Management Suite : terminer** , cliquez sur **Fermer**.

### <a name="add-agent-managed-computers"></a>Ajouter des ordinateurs gérés par agent
Après la configuration de l’intégration avec votre espace de travail de l’OMS, il établit une connexion avec l’OMS pour l’uniquement, aucune donnée n’est collectée par les agents à votre groupe d’administration. Cela ne se produire jusqu'à ce qu’après avoir configuré les ordinateurs gérés par agent spécifiques pour collecter les données d’Analytique de journal. Vous pouvez sélectionner les objets individuellement, ou vous pouvez sélectionner un groupe qui contient des objets d’ordinateur Windows. Vous ne pouvez pas sélectionner un groupe qui contient des instances d’une autre classe, tels que les disques logiques ou les bases de données SQL.

1. Ouvrez la console Operations Manager et sélectionnez l’espace de travail **d’Administration** .
2. Développez le nœud de la Suite de gestion des opérations et cliquez sur **connexion**.
3. Cliquez sur le lien **Ajouter un ordinateur/groupe** sous Actions de titre sur le côté droit du volet.
4. Dans la boîte de dialogue de **Recherche de l’ordinateur** , vous pouvez rechercher pour les ordinateurs ou les groupes contrôlés par Operations Manager. Sélectionnez les ordinateurs ou les groupes intégrés d’OMS et cliquez sur **Ajouter**, puis cliquez sur **OK**.

Vous pouvez afficher les ordinateurs et les groupes configurés pour collecter des données à partir du nœud ordinateurs gérés sous Suite de gestion des opérations dans l’espace de travail **d’Administration** de la console d’opérations.  À partir de là, vous pouvez ajouter ou supprimer des ordinateurs et groupes que nécessaire.

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>Configurer les paramètres de proxy d’OMS dans la console d’opérations
Si un serveur proxy interne est entre le groupe d’administration et le service web OMS, procédez comme suit.  Ces paramètres sont gérés à partir du groupe d’administration et distribués sur les systèmes gérés par agent qui sont inclus dans la portée pour collecter des données pour OMS de manière centralisée.  Cela est utile lorsque certaines solutions ignorer le serveur de gestion et envoient des données directement au service du web OMS.

1. Ouvrez la console Operations Manager et sélectionnez l’espace de travail **d’Administration** .
2. Développez la Suite de gestion des opérations, puis cliquez sur **connexions**.
3. Dans la vue de la connexion de l’OMS, cliquez sur **Configurer le serveur Proxy**.
4. Sur **Operations Management Suite Assistant : serveur Proxy** page, sélectionnez **utiliser un serveur proxy pour accéder à la Suite de gestion des opérations**, puis tapez l’URL et le numéro de port, par exemple, http://corpproxy:80 et puis cliquez sur **Terminer**.

Si votre serveur proxy requiert une authentification, procédez comme suit pour configurer les paramètres qui ont besoin de se propager sur les ordinateurs gérés qui signaleront d’OMS, du groupe d’administration et les informations d’identification.

1. Ouvrez la console Operations Manager et sélectionnez l’espace de travail **d’Administration** .
2. Sous **Configuration de RunAs**, sélectionnez **profils**.
3. Ouvrez le profil **Système Center Advisor exécuter en tant que Proxy de profil** .
4. L’exécution en tant qu’Assistant profil, cliquez sur Ajouter pour utiliser un compte Exécuter en tant que. Vous pouvez créer un nouveau [Exécuter en tant que compte](https://technet.microsoft.com/library/hh321655.aspx) ou utiliser un compte existant. Ce compte doit disposer des autorisations suffisantes pour passer par le serveur proxy.
5. Pour définir le compte à gérer, choisissez **une classe sélectionnée, un groupe ou un objet**, cliquez sur **Sélectionner...** puis cliquez sur **groupe...** Pour ouvrir la zone de **Recherche du groupe** .
6. Recherchez, puis sélectionnez **Système Center Advisor analyse groupe Microsoft Server**.  Après avoir sélectionné le groupe pour fermer la boîte de **Recherche de groupe** , cliquez sur **OK** .
7.  Cliquez sur **OK** pour fermer la boîte **Ajouter un compte Exécuter en tant que** .
8.  Cliquez sur **Enregistrer** pour terminer l’Assistant et enregistrer vos modifications.

Une fois que la connexion est créée, et vous configurez les agents seront collecte et reporting des données de l’OMS, la configuration suivante est appliquée dans le groupe d’administration, pas nécessairement dans l’ordre :

- Le compte Exécuter en tant que **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** est créé.  Il est associé à l’exécuter en tant que profil **Microsoft système Centre Conseiller de s’exécuter en tant que profil Blob** et est axée sur deux classes - **Serveur de collecte** et de **Groupe d’administration Operations Manager**.
- Deux connecteurs sont créés.  Le premier est nommé **Microsoft.SystemCenter.Advisor.DataConnector** et est automatiquement configuré avec un abonnement qui transmet toutes les alertes générées à partir des instances de toutes les classes dans le groupe d’administration pour OMS journal Analytique. Le second connecteur est le **Connecteur du Gestionnaire d’accès**, qui est responsable de la communication avec le service web OMS et le partage des données.
- Les agents et les groupes que vous avez sélectionnés pour collecter les données dans le groupe d’administration seront ajoutés au **Groupe de serveurs de surveillance Microsoft système Center Advisor**.

## <a name="management-pack-updates"></a>Mises à jour du pack de gestion
Une fois la configuration terminée, le groupe d’administration Operations Manager établit une connexion avec le service OMS.  Le serveur d’administration se synchroniser avec le service web et recevoir des informations de configuration mises à jour sous la forme de packs de gestion pour les solutions que vous avez activé qui s’intègrent avec Operations Manager.   Operations Manager recherche les mises à jour à la gestion de ces packs à téléchargement et à les importer lorsqu’elles sont disponibles automatiquement.  Il existe deux règles en particulier le contrôle de ce comportement :

- **Microsoft.SystemCenter.Advisor.MPUpdate** - met à jour les packs de gestion base OMS. Par défaut, exécute toutes les heures douze (12).
- **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - packs d’administration de solution mises à jour activées dans votre espace de travail. S’exécute toutes les cinq (5) minutes par défaut.

Vous pouvez remplacer ces deux règles pour empêcher le téléchargement automatique en les désactivant, soit modifier la fréquence de la fréquence à laquelle le serveur d’administration synchronise avec OMS pour déterminer si un pack d’administration est disponible et qu’il doit être téléchargé.  Suivez les étapes de [procédure pour remplacer une règle ou un moniteur](https://technet.microsoft.com/library/hh212869.aspx) pour modifier le paramètre de **fréquence** avec une valeur en secondes pour modifier la planification de synchronisation, ou modifier le paramètre **Enabled** pour désactiver les règles.  Cibler les remplacements à tous les objets de la classe de groupe d’administration Operations Manager.

Si vous souhaitez continuer à la suite de votre processus de contrôle des modifications existant pour le contrôle des versions de pack de gestion dans votre groupe de gestion de production, vous pouvez désactiver les règles et les activer pendant les heures spécifiques lorsque les mises à jour sont autorisées. Si vous avez un développement ou un groupe d’administration QA dans votre environnement, et qu’il dispose d’une connectivité à Internet, vous pouvez configurer ce groupe d’administration d’un espace de travail de l’OMS pour prendre en charge ce scénario.  Cela vous permettra d’examiner et d’évaluer les versions itératives de l’OMS des packs d’administration avant de les introduire dans votre groupe de gestion de production.

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>Basculer un groupe du Gestionnaire d’opérations à un nouvel espace de travail de l’OMS
1. Connectez-vous à votre abonnement OMS et créer un nouvel espace de travail dans [Microsoft Operations Management Suite](http://oms.microsoft.com/).
2. Ouvrez la console Operations Manager avec un compte qui est membre du rôle Administrateurs Operations Manager et sélectionnez l’espace de travail **d’Administration** .
3. Développez Microsoft Operations Management Suite et sélectionnez **connexions**.
4. Cliquez sur le lien **Configurer à nouveau opération Management Suite** côté au milieu du volet.
5. Suivez l' **Assistant d’intégration Operations Management Suite** et entrez l’adresse électronique ou le numéro de téléphone et le mot de passe du compte administrateur associé à votre nouvel espace de travail de l’OMS.

    > [AZURE.NOTE] Le **Assistant d’intégration Operations Management Suite : sélectionnez un espace de travail** page présente l’espace de travail existant qui est en cours d’utilisation.


## <a name="validate-operations-manager-integration-with-oms"></a>Valider l’intégration du Gestionnaire des opérations avec OMS
Il existe plusieurs manières différentes, vous pouvez vérifier que votre OMS et l’intégration d’Operations Manager a réussi.

### <a name="to-confirm-integration-from-the-oms-portal"></a>Pour confirmer l’intégration à partir du portail de l’OMS

1.  Dans le portail de l’OMS, cliquez dans la fenêtre **paramètres**
2.  Sélectionnez les **Sources connectées**.
3.  Dans le tableau dans la section System Center Operations Manager, vous devez voir le nom du groupe d’administration répertorié avec le nombre d’agents et d’état lors de la dernière réception de données.

    ![paramètres-OMS-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

4.  Notez la valeur de **l’ID de l’espace de travail** sous le côté gauche de la page Paramètres.  Vous allez le valider par rapport à votre groupe d’administration Operations Manager ci-dessous.  

### <a name="to-confirm-integration-from-the-operations-console"></a>Pour confirmer l’intégration à partir de la console d’opérations

1.  Ouvrez la console Operations Manager et sélectionnez l’espace de travail **d’Administration** .
2.  Sélectionner les **Packs d’administration** et de la **recherchez :** type de zone de texte **Conseiller** ou **Intelligence**.
3.  En fonction des solutions que vous avez activé, vous verrez un pack d’administration correspondant répertorié dans les résultats de la recherche.  Par exemple, si vous avez activé la solution de gestion des alertes, le pack de gestion Microsoft System Center Advisor Alert Management sera dans la liste.
4.  À partir de la vue **d’analyse** , accédez à l’affichage de **l’État de Suite\Health de gestion des opérations** .  Sélectionnez un serveur d’administration dans le volet **d’État du serveur de gestion** et dans le volet de **Détails** , vérifiez la valeur de la propriété **URI de service de l’authentification** correspond à l’ID de l’espace de travail de l’OMS.

    ![OMS-OpsMgr-mg-authsvcuri-Property-MS](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)


## <a name="remove-integration-with-oms"></a>Supprimer l’intégration avec OMS
Lorsque vous avez besoin n’est plus une intégration entre votre groupe d’administration Operations Manager et d’un espace de travail de l’OMS, plusieurs étapes sont nécessaires pour supprimer correctement la connexion et la configuration du groupe d’administration. La procédure suivante vous aura à mettre à jour votre espace de travail de l’OMS en supprimant la référence de votre groupe d’administration, supprimez les connecteurs de l’OMS et puis supprimez OMS de prise en charge des packs d’administration.   

1.  Ouvrez l’interface de commande Operations Manager avec un compte qui est membre du rôle Administrateurs Operations Manager.

    >[AZURE.WARNING] Vérifiez que vous n’avez pas des packs d’administration personnalisés avec le Gestionnaire d’accès ou les IntelligencePack dans le nom avant de continuer, sinon suit les supprimer du groupe d’administration.

2.  À partir de l’invite de commande, tapez`Get-SCOMManagementPack -name "*advisor*" | Remove-SCOMManagementPack`

3.  Type suivant,`Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack`

4.  Ouvrez la console Operations Manager avec un compte qui est membre du rôle Administrateurs Operations Manager.
5.  Sous **Administration**, sélectionnez le nœud **Packs d’administration** et de la **Rechercher :** , tapez **Conseiller** et vérifier les packs d’administration suivants sont toujours importés dans votre groupe d’administration :

    - Microsoft System Center Advisor
    - Interne à Microsoft System Center Advisor

6. Dans le portail de l’OMS, cliquez dans la fenêtre **paramètres** .
7.  Sélectionnez les **Sources connectées**.
8.  Dans le tableau dans la section System Center Operations Manager, vous devez voir le nom du groupe de gestion que vous souhaitez supprimer de l’espace de travail.  Sous les **Dernières données**de la colonne, cliquez sur **Supprimer**.  

    >[AZURE.NOTE] **Supprimer** le lien ne sera pas disponible jusqu'à ce que, après 14 jours si aucune activité n’est détectée dans le groupe d’administration connecté.  
   
9.  Une fenêtre s’affiche vous invitant à confirmer que vous souhaitez poursuivre la suppression.  Cliquez sur **Oui** pour continuer. 

Pour supprimer les deux connecteurs - Microsoft.SystemCenter.Advisor.DataConnector et conseiller de connecteur, enregistrez le script PowerShell ci-dessous sur votre ordinateur et exécuter à l’aide de la procédure ci-après.

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

>[AZURE.NOTE] L’ordinateur que vous exécutez ce script à partir de, si ce n’est pas un serveur d’administration doit avoir le shell de commande Operations Manager 2012 SP1 ou R2 installé, en fonction de la version de votre groupe d’administration.

```
    `param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

À l’avenir si vous prévoyez de vous reconnecter à votre groupe d’administration à un espace de travail de l’OMS, vous devrez importer à nouveau le `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` fichier de pack de gestion à partir de la dernière mise à jour cumulative appliquée à votre groupe d’administration.  Vous trouverez ce fichier dans le `%ProgramFiles%\Microsoft System Center 2012` ou le `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` dossier.

## <a name="next-steps"></a>Étapes suivantes

- [Solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et de collecter des données.
- [Configurer les paramètres de pare-feu et de proxy dans journal Analytique](log-analytics-proxy-firewall.md) si votre organisation utilise un serveur proxy ou un pare-feu afin que les agents peuvent communiquer avec le service de journal Analytique.
