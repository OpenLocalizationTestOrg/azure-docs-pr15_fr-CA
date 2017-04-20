<properties 
   pageTitle="Configurer le service DNS entre deux réseaux virtuels Azure | Microsoft Azure" 
   description="Apprenez à configurer des connexions VPN et résolution de noms de domaine entre deux réseaux virtuels et comment configurer la réplication geo HBase." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-dns-between-two-azure-virtual-networks"></a>Configurer le service DNS entre deux réseaux virtuels Azure

> [AZURE.SELECTOR]
- [Configurez la connectivité VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurer le service DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurer la réplication de HBase](hdinsight-hbase-geo-replication.md) 


Découvrez comment ajouter et configurer des serveurs DNS pour les réseaux virtuels Azure pour gérer la résolution de nom au sein et entre les réseaux virtuels.

Ce didacticiel est la deuxième partie de la [série] [ hdinsight-hbase-geo-replication] sur la création de HBase géo-réplication :

- [Configurer une connexion VPN entre deux réseaux virtuels][hdinsight-hbase-geo-replication-vnet]
- Configurer le service DNS pour les réseaux virtuels (ce didacticiel)
- [Configurer la réplication de zone géographique HBase][hdinsight-hbase-geo-replication]


Le diagramme suivant illustre les deux réseaux virtuels créé à [configurer une connexion VPN entre deux réseaux virtuels][hdinsight-hbase-geo-replication-vnet]:

![Diagramme de réseau virtuel de réplication HDInsight HBase][img-vnet-diagram]

##<a name="prerequisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Une station de travail avec PowerShell d’Azure**.

    Avant d’exécuter des scripts PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l’aide de l’applet de commande suivante :

        Add-AzureAccount

    Si vous avez plusieurs abonnements d’Azure, utilisez l’applet de commande suivante pour définir l’abonnement actuel :

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Réseau virtuel de deux Azure avec connectivité VPN**.  Pour plus d’informations, voir [configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE] Les noms de service Azure et machine virtuelle doit être unique. Le nom utilisé dans ce didacticiel est Contoso-[nom du Service/VM Azure]-[UE / États-Unis]. Par exemple, Contoso-VNet-UE est le réseau virtuel Azure dans le centre de données en Europe du Nord ; Contoso-DNS-US est la machine virtuelle dans le centre de données États-Unis orientale du serveur DNS. Vous devez trouver vos propres noms.
 
 
##<a name="create-azure-virtual-machines-to-be-used-as-dns-servers"></a>Créer des ordinateurs virtuels Azure pour être utilisés comme serveurs DNS

**Pour créer un ordinateur virtuel au sein de Contoso-VNet-UE, appelée Contoso-DNS-UE**

1.  Cliquez sur **Nouveau** **calcul**, **MACHINE virtuelle**, **De la galerie**.
2.  Choisissez **Windows Server 2012 R2 Datacenter**.
3.  Entrez :
    - **Nom de la MACHINE virtuelle**: Contoso-DNS-UE
    - **Nouveau nom d’utilisateur**: 
    - **Nouveau mot de passe**: 
4.  Entrez :
    - **SERVICE CLOUD**: créer un nouveau service de cloud
    - **Région/groupe d’affinité/virtuel réseau**: (Sélectionner Contoso-VNet-UE)
    - **Sous-réseaux du réseau virtuel**: sous-réseau-1
    - **Compte de stockage**: utilisez un compte de stockage généré automatiquement
    
        Le nom de service nuage sera le même que le nom de l’ordinateur virtuel. Dans ce cas, c’est de Contoso-DNS-UE. Pour les ordinateurs virtuels suivants, je peux choisir d’utiliser le même service de cloud.  Tous les ordinateurs virtuels sous le service cloud même partager le même réseau virtuel et le suffixe de domaine.

        Le compte de stockage est utilisé pour stocker le fichier image de machine virtuelle. 
    - **Points de terminaison**: (faites défiler et sélectionnez **DNS**) 

Après avoir créé l’ordinateur virtuel, découvrez interne IP et l’adresse IP externe.

1.  Cliquez sur le nom de la machine virtuelle, **Contoso-DNS-UE**.
2.  Cliquez sur le **tableau de bord**.
3.  Écrivez à :
    - ADRESSE IP VIRTUELLE PUBLIQUE
    - ADRESSE IP INTERNE


