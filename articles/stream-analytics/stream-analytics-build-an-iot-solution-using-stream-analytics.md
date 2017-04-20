<properties
    pageTitle="Créer une solution IoT à l’aide de flux de données Analytique | Microsoft Azure"
    description="Didacticiel de mise en route de la solution de flux de données Analytique IoT d’un scénario de péage"
    keywords="solution IOT, fonctions de fenêtre"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Créer une solution IoT à l’aide de flux de données Analytique

## <a name="introduction"></a>Introduction

Dans ce didacticiel, vous allez apprendre à Azure flux Analytique permet d’obtenir des informations en temps réel à partir de vos données. Les développeurs peuvent combiner facilement les flux de données, tels que click-flux de données, les journaux et les événements générés par le périphérique, avec les enregistrements historiques ou des données de référence pour tirer des leçons de l’entreprise. Sous la forme d’un service de calcul de flux en temps réel, entièrement géré qui est hébergé dans Microsoft Azure, Azure flux Analytique fournit la résilience intégrée, faible temps de latence et d’évolutivité pour obtenir opérationnel en quelques minutes.

À la fin de ce didacticiel, vous serez en mesure de :

-   Vous devez vous familiariser avec le portail Azure flux Analytique.
-   Configurer et déployer un travail en continu.
-   Expliquer les problèmes réels et de les résoudre à l’aide du langage de requête Analytique de flux de données.
-   Développer la diffusion en continu des solutions pour vos clients à l’aide de flux Analytique en toute confiance.
-   Utilisez la surveillance et la journalisation d’expérience pour résoudre les problèmes.

## <a name="prerequisites"></a>Conditions préalables

Vous devez les conditions préalables suivantes pour compléter ce didacticiel :

