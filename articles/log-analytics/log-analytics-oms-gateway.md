<properties
    pageTitle="Connecter des ordinateurs et des périphériques de l’OMS, à l’aide de la passerelle de l’OMS | Microsoft Azure"
    description="Connectent vos équipements OMS et analysés par Operations Manager grâce à la passerelle de l’OMS pour envoyer des données au service OMS lorsqu’ils ne disposent pas d’un accès à Internet."
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
    ms.date="10/26/2016"
    ms.author="banders"/>

# <a name="connect-computers-and-devices-to-oms-using-the-oms-gateway"></a>Connecter des ordinateurs et des périphériques de l’OMS, à l’aide de la passerelle de l’OMS

Ce document décrit comment votre géré par l’OMS les périphériques et les ordinateurs gérés de System Center Operations Manager SCOM peuvent envoyer des données au service OMS lorsqu’ils ne disposent pas d’un accès à Internet. La passerelle de l’OMS peut collecter les données et l’envoyer au service OMS en leur nom.

La passerelle est un proxy de transfert HTTP qui prend en charge le tunneling HTTP à l’aide de la commande HTTP CONNECT. La passerelle peut gérer jusqu'à 2000 périphériques OMS simultanément connectés lorsqu’il est exécutés sur une UC 4 cœurs, serveur de 16 Go fonctionnant sous Windows.

Par exemple, votre entreprise ou une organisation de grande taille peut-être avoir des serveurs avec une connectivité réseau mais ne dispose pas de connectivité Internet. Dans un autre exemple, vous pouvez avoir des point de nombreux périphériques de vente (PDV) avec aucun moyen de surveiller directement. Et dans un autre exemple, Operations Manager peuvent utiliser la passerelle de l’OMS, comme un serveur proxy. Dans ces exemples, la passerelle OMS peut transférer les données provenant des agents installés sur ces serveurs ou des équipements POS d’OMS.

Au lieu de chaque agent particulier envoie des données directement OMS et nécessitant une connexion directe à Internet, toutes les données de l’agent est à la place envoyé par un seul ordinateur qui possède une connexion Internet. Cet ordinateur est où installer et d’utiliser la passerelle. Dans ce scénario, vous pouvez installer des agents sur les ordinateurs où vous souhaitez collecter des données. La passerelle puis transfère les données provenant des agents d’OMS directement : la passerelle n’analyse pas les données qui sont transférées.

Pour surveiller la passerelle OMS et analyser les performances ou les données d’événement pour le serveur sur lequel il est installé, vous devez installer l’agent de l’OMS sur l’ordinateur où la passerelle est également installée.

La passerelle doit avoir accès à Internet pour télécharger les données vers l’OMS. Chaque agent doit également avoir la connectivité réseau vers sa passerelle afin que les agents peuvent transférer automatiquement les données vers et à partir de la passerelle. Pour de meilleurs résultats, n’installez pas la passerelle sur un ordinateur qui est également un contrôleur de domaine.

Voici un diagramme qui montre les flux de données provenant des agents directs d’OMS.

