<properties
    pageTitle="Créer un ordinateur virtuel en cours d’exécution MySQL | Microsoft Azure"
    description="Créer un ordinateur virtuel Azure exécutant Windows Server 2012 R2 et la base de données de MySQL en utilisant le modèle de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="cynthn"/>


# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2012-r2"></a>Installation de MySQL sur une machine virtuelle créée avec le modèle classique de déploiement de Windows Server 2012 R2 en cours d’exécution

[MySQL](http://www.mysql.com) est une base de données SQL courante Ouvrir la source. Ce didacticiel vous montre comment installer et exécuter la version communautaire de MySQL 5.6.23 comme un serveur MySQL sur une machine virtuelle exécutant Windows Server 2012 R2. Pour obtenir des instructions sur l’installation de MySQL sur Linux, reportez-vous à : [comment installer MySQL sur Azure](virtual-machines-linux-mysql-install.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="create-a-virtual-machine-running-windows-server-2012-r2"></a>Créer un ordinateur virtuel exécutant Windows Server 2012 R2

Si vous ne disposez pas d’un ordinateur virtuel exécutant Windows Server 2012 R2, vous pouvez utiliser ce [didacticiel](virtual-machines-windows-classic-tutorial.md) pour créer l’ordinateur virtuel. 

## <a name="attach-a-data-disk"></a>Connectez un disque de données

Une fois que la machine virtuelle est créée, vous pouvez éventuellement attacher un disque de données supplémentaires. Ceci est recommandé pour les charges de travail et pour éviter de manquer d’espace sur le lecteur du système d’exploitation (c), qui inclut le système d’exploitation.

Voir [comment attacher un disque de données pour une machine virtuelle Windows](virtual-machines-windows-classic-attach-disk.md) et suivez les instructions pour y attacher un disque vide. Définissez le paramètre de cache hôte sur **Aucun** ou **en lecture seule**.

## <a name="log-on-to-the-virtual-machine"></a>Ouvrez une session sur l’ordinateur virtuel

Ensuite, vous allez [Ouvrir une session sur l’ordinateur virtuel](virtual-machines-windows-classic-connect-logon.md) afin que vous puissiez installer MySQL.

##<a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Installer et exécuter le serveur de la Communauté MySQL sur l’ordinateur virtuel

Suivez ces étapes pour installer, configurer et exécuter la version communautaire du serveur MySQL :

> [AZURE.NOTE] Ces étapes sont pour la 5.6.23.0 version communautaire de MySQL et de Windows Server 2012 R2. Votre expérience peut être différent pour les différentes versions de Windows Server ou de MySQL.

1.  Une fois que vous êtes connecté à l’ordinateur virtuel à l’aide du Bureau à distance, cliquez sur **Internet Explorer** à partir de l’écran de démarrage.
2.  Cliquez sur le bouton **Outils** dans le coin supérieur droit (l’icône roue cogged), puis cliquez sur **Options Internet**. Cliquez sur l’onglet **sécurité** , cliquez sur l’icône **Sites de confiance** , puis cliquez sur le bouton **Sites** . Ajoutez http://*. mysql.com à la liste des sites de confiance. Cliquez sur * *Fermer**, puis cliquez sur * *OK**.
3.  Dans la barre d’adresse d’Internet Explorer, tapez http://dev.mysql.com/downloads/mysql/.
4.  Utiliser le site de MySQL pour localiser et télécharger la dernière version du programme d’installation de MySQL pour Windows. Lorsque vous choisissez le programme d’installation de MySQL, téléchargez la version qui possède l’ensemble des fichiers (par exemple, le mysql-d’installation-Communauté-5.6.23.0.msi avec une taille de fichier de 282.4 Mo) et enregistrer le programme d’installation.
5.  Lorsque le programme d’installation a terminé le téléchargement, cliquez sur **exécuter** pour lancer le programme d’installation.
6.  Dans la page **Contrat de licence** , acceptez le contrat de licence et cliquez sur **suivant**.
7.  Dans la page **Choisir un Type d’installation** , cliquez sur le type d’installation que vous souhaitez, puis cliquez sur **suivant**. Les étapes suivantes supposent que la sélection du type d’installation **serveur uniquement** .
8.  Dans la page **d’Installation** , cliquez sur **exécuter**. Lorsque l’installation est terminée, cliquez sur **suivant**.
9.  Sur la page de **Configuration de produit** , cliquez sur **suivant**.
10. Sur la page de **mise en réseau et Type** , spécifiez vos options de connectivité et du type de configuration souhaité, y compris le port TCP si nécessaire. Sélectionnez **Afficher les Options avancées**, puis cliquez sur **suivant**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_TypeNetworking.png)

11. Dans la page des **comptes et des rôles** , spécifiez un mot de passe racine MySQL fort. Ajouter des comptes d’utilisateur MySQL supplémentaires selon les besoins, puis cliquez sur **suivant**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AccountsRoles_Filled.png)

