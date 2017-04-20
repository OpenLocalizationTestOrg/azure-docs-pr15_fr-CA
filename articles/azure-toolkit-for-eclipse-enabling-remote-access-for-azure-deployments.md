<properties
    pageTitle="L’activation de l’accès distant pour les déploiements Azure dans Éclipse"
    description="Découvrez comment activer l’accès distant pour les déploiements Azure à l’aide de la Shared Computer Toolkit Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# <a name="enabling-remote-access-for-azure-deployments-in-eclipse"></a>L’activation de l’accès distant pour les déploiements Azure dans Éclipse

Pour vous aider à résoudre les problèmes liés à vos déploiements, vous pouvez activer et utiliser l’accès distant pour se connecter à la machine virtuelle qui héberge votre déploiement. La fonctionnalité d’accès à distance s’appuie sur le protocole RDP (Remote Desktop). Vous pouvez configurer l’accès distant pour votre déploiement une fois que vous l’avez publié vers Azure, ou si vous utilisez Eclipse avec un système d’exploitation de Windows, vous pouvez configurer l’accès distant avant de le publier vers Azure. Notez que vous aurez besoin d’un client de bureau à distance qui est compatible avec votre système d’exploitation pour vous connecter à la machine virtuelle de votre déploiement dans Azure.

## <a name="how-to-enable-remote-access-before-you-deploy-to-azure"></a>Comment faire pour activer l’accès distant avant de le déployer vers Azure

> [AZURE.NOTE] Pour activer l’accès distant avant de déployer votre application dans Azure, vous devez exécuter Eclipse sous Windows.

L’image suivante montre les propriétés **d’Accès distant** boîte de dialogue est utilisée pour activer l’accès distant.

![][ic719494]

Il existe deux manières d’afficher la boîte de dialogue de propriétés **d’Accès distant** :

* Cliquez sur le lien **Options avancées** dans la section de **l’Accès distant** de la boîte de dialogue **publier sur Azure** .
* Ouvrez la boîte de dialogue **Propriétés** de votre projet Azure.

Lorsque vous créez un nouveau projet de déploiement d’Azure, le projet n’aura pas accès à distance est activé par défaut. Toutefois, vous pouvez facilement activer l’accès distant en spécifiant le nom d’utilisateur et le mot de passe dans la boîte de dialogue **publier sur Azure** . Le mot de passe d’accès à distance est crypté à l’aide de certificats X.509. Si vous n’utilisez pas fournir votre propre certificat, le cryptage s’appuie sur un certificat auto-signé, livré avec le plug-in Eclipse Azure. Ce certificat auto-signé est dans le dossier de **certificat** de votre projet d’Azure, stockée sous la forme d’un fichier de certificat public (SampleRemoteAccessPublic.cer) et sous la forme d’un fichier de certificat d’échange dynamique de données (PFX, Personal Information Exchange) (SampleRemoteAccessPrivate.pfx). Cette dernière contient la clé privée pour le certificat, et il a un mot de passe, **mot de passe1**. Toutefois, étant donné que ce mot de passe est la connaissance du public, le certificat par défaut convient uniquement pour l’apprentissage, pas pour un déploiement de production. Ainsi, autres qu’à des fins pédagogiques, lorsque vous souhaitez activé des sessions à distance pour vos déploiements, vous devez cliquer sur le lien **Avancé** dans la boîte de dialogue **publier sur Azure** pour spécifier votre propre certificat. Notez que vous devrez télécharger la version PFX du certificat à votre service hébergé au sein du portail de gestion Azure, afin que Azure peut décrypter le mot de passe.

La suite de ce didacticiel vous montre comment activer l’accès distant pour un projet de déploiement d’Azure qui a été créé avec l’accès distant est désactivé. Pour les besoins de ce didacticiel, nous allons créer un nouveau certificat auto-signé et son fichier .pfx aura un mot de passe de votre choix. Vous avez également la possibilité d’utiliser un certificat émis par une autorité de certification.

## <a name="how-to-enable-remote-access-after-you-have-deployed-to-azure"></a>Comment faire pour activer l’accès distant après avoir déployé vers Azure

Pour activer l’accès distant une fois que vous avez déployés sur Azure, procédez comme suit :

1. Ouvrez une session sur le portail de gestion Azure à l’aide de votre compte Azure
1. Dans la liste des **Services en nuage**, sélectionnez votre service cloud déployé
1. Dans la page web du service cloud, cliquez sur le lien **configurer**
1. En bas de la page de configuration, cliquez sur le lien **à distance**
1. Lorsque la boîte de dialogue contextuelle s’affiche :
    * Spécifiez le rôle pour lequel vous souhaitez activer l’accès distant
    * Activez la case à cocher **Activer le Bureau à distance**
    * Spécifiez un nom d’utilisateur et le mot de passe à utiliser pour l’accès distant
    * Sélectionnez le certificat à utiliser
1. Cliquez sur **OK** 

Vous verrez un message vous indiquant que votre modification de la configuration est en cours, ce qui peut prendre quelques minutes pour terminer. À l’issue de la modification de configuration, suivez les étapes de la section **à ouvrir une session à distance** plus loin dans cet article.
    
