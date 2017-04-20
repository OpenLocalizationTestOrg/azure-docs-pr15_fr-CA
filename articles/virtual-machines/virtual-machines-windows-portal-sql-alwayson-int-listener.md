<properties
   pageTitle="Créer le port d’écoute pour le groupe availabilty AlwaysOn pour SQL Server dans Azure Virtual Machines"
   description="Instructions pas à pas pour la création d’un port d’écoute pour un groupe d’availabilty AlwaysOn pour SQL Server dans Azure Virtual Machines"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-an-internal-load-balancer-for-an-alwayson-availability-group-in-azure"></a>Configurer un équilibreur de charge interne pour un groupe de disponibilité AlwaysOn dans Azure

Cette rubrique explique comment créer un équilibreur de charge interne pour un groupe de disponibilité AlwaysOn de SQL Server sur des machines virtuelles Azure en cours d’exécution dans le modèle de gestionnaire de ressources. Un groupe de disponibilité AlwaysOn nécessite un équilibreur de charge lorsque les instances de SQL Server sont sur des machines virtuelles Azure. L’équilibreur de charge enregistre l’adresse IP pour l’écouteur de groupe de disponibilité. Si un groupe de disponibilité s’étend sur plusieurs zones, chaque zone doit être un équilibreur de charge.

Pour effectuer cette tâche, vous devez disposer d’un groupe de disponibilité AlwaysOn de SQL Server déployé sur Azure machines virtuelles dans le modèle de gestionnaire de ressources. Les deux machines virtuelles de SQL Server doivent appartenir au même jeu de disponibilité. Vous pouvez utiliser le [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) pour créer automatiquement le groupe de disponibilité AlwaysOn dans le Gestionnaire de ressources Azure. Ce modèle crée automatiquement l’équilibreur de charge interne. 

