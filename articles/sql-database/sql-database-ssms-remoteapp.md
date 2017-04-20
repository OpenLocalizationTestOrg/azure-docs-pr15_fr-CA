<properties
    pageTitle="Se connecter à la base de données SQL à l’aide de SQL Server Management Studio dans Azure RemoteApp | Microsoft Azure"
    description="Utilisez ce didacticiel pour apprendre à utiliser SQL Server Management Studio dans Azure RemoteApp pour la sécurité et les performances lors de la connexion à la base de données de SQL"
    services="sql-database"
    documentationCenter=""
    authors="adhurwit"
    manager="jhubbard"/>

<tags
    ms.service="sql-database"
    ms.workload="data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>Utilisez SQL Server Management Studio dans Azure RemoteApp à se connecter à la base de données de SQL

## <a name="introduction"></a>Introduction  
Ce didacticiel vous montre comment utiliser SQL Server Management Studio (SSMS) pour se connecter à la base de données de SQL Azure RemoteApp. Il vous guide dans le processus de configuration de SQL Server Management Studio dans Azure RemoteApp, explique les avantages et illustre les fonctionnalités de sécurité que vous pouvez utiliser dans Azure Active Directory.

**Estimée durée :** 45 minutes

## <a name="ssms-in-azure-remoteapp"></a>SSMS dans Azure RemoteApp

RemoteApp Azure est un service RDS dans Azure qui fournit des applications. Vous pouvez en savoir plus à ce sujet ici : [What RemoteApp ?](../remoteapp/remoteapp-whatis.md)

SSMS exécute dans Azure RemoteApp vous donne la même expérience que l’exécution de SSMS localement.

![Capture d’écran présentant SSMS exécute dans Azure RemoteApp][1]



## <a name="benefits"></a>Avantages

Il existe de nombreux avantages à l’aide de SSMS dans Azure RemoteApp, y compris :

- Port 1433 sur Azure SQL Server n’a pas à être exposés en externe (en dehors d’Azure).
- Aucune nécessité de les conserver d’ajout et de suppression des adresses IP dans le pare-feu Azure SQL Server.
- Toutes les connexions RemoteApp d’Azure se produisent via HTTPS sur à l’aide du port 443 cryptées de protocole Bureau à distance
- Utilisateurs multiples, il peut mettre à l’échelle.
- Il existe un gain de performance d’avoir SSMS dans la même région, comme la base de données SQL.
- Vous pouvez auditer l’utilisation d’Azure RemoteApp avec l’édition Premium d’Azure Active Directory ayant des rapports d’activité utilisateur.
- Vous pouvez activer une authentification multifacteur (AMF).
- Accès SSMS n’importe où lorsque vous utilisez une des clients Azure RemoteApp pris en charge qui inclut l’iOS, Android, Mac, Windows Phone et Windows de l’ordinateur.


## <a name="create-the-azure-remoteapp-collection"></a>Créer la collection Azure RemoteApp

Voici les étapes pour créer votre collection Azure RemoteApp avec SSMS :


### <a name="1-create-a-new-windows-vm-from-image"></a>1. Créez une machine virtuelle Windows à partir de l’Image
Utiliser l’Image de « Windows Server à distance Bureau Session hôte Windows Server 2012 R2 » à partir de la galerie pour rendre votre nouvelle machine virtuelle.


### <a name="2-install-ssms-from-sql-express"></a>2. Installez SSMS de SQL Express

Allez sur la nouvelle machine virtuelle et d’accéder à cette page de téléchargement : [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299)

Il existe une option permettant de ne télécharger SSMS. Après le téléchargement, passez dans le répertoire d’installation et exécutez le programme d’installation de SSMS.

