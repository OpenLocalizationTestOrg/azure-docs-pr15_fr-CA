<properties 
    pageTitle="L’aide du Gestionnaire de connexion hybride | Microsoft Azure" 
    description="Installer et configurer le Gestionnaire de connexion hybride et connecteurs sur site dans les applications de logique" 
    services="app-service\logic" 
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/>

# <a name="connect-to-on-premises-connectors-using-the-hybrid-connection-manager"></a>Se connecter à des connecteurs sur site en utilisant le Gestionnaire de connexion hybride

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2014-12-01-aperçu. Disponibilité générale des applications logique (GA) utilise une passerelle pour une connectivité sur site. Découvrez la nouvelle [passerelle](app-service-logic-gateway-connection.md) et de la [Logique d’applications GA](https://azure.microsoft.com/documentation/services/logic-apps/).

Pour utiliser un système local, les applications logique utilise le Gestionnaire de connexion hybride. Certains connecteurs peuvent se connecter à un système local, comme SQL Server, SAP, SharePoint et ainsi de suite. 

Le Gestionnaire de connexion hybride (HCM) est un clic-une fois les installer qui est installée sur un serveur IIS au sein de votre réseau, derrière le pare-feu. À l’aide d’un relais de Bus des services Azure, HCM authentifie le système local avec le connecteur dans Azure. 

> [AZURE.NOTE] Le Gestionnaire de connexion hybride est nécessaire uniquement si vous vous connectez à une ressource locale derrière votre pare-feu. Si vous ne vous connectez pas à un système local, puis vous n’avez pas besoin du Gestionnaire de connexion hybride.

Pour commencer, vous devez :

- Azure Bus de Service relais namespace sa chaîne de connexion. Consultez la [Tarification des Bus de Service](https://azure.microsoft.com/pricing/details/service-bus/) pour déterminer le niveau comprend les relais.
- Sur site-dans informations système, y compris le nom d’utilisateur et le mot de passe. Par exemple, si vous vous connectez à un de SQL Server locale, vous devez le compte de connexion SQL Server et un mot de passe.
- Informations sur le serveur, y compris le nom du serveur et le numéro de port sur site. Par exemple, si vous vous connectez à un de SQL Server locale, vous devez le nom de SQL Server et le numéro de port TCP.

## <a name="get-the-service-bus-connection-string"></a>Obtenir la chaîne de connexion de Bus de Service

Dans le portail Azure, copier à la racine de Bus de Service sa chaîne de connexion. Cette chaîne de connexion connecte à votre connecteur Azure sur votre système local. 

1. Dans [Azure portal classique](http://go.microsoft.com/fwlink/p/?LinkID=213885), sélectionnez votre espace de noms du Bus des services et sélectionnez les **Informations de connexion**:

    ![][SB_ConnectInfo]

2. Copier la chaîne de connexion SAS :

    ![][SB_SAS]

## <a name="install-the-hybrid-connection-manager"></a>Installer le Gestionnaire de connexion hybride

1. Dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), sélectionnez le connecteur que vous avez créé. Pour l’ouvrir, vous pouvez sélectionnez **Parcourir**, sélectionnez **Applications d’API**et puis sélectionnez votre connecteur ou l’API application. 
<br/><br/>
Dans **Connexion de hybride**, le programme d’installation est **incomplète**:
<br/>
![][2] 

2. Sélectionnez la **connexion de hybride**. La chaîne de connexion Bus de Service que vous avez précédemment entré est répertoriée.
3. Copier la **chaîne de Configuration principal**:
<br/>
![][PrimaryConfigString]

4. Sous **Local hybride Connection Manager**, vous pouvez télécharger le Gestionnaire de connexion hybride ou l’installer directement à partir du portail. 
<br/><br/>
Pour installer directement à partir du portail, accédez à votre serveur IIS local, accédez au portail, cliquez sur **télécharger et configurer**.
<br/><br/>
Pour télécharger le Gestionnaire de connexion hybride, accédez à votre serveur IIS sur site et accéder à l' **application de ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). L’installation démarre automatiquement et vous pouvez l’exécuter.

5. Dans la fenêtre **Paramètres de l’écouteur** , entrez la **Chaîne de Configuration principal** que vous avez précédemment collé (à l’étape 3) et sélectionnez **installer**.

Lorsque l’installation est terminée, le suivant s’affiche :
<br/>
![][3] 

Lorsque vous naviguez à nouveau vers le connecteur, l’état de connexion hybride est maintenant **connecté**. Il se peut que vous deviez fermer le connecteur et la rouvrir :
<br/>
![][4] 

> [AZURE.NOTE] Pour passer à la chaîne de connexion secondaire, exécutez à nouveau le programme d’installation de la connexion de hybride et entrez la **Chaîne de Configuration secondaires**.


## <a name="tcp-ports-and-security"></a>Les Ports TCP et sécurité

Lorsque vous créez une connexion hybride, un site Web est créé sur votre serveur IIS de site local. Le serveur IIS peut être dans un réseau de périmètre. La configuration de port TCP sur le serveur IIS :

Le Port TCP | Pourquoi
--- | ---
 | Aucun port TCP entrants n’est requis.
9350 - 9354 | Ces ports sont utilisés pour la transmission de données. Le Gestionnaire de relais du Bus de Service sondes port 9350 pour déterminer si la connectivité TCP est disponible. S’il est disponible, il suppose que le port 9352 est également disponible. Le trafic de données passe par le port 9352. <br/><br/>Autoriser les connexions sortantes vers ces ports.
5671 | Lorsque le port 9352 est utilisé pour le trafic de données, le port 5671 est utilisé comme le le canal de contrôle. <br/><br/>Autoriser les connexions sortantes vers ce port. 
80, 443 | Si les ports 9352 et 5671 ne sont pas utilisables, *puis* les ports 80 et 443 sont les ports de secours utilisés pour la transmission de données et le canal de contrôle.<br/><br/>Autoriser les connexions sortantes vers ces ports.
Port du système de prem | Sur le système local, ouvrez le port utilisé par le système. Par exemple, SQL Server utilise généralement le port 1433. Ouvrez ce port TCP.

[Hébergement derrière un pare-feu avec Bus des services](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## <a name="troubleshooting"></a>Résolution des problèmes

![][HCMFlow]

### <a name="on-premises-troubleshooting"></a>Dépannage sur site

1. Sur le serveur IIS, vérifiez le rôle web IIS est installé et que tous les services IIS sont démarrés.
2. Sur le serveur IIS, vérifiez le Gestionnaire de connexion hybride est installé et en cours d’exécution :
 - Dans le Gestionnaire IIS (inetmgr), le site Web de ***MicrosoftAzureBizTalkHybridListener*** doit être répertorié et s’exécute. 
 - Ce site Web utilise le ***HybridListenerAppPool*** qui s’exécute sous le compte d’utilisateur intégré local *NetworkService* . Ce pool d’applications doit également être démarré.
3. Sur le serveur IIS, vérifiez que le connecteur est installé et en cours d’exécution : 
 - Un site Web est créé pour le connecteur. Par exemple, si vous avez créé un connecteur SQL, est un site Web de ***MicrosoftSqlConnector_nnn*** . Dans le Gestionnaire IIS (inetmgr), confirmez ce site Web est répertorié et démarré. 
 - Ce site Web utilise son propre pool d’applications IIS nommé ***HybridAppPoolnnn***. Ce pool d’applications s’exécute sous le compte d’utilisateur intégré local *NetworkService* . Ce site Web et le pool d’applications doivent tous deux être démarré. 
 - Parcourir le connecteur local. Par exemple, si votre site Web de connecteur utilise le port 6569, accédez à http://localhost:6569. Un document par défaut n’est pas configuré pour un `HTTP Error 403.14 - Forbidden error` est attendu.
4. Dans votre pare-feu, vérifiez les Ports TCP répertoriés dans cette rubrique sont ouverts.
5. Examinez le système source ou de destination :
 - Certains systèmes locaux nécessitent des fichiers de dépendance supplémentaire. Par exemple, si vous vous connectez sur site SAP, certains fichiers SAP supplémentaires doivent être installés sur le serveur IIS.
 - Vérifiez la connectivité vers le système avec le compte de connexion. Par exemple, le port TCP utilisé par le système doit être ouvert, comme le port 1433 pour SQL Server. Le compte de connexion que vous avez entré dans le portail Azure doit avoir accès au système.
6. Sur le serveur IIS, vérifiez les journaux d’événements pour les erreurs. 
7. Nettoyage et de réinstaller le Gestionnaire de connexion hybride : 
 - Dans IIS, supprimez manuellement le site Web de connecteur et son pool d’applications. 
 - Exécutez à nouveau le Gestionnaire de connexion hybride et confirmer que vous saisissez la correcte de **La chaîne de Configuration principal** de votre connecteur.



### <a name="in-the-azure-classic-portal"></a>Dans le portail classique Azure

1. Vérifiez que l’espace de noms du Bus de Service a un état **actif** .
2. Lorsque vous créez le connecteur, entrez la chaîne de connexion de Service Bus SAS. N’entrez pas la chaîne de connexion ACS.


## <a name="faq"></a>FAQ

**QUESTION**: il y a deux gestionnaires de connexion hybride. Quelle est la différence ? 

**Réponse**: il est la technologie [Hybride connexions](../biztalk-services/integration-hybrid-connection-overview.md) qui est principalement utilisée par les applications Web (anciennement sites Web) et les applications Mobile (anciennement mobiles services) pour se connecter à des locaux. Ce gestionnaire de connexions hybride est son propre [programme d’installation](../biztalk-services/integration-hybrid-connection-create-manage.md) et utilise un Service de BizTalk Azure (en arrière-plan). Il prend en charge uniquement les protocoles TCP et HTTP.

Avec les connecteurs de Service d’application Azure, nous avons également un gestionnaire de connexions hybride.  Ce gestionnaire de connexion hybride n’utilise *pas* un BizTalk Azure Service (en arrière-plan) et prend en charge plus que les protocoles TCP et HTTP. Consultez les [connecteurs et la liste des applications API](app-service-logic-connectors-list.md).

Bus des services Azure les deux permet de se connecter au système local.

**QUESTION**: lors de la création d’une application API personnalisée, puis-je utiliser le Gestionnaire de connexion hybride application Service pour se connecter à sur site ? 

**Réponse**: pas dans le sens traditionnel du terme. Vous pouvez utiliser un connecteur intégré, configurer l’application Service hybride Connection Manager pour se connecter au système local. Ensuite, utilisez ce connecteur avec votre application API personnalisés, éventuellement à l’aide d’une application de logique. Actuellement, vous ne peut pas développer ou créer votre propre API application hybride (par exemple, le connecteur SQL ou fichier).

Si votre API personnalisé utilise un port TCP ou HTTP, vous pouvez utiliser [Les connexions hybride](../biztalk-services/integration-hybrid-connection-overview.md) et son hybride de Connection Manager. Dans ce scénario, un Service de BizTalk Azure est utilisé. [Se connecter à SQL Server de local à partir d’une application web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) peut vous aider.  


## <a name="read-more"></a>Pour en savoir plus

[Surveillance de vos applications de logique](app-service-logic-monitor-your-logic-apps.md)<br/>
[Bus tarification service](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 
