<properties
    pageTitle="Comment faire pour utiliser le plug-in d’esclave Azure avec une intégration continue Hudson | Microsoft Azure"
    description="Décrit comment utiliser le plug-in d’esclave Azure avec une intégration continue Hudson."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Comment faire pour utiliser le plug-in d’esclave Azure avec une intégration continue Hudson

L’esclave Azure Hudson plug-in vous permet de provisionner les nœuds esclave sur Azure lors de l’exécution distribuée génère.

## <a name="install-the-azure-slave-plug-in"></a>Installer le plug-in Azure esclave

1. Dans le tableau de bord Hudson, cliquez sur **Gérer les Hudson**.

1. Dans la page **Gérer les Hudson** , cliquez sur **Gérer les plug-ins**.

1. Cliquez sur l’onglet **disponible** .

1. Cliquez sur **Rechercher** et tapez **Azure** pour limiter la liste aux plug-ins appropriés.

    Si vous optez pour faire défiler la liste des plug-ins disponibles, vous trouverez l’esclave Azure plug-in dans la section **gestion du Cluster et distribués générer** dans l’onglet **autres** .

1. Activez la case à cocher du plug-in de **Azure esclave**.

1. Cliquez sur **installer**.

1. Redémarrez Hudson.

Maintenant que le plug-in est installé, la procédure suivante serait pour configurer le plug-in dans votre profil d’abonnement Azure et pour créer un modèle qui sera utilisé lors de la création de la machine virtuelle pour le nœud de l’esclave.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurer le plug-in Azure esclave avec votre profil d’abonnement

Un profil d’abonnement, également appelé paramètres de publication, est un fichier XML qui contient les informations d’identification sécurisées et des informations supplémentaires, que vous devez travailler avec Azure dans votre environnement de développement. Pour configurer l’esclave Azure plug-in, vous devez :

* Votre id d’abonnement
* Un certificat de gestion de votre abonnement

Vous la trouverez dans votre [profil d’abonnement]. Vous trouverez ci-dessous un exemple d’un profil d’abonnement.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Une fois que vous avez votre profil d’abonnement, procédez comme suit pour configurer l’esclave Azure plug-in.

1. Dans le tableau de bord Hudson, cliquez sur **Gérer les Hudson**.

1. Cliquez sur **configurer le système**.

1. Faites défiler la page jusqu'à la section de **nuage** .

1. Cliquez sur **Ajouter nouveau cloud > Microsoft Azure**.

    ![Ajouter nouveau nuage][add new cloud]

    Cela affiche les champs où vous devez entrer les détails de votre abonnement.

    ![configurer le profil][configure profile]

1. Copier le certificat de gestion et id d’abonnement à partir de votre profil d’abonnement et de les coller dans les champs appropriés.

    Lors de la copie du certificat d’id et de gestion des abonnement, **** n’incluez pas les guillemets qui entourent les valeurs.

1. Cliquez sur **configuration de la vérification**.

1. Lorsque la configuration est vérifiée avec succès, cliquez sur **Enregistrer**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Définition d’un modèle de machine virtuelle pour l’esclave Azure plug-in

Un modèle de machine virtuelle définit les paramètres que du plug-in permet de créer un noeud esclave sur Azure. Dans les étapes suivantes nous allons créer le modèle pour une VM Ubuntu.

1. Dans le tableau de bord Hudson, cliquez sur **Gérer les Hudson**.

1. Cliquez sur **configurer le système**.

1. Faites défiler la page jusqu'à la section de **nuage** .

1. Dans la section de **nuage** , trouver le **Modèle d’ordinateur virtuel Azure ajouter** et cliquez sur le bouton **Ajouter** .

    ![Ajouter un modèle de machine virtuelle][add vm template]

1. Spécifiez un nom de service de cloud dans le champ **nom** . Si le nom spécifié fait référence à un service cloud existant, l’ordinateur virtuel sera déployé dans ce service. Dans le cas contraire, Azure va créer un nouveau.

1. Dans le champ **Description** , entrez le texte qui décrit le modèle que vous créez. Cette information est uniquement à des fins documentaires et n’est pas utilisée dans la mise en service d’un ordinateur virtuel.

1. Dans le champ **d’étiquettes** , entrez **linux**. Cette étiquette est utilisée pour identifier le modèle que vous créez et est ensuite utilisée pour référencer le modèle lors de la création d’un travail Hudson.

1. Sélectionnez une région où l’ordinateur virtuel sera créé.

