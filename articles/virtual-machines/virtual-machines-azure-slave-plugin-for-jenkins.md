<properties
    pageTitle="Comment faire pour utiliser le plug-in d’esclave Azure avec une intégration continue Jenkins | Microsoft Azure"
    description="Décrit comment utiliser le plug-in d’esclave Azure avec une intégration continue Jenkins."
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

# <a name="how-to-use-the-azure-slave-plug-in-with-jenkins-continuous-integration"></a>Comment faire pour utiliser le plug-in d’esclave Azure avec une intégration continue Jenkins

Vous pouvez utiliser l’esclave Azure plug-in pour Jenkins à disposition des nœuds esclave sur Azure lors de l’exécution distribuée génère.

## <a name="install-the-azure-slave-plug-in"></a>Installer le plug-in d’esclave Azure

1. Dans le tableau de bord Jenkins, cliquez sur **Gérer les Jenkins**.

1. Dans la page **Gérer les Jenkins** , cliquez sur **Gérer les plug-ins**.

1. Cliquez sur l’onglet **disponible** .

1. Dans le champ filtre au-dessus de la liste des plug-ins disponibles, tapez **Azure** pour limiter la liste aux plug-ins appropriés.

    Si vous optez pour faire défiler la liste des plug-ins disponibles, vous trouverez l’esclave Azure plug-in dans la section **gestion du Cluster et distribués générer** .

1. Activez la case à cocher de **Plug-in d’Azure esclave** .

1. Cliquez sur **installer sans redémarrage** ou **Télécharger maintenant et installer après le redémarrage**.

Maintenant que le plug-in est installé, les étapes suivantes consistent à configurer le plug-in dans votre profil d’abonnement Azure et pour créer un modèle qui sera utilisé lors de la création de la machine virtuelle pour le nœud de l’esclave.


## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurer le plug-in d’esclave Azure avec votre profil d’abonnement

Un profil d’abonnement, également appelé paramètres de publication, est un fichier XML qui contient les informations d’identification sécurisées et des informations supplémentaires, que vous devez travailler avec Azure dans votre environnement de développement. Pour configurer l’esclave Azure plug-in, vous devez :

* Votre id d’abonnement
* Un certificat de gestion de votre abonnement

Vous la trouverez dans votre [profil d’abonnement]. Vous trouverez ci-dessous un exemple d’un profil d’abonnement.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID value>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Une fois que votre profil d’abonnement, procédez comme suit pour configurer le plug-in d’esclave Azure :

1. Dans le tableau de bord Jenkins, cliquez sur **Gérer les Jenkins**.

1. Cliquez sur **configurer le système**.

1. Faites défiler la page jusqu'à la section de **nuage** .

1. Cliquez sur **Ajouter nouveau cloud > Microsoft Azure**.

    ![section de nuage][cloud section]

    Cela affiche les champs où vous devez entrer les détails de votre abonnement.

    ![configuration d’un abonnement][subscription configuration]

1. Copier l’id d’abonnement et des valeurs de certificat de gestion de votre abonnement, de profil et les coller dans les champs appropriés.

    Lors de la copie du certificat d’id et de gestion des abonnement, n’incluez pas les guillemets qui entourent les valeurs.

1. Cliquez sur **vérifier la Configuration**.

1. Lorsque la configuration est vérifiée soit correct, cliquez sur **Enregistrer**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Définir un modèle d’ordinateur virtuel pour le serveur esclave Azure plug-in

Un modèle de machine virtuelle définit les paramètres du plug-in permet de créer un noeud esclave sur Azure. Dans les étapes suivantes, nous allons créer un modèle pour une machine virtuelle d’Ubuntu.

1. Dans le tableau de bord Jenkins, cliquez sur **Gérer les Jenkins**.

1. Cliquez sur **configurer le système**.

1. Faites défiler la page jusqu'à la section de **nuage** .

1. Dans la section de **nuage** , trouver le **Modèle d’ordinateur virtuel Azure ajouter**, puis cliquez sur **Ajouter**.

    ![Ajouter un modèle de machine virtuelle][add vm template]

    Cela affiche les champs où vous entrez les détails sur le modèle que vous créez.

    ![configuration générale vide][blank general configuration]

1. Dans la zone **nom** , entrez un nom de service cloud Azure. Si le nom que vous avez entré fait référence à un service cloud existant, l’ordinateur virtuel sera déployé dans ce service. Dans le cas contraire, Azure va créer un nouveau.

1. Dans la zone **Description** , entrez le texte qui décrit le modèle que vous créez. Ceci est uniquement pour vos enregistrements et n’est pas utilisé dans une machine virtuelle.

1. La zone **étiquettes** permettant d’identifier le modèle que vous créez et est ensuite utilisée pour référencer le modèle lors de la création d’un travail Jenkins. Dans notre cas, entrez **linux** dans cette zone.

1. Dans la **zone** de liste, cliquez sur la zone où l’ordinateur virtuel sera créé.

1. Dans la liste **Taille de la Machine virtuelle** , cliquez sur la taille appropriée.

