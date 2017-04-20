<properties
    pageTitle="Déploiement de FS haute disponibilité AD cross-géographique dans Azure avec Azure Traffic Manager | Microsoft Azure"
    description="Dans ce document, vous apprendrez comment déployer ADFS dans Azure pour haute disponibilité."
    keywords="ADFS avec le Gestionnaire de trafic Azure, adfs avec Azure Traffic Manager, géographiques, les centres de données multiples, des centres de données géographiques, plusieurs centres de données géographiques, déployer ADFS dans azure, déployer adfs azure, azure adfs, Federation Services azure, déployer adfs, déployer ADFS, adfs dans azure, déployer adfs dans azure, les déployer ADFS dans azure, adfs azure, introduction à ADFS, Azure, AD FS dans Azure, iaas , ADFS, déplacer adfs vers azure"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="anandy;billmath"/>
    
#<a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Déploiement de FS haute disponibilité AD cross-géographique dans Azure avec Azure Traffic Manager

[Déploiement d’ADFS dans Azure](active-directory-aadconnect-azure-adfs.md) fournit des indications pas à pas sur comment déployer une infrastructure AD FS simple pour votre organisation dans Azure. Cet article fournit les étapes pour créer un déploiement inter-géographique d’ADFS dans Azure à l’aide du [Gestionnaire de trafic Azure](../traffic-manager/traffic-manager-overview.md). Azure Traffic Manager vous permet de créer un géographiquement double haute disponibilité et l’infrastructure AD FS de hautes performances pour votre organisation en faisant usage de plage des méthodes de routage disponibles pour répondre à différents besoins de l’infrastructure.

Permet à une infrastructure AD FS de cross-géographique hautement disponible :

* **Élimination de point de défaillance unique :** Avec fonctions de basculement de Azure Traffic Manager, vous pouvez obtenir une infrastructure AD FS hautement disponible même lorsqu’un des centres de données dans une partie de la planète tombe en panne
* **Des performances améliorées :** Vous pouvez utiliser le déploiement suggéré dans cet article pour fournir une infrastructure AD FS hautes performances qui permet aux utilisateurs de s’authentifier plus rapidement. 

##<a name="design-principles"></a>Principes de conception

