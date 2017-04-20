<properties
   pageTitle="Exemple de zone DMZ – créer un réseau de périmètre pour protéger les applications avec un pare-feu et NSGs | Microsoft Azure"
   description="Créer un réseau de périmètre avec un pare-feu et des groupes de sécurité réseau (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Exemple 2 – créer un réseau de périmètre pour protéger les applications avec un pare-feu et NSGs

[Revenir à la Page de pratiques meilleure sécurité limite][HOME]

Cet exemple crée un réseau de périmètre avec un pare-feu, quatre serveurs windows et les groupes de sécurité de réseau. Vous y découvrirez également comment chacune des commandes utiles pour fournir une meilleure compréhension de chaque étape. Il existe également une section de scénario de trafic pour fournir une profondeur détaillées comment le trafic se poursuit à travers les couches de défense de la zone DMZ. Enfin, dans les références de la section est le code complet et les instructions pour créer cet environnement pour tester et expérimenter différents scénarios. 

![DMZ entrant à couloirs et NSG][1]

## <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

- Deux services en nuage : « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel « CorpNetwork », avec deux sous-réseaux : « Frontal » et « Back-end »
- Un seul groupe de sécurité réseau qui est appliquée pour les deux sous-réseaux.
- Un matériel de réseau virtuel, dans cet exemple, un pare-feu NextGen Barracuda, connecté au sous-réseau frontal
- Un serveur Windows qui représente un serveur web d’application (« IIS01 »)
- Serveurs de deux fenêtres qui représentent l’application back end serveurs (« AppVM01 », « AppVM02 »)
- Un serveur Windows qui représente un serveur DNS (« DNS01 »)

>[AZURE.NOTE] Bien que cet exemple utilise un pare-feu de NextGen Barracuda, bon nombre des différents appareils réseau virtuel pourraient être utilisés pour cet exemple.

Dans la section références ci-dessous est un script PowerShell qui générera la majeure partie de l’environnement décrite ci-dessus. Créer les ordinateurs virtuels et les réseaux virtuels, bien que sont effectuées par l’exemple de script, pas décrits en détail dans ce document.
 
Pour créer l’environnement :

  1.    Enregistrez le fichier xml de configuration réseau inclus dans la section Références (mis à jour avec IP, l’emplacement et les noms qui correspondent à un scénario donné)
  2.    Mettre à jour les variables d’utilisateur dans le script pour correspondre à l’environnement, que le script doit être exécuté sur (les abonnements, les noms de service, etc.)
  3.    Exécutez le script dans PowerShell

**Remarque**: la région signifiée dans le script PowerShell doit correspondre à la région indiquée dans le fichier xml de configuration réseau.

Une fois que le script s’exécute avec succès les étapes suivantes de scripts postérieur à peuvent être prises :

1.  Définir les règles de pare-feu, ce sujet est traité dans la section ci-dessous intitulée : règles de pare-feu.
2.  Si vous le souhaitez dans la section références sont deux scripts pour configurer le serveur web et le serveur d’applications avec une application web simple permettant de tester cette configuration DMZ.

La section suivante explique la plupart des instructions de scripts par rapport aux groupes de sécurité réseau.

## <a name="network-security-groups-nsg"></a>Groupes de sécurité réseau (NSG)
Pour cet exemple, un groupe NSG est généré et ensuite chargé avec six règles. 

>[AZURE.TIP] En règle générale, vous devez d’abord créer vos règles « Autoriser » spécifiques, et enfin les règles plus génériques « Refuser ». Exigences imposées par la priorité affectée dont les règles sont évaluées premier. Une fois que le trafic est trouvé à appliquer à une règle spécifique, aucuns plus de règles ne sont évaluées. Règles NSG s’appliquent dans une ou l’autre dans la direction entrante ou sortante (du point de vue du sous-réseau).

De façon déclarative, les règles suivantes sont en cours de génération pour le trafic entrant :

1.  Le trafic DNS (port 53) interne est autorisé
2.  Le trafic RDP (le port 3389) à partir d’Internet à toute machine virtuelle est autorisé
3.  Le trafic HTTP (port 80) à partir d’Internet vers l’à couloirs (pare-feu) est autorisé.
4.  Tout le trafic (tous les ports) à partir de IIS01 à AppVM1 est autorisé.
5.  Tout le trafic (tous les ports) à partir d’Internet à l’ensemble de VNet (les deux sous-réseaux) est refusé.
6.  Tout le trafic (tous les ports) à partir du sous-réseau frontal au sous-réseau back-end est refusé.

