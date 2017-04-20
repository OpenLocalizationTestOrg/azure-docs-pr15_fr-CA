<properties
pageTitle="Utiliser les données d’une base de données de SQL Server sur site dans l’apprentissage automatique | Azure"
description="Utiliser des données à partir d’une base de données de SQL Server sur site pour effectuer des analytique avancée avec formation de Machine Azure."
services="machine-learning"
documentationCenter=""
authors="garyericson"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/16/2016"
ms.author="garye;krishnan"/>

# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Effectuer analytique avancée avec formation de Machine Azure à l’aide de données provenant d’une base de données de SQL Server sur site


Souvent les entreprises qui fonctionnent avec des données sur site souhaite tirer parti de l’échelle et de la réactivité du nuage pour leur apprentissage des charges de travail de l’ordinateur. Mais ils ne souhaitent pas perturber leurs processus métier actuels et les flux de travail en déplaçant leurs données en local dans le nuage. Azure Machine Learning prend désormais en charge la lecture de vos données à partir d’une base de données de SQL Server sur site et de formation puis et de notation d’un modèle avec ces données. Vous n’avez plus à copier manuellement et de synchroniser les données entre le nuage et de votre serveur local. Au lieu de cela, le module **d’Importation de données** dans Azure Machine Learning Studio peut désormais lire directement à partir de votre base de données de SQL Server sur site pour votre formation et évaluation des travaux. 

Cet article fournit une vue d’ensemble de la pénétration sur site données SQL server dans la formation de Machine Azure. Il suppose que vous êtes familiarisé avec les concepts de formation de Machine Azure comme des espaces de travail, des modules, des groupes de données, essais, *etc.*..