Vous devez également installer le Service Pack 1 de SQL Server 2014. Vous pouvez le télécharger ici : [Service Pack 1 (SP1) de Microsoft SQL Server 2014](https://www.microsoft.com/en-us/download/details.aspx?id=46694)

2014 de SQL Server Service Pack 1 inclut les fonctionnalités essentielles pour travailler avec une base de données de SQL Azure.


### <a name="3-run-validate-script-and-sysprep"></a>3. Exécutez le script de validation et de Sysprep

Sur le bureau de la machine virtuelle est un script PowerShell appelé Validate. Exécutez cette procédure en double-cliquant. Il vérifie que l’ordinateur virtuel est prêt à être utilisé pour l’hébergement à distance des applications. Lorsque la vérification est terminée, il vous demande d’exécuter sysprep, choisir de l’exécuter.

Lorsque sysprep s’achève, il va arrêter la machine virtuelle.

Pour plus d’informations sur la création d’une image d’Azure RemoteApp, voir : [Comment faire pour créer une image de modèle RemoteApp dans Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)


### <a name="4-capture-image"></a>4. capture d’image

Lorsque la machine virtuelle est arrêté en cours d’exécution, il recherche dans le portail actuel et capturer.

Pour en savoir plus sur la capture d’une image, reportez-vous à [capturer une image d’un ordinateur virtuel de Azure Windows créé avec le modèle de déploiement classique](../virtual-machines/virtual-machines-windows-classic-capture-image.md)


### <a name="5-add-to-azure-remoteapp-template-images"></a>5. Ajoutez les images Azure RemoteApp modèle

Dans la section Azure RemoteApp du portail actuel, cliquez sur l’onglet Images du modèle et cliquez sur Ajouter. Dans la zone contextuelle, sélectionnez « Importation d’une image à partir de votre bibliothèque de Machines virtuelles » et choisissez l’Image que vous venez de créer.



### <a name="6-create-cloud-collection"></a>6. créer une collection de nuage

Dans le portail actuel, créez une nouvelle Collection de nuage de RemoteApp Azure. Cliquez sur l’Image du modèle que vous venez d’importer avec SSMS installé dessus.

![Créer la nouvelle collection de nuage][2]


### <a name="7-publish-ssms"></a>7. publier SSMS

Sur la publication, onglet de votre nouvelle collection de nuage, sélectionnez Publier une application à partir du Menu Démarrer et choisissez SSMS à partir de la liste.

![Publication d’application][5]

### <a name="8-add-users"></a>8. Ajoutez des utilisateurs

Sous l’onglet accès de l’utilisateur, vous pouvez sélectionner les utilisateurs qui auront accès à cette collection Azure RemoteApp qui inclut uniquement les SSMS.

![Ajouter utilisateur][6]


### <a name="9-install-the-azure-remoteapp-client-application"></a>9. installation de l’application cliente Azure RemoteApp

Vous pouvez télécharger et installer un client Azure RemoteApp ici : téléchargement de [| RemoteApp Azure](https://www.remoteapp.windowsazure.com/en/clients.aspx)



## <a name="configure-azure-sql-server"></a>Configurer SQL Server Azure

La seule configuration nécessaire est pour vous assurer que les Services Azure est activé pour le pare-feu. Si vous utilisez cette solution, vous n’avez pas besoin d’ajouter toutes les adresses IP pour ouvrir le pare-feu. Le trafic réseau est autorisé pour le SQL Server est provenant d’autres services Azure.


![Permettre d’Azure][4]



## <a name="multi-factor-authentication-mfa"></a>Plusieurs facteurs d’authentification (AMF)

AMF peut être activé pour cette application en particulier. Accédez à l’onglet Applications de Azure Active Directory. Vous trouverez une entrée pour Microsoft Azure RemoteApp. Si vous cliquez sur cette application, puis configurez, vous verrez la page suivante, dans laquelle vous pouvez activer AMF pour cette application.

![Activer AMF][3]



## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Auditez l’activité de l’utilisateur avec Azure Active Directory Premium

Si vous n’avez pas d’Azure AD Premium, vous devez activer dans la section de licences de votre répertoire. Avec prime activé, vous pouvez affecter des utilisateurs au niveau Premium.

Lorsque vous accédez à un utilisateur dans Active Directory Azure, vous pouvez accéder ensuite à l’onglet d’activité pour afficher les informations de connexion pour Azure RemoteApp.



## <a name="next-steps"></a>Étapes suivantes

Toutes les étapes ci-dessus, vous serez en mesure d’exécuter le client Azure RemoteApp et reconnectez-vous avec un utilisateur affecté. Vous obtenez SSMS comme l’une de vos applications, et vous pouvez l’exécuter comme vous le feriez s’il était installé sur votre ordinateur d’accéder à SQL Server de Azure.

Pour plus d’informations sur la façon d’établir la connexion à la base de données de SQL, reportez-vous à la section [se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).


C’est tout pour l’instant. Profitez !



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