![Conception générale](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Les principes de conception de base sera mêmes énumérés dans les principes de conception dans l’article déploiement AD FS dans Azure. Le schéma ci-dessus présente une extension simple de base du déploiement de la dans une autre région géographique. Voici quelques points à prendre en compte lorsque vous étendez votre déploiement vers la nouvelle zone géographique

* **Réseau virtuel :** Vous devez créer un nouveau réseau virtuel dans la zone géographique que vous souhaitez déployer une infrastructure AD FS supplémentaire. Dans le diagramme ci-dessus, vous voyez Geo1 VNET et VNET de Geo2 en tant que deux réseaux virtuels dans chaque région géographique.

* **Contrôleurs de domaine et les serveurs AD FS dans nouvelle VNET géographique :** Il est recommandé de déployer le domaine contrôleurs dans la nouvelle zone géographique afin que les serveurs ADFS dans la nouvelle zone n’aient pas à contacter un contrôleur de domaine dans un autre éloigné du réseau pour effectuer une authentification et ainsi améliorer les performances.

* **Des comptes de stockage :** Comptes de stockage sont associés à une région. Dans la mesure où vous déployez des ordinateurs dans une nouvelle région géographique, vous devrez créer de nouveaux comptes de stockage à utiliser dans la région.  

* **Réseau des groupes de sécurité :** Comme les comptes de stockage, les groupes de sécurité de réseau créés dans une région ne peut pas être utilisés dans une autre région géographique. Par conséquent, vous devrez créer nouveau réseau des groupes de sécurité similaires à celles de la première région géographique pour INT et DMZ de sous-réseau dans la nouvelle zone géographique.

* **Les étiquettes de DNS pour les adresses IP publiques :** Azure Traffic Manager peut faire référence aux points de terminaison uniquement via les étiquettes DNS. Par conséquent, vous devez créer des étiquettes DNS pour les adresses IP publiques des équilibreurs de charge externe.

* **Gestionnaire de trafic azure :** Microsoft Azure Traffic Manager vous permet de contrôler la distribution du trafic utilisateur à vos points de terminaison de service en cours d’exécution dans différents centres de données dans le monde entier. Azure Traffic Manager fonctionne au niveau du DNS. Il utilise les réponses DNS pour diriger le trafic des utilisateurs finaux aux points de terminaison dispersés géographiquement. Clients puis se connecter à ces points de terminaison directement. Avec des options de routage différentes de Weighted, de performances et de priorité, vous pouvez facilement sélectionner l’option de routage adaptée aux besoins de votre organisation. 

* **Connectivité net V V-réseau entre les deux régions :** Vous n’êtes pas obligé de disposer d’une connexion entre les réseaux virtuels lui-même. Étant donné que chaque réseau virtuel a accès aux contrôleurs de domaine et serveur ADFS et WAP en soi, ils peuvent travailler sans les connexions entre les réseaux virtuels dans différentes régions. 

##<a name="steps-to-integrate-azure-traffic-manager"></a>Étapes à suivre pour intégrer Azure Traffic Manager

###<a name="deploy-ad-fs-in-the-new-geographical-region"></a>Déployer AD FS dans la nouvelle zone géographique
Suivez les étapes et les instructions de [déploiement d’ADFS dans Azure](active-directory-aadconnect-azure-adfs.md) pour déployer la topologie même dans la nouvelle zone géographique.

###<a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Étiquettes DNS pour les adresses IP publiques des équilibreurs de charge Internet en vis-à-vis (public)
Comme mentionné ci-dessus, le Gestionnaire de trafic Azure ne peut faire référence aux étiquettes DNS en tant que points de terminaison et par conséquent, il est important de créer des étiquettes DNS pour les adresses IP publiques des équilibreurs de charge externe. Ci-dessous la capture d’écran montre comment vous pouvez configurer votre nom DNS pour l’adresse IP publique. 

![Nom DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

###<a name="deploying-azure-traffic-manager"></a>Déploiement d’Azure Traffic Manager

Suivez les étapes ci-dessous pour créer un profil de gestionnaire de trafic. Pour plus d’informations, vous pouvez également faire référence à [gérer un profil de gestionnaire de trafic Azure](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Création d’un profil de Traffic Manager :** Donnez à votre profil de gestionnaire de trafic un nom unique. Ce nom de profil fait partie du nom DNS et agit comme un préfixe pour l’étiquette de nom de domaine de Traffic Manager. Le nom / préfixe est ajouté à. trafficmanager.net pour créer un nom DNS pour le trafic de votre gestionnaire. La capture d’écran ci-dessous illustre le Gestionnaire de trafic préfixe DNS qui est défini comme mysts et l’étiquette DNS résultante sera mysts.trafficmanager.net. 

    ![Création de profil Traffic Manager](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
 
2. **Méthode de routage de trafic :** Il existe trois options de routage dans le Gestionnaire de trafic :

    * Priorité 
    * Performances
    * Pondérée
    
    **Performances** est l’option recommandée pour atteindre les infrastructure AD FS hautement réactive. Toutefois, vous pouvez choisir n’importe quelle méthode de routage plus adapté à vos besoins de déploiement. La fonctionnalité AD FS n’est pas concernée par l’option de routage sélectionnée. Pour plus d’informations, reportez-vous à la section [méthodes de routage de trafic Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md) . Dans l’exemple de capture d’écran ci-dessus, vous pouvez voir la méthode **performances** sélectionnée.
   
3.  **Configurer des points de terminaison :** Dans la page Gestionnaire de trafic, cliquez sur les points de terminaison et sélectionnez Ajouter. Cette action ouvre une page de point de terminaison ajouter semblable à la capture d’écran ci-dessous
 
    ![Configurer des points de terminaison](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
 
    Pour les entrées différentes, suivre les instructions ci-dessous :

    **Type :** Sélectionnez point de terminaison Azure comme nous seront pointe vers l’adresse IP publique Azure.

    **Nom :** Créer un nom que vous souhaitez associer au point final. Cela n’est pas le nom DNS et n’a aucune incidence sur les enregistrements DNS.

    **Type de ressource cible :** Sélectionnez l’adresse IP publique comme valeur à cette propriété. 

    **Cible de ressources :** Cela vous donnera une option à sélectionner les différentes étiquettes DNS dont vous disposez dans votre abonnement. Choisir l’étiquette DNS pour.

    Ajouter un point de terminaison pour chaque région géographique que vous voulez que le Gestionnaire de trafic de Azure pour acheminer le trafic vers.
    Pour plus d’informations et pour obtenir la procédure détaillée sur la façon d’ajouter et de configurer des points de terminaison dans le Gestionnaire de trafic, reportez-vous à [Ajouter, désactiver, activer ou supprimer des points de terminaison](../traffic-manager/traffic-manager-endpoints.md)
    
4. **Sonde de configuration :** Dans la page Gestionnaire de trafic, cliquez sur Configuration. Dans la page de configuration, vous devez modifier les paramètres du moniteur pour détecter le port HTTP 80 et /adfs/probe de chemin d’accès relatif

    ![Configurez la sonde](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 

    >[AZURE.NOTE] **Vérifiez que le statut des points de terminaison est en ligne, une fois la configuration terminée**. Si tous les points de terminaison sont dans un état « dégradé », Azure Traffic Manager fera une meilleure tentative pour acheminer le trafic en supposant que les tests de diagnostic est incorrecte et que tous les points de terminaison sont accessibles.

5. **Les enregistrements DNS de modification pour Azure Traffic Manager :** Votre service de fédération doit être un enregistrement CNAME pour le nom DNS de Traffic Manager Azure. Créer un enregistrement CNAME dans les enregistrements DNS publics, afin que toute personne essaie de contacter le service de fédération atteint réellement le Gestionnaire de trafic Azure.

    Par exemple, pour pointer le fs.fabidentity.com service de fédération dans le Gestionnaire de trafic, vous devez mettre à jour votre enregistrement de ressource DNS pour les suivants :

    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

##<a name="test-the-routing-and-ad-fs-sign-in"></a>Tester le routage et l’authentification dans AD FS   

###<a name="routing-test"></a>Test de routage

Un test de base pour le routage serait pour essayer de ping sur le nom DNS de service de fédération à partir d’un ordinateur dans chaque région géographique. Selon la méthode de routage choisie, il se connecte en fait le point de terminaison apparaîtront dans l’affichage de la commande ping. Par exemple, si vous avez sélectionné la gamme de performances, puis le point de terminaison plus proche de la région du client sera atteinte. Voici la capture instantanée de deux commandes ping à partir de deux ordinateurs de client d’autre région, dans la région EastAsia et ouest des États-Unis. 

![Test de routage](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

###<a name="ad-fs-sign-in-test"></a>AD FS connexion test

Le moyen le plus simple pour tester AD FS est à l’aide de la page IdpInitiatedSignon.aspx. Pour être en mesure de faire qu’il est nécessaire pour activer la IdpInitiatedSignOn sur les propriétés de AD FS. Suivez les étapes ci-dessous pour vérifier votre configuration AD FS
 
1. Exécuter le sous l’applet de commande sur le serveur AD FS, à l’aide de PowerShell, à défini sur activé. Set-AdfsProperties - EnableIdPInitiatedSignonPage $true
2. À partir de n’importe quel ordinateur externe accès https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Vous devez voir la page AD FS comme ci-dessous :

    ![ADFS test - demande d’authentification](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)

    et lors de connexion réussie, il vous fournira un message de réussite comme indiqué ci-dessous :

    ![Test d’ADFS - succès de l’authentification](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)
 
##<a name="related-links"></a>Liens connexes
* [Déploiement de base AD FS dans Azure](active-directory-aadconnect-azure-adfs.md)
* [Gestionnaire de trafic Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
* [Méthodes de routage de trafic Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md)

##<a name="next-steps"></a>Étapes suivantes
* [Gérer un profil Azure Traffic Manager](../traffic-manager/traffic-manager-manage-profiles.md)
* [Ajouter, désactiver, activer ou supprimer des points de terminaison](../traffic-manager/traffic-manager-endpoints.md) 