-   La dernière version de [PowerShell d’Azure](../powershell-install-configure.md)
-   Visual Studio 2015 ou la libre de [Communauté de Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   Un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Privilèges d’administrateur sur l’ordinateur
-   Téléchargement de [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) à partir du centre de téléchargement Microsoft
-   Facultatif : Code Source pour le Générateur d’événements TollApp dans [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>Introduction de scénario : « Hello, numéro payant ! »


Un poste de péage est un phénomène courant. Vous les rencontrez sur celle, les ponts et les tunnels sont nombreux dans le monde entier. Chaque poste de péage a plusieurs stands de péage. Vous arrêtez à stands manuelles, pour le paiement de péage à une surveillance. À des stands automatisées, un capteur sur chaque stand analyse une carte RFID qui est placée sur le pare-brise du véhicule lorsque vous passez de la cabine de péage. Il est facile de visualiser le passage des véhicules par le biais de ces stations de péage sous la forme d’un flux d’événements sur laquelle des opérations intéressantes peuvent être effectuées.

![Image de voitures sur les stands de péage](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Données entrantes

Ce didacticiel utilise deux flux de données. Capteurs installés dans l’entrée et de sortie des postes numéro payant de produisent le premier flux. Le second flux est un groupe de données de recherche statiques qui a des données d’immatriculation du véhicule.

### <a name="entry-data-stream"></a>Flux d’entrée de données

Le flux d’entrée de données contient des informations sur les voitures lorsqu’ils entrent des stations de péage.


| TollID | EntryTime               | LicensePlate | État | Rendre   | Modèle   | VehicleType | VehicleWeight | Numéro payant | Balise       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 2014-09-10 12:01:00.000 | JNB 7001     | NY    | Honda  | CRB     | 1           | 0             | 7    |           |
| 1      | 2014-09-10 12:02:00.000 | YXZ 1001     | NY    | Toyota | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 2014-09-10 12:02:00.000 | ABC 1004     | CT    | Ford   | Taurus  | 1           | 0             | 5    | 456789123 |
| 2      | 2014-09-10 12:03:00.000 | XYZ 1003     | CT    | Toyota | Pétales | 1           | 0             | 4    |           |
| 1      | 2014-09-10 12:03:00.000 | BNJ 1007     | NY    | Honda  | CRB     | 1           | 0             | 5    | 789123456 |
| 2      | 2014-09-10 12:05:00.000 | CDE 1007     | NEW JERSEY    | Toyota | 4 x 4     | 1           | 0             | 6    | 321987654 |


Voici une brève description des colonnes :

| Colonne | Description |
|--------------|--------------------------------------------------------------------|
| TollID       | L’ID de stand de numéro qui identifie de manière unique une cabine de péage            |
| EntryTime    | La date et l’heure de l’entrée du véhicule sur le stand de péage en UTC |
| LicensePlate | Le numéro de la plaque d’immatriculation du véhicule                            |
| État        | Un état des États-Unis                                           |
| Rendre         | Le fabricant de l’automobile                                 |
| Modèle        | Numéro de modèle de l’automobile                                 |
| VehicleType  | 1 pour les véhicules de tourisme ou 2 pour véhicules utilitaires       |
| WeightType   | Poids du véhicule en tonnes ; 0 pour les véhicules de tourisme                   |
| Numéro payant         | La valeur de numéro payant en dollars US                                              |
| Balise          | La balise e sur l’automobile qui automatise le paiement ; vide dans le cas où le paiement a été effectué manuellement |


### <a name="exit-data-stream"></a>Flux de données de sortie

Le flux de sortie de données contient des informations sur les voitures, laissant la station numéro payant.


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 2014-09-10T12:03:00.0000000Z | JNB 7001         |
| 1          | 2014-09-10T12:03:00.0000000Z | YXZ 1001         |
| 3          | 2014-09-10T12:04:00.0000000Z | ABC 1004         |
| 2          | 2014-09-10T12:07:00.0000000Z | XYZ 1003         |
| 1          | 2014-09-10T12:08:00.0000000Z | BNJ 1007         |
| 2          | 2014-09-10T12:07:00.0000000Z | CDE 1007         |

Voici une brève description des colonnes :


| Colonne | Description |
|--------------|-----------------------------------------------------------------|
| TollID       | L’ID de stand de numéro qui identifie de manière unique une cabine de péage         |
| ExitTime     | La date et l’heure de sortie du véhicule de cabine de péage en UTC |
| LicensePlate | Le numéro de la plaque d’immatriculation du véhicule                         |

### <a name="commercial-vehicle-registration-data"></a>Données d’inscription de VEHICULE commercial

Le didacticiel utilise un instantané statique d’une base de données d’inscription de VEHICULE commercial.


| LicensePlate | Attributs de RegistrationId | Expiré |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| BAC 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

Voici une brève description des colonnes :


| Colonne | Description |
|--------------|-----------------------------------------------------------------|
| LicensePlate | Le numéro de la plaque d’immatriculation du véhicule                         |
| Attributs de RegistrationId     | ID de l’enregistrement du véhicule |
| Expiré | L’état d’immatriculation du véhicule : 0 si l’immatriculation des véhicules est active, 1 si l’enregistrement a expiré.                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurer l’environnement pour l’Analytique des flux de données Azure

Pour terminer ce didacticiel, vous avez besoin d’un abonnement Microsoft Azure. Microsoft propose une version d’évaluation gratuite de services Microsoft Azure.

Si vous ne disposez pas d’un compte Azure, vous pouvez [demande une version d’évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Pour vous inscrire à une évaluation gratuite, vous avez besoin d’un appareil mobile qui peut recevoir des messages texte et une carte de crédit valide.

Veillez à suivre les étapes décrites dans la section « Nettoyer votre compte Azure » à la fin de cet article afin que vous pouvez utiliser au mieux votre 200 $ libre crédit Azure.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Configurer les ressources Azure requises pour le didacticiel

Ce didacticiel requiert deux concentrateurs d’événement pour recevoir les flux de données *d’entrée* et de *sortie* . Une base de données SQL Azure génère les résultats des tâches de flux de données Analytique. Stockage Azure stocke les données de référence à propos des enregistrements du véhicule.

Vous pouvez utiliser le script Setup.ps1 dans le dossier TollApp sur GitHub pour créer toutes les ressources requises. Pour des raisons de temps, nous vous conseillons d’exécuter il. Si vous souhaitez en savoir plus sur la configuration de ces ressources dans le portail Azure, consultez l’annexe « Configuration de ressources didacticiels dans Azure portal ».

Téléchargez et enregistrez les fichiers et le dossier de prise en charge [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) .

Ouvrir un **Microsoft Azure PowerShell** fenêtre _en tant qu’administrateur_. Si vous n’avez pas encore de PowerShell d’Azure, suivez les instructions dans [l’installation et la configuration d’Azure PowerShell](../powershell-install-configure.md) pour installer.

Étant donné que Windows bloque automatiquement les fichiers .exe, .dll et .ps1, vous devez définir la stratégie d’exécution avant d’exécuter le script. Assurez-vous que la fenêtre PowerShell d’Azure s’exécute _en tant qu’administrateur_. Exécutez **Set-ExecutionPolicy unrestricted**. Lorsque vous y êtes invité, tapez **O**.

![Capture d’écran de « Set-ExecutionPolicy unrestricted » en cours d’exécution dans la fenêtre PowerShell d’Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Exécutez **Get-ExecutionPolicy** pour vous assurer que la commande a fonctionné.

![Capture d’écran de « Get-ExecutionPolicy » en cours d’exécution dans la fenêtre PowerShell d’Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Accédez au répertoire contenant les scripts et les applications de générateur.

![Capture d’écran de « cd .\TollApp\TollApp » en cours d’exécution dans la fenêtre PowerShell d’Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Type de **.\\ Setup.ps1** pour configurer votre compte Azure, créer et configurer toutes les ressources et commencer à générer des événements. Le script sélectionne au hasard une région pour créer vos ressources. Pour spécifier explicitement une région, vous pouvez passer le **-emplacement** paramètre comme dans l’exemple suivant :

**. \\Setup.ps1-emplacement « US Central »**

![Capture d’écran de la page de connexion Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Le script s’ouvre à la page de **Connexion** pour Microsoft Azure. Entrez vos informations d’identification du compte.

> [AZURE.NOTE] Si votre compte a accès à plusieurs abonnements, vous devrez entrer le nom d’abonnement que vous souhaitez utiliser pour le didacticiel.

Le script peut prendre plusieurs minutes pour s’exécuter. Une fois qu’il a terminé, la sortie doit ressembler à la capture d’écran suivante.

![Capture d’écran de la sortie du script dans la fenêtre PowerShell d’Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Vous verrez également une autre fenêtre est semblable à la capture d’écran suivante. Cette application envoie des événements à des concentrateurs d’événement Azure, qui est requis pour exécuter le didacticiel. Par conséquent, ne pas arrêter l’application ou fermer cette fenêtre jusqu'à ce que vous avez terminé le didacticiel.

![Capture d’écran de « Envoi de données événement concentrateur »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Vous permettre de voir toutes les ressources créées dans Azure portal maintenant. Accédez à <https://manage.windowsazure.com>et vous connecter avec vos informations d’identification du compte.

### <a name="azure-event-hubs"></a>Événement Azure concentrateurs

Cliquez sur le **BUS DE SERVICE** sur le côté gauche du portail Azure pour voir les concentrateurs d’événements créés par le script dans la section précédente.

![Bus des services](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Vous verrez tous les espaces de noms disponibles dans votre abonnement. Cliquez sur celui qui commence par *tolldata* (tolldata4637388511 dans notre exemple). Cliquez sur l’onglet **Événement concentrateurs** .

![Onglet de concentrateurs d’événement dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Vous verrez deux événements *entrée* nommées et les concentrateurs *Quitter* créé dans cet espace de noms.

![Capture d’écran de concentrateurs d’événement « entrée » et « exit » dans Azure portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Conteneur de stockage Azure

1. Cliquez sur le **stockage** sur le côté gauche du portail Azure pour afficher le conteneur de stockage Azure qui est utilisé dans le didacticiel.

    ![Élément de menu de stockage](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. Cliquez sur celui qui commencent par *tolldata* (tolldata4637388511 dans notre exemple). Cliquez sur l’onglet **conteneurs** pour afficher le conteneur créé.

    ![Onglet conteneurs dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. Cliquez sur le conteneur **tolldata** pour afficher le fichier téléchargé JSON qui contient des données de l’enregistrement de véhicule.

    ![Capture d’écran du fichier registration.json dans le conteneur](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Base de données SQL Azure

1. Cliquez sur **Bases de données SQL** sur le côté gauche du portail Azure pour afficher la base de données SQL qui sera utilisé dans le didacticiel.

    ![Bases de données SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. Cliquez sur **tolldatadb**.

    ![Capture d’écran de la base de données SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. Copier le nom de serveur sans le numéro de port (*nom_serveur*. database.windows.net, par exemple).

## <a name="connect-to-the-database-from-visual-studio"></a>Se connecter à la base de données à partir de Visual Studio

Utilisez Visual Studio pour accéder aux résultats de la requête dans la base de données de sortie.

Connectez-vous à la base de données SQL (destination) à partir de Visual Studio :

1. Ouvrez Visual Studio, puis cliquez sur **Outils** > **se connecter à la base de données**.

2. Si vous êtes invité, cliquez sur **Microsoft SQL Server** comme source de données.

    ![Boîte de dialogue Source de données de modification](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. Dans le champ **nom du serveur** , collez le nom que vous avez copié dans la section précédente à partir du portail Azure (c'est-à-dire, *NomServeur*. database.windows.net).

4. Cliquez sur **utiliser l’authentification SQL Server**.

5. Entrez **tolladmin** dans le champ **nom d’utilisateur** et **123toll !** dans le champ **mot de passe** .

6. Cliquez sur **Sélectionner ou entrer un nom de base de données**et sélectionnez **TollDataDB** la base de données.

    ![Ajouter la boîte de dialogue Connexion](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. Cliquez sur **OK**.

8. Ouvrez l’Explorateur de serveurs.

    ![Explorateur de serveurs](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. Voir quatre tables dans la base de données TollDataDB.

    ![Tables de la base de données TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Générateur d’événements : exemple de projet TollApp

Le script PowerShell démarre automatiquement envoyer des événements à l’aide de l’application d’exemple de TollApp. Vous n’avez pas besoin d’effectuer des étapes supplémentaires.

Toutefois, si vous êtes intéressé par les détails d’implémentation, vous trouverez le code source de l’application TollApp dans GitHub [Exemples/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Capture d’écran de l’exemple de code affichée dans Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Créer une tâche de flux de données Analytique

1. Dans le portail Azure, ouvrir le flux de données Analytique et cliquez sur **Nouveau** dans le coin inférieur gauche de la page pour créer un nouveau travail analytique.

    ![Bouton Nouveau](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. Cliquez sur **créer rapide**. Permet de sélectionner la même région où vos autres ressources sont créés par le script.

3. Pour le paramètre de **Compte de stockage de surveillance régionales** , sélectionnez **Créer un nouveau compte de stockage** et lui donner un nom unique. Analytique de flux Azure utilise ce compte pour stocker des informations d’analyse pour vos futurs travaux.

4. Cliquez sur **Créer un flux de travail ANALYTICS** au bas de la page.

    ![Créez l’option de travail Analytique de flux](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Définition des sources d’entrée

1. Cliquez sur la tâche analytique créé dans le portail.

    ![Capture d’écran du travail analytique dans le portail](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. Cliquez sur l’onglet **entrées** pour définir la source de données.

    ![L’onglet entrées](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. Cliquez sur **Ajouter une entrée**.

    ![Ajouter une option de saisie](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. Sur la première page, cliquez sur **flux de données** .

    ![L’option de flux de données](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. Sur la deuxième page de l’Assistant, cliquez sur **Événement concentrateur** .

    ![L’option de concentrateur de l’événement](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. Saisissez **EntryStream** comme **ALIAS d’entrée**.

7. Cliquez sur le menu déroulant **Concentrateur d’événements** et sélectionnez celle qui commence par « TollData » (par exemple, TollData9518658221).

8. Sélectionnez **Saisie** le nom de concentrateur d’événements et **tous les** en tant que nom de stratégie concentrateur de l’événement.

    Vos paramètres ressemble à :

    ![Paramètres de concentrateur d’événements](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. Sur la page suivante, sélectionnez **JSON** pour le **FORMAT de sérialisation d’événement** et **UTF8** de **codage**.

    ![Paramètres de sérialisation](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. Cliquez sur **OK** au bas de la page pour terminer l’Assistant.

    ![Entrée de capture d’écran de EntryStream dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    Maintenant que vous avez créé le flux d’entrée, vous suivrez la même procédure pour créer le flux de sortie. Sur la troisième page de l’Assistant, veillez à entrer des valeurs en tant que sur la capture d’écran suivante.

    ![Paramètres pour le flux de sortie](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    Vous avez défini deux flux d’entrée :

    ![Flux d’entrée définis dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    Ensuite, vous ajouterez d’entrée de données de référence pour le fichier blob qui contient les données d’inscription de voiture.

11. Cliquez sur **Ajouter une entrée**, puis cliquez sur **Données de référence**.

    ![« Ajouter une entrée » des options avec les données de référence sélectionné](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. Sur la page suivante, sélectionnez le compte de stockage qui commence par **tolldata**. Le nom du conteneur doit être **tolldata**et le nom de l’objet blob sous **Motif de chemin d’accès** doit être **registration.json**. Ce nom de fichier respecte la casse et doit être en minuscules.

    ![Paramètres de stockage de blog](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. Sélectionnez les valeurs comme indiqué dans la capture d’écran suivante sur la page suivante, puis cliquez sur **OK** pour terminer l’Assistant.

    ![Sélection de JSON pour « format de sérialisation même » et UTF8 pour « Codage »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Désormais, toutes les entrées sont définies.

![Capture d’écran de trois entrées définies](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Définir la sortie

1. Cliquez sur l’onglet **sortie** , puis sur **Ajouter une sortie un**.

    ![L’onglet sortie et l’option « Ajouter une sortie »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. Cliquez sur **base de données SQL**.

3. Sélectionnez le nom du serveur qui a été utilisé dans la section « Se connecter à base de données à partir de Visual Studio » de l’article. Le nom de la base de données est **TollDataDB**.

4. Entrez **tolladmin** dans le champ **nom d’utilisateur** , **123toll !** dans le champ **mot de passe** et **TollDataRefJoin** dans le champ de la **TABLE** .

    ![Paramètres de la base de données SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Azure requête analytique de flux

L’onglet **requête** contient une requête SQL qui transforme les données entrantes.

![Une requête ajoutée à l’onglet requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

Ce didacticiel tente de répondre à plusieurs questions commerciales liées à l’appel de données et les constructions de requêtes Analytique de flux qui peuvent être utilisés dans Azure flux Analytique à fournir une réponse pertinente.

Avant de commencer votre première tâche flux Analytique, examinons quelques scénarios et la syntaxe de la requête.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introduction au langage de requête Analytique de flux de données Azure
-----------------------------------------------------

Supposons que vous deviez compter le nombre de véhicules qui permet d’entrer une cabine de péage. Dans la mesure où il s’agit d’un flux continu d’événements, vous devez définir une « période de temps ». Nous allons modifier la question pour être « combien de véhicules enter une cabine de péage toutes les trois minutes ? ». Il est communément appelé le nombre qui s’écroulent.

Examinons la requête Analytique de flux Azure qui répond à cette question :

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Comme vous pouvez le voir, Azure flux Analytique utilise un langage de requête comme SQL et ajoute quelques extensions pour spécifier l’heure des aspects de la requête.

Pour plus de détails, en savoir plus sur les constructions de [Gestion du temps](https://msdn.microsoft.com/library/azure/mt582045.aspx) et de [fenêtrage](https://msdn.microsoft.com/library/azure/dn835019.aspx) utilisées dans la requête à partir de MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Test des requêtes Analytique de flux de données Azure

Maintenant que vous avez écrit votre première requête Analytique de flux d’Azure, il est temps de la tester à l’aide des fichiers de données qui se trouve dans le dossier TollApp dans le chemin suivant :

**.. \\TollApp\\TollApp\\données**

Ce dossier contient les fichiers suivants :

- Entry.JSON
- Exit.JSON
- Registration.JSON

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Question 1 : Nombre de véhicules entrant une cabine de péage

1. Ouvrir le portail Azure et accédez à votre travail Azure flux Analytique créé. Cliquez sur l’onglet **requête** et collez la requête à partir de la section précédente.

    ![Requête collé dans l’onglet requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. Pour valider cette requête sur les exemples de données, cliquez sur le bouton **Test** . Dans la boîte de dialogue qui s’ouvre, accédez à Entry.json, un fichier qui contient des exemples de données à partir du flux d’événements **EntryTime** .

    ![Capture d’écran du fichier Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. Vérifiez que la sortie de la requête est comme prévu :

    ![Résultats de l’essai](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Question 2 : Rapport la durée totale de chaque véhicule à passer par l’intermédiaire de la cabine de péage

La durée moyenne nécessaire pour une voiture à passer par le numéro payant vous aident à évaluer l’efficacité du processus et la satisfaction du client.

Pour trouver le temps total, vous devez joindre le flux EntryTime avec le flux ExitTime. Vous allez joindre les flux de données dans les colonnes TollId et LicencePlate. L’opérateur **JOIN** nécessite de spécifier suffisamment temporelle qui décrit la différence de temps acceptable entre les événements joints. Vous utiliserez la fonction **DATEDIFF** pour spécifier que les événements doivent être pas plus de 15 minutes à partir de l’autre. Vous allez également appliquer la fonction **DATEDIFF** pour quitter et les heures d’entrée pour calculer le temps réel qui passe d’une voiture dans la station d’appel payant. Notez la différence de l’utilisation de **DATEDIFF** lorsqu’il est utilisé dans une instruction **SELECT** plutôt que dans une condition de **jointure** .

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Pour tester cette requête, mise à jour de la requête sous l’onglet **requête** de votre travail :

    ![Requête mise à jour dans l’onglet requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. Cliquez sur **Test** , puis spécifiez des exemples de fichiers d’entrée de EntryTime et ExitTime.

    ![Capture d’écran de fichiers d’entrée sélectionnés](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. Activez la case à cocher pour tester la requête et afficher les résultats :

    ![Résultat du test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Question 3 : Signaler tous les véhicules avec enregistrement arrivé à expiration

Azure Analytique de flux permet de joindre avec des flux de données temporelles des instantanés statiques des données. Pour illustrer cette fonctionnalité, utilisez l’exemple de question suivante.

Si un véhicule utilitaire est enregistré avec la société numéro payant, il peut passer par l’intermédiaire de la cabine de péage sans arrêt en cours du contrôle. Vous allez utiliser la table de choix d’immatriculation du véhicule utilitaire pour identifier tous les véhicules qui sont arrivés à expiration des enregistrements.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Pour tester une requête à l’aide de données de référence, vous devez définir une source en entrée pour les données de référence, vous avez déjà fait.

1. Pour tester cette requête, collez la requête dans l’onglet **requête** , cliquez sur **Test**et spécifier les deux sources d’entrée :

    ![Capture d’écran de fichiers d’entrée sélectionnés](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. Afficher la sortie de la requête :

    ![Capture d’écran de sortie de la requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Démarrer la tâche de flux de données Analytique


Il est maintenant temps de terminer la configuration et démarrage de la tâche. Enregistrer la requête à partir de la Question 3, qui produira un résultat qui correspond au schéma de la table de sortie **TollDataRefJoin** .

Accédez au **tableau de bord**projet, puis cliquez sur **Démarrer**.

![Capture d’écran du bouton Démarrer dans le tableau de bord projet](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Dans la boîte de dialogue qui s’ouvre, modifier l’heure de **Sortie de démarrer** à **l’heure**. Modifier l’heure à une heure antérieure à l’heure actuelle. Cette modification permet de s’assurer que tous les événements du concentrateur d’événements sont traités dans la mesure où vous avez commencé à générer les événements au début de ce didacticiel. Cliquez sur la case à cocher pour démarrer le travail.

![Sélection de temps personnalisé](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Démarrage de la tâche peut prendre quelques minutes. Vous pouvez voir le statut sur la page de niveau supérieur pour l’Analytique des flux de données.

![Capture d’écran de l’état de la tâche](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Résultats de la vérification dans Visual Studio

1. Ouvrez l’Explorateur de serveurs Visual Studio et avec le bouton droit de la table **TollDataRefJoin** .
2. Cliquez sur **Afficher les données de Table** pour voir le résultat de votre travail.

    ![Sélection de « Afficher des données de Table » dans l’Explorateur de serveurs](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Analytique de flux Azure évoluer de travaux

Azure Analytique de flux de données est conçu pour ELASTIQUEMENT échelle afin qu’il puisse gérer un grand nombre de données. La requête Analytique de flux Azure permet une clause **PARTITION BY** indiquent au système capable d’évoluer à cette étape. **IDPartition** est une colonne spéciale qui ajoute par le système pour le correspond à l’ID de partition de l’entrée (concentrateur d’événements).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Arrêter la tâche en cours, la requête dans l’onglet **requête** mise à jour et cliquez sur l’onglet **échelle** .

    **Unités de transmission en continu** définir la quantité de puissance de calcul capable de recevoir la tâche.

2. Déplacez le curseur sur 6.

    ![Capture d’écran de sélection de 6 unités de transmission en continu](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. Accédez à l’onglet **sorties** et remplacez le nom de la table SQL **TollDataTumblingCountPartitioned**.

Si vous démarrez la tâche maintenant, Azure flux Analytique peut distribuer le travail sur plusieurs ressources de calcul et obtenir de meilleurs résultats. Notez que l’application TollApp envoie également des événements partitionnées par TollId.

## <a name="monitor"></a>Moniteur

L’onglet **Moniteur** affiche des statistiques sur le travail en cours d’exécution.

![Capture d’écran de statistiques sur l’exécution des travaux](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Vous pouvez accéder de **Journaux** à partir de l’onglet de **tableau de bord** .

![L’option « Journaux »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Capture d’écran de journaux où vous pouvez consulter l’état des travaux](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Pour afficher des informations supplémentaires sur un événement particulier, cliquez sur l’événement, puis cliquez sur le bouton **Détails** .

![Capture d’écran de détails sur un événement sélectionné](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Conclusion

Ce didacticiel vous présente le service Analytique de flux Azure. Il a montré comment configurer les entrées et les sorties de la tâche de flux de données Analytique. Le didacticiel à l’aide du scénario numéro payant données, a expliqué les types courants de problèmes qui peuvent survenir dans l’espace des données de mouvement et comment ils peuvent être résolus avec les requêtes de type SQL simples dans Azure flux Analytique. Ce didacticiel décrit les constructions d’extension SQL pour travailler avec des données temporelles. Il a montré comment joindre des flux de données, comment enrichir le flux de données avec les données de référence statique et faire évoluer une requête pour obtenir un débit plus élevé.

Bien que ce didacticiel constitue une bonne introduction, il n’est pas terminée par tout moyen. Vous pouvez trouver plusieurs modèles de requête à l’aide de la langue SAQL exemples de [requête pour les modes d’utilisation courants Analytique de flux de données](stream-analytics-stream-analytics-query-patterns.md).
Reportez-vous à la [documentation en ligne](https://azure.microsoft.com/documentation/services/stream-analytics/) pour en savoir plus sur Azure flux Analytique.

## <a name="clean-up-your-azure-account"></a>Nettoyer votre compte Azure

1. Arrêter la tâche d’Analytique de flux dans le portail Azure.

    Le script Setup.ps1 crée deux concentrateurs d’événement et d’une base de données SQL. Les instructions suivantes vous aident à nettoyer les ressources à la fin du didacticiel.

2. Dans une fenêtre PowerShell, tapez **.\\ CleanUp.ps1** pour lancer le script qui supprime les ressources utilisées dans le didacticiel.

    > [AZURE.NOTE] Les ressources sont identifiées par le nom. Vérifiez que vous passez attentivement en revue chaque élément avant de confirmer la suppression.

    ![Capture d’écran du processus de nettoyage](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)
