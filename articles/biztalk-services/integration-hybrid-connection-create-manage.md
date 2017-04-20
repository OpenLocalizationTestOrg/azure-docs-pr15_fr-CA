<properties 
    pageTitle="Créer et gérer les connexions hybride | Microsoft Azure" 
    description="Apprenez à créer une connexion hybride, gérer la connexion et installer le Gestionnaire de connexion hybride. MABS, WABS" 
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
    ms.date="10/18/2016" 
    ms.author="ccompy"/>


# <a name="create-and-manage-hybrid-connections"></a>Créer et gérer les connexions de hybride


## <a name="overview-of-the-steps"></a>Vue d’ensemble des étapes
1. Créer une connexion hybride en entrant le **nom d’hôte** ou le **nom de domaine complet** de la ressource locale dans votre réseau privé.
2. Lier des applications web Azure Azure applications mobiles à la connexion hybride.
3. Installer le Gestionnaire de connexion hybride sur vos ressources sur site et connectez-vous à l’hybride de connexion spécifique. Le portail Azure fournit une expérience d’un simple clic pour installer et connecter.
4. Gérer les connexions hybride et leurs clés de connexion.

Cette rubrique répertorie ces étapes. 

> [AZURE.IMPORTANT] Il est possible de définir un point de terminaison de connexion de hybride à une adresse IP. Si vous utilisez une adresse IP, vous pouvez ou ne pénètrent pas la ressource sur site, en fonction de votre client. Le client effectuant une recherche DNS dépend de la connexion hybride. Dans la plupart des cas, le __client__ est le code de votre application. Si le client n’effectue pas une recherche DNS, (il n’essaie pas de résoudre l’adresse IP comme s’il s’agissait d’un nom de domaine (x.x.x.x)), puis le trafic n’est pas envoyé via la connexion hybride.
>
> Par exemple (en pseudo-code), vous définissez **10.4.5.6** comme votre hôte local :
> 
> **Le scénario suivant fonctionne :**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **Le scénario suivant ne fonctionne pas :**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`


## <a name="CreateHybridConnection"></a>Créer une connexion hybride

Une connexion hybrides peuvent être créée dans le portail Azure à l’aide d’applications Web **ou** à l’aide des Services de BizTalk. 

**Pour créer des connexions hybrides à l’aide d’applications Web**, voir [Connexion des applications Web Azure à une ressource locale](../app-service-web/web-sites-hybrid-connection-get-started.md). Vous pouvez également installer le Gestionnaire de connexion hybride (HCM) à partir de votre application web, qui est la méthode préférée. 

**Pour créer des connexions hybride dans les Services BizTalk**:

1. Connectez-vous à l' [Azure portal classique](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez les **Services BizTalk** , puis le BizTalk Service. 

    Si vous n’avez pas un BizTalk Service existant, vous pouvez [créer un BizTalk Service](biztalk-provision-services.md).
3. Sélectionnez l’onglet **Connexions d’hybrides** :  
![Onglet de connexions hybride][HybridConnectionTab]

4. Sélectionnez **créer une connexion hybride** ou cliquez sur le bouton **Ajouter** dans la barre des tâches. Entrez les informations suivantes :

    Propriété | Description
--- | ---
Nom | Le nom de connexion de hybride doit être unique et ne peut pas être le même nom que le BizTalk Service. Vous pouvez entrer n’importe quel nom mais spécifique avec son objectif. Voici quelques exemples :<br/><br/>Paie*SQLServer*<br/>SupplyList*SharepointServer*<br/>Clients*serveurOracle*
Nom d’hôte | Entrez le nom d’hôte complet, uniquement le nom d’hôte ou l’adresse IPv4 de la ressource locale. Voici quelques exemples :<br/><br/>mySQLServer<br/>*mySQLServer*. *Domaine*. corp.*votresociété*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*. *votresociété*.com<br/>10.100.10.10<br/><br/>Si vous utilisez l’adresse IPv4, notez que votre code client ou une application ne peut pas résoudre l’adresse IP. Consultez la Remarque importante au début de cette rubrique.
Port | Entrez le numéro de port de la ressource locale. Par exemple, si vous utilisez des applications Web, entrez le port 80 ou le port 443. Si vous utilisez SQL Server, entrez le port 1433.

5. Activez la case à cocher pour terminer l’installation. 

#### <a name="additional"></a>Supplémentaires

- Vous pouvez créer plusieurs connexions hybride. Consultez le [Services BizTalk : graphique des éditions de](biztalk-editions-feature-chart.md) pour le nombre de connexions autorisées. 
- Chaque connexion hybride est créée avec une paire de chaînes de connexion : Application clés que clés de locaux et d’envoi qui ÉCOUTENT. Chaque paire a une principale et une clé secondaire. 


## <a name="LinkWebSite"></a>Lier votre Azure Application Service Web App ou une application Mobile

Pour lier une application Web ou une application Mobile dans le Service d’application Azure à une connexion de hybride existant, sélectionnez **utiliser une connexion de hybride existant** dans la lame hybride connexions. Reportez-vous à la section [accès aux ressources à l’aide de connexions hybride dans Azure Application Service sur site](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="InstallHCM"></a>Installer le Gestionnaire de connexion hybride sur site

Après avoir créé une connexion hybride, installez le Gestionnaire de connexion hybride sur la ressource locale. Il peut être téléchargé à partir de vos applications web Azure ou votre BizTalk Service. Étapes des Services BizTalk : 

1. Connectez-vous à l' [Azure portal classique](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez les **Services BizTalk** , puis le BizTalk Service. 
3. Sélectionnez l’onglet **Connexions d’hybrides** :  
![Onglet de connexions hybride][HybridConnectionTab]
4. Dans la barre des tâches, sélectionnez **Le programme d’installation sur site**:  
![Programme d’installation sur site][HCOnPremSetup]
5. Sélectionnez **installer et le configurer** pour exécuter ou télécharger le Gestionnaire de connexion hybride sur le système local. 
6. Activez la case à cocher pour démarrer l’installation. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Supplémentaires
- Hybride Connection Manager peut être installé sur les systèmes d’exploitation suivants :

    - Windows Server 2008 R2 (de.NET Framework 4.5 + et de Windows Management Framework 4.0 + requis)
    - Windows Server 2012 (de Windows Management Framework 4.0 + requis)
    - Windows Server 2012 R2


- Après avoir installé le Gestionnaire de connexion hybride, les événements suivants se produisent : 

    - La connexion hybride hébergé sur Azure est automatiquement configurée pour utiliser la chaîne de connexion d’Application principal. 
    - La ressource locale est automatiquement configurée pour utiliser la chaîne de connexion locale principale.

- Le Gestionnaire de connexion hybride doit utiliser une chaîne de connexion valide sur site pour l’autorisation. Les applications Mobile Azure Web Apps doit utiliser une chaîne de connexion d’application valide pour l’autorisation.
- Vous pouvez adapter les connexions hybride en installant une autre instance du Gestionnaire de connexion hybride sur un autre serveur. Configurer le port d’écoute sur site pour utiliser la même adresse en tant que le premier écouteur local. Dans cette situation, le trafic est distribuée de manière aléatoire (round robin) entre les écouteurs active sur place. 


## <a name="ManageHybridConnection"></a>Gérer les connexions de hybride
Pour gérer vos connexions hybride, vous pouvez :

- Utiliser le portail Azure et accédez à votre BizTalk Service. 
- Utilisez les [API de reste](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copie/régénérer les chaînes de connexion hybride

1. Connectez-vous à l' [Azure portal classique](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez les **Services BizTalk** , puis le BizTalk Service. 
3. Sélectionnez l’onglet **Connexions d’hybrides** :  
![Onglet de connexions hybride][HybridConnectionTab]
4. Sélectionnez la connexion hybride. Dans la barre des tâches, sélectionnez **Gérer la connexion**:  
![Gérer les Options][HCManageConnection]

    **Gérer la connexion** répertorie les chaînes de connexion de l’Application et sur site. Vous pouvez copier les chaînes de connexion ou régénérer la clé d’accès utilisée dans la chaîne de connexion. 

    **Si vous sélectionnez régénérer (Regenerate)**, la touche d’accès partagé utilisé dans la chaîne de connexion est modifié. Effectuez le des opérations suivantes :
    - Dans le portail Azure classique, sélectionnez **Clés de synchronisation** dans l’application Azure.
    - Exécutez à nouveau **Le programme d’installation sur site**. Lorsque vous exécutez de nouveau le programme d’installation locale, la ressource locale est automatiquement configurée pour utiliser la chaîne de connexion de principal mis à jour.


#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Utiliser Stratégie de groupe pour contrôler les ressources sur site utilisés par une connexion hybride

1. Télécharger les [modèles d’administration du Gestionnaire de connexion hybride](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extraire les fichiers.
3. Sur l’ordinateur qui modifie la stratégie de groupe, effectuez le des opérations suivantes :  

    - Copie le. Fichiers ADMX dans le dossier *%WINROOT%\PolicyDefinitions* .
    - Copie le. Fichiers ADML dans le dossier *%WINROOT%\PolicyDefinitions\en-us* .

Une fois copié, vous pouvez utiliser l’éditeur de stratégie de groupe pour modifier la stratégie.




## <a name="next"></a>Suivant

[Applications Web Azure de se connecter à une ressource locale](../app-service-web/web-sites-hybrid-connection-get-started.md)  
[Se connecter à locale SQL Server à partir d’applications Web Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)   
[Vue d’ensemble des connexions hybride](integration-hybrid-connection-overview.md)


## <a name="see-also"></a>Voir aussi

[API REST pour gérer les Services BizTalk sur Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[Services de BizTalk : Graphique des éditions](biztalk-editions-feature-chart.md)  
[Création d’un BizTalk Service à l’aide d’Azure portal classique](biztalk-provision-services.md)  
[Services de BizTalk : Onglets de tableau de bord, du moniteur et échelle](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
