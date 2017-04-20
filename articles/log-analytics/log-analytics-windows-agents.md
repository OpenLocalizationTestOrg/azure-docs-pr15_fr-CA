<properties
    pageTitle="Ordinateurs Windows de se connecter au journal Analytique | Microsoft Azure"
    description="Cet article explique les étapes pour connecter les ordinateurs Windows de votre infrastructure sur site directement aux OMS à l’aide d’une version personnalisée de l’Agent Microsoft analyse (MMA)."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="connect-windows-computers-to-log-analytics"></a>Connecter des ordinateurs Windows à Analytique du journal

Cet article explique les étapes pour connecter les ordinateurs Windows de votre infrastructure sur site directement aux espaces de travail OMS à l’aide d’une version personnalisée de l’Agent Microsoft analyse (MMA). Vous devez installer et connecter des agents pour tous les ordinateurs que vous voulez pour intégré d’OMS pour qu’elles s’envoyer des données OMS et pour afficher et agir sur ces données dans le portail de l’OMS. Chaque agent peut signaler à plusieurs espaces de travail.

Vous pouvez installer des agents à l’aide du programme d’installation, ligne de commande, ou avec souhaité état Configuration (DSC) dans Azure Automation.  

>[AZURE.NOTE] Pour les machines virtuelles exécute dans Azure, vous pouvez simplifier l’installation à l’aide de l' [extension de l’ordinateur virtuel](log-analytics-azure-vm-extension.md).

Sur les ordinateurs avec connexion à Internet, l’agent utilisera la connexion à Internet pour envoyer des données à OMS. Pour les ordinateurs qui n’ont pas de connectivité Internet, vous pouvez utiliser un serveur proxy ou le redirecteur Analytique du journal OMS.

La connexion de vos ordinateurs Windows d’OMS est simple à l’aide de 3 étapes simples :

1. Téléchargez le fichier d’installation de l’agent
2. Installation de l’agent à l’aide de la méthode que vous choisissez.
3. Configuration de l’agent ou ajouter des espaces de travail supplémentaires, si nécessaire

Le diagramme suivant montre la relation entre vos ordinateurs Windows et l’OMS après avoir installé et configuré les agents.

