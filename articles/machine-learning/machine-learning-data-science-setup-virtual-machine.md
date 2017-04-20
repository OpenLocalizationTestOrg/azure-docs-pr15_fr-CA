<properties
    pageTitle="Configurer un ordinateur virtuel en tant que bloc-notes de IPython serveur | Microsoft Azure"
    description="Définir d’un Azure ordinateur virtuel pour une utilisation dans un environnement informatique de données avec le serveur de IPython pour analytique avancée."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurer un ordinateur virtuel Azure comme serveur de IPython portable pour analytique avancée

Cette rubrique montre comment mettre en service et de configurer une machine virtuelle Azure analytique avancée qui peut être utilisée dans le cadre d’un environnement informatique de données. La machine virtuelle Windows est configurée avec la prise en charge des outils tels que comme IPython bloc-notes, Explorateur de stockage Azure, AzCopy, ainsi que les autres utilitaires qui sont utiles pour les projets d’analytique avancée. Explorateur de stockage Azure et AzCopy, par exemple, fournissent des moyens pratiques pour transférer des données vers un stockage blob Azure à partir de votre ordinateur local ou pour le télécharger sur votre ordinateur local depuis le stockage blob.

## <a name="create-vm"></a>Étape 1 : Créer un objectif général Azure VM

Si vous disposez déjà d’une machine virtuelle Azure et souhaitez simplement configurer un serveur IPython bloc-notes sur celui-ci, vous pouvez ignorer cette étape et passez à le [étape 2 : ajouter un point de terminaison pour les PC portables de IPython à un ordinateur virtuel existant](#add-endpoint).

Avant de commencer le processus de création d’une machine virtuelle sur Azure, vous devez déterminer la taille de l’ordinateur qui est nécessaire pour traiter les données de leur projet. Machines de petite taille ont moins de mémoire et moins de coeurs de processeur que les machines plus grandes, mais ils sont également moins coûteux. Pour obtenir une liste des types d’ordinateurs et des prix, consultez la page <a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">Tarification des Machines virtuelles</a>

1. Connectez-vous au <a href="https://manage.windowsazure.com" target="_blank">Portail classique d’Azure</a>et cliquez sur **Nouveau** dans le coin inférieur gauche. Une fenêtre s’affiche. Sélectionnez **Calculer** -> **MACHINE virtuelle** -> **De la galerie**.

    ![Créer l’espace de travail][24]

2. Choisissez l’une des images suivantes :

    * Windows Server 2012 R2 Datacenter
    * Expérience Windows Server Essentials (Windows Server 2012 R2)

    Puis, cliquez sur la flèche pointant vers la droite en bas à droite pour atteindre la page suivante de la configuration.

    ![Créer l’espace de travail][25]

3. Entrez un nom pour l’ordinateur virtuel que vous souhaitez créer, sélectionnez la taille de l’ordinateur (par défaut : A3) en fonction de la taille des données va au processus de l’ordinateur et le plus puissant vous souhaitez que l’ordinateur (taille de la mémoire et le nombre de cœurs de calcul), entrez un nom d’utilisateur et le mot de passe pour l’ordinateur. Puis, cliquez sur la flèche pointant vers la droite pour atteindre la page suivante de la configuration.

    ![Créer l’espace de travail][26]

4. Sélectionnez le **Réseau de groupe/virtuel / l’affinité de la région** qui contient le **Compte de stockage** que vous envisagez d’utiliser pour cet ordinateur virtuel, puis sélectionnez ce compte de stockage. Ajouter un point de terminaison en bas dans le champ **points de terminaison** en entrant le nom du point de terminaison (« IPython » ici). Vous pouvez choisir n’importe quelle chaîne comme **nom** du point de fin et tout nombre entier entre 0 et 65 536 qui est **disponible** comme **PORT PUBLIC**. Le **PORT de privé** doit être **9999**. Les utilisateurs doivent **éviter** d’utiliser des ports publics qui ont déjà été affectés pour les services internet. <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">Ports pour les Services Internet</a> fournit une liste de ports qui ont été affectés et doit être évité.

    ![Créer l’espace de travail][27]

5. Cliquez sur la case à cocher pour démarrer l’ordinateur virtuel que le processus de fourniture.

    ![Créer l’espace de travail][28]


Il peut prendre 15-25 minutes pour terminer la machine virtuelle que le processus de fourniture. Une fois que l’ordinateur virtuel a été créé, le statut de cet ordinateur doit afficher comme **en cours d’exécution**.

![Créer l’espace de travail][29]

## <a name="add-endpoint"></a>Étape 2 : Ajouter un point de terminaison pour les PC portables de IPython à un ordinateur virtuel existant

Si vous avez créé l’ordinateur virtuel en suivant les instructions de l’étape 1, puis le point de terminaison pour le IPython portable a déjà été ajouté et cette étape peut être omise.

Si la machine virtuelle existe déjà et que vous devez ajouter un point de terminaison pour ordinateur portable IPython que vous installerez à l’étape 3 ci-dessous, la première connexion au portail classique d’Azure, sélectionnez l’ordinateur virtuel et ajouter le point de terminaison de serveur de l’ordinateur portable de IPython. L’illustration suivante contient une capture d’écran du portail après que le point de terminaison pour le portable de IPython a été ajouté à une machine virtuelle Windows.

![Créer l’espace de travail][17]

## <a name="run-commands"></a>Étape 3 : Installer IPython bloc-notes et autres outils de prise en charge

Après avoir créé l’ordinateur virtuel, utiliser le protocole RDP (Remote Desktop) pour vous connecter à la machine virtuelle Windows. Pour obtenir des instructions, voir [Comment faire pour ouvrir une session sur un ordinateur virtuel exécutant Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md). Ouvrez l' **invite de commande** (**pas la fenêtre de commande Powershell**) en tant qu' **administrateur** et exécutez la commande suivante.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Une fois l’installation terminée, le serveur de IPython bloc-notes est lancé automatiquement dans le *C:\\les utilisateurs\\\<nom d’utilisateur\>\\Documents\\IPython portables* directory.

Lorsque vous y êtes invité, entrez un mot de passe pour l’ordinateur portable de IPython et le mot de passe de l’administrateur de l’ordinateur. Cela permet au portable IPython exécuter en tant que service sur l’ordinateur.

## <a name="access"></a>Étape 4 : Accès IPython portables à partir d’un navigateur web
Pour accéder au serveur IPython bloc-notes, ouvrez un site web navigateur et l’entrée *nom DNS de l’ordinateur https://&#60;virtual > : & #60 ; numéro de port publics >* dans la zone de texte URL. Ici, le *& #60 ; numéro de port publics >* doit être le numéro de port que vous avez spécifié lorsque le point de terminaison IPython bloc-notes a été ajoutée.

Le *& #60 ; nom DNS de machine virtuelle >* , visitez le portail d’Azure classique. Une fois la connexion au portail classique, cliquez sur **ordinateurs virtuels**, sélectionnez l’ordinateur que vous avez créé, puis sélectionnez **tableau de bord**, le nom DNS apparaît comme suit :

![Créer l’espace de travail][19]

Vous rencontrez un message d’avertissement indiquant qu' _il y a un problème avec le certificat de sécurité de ce site Web_ (Internet Explorer) ou _votre connexion n’est pas privée_ (Chrome), comme indiqué dans les figures suivantes. Cliquez sur **Continuer pour ce site Web (non recommandé)** (Internet Explorer) ou sur **Avancé** , puis * *continuer à & #60 ;* Nom DNS*> (potentiellement dangereuses) ** (Chrome) pour continuer. Puis entrez le mot de passe que vous avez spécifié précédemment pour accéder à la IPython de portables.

**Internet Explorer :**
![créer l’espace de travail][20]

**Chrome :**
![créer l’espace de travail][21]

Après que vous être connecté à l’ordinateur portable de IPython, un répertoire *DataScienceSamples* apparaîtra sur le navigateur. Ce répertoire contient des exemples d’agendas IPython qui sont partagées par Microsoft pour aider les utilisateurs à effectuer des tâches de données scientifiques. Ces exemples d’agendas IPython sont extraits du [**référentiel de Github**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) aux machines virtuelles pendant le serveur IPython bloc-notes paramétrer des processus. Microsoft tient à jour et mises à jour de ce référentiel fréquemment. Les utilisateurs peuvent visiter le référentiel Github pour obtenir les exemples d’agendas IPython plus récemment mis à jour.
![Créer l’espace de travail][18]

## <a name="upload"></a>Étape 5 : Télécharger un bloc-notes IPython existant à partir d’un ordinateur local vers le serveur du carnet d’IPython

IPython portables offrent un moyen simple pour les utilisateurs à télécharger un bloc-notes IPython existant sur l’ordinateur local sur le serveur IPython bloc-notes sur les machines virtuelles. Une fois que les utilisateurs ouvrir une session sur l’ordinateur portable de IPython dans un navigateur web, cliquez sur dans le **répertoire** qui seront téléchargé dans le bloc-notes de IPython. Ensuite, sélectionnez un fichier de .ipynb de IPython portable à télécharger à partir de l’ordinateur local dans l' **Explorateur de fichiers**et faites glisser-déplacer dans le répertoire IPython bloc-notes sur le navigateur web. Cliquez sur le bouton **Télécharger** pour télécharger le fichier .ipynb sur le serveur IPython bloc-notes. Autres utilisateurs peuvent alors commencer à l’utiliser dans à partir de leurs navigateurs web.

![Créer l’espace de travail][22]

![Créer l’espace de travail][23]


##<a name="shutdown"></a>Arrêt et désalloue la machine virtuelle pas en utilisation

Les Machines virtuelles Azure sont tarifés comme **payez uniquement ce que vous utilisez**. Pour vous assurer que vous ne sont pas en cours facturés lorsque vous n’utilisez ne pas votre ordinateur virtuel, il doit se trouver dans l’état **arrêté (Deallocated)** pas en utilisation.

> [AZURE.NOTE] Si vous arrêtez l’ordinateur virtuel à partir de l’intérieur de la machine virtuelle (à l’aide d’options d’alimentation de Windows), la machine virtuelle est arrêtée, mais reste allouée. Pour garantir que vous ne continuez pas à facturer, toujours arrêter les machines virtuelles à partir du [Portail classique d’Azure](http://manage.windowsazure.com/). Vous pouvez également arrêter la machine virtuelle par le biais de Powershell en appelant **ShutdownRoleOperation** avec « PostShutdownAction » égal à « StoppedDeallocated ».

Pour arrêter et supprimer de l’ordinateur virtuel :

1. Connectez-vous au [Portail classique d’Azure](http://manage.windowsazure.com/) à l’aide de votre compte.  

2. Sélectionnez les **ordinateurs virtuels** à partir de la barre de navigation de gauche.

3. Dans la liste des ordinateurs virtuels, cliquez sur le nom de votre machine virtuelle, puis accédez à la page de **tableau de bord** .

4. En bas de la page, cliquez sur **Arrêter**.

![Arrêt de la machine virtuelle][15]

L’ordinateur virtuel seront libéré mais pas supprimé. Vous pouvez redémarrer votre machine virtuelle à tout moment à partir du portail classique d’Azure.

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>Votre Azure VM est prêt à l’emploi : quel est l’avenir ?

Votre ordinateur virtuel est prêt à utiliser dans vos exercices de science de données. La machine virtuelle est également prêt à être utilisé comme serveur d’IPython portable d’exploration et de traitement de données et d’autres tâches en conjonction avec un apprentissage de Machine Azure et le processus de Science de données Team.

Les étapes dans le processus de Science de données équipe sont mappés dans le [Chemin d’accès de formation](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et peuvent inclure des étapes de déplacement des données vers HDInsight, de traitent et d’aperçu en vue de l’apprentissage à partir des données avec Azure Machine d’apprentissage.


[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png