Avec ces règles lié à chaque sous-réseau, si une requête HTTP a été entrante provenant d’Internet vers le serveur web, les deux règles 3 (autoriser) et 5 (refuser) s’appliquent, mais dans la mesure où les règles 3 a une priorité plus élevée qu’il appliquerait et règle 5 ne serait pas entrent en jeu. Ainsi, la requête HTTP serait autorisée au pare-feu. Si vous essayez le même trafic d’atteindre le serveur DNS01, règle 5 (Deny) est le premier à appliquer et le trafic ne serait pas autorisé à transmettre au serveur. Règle 6 (Deny) bloque le sous-réseau frontal de communiquer sur le sous-réseau du serveur principal (à l’exception du trafic autorisé dans les règles 1 et 4), cela protège le réseau back-end en cas un intrus découvre que l’application web sur le site Web frontal, l’attaquant aurait un accès limité au réseau protégé « back-end » (uniquement pour les ressources exposées sur le serveur de AppVM01).

Il existe une règle sortante par défaut qui autorise le trafic vers internet. Pour cet exemple, nous allons autorisant le trafic sortant et ne modifie ne pas les règles de trafic sortant. Pour verrouiller le trafic dans les deux directions, le routage défini par l’utilisateur est requise, il est exploré dans un autre exemple qui peut trouve dans le [document de limite de sécurité principal][HOME].

Les règles NSG évoquées ci-dessus sont très similaires aux règles NSG dans [l’exemple 1 - créer une Simple DMZ avec NSGs][Example1]. Veuillez consulter la Description du NSG dans ce document pour plus de détails à chaque règle NSG et ses attributs.

