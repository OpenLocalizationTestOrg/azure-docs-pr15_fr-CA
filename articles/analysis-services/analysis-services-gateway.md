<properties
   pageTitle="Passerelle de données sur site | Microsoft Azure"
   description="Une passerelle locale est nécessaire si votre serveur d’Analysis Services dans Azure se connectent à des sources de données sur site."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="on-premises-data-gateway"></a>Passerelle de données sur site

La passerelle de données local agit comme un pont, assurant le transfert sécurisé des données entre des sources de données sur site et votre serveur Azure Analysis Services dans le nuage.

Une passerelle est installée sur un ordinateur de votre réseau. Une passerelle doit être installée pour chaque serveur Azure Analysis Services que vous avez dans votre abonnement Azure. Par exemple, si vous disposez de deux serveurs dans votre abonnement Azure qui se connectent à des sources de données sur site, une passerelle doit être installée sur les deux ordinateurs distincts de votre réseau.

## <a name="requirements"></a>Configuration requise

**Configuration minimale requise :**

- 4.5 de .NET framework
- version 64 bits de Windows 7 / Windows Server 2008 R2 (ou version ultérieure)

**Recommandé :**

- Base de 8 UC
- 8 Go de mémoire
- version 64 bits de Windows 2012 R2 (ou version ultérieure)

**Considérations importantes :**

- La passerelle ne peut pas être installée sur un contrôleur de domaine.

- Une seule passerelle peut être installée sur un seul ordinateur.

- Installez la passerelle sur un ordinateur qui reste sur et ne se met en veille. Si l’ordinateur n’est pas sur, votre serveur Azure Analysis Services ne peut pas se connecter à vos sources de données sur site pour actualiser les données.

- N’installez pas la passerelle sur un ordinateur sans fil connecté à votre réseau. Peut être réduit les performances.

- Pour modifier le nom du serveur de passerelle qui a déjà été configuré, vous devez réinstaller et configurer une nouvelle passerelle.

- Dans certains cas, les modèles sous forme de tableau de la connexion aux sources de données à l’aide des fournisseurs natifs tels que SQL Server Native Client (SQLNCLI11) peuvent renvoyer une erreur. Pour plus d’informations, voir [connexions de source de données](analysis-services-datasource.md).

## <a name="supported-on-premises-data-sources"></a>Sources de données prises en charge sur site
Pour l’aperçu, la passerelle prend en charge les connexions entre votre serveur Azure Analysis Services et les sources de données sur les sites suivants :

- SQL Server
- Entrepôt de données SQL
- POINTS D’ACCÈS
- Oracle
- Teradata


