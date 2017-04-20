<properties
    pageTitle="Configurer les paramètres de pare-feu et de proxy dans journal Analytique | Microsoft Azure"
    description="Configurer les paramètres de proxy et de pare-feu lorsque vos agents ou les services de l’OMS doivent utiliser des ports spécifiques."
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
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="banders;magoedte"/>

# <a name="configure-proxy-and-firewall-settings-in-log-analytics"></a>Configurer les paramètres de pare-feu et de proxy dans journal Analytique

Actions nécessaires à la configuration de proxy et les paramètres de pare-feu pour l’Analytique de journal dans l’OMS diffèrent lorsque vous utilisez Operations Manager et ses agents contre des Agents de surveillance de Microsoft qui se connectent directement aux serveurs. Passez en revue les sections suivantes pour le type d’agent que vous utilisez.

## <a name="configure-proxy-and-firewall-settings-with-the-microsoft-monitoring-agent"></a>Configurer les paramètres de proxy et de pare-feu avec l’Agent de surveillance Microsoft

Pour l’Agent de surveillance du Microsoft à se connecter à et l’inscrire auprès du service OMS, il doit avoir accès au numéro de port de l’URL et de vos domaines. Si vous utilisez un serveur proxy pour la communication entre l’agent et le service OMS, vous devrez vous assurer que les ressources appropriées sont accessibles. Si vous utilisez un pare-feu pour limiter l’accès à Internet, vous devez configurer votre pare-feu pour autoriser l’accès à l’OMS. Les tableaux suivants répertorient les ports OMS a besoin.

|**Ressource de l’agent**|**Ports**|**Ignorer l’inspection HTTPS**|
|--------------|-----|--------------|
|\*. ods.opinsights.azure.com|443|Oui|
|\*. oms.opinsights.azure.com|443|Oui|
|\*. blob.core.windows.net|443|Oui|
|ODS.systemcenteradvisor.com|443| |

