<properties
   pageTitle="Exécuter n’importe quelle application Windows sur n’importe quel périphérique avec Azure RemoteApp | Microsoft Azure"
   description="Découvrez comment partager n’importe quelle application Windows avec vos utilisateurs à l’aide de RemoteApp d’Azure."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="run-any-windows-app-on-any-device-with-azure-remoteapp"></a>Exécuter n’importe quelle application Windows sur n’importe quel périphérique avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Vous pouvez exécuter une application Windows n’importe où sur n’importe quel périphérique, tout de suite, gravement - simplement à l’aide de RemoteApp d’Azure. Qu’il s’agisse d’une application personnalisée écrite il y a 10 ans, ou une application Office, vos utilisateurs n’aient plus être liée à un système d’exploitation (comme Windows XP) pour les applications peu.

Avec Azure RemoteApp, vos utilisateurs peuvent également utiliser leurs propres périphériques Android ou Apple et obtenir la même expérience qu’ils obtenus sur Windows (ou sur les téléphones Windows). Pour ce faire, l’hébergement de votre application Windows dans une collection d’ordinateurs virtuels Windows Azure - les utilisateurs peuvent y accéder depuis n’importe où ils disposent d’une connexion internet. 

Lisez la suite pour obtenir un exemple d’exactement comment procéder.

Dans cet article, nous allons partager l’accès à tous nos utilisateurs. Toutefois, vous pouvez utiliser n’importe quelle application. Tant que vous pouvez installer votre application sur un ordinateur Windows Server 2012 R2, vous pouvez la partager en suivant les étapes ci-dessous. Vous pouvez consulter les [exigences de l’application](remoteapp-appreqs.md) pour vous assurer que votre application fonctionnera.

Notez que dans la mesure où Access est une base de données, et nous souhaitons que cette base de données pour être utile, nous va faire quelques étapes supplémentaires pour permettre aux utilisateurs d’accéder au partage de données Access. Si votre application n’est pas une base de données ou si vous n’avez pas besoin les utilisateurs pour pouvoir accéder à un partage de fichier, vous pouvez ignorer les étapes de ce didacticiel

> [AZURE.NOTE] <a name="note"></a>Vous avez besoin d’un compte Azure pour compléter ce didacticiel :
> - Vous pouvez [Ouvrir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): vous obtenez des crédits vous permet d’essayer les services Azure payés et même après leur utilisation jusqu'à vous pouvez conserver le compte et utilisation libre des services Azure, tels que les sites Web. Votre carte de crédit ne sera jamais être débitée, sauf si vous modifiez vos paramètres explicitement et que vous demandez à percevoir.
> - Vous pouvez [activer des avantages pour les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): MSDN votre abonnement fournit les crédits chaque mois que vous pouvez utiliser pour des services Azure payés.


## <a name="create-a-collection-in-remoteapp"></a>Créer une collection dans RemoteApp

Commencez par créer une collection. La collection est utilisé comme un conteneur pour vos applications et les utilisateurs. Chaque collection est basée sur une image, vous pouvez créer votre propre ou utilisez celui fourni avec votre abonnement. Pour ce didacticiel, nous utilisons l’image d’évaluation Office 2013 - il contient l’application que nous souhaitons partager.

1. Dans le portail Azure, défiler vers le bas dans l’arborescence de navigation de gauche jusqu'à ce que vous voyiez RemoteApp. Ouvrir cette page.
2. Cliquez sur **créer une collection de RemoteApp**.
3. Cliquez sur **Création rapide** et entrez un nom pour la collection.
4. Sélectionnez la zone que vous souhaitez utiliser pour créer votre collection. Pour optimiser les performances, sélectionnez la zone qui est le plus proche géographiquement à l’emplacement où vos utilisateurs auront accès à l’application. Par exemple, dans ce didacticiel, les utilisateurs se situeront à Redmond, Washington. La région Azure le plus proche est **-Ouest des États-Unis**.
5. Sélectionnez le plan de facturation que vous souhaitez utiliser. Le plan de facturation base place 16 utilisateurs sur un ordinateur virtuel d’un grand Azure, alors que le plan de facturation standard a 10 utilisateurs sur un grand Azure VM. Un exemple général, le plan de base fonctionne bien pour les workflow de type d’entrée de données. Pour une application de productivité, comme Office, vous pouvez le plan standard.
6. Enfin, sélectionnez l’image Office 2013 Professionnel. Cette image contient les applications Office 2013. Juste un rappel - cette image convient uniquement pour les versions d’essai et les validations fonctionnelles. Vous « Impossible d’utiliser cette image dans une collection de production.
7. Maintenant, cliquez sur **créer un RemoteApp collection**.