Si vous préférez, vous pouvez [configurer manuellement un groupe de disponibilité AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Cette rubrique nécessite que vos groupes de disponibilité sont déjà configurés.  

Rubriques connexes :

 - [Configurer des groupes de disponibilité AlwaysOn dans Azure VM (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [Configurer une connexion VNet à VNet en utilisant le Gestionnaire de ressources Azure et PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="steps"></a>Étapes

En parcourant ce document vous créer et configurer un équilibreur de charge dans le portail Azure. Une fois cette opération terminée, vous allez configurer le cluster pour utiliser l’adresse IP de l’équilibreur de charge pour le récepteur de groupe de disponibilité AlwaysOn.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Créer et configurer l’équilibrage de charge dans le portail Azure

Dans cette partie de la tâche, vous allez effectuer les opérations suivantes dans le portail Azure :

1. Créer l’équilibreur de charge et configurer l’adresse IP

1. Configurer le pool back-end

1. Créer la sonde 

1. Définir la règles d’équilibrage de la charge

>[AZURE.NOTE] Si les serveurs SQL sont dans les régions et les différents groupes de ressources, vous effectuez toutes ces étapes deux fois, une fois dans chaque groupe de ressources.

## <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. créer l’équilibreur de charge et configurer l’adresse IP

La première étape consiste à créer l’équilibreur de charge. Dans le portail Azure, ouvrez le groupe de ressources contenant les machines virtuelles de SQL Server. Dans le groupe de ressources, cliquez sur **Ajouter**.

- Rechercher **l’équilibreur de charge**. Résultats de la recherche, sélectionnez **Équilibreur de charge**, qui est publié par **Microsoft**.

- Sur la lame de **l’Équilibreur de charge** , cliquez sur **créer**.

- **L’équilibrage de charge de créer**, de configurer le l’équilibreur de charge comme suit :

| Paramètre | Valeur |
| ----- | ----- |
| **Nom** | Un nom représentant l’équilibreur de charge. Par exemple, **sqlLB**. |
| **Schéma** | **Interne** |
| **Réseau virtuel** | Sélectionnez le réseau virtuel qui se trouvent dans les serveurs SQL.   |
| **Sous-réseau**  | Cliquez sur le sous-réseau sont les serveurs SQL. |
| **Abonnement** | Si vous disposez de plusieurs abonnements, ce champ peut apparaître. Sélectionnez l’abonnement que vous souhaitez associer à cette ressource. Il est normalement le même abonnement comme toutes les ressources pour le groupe de disponibilité.  |
| **Groupe de ressources** | Choisissez le groupe de ressources figurant dans les serveurs SQL. | 
| **Emplacement** | Choisissez l’emplacement Azure figurant dans les serveurs SQL. |

- Cliquez sur **créer**. 

Azure crée l’équilibreur de charge que vous avez configurée précédemment. L’équilibreur de charge appartient à un réseau spécifique, un sous-réseau, un groupe de ressources et un emplacement. Une fois terminée d’Azure, vérifiez les paramètres d’équilibrage de la charge dans Azure. 

Maintenant, configurez l’adresse IP d’équilibrage de la charge.  

- Sur la lame de **paramètres** équilibreur de charge, cliquez sur **adresse IP**. La lame de **l’adresse IP** indique qu’il s’agit d’un équilibreur de charge privé sur le même réseau virtuel que vos serveurs SQL. 

- Définir les paramètres suivants : 

| Paramètre | Valeur |
| ----- | ----- |
| **Sous-réseau** | Cliquez sur le sous-réseau sont les serveurs SQL. |
| **Affectation** | **Statique** |
| **Adresse IP** | Tapez une adresse IP virtuelle inutilisée à partir du sous-réseau.  |

- Enregistrer les paramètres.

Désormais, l’équilibreur de charge a une adresse IP. Enregistrer cette adresse IP. Vous allez utiliser cette adresse IP lorsque vous créez un port d’écoute sur le cluster. Dans un script PowerShell plus loin dans cet article, utilisez cette adresse pour le `$ILBIP` variable.



## <a name="2-configure-the-backend-pool"></a>2. configurer le pool back-end

L’étape suivante consiste à créer un pool d’adresses back-end. Azure appelle le pool d’adresse back-end *back-end pool*. Dans ce cas, le pool back-end est les adresses des deux serveurs SQL dans votre groupe de disponibilité. 

- Dans votre groupe de ressources, cliquez sur l’équilibrage de charge que vous avez créé. 

- Dans **paramètres**, cliquez sur **pools de back-end**.

- Sur le **back-end les pools d’adresses**, cliquez sur **Ajouter** pour créer un pool d’adresses back-end. 

- Sur **Ajouter back-end pool** sous **nom**, tapez un nom pour le pool de back-end.

- **Machines virtuelles** cliquez sur **+ Ajouter un ordinateur virtuel**. 

- Sous **Choisir virtual machines** cliquez sur **Choisir un jeu de disponibilité** et spécifier que la disponibilité que les machines virtuelles de SQL Server appartiennent à la valeur.

- Une fois que vous avez choisi l’ensemble de la disponibilité, cliquez sur **Choisir les ordinateurs virtuels**. Cliquez sur les deux ordinateurs virtuels qui hébergent des instances de SQL Server dans le groupe de disponibilité. Cliquez sur **Sélectionner**. 

- Cliquez sur **OK** pour fermer les lames des **machines virtuelles de choisir** **Ajouter back-end pool**. 

Azure met à jour les paramètres pour le pool d’adresses back-end. Maintenant votre disponibilité de jeu dispose d’un pool de deux serveurs SQL.

## <a name="3-create-a-probe"></a>3. création d’une sonde

L’étape suivante consiste à créer une sonde. La sonde définit comment Azure va vérifier que les serveurs SQL possède actuellement l’écouteur de groupe de disponibilité. Azure chercheront le service basé sur l’adresse IP sur un port que vous définissez lorsque vous créez la sonde.

- Sur la lame de **paramètres** équilibreur de charge, cliquez sur **des sondes**. 

- Sur la lame de **sondes** , cliquez sur **Ajouter**.

- Configurez la sonde sur la lame de **sonde d’ajouter** . Configuration de la sonde, utilisez les valeurs suivantes :

| Paramètre | Valeur |
| ----- | ----- |
| **Nom** | Un nom représentant la sonde. Par exemple, **SQLAlwaysOnEndPointProbe**. |
| **Protocole** | **TCP** |
| **Port** | Vous pouvez utiliser n’importe quel port disponible. Par exemple, *59999*.    |
| **Intervalle**  | *5* | 
| **Seuil de mauvais fonctionnement**  | *2* | 

- Cliquez sur **OK**. 

>[AZURE.NOTE] Assurez-vous que le port spécifié est ouvert sur le pare-feu des deux serveurs SQL. Les deux serveurs requièrent une règle de trafic entrant pour le port TCP que vous utilisez. Pour plus d’informations, voir [Ajouter ou modifier une règle de pare-feu](http://technet.microsoft.com/library/cc753558.aspx) . 

Azure crée la sonde. Azure utilise la sonde pour tester les SQL Server a l’écouteur pour le groupe de disponibilité.

## <a name="4-set-the-load-balancing-rules"></a>4. définir la règles d’équilibrage de la charge

Définir la règles d’équilibrage de la charge. Les règles d’équilibrage de la charge configurer la façon dont l’équilibrage de charge achemine le trafic vers les serveurs SQL. Pour l’équilibrage de la charge, vous allez activer serveur direct retour car seul l’un des deux serveurs SQL possédera jamais la ressource de port d’écoute de groupe de disponibilité à la fois.

- Sur la lame de **paramètres** équilibreur de charge, cliquez sur **règles d’équilibrage de charge**. 

- Sur la lame de **règles d’équilibrage de charge** , cliquez sur **Ajouter**.

- La lame **Ajouter charger les règles de contrepartie** permet de configurer la règle d’équilibrage de la charge. Utilisez les paramètres suivants : 

| Paramètre | Valeur |
| ----- | ----- |
| **Nom** | Un nom qui représente la règles d’équilibrage de la charge. Par exemple, **SQLAlwaysOnEndPointListener**. |
| **Protocole** | **TCP** |
| **Port** | *1433*   |
| **Port du serveur principal** | *1433*. Notez que cela va être désactivée, car cette règle utilise **Flottante IP (retour de serveur direct)**.   |
| **Sonde** | Utilisez le nom de la sonde que vous avez créé pour ce équilibreur de charge. |
| **Persistance de session**  | **Aucun** | 
| **Délai d’inactivité (minutes)**  | *4* | 
| **Flottante IP (serveur direct retour)**  | **Activé** | 

 >[AZURE.NOTE] Vous devrez peut-être faire défiler vers le bas sur la lame pour voir tous les paramètres.

- Cliquez sur **OK**. 

- Azure configure la règle d’équilibrage de la charge. Désormais, l’équilibreur de charge est configuré pour acheminer le trafic vers le SQL Server qui héberge le port d’écoute pour le groupe de disponibilité. 

À ce stade le groupe de ressources a un équilibreur de charge, connectez les deux ordinateurs SQL Server. L’équilibreur de charge contient également une adresse IP pour l’écouteur de groupe de disponibilité AlwaysOn de SQL Server afin que l’ordinateur peut répondre aux demandes pour les groupes de disponibilité.

>[AZURE.NOTE] Si vos serveurs SQL en deux régions distinctes, répétez les étapes décrites dans l’autre région. Chaque région nécessite un équilibreur de charge. 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurer le cluster pour utiliser l’adresse IP d’équilibrage de la charge 

L’étape suivante consiste à configurer le port d’écoute sur le cluster et l’écouteur en ligne. Pour ce faire, effectuez les opérations suivantes : 

1. Créer l’écouteur de groupe de disponibilité sur le cluster de basculement 

1. Mettre le récepteur en ligne

## <a name="1-create-the-availablity-group-listener-on-the-failover-cluster"></a>1. création de l’écouteur de groupe de disponibilité sur le cluster de basculement

Dans cette étape, vous créez manuellement l’écouteur du groupe de disponibilité dans le Gestionnaire du Cluster de basculement et de SQL Server Management Studio (SSMS).

- Utiliser le protocole RDP pour se connecter à l’ordinateur virtuel Azure qui héberge le réplica principal. 

- Ouvrez le Gestionnaire du Cluster de basculement.

- Sélectionnez le nœud **réseaux** et notez le nom de réseau du cluster. Ce nom sera utilisé dans le `$ClusterNetworkName` variable dans le script PowerShell.

- Développez le nom du cluster, puis cliquez sur **rôles**.

- Dans le volet de **rôles** , cliquez sur le nom de groupe de disponibilité, puis sélectionnez **Ajouter une ressource** > **Point d’accès Client**.

- Dans la zone **nom** , attribuez un nom à ce nouveau port d’écoute, puis cliquez deux fois sur **suivant** puis cliquez sur **Terminer**. Ne mettez pas le port d’écoute ou de la ressource en ligne à ce stade.

 >[AZURE.NOTE] Le nom pour le nouveau port d’écoute est le nom de réseau que les applications utiliseront pour se connecter aux bases de données dans le groupe de disponibilité de SQL Server.

- Cliquez sur l’onglet **ressources** , puis développez le Point d’accès Client vous venez de créer. Avec le bouton droit de la ressource IP et cliquez sur Propriétés. Notez le nom de l’adresse IP. Vous utiliserez ce nom dans la `$IPResourceName` variable dans le script PowerShell.

- Sous **Adresse IP** , cliquez sur **Adresse IP statique** et définir l’adresse IP statique à la même adresse que vous avez utilisé lorsque vous définissez l’adresse IP d’équilibrage de la charge sur le portail Azure. Activer NetBIOS pour cette adresse et cliquez sur **OK**. Répétez cette étape pour chaque ressource IP si votre solution couvre plusieurs VNets d’Azure. 

- Sur le nœud de cluster qui héberge actuellement le réplica principal, ouvrez une PowerShell ISE élevés et collez les commandes suivantes dans un nouveau script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- Mettre à jour les variables et exécuter le script PowerShell pour configurer l’adresse IP et le port pour le nouveau port d’écoute.

 >[AZURE.NOTE] Si vos serveurs SQL se trouvent dans des zones distinctes, vous devez exécuter le script PowerShell deux fois. La première fois utiliser le nom réseau du cluster, nom de la ressource cluster IP et charger l’adresseIP d’équilibrage du premier groupe de ressources. La deuxième fois utiliser le nom réseau du cluster, nom de la ressource cluster IP et charger l’adresseIP d’équilibrage du deuxième groupe de ressources.

Désormais, le cluster a une ressource de port d’écoute de groupe de disponibilité.

## <a name="2-bring-the-listener-online"></a>2. Mettez l’écouteur en ligne

Avec la ressource de port d’écoute disponibilité groupe configurée, vous pouvez mettre l’écouteur en ligne afin que les applications peuvent se connecter aux bases de données dans le groupe de disponibilité avec le port d’écoute.

- Naviguer vers le Gestionnaire des clusters de basculement. Développez **rôles** et mettez en surbrillance le groupe de votre disponibilité. Sous l’onglet **ressources** , cliquez sur le nom de l’écouteur et cliquez sur **Propriétés**.

- Cliquez sur l’onglet **dépendances** . S’il existe plusieurs ressources répertoriées, vérifiez que les adresses IP ou non et dépendances. Cliquez sur **OK**.

- Double-cliquez sur le nom du port d’écoute, puis cliquez sur **Mettre en ligne**.


- Une fois que l’écouteur est en ligne, à partir de l’onglet **ressources** , cliquez sur le groupe de disponibilité, puis cliquez sur **Propriétés**.

- Créer une dépendance sur la ressource de nom de port d’écoute (pas le IP adresse nom de ressources). Cliquez sur **OK**.


- Lancement de SQL Server Management Studio et connectez-vous au réplica principal.


- Atteindre **AlwaysOn haute disponibilité** | **groupes de disponibilité** | **groupe de disponibilité écouteurs**. 


- Vous devez maintenant voir le nom de l’écouteur que vous avez créé dans le Gestionnaire de Cluster de basculement. Double-cliquez sur le nom du port d’écoute, puis cliquez sur **Propriétés**.


- Dans la zone **Port** , spécifiez le numéro de port pour l’écouteur de groupe de disponibilité à l’aide de la $EndpointPort utilisée précédemment (1433 était la valeur par défaut), puis cliquez sur **OK**.

Vous disposez maintenant d’un groupe de disponibilité AlwaysOn de SQL Server dans Azure des machines virtuelles en cours d’exécution en mode Gestionnaire de ressources. 

## <a name="test-the-connection-to-the-listener"></a>Tester la connexion pour le port d’écoute

Pour tester la connexion :

1. RDP à un SQL Server qui se trouve dans le même réseau virtuel, mais qui ne possède pas de réplica. Cela peut être l’autre de SQL Server dans le cluster.

1. Utilisez l’utilitaire **sqlcmd** pour tester la connexion. Par exemple, le script suivant établit une connexion **sqlcmd** pour le réplica principal par l’intermédiaire de l’écouteur avec l’authentification Windows :

        sqlcmd -S <listenerName> -E

La connexion SQLCMD se connecter automatiquement à quelle instance de SQL Server héberge le réplica principal. 

## <a name="guidelines-and-limitations"></a>Instructions et limitations

Notez que les instructions suivantes sur port d’écoute disponibilité groupe dans Azure à l’aide interne l’équilibreur de charge :

- Écouteur de groupe interne de disponibilité qu’un seul est pris en charge par le service en nuage, car le port d’écoute est configuré pour l’équilibrage de charge, et seul un équilibreur de charge interne. Toutefois, il est possible de créer plusieurs adresses les écouteurs externes. 

- Avec un équilibreur de charge interne vous permet uniquement d’accéder l’écouteur au sein du même réseau virtuel.
 