## <a name="firewall-rules"></a>Règles de pare-feu
Gestion client aura besoin doit être installé sur un ordinateur pour gérer le pare-feu et créer les configurations nécessaires. Comment gérer le périphérique, consultez le fournisseur de la documentation de votre pare-feu (ou autres à couloirs). Le reste de cette section décrit la configuration du pare-feu, via le client de gestion de fournisseurs (c'est-à-dire non Azure portal ou PowerShell).

Instructions de téléchargement du client et de la connexion à la Barracuda utilisé dans cet exemple se trouve ici : [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Sur le pare-feu, règles de transfert devra être créé. Cet exemple achemine uniquement le trafic internet sur le pare-feu, puis sur le serveur web entrant, transfert d’une seule règle TAR est nécessaire. Sur le pare-feu NextGen Barracuda utilisé dans cet exemple, la règle serait une règle NAT de Destination (« l’heure d’été NAT ») pour transmettre ce trafic.

Pour créer la règle suivante (ou vérifier les règles par défaut existant), à partir du tableau de bord de client Barracuda NG Admin, accédez à l’onglet configuration, dans la Configuration opérationnelle de section, cliquez sur groupe de règles. « Règles de Main » affiche une grille est appelée, les règles existantes d’actifs et désactivés sur le pare-feu. Dans le coin supérieur droit de cette grille est un petit, vert « + » bouton, cliquez sur ce bouton pour créer une nouvelle règle (Remarque : votre pare-feu peut être « verrouillé » pour les modifications, si vous voyez un bouton marqué « Verrouiller » et vous ne pouvez pas créer ou modifier des règles, cliquez sur ce bouton pour « déverrouiller » le groupe de règles et d’autoriser la modification). Si vous souhaitez modifier une règle existante, sélectionnez cette règle, avec le bouton droit et sélectionnez Modifier la règle.

Créer une nouvelle règle et indiquez un nom, tel que « WebTraffic ». 

L’icône de règle NAT de Destination se présente comme suit : ![Icône NAT de Destination][2]

La règle elle-même ressemblerait à ceci :

![Règle de pare-feu][3]

Ici tout trafic entrant adresse qui atteint le pare-feu essayez d’atteindre HTTP (port 80 ou 443 pour HTTPS) est envoyée à l’interface du pare-feu « DHCP1 Local IP » et redirigé vers le serveur Web avec l’adresse IP de 10.0.1.5. Dans la mesure où le trafic est entrant sur le port 80 et accéder au serveur web sur le port 80, aucune modification du port n’était nécessaire. Toutefois, la liste des cibles aurait pu être 10.0.1.5:8080 si notre serveur Web écoutait sur le port 8080 donc traduire le port d’entrée 80 sur le pare-feu entrant port 8080 sur le serveur web.

Une méthode de connexion doit également être signifiée, pour la règle de Destination à partir d’Internet, « SNAT dynamique » est la plus appropriée. 

Bien qu’une règle a été créée, il est important que sa priorité est correctement définie. Si dans la grille de toutes les règles du pare-feu, cette nouvelle règle se trouve au bas (en dessous de la règle « BLOCKALL ») il n’entreront jamais en ligne. Assurez-vous que la règle nouvellement créée pour le trafic web est au-dessus de la règle BLOCKALL.

Une fois que la règle est créée, doivent être diffusée sur le pare-feu et activé puis, si cela n’est pas la modification de règle ne prendront effet. Le processus de transmission et d’activation est décrite dans la section suivante.

## <a name="rule-activation"></a>Activation de règle
Avec l’ensemble de règles modifié pour ajouter cette règle, le groupe de règles doit être chargé sur le pare-feu et activé.

![Activation de règle de pare-feu][4]

Dans le coin supérieur droit du client de gestion sont un groupe de boutons. Cliquez sur le bouton « Envoyer les modifications » pour envoyer les règles modifiées au pare-feu, puis cliquez sur le bouton « Activer ».

Avec l’activation de l’ensemble de règles de pare-feu, création de l’environnement cet exemple est terminée. Le cas échéant, les scripts de compilation post dans la section références peuvent être exécutés pour ajouter une application à cet environnement pour tester le dessous des scénarios de trafic.

>[AZURE.IMPORTANT] Il est essentiel de comprendre que vous ne sera pas atteint le serveur web directement. Lorsqu’un navigateur demande une page HTTP à partir de FrontEnd001.CloudApp.Net, le point de terminaison HTTP (port 80) transmet ce trafic vers le pare-feu pas le serveur web. Le pare-feu puis, conformément à la règle créée ci-dessus, les périphériques NAT qui demande au serveur Web.

## <a name="traffic-scenarios"></a>Scénarios de trafic

#### <a name="allowed-web-to-web-server-through-firewall"></a>(Autorisé) Serveur Web sur le Web par l’intermédiaire de pare-feu
1.  Page de Internet utilisateur demandes HTTP à partir de FrontEnd001.CloudApp.Net (face Cloud Service Internet)
2.  Cloud service passe le trafic via le point de terminaison ouvert sur le port 80 pour l’interface locale de pare-feu sur 10.0.1.4:80
3.  Site Web frontal sous-réseau commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (DNS) ne s’appliquent pas, atteindre la règle suivante
  2.    NSG règle 2 (RDP) ne s’appliquent pas, atteindre la règle suivante
  3.    NSG règle 3 (Internet au pare-feu) s’applique, le trafic est le traitement des règles autorisées, arrêter
4.  Le trafic atteint une adresse IP interne du pare-feu (10.0.1.4)
5.  Règle de transfert de pare-feu voir ceci est du trafic sur le port 80, il redirige vers le serveur web IIS01
6.  IIS01 est à l’écoute du trafic web, reçoit cette demande et commence le traitement de la demande
7.  IIS01 demande le SQL Server sur AppVM01 pour plus d’informations
8.  Aucune règle sortante sur le sous-réseau du site Web frontal, le trafic est autorisé.
9.  Le sous-réseau de back-end commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (DNS) ne s’appliquent pas, atteindre la règle suivante
  2.    NSG règle 2 (RDP) ne s’appliquent pas, atteindre la règle suivante
  3.    NSG règle 3 (Internet au pare-feu) ne s’appliquent, passer à la règle suivante
  4.    NSG règle 4 (IIS01 à AppVM01) s’applique, le trafic est autorisé, d’interrompre le traitement de la règle
10. AppVM01 reçoit la requête SQL et répond
11. Étant donné qu’aucune règle de trafic sortant sur le back-end sous-réseau la réponse est autorisée.
12. Site Web frontal sous-réseau commence le traitement de la règle de trafic entrant :
  1.    Il n’y a aucune règle NSG qui s’applique à l’entrée le trafic du sous-réseau du serveur principal pour le sous-réseau frontal, donc aucun de du NSG règles s’appliquent
  2.    La règle du système par défaut autorisant le trafic entre sous-réseaux permettrait de ce trafic afin que le trafic est autorisé.
13. Le serveur IIS reçoit la réponse SQL et la fin de la réponse HTTP et envoie ensuite au demandeur
14. Dans la mesure où il s’agit d’une session de NAT, du pare-feu, la destination de la réponse est (initialement) pour le pare-feu
15. Le pare-feu reçoit la réponse du serveur Web et le transfère à l’utilisateur d’Internet
16. Dans la mesure où il n’y a aucune règle sortante sur le sous-réseau frontal la réponse n’est autorisée, et l’utilisateur Internet reçoit la page web demandée.

#### <a name="allowed-rdp-to-backend"></a>(Autorisé) RDP pour le back-end
1.  Administrateur de serveur sur internet demande la session RDP à AppVM01 sur BackEnd001.CloudApp.Net:xxxxx, où xxxxx est le numéro de port attribué de façon aléatoire pour RDP pour AppVM01 (le port affecté se trouve sur le portail Azure ou via PowerShell)
2.  Étant donné que le pare-feu est uniquement à l’écoute sur l’adresse FrontEnd001.CloudApp.Net, il n’est pas impliqué avec ce flux de trafic
3.  Sous-réseau de back-end commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (DNS) ne s’appliquent pas, atteindre la règle suivante
  2.    NSG règle 2 (RDP) s’applique, le trafic est le traitement des règles autorisées, arrêter
4.  Qu’aucune règle de trafic sortant, les règles par défaut s’appliquent et le trafic de retour est autorisé
5.  Session RDP est activée.
6.  AppVM01 demande de mot de passe utilisateur

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Autorisé) Recherche de DNS du serveur Web sur le serveur DNS
1.  Web serveur, IIS01, besoins de flux de données à www.data.gov, mais doit résoudre l’adresse.
2.  La configuration du réseau pour les listes de VNet DNS01 (10.0.2.4 sur le sous-réseau du back-end) comme serveur DNS principal, IIS01 envoie la requête DNS pour DNS01
3.  Aucune règle sortante sur le sous-réseau du site Web frontal, le trafic est autorisé.
4.  Sous-réseau de back-end commence le traitement de la règle de trafic entrant :
  1.     NSG règle 1 (DNS) s’applique, le trafic est le traitement des règles autorisées, arrêter
5.  Serveur DNS reçoit la requête
6.  Serveur DNS ne possède pas l’adresse mise en cache et demande à un serveur DNS racine sur internet
7.  Aucune règle sortante sur le sous-réseau du back-end, le trafic est autorisé.
8.  Serveur DNS Internet répond, dans la mesure où cette session a été initialisée en interne, la réponse est autorisée.
9.  Serveur DNS met en cache la réponse et répond à la demande initiale à IIS01
10. Aucune règle sortante sur le sous-réseau du back-end, le trafic est autorisé.
11. Site Web frontal sous-réseau commence le traitement de la règle de trafic entrant :
  1.    Il n’y a aucune règle NSG qui s’applique à l’entrée le trafic du sous-réseau du serveur principal pour le sous-réseau frontal, donc aucun de du NSG règles s’appliquent
  2.    La règle du système par défaut autorisant le trafic entre sous-réseaux permettrait à ce trafic afin que le trafic est autorisé.
12. IIS01 reçoit la réponse DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Autorisé) Fichier d’accès de serveur Web sur AppVM01
1.  IIS01 demande un fichier sur AppVM01
2.  Aucune règle sortante sur le sous-réseau du site Web frontal, le trafic est autorisé.
3.  Le sous-réseau de back-end commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (DNS) ne s’appliquent pas, atteindre la règle suivante
  2.    NSG règle 2 (RDP) ne s’appliquent pas, atteindre la règle suivante
  3.    NSG règle 3 (Internet au pare-feu) ne s’appliquent, passer à la règle suivante
  4.    NSG règle 4 (IIS01 à AppVM01) s’applique, le trafic est autorisé, d’interrompre le traitement de la règle