> [AZURE.NOTE] Cette fonctionnalité n’est pas disponible pour les espaces de travail libres. Pour plus d’informations sur les prix de l’apprentissage automatique et de niveaux, consultez [Tarification d’apprentissage Azure Machine](https://azure.microsoft.com/pricing/details/machine-learning/).

<!-- --> 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="install-the-microsoft-data-management-gateway"></a>Installation de la passerelle de gestion des données de Microsoft

Pour accéder à une base de données de SQL Server sur site de formation de Machine Azure vous devez télécharger et installer la passerelle de gestion des données de Microsoft. Lorsque vous configurez la connexion de la passerelle dans le Studio d’apprentissage Machine, vous aurez la possibilité de télécharger et d’installer la passerelle à l’aide de la boîte de dialogue **téléchargement et passerelle de données de Registre** décrite ci-dessous.

Vous pouvez également installer la passerelle de gestion des données à l’avance en téléchargeant et en exécutant le package d’installation MSI à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Choisissez la version la plus récente, sélectionnant 32 bits ou 64 bits en fonction de votre ordinateur. Le fichier MSI peut également servir à la mise à niveau d’une passerelle de gestion des données existantes à la version la plus récente, avec tous les paramètres sont conservés.

La passerelle a les conditions préalables suivantes :

- Les versions de système d’exploitation Windows pris en charge sont Windows 7, Windows 8/8.1, 10 de Windows, Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.
- La configuration recommandée pour l’ordinateur de la passerelle est au moins de 2 GHz, 4 cœurs, 8 Go de RAM et disque de 80 Go.
- Si l’ordinateur hôte en veille prolongée, la passerelle ne sera en mesure de répondre aux demandes de données. Par conséquent, configurer un plan d’alimentation sur l’ordinateur avant d’installer la passerelle. L’installation de la passerelle affiche un message si l’ordinateur est configuré pour la mise en veille prolongée.
- Dans la mesure où l’activité copie a lieu à une fréquence spécifique, l’utilisation des ressources (processeur, mémoire) sur la machine également respecte la même avec un pic et de temps d’inactivité. L’utilisation des ressources également tributaire de la quantité de données déplacées. Lorsque plusieurs tâches sont en cours vous allez observer l’utilisation des ressources de remonter pendant les heures de pointe. Lors de la configuration minimale ci-dessus est techniquement suffisante, vous souhaiterez disposent d’une configuration avec plus de ressources que la configuration minimale requise en fonction de votre charge spécifique pour le déplacement des données.

Vous devez envisager ce qui suit lors de la configuration et l’utilisation d’une passerelle de gestion des données :

- Vous pouvez installer qu’une seule instance de passerelle de gestion des données sur un seul ordinateur.
- Vous pouvez utiliser une passerelle unique pour plusieurs sources de données sur site.
- Vous pouvez connecter plusieurs passerelles sur des ordinateurs différents pour la même source de données sur site.
- Vous configurez une passerelle pour un seul espace de travail à la fois. Les passerelles ne peut être partagées entre les espaces de travail pour l’instant.
- Vous pouvez configurer plusieurs passerelles pour un espace de travail unique. Par exemple, vous souhaiterez utiliser une passerelle connecté à vos sources de données de test lors du développement et une passerelle de production lorsque vous êtes prêt à mettre.
- La passerelle n’a pas besoin d’être sur le même ordinateur que la source de données, mais rester le plus près de la source de données permet de réduire le temps de la passerelle pour se connecter à la source de données. Nous vous conseillons d’installer la passerelle sur un ordinateur qui est différent de celui qui héberge la source de données sur site afin que la source de données et de la passerelle ne sont en concurrence pour les ressources.
- Si vous disposez déjà d’une passerelle installée sur votre ordinateur servant de scénarios d’alimentation BI ou Azure Data Factory, installer une passerelle séparée pour l’apprentissage de Machine Azure sur un autre ordinateur. 

    > [AZURE.NOTE] Vous ne pouvez pas exécuter la passerelle de gestion des données et d’alimentation BI passerelle sur le même ordinateur.

- Vous devez utiliser la passerelle de gestion des données pour l’apprentissage de Machine Azure même si vous utilisez des ExpressRoute d’Azure pour d’autres données. Vous devez considérer votre source de données comme source de données sur site (qui se trouve derrière un pare-feu) même lorsque vous utilisez ExpressRoute et la passerelle de gestion des données pour établir la connectivité entre l’apprentissage automatique et la source de données. 

Vous trouverez des informations détaillées sur les conditions préalables d’installation, des étapes d’installation et des conseils de dépannage dans l’article, de [déplacer des données entre sources de locaux et cloud avec la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway), à partir de la section, [Considérations sur l’utilisation de la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Données d’entrée à partir de votre base de données de SQL Server sur site en formation de Machine Azure


Dans cette procédure pas à pas, vous configurer une passerelle de gestion des données dans un espace de travail formation de Machine Azure, configurer et puis lire les données à partir d’une base de données de SQL Server sur site.

> [AZURE.TIP] Avant de commencer, désactivez votre bloqueur de `studio.azureml.net`. Si vous utilisez le navigateur Google Chrome, téléchargez et installez un de plusieurs plug-ins disponibles sur Google Chrome WebStore, [Cliquez sur une fois App Extension](https://chrome.google.com/webstore/search/clickonce?_category=extensions).

### <a name="step-1-create-a-gateway"></a>Étape 1 : Création d’une passerelle

La première étape consiste à créer et à configurer la passerelle pour accéder à votre base de données SQL sur site.

1. Connexion à [Azure Machine Learning Studio](https://studio.azureml.net/Home/) et sélectionnez l’espace de travail que vous souhaitez utiliser.

2. Cliquez sur la lame de **paramètres** sur la gauche, puis cliquez sur l’onglet **Passerelles de données** en haut.

3. Cliquez sur **Nouvelle passerelle de données** en bas de l’écran.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)

4. Dans la boîte de dialogue **nouvelle passerelle de données** , entrez le **Nom de la passerelle** et éventuellement ajouter une **Description**. Cliquez sur la flèche dans l’angle inférieur droit pour passer à l’étape suivante de la configuration.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)

5. Dans le téléchargement et le Registre de données passerelle boîte de dialogue Copier la clé d’inscription de passerelle dans le Presse-papiers.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)

6. <span id="note-1" class="anchor"></span>Si vous n’avez pas encore téléchargé et installé la passerelle de gestion des données de Microsoft, puis cliquez sur **Télécharger la passerelle de gestion des données**. Vous accédez à du Microsoft Download Center où vous pouvez sélectionner la version de la passerelle que vous devez le télécharger et l’installer. Vous trouverez des informations détaillées sur les conditions préalables d’installation, des étapes d’installation et des conseils de dépannage dans les sections de début de l’article de [déplacer des données entre sources de locaux et cloud avec la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

7. Après l’installation de la passerelle, le Gestionnaire de Configuration de passerelle de gestion des données s’ouvre et la boîte de dialogue **Enregistrer la passerelle** s’affiche. Collez la **Clé d’enregistrement de passerelle** que vous avez copié dans le Presse-papiers, cliquez sur **Enregistrer**.

8. Si vous disposez déjà d’une passerelle installée, exécutez le Gestionnaire de Configuration de passerelle de gestion des données et cliquez sur **Modifier la clé**, collez la  **Clé d’enregistrement de passerelle** que vous avez copié dans le Presse-papiers, cliquez sur **OK**.

9. Lorsque l’installation est terminée, la boîte de dialogue **Enregistrer la passerelle** pour le Gestionnaire de Configuration de passerelle Microsoft données gestion s’affiche. Collez la clé d’inscription de passerelle que vous avez copié dans le Presse-papiers ci-dessus et cliquez sur **Enregistrer**.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)

10. La configuration de la passerelle est terminée lorsque les valeurs suivantes sont définies sous l’onglet **accueil** dans le Gestionnaire de Configuration de passerelle Microsoft données gestion :

    - **Nom de la passerelle** et le **nom de l’Instance** sont définies sur le nom de la passerelle.

    - **L’enregistrement** a **enregistrés**.

    - **Statut** est défini sur **démarré**.

    - La barre d’état du bas affiche **connecté au Service de Cloud de passerelle de gestion des données** avec une coche verte.

     ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

     Studio d’apprentissage Machine Azure également est mis à jour lors de l’enregistrement a réussi.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-registered.png)