**Pour créer un ordinateur virtuel de Contoso-VNet-US, appelée Contoso-DNS-US** 

- Répétez la même procédure pour créer un ordinateur virtuel avec les valeurs suivantes :
    - NOM de la MACHINE virtuelle : Contoso-DNS-US
    - RÉGION/affinité du groupe réseau virtuel : sélectionnez Contoso-VNET-US
    - SOUS-réseaux du réseau virtuel : Sous-réseau-1
    - COMPTE de stockage : Utilisez un compte de stockage généré automatiquement
    - Points de terminaison : (Sélectionner DNS)

##<a name="set-static-ip-addresses-for-the-two-virtual-machines"></a>Définir des adresses IP statiques pour les deux machines virtuelles

Serveurs DNS requiert des adresses IP statiques.  Cette étape ne peut être effectuée à partir du portail classique d’Azure. Vous allez utiliser PowerShell d’Azure.

**Pour configurer une adresse IP statique pour les deux machines virtuelles**

1. Ouvrez Windows PowerShell ISE.
2. Exécuter les applets de commande suivantes.  

        Add-AzureAccount
        Select-AzureSubscription [YourAzureSubscriptionName]
        
        Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
        Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

    Nom_service est le nom de service de cloud. Étant donné que le serveur DNS est la première machine virtuelle du service cloud, le nom de service de cloud est le même que le nom de l’ordinateur virtuel.

    Vous devrez peut-être mettre à jour ServiceName et nom pour faire correspondre les noms que vous avez.


##<a name="add-the-dns-server-role-the-two-virtual-machines"></a>Ajoutez les ordinateurs virtuels de rôle les deux serveur DNS

**Pour ajouter le rôle de serveur DNS pour Contoso-DNS-UE**

1.  À partir du portail classique d’Azure, sur des **Machines virtuelles** à gauche. 
2.  Cliquez sur **Contoso-DNS-UE**.
3.  À partir du haut, cliquez sur **tableau de bord** .
4.  Cliquez sur **se connecter** à partir du bas et suivez les instructions pour vous connecter à l’ordinateur virtuel via le protocole RDP.
2.  Dans la session RDP, cliquez sur le bouton dans le coin inférieur gauche pour ouvrir l’écran de démarrage Windows.
3.  Cliquez sur la mosaïque du **Gestionnaire de serveur** .
4.  Cliquez sur **Ajouter des rôles et fonctionnalités**.
5.  Cliquez sur **suivant**
6.  Sélectionnez **installation basée sur les rôles ou basé sur la fonctionnalité**, puis cliquez sur **suivant**.
7.  Sélectionnez votre machine DNS (il doit être mis en surbrillance déjà), puis cliquez sur **suivant**.
8.  Vérifiez le **serveur DNS**.
9.  Cliquez sur **Ajouter des fonctionnalités**, puis cliquez sur **Continuer**.
10. Cliquez trois fois sur **suivant** , puis cliquez sur **installer**. 

**Pour ajouter le rôle de serveur DNS pour DNS-Contoso-États-Unis**

- Répétez les étapes pour ajouter le rôle de DNS à **Contoso-DNS-US**.

##<a name="assign-dns-servers-to-the-virtual-networks"></a>Affecter des serveurs DNS pour les réseaux virtuels

**Pour enregistrer les deux serveurs DNS**

1.  À partir du portail classique d’Azure, cliquez sur **Nouveau**, **SERVICES réseau**, **Réseau virtuel**, **Inscrire un serveur DNS**.
2.  Entrez :
    - **Nom**: Contoso-DNS-UE
    - **Adresse IP du serveur DNS**: 10.1.0.4 – l’adresse IP doit correspondant à l’adresse IP de machine virtuelle du serveur DNS.
     
3.  Répétez le processus pour inscrire les DNS-Contoso-États-Unis avec les paramètres suivants :
    - **Nom**: Contoso-DNS-US
    - **Adresse IP du serveur DNS**: 10.2.0.4

**Pour affecter les deux serveurs DNS pour les deux réseaux virtuels**