4.  AppVM01 reçoit la demande et répond avec un fichier (en supposant que l’accès est autorisé)
5.  Étant donné qu’aucune règle de trafic sortant sur le back-end sous-réseau la réponse est autorisée.
6.  Site Web frontal sous-réseau commence le traitement de la règle de trafic entrant :
  1.    Il n’y a aucune règle NSG qui s’applique à l’entrée le trafic du sous-réseau du serveur principal pour le sous-réseau frontal, donc aucun de du NSG règles s’appliquent
  2.    La règle du système par défaut autorisant le trafic entre sous-réseaux permettrait de ce trafic afin que le trafic est autorisé.
7.  Le serveur IIS reçoit le fichier

#### <a name="denied-web-direct-to-web-server"></a>(Refusé) Web directement vers le serveur Web
Depuis le serveur Web, IIS01 et le pare-feu sont dans le même Service de Cloud ils partagent la même adresse IP en vis-à-vis publique. Ainsi le trafic HTTP est dirigé vers le pare-feu. Alors que la demande serait servie avec succès, il ne peut pas accéder directement au serveur Web, il passé comme prévu, à travers le pare-feu tout d’abord. Consultez le premier scénario de cette section pour le flux de trafic.

#### <a name="denied-web-to-backend-server"></a>(Refusé) Site Web sur le serveur principal
1.  Utilisateur Internet tente d’accéder à un fichier sur AppVM01 via le service de BackEnd001.CloudApp.Net
2.  Dans la mesure où il n’y a aucun point de terminaison ouvert pour le partage de fichiers, cela ne serait pas passer le Service en nuage et n’aurait pas d’atteindre le serveur
3.  Si les points de terminaison ont été ouverts pour une raison quelconque, les règles NSG 5 (Internet à VNet) serait bloquer ce trafic

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Refusé) Recherche DNS Web sur le serveur DNS
1.  Utilisateur Internet essaie de rechercher un enregistrement DNS interne sur DNS01 via le service de BackEnd001.CloudApp.Net
2.  Dans la mesure où il n’y a aucun point de terminaison ouvert pour DNS, cela ne serait pas passer le Service en nuage et n’aurait pas d’atteindre le serveur
3.  Si les points de terminaison ont été ouverts pour une raison quelconque, les règles NSG 5 (Internet à VNet) serait bloquer ce trafic (Remarque : cette règle 1 (DNS) ne s’appliquent pas pour deux raisons, tout d’abord l’adresse source est internet, cette règle ne s’applique qu’à la VNet local comme source, aussi il s’agit d’une règle d’autorisation, afin qu’il ne serait jamais refuser le trafic)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Refusé) Web Access SQL par l’intermédiaire de pare-feu
1.  Utilisateur Internet demande les données SQL à partir de FrontEnd001.CloudApp.Net (face Cloud Service Internet)
2.  Dans la mesure où il n’y a aucun point de terminaison ouvert pour SQL, cela ne serait pas passer le Service en nuage et n’aboutiraient le pare-feu
3.  Si les points de terminaison ont été ouverts pour une raison quelconque, le sous-réseau frontal commence le traitement de la règle de trafic entrant :
  1.    NSG règle 1 (DNS) ne s’appliquent pas, atteindre la règle suivante
  2.    NSG règle 2 (RDP) ne s’appliquent pas, atteindre la règle suivante
  3.    NSG règle 2 (pare-feu Internet) s’applique, le trafic est le traitement des règles autorisées, arrêter
