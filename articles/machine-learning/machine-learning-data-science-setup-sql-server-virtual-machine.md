<properties
    pageTitle="Configurer un ordinateur virtuel de SQL Server en tant que bloc-notes de IPython serveur | Microsoft Azure"
    description="La valeur d’une Science Virtual Machine données avec SQL Server et le serveur de IPython."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev" 
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-sql-server-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurer un ordinateur virtuel de Azure SQL Server comme serveur de IPython portable pour analytique avancée

Cette rubrique montre comment mettre en service et de configurer un ordinateur virtuel de SQL Server à utiliser dans le cadre d’un environnement informatique de données basées sur le nuage. La machine virtuelle Windows est configurée avec la prise en charge des outils comme IPython bloc-notes, Explorateur de stockage Azure et AzCopy, ainsi que les autres utilitaires qui sont utiles pour des projets de science. Explorateur de stockage Azure et AzCopy, par exemple, fournissent des moyens pratiques pour transférer des données vers un stockage blob Azure à partir de votre ordinateur local ou pour le télécharger sur votre ordinateur local depuis le stockage blob.

La galerie d’Azure VM inclut plusieurs images qui contiennent de Microsoft SQL Server. Sélectionnez une image de machine virtuelle de SQL Server qui est adaptée à vos besoins. Images recommandées sont les suivantes :

- SQL Server 2012 SP2 entreprise pour les petites tailles de données moyennes
- Optimisé de SQL Server 2012 SP2 entreprise pour le DataWarehousing des charges de travail pour les formats de données de grande à très grande envergure

 > [AZURE.NOTE] SQL Server 2012 SP2 Entreprise image **n’inclut pas un disque de données**. Vous devez ajouter ou joindre un ou plusieurs disques durs virtuels pour stocker vos données. Lorsque vous créez une machine virtuelle Azure, il possède un disque du système d’exploitation mappé vers le lecteur C et un disque temporaire est mappé vers le lecteur D. N’utilisez pas le lecteur D pour stocker des données. Comme son nom l’indique, il fournit un stockage temporaire uniquement. Il ne propose aucune redondance ou la sauvegarde, car il ne réside pas dans le stockage Azure.


##<a name="Provision"></a>Se connecter au portail Azure classique et mettre en service un ordinateur virtuel de SQL Server