![Créer une collection de nuage dans RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Cela démarre la création de votre collection, mais il peut prendre jusqu'à une heure.

Vous êtes maintenant prêt à ajouter les utilisateurs.

## <a name="share-the-app-with-users"></a>Partager l’application avec les utilisateurs

Une fois que votre collection a été créée avec succès, il est temps de publier l’accès aux utilisateurs et ajouter les utilisateurs qui doivent avoir accès à celui-ci.

Si vous avez quitté le nœud Azure RemoteApp lors de la création de la collection, commencez par effectuer votre moyen de revenir à partir de la page d’accueil Azure.

2. Cliquez sur la collection que vous avez créé précédemment pour accéder à des options supplémentaires et la configuration de la collection.
![Une nouvelle collection de nuage de RemoteApp](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. Sous l’onglet **publication** , cliquez sur **Publier** en bas de l’écran, puis cliquez sur **programmes du menu Démarrer de publier**.
![Publier un programme RemoteApp](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Sélectionnez les applications que vous souhaitez publier à partir de la liste. Dans notre cas, nous avons choisi d’accès. Cliquez sur **terminé**. Attendez que les applications terminer la publication.
![Publication Access dans RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Une fois que l’application a terminé la publication, tête sur l’onglet de **L’accès des utilisateurs** à ajouter tous les utilisateurs qui ont besoin d’accéder à vos applications. Entrez les noms d’utilisateur (adresse e-mail) pour vos utilisateurs et puis cliquez sur **Enregistrer**.

![Ajouter des utilisateurs à RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. Maintenant, il est temps pour indiquer à vos utilisateurs sur ces nouvelles applications et comment y accéder. Pour ce faire, envoyez à vos utilisateurs un e-mail les pointant vers l’URL de téléchargement du client Bureau à distance.
![URL de téléchargement du client de RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## <a name="configure-access-to-access"></a>Configurer l’accès à l’accès

Certaines applications ont besoin de configuration supplémentaire après leur déploiement par le biais de RemoteApp. En particulier, pour l’accès, nous allons créer un partage de fichiers sur Azure que n’importe quel utilisateur peut accéder. (Si vous ne souhaitez pas le faire, vous pouvez créer une [collection de hybride](remoteapp-create-hybrid-deployment.md) [au lieu de notre collection de nuage] qui permet à vos utilisateurs accéder aux fichiers et les informations sur votre réseau local.) Ensuite, nous avons besoin indiquer à nos utilisateurs de mapper un lecteur local sur leur ordinateur vers le système de fichiers Azure.

La première partie que vous en tant que l’administrateur de le faire. Ensuite, nous avons quelques étapes pour vos utilisateurs.

1. Commencez par la publication de l’interface de ligne de commande (cmd.exe). Dans l’onglet **publication** , sélectionnez **cmd**, puis cliquez sur **Publier > programme de publication à l’aide du chemin d’accès**.
2. Entrez le nom de l’application et le chemin d’accès. Dans notre cas, utilisez « Explorateur », ainsi que le nom de « % SYSTEMDRIVE%\windows\explorer.exe » comme le chemin d’accès.
![Publier le fichier cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Vous devez maintenant créer un [compte de stockage](../storage/storage-create-storage-account.md)d' Azure. Nous avons appelé le nôtre « accessstorage », de choisir un nom significatif pour vous. (Pour misquote Highlander, il peut y avoir qu’un seul « accessstorage ».) ![Compte de stockage Azure notre](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Revenez maintenant à votre tableau de bord afin d’obtenir le chemin d’accès à votre système de stockage (emplacement de point de terminaison). Vous allez utiliser cette commande dans un bit, assurez-vous donc de que copier quelque part.
![Le chemin d’accès du compte de stockage](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Ensuite, une fois le compte de stockage a été créé, vous devez la clé d’accès principal. Cliquez sur **Gérer les touches d’accès**et copiez la clé d’accès principal.
6. Maintenant, de définir le contexte du compte de stockage et de créer un nouveau partage de fichiers pour l’accès. Exécuter les applets de commande suivantes dans une fenêtre Windows PowerShell avec élévation de privilèges :

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    Pour notre part, il s’agit donc nous exécuter les applets de commande :

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


Maintenant, il est le tour de l’utilisateur. Tout d’abord, avoir à vos utilisateurs d’installer un [client de RemoteApp](remoteapp-clients.md). Ensuite, les utilisateurs doivent mapper un lecteur à partir de leur compte à ce partage de fichier Azure que vous avez créé et ajouter leur accès aux fichiers. Voici comment ils procèdent :

1. Dans le client de RemoteApp, accéder aux applications publiées. Démarrez le programme cmd.exe.
2. Exécutez la commande suivante pour mapper un lecteur à partir de votre ordinateur pour le partage de fichiers :

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    Si vous définissez le paramètre **/ persistent** sur yes, le lecteur mappé persiste entre les sessions.
1. Maintenant, lancez l’application Explorateur de fichiers à partir de RemoteApp. Copier des fichiers Access que vous souhaitez utiliser dans l’application partagée pour le partage de fichiers.
![Placer les accès aux fichiers dans un partage Azure](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Enfin, ouvrez Microsoft Access et puis ouvrez la base de données que vous venez de partager. Vous devriez voir vos données dans Access en cours d’exécution à partir du cloud.
![Une base de données réelle en cours d’exécution à partir du cloud](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Vous pouvez désormais utiliser Access sur un de vos périphériques - Assurez-vous que vous installez un client de RemoteApp.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous maîtrisez la création d’une collection, essayez de créer une [collection qui utilise Office 365](remoteapp-tutorial-o365anywhere.md). Ou vous pouvez créer une [collection de hybride ](remoteapp-create-hybrid-deployment.md)qui peut accéder à votre réseau local.

<!--Image references-->
 