Vous pouvez utiliser la procédure suivante pour configurer les paramètres de proxy de l’Agent de surveillance de Microsoft à l’aide du Panneau de configuration. Vous devez utiliser la procédure pour chaque serveur. Si vous avez plusieurs serveurs dont vous avez besoin pour configurer, vous sembler plus facile d’utiliser un script pour automatiser ce processus. Si tel est le cas, consultez la procédure suivante [pour configurer les paramètres de proxy de l’Agent de surveillance de Microsoft à l’aide d’un script](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Pour configurer les paramètres de proxy de l’Agent de surveillance de Microsoft à l’aide du Panneau de configuration

1. Ouvrez **le panneau de configuration**.

2. Ouvrez **Microsoft, l’Agent de surveillance**.

3. Cliquez sur l’onglet **Paramètres de Proxy** .<br>  
  ![onglet Paramètres proxy](./media/log-analytics-proxy-firewall/proxy-direct-agent-proxy.png)

4. Sélectionnez **utiliser un serveur proxy** , tapez l’URL et numéro de port, si une est nécessaire, similaire à l’exemple indiqué. Si votre serveur proxy requiert une authentification, tapez le nom d’utilisateur et le mot de passe pour accéder au serveur proxy.

Utilisez la procédure suivante pour créer un script PowerShell que vous pouvez exécuter pour définir les paramètres de proxy pour chaque agent se connecte directement aux serveurs.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>Pour configurer les paramètres de proxy de l’Agent de surveillance de Microsoft à l’aide d’un script

Copiez l’exemple suivant, le mettre à jour avec les informations spécifiques à votre environnement, enregistrez-le avec une extension de nom de fichier PS1 et puis exécuter le script sur chaque ordinateur qui se connecte directement au service OMS.

        
    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
        

## <a name="configure-proxy-and-firewall-settings-with-operations-manager"></a>Configurer les paramètres de proxy et de pare-feu avec Operations Manager

Pour un groupe d’administration Operations Manager pour se connecter à et l’inscrire auprès du service OMS, il doit avoir accès aux numéros de port de vos domaines d’URL. Si vous utilisez un serveur proxy pour la communication entre le serveur d’administration Operations Manager et le service OMS, vous devrez vous assurer que les ressources appropriées sont accessibles. Si vous utilisez un pare-feu pour limiter l’accès à Internet, vous devez configurer votre pare-feu pour autoriser l’accès à l’OMS. Même si un serveur d’administration Operations Manager n’est pas derrière un serveur proxy, ses agents peuvent être. Dans ce cas, le serveur proxy doit être configuré de la même manière que les agents sont afin d’activer et de permettre la sécurité et de service web de données de solutions de gestion du journal à envoyées à l’OMS.

Pour les agents d’Operations Manager communiquer avec le service OMS, votre infrastructure Operations Manager (y compris les agents) doit avoir les paramètres de proxy appropriés et de la version. Le paramètre pour les agents proxy est spécifié dans la console Operations Manager. La version doit être une des opérations suivantes :

- Mise à jour cumulative 7 ou une version ultérieure de Operations Manager 2012 SP1
- Operations Manager 2012 R2 mise à jour cumulative 3 ou version ultérieure


Les tableaux suivants répertorient les ports associés à ces tâches.

>[AZURE.NOTE] Certaines des ressources suivantes mentionnent Advisor et des perspectives opérationnelles, les deux ont été les versions précédentes de l’OMS. Toutefois, les ressources répertoriées changera à l’avenir.

Voici une liste des ports et des ressources de l’agent :<br>

|**Ressource de l’agent**|**Ports**|
|--------------|-----|
|\*. ods.opinsights.azure.com|443|
|\*. oms.opinsights.azure.com|443|
|\*.BLOB.Core.Windows.NET/\*|443|
|ODS.systemcenteradvisor.com|443|
<br>
Voici une liste des ports et des ressources de serveur de gestion :<br>

|**Ressource de serveur de gestion**|**Ports**|**Ignorer l’inspection HTTPS**|
|--------------|-----|--------------|
|service.systemcenteradvisor.com|443| |
|\*. service.opinsights.azure.com|443| |
|\*. blob.core.windows.net|443|Oui| 
|Data.systemcenteradvisor.com|443| | 
|ODS.systemcenteradvisor.com|443| | 
|\*. ods.opinsights.azure.com|443|Oui| 
<br>
Voici une liste de ports et de ressources de console OMS et Operations Manager.<br>

|**Ressources de console OMS et Operations Manager**|**Ports**|
|----|----|
|service.systemcenteradvisor.com|443|
|\*. service.opinsights.azure.com|443|
|\*. live.com|Ports 80 et 443|
|\*. microsoft.com|Ports 80 et 443|
|\*. microsoftonline.com|Ports 80 et 443|
|\*. mms.microsoft.com|Ports 80 et 443|
|Login.Windows.NET|Ports 80 et 443|
<br>

Utilisez les procédures suivantes pour enregistrer votre groupe d’administration Operations Manager avec le service OMS. Si vous rencontrez des problèmes de communication entre le groupe d’administration et le service OMS, utilisez les procédures de validation pour résoudre les problèmes de transmission de données au service OMS.

### <a name="to-request-exceptions-for-the-oms-service-endpoints"></a>Pour demander des exceptions pour les points de terminaison de service OMS

1. Utilisez les informations de la première table présentée précédemment pour vous assurer que les ressources nécessaires pour le serveur d’administration Operations Manager sont accessibles par le biais de n’importe quel pare-feu peut avoir.
2. Utilisez les informations de la deuxième table présentée précédemment pour vous assurer que les ressources nécessaires pour la console Opérateur Operations Manager et l’OMS sont accessibles par le biais de n’importe quel pare-feu peut avoir.
3. Si vous utilisez un serveur proxy avec Internet Explorer, assurez-vous qu’il est configuré et fonctionne correctement. Pour vérifier, vous pouvez ouvrir une connexion web sécurisée (HTTPS), par exemple [https://bing.com](https://bing.com). Si la connexion web sécurisé ne fonctionne pas dans un navigateur, il probablement ne fonctionnera pas dans la console de gestion d’Operations Manager avec les services web dans le nuage.

### <a name="to-configure-the-proxy-server-in-the-operations-manager-console"></a>Pour configurer le serveur proxy dans la console Operations Manager

1. Ouvrez la console Operations Manager et sélectionnez l’espace de travail **d’Administration** .

2. Développez des **Conseils opérationnels**et sélectionnez **Connexion de perspectives opérationnelles**.<br>  
    ![Connexion de Operations Manager OMS](./media/log-analytics-proxy-firewall/proxy-om01.png)
3. Dans la vue de la connexion de l’OMS, cliquez sur **Configurer le serveur Proxy**.<br>  
    ![Connexion de Operations Manager OMS configurer le serveur Proxy](./media/log-analytics-proxy-firewall/proxy-om02.png)
4. Assistant Paramètres perspectives opérationnelles en : Serveur Proxy, sélectionnez **utiliser un serveur proxy pour accéder au Service Web de perspectives opérationnelles**et tapez l’URL avec le port numéro, par exemple, **http://myproxy:80**.<br>  
    ![Adresse du proxy Operations Manager OMS](./media/log-analytics-proxy-firewall/proxy-om03.png)


### <a name="to-specify-credentials-if-the-proxy-server-requires-authentication"></a>Pour spécifier les informations d’identification si le serveur proxy requiert une authentification
 Paramètres et informations d’identification du serveur proxy est nécessaire pour se propager sur les ordinateurs gérés qui dépendront de l’OMS. Ces serveurs doivent être dans le *Groupe de serveurs de surveillance Microsoft système Center Advisor*. Informations d’identification sont cryptées dans le Registre de chaque serveur dans le groupe.

1. Ouvrez la console Operations Manager et sélectionnez l’espace de travail **d’Administration** .
2. Sous **Configuration de RunAs**, sélectionnez **profils**.
3. Ouvrez le profil **Système Center Advisor exécuter en tant que Proxy de profil** .  
    ![image du profil système Center Advisor s’exécutent en tant que Proxy](./media/log-analytics-proxy-firewall/proxy-proxyacct1.png)
4. Dans l’exécution en tant qu’Assistant profil, cliquez sur **Ajouter** pour utiliser un compte Exécuter en tant que. Vous pouvez créer un nouveau compte Exécuter en tant qu’ou utiliser un compte existant. Ce compte doit disposer des autorisations suffisantes pour passer par le serveur proxy.  
    ![image de l’Assistant s’exécutent en tant que profil](./media/log-analytics-proxy-firewall/proxy-proxyacct2.png)
5. Pour définir le compte à gérer, cliquez sur **une classe sélectionnée, un groupe ou un objet** pour ouvrir la zone de recherche de l’objet.  
    ![image de l’Assistant s’exécutent en tant que profil](./media/log-analytics-proxy-firewall/proxy-proxyacct2-1.png)
6. Recherchez puis sélectionnez **Système Center Advisor analyse groupe Microsoft Server**.  
    ![image de la zone de recherche d’objet](./media/log-analytics-proxy-firewall/proxy-proxyacct3.png)
7. Cliquez sur **OK** pour fermer la boîte Ajouter un exécuter en tant que compte.  
    ![image de l’Assistant s’exécutent en tant que profil](./media/log-analytics-proxy-firewall/proxy-proxyacct4.png)
8. Terminez l’Assistant et enregistrer les modifications.  
    ![image de l’Assistant s’exécutent en tant que profil](./media/log-analytics-proxy-firewall/proxy-proxyacct5.png)


### <a name="to-validate-that-oms-management-packs-are-downloaded"></a>Pour valider cette gestion OMS packs sont téléchargés.

Si vous avez ajouté des solutions d’OMS, vous pouvez les afficher dans la console Operations Manager sous forme de packs d’administration sous **Administration**. Recherche *System Center Advisor* à les retrouver rapidement.  
    ![les packs d’administration téléchargés](./media/log-analytics-proxy-firewall/proxy-mpdownloaded.png) ou, vous pouvez également rechercher des packs d’administration OMS dans le serveur d’administration Operations Manager à l’aide de la commande Windows PowerShell suivante :

    ```
    Get-ScomManagementPack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken
    ```

### <a name="to-validate-that-operations-manager-is-sending-data-to-the-oms-service"></a>Pour valider cette Operations Manager envoie des données au service OMS

1. Dans le serveur d’administration Operations Manager, ouvrez l’Analyseur de performances (perfmon.exe) et sélectionnez **Analyseur de performances**.
2. Cliquez sur **Ajouter**, puis sélectionnez les **Groupes de gestion de Service de santé**.
3. Ajoutez tous les compteurs commençant par **HTTP**.  
    ![ajouter des compteurs](./media/log-analytics-proxy-firewall/proxy-sendingdata1.png)
4. Si votre configuration d’Operations Manager est bonne, que vous verrez une activité pour les compteurs de gestion des services de santé pour les événements et les autres éléments de données, selon les packs d’administration que vous avez ajouté dans l’OMS et la stratégie de collecte de journal configuré.  
    ![Surveiller l’activité présentant des performances](./media/log-analytics-proxy-firewall/proxy-sendingdata2.png)


## <a name="next-steps"></a>Étapes suivantes

- [Solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et de collecter des données.
- Familiarisez-vous avec les [recherches de journaux](log-analytics-log-searches.md) afficher des informations détaillées collectées par les solutions.
