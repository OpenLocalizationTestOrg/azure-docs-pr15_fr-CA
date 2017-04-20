<properties
    pageTitle="Vue d’ensemble des connexions hybride | Microsoft Azure"
    description="Obtenir des informations sur les connexions hybride, la sécurité, les ports TCP et configurations prises en charge. MABS, WABS."
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
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ccompy"/>


# <a name="hybrid-connections-overview"></a>Vue d’ensemble des connexions hybride
Introduction aux connexions de hybride, répertorie les configurations prises en charge et les ports TCP requis.


## <a name="what-is-a-hybrid-connection"></a>Ce qu’est une connexion hybride

Hybride connexions sont une fonctionnalité des Services de BizTalk Azure. Les connexions hybride fournissent un moyen simple et pratique pour connecter la fonctionnalité d’applications Web dans le Service d’application Azure (anciennement sites Web) et la fonctionnalité d’applications Mobile dans le Service d’application Azure (anciennement Mobile Services) aux ressources sur site derrière votre pare-feu.

![Connexions hybride][HCImage]

Avantages de connexions hybride :

- Applications Web et applications Mobile peuvent accéder en toute sécurité les données sur les sites et les services existants.
- Plusieurs applications Web ou applications Mobile peuvent partager une connexion hybride pour accéder à une ressource locale.
- Les ports TCP minimales sont requis pour accéder à votre réseau.
- Applications à l’aide de connexions de hybride accéder à la ressource locale spécifique qui est publiée via la connexion hybride.
- Peut se connecter à n’importe quelle ressource locale qui utilise un port TCP statique, tel que SQL Server, MySQL, API de Web HTTP et la plupart des Services Web personnalisés.

    > [AZURE.NOTE] Les services TCP qui utilisent des ports dynamiques (par exemple, le Mode FTP passif ou en Mode passif étendu) ne sont actuellement pas pris en charge. LDAP n’est également pas prise en charge. LDAP utilise un port TCP statique, mais il peut également utiliser UDP. Par conséquent, il n’est pas supportée.

- Peut être utilisé avec toutes les infrastructures sont pris en charge par les applications Web (.NET, PHP, Java, Python, Node.js) et les applications Mobile (Node.js, .NET).
- Les applications Web et applications Mobile accessibles les ressources sur site exactement de la même façon que si le site Web ou une application Mobile se trouve sur votre réseau local. Par exemple, la même connexion chaîne utilisée sur site peut également servir dans Azure.


Hybride connexions fournissent également des administrateurs de l’entreprise avec le contrôle et la visibilité des ressources d’entreprise pour accéder à des applications hybrides, y compris :

- Paramètres de stratégie de groupe, les administrateurs peuvent autoriser les connexions hybride sur le réseau et également désigner les ressources qui sont accessibles par les applications hybrides.
- Les journaux d’audit et d’événements sur le réseau d’entreprise fournissent une visibilité sur les ressources accédé via des connexions de hybride.


## <a name="example-scenarios"></a>Exemples de scénarios

Les connexions hybride prennent en charge les combinaisons suivantes de l’infrastructure et des applications :

- Accès .NET framework pour SQL Server
- Accès .NET framework pour les services HTTP/HTTPS avec WebClient
- Accès à SQL Server, MySQL PHP
- Accès à SQL Server, MySQL et Oracle Java
- Accès Java pour les services HTTP/HTTPS

Lors de l’utilisation hybride à accès local SQL Server, prenez en compte les éléments suivants :

- Instances nommées de SQL Express doit être configuré pour utiliser les ports statiques. Par défaut, SQL Express instances nommées utiliser des ports dynamiques.
- Instances par défaut de SQL Express utiliser un port statique, mais TCP doit être activée. Par défaut, TCP n’est pas activé.
- Lors de l’utilisation de clusters ou groupes de la disponibilité, la `MultiSubnetFailover=true` mode n’est actuellement pas pris en charge.
- Le `ApplicationIntent=ReadOnly` n’est actuellement pas pris en charge.
- L’authentification SQL peut être requise comme la méthode d’autorisation de bout en bout pris en charge par l’application Azure et le serveur SQL local.


## <a name="security-and-ports"></a>Ports et sécurité

Hybride connexions permet de sécuriser les connexions entre les applications Azure et le Gestionnaire de connexion hybride sur site et la connexion hybride d’autorisation de Signature de l’accès partagé (SAS). Clés de connexion distincts sont créés pour l’application et le Gestionnaire de connexion hybride sur site. Ces clés de connexion peuvent être annulés et révoqués de manière indépendante.

Hybride connexions fournissent pour distribution transparente et sécurisée des clés pour les applications et le Gestionnaire de connexion hybride sur site.

Reportez-vous à la section [créer et de gérer les connexions hybride](integration-hybrid-connection-create-manage.md).

*Autorisation de l’application est séparée de la connexion hybride*. N’importe quelle méthode d’autorisation appropriée peut être utilisée. La méthode d’autorisation dépend des méthodes d’autorisation de bout en bout pour prise en charge sur le nuage Azure et les composants locaux. Par exemple, votre application Azure accède à une de SQL Server sur site. Dans ce scénario, l’autorisation de SQL peut être la méthode d’autorisation qui est prise en charge de bout en bout.

#### <a name="tcp-ports"></a>Ports TCP
Hybride connexions nécessitent une connectivité TCP ou HTTP sortante uniquement à partir de votre réseau privé. Vous n’avez pas besoin d’ouvrir les ports de pare-feu ou de modifier votre configuration de réseau de périmètre pour autoriser les connexions entrantes sur votre réseau.

Les ports TCP suivants sont utilisés par les connexions hybride :

Port | Pourquoi vous en avez besoin
--- | ---
9350 - 9354 | Ces ports sont utilisés pour la transmission de données. Le Gestionnaire de relais du Bus de Service sondes port 9350 pour déterminer si la connectivité TCP est disponible. S’il est disponible, il suppose que le port 9352 est également disponible. Le trafic de données passe par le port 9352. <br/><br/>Autoriser les connexions sortantes vers ces ports.
5671 | Lorsque le port 9352 est utilisé pour le trafic de données, le port 5671 est utilisé en tant que le canal de contrôle. <br/><br/>Autoriser les connexions sortantes vers ce port.
80, 443 | Ces ports sont utilisés pour les demandes de certaines données sur Azure. En outre, si les ports 9352 et 5671 ne sont pas utilisables, *puis* les ports 80 et 443 sont les ports de secours utilisés pour la transmission de données et le canal de contrôle.<br/><br/>Autoriser les connexions sortantes vers ces ports. <br/><br/>**Remarque** Il n’est pas recommandé à utiliser en tant que les ports de secours à la place d’autres ports TCP. Le HTTP/WebSocket est utilisé comme protocole au lieu de TCP natif pour canaux de données. Il peut entraîner des performances inférieures.



## <a name="next-steps"></a>Étapes suivantes

[Créer et gérer les connexions de hybride](integration-hybrid-connection-create-manage.md)<br/>
[Applications Web Azure de se connecter à une ressource locale](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[Se connecter à locale SQL Server à partir d’une application web Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>


## <a name="see-also"></a>Voir aussi

[API REST pour gérer les Services BizTalk sur Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[les Services BizTalk : graphique des éditions de](biztalk-editions-feature-chart.md)<br/>
[Création d’un BizTalk Service à l’aide du portail Azure](biztalk-provision-services.md)<br/>
[Services de BizTalk : Onglets de tableau de bord, du moniteur et échelle](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