1. Sélectionnez la taille appropriée de la machine virtuelle.

1. Spécifiez un compte de stockage dans lequel l’ordinateur virtuel sera créé. Assurez-vous qu’il est dans la même région que vous allez utiliser le service en nuage. Si vous souhaitez que les nouveaux systèmes de stockage doivent être créés, vous pouvez laisser ce champ vide.

1. Temps de rétention spécifie le nombre de minutes avant la suppression d’un esclave inactif Hudson. Laissez la valeur par défaut de 60.

1. **L’utilisation**, sélectionnez la condition appropriée lorsque ce nœud esclave sera utilisé. Pour l’instant, sélectionnez **Utilisez ce nœud autant que possible**.

    À ce stade, votre formulaire devrait ressembler un peu à cela :

    ![configuration de modèle][template config]

1. Dans la **famille de l’Image ou de code** , vous devez spécifier quelle image système va être installé sur votre machine virtuelle. Vous pouvez sélectionner dans une liste de familles de l’image ou spécifier une image personnalisée.

    Si vous souhaitez sélectionner dans une liste de familles de l’image, entrez le premier caractère (sensible à la casse) du nom de famille de l’image. Par exemple, taper **U** s’affiche une liste des familles de serveur d’Ubuntu. Une fois que vous sélectionnez dans la liste, Jenkins utilise la dernière version de cette image système à partir de cette famille lors de la mise en service de votre machine virtuelle.

    ![Liste de familles de système d’exploitation][OS family list]

    Si vous avez une image personnalisée que vous souhaitez utiliser à la place, entrez le nom de cette image personnalisée. Nom de l’image personnalisée ne figurent pas dans une liste donc vous devez vous assurer que le nom est correctement saisi.    

    Pour ce didacticiel, entrez **U** pour afficher une liste d’images d’Ubuntu et sélectionnez **Ubuntu Server 14.04 LTS**.

1. **Méthode de lancement**, sélectionnez **SSH**.

1. Copiez le script ci-dessous et collez le **script d’initialisation** de champ.

        # Install Java

        sudo apt-get -y update

        sudo apt-get install -y openjdk-7-jdk

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y openjdk-7-jdk

        # Install git

        sudo apt-get install -y git

        #Install ant

        sudo apt-get install -y ant

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y ant

    Le **script d’initialisation** sera exécuté après la création de la machine virtuelle. Dans cet exemple, le script installe Java, git et ant.

1. Dans les champs **nom d’utilisateur** et le **mot de passe** , entrez vos valeurs par défaut pour le compte d’administrateur qui va être créé sur votre ordinateur virtuel.

1. Cliquez sur **Vérifier le modèle** pour vérifier si les paramètres spécifiés sont valides.

1. Cliquez sur **Enregistrer**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Créer un travail Hudson qui s’exécute sur un noeud esclave sur Azure

Dans cette section, vous allez créer une tâche Hudson qui s’exécute sur un noeud esclave sur Azure.

1. Dans le tableau de bord Hudson, cliquez sur **Nouvelle tâche**.

1. Entrez un nom pour la tâche que vous créez.

1. Pour le type de tâche, sélectionnez **Générer un projet de logiciel de forme libre**.

1. Cliquez sur **OK**.

1. Dans la page configuration de la tâche, sélectionnez **restreindre où ce projet peut être exécuté**.

1. Sélectionnez le **nœud et l’étiquette de menu** et **linux** (ce que nous avons spécifié cette étiquette lors de la création du modèle d’ordinateur virtuel dans la section précédente).

1. Dans la section **créer** , cliquez sur **étape de génération ajouter** et sélectionnez **Execute shell**.

1. Modifier le script suivant, remplacez **{votre nom de compte github}** **{votre nom de projet}**et **{votre répertoire de projet}** avec les valeurs appropriées et collez le script modifié dans la zone de texte qui s’affiche.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e {your project directory} ]; then

            cd {your project directory}

            git pull origin master

        else

            git clone https://github.com/{your github account name}/{your project name}.git

        fi

        # change directory to project

        cd $currentDir/{your project directory}

        #Execute build task

        ant

1. Cliquez sur **Enregistrer**.

1. Dans le tableau de bord Hudson, rechercher la tâche que vous venez de créer et cliquez sur l’icône de **planification d’une build** .

Hudson va créer un noeud esclave en utilisant le modèle créé dans la section précédente, puis exécuter le script que vous avez spécifié dans l’étape de génération pour cette tâche.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure].

<!-- URL List -->

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[profil d’abonnement]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