11. Dans la boîte de dialogue **télécharger et enregistrer la passerelle de données** , cliquez sur la case à cocher pour terminer l’installation. La page **paramètres** affiche l’état de la passerelle « En ligne ». Dans le volet de droite, vous trouverez état et autres informations utiles.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-status.png)

12. Dans le commutateur de gestionnaire de Configuration de passerelle de gestion de données de Microsoft, à l’onglet **certificats** . Le certificat spécifié sous cet onglet est utilisé pour chiffrer/déchiffrer les informations d’identification pour le magasin de données local que vous spécifiez dans le portail. C’est le certificat par défaut généré. Microsoft recommande cette modification à votre propre certificat que sauvegarder dans votre système de gestion de certificats. Cliquez sur **Modifier** pour utiliser à la place de votre propre certificat.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-certificate.png)

13. (facultatif) Si vous souhaitez activer l’inscription commentée afin de résoudre les problèmes liés à la passerelle, dans le Gestionnaire de Configuration des passerelle données gestion Microsoft basculer vers l’onglet **Diagnostics** et activez l’option **Activer la journalisation détaillée aux fins de dépannage** . Vous trouverez les informations de journalisation dans l’Observateur d’événements Windows dans les **Applications et les journaux des Services**  - &gt; **Passerelle de gestion des données de** nœud. Vous pouvez également utiliser l’onglet **Diagnostics** pour tester la connexion à une source de données en local à l’aide de la passerelle.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-verbose-logging.png)

Cela termine la passerelle configurer le processus d’apprentissage d’ordinateur Azure.
Vous êtes maintenant prêt à utiliser vos données sur site.