## <a name="how-to-enable-remote-access-in-your-package"></a>Comment faire pour activer l’accès distant dans votre package.

1. Dans le volet de l’Explorateur de projet de Eclipse, droit sur votre projet Azure et cliquez sur **Propriétés**.

1. Dans la boîte de dialogue **Propriétés** , développez **Azure** dans le volet gauche, puis cliquez sur **Accès à distance**.

1. Dans la boîte de dialogue **Accès à distance** , assurez-vous de **Qu'activer tous les rôles accepter les connexions Bureau à distance avec ces informations d’identification d’ouverture de session** est activée.

1. Spécifiez un nom d’utilisateur pour la connexion Bureau à distance.

1. Spécifiez et confirmez le mot de passe de l’utilisateur. Les valeurs de nom et de mot de passe utilisateur définies dans cette boîte de dialogue seront utilisés lorsque vous effectuez une connexion Bureau à distance. (Notez qu’il s’agit d’un mot de passe distinct de votre mot de passe PFX.)

1. Spécifiez la date d’expiration du compte d’utilisateur.

1. Cliquez sur **Nouveau** pour créer un nouveau certificat auto-signé. (Sinon, vous pouvez sélectionner un certificat à partir de votre espace de travail ou système de fichiers par le biais de **l’espace de travail** ou de **système de fichiers** boutons, respectivement, mais pour les besoins de ce didacticiel, nous allons créer un nouveau certificat.)

    * Dans la boîte de dialogue **Nouveau certificat** , spécifiez et confirmez le mot de passe que vous utiliserez pour votre fichier PFX.

    * Accepter la valeur fournie pour le **Nom (CN)**, ou utiliser un nom personnalisé.

    * Spécifiez le chemin d’accès et nom de fichier où sera enregistré le nouveau certificat, sous forme de .cer. Pour cette étape et l’étape suivante, vous pouvez utiliser le dossier de **certificat** de votre projet Azure, mais vous êtes libre de choisir un autre emplacement. Pour les besoins de ce didacticiel, nous allons utiliser **c:\mycert\mycert.cer**. (Créez le dossier **c:\mycert** avant de poursuivre, ou utiliser un dossier existant le cas échéant).

    * Spécifiez le chemin d’accès et nom de fichier où seront enregistrés le nouveau certificat et sa clé privée, sous forme de .pfx. Pour les besoins de ce didacticiel, nous allons utiliser **c:\mycert\mycert.pfx**. La boîte de dialogue **Nouveau certificat** doit ressembler à ce qui suit (mettre à jour les chemins d’accès de dossier si vous n’utilisez pas **c:\mycert**) :

        ![][ic712275]

    * Cliquez sur **OK** pour fermer la boîte de dialogue **Nouveau certificat** .

1. La boîte de dialogue **Accès à distance** doit ressembler à ce qui suit :</p>

    ![][ic719495]

1. Cliquez sur **OK** pour fermer la boîte de dialogue **Accès à distance** .
    
Régénérez votre application, avec la version définie pour le déploiement vers le cloud.

## <a name="to-log-in-remotely"></a>Pour ouvrir une session à distance

Une fois votre instance de rôle est prêt, vous pouvez à distance vous connecter à la machine virtuelle qui héberge votre application.

* Si Éclipse sur Windows et que vous sélectionnez l’option **Démarrer le Bureau à distance sur déployer** lors de votre déploiement d’Azure, s’affiche sur un écran d’ouverture de session de connexion Bureau à distance au démarrage de votre déploiement. Lorsque vous êtes invité à entrer le nom d’utilisateur et le mot de passe, entrez les valeurs que vous avez spécifié pour l’utilisateur distant et sera en mesure de se connecter.

* Il est un autre moyen de se connecter à distance via le <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Portail de gestion Azure</a>:

    * Dans la vue **Des Services de Cloud** du portail Azure Management, cliquez sur votre service cloud et cliquez sur **Instances**, cliquez sur une instance spécifique, puis cliquez sur le bouton **se connecter** . Le bouton **se connecter** s’affiche comme suit dans la barre de commandes :

        ![][ic659273]

    * Après avoir cliqué sur le bouton **connexion** , vous devez ouvrir un fichier RDP. Ouvrez le fichier et suivez les invites. (Vous pourriez également enregistrer ce fichier sur votre ordinateur local et puis exécutez le fichier en double-cliquant dessus pour se connecter à distance à votre ordinateur virtuel sans devoir d’abord atteindre le portail de gestion)

    * Lorsque vous êtes invité à entrer le nom d’utilisateur et le mot de passe, entrez les valeurs que vous avez spécifié pour l’utilisateur distant et sera en mesure de se connecter.

> [AZURE.NOTE] Si vous êtes sur un système d’exploitation non Windows, vous devez utiliser un client de bureau à distance qui est compatible avec votre système d’exploitation et suivez les étapes pour configurer le client avec les paramètres dans le fichier RDP que vous avez téléchargé.

## <a name="see-also"></a>Voir aussi

[Azure Shared Computer Toolkit pour Éclipse][]

[Création d’une Application du monde Hello pour Azure dans Éclipse][]

[L’installation de la Shared Computer Toolkit Azure pour Éclipse][] 

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application du monde Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png