1.  Dans le volet de gauche dans le portail classique, cliquez sur **réseaux** .
2.  Cliquez sur **Contoso-VNet-UE**.
3.  Cliquez sur **configurer**.
4.  Dans la section **serveurs dns** , sélectionnez **Contoso-DNS-UE** .
5.  Cliquez sur **Enregistrer** dans la partie inférieure de la page, cliquez sur **Oui** pour confirmer.
6.  Répétez le processus pour affecter le serveur DNS de **Contoso-États-Unis-DNS** au réseau virtuel **Contoso-VNet-US** .

Tous les ordinateurs virtuels qui ont été déployés sur les réseaux virtuels doit être redémarrés pour mettre à jour de la configuration du serveur DNS.

**Pour redémarrer les machines virtuelles**

1. À partir du portail classique d’Azure, sur des **Machines virtuelles** à gauche.
2. Cliquez sur **Contoso-DNS-UE**.
3. À partir du haut, cliquez sur **tableau de bord** .
4. Dans la partie inférieure, cliquez sur **redémarrer** .
5. Répétez la même procédure pour redémarrer **Contoso-DNS-US**.


##<a name="configure-dns-conditional-forwarders"></a>Configurer les redirecteurs DNS conditionnels

Le serveur DNS sur chaque réseau virtuel peut uniquement résoudre les noms DNS au sein de ce réseau virtuel. Vous devez configurer un redirecteur conditionnel pour pointer vers le serveur DNS de pair pour la résolution de nom du réseau virtuel homologue.

Pour configurer un redirecteur conditionnel, vous devez connaître les suffixes de domaine des deux serveurs DNS. Les suffixes DNS peuvent être différents selon la configuration de Services en nuage que vous avez utilisé lors de la création d’ordinateurs virtuels. Pour chaque suffixe DNS utilisé dans le réseau virtuel, vous devez ajouter un redirecteur conditionnel. 

**Pour rechercher les suffixes de domaine des deux serveurs DNS**

1. RDP dans **Contoso-DNS-Union européenne**.
2. Ouvrez la console Windows PowerShell ou invite de commande.
3. Exécutez **ipconfig**et notez le **suffixe DNS spécifique à la connexion**.
4. Ne fermez pas la session RDP, vous en aurez toujours besoin. 
5. Répétez les mêmes étapes, à savoir le **suffixe DNS spécifique à la connexion** de **Contoso-DNS-US**.


**Pour configurer des redirecteurs DNS**
 
1.  À partir de la session RDP à **Contoso-DNS-UE**, cliquez sur la touche Windows sur le coin inférieur gauche.
2.  Cliquez sur **Outils d’administration**.
3.  Cliquez sur **DNS**.
4.  Dans le volet gauche, développez **DSN**, **Contoso-DNS-UE**.
5.  Entrez les informations suivantes :
    - **Domaine DNS**: entrez le suffixe DNS de Contoso-DNS-US. Par exemple : Contoso-DNS-US.b5.internal.cloudapp.net.
    - **Les adresses IP des serveurs maîtres**: entrez 10.2.0.4, qui est l’adresse IP du Contoso-DNS-États-Unis.
6.  Appuyez sur **entrée**, puis cliquez sur **OK**.  Maintenant, vous serez en mesure de résoudre l’adresse IP du Contoso-DNS-US Contoso-DNS-UE.
7.  Répétez les étapes pour ajouter un redirecteur DNS pour le service DNS sur l’ordinateur virtuel de Contoso-États-Unis-DNS avec les valeurs suivantes :
    - **Domaine DNS**: entrez le suffixe DNS de l’UE de DNS de Contoso. 
    - **Les adresses IP des serveurs maîtres**: entrez 10.2.0.4, qui est l’adresse IP de Contoso-DNS-l’Union européenne.

##<a name="test-the-name-resolution-across-the-virtual-networks"></a>Tester la résolution de noms sur les réseaux virtuels

Vous pouvez maintenant tester la résolution de noms sur les réseaux virtuels. Ping est bloquée par le pare-feu par défaut.  Vous pouvez utiliser nslookup pour résoudre les ordinateurs virtuels de serveur DNS (vous devez utiliser de nom de domaine complet) dans les réseaux homologues.  


##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment configurer la résolution de noms sur les réseaux virtuels avec les connexions VPN. Les autres deux articles dans la couverture de série :

- [Configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-geo-replication-vnet]
- [Configurer la réplication de zone géographique HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png 