Vous pouvez créer et configurer plusieurs passerelles dans Studio pour chaque espace de travail. Par exemple, peuvent avoir une passerelle que vous souhaitez vous connecter à vos sources de données de test pendant le développement et une autre passerelle pour vos sources de données de production. Formation de Machine Azure vous donne la possibilité de configurer plusieurs passerelles en fonction de votre environnement d’entreprise. Actuellement, vous ne pouvez pas partager une passerelle entre les espaces de travail et qu’une seule passerelle peut être installée sur un seul ordinateur. Pour plus d’informations sur l’installation de la passerelle, consultez [Considérations sur l’utilisation de la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway) dans l’article, de [déplacer des données entre sources de locaux et cloud avec la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Étape 2 : Utiliser la passerelle pour lire des données à partir d’une source de données sur site

Après avoir configuré la passerelle, vous pouvez ajouter un module **d’Importation de données** pour une expérience dont les données de la base de données de SQL Server sur site une entrée.

1.  Dans un Studio de formation de Machine, cliquez sur l’onglet **d’expériences** , cliquez sur le **+ de nouveau** dans le coin inférieur gauche et sélectionnez **Essai à blanc** (ou sélectionnez parmi plusieurs expériences sur des échantillons disponibles).

2.  Rechercher et faites glisser le module **d’Importation de données** pour la toile de l’expérimentation.

3.  Au-dessous de la zone de dessin, cliquez sur **Enregistrer sous** . Entrez « Azure Machine formation sur site SQL Server Tutorial » pour le nom de l’expérimentation, sélectionnez l’espace de travail et cliquez sur la coche **OK** .

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\experiment-save-as.png)

4.  Cliquez sur le module **d’Importation de données** pour la sélectionner, puis dans le volet de **Propriétés** à droite de la zone de dessin, sélectionnez « De base de données SQL locale » dans la liste déroulante de **source de données** .

5.  Sélectionnez la **passerelle de données** vous avez installé et enregistré. Vous pouvez configurer une autre passerelle en sélectionnant « (ajouter une nouvelle passerelle de données...) ».

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-select-on-premises-data-source.png)

6.  Entrez le du **nom du serveur de base de données** SQL et le **nom de la base de données**, ainsi que de la SQL **requête de base de données** que vous souhaitez exécuter.

7.  Cliquez sur les **valeurs d’entrée** sous **nom d’utilisateur et le mot de passe** et entrez vos informations d’identification de base de données. Vous pouvez utiliser l’authentification intégrée de Windows ou l’authentification SQL Server en fonction de la configuration de votre du SQL Server local.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\database-credentials.png)
    
    Le message « valeurs requises » devient « jeu de valeurs » avec une coche verte. Vous devez simplement entrer les informations d’identification une seule fois, à moins que le mot de passe ou des informations de base de données change. Formation de Machine Azure utilise le certificat que vous avez fournies lors de l’installation de la passerelle pour crypter les informations d’identification dans le nuage. Azure ne stocke jamais d’informations d’identification locales sans cryptage.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-properties-entered.png)

8.  Cliquez sur **exécuter** pour exécuter l’essai.

Une fois la fin de l’expérimentation en cours d’exécution vous permet d’afficher les données que vous avez importé à partir de la base de données en cliquant sur le port de sortie du module **Importation de données** et en sélectionnant les **visualiser**.

Une fois que vous terminé de développer votre expérience, vous pouvez déployer et mettre votre modèle. À l’aide du Service d’exécution de lot, les données à partir de la base de données locale SQL Server est configuré dans le module **d’Importation de données** sont lues, mais utilisées pour l’attribution de scores. Si vous pouvez utiliser le Service de réponse de demande pour les données locales d’attribution de scores, Microsoft recommande d’utiliser le [complément Excel](machine-learning-excel-add-in-for-web-services.md) au lieu de cela. Actuellement, écrire à un de SQL Server sur site la base de données à **Exporter les données** n'est pas pris en charge dans vos expériences ou les services web publiés.

