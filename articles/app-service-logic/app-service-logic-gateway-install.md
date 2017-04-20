<properties
   pageTitle="Logique Apps installer passerelle de données sur site | Microsoft Azure"
   description="Informations sur l’installation de la passerelle de données local pour une utilisation dans une logique d’application."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Installation de la passerelle de données sur site pour les applications de logique

## <a name="installation-and-configuration"></a>Installation et Configuration

### <a name="prerequisites"></a>Conditions préalables

Minimum :

* 4.5 de .NET framework
* version 64 bits de Windows 7 ou Windows Server 2008 R2 (ou version ultérieure)

Recommandé :

* Base de 8 UC
* 8 Go de mémoire
* version 64 bits de Windows 2012 R2 (ou version ultérieure)

Considérations liées :

* Vous ne pouvez pas installer une passerelle sur un contrôleur de domaine.
* Vous ne devez pas installer une passerelle sur un ordinateur, tel un ordinateur portable, peut être éteint, en veille, ou pas connecté à Internet, car la passerelle ne peut pas s’exécuter dans une de ces circonstances. En outre, les performances de la passerelle peuvent en souffrir sur un réseau sans fil.

### <a name="install-a-gateway"></a>Installer une passerelle

Vous pouvez obtenir [programme d’installation de la passerelle de données sur site ici](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Spécifiez le mode **passerelle de données local** , connectez-vous avec votre travail ou école de compte, puis configurer une passerelle ou migrer, restaurer ou prendre une passerelle existante.

* Pour configurer une passerelle, tapez un **nom** et une **clé de récupération**, puis cliquez sur ou cliquez sur **configurer**.

    Spécifier une clé de récupération qui contient au moins huit caractères et conservez-le dans un endroit sûr. Vous aurez besoin de cette clé si vous souhaitez migrer, de restaurer ou de prendre sa passerelle.

* Pour migrer, restaurer ou prendre une passerelle existante, indiquez la clé de récupération qui a été spécifiée lors de la création de la passerelle.

### <a name="restart-the-gateway"></a>Redémarrer la passerelle

La passerelle s’exécute comme un service Windows et, comme avec n’importe quel autre service Windows, vous pouvez démarrer et arrêter de plusieurs façons. Par exemple, vous pouvez ouvrir une invite de commandes avec des autorisations élevées sur l’ordinateur exécutant la passerelle et puis exécutez une ou l’autre de ces commandes :

* Pour arrêter le service, exécutez la commande suivante :

    `net stop PBIEgwService`

* Pour démarrer le service, exécutez la commande suivante :

    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configurer un pare-feu ou un proxy

Pour plus d’informations sur la façon de fournir les informations de proxy pour votre passerelle, consultez [configurer les paramètres de proxy](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/).

Vous pouvez vérifier si votre pare-feu ou proxy peut bloquer les connexions en exécutant la commande suivante à partir d’une invite de commandes PowerShell. Cela va tester la connectivité avec le Bus des services Azure. Ce seul teste la connectivité du réseau et n’ont rien à voir avec le service de serveur de nuage ou de la passerelle. Il permet de déterminer si votre ordinateur peut effectivement y accéder l’internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Le résultat doit ressembler à cet exemple. Si **TcpTestSucceeded** n’est pas remplie, vous pouvez être bloqué par un pare-feu.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Si vous souhaitez être exhaustive, remplacez les valeurs de **nom_ordinateur** et le **Port** à ceux répertoriés sous [configurer les ports](#configure-ports) plus loin dans cette rubrique.

Le pare-feu peut également bloquer les connexions que le Bus des services Azure permet aux centres de données Azure. Si tel est le cas, vous souhaiterez d’autorisation (débloquer) toutes les adresses IP de votre région pour les centres de données. Vous pouvez obtenir une liste des [adresses IP d’Azure ici](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configurer les ports

La passerelle crée une connexion sortante vers Bus de Service d’Azure. Il communique sur des ports de sortie : 443 TCP (par défaut), 5671, 5672, 9350, via 9354. Les ports entrants ne nécessite pas la passerelle.

Pour en savoir plus sur [les solutions hybride](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMS DE DOMAINE | PORTS DE SORTIE | DESCRIPTION |
| ----- | ------ | ------ |
| *. analysis.windows.net | 443 | HTTPS |
| *. login.windows.net | 443 | HTTPS |
| *. servicebus.windows.net |5671-5672 | Avancées Message Queuing Protocol (AMQP) |
| *. servicebus.windows.net | 443, 9350-9354 | Écouteurs sur relais de Bus de Service sur TCP (nécessite le 443 pour l’acquisition de jeton de contrôle d’accès) |
| *. frontend.clouddatahub.net | 443 | HTTPS |
| *. core.windows.net | 443 | HTTPS |
| Login.microsoftonline.com | 443 | HTTPS |
| *. msftncsi.com | 443 | Utilisé pour tester la connectivité à internet si la passerelle est inaccessible par le service d’alimentation BI. |

Si vous avez besoin pour la liste d’autorisation des adresses IP plutôt que les domaines, vous pouvez télécharger et utiliser la [liste des plages IP de Datacenter Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Dans certains cas, les connexions de Bus des services Azure sont effectuées avec une adresse IP au lieu des noms de domaine pleinement qualifié.

### <a name="sign-in-account"></a>Compte de connexion

Les utilisateurs vous connecter soit un travail ou l’école de compte. Il s’agit de votre compte de l’organisation. Si vous souscrit une offre Office 365 et que vous n’avez pas de fournir votre adresse de courriel du travail réel, il peut ressembler à jeff@contoso.onmicrosoft.com. Votre compte, au sein d’un service en nuage, est stocké dans un locataire dans Azure Active Directory (DAS). Dans la plupart des cas, UPN de votre compte DAS correspondra à l’adresse de messagerie.

### <a name="windows-service-account"></a>Compte de Service Windows

La passerelle de données local est configurée pour utiliser NT SERVICE\PBIEgwService pour les informations d’identification d’ouverture de session de Windows service. Par défaut, il a le droit de journal sur en tant que service. C’est dans le contexte de l’ordinateur sur lequel vous installez la passerelle.

Ce n’est pas le compte utilisé pour se connecter à des sources de données en local ou le compte de l’école ou de travail avec lequel vous vous connectez aux services en nuage.

##<a name="frequently-asked-questions"></a>Forum aux questions

### <a name="general"></a>Général

**Question**: quelles sources de données pris en charge par la passerelle ?<br/>
**Réponse**: à partir de cette écriture, SQL Server.

**Question**: ai-je besoin d’une passerelle pour les sources de données dans le nuage, telles que SQL Azure ? <br/>
**Réponse**: non. Une passerelle connecte aux sources de données locales uniquement.

**Question**: ce que l'on appelle le service Windows réel ?<br/>
**Réponse**: dans les Services, la passerelle est appelée Service de passerelle de puissance BI entreprise.

**Question**: existe-t-il des toutes les connexions entrantes à la passerelle dans le nuage ? <br/>
**Réponse**: non. La passerelle utilise les connexions sortantes vers Bus de Service d’Azure.

**Question**: que se passe-t-il si je bloque les connexions sortantes ? Que dois-je ouvrir ? <br/>
**Réponse**: consultez les ports et les hôtes qui utilise de la passerelle.


**Question**: la passerelle doit-il être installé sur le même ordinateur que la source de données ? <br/>
**Réponse**: non. La passerelle qui se connecte à la source de données en utilisant les informations de connexion qui a été fournies. Considérez la passerelle comme une application client dans ce sens. Elle devra simplement être en mesure de se connecter au nom du serveur qui a été fourni.


**Question**: quel est le délai d’exécution des requêtes à une source de données à partir de la passerelle ? Quelle est la meilleure architecture ? <br/>
**Réponse**: pour réduire la latence du réseau, installation de la passerelle aussi proche de la source de données que possible. Si vous pouvez installer la passerelle sur la source de données, elle permet de minimiser le temps de latence. Envisagez également les centres de données. Par exemple, si votre service est à l’aide du centre de données États-Unis Ouest et que SQL Server hébergées dans un ordinateur virtuel d’Azure, vous voudrez avoir l’Azure VM ouest des États-Unis ainsi. Cela minimiser la latence et éviter des frais de sortie sur la machine virtuelle d’Azure.


**Question**: existe-t-il des conditions de bande passante réseau ? <br/>
**Réponse**: il est recommandé d’avoir bon débit de votre connexion réseau. Chaque environnement est différent, et la quantité de données envoyées affecte les résultats. À l’aide de ExpressRoute pourrait aider à garantir un niveau de débit entre les centres de données Azure et locaux.

Vous pouvez utiliser l’application de Test de vitesse Azure un outil tiers pour aider à déterminer quel est le débit.


**Question**: peut la passerelle service Windows exécuté avec un compte Azure Active Directory ? <br/>
**Réponse**: non. Le service Windows doit avoir un compte Windows valide. Par défaut, il s’exécute avec le SID de Service, SERVICE\PBIEgwService de NT.


**Question**: comment résultats sont envoyés vers le nuage ? <br/>
**Réponse**: cela par le Bus des services Azure. Pour plus d’informations, voir comment il fonctionne.


**Question**: où sont stockées mes informations d’identification ? <br/>
**Réponse**: les informations d’identification que vous entrez pour une source de données sont stockées cryptées dans le service de cloud de passerelle. Les informations d’identification sont décryptées au niveau de la passerelle locale.

### <a name="high-availabilitydisaster-recovery"></a>Haute disponibilité/récupération d’urgence

**Question**: existe-t-il des plans pour activer les scénarios de haute disponibilité avec la passerelle ? <br/>
**Réponse**: c’est le plan d’évolution, mais nous n’avons pas encore une barre de planning.


**Question**: quelles sont les options disponibles pour la reprise après sinistre ? <br/>
**Réponse**: vous pouvez utiliser la clé de récupération pour restaurer ou déplacer une passerelle. Lorsque vous installez la passerelle, spécifiez la clé de récupération.


**Question**: quel est l’avantage de la clé de récupération ? <br/>
**Réponse**: il fournit un moyen de migrer ou de récupérer les paramètres de votre passerelle après un sinistre.

### <a name="troubleshooting"></a>Résolution des problèmes

**Question**: où sont les journaux de la passerelle ? <br/>
**Réponse**: voir outils plus loin dans cette rubrique.


**Question**: Comment puis-je voir ce que les requêtes sont en cours envoyées à la source de données sur site ? <br/>
**Réponse**: vous pouvez activer le traçage de requête, y compris les requêtes en cours d’envoi. N’oubliez pas de modifier la valeur d’origine lorsque terminé le dépannage. En laissant le traçage activé de requête provoque les journaux supérieure.

Vous pouvez également consulter des outils votre source de données pour les requêtes de suivi. Par exemple, vous pouvez utiliser des événements étendus ou le Générateur de profils SQL pour SQL Server et Analysis Services.

## <a name="how-the-gateway-works"></a>Fonctionne de la passerelle

Lorsqu’un utilisateur interagit avec un élément qui est connecté à une source de données sur site :

1. Le service en nuage crée une requête, ainsi que les informations d’identification cryptées pour la source de données et envoie la requête à la file d’attente de la passerelle à traiter.
1. Le service analyse la requête et exécute un push de la demande pour le Bus des services Azure.
1. La passerelle de données local interroge le Bus des services Azure pour les demandes en attente.
1. Obtient la requête, la passerelle décrypte les informations d’identification et se connecte aux sources de données avec les informations d’identification.
1. La passerelle envoie la requête à la source de données pour l’exécution.
1. Les résultats sont envoyés à partir de la source de données à la passerelle, puis sur le service en nuage. Le service utilise ensuite les résultats.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="update-to-the-latest-version"></a>Mise à jour vers la version la plus récente

Un grand nombre de problèmes de surface lorsque la version de la passerelle est obsolète.  Il est recommandé pour vous assurer que vous êtes sur la dernière version.  Si vous n’avez pas mis à jour de la passerelle pour un mois, ou plus, vous souhaiterez peut-être envisager d’installer la dernière version de la passerelle et de voir si vous pouvez reproduire le problème.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>Erreur : Impossible d’ajouter l’utilisateur au groupe. (Utilisateurs du journal de performances-2147463168 PBIEgwService)

Vous pouvez recevoir cette erreur si vous essayez d’installer la passerelle sur un contrôleur de domaine, ce qui n’est pas pris en charge. Vous devez déployer la passerelle sur un ordinateur qui n’est pas d’un contrôleur de domaine.

## <a name="tools"></a>Outils

### <a name="collecting-logs-from-the-gateway-configurator"></a>Collecter les journaux de l’outil de configuration de passerelle

Vous pouvez collecter plusieurs journaux pour la passerelle. Commencez toujours par les journaux !

#### <a name="installer-logs"></a>Journaux d’installation

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Journaux de configuration

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Journaux de service de passerelle de Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Journaux des événements

Les journaux de la passerelle de gestion des données et PowerBIGateway sont présents dans **l’Application et les journaux des Services**.

### <a name="fiddler-trace"></a>Trace Fiddler

[Fiddler](http://www.telerik.com/fiddler) est un outil gratuit de Telerik qui surveille le trafic HTTP.  Vous pouvez voir l’arrière et en avant avec la BI d’alimentation service à partir de l’ordinateur client. Cela peut indiquer des erreurs et autres informations connexes.

## <a name="next-steps"></a>Étapes suivantes
- [Créer une connexion locale à la logique d’applications](app-service-logic-gateway-connection.md)
- [Fonctionnalités d’intégration entreprise](app-service-logic-enterprise-integration-overview.md)
- [Connecteurs d’applications logique](../connectors/apis-list.md)