## <a name="download"></a>Télécharger
 [Télécharger la passerelle](https://aka.ms/azureasgateway)


## <a name="install-and-configure"></a>Installer et configurer

1. Exécutez le programme d’installation.

2. Choisissez un emplacement d’installation et acceptez les termes de la licence.

3. Se connecter à Azure.

4. Spécifiez le nom de votre serveur d’analyse Azure. Vous ne pouvez spécifier qu’un seul serveur par passerelle. Cliquez sur **configurer** et vous êtes fin prêt.

    ![Ouvrez une session pour azure](./media\analysis-services-gateway\aas-gateway-configure-server.png)


## <a name="how-it-works"></a>Mode de fonctionnement
La passerelle s’exécute comme un service Windows, **passerelle de données en local**, sur un ordinateur dans le réseau de votre organisation. La passerelle que vous installez pour une utilisation avec les Services d’analyse Azure est basée sur la même passerelle utilisée pour d’autres services comme alimentation BI, mais avec quelques différences dans la manière dont il est configuré.

![Mode de fonctionnement](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Les requêtes et les données de flux travail comme suit :

1.  Une requête est créée par le service en nuage avec les informations d’identification chiffrées pour la source de données sur site. Il est ensuite envoyé à une file d’attente de la passerelle à traiter.

2.  Le service en nuage gateway analyse la requête et exécute un push de la demande pour le [Bus des services Azure](https://azure.microsoft.com/documentation/services/service-bus/).

3.  La passerelle de données local interroge le Bus des services Azure pour les demandes en attente.

4.  Obtient la requête, la passerelle décrypte les informations d’identification et se connecte aux sources de données avec les informations d’identification.

5.  La passerelle envoie la requête à la source de données pour l’exécution.

6.  Les résultats sont envoyés à partir de la source de données, à la passerelle, puis sur le service en nuage.

## <a name="windows-service-account"></a>Compte de Service Windows

La passerelle de données local est configurée pour utiliser *NT SERVICE\PBIEgwService* pour les informations d’identification d’ouverture de session de Windows service. Par défaut, elle a le droit d’ouverture de session en tant que service ; dans le contexte de l’ordinateur que vous installez sur la passerelle. Cette information d’identification n’est pas le même compte que celui utilisé pour se connecter aux sources de données sur site ou votre compte Azure.  

Si vous rencontrez des problèmes avec votre serveur proxy en raison de l’authentification, vous souhaiterez peut-être modifier le compte de service Windows à un utilisateur de domaine ou compte de service géré.

## <a name="ports"></a>Ports

La passerelle crée une connexion sortante vers Bus de Service d’Azure. Il communique sur des ports de sortie : 443 TCP (par défaut), 5671, 5672, 9350, via 9354.  La passerelle n’a pas besoin des ports d’entrée.

Il est recommandé d’autorisation les adresses IP de votre région de données dans votre pare-feu. Vous pouvez télécharger la [liste des IP de Datacenter Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Cette liste est mise à jour hebdomadaire.

> [AZURE.NOTE]  Les adresses IP figurant dans la liste IP du centre de données Azure sont dans la notation CIDR. Par exemple, 10.0.0.0/24 ne signifie pas 10.0.0.0 via 10.0.0.24. Pour en savoir plus à propos de la [notation CIDR](http://whatismyipaddress.com/cidr).

Voici les noms de domaine pleinement qualifié utilisés par la passerelle.

|Noms de domaine|Ports de sortie|Description|
|---|---|---|
|*. powerbi.com|80|HTTP permet de télécharger le programme d’installation.|
|*. powerbi.com|443|HTTPS|
|*. analysis.windows.net|443|HTTPS|
|*. login.windows.net|443|HTTPS|
|*. servicebus.windows.net|5671-5672|Avancées Message Queuing Protocol (AMQP)|
|*. servicebus.windows.net|443, 9350-9354|Écouteurs sur relais de Bus de Service sur TCP (nécessite le 443 pour l’acquisition de jeton de contrôle d’accès)|
|*. frontend.clouddatahub.net|443|HTTPS|
|*. core.windows.net|443|HTTPS|
|Login.microsoftonline.com|443|HTTPS|
|*. msftncsi.com|443|Utilisé pour tester la connectivité à internet si la passerelle est inaccessible par le service d’alimentation BI.|
|*.microsoftonline-p.com|443|Utilisé pour l’authentification en fonction de la configuration.|


### <a name="forcing-https-communication-with-azure-service-bus"></a>Forcer la communication HTTPS avec Bus des services Azure

Vous pouvez forcer la passerelle pour communiquer avec le Bus des services Azure utilisant le protocole HTTPS au lieu de TCP directe ; Toutefois, elle peut réduire considérablement les performances. Vous devez modifier le fichier *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* . Modifiez la valeur de `AutoDetect` à `Https`. Ce fichier se trouve, par défaut, au niveau de *passerelle de données locaux C:\Program Files\On*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>Résolution des problèmes
Dans la pratique, la passerelle de données locaux utilisée pour la connexion Azure Analysis Services pour vos sources de données sur site est la même passerelle utilisée avec alimentation BI.

Si vous rencontrez des problèmes lors de l’installation et la configuration d’une passerelle, veillez à voir [résolution des problèmes de la passerelle de BI d’alimentation](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/). Si vous pensez que vous rencontrez un problème avec votre pare-feu, consultez les sections de proxy ou de pare-feu.

Si vous pensez que vous rencontrez des problèmes de proxy, grâce à la passerelle, consultez [Configuration des paramètres de proxy pour les passerelles de BI d’alimentation](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md).

## <a name="next-steps"></a>Étapes suivantes
- [Gérer les Services d’analyse](analysis-services-manage.md)
- [Obtenir des données d’Azure Analysis Services](analysis-services-connect.md)
