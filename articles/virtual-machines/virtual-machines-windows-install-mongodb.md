<properties
    pageTitle="Installer MongoDB sur une machine virtuelle de Windows | Microsoft Azure"
    description="Découvrez comment installer MongoDB sur un Azure VM créée avec le modèle de déploiement du Gestionnaire de ressources de Windows Server 2012 R2 en cours d’exécution."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Installation et configuration de MongoDB sur un ordinateur virtuel Windows Azure
[MongoDB](http://www.mongodb.org) est une base de NoSQL d’open source, de hautes performances les plus courants. Cet article vous guide à travers l’installation et la configuration de MongoDB sur une machine virtuelle de Windows Server 2012 R2 (VM) dans Azure. Vous pouvez également [installer MongoDB sur un ordinateur virtuel de Linux dans Azure](virtual-machines-linux-install-mongodb.md).


## <a name="prerequisites"></a>Conditions préalables

Avant d’installer et de configurer MongoDB, vous devez créer un ordinateur virtuel et, idéalement, lui ajouter un disque de données. Consultez les articles suivants pour créer une machine virtuelle et l’ajout d’un disque de données :

- [Créer une machine virtuelle de Windows Server à l’aide du portail Azure](virtual-machines-windows-hero-tutorial.md) ou [créer un ordinateur virtuel de serveur de Windows à l’aide de PowerShell d’Azure](virtual-machines-windows-ps-create.md)
- [Attacher un disque de données pour une machine virtuelle de Windows Server à l’aide du portail Azure](virtual-machines-windows-attach-disk-portal.md) ou [attacher un disque de données à une VM de serveur Windows Azure PowerShell](https://msdn.microsoft.com/library/mt603673.aspx)
    
Pour commencer à installer et à configurer MongoDB, [Ouvrez une session sur votre machine virtuelle de Windows Server](virtual-machines-windows-connect-logon.md) à l’aide de bureau à distance.


## <a name="install-mongodb"></a>Installer MongoDB

> [AZURE.IMPORTANT] Les fonctionnalités de sécurité de MongoDB, comme l’authentification et le lien de l’adresse IP, ne sont pas activées par défaut. Fonctionnalités de sécurité doivent être activées avant le déploiement de MongoDB dans un environnement de production. Pour plus d’informations, consultez [sécurité de MongoDB et d’authentification](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Une fois que vous avez connecté à votre machine virtuelle à l’aide du Bureau à distance, ouvrez Internet Explorer à partir du menu **Démarrer** sur l’ordinateur virtuel.

2. Sélectionnez **l’utilisation recommandée de sécurité, de confidentialité et les paramètres de compatibilité** lorsque vous ouvrez Internet Explorer, puis cliquez sur **OK**.

3. Configuration de sécurité renforcée d’Internet Explorer est activée par défaut. Ajoutez le site Web de MongoDB à la liste des sites autorisés :

    - Sélectionnez l’icône des **Outils** dans le coin supérieur droit.
    - Dans les **Options Internet**, sélectionnez l’onglet **sécurité** , puis sélectionnez l’icône **Sites de confiance** .
    - Cliquez sur le bouton **Sites** . Ajouter _https://\*. mongodb.org_ à la liste des sites de confiance, puis fermez la boîte de dialogue.

    ![Configurer les paramètres de sécurité d’Internet Explorer](./media/virtual-machines-windows-install-mongodb/configure-internet-explorer-security.png)

4. Accédez à la page des [téléchargements de MongoDB -](http://www.mongodb.org/downloads) (http://www.mongodb.org/downloads).

5. Par défaut, il doit sélectionner l’édition du **Serveur de la Communauté** et la dernière version stable en cours pour Windows Server 2008 R2 64 bits et les versions ultérieures. Pour télécharger le programme d’installation, cliquez sur **télécharger (msi)**.

    ![Télécharger le programme d’installation de MongoDB](./media/virtual-machines-windows-install-mongodb/download-mongodb.png)

    Exécutez le programme d’installation après que le téléchargement est terminé.

6. Lisez et acceptez le contrat de licence. Lorsque vous y êtes invité, sélectionnez l’installation de **terminée** .

7. Sur l’écran final, cliquez sur **installer**.


## <a name="configure-the-vm-and-mongodb"></a>Configurer l’ordinateur virtuel et MongoDB

1. Les variables de chemin d’accès ne sont pas mis à jour par le programme d’installation de MongoDB. Sans le MongoDB `bin` emplacement dans votre variable de chemin d’accès, vous devez spécifier le chemin d’accès complet chaque fois que vous utilisez un exécutable MongoDB. Pour ajouter l’emplacement à votre variable de chemin d’accès :

    - Cliquez sur le menu **Démarrer** , puis sélectionnez **système**.
    - Cliquez sur **paramètres système avancés**, puis cliquez sur **Variables d’environnement**.
    - Sous **variables système**, sélectionnez le **chemin d’accès**, puis cliquez sur **Modifier**.

    ![Configurer des variables de chemin d’accès](./media/virtual-machines-windows-install-mongodb/configure-path-variables.png)

    Ajouter le chemin d’accès à votre MongoDB `bin` dossier. MongoDB est généralement installé dans `C:\Program Files\MongoDB`. Vérifiez le chemin d’installation sur votre ordinateur virtuel. L’exemple suivant ajoute la valeur par défaut de MongoDB installer emplacement à la `PATH` variable :

    ```
    ;C:\Program Files\MongoDB\Server\3.2\bin
    ```

    > [AZURE.NOTE] Veillez à ajouter le premier point-virgule (`;`) pour indiquer que vous ajoutez un emplacement pour votre `PATH` variable.

2. Créer des répertoires de données et de journaux de MongoDB sur votre disque de données. Dans le menu **Démarrer** , sélectionnez **invite de commande**. Les exemples suivants créent les répertoires sur le lecteur F:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```

3. Démarrer une instance de MongoDB avec la commande suivante, en ajustant le chemin d’accès à vos données et des journaux en conséquence :

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    Elle peut prendre plusieurs minutes pour MongoDB allouer les fichiers journal et démarrer l’écoute des connexions. Tous les messages du journal sont dirigées vers le fichier *F:\MongoLogs\mongolog.log* en `mongod.exe` serveur démarre et alloue des fichiers journaux.

    > [AZURE.NOTE] L’invite de commande reste axé sur cette tâche pendant l’exécution de votre instance de MongoDB. Laissez la fenêtre d’invite de commandes ouverte pour poursuivre l’exécution de MongoDB. Ou bien, installez MongoDB en tant que service, comme indiqué dans l’étape suivante.

4. Pour une expérience de MongoDB plus robuste, installer le `mongod.exe` en tant que service. Création d’un service signifie que vous n’avez pas besoin de quitter une invite de commande en cours d’exécution chaque fois que vous souhaitez utiliser MongoDB. Créer le service, comme suit, le chemin d’accès pour les répertoires de données et de journaux en ajustant en conséquence :

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
        --logappend  --install
    ```

    La commande ci-dessus crée un service appelé MongoDB, avec une description de le « Mongo DB ». Les paramètres suivants sont également spécifiés :

    - Le `--dbpath` option spécifie l’emplacement du répertoire de données.
    - Le `--logpath` option doit être utilisée pour spécifier un fichier journal, car le service en cours d’exécution n’est pas une fenêtre de commande pour afficher la sortie.
    - Le `--logappend` option indique qu’un redémarrage du service entraîne la sortie à ajouter au fichier journal existant.

  Pour démarrer le service de MongoDB, exécutez la commande suivante :

    ```
    net start MongoDB
    ```

    Pour plus d’informations sur la création du service de MongoDB, consultez [configurer un Service Windows pour MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Test de l’instance de MongoDB

Avec MongoDB exécute sous la forme d’une seule instance ou installé en tant que service, vous pouvez maintenant commencer la création et l’utilisation de vos bases de données. Pour démarrer l’environnement d’administration MongoDB, ouvrez une autre fenêtre de l’invite de commande dans le menu **Démarrer** et saisissez la commande suivante :

```
mongo  
```

Vous pouvez répertorier les bases de données avec le `db` commande. Insérer des données comme suit :

```
db.foo.insert( { a : 1 } )
```

Rechercher des données comme suit :

```
db.foo.find()
```

La sortie est similaire à l’exemple suivant :

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Quitter le `mongo` de la console comme suit :

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurer les pare-feu et les règles du groupe de sécurité réseau
Maintenant que MongoDB est installé et en cours d’exécution, ouvrir un port dans le pare-feu Windows pour vous connecter à distance à MongoDB. Pour créer une nouvelle règle de trafic entrant pour autoriser le port TCP 27017, ouvrez une invite de PowerShell d’administration et entrez la commande suivante :

```powerShell
New-NetFirewallRule -DisplayName "Allow MongoDB" -Direction Inbound `
    -Protocol TCP -LocalPort 27017 -Action Allow
```

Vous pouvez également créer la règle à l’aide de l’outil de gestion graphique du **Pare-feu Windows avec sécurité avancée** . Créer une nouvelle règle de trafic entrant pour autoriser le port TCP 27017.

Si nécessaire, créez une règle de groupe de sécurité de réseau pour autoriser l’accès à MongoDB de hors du sous-réseau Azure réseau virtuel existant. Vous pouvez créer les règles du groupe de sécurité de réseau en utilisant le [portail Azure](virtual-machines-windows-nsg-quickstart-portal.md) ou [l’Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md). Comme avec les règles du pare-feu Windows, autoriser le port TCP 27017 pour l’interface réseau virtuelle de votre VM MongoDB.

> [AZURE.NOTE] Le port TCP 27017 est le port par défaut utilisé par MongoDB. Vous pouvez modifier ce port à l’aide de la `--port` paramètre lors du démarrage de `mongod.exe` manuellement ou à partir d’un service. Si vous modifiez le port, vérifiez que mettre à jour les règles de pare-feu Windows et le groupe de sécurité réseau dans les étapes précédentes.


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris comment installer et configurer MongoDB sur votre machine virtuelle de Windows. Vous pouvez maintenant accéder à MongoDB sur votre machine virtuelle Windows, en suivant les rubriques avancées de la [documentation MongoDB](https://docs.mongodb.com/manual/).