12. Sur la page **Service de Windows** , spécifier des modifications aux paramètres par défaut pour exécuter le serveur MySQL comme un service Windows, si nécessaire, puis cliquez sur **suivant**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_WindowsService.png)

13. Dans la page **Options avancées** , spécifiez les modifications apportées aux options d’enregistrement en fonction des besoins, puis cliquez sur **suivant**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AdvOptions.png)

14. Dans la page **Appliquer la Configuration de serveur** , cliquez sur **exécuter**. Lorsque les étapes de configuration sont terminées, cliquez sur **Terminer**.
15. Sur la page de **Configuration de produit** , cliquez sur **suivant**.
16. Dans la page **Installation terminée** , cliquez sur **Journal de copie dans le Presse-papiers** si vous souhaitez examiner ultérieurement, puis cliquez sur **Terminer**.
17. À partir de l’écran de démarrage, tapez **mysql**, puis cliquez sur **Client de ligne de commande de MySQL 5.6**.
18. Entrez le mot de passe racine que vous avez spécifié à l’étape 11 et s’affiche avec une invite de commandes où vous pouvez exécuter des commandes pour configurer MySQL. Pour les détails des commandes et la syntaxe, consultez [Les manuels de référence MySQL](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_CommandPrompt.png)

19. Vous pouvez également configurer les paramètres par défaut configuration serveur, tels que les lecteurs et les répertoires de base et des données avec des entrées dans le fichier C:\Program Files (x86) \MySQL\MySQL Server 5.6\my-default.ini. Pour plus d’informations, consultez [5.1.2 Configuration de serveur par défaut est](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Configurer des points de terminaison

Si vous souhaitez que le service MySQL Server soit disponible sur les ordinateurs clients de MySQL sur Internet, vous devez configurer un point de terminaison pour le port TCP sur lequel le service MySQL Server est à l’écoute et créer une règle de pare-feu Windows supplémentaire. C’est le port TCP 3306, sauf si vous avez spécifié un port différent sur la page de **mise en réseau et le Type** (étape 10 de la procédure précédente).


> [AZURE.NOTE] Vous envisagez soigneusement les conséquences de cela, car le service MySQL Server disponible à tous les ordinateurs sur Internet. Vous pouvez définir l’ensemble des adresses IP source qui sont autorisés à utiliser le point de terminaison avec une liste de contrôle d’accès (ACL). Pour plus d’informations, consultez [comment définir les points de terminaison à une Machine virtuelle](virtual-machines-windows-classic-setup-endpoints.md).


Pour configurer un point de terminaison pour le service serveur MySQL :

1.  Dans le portail Azure classique, cliquez sur **ordinateurs virtuels**, cliquez sur le nom de votre machine virtuelle de MySQL, puis cliquez sur **points de terminaison**.
2.  Dans la barre de commandes, cliquez sur **Ajouter**.
3.  Dans la page **Ajouter un point de terminaison à une machine virtuelle** , cliquez sur la flèche vers la droite.
4.  Si vous utilisez la valeur par défaut le port TCP de MySQL 3306, cliquez sur **MySQL** dans la zone **nom**, puis cliquez sur la case à cocher.
5.  Si vous utilisez un port TCP différent, tapez un nom unique dans la zone **nom**. Sélectionnez **TCP** dans protocole, tapez le numéro de port dans **Port Public** et **Privé Port**, puis cliquez sur la case à cocher.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Ajouter une règle de pare-feu Windows pour autoriser le trafic de MySQL

Pour ajouter une règle de pare-feu Windows qui autorise le trafic de MySQL à partir d’Internet, exécutez la commande suivante à une invite de commande Windows PowerShell avec élévation de privilèges sur l’ordinateur virtuel du serveur MySQL.

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


    
## <a name="test-your-remote-connection"></a>Testez votre connexion à distance


Pour tester votre connexion à distance pour le service MySQL Server en cours d’exécution sur l’ordinateur virtuel Azure, vous devez d’abord déterminer le nom DNS correspondant au service de cloud qui contient l’ordinateur virtuel qui exécute le serveur MySQL.

1.  Dans le portail Azure classique, cliquez sur **ordinateurs virtuels**, cliquez sur le nom de votre machine virtuelle du serveur MySQL, puis cliquez sur **tableau de bord**.
2.  Tableau de bord de la machine virtuelle, notez la valeur de **Nom DNS** dans la section **Aperçu rapide** . Voici un exemple :

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_DNSName.png)

3.  À partir d’un ordinateur local exécutant MySQL ou MySQL client, exécutez la commande suivante pour ouvrir une session en tant qu’un utilisateur MySQL.

        mysql -u <yourMysqlUsername> -p -h <yourDNSname>

    Par exemple, pour le dbadmin3 de nom d’utilisateur MySQL et le nom DNS de testmysql.cloudapp.net pour l’ordinateur virtuel, utilisez la commande suivante.

        mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’exécution de MySQL, reportez-vous à la [Documentation MySQL](http://dev.mysql.com/doc/).