1.  Connectez-vous au [Portail classique d’Azure](http://manage.windowsazure.com/) à l’aide de votre compte.
    Si vous ne disposez pas d’un compte Azure, visitez [Azure version d’essai gratuite](https://azure.microsoft.com/pricing/free-trial/).

2.  Sur le portail classique Azure, en bas à gauche de la page web, cliquez sur **+ Nouveau**et cliquez sur **Calculer**, cliquez sur **MACHINE virtuelle**, puis cliquez sur **De la galerie**.

3.  Sur la page **créer une Machine virtuelle** , sélectionnez une image de machine virtuelle contenant SQL Server en fonction de vos besoins, puis cliquez sur la flèche suivant en bas à droite de la page. Pour obtenir les informations les plus récentes sur les images de SQL Server prises en charge sur Azure, consultez [Mise en route de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294720) dans la documentation de [SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294719) définie.

    ![Sélectionnez SQL Server VM][1]

4.  Sur la première page de **Configuration de Machine virtuelle** , fournir les informations suivantes :

    -   Fournissez un **nom d’ordinateur virtuel**.
    -   Dans la zone **Nouveau nom d’utilisateur** , tapez le nom d’utilisateur unique pour le compte d’administrateur local de machine virtuelle.
    -   Dans la zone **Nouveau mot de passe** , tapez un mot de passe. Pour plus d’informations, consultez [Mots de passe renforcés](http://msdn.microsoft.com/library/ms161962.aspx).
    -   Dans la zone **Confirmer le mot de passe** , retapez le mot de passe.
    -   Sélectionnez la **taille** de appropriée dans la liste déroulante.

     > [AZURE.NOTE] La taille de la machine virtuelle est spécifiée au cours de la mise en service : A2 est la plus petite taille recommandée pour les charges de travail. La taille minimale recommandée pour un ordinateur virtuel est A3 lors de l’utilisation de SQL Server Enterprise Edition. Sélectionnez A3 ou version ultérieure lors de l’utilisation de SQL Server Enterprise Edition. Sélectionnez A4 lors de l’utilisation de SQL Server 2012 ou optimisé d’entreprise 2014 pour les images de charges de travail transactionnelles.
Sélectionnez A7 lors de l’utilisation de SQL Server 2012 ou optimisé d’entreprise 2014 pour les images de charges de travail de l’entrepôt de données. La taille sélectionnée limite le nombre de disques de données que vous pouvez configurer. Pour obtenir des informations plus récentes sur la taille de l’ordinateur virtuel disponible et le nombre de disques de données que vous pouvez attacher à une machine virtuelle, voir [Taille de Machine virtuelle pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Informations de tarification, consultez [Tarification de Macines virtuel](https://azure.microsoft.com/pricing/details/virtual-machines/).

    Cliquez sur la flèche suivant en bas à droite pour continuer.

    ![Configuration de machine virtuelle][2]

5.  Sur la deuxième page de **configuration de machine virtuelle** , configurer des ressources pour la mise en réseau, de stockage et disponibilité :

    -   Dans la zone de **Service Cloud** , sélectionnez **créer un nouveau service en nuage**.
    -   Dans la zone **Nom DNS du Service Cloud** , fournir la première partie d’un nom DNS de votre choix, afin qu’elle termine un nom au format **TESTNAME.cloudapp.net**
    -   Dans la zone de la **Région/affinité/virtuels du groupe réseau** , sélectionnez une région dans laquelle cette image virtuelle sera hébergée.
    -   Dans le **Compte de stockage**, sélectionnez un compte de stockage existant ou sélectionnez une généré automatiquement.
    -   Dans la zone **Disponibilité** , sélectionnez **(aucun)**.
    -   Lisez et acceptez les informations de tarification.

6.  Dans la section **points de terminaison** , cliquez dans la liste déroulante sous **nom**, du vide et sélectionnez **MSSQL** , puis tapez le numéro de port de l’instance du moteur de base de données (**1433** pour l’instance par défaut).

7.  Votre ordinateur virtuel de SQL Server peut également servir d’un serveur IPython portable, qui sera configuré dans une étape ultérieure.
    Ajouter un nouveau point de terminaison pour spécifier le port à utiliser pour votre serveur IPython bloc-notes. Entrez un nom dans la colonne **nom** , sélectionnez un numéro de port de votre choix pour le port public et 9999 pour le port privé.

    Cliquez sur la flèche suivant en bas à droite pour continuer.

    ![Sélectionnez les ports MSSQL et IPython][3]

8.  Acceptez l’option **d’agent d’installer la machine virtuelle** par défaut activée et cliquez sur le la coche dans le coin inférieur droit de l’Assistant pour terminer la processus d’approvisionnement de VM.

    `![Options finales de machine virtuelle][4]

9.  Patientez pendant l’Azure prépare votre machine virtuelle. Attendez l’état de la machine virtuelle pour passer :

    -   Démarrage (Provisioning)
    -   Arrêté
    -   Démarrage (Provisioning)
    -   En cours d’exécution (Provisioning)
    -   En cours d’exécution


##<a name="RemoteDesktop"></a>Ouvrez la machine virtuelle à l’aide du Bureau à distance et terminer l’installation

1.  Lors de la mise en service terminée, cliquez sur le nom de votre machine virtuelle pour accéder à la page du tableau de bord. En bas de la page, cliquez sur **se connecter**.

2.  Cliquez pour ouvrir le fichier rpd en utilisant le programme de bureau à distance de Windows (`%windir%\system32\mstsc.exe`).

3.  Dans la boîte de dialogue **Sécurité de Windows** , fournir le mot de passe pour le compte d’administrateur local que vous avez spécifié dans une étape antérieure.
    (Vous pourriez être demandé pour vérifier les informations d’identification de l’ordinateur virtuel.)

4.  La première fois que vous ouvrez une session sur cet ordinateur virtuel, plusieurs processus peuvent avoir besoin pour s’exécuter, y compris le programme d’installation de votre bureau, mises à jour de Windows et l’achèvement des tâches de configuration initiale de Windows (sysprep). Une fois sysprep de Windows est terminée, le programme d’installation de SQL Server effectue les tâches de configuration. Ces tâches peuvent entraîner un délai de quelques minutes pendant que les terminer. `SELECT @@SERVERNAME`peut ne pas retourner le nom correct jusqu'à ce que la fin de l’installation de SQL Server et SQL Server Management Studio n’est peut-être pas visible sur la page de démarrage.

Une fois que vous êtes connecté à l’ordinateur virtuel avec le Bureau à distance de Windows, l’ordinateur virtuel fonctionne comme tout autre ordinateur. Se connecter à l’instance par défaut de SQL Server avec SQL Server Management Studio, (s’exécutant sur l’ordinateur virtuel) de façon normale.


##<a name="InstallIPython"></a>Installer IPython bloc-notes et autres outils de prise en charge

Pour configurer votre nouvel ordinateur SQL Server virtuel pour servir un serveur IPython portable et installer d’autres outils de prise en charge ce AzCopy, Explorateur de stockage Azure, les packages de données Science Python utiles et d’autres, un script de personnalisation spéciale est fourni. Pour installer :

- Cliquez sur l’icône **Démarrer de Windows** , puis cliquez sur **invite de commandes (Admin)**
- Copiez les commandes suivantes et le coller au niveau de l’invite de commande.

        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Lorsque vous y êtes invité, entrez un mot de passe de votre choix pour le serveur de l’ordinateur portable de IPython.
- Le script de personnalisation automatise plusieurs procédures de post-installation, qui incluent :
    + Installation et configuration du serveur du carnet d’IPython
    + Ouverture des ports TCP dans le pare-feu Windows pour les points de terminaison créé précédemment :
    + Pour une connectivité à distance SQL Server
    + Pour la connectivité à distance du serveur IPython portable
    + L’extraction des exemples d’agendas IPython et les scripts SQL
    + Téléchargement et installation des packages de données Science Python utiles
    + Téléchargement et installation des outils Azure comme AzCopy et l’Explorateur de stockage Azure  
<br>
- Vous pouvez accéder et exécuter IPython bloc-notes à partir de n’importe quel navigateur local ou à distance à l’aide d’une URL de la forme `https://<virtual_machine_DNS_name>:<port>`, où port est le port public IPython vous avez sélectionné lors de la mise en service de l’ordinateur virtuel.
- Serveur de IPython portable s’exécute comme un service d’arrière-plan et va redémarrer automatiquement lorsque vous redémarrez l’ordinateur virtuel.

##<a name="Optional"></a>Attacher le disque de données selon les besoins

Si votre image VM n’inclut pas de disques de données, c'est-à-dire les disques que le lecteur C (disque du système d’exploitation) et D (disque temporaire), vous devez ajouter un ou plusieurs disques de données pour stocker vos données. L’image de machine virtuelle pour l’optimisation de SQL Server 2012 SP2 entreprise pour les charges de travail DataWarehousing est préconfigurée avec des disques supplémentaires pour les fichiers journaux et de données de SQL Server.

 > [AZURE.NOTE] N’utilisez pas le lecteur D pour stocker des données. Comme son nom l’indique, il fournit un stockage temporaire uniquement. Il ne propose aucune redondance ou la sauvegarde, car il ne réside pas dans le stockage Azure.

Pour attacher des disques de données supplémentaires, suivez la procédure décrite dans [comment attacher un disque de données pour une Machine virtuelle de Windows](virtual-machines-windows-classic-attach-disk.md), qui vous guidera pour :

1. Attachement de disques vides à la machine virtuelle mise en service dans les étapes précédentes
2. Initialisation des nouveaux disques sur l’ordinateur virtuel


##<a name="SSMS"></a>Se connecter à SQL Server Management Studio et activer l’authentification en mode mixte

Le moteur de base de données SQL Server ne peut pas utiliser l’authentification Windows sans un environnement de domaine. Pour vous connecter au moteur de base de données à partir d’un autre ordinateur, configurez SQL Server pour l’authentification en mode mixte. L’authentification en mode mixte permet à la fois SQL Server et l’authentification Windows. Le mode d’authentification SQL est requise pour l’acquisition des données directement à partir de vos bases de données SQL Server VM dans le [Studio de formation Azure Machine](https://studio.azureml.net) en utilisant le module Importation de données.

1.  Lorsque vous êtes connecté à la machine virtuelle à l’aide de bureau à distance, utilisez le volet de **recherche** de Windows et tapez **SQL Server Management Studio** (SMSS). Cliquez pour démarrer la de Management Studio (SSMS) de SQL Server. Vous souhaiterez peut-être ajouter un raccourci vers SSMS sur votre bureau pour une utilisation ultérieure.

    ![Démarrer SSMS][5]

    La première fois que vous ouvrez Management Studio, il doit créer l’environnement des utilisateurs de Management Studio. Cette opération peut prendre quelques instants.

2.  Lors de l’ouverture, Management Studio affiche la boîte de dialogue **se connecter au serveur** . Dans la zone **nom du serveur** , tapez le nom de l’ordinateur virtuel pour se connecter au moteur de base de données avec l’Explorateur d’objets.
    (Au lieu du nom de la machine virtuelle vous pouvez également utiliser **(local)** ou un point comme **nom de serveur**. Sélectionnez **L’authentification Windows**et laissez ** *votre\_VM\_nom de*\\votre\_local\_administrateur** dans la zone **nom de l’utilisateur** . Cliquez sur **se connecter**.

    ![Se connecter au serveur][6]

    <br>

     > [AZURE.TIP] Vous pouvez modifier le mode d’authentification SQL Server à l’aide d’un changement de clé de Registre de Windows ou le Management Studio de SQL Server. Pour modifier le mode d’authentification à l’aide de la modification de la clé du Registre, démarrez une **Nouvelle requête** et exécutez le script suivant :

        USE master
        go

        EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
        go


    Pour modifier le mode d’authentification à l’aide de la gestion de SQL Server Studio :

3.  Dans l' **Explorateur d’objets SQL Server Management Studio**, cliquez sur le nom de l’instance de SQL Server (le nom de la machine virtuelle), puis cliquez sur **Propriétés**.

    ![Propriétés de serveur][7]

4.  Dans la page **sécurité** , sous **authentification du serveur**, sélectionnez le **mode d’authentification Windows et SQL Server**, puis cliquez sur **OK**.

    ![Sélectionnez le Mode d’authentification][8]

5.  Dans la boîte de dialogue **SQL Server Management Studio** , cliquez sur **OK** pour accuser réception de la nécessité de redémarrer SQL Server.

6.  Dans l' **Explorateur d’objets**, cliquez sur votre serveur, puis cliquez sur **redémarrer**. (Si l’Agent de SQL Server est en cours d’exécution, il doit également être redémarré.)

    ![Redémarrage][9]

7.  Dans la boîte de dialogue **SQL Server Management Studio** , cliquez sur **Oui** pour accepter que vous souhaitez redémarrer SQL Server.

##<a name="Logins"></a>Créer des connexions de l’authentification SQL Server

Pour vous connecter au moteur de base de données à partir d’un autre ordinateur, vous devez créer au moins une connexion d’authentification SQL Server.  

Vous pouvez créer des nouvelles connexions d’accès SQL Server par programme ou en utilisant le Management Studio de SQL Server. Pour créer un nouvel utilisateur avec l’authentification SQL par programme, lancez une **Nouvelle requête** et exécutez le script suivant. Remplacez < nom d’utilisateur\> et < nouveau mot de passe\> avec un choix de *nom d’utilisateur* et le *mot de passe*. 


    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


Ajustez la stratégie de mot de passe (l’exemple code désactive expiration de mot de passe et la vérification de stratégie). Pour plus d’informations sur les connexions SQL Server, voir [Création d’une connexion d’accès](http://msdn.microsoft.com/library/aa337562.aspx).  

Pour créer de nouvelles connexions d’accès SQL Server à l’aide de la de Management Studio de SQL Server :

1.  Dans l' **Explorateur d’objets SQL Server Management Studio**, développez le dossier de l’instance de serveur dans lequel vous souhaitez créer la nouvelle connexion.

2.  Cliquez sur le dossier **sécurité** , pointez sur **Nouveau**et sélectionnez **connexion...**.

    ![Nouvelle connexion][10]

3.  Dans la boîte de dialogue **nouvelle connexion** , dans la page **Général** , entrez le nom du nouvel utilisateur dans la zone **nom d’ouverture de session** .

4.  Sélectionnez **l’authentification SQL Server**.

5.  Dans la zone **mot de passe** , entrez un mot de passe pour le nouvel utilisateur. Entrez de nouveau ce mot de passe dans la zone **Confirmer le mot de passe** .

6.  Pour appliquer les options de stratégie de mot de passe pour la complexité et de l’application, sélectionnez **stratégie de mot de passe d’appliquer** (recommandé). Il s’agit d’une option par défaut lors de l’authentification SQL Server est sélectionnée.

7.  Pour appliquer les options de la stratégie d’expiration du mot de passe, sélectionnez **appliquer l’expiration du mot de passe** (recommandé). Appliquer la stratégie de mot de passe doit être sélectionnée pour activer cette case à cocher. Il s’agit d’une option par défaut lors de l’authentification SQL Server est sélectionnée.

8.  Pour forcer l’utilisateur à créer un nouveau mot de passe après la première utilisation de la connexion, sélectionnez l' **utilisateur doit changer de mot de passe à la prochaine connexion** (recommandé si cette connexion est pour quelqu'un d’autre à utiliser. Si la connexion est pour votre propre usage, ne sélectionnez pas cette option.) Appliquer expiration mot de passe doit être sélectionnée pour activer cette case à cocher. Il s’agit d’une option par défaut lors de l’authentification SQL Server est sélectionnée.

9.  Dans la liste **base de données de la valeur par défaut** , sélectionnez une base de données par défaut pour la connexion. **maître** est la valeur par défaut pour cette option. Si vous n’avez pas encore créé une base de données de l’utilisateur, laissez **maître**.

10. Dans la liste **langue par défaut** , laissez la valeur **par défaut** .

    ![Propriétés de la connexion][11]

11. S’il s’agit de la première connexion que vous créez, vous souhaiterez peut-être désigner cette connexion en tant qu’un administrateur SQL Server. Si tel est le cas, dans la page **Rôles de serveur** , consultez **sysadmin**.

    > [AZURE.IMPORTANT] Les membres du rôle de serveur fixe sysadmin ont le contrôle complet du moteur de base de données. Pour des raisons de sécurité, vous devez soigneusement restreindre l’appartenance à ce rôle.

    ![sysadmin][12]

12. Cliquez sur OK.

##<a name="DNS"></a>Déterminer le nom DNS de l’ordinateur virtuel

Pour se connecter au moteur de base de données SQL Server à partir d’un autre ordinateur, vous devez connaître le nom de système de nom de domaine (DNS) de l’ordinateur virtuel.

(C’est le nom qu'internet utilise pour identifier la machine virtuelle. Vous pouvez utiliser l’adresse IP, mais l’adresse IP peut changer lorsque Azure déplace les ressources de redondance ou de maintenance. Le nom DNS est stable car il peut être redirigé vers une nouvelle adresse IP.)

1.  Dans le portail classique Azure (ou à partir de l’étape précédente), sélectionnez les **ordinateurs virtuels**.

2.  Sur la page **INSTANCES DE MACHINE virtuelle** , dans la colonne de **Nom DNS** , rechercher et copier le nom DNS de l’ordinateur virtuel qui apparaît précédée de **http://**. (L’interface utilisateur ne peut pas afficher la totalité du nom, mais vous pouvez avec le bouton droit dessus et sélectionnez Copier.)

##<a name="cde"></a>Se connecter au moteur de base de données à partir d’un autre ordinateur

1.  Sur un ordinateur connecté à internet, ouvrez SQL Server Management Studio.

2.  Dans la boîte de dialogue **se connecter au moteur de base de données** ou de **se connecter au serveur** , dans la zone **nom du serveur** , entrez le nom DNS de l’ordinateur virtuel (déterminé à l’étape précédente) et un numéro de port publics de point de terminaison dans le format de *NomDNS, numéro_port* comme **tutorialtestVM.cloudapp.net,57500**.

3.  Dans la zone **authentification** , sélectionnez **L’authentification SQL Server**.

4.  Dans la zone **connexion** , tapez le nom d’une connexion d’accès que vous avez créé dans une précédente tâche.

5.  Dans la zone **mot de passe** , tapez le mot de passe de la connexion que vous créez dans une tâche précédente.

6.  Cliquez sur **se connecter**.

##<a name="amlconnect"></a>Se connecter au moteur de base de données d’apprentissage automatique Azure

Dans les étapes ultérieures du processus Team données Science, vous utiliserez le [Studio d’apprentissage Machine Azure](https://studio.azureml.net) pour créer et déployer des modèles de formation des machines. Pour réceptionner les données provenant de vos bases de données SQL Server VM directement dans la formation de Machine Azure de formation ou d’évaluation, utilisez le module **Importation de données** dans une nouvelle expérience de [Studio de formation de Machine Azure](https://studio.azureml.net) . Cette rubrique est traitée dans plus de détails via les liens de guide de processus d’équipe données scientifiques. Pour une introduction, consultez [Nouveautés Azure Machine Learning Studio ?](machine-learning-what-is-ml-studio.md).

2.  Dans le volet de **Propriétés** du [module d’importation de données](https://msdn.microsoft.com/library/azure/dn905997.aspx), sélectionnez la **Base de données de SQL Azure** dans la liste déroulante de **Source de données** .

3.  Dans la zone de texte **nom du serveur de base de données** , entrez`tcp:<DNS name of your virtual machine>,1433`

4.  Dans la zone de texte **nom du compte utilisateur serveur** , entrez le nom d’utilisateur SQL.

5.  Entrez le mot de passe de l’utilisateur sql dans la zone de texte **mot de passe utilisateur serveur** .

    ![Données Azure ML][13]

##<a name="shutdown"></a>Arrêt et libérer l’ordinateur virtuel lorsqu’elles pas en cours d’utilisation

Les Machines virtuelles Azure sont tarifés comme **payez uniquement ce que vous utilisez**. Pour vous assurer que vous ne sont pas en cours facturés lorsque vous n’utilisez ne pas votre ordinateur virtuel, il doit se trouver dans l’état **arrêté (Deallocated)** .

> [AZURE.NOTE] Arrêt de la machine virtuelle à partir de l’intérieur (à l’aide d’options d’alimentation de Windows), la machine virtuelle est arrêtée mais reste allouée. Pour vous assurer que vous ne soyez pas facturé, toujours arrêter les machines virtuelles à partir du [Portail classique d’Azure](http://manage.windowsazure.com/). Vous pouvez également arrêter la machine virtuelle par le biais de Powershell en appelant ShutdownRoleOperation avec « PostShutdownAction » égal à « StoppedDeallocated ».

Pour arrêter et supprimer de l’ordinateur virtuel :

1. Connectez-vous au [Portail classique d’Azure](http://manage.windowsazure.com/) à l’aide de votre compte.  

2. Sélectionnez les **ordinateurs virtuels** à partir de la barre de navigation de gauche.

3. Dans la liste des ordinateurs virtuels, cliquez sur le nom de votre machine virtuelle, puis accédez à la page de **tableau de bord** .

4. En bas de la page, cliquez sur **Arrêter**.

![Arrêt de la machine virtuelle][15]

L’ordinateur virtuel seront libéré mais pas supprimé. Vous pouvez redémarrer votre machine virtuelle à tout moment à partir du portail classique d’Azure.

## <a name="your-azure-sql-server-vm-is-ready-to-use-whats-next"></a>Votre ordinateur virtuel de Azure SQL Server est prêt à l’emploi : quel est l’avenir ?

Votre ordinateur virtuel est prêt à utiliser dans vos exercices de science de données. La machine virtuelle est également prêt à être utilisé en tant que bloc-notes de IPython serveur d’exploration et de traitement de données et d’autres tâches en conjonction avec un apprentissage de Machine Azure et le processus de Science données Team (TDSP).

Les étapes du processus de données scientifiques sont mappés dans le [Processus d’équipe données Science](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et peuvent inclure des étapes de déplacement des données vers HDInsight, de traitent et d’aperçu en vue de l’apprentissage à partir des données avec Azure Machine d’apprentissage.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 