![OMS-direct-agent-schéma](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## <a name="system-requirements-and-required-configuration"></a>Configuration système requise et la configuration requise
Avant d’installer ou de déployer des agents, passez en revue les informations suivantes afin de que vous répondre aux conditions requises.

- L’OMS MMA ne peut être installé sur les ordinateurs exécutant Windows Server 2008 SP 1 ou version ultérieure ou Windows 7 SP1 ou une version ultérieure.
- Vous aurez besoin d’un abonnement OMS.  Pour plus d’informations, consultez [mise en route de journal Analytique](log-analytics-get-started.md).
- Chaque ordinateur doit être en mesure de se connecter à Internet à l’aide de HTTPS. Cette connexion peut être directe, via un serveur proxy ou via le redirecteur Analytique du journal OMS.
- Vous pouvez installer l’OMS MMA sur des ordinateurs autonomes, des serveurs et des ordinateurs virtuels. Si vous souhaitez vous connecter Azure hébergeant des ordinateurs virtuels OMS, consultez [ordinateurs virtuels journal Analytique Azure de se connecter](log-analytics-azure-vm-extension.md).
- L’agent doit utiliser le port TCP 443 pour les différentes ressources. Pour plus d’informations, consultez [configurer les paramètres de pare-feu et de proxy dans journal Analytique](log-analytics-proxy-firewall.md).

## <a name="download-the-agent-setup-file-from-oms"></a>Téléchargez le fichier d’installation de l’agent à partir de l’OMS
1. Dans le portail de l’OMS, sur la page de **vue d’ensemble** , cliquez sur la fenêtre de **paramètres** .  Cliquez sur l’onglet **Sources connectées** en haut.  
    ![Onglet de Sources connectée](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. **Joindre les ordinateurs directement**, cliquez sur **Télécharger un Agent Windows** applicables à votre type de processeur d’ordinateur pour télécharger le fichier d’installation.
3. Sur la droite de **l’ID de l’espace de travail**, cliquez sur l’icône Copier et coller le code dans le bloc-notes.
4. Sur la droite de **Clé primaire**, cliquez sur l’icône Copier et coller la clé dans le bloc-notes.     
    ![Copiez l’ID de l’espace de travail et de la clé primaire](./media/log-analytics-windows-agents/oms-direct-agent-primary-key.png)

## <a name="install-the-agent-using-setup"></a>Installation de l’agent à l’aide du programme d’installation
1. Exécutez le programme d’installation pour installer l’agent sur un ordinateur que vous souhaitez gérer.
2. Dans la page Bienvenue, cliquez sur **suivant**.
3. Dans la page Contrat de licence, lisez la licence et cliquez sur **J’accepte**.
4. Sur la page dossier de Destination, à modifier ou à garder le dossier d’installation par défaut et puis cliquez sur **suivant**.
5. Dans la page Options d’installation de l’Agent, vous pouvez choisir de vous connecter à l’agent pour Azure journal Analytique (OMS), responsable des opérations, ou vous pouvez laisser les choix vide si vous souhaitez configurer l’agent ultérieurement. Cliquez sur **suivant**.   
    - Si vous avez choisi de vous connecter à Azure journal Analytique (OMS), collez **l’ID de l’espace de travail** et l' **Espace de travail de clé (clé primaire)** que vous avez copié dans le bloc-notes, dans la procédure précédente, puis sur **suivant**.  
        ![Collez l’ID de l’espace de travail et de la clé primaire](./media/log-analytics-windows-agents/connect-workspace.png)
    - Si vous choisissez de vous connecter à Operations Manager, tapez le **Nom du groupe d’administration**, nom du **Serveur de gestion** et **Port du serveur de gestion**, puis cliquez sur **suivant**. Dans la page compte d’Action de l’Agent, le compte système Local ou un compte de domaine local et cliquez sur **suivant**.  
        ![configuration du groupe de gestion](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![compte action d’agent](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. Dans la page Prêt pour l’installation, vérifiez vos choix et puis cliquez sur **installer**.
7. La configuration a réussi, cliquez sur **Terminer**.
8. Lorsque vous avez terminé, l' **Agent de surveillance de Microsoft** s’affiche dans **Le panneau de configuration**. Vous pouvez vérifier votre configuration il et vérifiez que l’agent est connecté aux perspectives opérationnelles (OMS). Lorsque vous êtes connecté à l’OMS, l’agent affiche un message indiquant : **l’Agent de surveillance de Microsoft s’est connecté au service Microsoft Operations Management Suite.**

## <a name="install-the-agent-using-the-command-line"></a>Installation de l’agent à l’aide de la ligne de commande
- Modifier et utilisez l’exemple suivant pour installer l’agent à l’aide de la ligne de commande.

    >[AZURE.NOTE] Si vous souhaitez mettre à niveau un agent, vous devez utiliser l’Analytique de journal API de script. Reportez-vous à la section suivante pour mettre à niveau un agent.

    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## <a name="upgrade-the-agent-and-add-a-workspace-using-a-script"></a>L’agent de mise à niveau et ajouter un espace de travail à l’aide d’un script
Vous pouvez mettre à niveau un agent et ajouter un espace de travail à l’aide de l’Analytique de journal API de script avec l’exemple PowerShell suivant.

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

>[AZURE.NOTE] Si vous avez utilisé précédemment le format ou le script de ligne de commande pour installer ou configurer l’agent, `EnableAzureOperationalInsights` a été remplacé par `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Installation de l’agent à l’aide de DSC dans Azure Automation

>[AZURE.NOTE] Cet exemple de procédure et le script ne mettra pas à niveau un agent existant.

1. Importer la Module de DSC de xPSDesiredStateConfiguration de [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) Azure Automation.  
2.  Créer des actifs de variable Automation d’Azure pour *OPSINSIGHTS_WS_ID* et *OPSINSIGHTS_WS_KEY*. Affectez *OPSINSIGHTS_WS_ID* à votre ID d’espace de travail OMS journal Analytique et *OPSINSIGHTS_WS_KEY* à la clé primaire de votre espace de travail.
3.  Utilisez le script ci-dessous et enregistrez-le en tant que MMAgent.ps1
4.  Modifier et utilisez l’exemple suivant pour installer l’agent à l’aide de DSC dans Azure Automation. Importer les MMAgent.ps1 dans Azure Automation à l’aide de l’interface d’automatisation d’Azure ou l’applet de commande.
5.  Attribuer un nœud dans la configuration. Dans les 15 minutes, le nœud vérifiera sa configuration et la MMA est poussée vers le nœud.

```
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "http://download.microsoft.com/download/0/C/0/0C072D6E-F418-4AD4-BCB2-A362624F400A/MMASetup-AMD64.exe"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}  


```


## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Configurer un agent manuellement ou d’ajouter des espaces de travail supplémentaires
Si vous avez installé les agents, mais ne les avez ne pas configurés ou si vous souhaitez que l’agent de signaler à plusieurs espaces de travail, vous pouvez utiliser les informations suivantes pour activer un agent ou reconfigurez-le. Une fois que vous avez configuré l’agent, il sera enregistré avec le service de l’agent et obtenez des informations de configuration nécessaires et les packs d’administration contiennent des informations sur la solution.

1. Une fois que vous avez installé l’Agent de surveillance de Microsoft, ouvrez **Le panneau de configuration**.
2. Ouvrir **l’Agent de surveillance de Microsoft** , puis cliquez sur l’onglet **Journal de Azure Analytique (OMS)** .   
3. Cliquez sur **Ajouter** pour ouvrir la zone **Ajouter un espace de travail d’Analytique de journal** .
4. Collez **l’ID de l’espace de travail** et l' **Espace de travail de clé (clé primaire)** que vous avez copié dans le bloc-notes, dans une procédure précédente pour l’espace de travail que vous souhaitez ajouter, puis cliquez sur **OK**.  
    ![configurer des perspectives opérationnelles](./media/log-analytics-windows-agents/add-workspace.png)

Une fois que les données sont collectées à partir des ordinateurs surveillés par l’agent, le nombre d’ordinateurs analysés par OMS apparaîtra dans le portail de l’OMS sur l’onglet **Sources connectées** dans **paramètres** comme **Serveurs connectés**.


## <a name="to-disable-an-agent"></a>Pour désactiver un agent
1. Après l’installation de l’agent, ouvrez **Le panneau de configuration**.
2. Ouvrir l’Agent de surveillance de Microsoft, puis cliquez sur l’onglet **Journal de Azure Analytique (OMS)** .
3. Sélectionnez un espace de travail et cliquez sur **Supprimer**. Répétez cette étape pour tous les autres espaces de travail.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>Le cas échéant, configurez les agents dans un groupe d’administration Operations Manager

Si vous utilisez Operations Manager dans votre infrastructure informatique, vous pouvez également utiliser l’agent MMA comme un agent Operations Manager.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Pour configurer les agents MMA dans un groupe d’administration Operations Manager
1.  Sur l’ordinateur où l’agent est installé, ouvrez **Le panneau de configuration**.
2.  Ouvrir **l’Agent de surveillance de Microsoft** , puis cliquez sur l’onglet **Operations Manager** .
    ![Onglet de l’Agent de surveillance de Microsoft Operations Manager](./media/log-analytics-windows-agents/om-mg01.png)
3.  Si vos serveurs Operations Manager intégration avec Active Directory, cliquez sur **mettre à jour automatiquement les affectations de groupe de gestion des services AD DS**.
4.  Cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Ajouter un groupe d’administration** .  
    ![Agent de surveillance Microsoft ajouter un groupe d’administration](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  Dans la zone **nom de groupe d’administration** , tapez le nom de votre groupe d’administration.
6.  Dans la zone **serveur d’administration principal** , tapez le nom d’ordinateur du serveur d’administration principal.
7.  Dans la zone **port de gestion du serveur** , tapez le numéro de port TCP.
8.  Sous **Compte Action d’Agent**, cliquez sur le compte système Local ou un compte de domaine local.
9.  Cliquez sur **OK** pour fermer la boîte de dialogue **Ajouter un groupe d’administration** et cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de l’Agent de surveillance de Microsoft** .

## <a name="optionally-configure-agents-to-use-the-oms-log-analytics-forwarder"></a>Vous pouvez également configurer des agents de pour utiliser le redirecteur Analytique du journal OMS

Si vous avez des serveurs ou des clients qui ne disposent pas d’une connexion à Internet, vous avez encore les envoyer des données à l’aide du redirecteur Analytique du journal OMS d’OMS.  Lorsque vous utilisez le redirecteur, toutes les données provenant des agents est envoyé par un serveur unique qui dispose d’un accès à Internet. Le redirecteur transfère les données provenant des agents d’OMS directement sans analyse des données qui sont transférées.

Consultez le [Redirecteur de OMS journal Analytique](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder) pour plus d’informations sur le redirecteur, y compris le programme d’installation et de configuration.

Pour plus d’informations sur la façon de configurer vos agents pour utiliser un serveur proxy, qui dans ce cas est le redirecteur OMS, consultez [configurer les paramètres de pare-feu et de proxy dans journal Analytique](log-analytics-proxy-firewall.md).

## <a name="optionally-configure-proxy-and-firewall-settings"></a>Le cas échéant, configurer les paramètres de proxy et de pare-feu
Si vous avez des serveurs proxy ou pare-feu qui limitent l’accès à Internet dans votre environnement, consultez [configurer les paramètres de pare-feu et de proxy dans journal Analytique](log-analytics-proxy-firewall.md) pour activer vos agents communiquer avec le service OMS.

## <a name="next-steps"></a>Étapes suivantes

- [Solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et de collecter des données.
- [Configurer les paramètres de pare-feu et de proxy dans journal Analytique](log-analytics-proxy-firewall.md) si votre organisation utilise un serveur proxy ou un pare-feu afin que les agents peuvent communiquer avec le service de journal Analytique.