![diagramme d’agent direct](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

Voici un diagramme qui montre les flux de données à partir du Gestionnaire d’opérations d’OMS.

![Diagramme du Gestionnaire des opérations](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="install-the-oms-gateway"></a>Installez la passerelle OMS

L’installation de cette passerelle remplace les versions précédentes de la passerelle que vous avez installé (redirecteur Analytique de journal).

Conditions préalables : .net Framework 4.5, Windows Server 2012 R2 SP1 et versions ultérieures

1. Téléchargez la dernière version de la passerelle de l’OMS à partir du [Centre de téléchargement Microsoft](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi).
2. Pour démarrer l’installation, double-cliquez sur **Gateway.msi de l’OMS**.
3. Sur la page d’accueil, **suivant**.  
    ![Assistant de configuration de passerelle](./media/log-analytics-oms-gateway/gateway-wizard01.png)
4. Dans la page Contrat de licence, sélectionnez **J’accepte les termes du contrat de licence** pour accepter l’accord de licence puis cliquez sur **suivant**.
5. Dans la page proxy et du port de l’adresse :
    1. Tapez le numéro de port TCP à utiliser pour la passerelle. Le programme d’installation ouvre ce numéro de port du pare-feu Windows. La valeur par défaut est 8080.
    La plage valide comprise entre le numéro de port est 1-65535. Si l’entrée n’est pas comprise dans cette plage, un message d’erreur s’affiche.
    2. Le cas échéant, si le serveur où est installée la passerelle doit utiliser un serveur proxy, tapez l’adresse de proxy où la passerelle doit se connecter. Par exemple, `http://myorgname.corp.contoso.com:80` si vide, la passerelle va essayer de se connecter directement à Internet. Dans le cas contraire, la passerelle qui se connecte au serveur proxy. Si votre serveur proxy requiert une authentification, tapez votre nom d’utilisateur et le mot de passe.
        ![Configuration du serveur proxy passerelle Assistant](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
    3. Cliquez sur **suivant**
6. Si vous n’avez pas activé de Microsoft Updates, la page Microsoft Update s’affiche dans laquelle vous pouvez choisir d’activer les Microsoft Updates. Effectuez une sélection, puis cliquez sur **suivant**. Sinon, passez à l’étape suivante.
7. Sur la page dossier de Destination, soit laisser le de dossier par défaut **%ProgramFiles%\OMS passerelle** ou tapez l’emplacement où vous souhaitez installer gateway et puis cliquez sur **suivant**.
8. Sur la page prêt à installer, cliquez sur **installer**. Un contrôle du compte utilisateur peut s’afficher à demander l’autorisation d’installer. Si tel est le cas, cliquez sur **Oui**.
9. Une fois l’installation terminée, cliquez sur **Terminer**. Vous pouvez vérifier que le service est en cours d’exécution, ouvrez le composant logiciel enfichable services.msc et vérifiez que la **Passerelle de l’OMS** s’affiche dans la liste des services.  
    ![Services – passerelle d’OMS](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>Installer un agent sur les périphériques

Si nécessaire, reportez-vous à la section [ordinateurs Windows de se connecter au journal Analytique](log-analytics-windows-agents.md) pour plus d’informations sur la façon d’installer directement les agents connectés. Cet article décrit comment vous pouvez installer l’agent à l’aide d’un Assistant d’installation ou à l’aide de la ligne de commande.

## <a name="configure-oms-agents"></a>Configurer les agents de l’OMS

Consultez [configurer les paramètres de pare-feu et de proxy de l’Agent de surveillance de Microsoft](log-analytics-proxy-firewall.md) pour plus d’informations sur la configuration d’un agent pour utiliser un serveur proxy, ce qui est ce cas est la passerelle.

Agents Operations Manager envoient des données telles que les alertes, évaluation de la configuration, l’espace de l’instance et capacité données, via le serveur d’administration Operations Manager. Autres données volumineuses, telles que les journaux IIS, de performances et de sécurité sont envoyées directement vers la passerelle de l’OMS. Pour une liste complète des données envoyées par le biais de chaque canal, reportez-vous à la section [Analytique de journal ajouter des solutions à partir de la galerie de Solutions](log-analytics-add-solutions.md) .

>[AZURE.NOTE]
Si vous envisagez d’utiliser la passerelle avec équilibrage de charge réseau, consultez [éventuellement configurer l’équilibrage de charge réseau](#optionally-configure-network-load-balancing).

## <a name="configure-a-scom-proxy-server"></a>Configurer un serveur proxy SCOM

Vous configurez Operations Manager pour ajouter de la passerelle pour agir comme un serveur proxy. Lorsque vous mettez à jour la configuration du proxy, la configuration du proxy est automatiquement appliquée à tous les agents des création de rapports d’Operations Manager.

Pour utiliser la passerelle pour prendre en charge d’Operations Manager, vous devez disposer :

- Agent de surveillance de Microsoft (version de l’agent – **8.0.10900.0** et version ultérieure) installé sur le serveur de passerelle et configuré pour les espaces de travail OMS avec qui vous souhaitez communiquer.
- La passerelle doit se connecter à Internet ou être connectée à un serveur proxy qui exécute.

### <a name="to-configure-scom-for-the-gateway"></a>Pour configurer SCOM de la passerelle

1. Ouvrez la console Operations Manager et dans la **Suite de gestion des opérations**, cliquez sur **connexion** et puis cliquez sur **Configurer le serveur Proxy**:  
    ![Operations Manager – Configurez le serveur Proxy](./media/log-analytics-oms-gateway/scom01.png)
2. Sélectionnez **utiliser un serveur proxy pour accéder à la Suite de gestion des opérations** , puis tapez l’adresse IP du serveur de passerelle de l’OMS. Vérifiez que vous démarrez avec le `http://` préfixe :  
    ![Operations Manager – adresse du serveur proxy](./media/log-analytics-oms-gateway/scom02.png)
3. Cliquez sur **Terminer**. Votre serveur Operations Manager est connecté à votre espace de travail de l’OMS.

## <a name="configure-network-load-balancing"></a>Configurer l’équilibrage de charge réseau

Vous pouvez configurer la passerelle pour une haute disponibilité à l’aide de par la création d’un cluster de l’équilibrage de charge réseau. Le cluster gère le trafic à partir de vos agents en redirigeant les connexions demandées par les Agents de surveillance de Microsoft sur ses nœuds. Si un serveur de passerelle tombe en panne, le trafic est redirigé vers d’autres nœuds.

1. Ouvrez le Gestionnaire d’équilibrage de charge réseau et créez un cluster.
2. Droit du cluster avant d’ajouter des passerelles, puis cliquez sur **les propriétés du Cluster.** Configurer le cluster pour sa propre adresse IP :  
    ![Gestionnaire d’équilibrage de charge – adresses IP de Cluster du réseau](./media/log-analytics-oms-gateway/nlb01.png)
3. Pour vous connecter à un serveur de passerelle de l’OMS avec l’Agent de surveillance Microsoft installé, cliquez sur adresse IP du cluster, puis cliquez sur **Ajouter l’hôte au Cluster**.  
    ![– Le Gestionnaire équilibrage de charge réseau ajouter l’hôte au Cluster](./media/log-analytics-oms-gateway/nlb02.png)
4. Entrez l’adresse IP du serveur de la passerelle que vous souhaitez vous connecter :  
    ![Le Gestionnaire d’équilibrage de la charge – du réseau ajouter l’hôte au Cluster : se connecter](./media/log-analytics-oms-gateway/nlb03.png)
5. Sur les ordinateurs qui n’ont pas de connectivité Internet, veillez à utiliser l’adresse IP du cluster lorsque vous configurez les **Propriétés de l’Agent de surveillance de Microsoft**:  
    ![Microsoft analyse les propriétés de l’Agent – paramètres de Proxy](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>Configurer pour les travailleurs d’automation hybride

Si vous avez des travailleurs hybride d’automation dans votre environnement, les étapes suivantes fournissent des solutions de contournement temporaires, manuelles pour la configuration de la passerelle pour prendre en charge les.

Dans les étapes suivantes, vous devez connaître la région Azure où réside le compte de l’Automation. Pour localiser l’emplacement :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez le service Automation d’Azure.
3. Sélectionnez le compte Azure Automation approprié.
4. Permet d’afficher la région sous **emplacement**.  
    ![Azure portal – emplacement du compte d’Automation](./media/log-analytics-oms-gateway/location.png)

Pour identifier l’URL pour chaque emplacement, utilisez les tables suivantes :

**Travail des URL de service de données de runtime**

| **emplacement** | **URL** |
| --- | --- |
| États-Unis centre nord | ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europe de l’ouest | Nous-jobruntimedata-prod-su1.azure-automation.net |
| États-Unis centre sud | scus-jobruntimedata-prod-su1.azure-automation.net |
| Les États-Unis | phase-jobruntimedata-prod-su1.azure-automation.net |
| Canada central | CC-jobruntimedata-prod-su1.azure-automation.net |
| Europe du Nord | ne-jobruntimedata-prod-su1.azure-automation.net |
| Asie du Sud-est | SEA-jobruntimedata-prod-su1.azure-automation.net |
| Central de l’Inde | cid-jobruntimedata-prod-su1.azure-automation.net |
| Japon | jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australie | ASE-jobruntimedata-prod-su1.azure-automation.net |

**URL de service de l’agent**

| **emplacement** | **URL** |
| --- | --- |
| États-Unis centre nord | ncus-agentservice-prod-1.azure-automation.net |
| Europe de l’ouest | Nous-agentservice-prod-1.azure-automation.net |
| États-Unis centre sud | scus-agentservice-prod-1.azure-automation.net |
| Les États-Unis | eus2-agentservice-prod-1.azure-automation.net |
| Canada central | CC-agentservice-prod-1.azure-automation.net |
| Europe du Nord | ne-agentservice-prod-1.azure-automation.net |
| Asie du Sud-est | SEA-agentservice-prod-1.azure-automation.net |
| Central de l’Inde | cid-agentservice-prod-1.azure-automation.net |
| Japon | jpe-agentservice-prod-1.azure-automation.net |
| Australie | ASE-agentservice-prod-1.azure-automation.net |

Si votre ordinateur est inscrit en tant que travailleur hybride automatiquement pour l’application de correctifs à l’aide de la solution de gestion de la mise à jour, suivez ces étapes :

1. Ajouter les URL de service de données d’exécution de tâche à la liste autorisée l’hôte sur la passerelle de l’OMS. Par exemple : `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Redémarrez le Service de passerelle OMS à l’aide de l’applet de commande PowerShell suivante :`Restart-Service OMSGatewayService`

Si votre ordinateur est sur-arraisonné Azure Automation à l’aide de l’applet de commande d’enregistrement de travailleur hybride, suivez cette procédure :

1. Ajoutez l’URL de l’enregistrement de service de l’agent à la liste autorisée l’hôte sur la passerelle de l’OMS. Par exemple :`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Ajouter les URL de service de données d’exécution de tâche à la liste autorisée l’hôte sur la passerelle de l’OMS. Par exemple : `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Redémarrez le Service de passerelle OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Applets de commande PowerShell utiles

Applets de commande peut vous aider à réaliser les tâches qui sont nécessaires pour mettre à jour les paramètres de configuration de la passerelle de l’OMS. Avant de les utiliser, veillez à :

1. Installation de la passerelle de l’OMS (MSI).
2. Ouvrez la fenêtre PowerShell.
3. Pour importer le module, saisissez cette commande :`Import-Module OMSGateway`
4. Si aucune erreur ne s’est produite à l’étape précédente, le module a été importé avec succès, et les applets de commande peut être utilisée. Type de`Get-Module OMSGateway`
5. Après avoir apporté des modifications à l’aide des applets de commande, assurez-vous que vous redémarrez le service de passerelle.

Si vous obtenez une erreur à l’étape 3, le module n’a pas été importé. L’erreur peut se produire lorsque PowerShell ne parvient pas à trouver le module. Vous pouvez le trouver dans le chemin d’installation de la passerelle : C:\Program File\Microsoft OMS Gateway\PowerShell.

| **Applet de commande** | **Paramètres** | **Description** | **Exemples** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` | Clé (obligatoire) <br> Valeur | Modifie la configuration du service | `Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` | Clé | Obtient la configuration du service | `Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` | Adresse <br> Nom d’utilisateur <br> Mot de passe | Définit l’adresse (et les informations d’identification) du proxy (en amont) de relais | 1. Définissez un proxy de réponse et les informations d’identification :`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. définir un proxy de réponse qui n’a pas besoin de l’authentification :`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. Désactivez le paramètre, du proxy de réponse, n’est pas nécessaire un proxy de réponse :`Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` |   | Obtient l’adresse du proxy (en amont) de relais | `Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` | Hôte (obligatoire) | Ajoute l’hôte à la liste autorisée | `Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHos`t | Hôte (obligatoire) | Supprime l’hôte à partir de la liste autorisée | `Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` |   | Obtient l’hôte actuellement autorisé (uniquement configurée localement hôte, n’incluez pas automatiquement téléchargés hôtes autorisés) | `Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` | Sujet (obligatoire) | Ajoute le certificat du client à la liste autorisée | `Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` | Sujet (obligatoire) | Supprime l’objet du certificat client à partir de la liste autorisée | `Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificat`e |   | Obtient le client actuellement autorisées les sujets de certificat (uniquement les sujets autorisées configurées localement, n’incluent pas automatiquement téléchargés sujets autorisés) | `Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>Résoudre les problèmes

Nous vous conseillons d’installer l’agent de l’OMS sur les ordinateurs qui disposent de la passerelle installée. Vous pouvez ensuite utiliser l’agent pour collecter les événements qui sont enregistrés par la passerelle.

![Observateur d’événements – journal de passerelle OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**ID d’événement passerelle OMS et descriptions**

Le tableau suivant montre les ID d’événement et les descriptions des événements de journal de passerelle OMS.

| **ID** | **Description** |
| --- | --- |
| 400 | Les erreurs d’application qui ne dispose pas d’un ID spécifique |
| 401 | Configuration incorrecte. Par exemple : listenPort = « text » au lieu d’un entier. |
| 402 | Exception lors de l’analyse des messages de négociation TLS |
| 403 | Erreur de mise en réseau. Par exemple : Impossible de se connecter au serveur cible |
| 100 | Informations générales |
| 101 | Le service a démarré |
| 102 | Service s’est arrêté |
| 103 | A reçu une commande HTTP CONNECT à partir du client |
| 104 | Pas une commande HTTP CONNECT |
| 105 | Serveur de destination n’est pas dans la liste autorisée ou le port de destination n’est pas un port sécurisé (443) <br> <br> Assurez-vous que l’agent MMA sur votre serveur de passerelle et les agents de communiquer avec la passerelle sont connectés au même espace de travail Analytique du journal.|
| 105 | ERREUR TcpConnection-certificat Client non valide : CN = passerelle <br><br> S’assurer que : <br> <br> & #149 ; Vous utilisez une passerelle avec le numéro de version 1.0.395.0 ou supérieure. <br> & #149 ; L’agent MMA sur votre serveur de passerelle et les agents de communiquer avec la passerelle sont connectés au même espace de travail Analytique du journal. |
| 106 | Une raison quelconque, la session TLS est suspect et rejetés |
| 107 | La session TLS a été vérifiée. |

**Compteurs de performance à collecter**

Le tableau suivant indique les compteurs de performance disponibles pour la passerelle de l’OMS. Vous pouvez ajouter les compteurs à l’aide de l’Analyseur de performances.

| **Nom** | **Description** |
| --- | --- |
| Connexion au Client OMS passerelle/actif | Nombre de connexions client actives (TCP) |
| Nombre de passerelle/erreur OMS | Nombre d’erreurs |
| Client de passerelle/connectée OMS | Nombre de clients connectés |
| Nombre de passerelle/rejet OMS | Nombre de rejets en raison d’une erreur de validation de TLS |

![Compteurs de performance de passerelle de l’OMS](./media/log-analytics-oms-gateway/counters.png)


## <a name="get-assistance"></a>Obtenir de l’aide

Lorsque vous avez signé dans le portail Azure, vous pouvez créer une demande d’assistance avec la passerelle OMS ou tout autre service Azure ou fonctionnalité d’un service.
Demander de l’aide, cliquez sur le symbole de point d’interrogation dans le coin supérieur droit du portail, puis cliquez sur **nouvelle demande d’assistance**. Effectuez ensuite le nouveau formulaire de demande de prise en charge.

![Nouvelle demande de support](./media/log-analytics-oms-gateway/support.png)

Vous pouvez également laisser des commentaires sur OMS ou Analytique du journal lors du [forum de commentaires de Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des sources de données](log-analytics-data-sources.md) pour collecter des données à partir des Sources connectées dans votre espace de travail de l’OMS et le stocker dans le référentiel de l’OMS.