4.  Le trafic atteint une adresse IP interne du pare-feu (10.0.1.4)
5.  Pare-feu n’a aucune règle de transfert pour SQL et supprime le trafic

## <a name="conclusion"></a>Conclusion
Il s’agit d’un moyen relativement simple de protéger votre application à l’aide d’un pare-feu et d’isoler le sous-réseau back-end du trafic entrant.

Plus d’exemples et une vue d’ensemble des limites de sécurité réseau, vous pouvez trouver [ici][HOME].

## <a name="references"></a>Références
### <a name="main-script-and-network-config"></a>Script principal et la configuration du réseau
Enregistrer le Script complet dans un fichier de script PowerShell. Enregistrer la configuration du réseau dans un fichier nommé « NetworkConf2.xml ».
Modifiez les variables définies par l’utilisateur si nécessaire. Exécutez le script, puis suivez les instructions d’installation pare-feu règle ci-dessus.

#### <a name="full-script"></a>Script complet
Ce script va, basé sur les variables définies par l’utilisateur :

1.  Se connecter à un abonnement Azure
2.  Créer un nouveau compte de stockage
3.  Créer un nouveau VNet et deux sous-réseaux tel que défini dans le fichier de configuration de réseau
4.  Créer des ordinateurs virtuels du serveur 4 windows
5.  Configurer NSG, y compris :
  - Création d’un NSG
  - Remplissage avec des règles
  - Liaison du NSG aux sous-réseaux appropriés

Ce script PowerShell doit être exécuté localement sur qu'un internet connecté PC ou serveur.

>[AZURE.IMPORTANT] Lorsque ce script est exécuté, il peut être avertissements et autres messages d’information qui apparaît dans PowerShell. Seuls les messages erreur en rouge sont un motif de préoccupation.


    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared
      
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.
    
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"
    
    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #
    
      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *
        
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *
    
        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Fichier de configuration de réseau
Enregistrer ce fichier xml avec l’emplacement de mise à jour et ajouter le lien vers ce fichier à la variable $NetworkConfigFile dans le script ci-dessus.
    
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Exemples de Scripts Application
Si vous souhaitez installer un exemple d’application pour ce, ainsi que d’autres exemples de DMZ, une a été fournie à l’adresse suivante : [Exemple de Script de l’Application][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "DMZ entrant avec NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Icône NAT de destination"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Règle de pare-feu"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Activation de règle de pare-feu"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