1. Dans la zone **Nom du compte de stockage** , spécifiez un compte de stockage dans lequel l’ordinateur virtuel sera créé. Assurez-vous qu’il est dans la même région que vous allez utiliser le service en nuage. Si vous souhaitez que les nouveaux systèmes de stockage doivent être créés, vous pouvez laisser cette zone vide.

1. Temps de rétention spécifie le nombre de minutes avant la suppression d’un esclave inactif Jenkins. Laissez la valeur par défaut de 60. Vous pouvez également choisir d’arrêter l’esclave au lieu de la supprimer lorsqu’il est inactif. Pour ce faire, activez la case à cocher **Arrêt uniquement (ne pas supprimer) après le temps de rétention** .

1. Dans la liste **utilisation** , cliquez sur la condition appropriée lorsque ce nœud esclave sera utilisé. Pour l’instant, cliquez sur **Utilisez ce nœud autant que possible**.

    À ce stade, votre formulaire doit ressembler un peu similaire à ceci :

    ![configuration de modèle général de point de contrôle][checkpoint general template config]

    L’étape suivante consiste à fournir des détails sur l’image de système d’exploitation que vous souhaitez que votre esclave à créer dans.

1. Dans la zone **Id ou famille de l’Image** , vous devez spécifier quelle image système sera installé sur votre machine virtuelle. Vous pouvez sélectionner dans une liste de familles de l’image ou spécifier une image personnalisée.

    Si vous souhaitez sélectionner dans une liste de familles de l’image, entrez le premier caractère (sensible à la casse) du nom de famille de l’image. Par exemple, taper **U** s’affiche une liste des familles de serveur d’Ubuntu. Une fois que vous sélectionnez dans la liste, Jenkins utilise la dernière version de cette image système à partir de cette famille lors de la mise en service de votre machine virtuelle.

    ![Exemple de liste d’images de système d’exploitation][OS Image list sample]

    Si vous avez une image personnalisée que vous souhaitez utiliser à la place, entrez le nom de cette image personnalisée. Nom de l’image personnalisée ne figurent pas dans une liste, donc vous devez vous assurer que le nom est correctement saisi.

    Pour ce didacticiel, **U** pour afficher une liste d’images d’Ubuntu, puis tapez **LTS de 14.04 serveur Ubuntu**.

1. Dans la liste **Mode de lancement** , cliquez sur **SSH**.

1. Copiez le script ci-dessous et collez-le dans la zone de **Script d’initialisation** .

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

    Le script d’initialisation sera exécuté après que la machine virtuelle est créée. Dans cet exemple, le script installe Java, Git et ant.

1. Dans les zones **nom d’utilisateur** et le **mot de passe** , entrez vos valeurs par défaut pour le compte administrateur qui sera créé sur votre ordinateur virtuel.

1. Cliquez sur **Vérifier le modèle** pour vérifier si les paramètres spécifiés sont valides.

1. Cliquez sur **Enregistrer**.


## <a name="create-a-jenkins-job-that-runs-on-a-slave-node-on-azure"></a>Créer un travail Jenkins qui s’exécute sur un noeud esclave sur Azure

Dans cette section, vous allez créer une tâche Jenkins qui s’exécute sur un noeud esclave sur Azure. Vous devez disposer de votre propre projet sur GitHub à suivre.

1. Dans le tableau de bord Jenkins, cliquez sur **Nouvel élément**.

1. Entrez un nom pour la tâche que vous créez.

1. Pour le type de projet, cliquez sur **projet Freestyle**.

1. Cliquez sur **Ok**.

1. Dans la page configuration de la tâche, sélectionnez l’option **limiter où ce projet peut être exécuté**.

1. Dans la zone **Expression de l’étiquette** , entrez **linux**. Dans la section précédente, nous avons créé un modèle d’esclave que nous avons appelé **linux**, qui est ce que nous spécifions ici.

1. Dans la section **créer** , cliquez sur **étape de génération ajouter** et sélectionnez **Execute shell**.

1. Modifier le script suivant, en remplaçant **(votre nom de compte GitHub)**, **(nom de votre projet)**et **(le répertoire du projet)** par les valeurs appropriées et collez le script modifié dans la zone de texte qui s’affiche.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e (your project directory) ]; then

            cd (your project directory)

            git pull origin master

        else

            git clone https://github.com/(your GitHub account name)/(your project name).git

        fi

        # change directory to project

        cd $currentDir/(your project directory)

        #Execute build task

        ant

1. Cliquez sur **Enregistrer**.

1. Dans le tableau de bord Jenkins, pointez sur la tâche que vous venez de créer et cliquez sur la flèche déroulante pour afficher les options de la tâche.

1. Cliquez sur **créer maintenant**.

Jenkins va créer un noeud esclave en utilisant le modèle créé dans la section précédente, puis exécuter le script que vous avez spécifié dans l’étape de génération pour cette tâche.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure].

<!-- URL List -->

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[profil d’abonnement]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png